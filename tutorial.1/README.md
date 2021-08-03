# tutorial.1

<br/><br/><br/>

## Run  
* Move to working path:  
```shell
cd {Project Root}/tutorial.1/  
```

<br/>

* Build php app image if not exists:  
```
$ docker build . -t k8s-php:1.0.0
```

<br/>

* Start minikube cluster(using-Minikube, non-WSL):  
```
$ minikube start
$ minikube image load k8s-php:1.0.0
```

> In WSL, You can also use `localhost` with the `{Node IP}` obtained as a result of  
> executing command `minikube ip`

<br/>

* Create kubernetes workloads:  
```shell
$ kubectl apply -f resources/  
  configmap/k8s-nginx-config created
  pod/k8s-nginx-php created
```

<br/>

* Expose service:  
```shell
$ kubectl expose pod k8s-nginx-php --type=NodePort --port=80
```

<br/>

* Get service url:  
```shell
$ minikube service k8s-nginx-php --url
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

* Describe kubernetes service:  
```shell
$ $ kubectl describe service k8s-nginx-php
```

<br/>

* Check running app in the kubernetes:  
```
$ kubectl get all
$ wget {Service Url}
```

<br/>

* Run command `/bin/sh` in kubernetes pod:  
```
$ kubectl exec -it k8s-nginx-php -c nginx -- /bin/sh
```

<br/>

* Delete minikube cluster(using-Minikube, non-WSL):  
```
$ minikube delete
```

<br/><br/><br/>

## References  
* [PHP-FPM, Nginx, Kubernetes, and Docker](https://matthewpalmer.net/kubernetes-app-developer/articles/php-fpm-nginx-kubernetes.html)  
* [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)  
* [How to Install and Configure Minikube on Ubuntu](https://www.liquidweb.com/kb/how-to-install-and-configure-minikube-on-ubuntu/)  
  This article will demonstrate how to install and configure Minikube to set up a small Kubernetes cluster  
