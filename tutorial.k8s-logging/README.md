# tutorial: k8s logging  

Written with reference to [*Fluentd* on Kubernetes: Log collection explained](https://www.youtube.com/watch?v=6kmHvXdAzIM&t=316s) by *That DevOps Guy*.  

<br/>

## [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)  

Application logs can help you understand what is happening inside your application. The logs are particularly useful for debugging problems and monitoring cluster activity.  

In a cluster, logs should have a separate storage and lifecycle independent of nodes, pods, or containers. This concept is called *cluster-level logging*.  

Cluster-level logging architectures require a separate backend to store, analyze, and query logs. Kubernetes does not provide a native storage solution for log data. Instead, there are many logging solutions that integrate with Kubernetes. The following sections describe how to handle and store logs on nodes.  

...

<br/><br/>

## [Introduction to *Fluentd* on Kubernetes](https://github.com/marcel-dempers/docker-development-youtube-series/blob/master/monitoring/logging/fluentd/kubernetes/README.md)  

### [*fluentd*](https://github.com/fluent/fluentd-kubernetes-daemonset)  
Before we start check out the official *fluentd* repo [fluent/fluentd-kubernetes-daemonset](https://github.com/fluent/fluentd-kubernetes-daemonset) which has a lot of implementations run *fluentd* as a *daemonset*. Send logs to *azureblob*, *cloudwatch*, *elasticsearch*, *gcs*, *forward to other fluentd instances* and so on.   
Also includes different versions of [*fluentd* docker images](https://github.com/fluent/fluentd-kubernetes-daemonset/tree/master/docker-image).   

<br/>

#### Build *fluentd*  

Move to working path:  
  ```shell
  cd {Project Root}/tutorial.k8s-logging/reousrces/docker-image
  ```

Build image and push to a registry:  
  ```shell
  $ docker build . -t fluentd-demo:v0.0.1
  ```

  > if you use remote registry like docker hub, push image to registry:  
  > ```shell
  > $ docker push {Docker Hub ID}/fluentd-demo:v0.0.1
  > ```

  > #### anatomy of a docker image reference  
  >   `docker.io/cppis/fluentd-demo:latest`  
  >   * docker.io: domain of registry  
  >   * cppis: image owner
  >   * fluentd-demo: image name
  >   * latest: image tag  

Now, we have custom *fluentd* image that can run in our kubernetes cluster to collect logs.  

We want to run this image in a pod on every node in our cluster.  
And also want to look at how to collect the logs from each node in the cluster.   
If you don't want to build your own, you can also refer to docker hub and use the [*fluentd* daemonset images](https://hub.docker.com/r/fluent/fluentd-kubernetes-daemonset/).  

<br/><br/>

#### *Fluentd* Configmap  
We have 5 files in our `fluentd-configmap.yaml`:  
* fluent.conf: Our main config which includes all other configurations
* pods-kind-fluent.conf: `tail` config that sources all pod logs on the `kind` cluster.
  Note: `kind` cluster writes its log in a different format
* pods-fluent.conf: `tail` config that sources all pod logs on the `kubernetes` host in the cloud. <br/>
  Note: When running K8s in the cloud, logs may go into JSON format.
* file-fluent.conf: `match` config to capture all logs and write it to file for testing log collection </br>
  Note: This is great to test if collection of logs works
* elastic-fluent.conf: `match` config that captures all logs and sends it to `elasticseach`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluentd-config
  namespace: fluentd
data:
  fluent.conf: |-
    ################################################################
    # This source gets all logs from local docker host
    #@include pods-kind-fluent.conf
    @include pods-fluent.conf
    @include file-fluent.conf
    #@include elastic-fluent.conf
  pods-kind-fluent.conf: |-
    <source>
      @type tail
      read_from_head true
      tag kubernetes.*
      path /var/log/containers/*.log
      pos_file /var/log/fluentd-containers.log.pos
      exclude_path ["/var/log/containers/fluent*"]
      <parse>
        @type regexp
        #https://regex101.com/r/ZkOBTI/1
        expression ^(?<time>\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[^Z]*Z)\s(?<stream>[^\s]+)\s(?<character>[^\s])\s(?<message>.*)$
        #time_format %Y-%m-%dT%H:%M:%S.%NZ
      </parse>
    </source>

    <filter kubernetes.**>
      @type kubernetes_metadata
      @id filter_kube_metadata
      kubernetes_url "#{ENV['FLUENT_FILTER_KUBERNETES_URL'] || 'https://' + ENV.fetch('KUBERNETES_SERVICE_HOST') + ':' + ENV.fetch('KUBERNETES_SERVICE_PORT') + '/api'}"
      verify_ssl "#{ENV['KUBERNETES_VERIFY_SSL'] || true}"
      ca_file "#{ENV['KUBERNETES_CA_FILE']}"
      skip_labels "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_LABELS'] || 'false'}"
      skip_container_metadata "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_CONTAINER_METADATA'] || 'false'}"
      skip_master_url "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_MASTER_URL'] || 'false'}"
      skip_namespace_metadata "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_NAMESPACE_METADATA'] || 'false'}"
    </filter>
  pods-fluent.conf: |-
    <source>
      @type tail
      read_from_head true
      tag kubernetes.*
      path /var/log/containers/*.log
      pos_file /var/log/fluentd-containers.log.pos
      exclude_path ["/var/log/containers/fluent*"]
      <parse>
        @type kubernetes
        @type "#{ENV['FLUENT_CONTAINER_TAIL_PARSER_TYPE'] || 'json'}"
        time_format %Y-%m-%dT%H:%M:%S.%NZ
      </parse>
    </source>

    <filter kubernetes.**>
      @type kubernetes_metadata
      @id filter_kube_metadata
      kubernetes_url "#{ENV['FLUENT_FILTER_KUBERNETES_URL'] || 'https://' + ENV.fetch('KUBERNETES_SERVICE_HOST') + ':' + ENV.fetch('KUBERNETES_SERVICE_PORT') + '/api'}"
      verify_ssl "#{ENV['KUBERNETES_VERIFY_SSL'] || true}"
      ca_file "#{ENV['KUBERNETES_CA_FILE']}"
      skip_labels "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_LABELS'] || 'false'}"
      skip_container_metadata "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_CONTAINER_METADATA'] || 'false'}"
      skip_master_url "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_MASTER_URL'] || 'false'}"
      skip_namespace_metadata "#{ENV['FLUENT_KUBERNETES_METADATA_SKIP_NAMESPACE_METADATA'] || 'false'}"
    </filter>
  file-fluent.conf: |-
    <match **>
      @type file
      path /tmp/file-test.log
    </match>
  elastic-fluent.conf: |-
    <match **>
      @type elasticsearch
      host "#{ENV['FLUENT_ELASTICSEARCH_HOST'] || 'elasticsearch.elastic-kibana'}"
      port "#{ENV['FLUENT_ELASTICSEARCH_PORT'] || '9200'}"
      index_name fluentd-k8s
      type_name fluentd
    </match>
```

Move to working path:  
  ```shell
  $ cd {Project Root}/tutorial.k8s-logging/reousrces
  ```

Create *fluentd* namespace:  
  ```shell
  $ kubectl create ns fluentd
  ```

Let's deploy our `configmap`:
  ```shell
  $ kubectl apply -f fluentd-configmap.yaml
  ```

<br/><br/>

#### The daemonset  
Let's go ahead and see what the kubernetes daemon sets for *fluentd* looks like.  
The daemonset will mount all these config files as well as location where the logs are stored on the kubernetes node.  

Daemonset is in [`fluentd.yaml`](resources/fluentd.yaml).  
  ```yaml
  ...
    volumeMounts:
    - name: fluentd-config
      mountPath: /fluentd/etc
    - name: varlog
      mountPath: /var/log
    - name: varlibdockercontainers
      mountPath: /var/lib/docker/containers
      readOnly: true
    ...
  volumes:
  - name: fluentd-config
    configMap:
      name: fluentd-config
  - name: varlog
    hostPath:
      path: /var/log
  - name: varlibdockercontainers
    hostPath:
      path: /var/lib/docker/containers    
  ```

Volumes `varlog` and `varlibdockercontainers` are both host path, so we're mounting path on the host into this *fluentd* container so once we have this pod up and running i will go inside the pod and show you the file system.  

<br/><br/>

#### Permissions  
Before we deploy our daemonset in order for *fluentd* to read the metadata of each pod and add that metadata to each log entry, we have to deploy a *fluentd-rbac* file and we need to give *fluentd* access to be able to call the kubernetes API.  
So to do that we have a [fluentd-rbac.yaml](resoureces/../resources/fluentd-rbac.yaml) and here we just give it a cluster role and role binding and we allow it to basically get, list and watch *pods* and *namespaces* in the cluster.  
And we also create a small service account that we bind these roles to.  
To create service account the `ClusterRole` and the `ClusterRoleBinding` run the following command:  
```shell
$ kubectl apply -f fluentd-rbac.yaml
``` 

This will go ahead and create our service account the role and the role binding which we can use on the daemonset.  
And we take a look at the daemonset [fluentd.yaml](resources/fluentd.yaml), 
we're passing in specific service account, name which is *fluentd*.  
```yaml
  ...
  serviceAccount: fluentd
  serviceAccountName: fluentd
  ...
```

To deploy the daemonset, run the following command:  
```shell
$ kubectl apply -f fluentd.yaml
```

And To check pod is up and running, run the following command:  
```shell
$ kubectl -n fluentd get pods
```

<br/><br/>

#### Example app  
Now, we have the *fluentd* running.  
Let's deploy a small example application the writes logs that we can collect.  
Example is [counter.yaml](reousrces/../resources/counter.yaml), it is just a pod called *counter* which runs a small *bushbox* container image which runs in a loop and write a date stamp out to `stdout`.  

To deploy the *counter* app, run the following command:  
```shell
$ kubectl apply -f counter.yaml
``` 

And To check pod is up and running, run the following command:  
```shell
$ kubectl get pods
```

And if run `kubectl logs` for that pod, we can see writing logs:  
```shell
$ kubectl logs counter
```

Next, let's see how *fluentd* collects those logs.  

<br/><br/>

#### Log collection  
Now we have *fluentd* up and running, it's very important to understand where the logs stored on the kubernetes node that's the same place the we've just mounted into our *fluentd* pod.   
So, let's go inside that *fluentd* pod and look at the log files.  

To grab the pod name in the *fluentd* namespace, run the following command:  
```shell
$ kubectl -n fluentd get pods
  NAME ...
  fluentd-abcde ...
```

Attach bash terminal inside of the *fluentd* pod, run the following command:  
```shell
$ kubectl -n fluentd exec -it fluentd-abcde bash
  root@fluentd-abcde:/home/fluentd# 
```

Now, we are inside of the pod.
Let's go to the path that we're mounting in from the host which is `/var/log`.  
If we move to that directory and run `ls`. We can see a directory called `containers`.  
This is where the logs are stored on the host.  
We also see a `fluentd-containers.log.pos` file, this is the position file where fluentd keeps track of where it's read up to. It's important to note that you have to mount this in from the host, so when *fluentd* reboots it won't lose track of read position.  

Run `ls` in the `containers` direcotry. to see log file.  
```shell
root@fluentd-abcde:/home/fluentd# cd /var/log
root@fluentd-abcde:/home/fluentd# ls containers
  ...
  counter_default_count-...
  ...
```

There is count counter pod in the default namespace. And if run `ls -l` to that specific log file, we can see that that file actually sym linked to `/var/log/pods`.  
Everything under `/var/log/containers` is sym linked to `/var/log/pods`.  

```
root@fluentd-abcde:/var/log# ls -l containers/counter_default_count-08a97e884e6c9473d29edf8d6764ce5dce7413ddce2830eb84167d3166578610.log
lrwxrwxrwx 1 root root 78 Aug 19 06:44 containers/counter_default_count-08a97e884e6c9473d29edf8d6764ce5dce7413ddce2830eb84167d3166578610.log -> /var/log/pods/default_counter_16be5a1e-adac-4b77-93b2-d09ae7260ee1/count/0.log
```

If run `cat` on that file in the `/var/log/pods`, can see raw logs on the node.  
Raw logs are being stored under `/var/log/pods`.  
So you can see the sym linking here gives linux the capability of writing like aliases for pods.  And we go back to fluentd configuration file, we can see that we always tell fluentd to look at the `/var/log/containers` direcotry.   
`fluentd-configmap.yaml`:  
```yaml
pods-fluentd.conf: |-
  ...
  path /var/log/containeres/*.log
  ...
```

So, *fluentd* always look at the containers directory but depending on the type of kubernetes cluster you're running the container runtime might be writing its log in a different location. And those kubernetes implementations will have different sym links to make sure that there is a sym link available so we can read logs from `/var/log/containres`.  
If you're running kubernetes in a cloud provider, every cloud provider might slightly different implementation. Every cloud provider might use a different OS and container runtime, some might use docker, some might use containerd or other container runtimes.  

And if this is the case the cloud provider will provide a sym link to link that direcotry to `/var/log/containers`.  
As many cloud providers run docker, that's why the *fluentd* daemonset under the volume mount section mount `/var/log` as well as `var/lib/docker/containers`.  
So if you're running in like microsoft aks, you might see that there is an additional sym link to sym link `/var/lib/containers` to `/var/log/pod` and in turn sym link `/var/log/pods` to `/var/log/containers`.  
This helps us keep our fluentd configmap simple as we can always expect the logs to be in the `/var/log/containers` directory.  
Docker writes its container logs to `/var/lib/docker/containers` but not all kubernetes nodes use docker. So to keep kubernetes consistent this directory may be sym linked to `/var/log/containers` to maintain consistency.  

We see the logs on the node and mounted those logs into our fluentd container.   

Next, Let's see `file-fluent.conf` using the file plugin.  
Move path to `/tmp/file-test.log/`, and we can see all log files are dumped here:  
```shell
cd /tmp/file-test.log/
```

And it's also important to notice that our logs have been parsed, so they're now written as json format.  
And we can also see our kubernetes metadata plugin has added extra kubernetes metadata onto this log. So you can see container name and namespace, the pod name and so on.    

<br/><br/>

#### Elastic search  
To deploy a demo, first create a new namespace:  
```shell
$ kubectl create ns elastic-kibana
```

To deploy a elastic search and kibana:  
```shell
$ kubectl -n elastic-kibana apply -f elastic/elastic-demo.yaml
$ kubectl -n elastic-kibana apply -f elastic/kibana-demo.yaml
```

To check a elastic search and kibana is running, run the following command:  
```shell
$ kubectl -n elastic-kibana get pods
```

To access to a kibana, run the following command:  
```shell
$ kubectl -n elastic-kibana port-forward svc/kibana 5601
```

Open browser and go to `localhost:5601` to access kibana dashboard.  
We don't have any data yet as fluentd is not configured to send logs to elastic search yet.  

Let's change [fluentd-configmap.yaml](resources/fluentd-configmap.yaml) and uncomment `elastic-fluent.conf`.  
```
  ...
-  #@include elastic-fluent.conf
+  @include elastic-fluent.conf
  ...
```

To apply a new configmap, run the following command:  
```shell
$ kubectl apply -f fluentd-configmap.yaml
```

To create a new pod, just delete current:  
```shell
$ kubectl -n fluentd get pods
  NAME ...
  fluentd-abcde
$ kubectl -n fluentd delete po fluentd-abcde
```

We don't have any data yet.  
After some time, In kibana go to `Menu > Kibana > Discover`.  
In the Index patterns page, hit `Create Index pattern` button.  

<br/><br/>

#### Delete all  
To delete everything created from this tutorial, run the following command:  
```shell
$ kubectl delete all --all -n elastic-kibana
$ kubectl delete all --all -n fluentd
$ kubectl delete pod counter
```  

<br/><br/><br/>

## Troubleshootings  
### /usr/bin/env: ‘sh\r’: no such file or directory  
* change line endings to `LF`
* change git autocrlf config to `false`  
  ```shell
  $ git config --global core.autocrlf false
  ```

<br/><br/><br/>

## References  
* [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)  
* [Kubernetes Logging Simplified – Pt 1: Applications](https://observiq.com/blog/kubernetes-logging-simplified-pt-1-applications/)  
* [A Practical Guide to Kubernetes Logging](https://logz.io/blog/a-practical-guide-to-kubernetes-logging/)  
* [Introduction to Fluentd on Kubernetes](https://github.com/marcel-dempers/docker-development-youtube-series/tree/master/monitoring/logging/fluentd/kubernetes)  
* [5 Sharing images with Docker Hub and other registries](https://livebook.manning.com/book/learn-docker-in-a-month-of-lunches/chapter-5/4)  
  Shows anatomy of a Docker image reference  