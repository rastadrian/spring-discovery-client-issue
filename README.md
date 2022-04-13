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

## How to Reproduce the Issue

I am using [Lens](https://k8slens.dev/) to manage my `minikube` cluster. After deploying the applications, I create a 
Port Forwarding for the `client-service` application using port `7070`.

Then execute this command:
```
curl -X GET http://localhost:7070/services
```
This will output an error:
```
curl: (52) Empty reply from server
```
The `client-service` instance logs will show a repeating issue between the `RoundRobinLoadBalancer` and the 
`reactorLoadBalancerExchangeFilterFunction` and an exception regarding an unsupported Media Type exception:

```
2022-04-13 20:40:03.165 ERROR 1 --- [     parallel-1] scoveryClientServiceInstanceListSupplier : Exception occurred while retrieving instances for service spring-cloud-kubernetes-discoveryserver

org.springframework.web.reactive.function.UnsupportedMediaTypeException: Content type 'application/octet-stream' not supported for bodyType=org.springframework.cloud.kubernetes.discovery.KubernetesServiceInstance
	at org.springframework.web.reactive.function.BodyExtractors.lambda$readWithMessageReaders$12(BodyExtractors.java:201) ~[spring-webflux-5.3.18.jar:5.3.18]
	Suppressed: reactor.core.publisher.FluxOnAssembly$OnAssemblyException: 
Error has been observed at the following site(s):
	*__checkpoint ? Body from UNKNOWN  [DefaultClientResponse]
```