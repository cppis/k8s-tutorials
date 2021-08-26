# tutorial: emqx

Written with reference to [Building K8S cluster of EMQ X starting from scratch](https://www.emqx.com/en/blog/emqx-mqtt-broker-k8s-cluster)  

<br/><br/><br/>

## Run  

Move to working path:  
  ```shell
  cd {Project Root}/tutorial.emqx/  
  ```

<br/>

### 0. Use Deployment deploy Pod  
Deploy a *deployment*:  
```shell
$ kubectl apply -f 0.service.yaml
```

View *Deployment* status: 
```shell
$ kubectl get pods
  NAME                              READY   STATUS    RESTARTS   AGE
  emqx-deployment-7fd446d95-jgxcr   1/1     Running   0          16m

$ kubectl exec emqx-deployment-7fd446d95-jgxcr -- emqx_ctl status
  Node 'emqx-deployment-7fd446d95-jgxcr@10.1.0.184' 4.3.7 is started
```

*Pod* has a lifecycle.  
And a client needs a fixed target to connect to in order to use the app.  
*Service* is an abstract method for exposing the app which is running on a set of Pods.  

Deploy a *service*:  
```shell
$ kubectl apply -f 0.service.yaml
```

Get status of emqx broker using API:  
```shell
$ kubectl get svc
  NAME          TYPE     CLUSTER-IP EXTERNAL-IP PORT
  emqx-service0 NodePort ...         <none>     ...8081:32082/TCP...

$ curl localhost:32082/status
  Node emqx-deployment-7fd446d95-jgxcr@10.1.0.184 is started
  emqx is running
```

<br/><br/>

### 1. Automatically cluster emqx through Kubernetes  
In the [automatically cluster](https://docs.emqx.io/en/broker/v4.3/advanced/cluster.html#emqx-service-discovery-k8s) of a `emqx` documentation, you can see the cluster configuration. 
```shell
cluster.discovery = kubernetes
cluster.kubernetes.apiserver = https://kubernetes.docker.internal:6443
cluster.kubernetes.service_name = ekka
cluster.kubernetes.address_type = ip
cluster.kubernetes.app_name = ekka
``` 

* `cluster.kubernetes.apiserver` is the address of Kubernetes apiserver,  
  you can get it through run a `kubectl cluster-info`.  
* `cluster.kubernetes.service_name` is the *Service* name.  
* `cluster.kubernetes.app_name` is the part before the `@` symbol in node.name of `emqx`  
  so you also need to set the `emqx` in the cluster as a uniform prefix of `node.name`.  

<br/>

#### Give authority to pod for accessing Kubernetes apiserver  
The normal *Pod* can not access Kubernetes apiserver.  
To solve this problem:  
* Open the HTTP interface of Kubernetes apiserver, but some security risks are exiting.  
* Using *ServiceAccount*, *Role* and *RoleBinding* to configure RBAC authentication.  

```shell
$ kubectl apply -f rbac.yaml
``` 

<br/>

#### Deploy a *ConfigMap*   
A *ConfigMap* is an API object used to store non-confidential data in key-value pairs.  
*Pods* can consume *ConfigMaps* as environment variables, command-line arguments, or as configuration files in a volume.  

> ConfigMap does not provide secrecy or encryption. If the data you want to store are confidential, use a Secret rather than a ConfigMap, or use third-party tools to keep your data private.  

Deploy a *configmap*:  
```shell
$ kubectl apply -f 1.configmap.yaml
```

Deploy a *service*:  
```shell
$ kubectl apply -f 1.service.yaml
```

Deploy a *deployment*:  
```shell
$ kubectl apply -f 1.deployment.yaml
```

View a cluster info:  
```shell
$ kubectl get pods
  NAME                               READY   STATUS    RESTARTS   AGE
  emqx-deployment1-5d9d56689-49jqg   1/1     Running   0          16s
  emqx-deployment1-5d9d56689-dsr8q   1/1     Running   0          16s
  emqx-deployment1-5d9d56689-hn79z   1/1     Running   0          16s

$ kubectl exec emqx-deployment1-5d9d56689-49jqg -- emqx_ctl cluster status
  Cluster status: #{
    running_nodes => ['emqx@10.1.0.219','emqx@10.1.0.220','emqx@10.1.0.221'],
    stopped_nodes => []
  }
```

Delete all:  
```shell
$ kubectl delete -f 1.deployment.yaml
$ kubectl delete -f 1.service.yaml
$ kubectl delete -f 1.configmap.yaml
```

<br/><br/>

### 2. StatefulSet    
The DNS format of every pod in *StatefulSet* is `[appName@]statefulSetName-{0..N-1}.serviceName.namespace.svc.cluster.local`:  

Deploy all:  
```shell
$ kubectl apply -f 2.configmap.yaml
$ kubectl apply -f 2.service.headless.yaml
$ kubectl apply -f 2.statefulset.yaml
```

View a cluster info:  
```shell
$ kubectl get pods
  NAME                  READY   STATUS    RESTARTS   AGE
  emqx-statefulset2-0   1/1     Running   0          12s
  emqx-statefulset2-1   1/1     Running   0          11s
  emqx-statefulset2-2   1/1     Running   0          9s

$ kubectl exec emqx-statefulset2-0 -- emqx_ctl cluster status
  Cluster status: #{
    running_nodes => [
      'emqx@emqx-statefulset2-0.emqx-headless2.default.svc.cluster.local',
      'emqx@emqx-statefulset2-1.emqx-headless2.default.svc.cluster.local',
      'emqx@emqx-statefulset2-2.emqx-headless2.default.svc.cluster.local'
    ],
    stopped_nodes => []
  }
```

Delete all:  
```shell
$ kubectl delete -f 2.statefulset.yaml
$ kubectl delete -f 2.service.headless.yaml
$ kubectl delete -f 2.configmap.yaml
```

<br/><br/>

### 3. Persistence EMQ X Broker cluster  
Deploy all:  
```shell
$ kubectl apply -f 3.configmap.yaml
$ kubectl apply -f 3.service.headless.yaml
$ kubectl apply -f 3.statefulset.yaml
```

View pvc and a cluster info:  
```shell
$ kubectl get pods
  NAME                  READY   STATUS    RESTARTS   AGE
  emqx-statefulset3-0   1/1     Running   0          12s
  emqx-statefulset3-1   1/1     Running   0          11s
  emqx-statefulset3-2   1/1     Running   0          9s

$ kubectl get pvc
  NAME                         STATUS VOLUME...     
  emqx-pvc-emqx-statefulset3-0 Bound  pvc-f7a5a3e8-...   
  emqx-pvc-emqx-statefulset3-1 Bound  pvc-acc3b4ce-...
  emqx-pvc-emqx-statefulset3-2 Bound  pvc-a511a51e-...

$ kubectl exec emqx-statefulset3-0 -- emqx_ctl cluster status
  Cluster status: #{
    running_nodes => [
      'emqx@emqx-statefulset3-0.emqx-headless2.default.svc.cluster.local',
      'emqx@emqx-statefulset3-1.emqx-headless2.default.svc.cluster.local',
      'emqx@emqx-statefulset3-2.emqx-headless2.default.svc.cluster.local'
    ],
    stopped_nodes => []
  }
```

Delete all:  
```shell
$ kubectl delete -f 2.statefulset.yaml
$ kubectl delete -f 2.service.headless.yaml
$ kubectl delete -f 2.configmap.yaml
```
