# tutorial.1

<br/><br/><br/>

## Run  
* Move to working path:  
  ```shell
  cd {Project Root}/tutorial.1/  
  ```

<br/>

* Build php app image if not exists. and tag by `tut01-php:1.0.0`:  
  ```
  $ docker build . -t tut01-php:1.0.0
  ```

<br/>

* **(Minikube Only)** Start a new minikube cluster:  
  ```
  $ minikube delete
  $ minikube start
  $ minikube image load tut01-php:1.0.0
  ```

> In WSL, You can also use `localhost` with the `{Node IP}` obtained as a result of  
> executing command `minikube ip`

<br/>

* Create kubernetes resources:  
  ```shell
  $ kubectl apply -f resources/
    configmap/tut01-nginx-config created
    pod/tut01-nginx-php created
  ```

<br/>

* Expose pod:  
  ```shell
  $ kubectl expose pod tut01-nginx-php --type=NodePort --port=80
  ```

<br/>

* Show all resources. And check *Running* Status:  
  ```shell 
  $ kubectl get all
  NAME                  READY   STATUS    RESTARTS   AGE
  pod/tut01-nginx-php   2/2     Running   0          18s

  NAME                    TYPE      CLUSTER-IP    EXTERNAL-IP PORT(S)               AGE
  service/kubernetes      ClusterIP 10.96.0.1     <none>      443/TCP               110d
  service/tut01-nginx-php NodePort  10.106.152.89 <none>      80:{Service Port}/TCP 4s
  ```

<br/>

* **(Minikube Only)** Get url to access to a service:  
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

* Describe a specific kubernetes service:  
  ```shell
  $ kubectl describe service {Service}
  ```

<br/>

* Get all resources in the kubernetes:  
  ```
  $ kubectl get all
  ```

<br/>

* Run command `/bin/sh` in kubernetes pod:  
  ```
  $ kubectl exec -it tut01-nginx-php -c nginx -- /bin/sh
  ```

<br/>

* **(Minikube Only)** Delete minikube cluster(using-Minikube, non-WSL):  
  ```
  $ minikube delete
  ```

<br/><br/><br/>

## References  
* [PHP-FPM, Nginx, Kubernetes, and Docker](https://matthewpalmer.net/kubernetes-app-developer/articles/php-fpm-nginx-kubernetes.html)  
* [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)  
* [How to Install and Configure Minikube on Ubuntu](https://www.liquidweb.com/kb/how-to-install-and-configure-minikube-on-ubuntu/)  
  This article will demonstrate how to install and configure Minikube to set up a small Kubernetes cluster  
