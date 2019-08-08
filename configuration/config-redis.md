# Configuring Redis using a ConfigMap

Examples found in the [Kubernetes Documentation](https://kubernetes.io/docs/tutorials/configuration/configure-redis-using-configmap/).

## Objective

Configure a Redis cache using data stored in a ConfigMap.

## Instructions

* Create a `kustomization.yaml` [file](redis/kustomization.yaml), containing a ConfigMap from the `redis-config` [file](redis/redis-config) and a pod resource config from the `redis-pod.yaml` [file](redis/redis-pod.yaml).

* Apply the directory by running `kubectl apply -k <directory>`.

* Examine the created objects with `kubectl get -k <directory>`

* Use `kubectl exec -it <pod-name> <command>` to enter the pod and run the `redis-cli` tool to verify that the configuration was correctly applied.
  * Use `CONFIG GET <KEY>` inside the cli tool.
