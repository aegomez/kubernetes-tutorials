# Exposing an External IP Address to Access an Application in a Cluster

Example: create a Kubernetes Service object that exposes an external IP Address.

It is recommended to use a cloud provider like Google Kubernetes Engine or Amazon Web Services to create the cluster, to use an external load balancer to its full potential.

This example uses Minikube, some instructions may differ.

## Instructions

* Run three instances of a [Hello World](load-balancer-example.yaml) application:
```
kubectl apply -f load-balancer-example.yaml
```

* Display information about the Deployment:
```
kubectl get deployment hello-world
kubectl describe deployment hello-world
```

* Display information about the ReplicaSet objects:
```
kubectl get replicasets
kubectl describe replicasets
```

* Create a Service object that exposes the deployment:
```
kubectl expose deployment hello-world \
  --type=LoadBalancer \
  --name=the-service
```

* Display information about the Service:
```
kubectl get service the-service
```
> In Minikube the external IP address will always be shown as \<pending\>. With a cloud provider, it may take a minute to be available.

* Display detailed information about the Service:
```
kubectl describe service the-service
```
> Make a note of the external IP address (`LoadBalancer Ingress`) exposed by the service. Also note the value of `Port` and `NodePort`.

* Verify that the service's `Endpoints` are the internal addresses of the pods running the application:
```
kubectl get pods -o wide
```

* Access the Hello World application:
```sh
# <external-ip>: LoadBalancer Ingress
# <port>: Port
curl http://<external-ip>:<port>

# If using minikube, type this instead:
minikube service <service-name>
```

## Cleaning up

* To delete the Service:
```
kubectl delete service the-service
```

* To delete the Deployment, the ReplicaSet, and the Pods:
```
kubectl delete deployment hello-world
```