## DevOps Challenge

## Option 3

The Task

Your task is to provide a git repository that will contain the code to build a Kubernetes Operator that does
the following:
1. Sends a slack message hello world from $name when a pod resource with $name in the
spec is created
2. Sends a slack message things have changed, $name when the above custom resource is
modified
3. Sends a slack message goodbye world from $name when the above custom resource is deleted
You may use any language and/or toolset to achieve the above as long as they are freely available


## Prerequisites

You will need a Kubernetes cluster, that's it! By default it is assumed, that the kubelet uses token authentication and authorization, as otherwise Prometheus needs a client certificate, which gives it full access to the kubelet, rather than just the metrics. Token authentication and authorization allows more fine grained and easier access control.

This means the kubelet configuration must contain these flags:

* `--authentication-token-webhook=true` This flag enables, that a `ServiceAccount` token can be used to authenticate against the kubelet(s). This can also be enabled by setting the kubelet configuration value `authentication.webhook.enabled` to `true`.
* `--authorization-mode=Webhook` This flag enables, that the kubelet will perform an RBAC request with the API to determine, whether the requesting entity (Prometheus in this case) is allowed to access a resource, in specific for this project the `/metrics` endpoint. This can also be enabled by setting the kubelet configuration value `authorization.mode` to `Webhook`.

This stack provides [resource metrics](https://github.com/kubernetes/metrics#resource-metrics-api) by deploying the [Prometheus Adapter](https://github.com/DirectXMan12/k8s-prometheus-adapter/).
This adapter is an Extension API Server and Kubernetes needs to be have this feature enabled, otherwise the adapter has no effect, but is still deployed.

## Compatibility

The following Kubernetes versions are supported and work as we test against these versions in their respective branches. But note that other versions might work!

| kube-prometheus stack                                                                      | Kubernetes 1.20 | Kubernetes 1.21 | Kubernetes 1.22 | Kubernetes 1.23 | Kubernetes 1.24 |
|--------------------------------------------------------------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
| [`release-0.8`](https://github.com/prometheus-operator/kube-prometheus/tree/release-0.8)   | ✔               | ✔               | ✗               | ✗               | ✗               |
| [`release-0.9`](https://github.com/prometheus-operator/kube-prometheus/tree/release-0.9)   | ✗               | ✔               | ✔               | ✗               | ✗               |
| [`release-0.10`](https://github.com/prometheus-operator/kube-prometheus/tree/release-0.10) | ✗               | ✗               | ✔               | ✔               | ✗               |
| [`release-0.11`](https://github.com/prometheus-operator/kube-prometheus/tree/release-0.11) | ✗               | ✗               | ✗               | ✔               | ✔               |
| [`main`](https://github.com/prometheus-operator/kube-prometheus/tree/main)                 | ✗               | ✗               | ✗               | ✗               | ✔               |


## Install

You need to have a slack webhook set up for a channel to which the messages should come to 



1. Change the slack api hook url(line 16) and slack channel(line 43) in file `./manifests/alertmanager-secret.yaml` to yours



2. Create the namespace and CRDs, and then wait for them to be availble before creating the remaining resources
```
kubectl create -f manifests/setup
```
#Wait until the "servicemonitors" CRD is created. The message "No resources found" means success in this context.
```
until kubectl get servicemonitors --all-namespaces ; do date; sleep 1; echo ""; done


kubectl create -f manifests/
```

3. Create the app resources (originally there are no pods to fit the task)

```
kubectl create -f example-app/
```
4. Create a pod
```
kubectl scale deployments example-app --replicas=1
```
#you should get the expected message whihin a few minues

5. Delete a pod
```
kubectl scale deployments example-app --replicas=1
```
#you should get the expected message whihin a few minues
