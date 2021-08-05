# tutorial.5

This tutorial shows [`How To Deploy a PHP Application with Kubernetes on Ubuntu 18.04`](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-php-application-with-kubernetes-on-ubuntu-18-04). 

<br/><br/><br/>

## Run  
### Step 1 — Creating the PHP-FPM and Nginx Services  
```shell
$ kubectl apply -f resources/service
  service/php created
  service/nginx created
```

<br/>

### Step 2 — Creating the Persistent Volume  
```shell
$ kubectl apply -f resources/volume
  persistentvolumeclaim/code created
```

<br/>

### Step 3 — Creating a Nginx ConfigMap  
```shell
$ kubectl apply -f resources/configmap  
  configmap/nginx-config created 
```

<br/>

### Step 4 — Creating the PHP-FPM and Nginx Deployment  
```shell
$ kubectl apply -f resources/deployment
  deployment.apps/php created
  deployment.apps/nginx created
```

<br/><br/><br/>

## References  
* 
