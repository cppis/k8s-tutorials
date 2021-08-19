# tutorial: nginx deployment

Written with reference to [Run a Stateless Application Using a Deployment](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)  

## [Objectives](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/#objectives)  
* Create an nginx deployment.  
* Use kubectl to list information about the deployment.  
* Update the deployment.  

<br/>

## Run  
Move to working path:  
  ```shell
  cd {Project Root}/tutorial.nginx-deployment/  
  ```

### Creating and exploring an nginx deployment  
Creating a Kubernetes Deployment object:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

<br/>

Create a Deployment based on the YAML file:  
```shell
$ kubectl apply -f app/deployment.yaml
```

<br/>

Display information about the Deployment:  
```shell
$ kubectl describe deployment nginx-deployment
  NAME                  READY     STATUS    RESTARTS   AGE
 nginx-deployment-...   1/1       Running   0          16h
 nginx-deployment-...   1/1       Running   0          16h
```

<br/>

List the Pods created by the deployment:  
```shell
$ kubectl describe pod <pod-name>
```

<br/>

Display information about a Pod:  
```shell
$ kubectl get pods -l app=nginx
```

<br/>

Expose service:  
```
$ kubectl expose deployment nginx-deployment --type=LoadBalancer --name=my-service -l app=nginx
```

<br/>

Delete deployment:  
```shell
$ kubectl delete -f app/deployment.yaml
```

<br/><br/><br/>

## Reference  
* [Run a Stateless Application Using a Deployment](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)  