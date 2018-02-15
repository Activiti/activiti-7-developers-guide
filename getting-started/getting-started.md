## Getting Started

Welcome to our Getting Started Guide. This guide is a step by step tutorial on how to get Activiti Cloud up and running.

The first decision that you will need to make is to choose between running all services with:

* [Docker Compose](getting-started/docker.md)
* [Kubernetes (Minikube)](getting-started/minikube.md)

The main difference between these two options is the degree of similarities that you will have a with a real production environment in your local setup. I would recommend [Minikube](https://github.com/kubernetes/minikube) if you are planning to run in [Kubernetes](https://kubernetes.io/) hosted in a cloud provider. Minikube runs inside a VM, causing the application to be running inside a different instance of an Operating System. But if you're new to microservices Docker is a good place to start.

The Docker Compose approach is probably faster, as it doesn’t require a VM, but you will need to be careful with configurations. Remember that if you are planning to run your applications in a clustered environment, each of the services might run in a different node.

No matter which option do you choose, from a high level perspective these are the steps that you will need to perform the following steps:

1. Start Infrastructure
2. New Activiti Cloud Application
   1. Configure it
   2. Build it
   3. Deploy it
   4. Repeat 2 for a new Activiti Cloud Application


Once you have everything up and running, you can [Test](getting-started/testing.md).
