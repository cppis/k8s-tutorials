# tutorial: k8s logging  

Written with reference to [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)  

<br/>

## Logging Architecture  

Application logs can help you understand what is happening inside your application. The logs are particularly useful for debugging problems and monitoring cluster activity.  

In a cluster, logs should have a separate storage and lifecycle independent of nodes, pods, or containers. This concept is called *cluster-level logging*.  

Cluster-level logging architectures require a separate backend to store, analyze, and query logs. Kubernetes does not provide a native storage solution for log data. Instead, there are many logging solutions that integrate with Kubernetes. The following sections describe how to handle and store logs on nodes.  

<br/><br/>

### [Basic logging in Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/#basic-logging-in-kubernetes)  
This example uses a Pod specification with a container to write text to the standard output stream once per second.  

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
  0: Mon Jan  1 00:00:00 UTC 2001
  1: Mon Jan  1 00:00:01 UTC 2001
  2: Mon Jan  1 00:00:02 UTC 2001
  ...
```

You can use `kubectl logs --previous` to retrieve logs from a previous instantiation of a container.  

<br/><br/>

### [Logging at the node level](https://kubernetes.io/docs/concepts/cluster-administration/logging/#logging-at-the-node-level)  

<figure>
<div style="text-align:center">
  <a href="https://d33wubrfki0l68.cloudfront.net/59b1aae2adcfe4f06270b99a2789012ed64bec1f/4d0ad/images/docs/user-guide/logging/logging-node-level.png">
  <img src="https://d33wubrfki0l68.cloudfront.net/59b1aae2adcfe4f06270b99a2789012ed64bec1f/4d0ad/images/docs/user-guide/logging/logging-node-level.png" style="width: 360px; max-width: 100%; height: auto" title="tutorial.nginx+phpfpm-pod" />
  </a>
</div>
</figure>

A container engine handles and redirects any output generated to a containerized application's `stdout` and `stderr` streams. For example, the Docker container engine redirects those two streams to a logging driver, which is configured in Kubernetes to write to a file in JSON format.  

An important consideration in node-level logging is implementing log rotation, so that logs don't consume all available storage on the node. Kubernetes is not responsible for rotating logs, but rather a deployment tool should set up a solution to address that. For example, in Kubernetes clusters, deployed by the `kube-up.sh` script, there is a [`logrotate`](https://linux.die.net/man/8/logrotate) tool configured to run each hour. You can also set up a container runtime to rotate an application's logs automatically.  

<br/><br/>

### [Cluster-level logging architectures](https://kubernetes.io/docs/concepts/cluster-administration/logging/#cluster-level-logging-architectures)  

<br/><br/><br/>

## References  
* [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)  
* [Kubernetes Logging Simplified – Pt 1: Applications](https://observiq.com/blog/kubernetes-logging-simplified-pt-1-applications/)  
* [A Practical Guide to Kubernetes Logging](https://logz.io/blog/a-practical-guide-to-kubernetes-logging/)  
* [Introduction to Fluentd on Kubernetes](https://github.com/marcel-dempers/docker-development-youtube-series/tree/master/monitoring/logging/fluentd/kubernetes)  