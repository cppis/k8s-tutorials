# tutorial.03  

Shows deploy a new php container in [tutorial.02](tutorial.02/README.md).   

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which is built from Dockerfile in [tutorial.01](../tutorial.01/README.md)   

<br/><br/><br/>

## Objectives  
* Create a k8s *deployment*  
  * Nginx container  
  * PHP-FPM container  
* Create a k8s *Persistent Volume*  
  * Bind the volume to the *deployment*  
* Deploy a new php container(*php.new.yaml*)  

<br/><br/><br/>

## Run  
> This tutorial is the continuation of [tutorial.02](../tutorial.02/README.md).  

<br/>

### Deploy a new PHP *Deployment*  
If an deploy an updated php *deployment*, run the following command:    
  ```shell
  $ kubectl apply -f resources/deployment/php.new.yaml
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

### Summary commands  
Create all resources:   
  ```shell
  $ kubectl apply -f resources/deployment/php.new.yaml
  ```

Delete all resources:   
  ```shell
  $ kubectl delete -f resources/deployment
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
