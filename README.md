# kubernetes-tutorials

Reproducing some Kubernetes tutorials found in the [official documentation](https://kubernetes.io/docs/tutorials/).

## Getting Started

### Prerequisites

For all the examples, you need to have:
* A Kubernetes cluster; you can create one by using [Minikube](https://kubernetes.io/docs/setup/minikube).
* The [**kubectl**](https://kubernetes.io/docs/tasks/tools/install-kubectl/) command line tool, configured to communicate with your Kubernetes cluster.

Or you can use a Kubernetes playground, e.g.:
* [Katacoda](https://www.katacoda.com/courses/kubernetes/playground)
* [Play with Kubernetes](http://labs.play-with-k8s.com/)

Check the version with `kubectl version`. The examples in this project were tested with `kubectl` 1.15.

### Suggested order

Most task will be executed in the command line. Follow the instructions included in these files:
1. Configuration
    * [Configure Containers using a ConfigMap](configuration/config-containers.md)
    * [Configure Redis using a ConfigMap](configuration/config-redis.md)
2. Stateless Applications
* [Exposing an External IP Address to Access an Application in a Cluster](stateless/expose-address.md)
3. Stateful Applications
4. CI/CD Pipeline
5. Clusters
6. Services

### Not included

The *hello-minikube* and *Kubernetes basics* tutorials found in the documentation are not included in here, they are located in different repositories.

## Authors

* [**Adrian Gomez**](https://github.com/aegomez)

## License

[MIT](LICENSE)