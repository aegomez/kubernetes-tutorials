# Configure a Pod to Use a ConfigMap

Examples found in the [Kubernetes Documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/).

## Create a ConfigMap

ConfigMaps allow to decouple configuration from image content to keep containerized applications portable.

You can use either `kubectl create configmap` or a ConfigMap generator in `kustomization.yaml`.

### - Create a ConfigMap Using kubectl create configmap

```sh
kubectl create configmap <map-name> <data-source>
# data-source is the directory, file or literal value to draw the data from
```

Use `kubectl describe` or `kubectl get` to retrieve information about a ConfigMap.

#### Create ConfigMaps from directories

```sh
# Create a local directory
mkdir -p container/configmap/

# Download the sample files into the directory
curl https://kubernetes.io/examples/configmap/game.properties -o container/configmap/game.properties
curl https://kubernetes.io/examples/configmap/ui.properties -o container/configmap/ui.properties

# Create the ConfigMap
kubectl create configmap game-config --from-file=container/configmap/
```

* The ConfigMap combines the contents of the files:

```sh
# description
kubectl describe configmaps game-config

# YAML
kubectl get configmap game-config -o yaml
```

#### Create ConfigMaps from files

```sh
# You can pass --from-file one or more times
kubectl create configmap game-config-2 \
  --from-file=container/configmap/game.properties \
  --from-file=container/configmap/ui.properties

# The created ConfigMap
kubectl describe configmap game-config-2
```

* Using the option `--from-env-file`:

```sh
# Env-files contain a list of environment variables.
# Syntax rules:
# - Each line has to be in VAR=VAL format.
# - Comments (#) are ignored.
# - Blank lines are ignored.
# - There is no special handling of quotation marks.

# Download sample file
curl https://kubernetes.io/examples/configmap/game-env-file.properties -o container/configmap/game-env-file.properties

# Contents of the env-file
cat container/configmap/game-env-file.properties

# Create a new ConfigMap
kubectl create configmap game-config-env-file \
  --from-env-file=container/configmap/game-env-file.properties

# Result
kubectl get configmap game-config-env-file -o yaml

# When passing multiple env-files as sources, only the last one is used.
```

#### Define the key to use when creating from a file

```sh
# Define a key other that the file name to use in the data section.
kubectl create configmap game-config-3 \
  --from-file=game-special-key=container/configmap/game.properties

# Result
kubectl get configmaps game-config-3 -o yaml
```

#### Create ConfigMaps from literal values

```sh
# Using the --from-literal argument
# Multiple key-value pairs can be passed in
kubectl create configmap special-config \
  --from-literal=special.how=very \
  --from-literal=special.type=charm

# Result
kubectl get configmaps special-config -o yaml
```

---

### - Create a ConfigMap from generator

`kubectl` supports `kustomization.yaml` since 1.14.

#### Generate ConfigMaps from files

```yaml
# inside ./container/kustomization.yaml
configMapGenerator:
- name: game-config-4
  files:
  - configmap/game.properties
```

```sh
# apply the kustomization directory
kubectl apply -k ./container

# list of configmaps
kubectl get configmaps

# suffix is appended by hashing the contents
kubectl describe configmaps/game-config-4-m9dm2f92bt
```

#### Define the key to use when generating from a file

```yaml
# container/kustomization.yaml
configMapGenerator:
- name: game-config-5
  files:
  - some-other-key=configmap/ui.properties
```

```sh
kubectl apply -k ./container/
```

#### Generate ConfigMaps from Literals

```yaml
# container/kustomization.yaml
configMapGenerator:
- name: special-config-2
  literals:
  - special.how=very
  - special.type=charm
```

```sh
kubectl apply -k ./container/
```

---

## Define container environment variables using ConfigMap data

### - Define with data from a single ConfigMap

1. Define an environment variable as a key-value pair in a ConfigMap:
```sh
kubectl create configmap env-config \
  --from-literal=foo.env=extra
```
2. Assign the `foo.env` value defined in the ConfigMap to the `SPECIAL_LEVEL_KEY` environment variable in the Pod specification:
```sh
# create container/pod-single-configmap-env-variable.yaml

# create the Pod
kubectl create -f container/pod-single-configmap-env-variable.yaml
```

### - Define with data from multiple ConfigMaps

```sh
# Create the ConfigMaps first:
# container/configmap/multimaps.yaml
kubectl create -f ./container/configmap/multimaps.yaml

# Define the environment variables in the Pod specification
# container/pod-multiple-configmap-env-variable.yaml

# Create the Pod
kubectl create -f ./container/pod-multiple-configmap-env-variable.yaml
```

## Configure all key-value pairs in a ConfigMap as container environment variables

```sh
# Create a ConfigMap containing multiple key-value pairs
kubectl create -f container/configmap/multikeys.yaml

# Use envFrom to define all of the ConfigMap’s
# data as container environment variables

# Create the Pod:
kubectl create -f container/pod-configmap-envFrom.yaml
```

## Use ConfigMap-defined environment variables in Pod commands

* Use the variables in the `command` section of the Pod specification using the `$(VAR_NAME)` syntax. Example:

```sh
kubectl create -f container/pod-configmap-env-var-valueFrom.yaml
```
