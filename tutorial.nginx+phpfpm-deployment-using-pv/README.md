# tutorial: nginx+php-fpm deployment using pv  

Written with reference to [Kubernetes Basic PHP 7.3 Application with Nginx on Google Cloud](https://www.cloudbooklet.com/kubernetes-basic-php-application-with-nginx-on-google-cloud/).  

<br/>

## Objectives  
* Create a k8s *deployment*  
  * Nginx container  
  * PHP-FPM container  
* Create a k8s *Persistent Volume*  
  * Bind the volume to the *deployment*  

<br/>

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which is built from Dockerfile in [tutorial: nginx+php-fpm pod](../tutorial.nginx+phpfpm-pod/README.md)   
 
<br/><br/><br/>

## Run  

Move to working path:  
  ```shell
  cd {Project Root}/tutorial.nginx+phpfpm-deployment-using-pv/  
  ```

<br/>

### Create PHP-FPM *Service*  
A *service* allows access to a set of *pods* from within the cluster.  
*Services* within a cluster can communicate directly through their names,  
without IP addresses. The PHP-FPM *service* will allow access to the PHP-FPM *pods*.  
  ```shell
  $ kubectl apply -f resources/service/php.yaml
  ```

<br/>

### Create *Persistent Volume*  
A *Persistent Volume*, or *PV*, is block storage of a specified size that lives   
independently of a *pod*’s life cycle.  
Using a *Persistent Volume* will allow you to manage or update your *pods*  
without worrying about losing your application code.  
A *Persistent Volume* is accessed by using a *PersistentVolumeClaim*, or *PVC*,  
which mounts the *PV* at the required path.  
  ```shell
  $ kubectl apply -f resources/volume/app.yaml
  ```

<br/>

### Create PHP-FPM *Deployment*  
*Deployments* provide a uniform way to create, update, and manage *pods*  
by using *ReplicaSets*.  
If an update does not work as expected, a *Deployment* will automatically  
rollback its *pods* to a previous image.  
  ```shell
  $ kubectl apply -f resources/deployment/php.yaml
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

### Create Nginx Configuration  
In this step, you will use a *ConfigMap* to configure Nginx.  
A *ConfigMap* holds your configuration in a key-value format that you can  
reference in other Kubernetes object definitions.  
  ```shell
  $ kubectl apply -f resources/configmap/nginx.yaml
  ```

### Create Nginx *Deployment*  
Next, you will specify the image to create your *pod* from.  
This tutorial will use the `nginx:1.14.2` image for stability, but you can  
find other Nginx images on the Docker store.  
Also, make Nginx available on port `80`.  
  ```shell
  $ kubectl apply -f resources/deployment/nginx.yaml
  ```

You can view the pods that this *Deployment* started with the following command:  
  ```shell
  $ kubectl get pods
    nginx-548b4c679b-mnbj7   1/1       Running   0          5m
    php-7894656896-jlp4l     1/1       Running   0          9m  
  ```
<br/>

### Expose your Application  
Now everything is in place and you can expose your application to internet.  
To do this you can run the following command to create a *Load Balancer*  
which provides you an external IP.  
```shell
$ kubectl expose deployment tut02-nginx --type=NodePort --port=80
```

> Note: The `type=LoadBalancer` service is backed by external cloud providers,  
> which is not covered in this example, please refer to [this page](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer) for the details.
 
> Note: If the external IP address is shown as <pending>,  
> wait for a minute and enter the same command again. 

<br/>

### Summary commands  
Create all resources:   
  ```shell
  $ kubectl apply -f resources/service/php.yaml
  $ kubectl apply -f resources/volume/app.yaml
  $ kubectl apply -f resources/deployment/php.yaml
  $ kubectl apply -f resources/configmap/nginx.yaml
  $ kubectl apply -f resources/deployment/nginx.yaml
  $ kubectl expose deployment tut02-nginx --type=NodePort --port=80
  ```

Delete all resources:   
  ```shell
  $ kubectl delete service tut02-nginx
  $ kubectl delete -f resources/deployment/nginx.yaml
  $ kubectl delete -f resources/configmap/nginx.yaml
  $ kubectl delete -f resources/deployment/php.yaml
  $ kubectl delete -f resources/volume/app.yaml
  $ kubectl delete -f resources/service/php.yaml
  ```

<br/>

> Configuration Files in the same path(resources/deployment) can be  
> executed at once with the following command:  
> ```shell
> $ kubectl apply -f resources/deployment
> ```

<br/><br/><br/>

## References  
* [Kubernetes Basic PHP 7.3 Application with Nginx on Google Cloud](https://www.cloudbooklet.com/kubernetes-basic-php-application-with-nginx-on-google-cloud/)  
* [Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)  
  * ["Naked" Pods versus ReplicaSets, Deployments, and Jobs](https://kubernetes.io/docs/concepts/configuration/overview/#naked-pods-vs-replicasets-deployments-and-jobs)  
    Don't use naked Pods (that is, Pods not bound to a [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) or [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)) if you can avoid it. Naked Pods will not be rescheduled in the event of a node failure.

    A Deployment, which both creates a ReplicaSet to ensure that the desired number of Pods is always available, and specifies a strategy to replace Pods (such as [RollingUpdate](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment)), is almost always preferable to creating Pods directly, except for some explicit [restartPolicy: Never](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy) scenarios. A [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) may also be appropriate
* [Deploying PHP Apps to Kubernetes – Michelle Krejci (DevNet Create 2017)](https://www.youtube.com/watch?v=au_CSyYR5lc)  