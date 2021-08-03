# k8s Tutorials  

<br/><br/><br/>

## Tutorials  
### [tutorial.0](tutorial.0/README.md)  
  * [Run a Stateless Application Using a Deployment](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)  

<br/>

### [tutorial.1](tutorial.1/README.md)  
  * [PHP-FPM, Nginx, Kubernetes, and Docker](https://matthewpalmer.net/kubernetes-app-developer/articles/php-fpm-nginx-kubernetes.html)  

<br/>

### tutorial.2  
  * [Kubernetes Basic PHP 7.3 Application with Nginx on Google Cloud](https://www.cloudbooklet.com/kubernetes-basic-php-application-with-nginx-on-google-cloud/)  

  * [How To Deploy a PHP Application with Kubernetes on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-php-application-with-kubernetes-on-ubuntu-16-04)  


<br/><br/><br/>

## Tips  
### docker  
Build an image from a Dockerfile(`.` is current path).  
`-t` Option tags in the 'name:tag' format

```shell
$ docker build . -t my-php-app:1.0.0
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

```shell
$ eval $(minikube docker-env)
```

```shell
$ minikube docker-env
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

<br/><br/><br/>

## References  
* [kubernetes - Images](https://kubernetes.io/docs/concepts/containers/images/)  
* [minikube - Pushing images](https://minikube.sigs.k8s.io/docs/handbook/pushing/)  
* [Connecting Applications with Services](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/)  
* [Exposing an External IP Address to Access an Application in a Cluster](https://kubernetes.io/docs/tutorials/stateless-application/expose-external-ip-address/)  