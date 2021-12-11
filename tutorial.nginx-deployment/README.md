# tutorial: nginx deployment

Written with reference to [Run a Stateless Application Using a Deployment](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)  

## [Objectives](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/#objectives)  
* Create an nginx deployment.  
* Use kubectl to list information about the deployment.  
* Update the deployment.  

<br/>

## Run  
Move to working path:  
  ```bash
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
```bash
kubectl apply -f app/deployment.yaml
```

<br/>

Display information about the Deployment:  
```bash
kubectl get pods  
  NAME                  READY     STATUS    RESTARTS   AGE
  nginx-deployment-...   1/1       Running   0          16s
  nginx-deployment-...   1/1       Running   0          16s
```

<br/>

List the Pods created by the deployment:  
```bash
kubectl describe pod <pod-name>
```

<br/>

Display information about a Pod:  
```bash
kubectl get pods -l app=nginx
```

<br/>

Expose a service:  
```bash
kubectl expose deployment nginx-deployment --type=LoadBalancer --name=my-service -l app=nginx
```

<br/>

Get exposed port of a service:  
```bash
APP_PORT=$(kubectl get -o jsonpath="{.spec.ports[0].nodePort}" services my-service)
```

<br/>

Check a service:  
```bash
wget localhost:$APP_PORT

  --2021-12-11 23:39:29--  http://localhost:31894/
  Resolving localhost (localhost)... 127.0.0.1
  Connecting to localhost (localhost)|127.0.0.1|:31894... connected.
  HTTP request sent, awaiting response... 200 OK
  Length: 612 [text/html]
  Saving to: ‘index.html’

  index.html                       100%[==========================================================>]     612  --.-KB/s    in 0s

  2021-12-11 23:39:29 (119 MB/s) - ‘index.html’ saved [612/612]
```

<br/>

Delete a deployment:  
```bash
kubectl delete -f app/deployment.yaml
```

<br/>

Delete an exposed service:  
```shell
$ kubectl delete service my-service
```

<br/><br/><br/>

## Reference  
* [Run a Stateless Application Using a Deployment](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)  