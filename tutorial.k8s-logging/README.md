# tutorial: k8s logging  

Written with reference to [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)  

<br/>

## Logging Architecture  

Application logs can help you understand what is happening inside your application. The logs are particularly useful for debugging problems and monitoring cluster activity.  

In a cluster, logs should have a separate storage and lifecycle independent of nodes, pods, or containers. This concept is called *cluster-level logging*.  

Cluster-level logging architectures require a separate backend to store, analyze, and query logs. Kubernetes does not provide a native storage solution for log data. Instead, there are many logging solutions that integrate with Kubernetes. The following sections describe how to handle and store logs on nodes.  

<br/><br/>

### [Basic logging in Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/#basic-logging-in-kubernetes)  
다음 예제는 텍스트를 초당 한 번 stdout 스트림에 쓰는 컨테이너에 대한 `Pod` 사양을 사용합니다.  

`resources/debug/counter-pod.yaml`  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args: [/bin/sh, -c,
            'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
```

To run this pod, use the following command:  
```shell
kubectl apply -f https://k8s.io/examples/debug/counter-pod.yaml
  pod/counter created
```

To fetch the logs, use the `kubectl logs` command, as follows:  
```shell
kubectl logs counter
```

<br/><br/>

### [Logging at the node level](https://kubernetes.io/docs/concepts/cluster-administration/logging/#logging-at-the-node-level)  

<figure>
<div style="text-align:center">
  <a href="https://d33wubrfki0l68.cloudfront.net/59b1aae2adcfe4f06270b99a2789012ed64bec1f/4d0ad/images/docs/user-guide/logging/logging-node-level.png">
  <img src="https://d33wubrfki0l68.cloudfront.net/59b1aae2adcfe4f06270b99a2789012ed64bec1f/4d0ad/images/docs/user-guide/logging/logging-node-level.png" style="width: 360px; max-width: 100%; height: auto" title="tutorial.nginx+phpfpm-pod" />
  </a>
</div>
</figure>

<br/><br/><br/>

## References  
* [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)  