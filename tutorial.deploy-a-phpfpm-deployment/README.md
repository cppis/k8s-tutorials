# tutorial: deploy a php-fpm deployment  

Shows deploy a new php container in [tutorial: nginx + php-fpm deployment](../tutorial.nginx+phpfpm-deployment/README.md).   

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which is built from Dockerfile in [tutorial: nginx + php-fpm pod](../tutorial.nginx+phpfpm-pod/README.md)   

<br/>

<figure>
<div style="text-align:center">
  <a href="https://drive.google.com/uc?export=view&id=1QpH2nVmUOwrx9nfv3S0QJKeilWSb9ClO">
  <img src="https://drive.google.com/uc?export=view&id=1QpH2nVmUOwrx9nfv3S0QJKeilWSb9ClO" style="width: 480px; max-width: 100%; height: auto" title="tutorial.nginx+phpfpm-deployment" />
  </a>
</div>
</figure>

<br/><br/><br/>

## Objectives  
* Deploy a new php container(*php.yaml*)  

<br/><br/><br/>

## Run  
> This tutorial is the continuation of [tutorial: nginx + php-fpm deployment](../tutorial.nginx+phpfpm-deployment/README.md).  

<br/>

Move to working path:  
  ```shell
  cd {Project Root}/tutorial.deploy-a-phpfpm-deployment/  
  ```

<br/>

### Deploy a new PHP *Deployment*  
If an deploy an updated php *deployment*, run the following command:    
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
