# k8s Tutorials  

## Prerequisites  
### [Docker Desktop](https://docs.docker.com/desktop/)  
*Docker Desktop* is an easy-to-install application for your Mac or Windows environment  
that enables you to build and share containerized applications and microservices.  
* **Download and install**  
  Docker Desktop is available for Mac and Windows.   
  For download information, system requirements, and installation instructions, see:  
  * [Install Docker Desktop on Mac](https://docs.docker.com/docker-for-mac/install/)  
  * [Install Docker Desktop on Windows](https://docs.docker.com/docker-for-windows/install/)  
* **Configure Docker Desktop**  
  To learn about the various UI options and their usage, see:
  * [Docker Desktop for Mac user manual](https://docs.docker.com/docker-for-mac/)  
  * [Docker Desktop for Windows user manual](https://docs.docker.com/docker-for-windows/)  

> The following tutorial did not use minikube,  
> I checked it using only the Kubernetes cluster in *Docker Desktop*. 

<br/><br/><br/>

## Tutorials  
### [tutorial: nginx deployment](tutorial.nginx-deployment/README.md)  
Written with reference to [Run a Stateless Application Using a Deployment](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)  

#### Objectives  
* Create a k8s Nginx *deployment*  
* Update the *deployment*  

<br/><br/>

### [tutorial: nginx + php-fpm pod](tutorial.nginx+phpfpm-pod/README.md)  
Written with reference to [PHP-FPM, Nginx, Kubernetes, and Docker](https://matthewpalmer.net/kubernetes-app-developer/articles/php-fpm-nginx-kubernetes.html)  

<figure>
<div style="text-align:center">
  <a href="https://drive.google.com/uc?export=view&id=1eweNr6OMtE86-tRa0MfGTdy4Kfpyo_3u">
  <img src="https://drive.google.com/uc?export=view&id=1tylVCpsrV2lAwzBqkQ_U-2MBLFMMeyWt" style="width: 360px; max-width: 100%; height: auto" title="tutorial.nginx+phpfpm-pod" />
  </a>
</div>
</figure>

#### Objectives  
* Create a k8s *pod*  
  * Add a Nginx container  
  * Add a PHP-FPM container   

<br/><br/>

### [tutorial: nginx + php-fpm deployment](tutorial.nginx+phpfpm-deployment/README.md)  
Written with reference to [Kubernetes Basic PHP 7.3 Application with Nginx on Google Cloud](https://www.cloudbooklet.com/kubernetes-basic-php-application-with-nginx-on-google-cloud/).  

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which built using a Dockerfile in [tutorial: nginx + php-fpm pod](tutorial.nginx+phpfpm-pod/README.md)  

<figure>
<div style="text-align:center">
  <a href="https://drive.google.com/uc?export=view&id=1QpH2nVmUOwrx9nfv3S0QJKeilWSb9ClO">
  <img src="https://drive.google.com/uc?export=view&id=1YjRNN7XGG3XjnKdBK5cv4JQG-9dMkLU5" style="width: 360px; max-width: 100%; height: auto" title="tutorial.nginx+phpfpm-deployment" />
  </a>
</div>
</figure>

#### Objectives  
* Create a k8s Nginx *deployment*  
* Create a k8s PHP-FPM *deployment*  
* Create a k8s *Persistent Volume*  
  * Bind the volume to the *deployment*  

<br/><br/>

### [tutorial: deploy a php-fpm deployment](tutorial.deploy-a-phpfpm-deployment/README.md)  
Shows deploy a new php container in [tutorial: nginx + php-fpm deployment](tutorial.nginx+phpfpm-deployment/README.md).  

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which is built from Dockerfile in [tutorial: nginx + php-fpm pod](tutorial.nginx+phpfpm-pod/README.md)   

> This tutorial is the continuation of [tutorial: nginx + php-fpm deployment](tutorial.nginx+phpfpm-deployment/README.md).  

#### Objectives  
* Deploy a new php *deployment*   

<br/><br/>

### [tutorial: nginx + php-fpm deployment using pv](tutorial.nginx+phpfpm-deployment-using-pv/README.md)  
Shows remove use of persistent volume in [tutorial: nginx + php-fpm deployment](tutorial.nginx+phpfpm-deployment/README.md). 

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which is built from Dockerfile in [tutorial: nginx + php-fpm pod](tutorial.nginx+phpfpm-pod/README.md)   

#### Objectives  
* Create a k8s Nginx *deployment*  
* Create a k8s PHP-FPM *deployment*  

<br/><br/>

### [tutorial: k8s logging](tutorial.k8s-logging/README.md)  
Written with reference to [*Fluentd* on Kubernetes: Log collection explained](https://www.youtube.com/watch?v=6kmHvXdAzIM&t=316s) by *That DevOps Guy*.  

<figure>
<div style="text-align:center">
  <a href="https://d33wubrfki0l68.cloudfront.net/2585cf9757d316b9030cf36d6a4e6b8ea7eedf5a/1509f/images/docs/user-guide/logging/logging-with-node-agent.png">
  <img src="https://d33wubrfki0l68.cloudfront.net/2585cf9757d316b9030cf36d6a4e6b8ea7eedf5a/1509f/images/docs/user-guide/logging/logging-with-node-agent.png" style="width: 360px; max-width: 100%; height: auto" title="node-level logging agent" />
  </a>
</div>
</figure>

Implement cluster-level logging by including a node-level logging agent on each node.  

#### Objectives  
* Understand logging architecture    
* Create a node logging agent  
  * fluentd *daemonset*    

<br/><br/>

### [tutorial: nginx + unixsocket php-fpm deployment using pv](tutorial.nginx+unixsocket-phpfpm-deployment-using-pv/README.md)  
Using unix socket for fastcgi_pass in [tutorial: nginx + php-fpm deployment using pv](tutorial.nginx+phpfpm-deployment-using-pv/README.md). 

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which is built from Dockerfile in [tutorial: nginx + php-fpm pod](tutorial.nginx+phpfpm-pod/README.md)  

  working...

#### Objectives  
* K8s deployment  
  * Nginx container  
  * PHP-FPM container   
    * Using unix socket for *fastcgi_pass*  

<br/><br/>

### [tutorial: nginx + unixsocket php-fpm deployment](tutorial.nginx+unixsocket-phpfpm-deployment/README.md)  
Using unix socket for fastcgi_pass in [tutorial: nginx + php-fpm deployment](tutorial.nginx+phpfpm-deployment/README.md). 

> This tutorial uses a `tut01-php:1.0.0` image,  
> Which is built from Dockerfile in [tutorial: nginx + php-fpm pod](tutorial.nginx+phpfpm-pod/README.md)  

  working...

#### Objectives  
* Create a k8s *deployment*  
  * Nginx container  
  * PHP-FPM container  
    * Using unix socket for *fastcgi_pass*  
* Create a k8s Persistent Volume  
  * Bind the volume to the deployment  

<br/><br/><br/>

## Tips  
### docker  
Build an image from a Dockerfile(`.` is current path).  
`-t` Option tags in the 'name:tag' format

```shell
$ docker build . -t my-php-app:1.0.0
```

<br/>

### kubernetes  
Apply a kubernetes config:  
```shell
$ kubectl apply -f {k8s config path or yaml}
```

Delete a kubernetes config:  
```shell
$ kubectl delete -f {k8s config path or yaml}
```

Expose kubernetes service:  
```shell
$ kubectl expose {Type} {Name} --type={Expose Type} --port={Port}
```

  * Example:  
  ```shell
  $ kubectl expose pod k8s-nginx-php --type=NodePort --port=80
  ```

[Get a Shell to a Running Container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)  
Verify that the container is running:  
```shell
$ kubectl get pod {Pod Name}
```

Get a shell to the running container:  
```shell
$ kubectl exec --stdin --tty {Pod Name} -- /bin/bash  
```

[Opening a shell when a Pod has more than one container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/#opening-a-shell-when-a-pod-has-more-than-one-container):  
```shell
$ kubectl exec -i -t {Pod Name} --container {Container Name} -- /bin/bash
```

<br/>

### minikube  
Start a new minikube cluster:  
```shell
$ minikube start
```

Delete a minikube cluster:  
```shell
$ minikube delete
```

The command `minikube docker-env` returns a set of Bash environment variable  
exports to configure your local environment to re-use the Docker daemon  
inside the Minikube instance.  
```shell
$ minikube docker-env
```

To point your terminal to use the docker daemon inside minikube run this:  
```shell
$ eval $(minikube docker-env)
```

Load image to minikube:  
```shell
$ minikube image load my-php-app:1.0.0
```

Log into the minikube environment (for debugging):  
```shell
$ minikube ssh
```

View logs of minikube:  
```shell
$ minikube logs
```

<br/><br/><br/>

## References  
### Kubernetes  
#### [Concepts](https://kubernetes.io/docs/concepts/)  
* [Containers](https://kubernetes.io/docs/concepts/containers/)  
  * [Images](https://kubernetes.io/docs/concepts/containers/images/)  
* [Configuration](https://kubernetes.io/docs/concepts/configuration/)  
  * [Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)  
  * [Managing Resources for Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)  
  * [Resource requests and limits of Pod and Container](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-requests-and-limits-of-pod-and-container)  
* [Services, Load Balancing, and Networking](https://kubernetes.io/docs/concepts/services-networking/)  
  * [Service](https://kubernetes.io/docs/concepts/services-networking/service/)
  * [Connecting Applications with Services](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/)  
  * [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)  
  * [Ingress Controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)  
* [Storage](https://kubernetes.io/docs/concepts/storage/)  
  * [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)  
    * [Kubernetes 1.14: Local Persistent Volumes GA](https://kubernetes.io/blog/2019/04/04/kubernetes-1.14-local-persistent-volumes-ga/)  
    * [Kubernetes Persistent Volumes with Deployment and StatefulSet](https://akomljen.com/kubernetes-persistent-volumes-with-deployment-and-statefulset/)  
    * [Kubernetes: Persistent Volume For Beginners:](https://medium.com/@muneeburrehman2610/kubernetes-persistent-volume-for-beginners-a13cbe5bdeea)  
    * [Providing Persistent Storage to Windows Containers](https://cloud.redhat.com/blog/providing-persistent-storage-to-windows-containers)  
#### [Tasks](https://kubernetes.io/docs/tasks/)  
* [Access Applications in a Cluster](https://kubernetes.io/docs/tasks/access-application-cluster/)  
#### [Tutorials](https://kubernetes.io/docs/tutorials/)  
* [Exposing an External IP Address to Access an Application in a Cluster](https://kubernetes.io/docs/tutorials/stateless-application/expose-external-ip-address/)  

#### [Reference](https://kubernetes.io/docs/reference/)  
* [Using a feature](https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/)  
  A feature can be in *Alpha*, *Beta* or *GA* stage.  
  A *General Availability(GA)* feature is also referred to as a stable feature.  

<br/>

### minikube  
* [minikube - Pushing images](https://minikube.sigs.k8s.io/docs/handbook/pushing/)  

<br/>

### Examples  
* [Drupal 8 on the IBM Cloud (based on NGINX, PHP-FPM)](https://github.com/IBM/drupal-nginx-php-kubernetes)  
