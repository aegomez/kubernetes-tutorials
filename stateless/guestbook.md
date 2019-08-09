# Example: Deploying PHP Guestbook application with Redis

Build and deploy a simple, multi-tier application using Kubernetes and Docker.

Source: [Kubernetes Documentation](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/).

## Objectives

* Start up a single Redis master to store guestbook entries.
* Start up multiple Redis slaves to serve reads.
* Start up the guestbook frontend.
* Expose and view the Frontend Service.
* Clean up.

## Before beginning

A Kubernetes cluster and `kubectl` are required. Create a cluster with a cloud provider (Google Kubernetes Engine, Amazon Web Services, ), using Minikube, or use a Kubernetes playground (Katacoda, Play with Kubernetes).

The following examples were tested using Minikube v1.3.0 and kubectl 1.15.2.

## Start up the Redis Master.

The guestbook application writes its data to a Redis master instance, and reads data from multiple Redis slave instances.

### Creating the Redis Master Deployment

The `redis-master-deployment.yaml` [manifest file](guestbook/redis-master-deployment.yaml) specifies a Deployment controller that runs a single replica Redis master Pod.

```sh
# Apply the Redis Master Deployment from file
kubectl apply -f ./guestbook/redis-master-deployment.yaml

# Verify the Master is running
kubectl get pods

# View logs from the Master
kubectl logs -f <pod-name>
```

### Creating the Redis Master Service

Apply a [Service](guestbook/redis-master-service.yaml) to proxy the traffic to the Redis Master Pod.
```sh
# Apply the Master Service from file
kubectl apply -f guestbook/redis-master-service.yaml

# Verify the Master Service is running
kubectl get services
```

## Start up the Redis Slaves

Make the Redis master highly available by adding replica Redis slaves.

### Creating the Redis Slave Deployment

Specify two replicas in the Deployment object's [manifest file](guestbook/redis-slave-deployment.yaml).

```sh
# Create the Redis slave deployment from file
kubectl apply -f guestbook/redis-slave-deployment.yaml

# Verify that the slave pods are running
kubectl get pods
```

### Creating the Redis Slaves Service

Setup a [Service](guestbook/redis-slave-service.yaml) to make the Redis slaves discoverable, so the application can communicate to them to read data.

```sh
# Apply the slave Service from file
kubectl apply -f guestbook/redis-slave-service.yaml

# Verify the Master Service is running
kubectl get services
```

## Setup and Expose the Guestbook Frontend

The [guestbook application](guestbook/frontend-deployment.yaml) has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the `redis-master` service for write requests and the `redis-slave` service for read requests.

### Creating the Guestbook Frontend Deployment

```sh
# frontend deployment
kubectl apply -f guestbook/frontend-deployment.yaml

# Verifiy that the frontend replicas are running
kubectl get pods -l app=guestbook -l tier=frontend
```

### Creating the Frontend Service

Configure the frontend to be visible from outside the cluster. Setup [the service](guestbook/frontend-service.yaml) to type `LoadBalancer` or `NodePort`.

```sh
kubectl apply -f guestbook/frontend-service.yaml

# Verify the service is running
kubectl get services
```

### Viewing the Frontend Service

```sh
# Minikube 
minikube service frontend

# or get the EXTERNAL-IP and PORT from
kubectl get service frontend
```

## Scale the Web Frontend

```sh
# scale up the number of frontend pods
kubectl scale deployment frontend --replicas=5

# verify the number of frontend Pods running
kubectl get pods


# scale down the number of frontend pods
kubectl scale deployment frontend --replicas=2
kubectl get pods
```

## Cleaning up

Deleting the Deployments and Services also deletes any running Pods. Labels can be used to delete multiple resources with one command.

```
kubectl delete deployment -l app=redis
kubectl delete service -l app=redis
kubectl delete deployment -l app=guestbook
kubectl delete service -l app=guestbook

kubectl get pods
```