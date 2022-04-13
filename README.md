# Client Issue

A bare-bone Spring project with `spring-cloud-starter-kubernetes-discoveryclient` is able to leverage the `discovery-server` 
appropriately, but as soon as `spring-cloud-starter-loadbalancer` is introduced, the `client-service` is unable to 
communicate with the `discovery-server` anymore.

## Pre-requisites

* Java 17
* docker
* minikube

## How to Build Image

1. Sanity Checks
```
minikube -p minikube docker-env | source
set -x JAVA_HOME (/usr/libexec/java_home -v 17)
```

2. Build

```
./gradlew clean bootBuildImage --imageName=rastadrian/client-issue
```

## How to Deploy to Kubernetes

```
kubectl create -f k8s-namespace.yaml
kubectl config set-context --current --namespace=poc-service-discovery
kubectl create -f k8s-deployment.yaml
```