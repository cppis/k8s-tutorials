# tutorial: nginx+php-fpm deployment using pv

Shows remove use of persistent volume in [tutorial: nginx+php-fpm deployment](../tutorial.nginx+phpfpm-deployment/README.md). 

<br/><br/><br/>

## Objectives  
* K8s deployment  
  * Nginx container  
  * PHP-FPM container   

<br/><br/><br/>

## Run  

Move to working path:  
  ```shell
  cd {Project Root}/tutorial.nginx+phpfpm-deployment-using-pv/  
  ```

<br/>

### Create Nginx Configuration  
In this step, you will use a *ConfigMap* to configure Nginx.  
A *ConfigMap* holds your configuration in a key-value format that you can  
reference in other Kubernetes object definitions.  
  ```shell
  $ kubectl apply -f resources/configmap
  ```

<br/>

### Create Nginx + PHP-FPM *Deployment*  
*Deployments* provide a uniform way to create, update, and manage *pods*  
by using *ReplicaSets*.  
If an update does not work as expected, a *Deployment* will automatically  
rollback its *pods* to a previous image.  
  ```shell
  $ kubectl apply -f resources/deployment/nginx-php.yaml
  ```

It will take some time for the *pods* status to become `podInitializing`.  
  ```shell
  $ kubectl get pods
    NAME                     READY     STATUS            RESTARTS   AGE
    php-7894656896-jlp4l     0/1       podInitializing   0          24s
  ```

Once it’s completed you will have your *pod* `running`.
  ```shell
  NAME                     READY     STATUS     RESTARTS   AGE
  php-7894656896-jlp4l     1/1       Running    0          1m
  ```

#### Reference  
* [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)  
* [Kubernetes Patterns : The Init Container Pattern](https://www.magalix.com/blog/kubernetes-patterns-the-init-container-pattern)  

<br/>

### Expose your Application  
Now everything is in place and you can expose your application to internet.  
To do this you can run the following command to create a *Load Balancer*  
which provides you an external IP.  
```shell
$ kubectl expose deployment tut04-nginx-php --type=NodePort --port=80
```

> Note: The `type=LoadBalancer` service is backed by external cloud providers,  
> which is not covered in this example, please refer to [this page](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer) for the details.
 
> Note: If the external IP address is shown as <pending>,  
> wait for a minute and enter the same command again. 

<br/>

### Summary commands  
Create all resources:   
  ```shell
  $ kubectl apply -f resources/configmap
  $ kubectl apply -f resources/deployment
  $ kubectl expose deployment tut04-nginx-php --type=NodePort --port=80
  ```

Delete all resources:   
  ```shell
  $ kubectl delete service tut04-nginx-php
  $ kubectl delete -f resources/deployment
  $ kubectl delete -f resources/configmap
  ```

<br/><br/><br/>

## References  
* [Kubernetes Basic PHP 7.3 Application with Nginx on Google Cloud](https://www.cloudbooklet.com/kubernetes-basic-php-application-with-nginx-on-google-cloud/)  
* [Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)  
  * ["Naked" Pods versus ReplicaSets, Deployments, and Jobs](https://kubernetes.io/docs/concepts/configuration/overview/#naked-pods-vs-replicasets-deployments-and-jobs)  
    Don't use naked Pods (that is, Pods not bound to a [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) or [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)) if you can avoid it. Naked Pods will not be rescheduled in the event of a node failure.

    A Deployment, which both creates a ReplicaSet to ensure that the desired number of Pods is always available, and specifies a strategy to replace Pods (such as [RollingUpdate](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment)), is almost always preferable to creating Pods directly, except for some explicit [restartPolicy: Never](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy) scenarios. A [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) may also be appropriate
* [Deploying PHP Apps to Kubernetes – Michelle Krejci (DevNet Create 2017)](https://www.youtube.com/watch?v=au_CSyYR5lc)  
* [Exposing FastCGI Servers](https://kubernetes.github.io/ingress-nginx/user-guide/fcgi-services/)  
