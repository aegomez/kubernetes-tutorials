# Example: Deploying WordPress and MySQL with Persistent Volumes

How to deploy a WordPress site and a MySQL database using Minikube. Both applications use PersistentVolumes to store data. Based on the [official documentation example](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/).

## Objectives

* Create PersistentVolumeClaims and PersistentVolumes.

* Create a `kustomization.yaml` with resource configs for MySQL and WordPress.

* Apply the kustomization directory.

* Clean up.

## Before beginning

A Kubernetes cluster and `kubectl` are required. Create a cluster with a cloud provider (Google Kubernetes Engine, Amazon Web Services, ...), using Minikube, or use a Kubernetes playground (Katacoda, Play with Kubernetes).

The following examples were tested using Minikube v1.3.0 and kubectl 1.15.2.

## Create PersistentVolumeClaims and PersistentVolumes

Many cluster environments have a default StorageClass installed. When a StorageClass is not specified in the PersistentVolumeClaim, the cluster’s default StorageClass is used instead.

When a PersistentVolumeClaim is created, a PersistentVolume is dynamically provisioned based on the StorageClass configuration.

In local clusters, the default StorageClass uses the `hostPath` provisioner. `hostPath` volumes are only suitable for development and testing. With `hostPath` volumes, the data lives in `/tmp` on the node the Pod is scheduled onto and does not move between nodes. If a Pod dies and gets scheduled to another node in the cluster, or the node is rebooted, the data is lost.

## Create a `kustomization.yaml`

### Add a Secret generator

A **Secret** is an object that stores a piece of sensitive data like a password or key. You can create a Secret by generators in a [`kustomization` file](mysql-wordpress/kustomization.yaml).

## Add resource configs for MySQL and WordPress

The [MySQL manifest](mysql-wordpress/mysql-deployment.yaml) describes a single-instance Deployment in which: 
* The MySQL container mounts the PersistentVolume at `/var/lib/mysql`.
* The `MYSQL_ROOT_PASSWORD` environment variable sets the database password from the Secret.

The [WordPress manifest](mysql-wordpress/wordpress-deployment.yaml) describes a single-instance Deployment: 
* The WordPress container mounts the PersistentVolume at `/var/www/html` for website data files.
* The `WORDPRESS_DB_HOST` environment variable sets the name of the MySQL Service defined above, and WordPress will access the database by Service. The `WORDPRESS_DB_PASSWORD` environment variable sets the database password from the Secret kustomize generated.

These resources are also added in the [`kustomization.yaml` file](mysql-wordpress/kustomization.yaml).

## Apply and Verify

```sh
kubectl apply -k <directory>

# Verify that the Secret exists
kubectl get secrets

# Verify that a PersistentVolume got dynamically provisioned
kubectl get pvc

# Verify that the Pod is running 
kubectl get pods

# Verify that the Service is running
kubectl get services wordpressI
```

Note: In Minikube the EXTERNAL-IP is always <pending>. To get the IP Address, run:
```
minikube service wordpress --url
```

Copy the IP Address and load the page in a browser to see the site.

## Cleaning up

Run the following command to delete your Secret, Deployments, Services and PersistentVolumeClaims:
```
kubectl delete -k <directory>
```