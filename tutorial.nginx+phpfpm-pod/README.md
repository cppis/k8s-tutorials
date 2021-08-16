# tutorial.01

This tutorial shows how to create a `nginx + php-fpm` app in a Kubernetes. 

<br/>

<figure>
<div style="text-align:center">
  <a href="https://drive.google.com/uc?export=view&id=1eweNr6OMtE86-tRa0MfGTdy4Kfpyo_3u">
  <img src="https://drive.google.com/uc?export=view&id=1eweNr6OMtE86-tRa0MfGTdy4Kfpyo_3u" style="width: 560px; max-width: 100%; height: auto" title="tutorial.01" />
  </a>
</div>
</figure>

<br/><br/><br/>

## Run  
* Move to working path:  
  ```shell
  cd {Project Root}/tutorial.01/  
  ```

<br/>

* Build php app image if not exists. and tag by `tut01-php:1.0.0`:  
  ```shell
  $ docker build . -t tut01-php:1.0.0
  ```

<br/>

* **(Minikube Only)** To start a new minikube cluster.  
  Run the following command:  
  ```
  $ minikube delete
  $ minikube start
  $ minikube image load tut01-php:1.0.0
  ```

<br/>

* To create all kubernetes resources.  
  Run the following command:  
  ```shell
  $ kubectl apply -f resources/
    configmap/tut01-nginx-config created
    pod/tut01-nginx-php created
  ```

<br/>

* To expose a pod. Run the following command:  
  ```shell
  $ kubectl expose pod tut01-nginx-php --type=NodePort --port=80
  ```

<br/>

* To check all resources are *Running* Status.  
  Run the following command:  
  ```shell 
  $ kubectl get all
    NAME                  READY   STATUS    RESTARTS   AGE
    pod/tut01-nginx-php   2/2     Running   0          18s
  ...
  ```

<br/><br/><br/>

## Test  
* To get the Access URL to service. view all resources and get {Service Port}:  
  ```shell 
  $ kubectl get all
    ...
    NAME                    TYPE      CLUSTER-IP    EXTERNAL-IP PORT(S)               AGE
    service/kubernetes      ClusterIP 10.96.0.1     <none>      443/TCP               110d
    service/tut01-nginx-php NodePort  10.106.152.89 <none>      80:{Service Port}/TCP 4s
  ```

  Without minikube, Target URL is a `localhost:{Service Port}`  
  With minikube, Target URL is `{Node IP}:{Service Port}` and `{Node IP}` is obtained  
  as a result of executing `minikube ip`  

<br/>

* **(Minikube Only)** Get url to access to a service.  
  Run the following command:  
  ```shell
  $ minikube service tut01-nginx-php --url
    {Service Url}
  ```

<br/><br/><br/>

## Tips  
* [The storage location of Docker images and containers](https://www.freecodecamp.org/news/where-are-docker-images-stored-docker-container-paths-explained/)  
  * Ubuntu(WSL): `/var/lib/docker/`  
  * Fedora: `/var/lib/docker/`  
  * Debian: `/var/lib/docker/`  
  * Windows: `C:\ProgramData\DockerDesktop`  
  * MacOS: `~/Library/Containers/com.docker.docker/Data/vms/0/`    

  <br/>

  Run the following command to inspect details about image:  
  ```
  $ docker inspect NAME|ID
  ```

* To describe a specific kubernetes service.  
  Run the following command:  
  ```shell
  $ kubectl describe service {Service}
  ```

<br/>

* To get all resources in the kubernetes. Run the following command:  
  ```
  $ kubectl get all
  ```

<br/>

* To run command `/bin/sh` in kubernetes pod.  
  Run the following command:  
  ```
  $ kubectl exec -it tut01-nginx-php -c nginx -- /bin/sh
  ```

<br/>

* **(Minikube Only)** To delete minikube cluster. Run the following command:  
  ```
  $ minikube delete
  ```

<br/><br/><br/>

## References  
* [PHP-FPM, Nginx, Kubernetes, and Docker](https://matthewpalmer.net/kubernetes-app-developer/articles/php-fpm-nginx-kubernetes.html)  
* [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)  
* [How to Install and Configure Minikube on Ubuntu](https://www.liquidweb.com/kb/how-to-install-and-configure-minikube-on-ubuntu/)  
  This article will demonstrate how to install and configure Minikube to set up a small Kubernetes cluster  
