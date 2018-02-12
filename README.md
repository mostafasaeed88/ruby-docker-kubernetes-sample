﻿# Welcome to Ruby , Docker , Docker Compose & Kubernetes Sample

This repository will show you how to use a deploy a containerized sample ruby project using Docker Compose ,Kubernetes & Helm.

# Prerequisites
You should have the following installed to your local machine :
1 - [Docker](https://docs.docker.com/install/)
2 - [Docker Compose](https://docs.docker.com/compose/install/#prerequisites) will act as your development environment for testing the application.
3 - [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) : a command line tool used to execute commands and talk to kubernetes.
3 - [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)  will be used to test kubernetes deployments on your local machine.
4 - [Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) will be used to deploy to Kubernetes

## Using Docker Compose



## Using MiniKube

First you should use Minikube to start a local Kubernetes cluster. you can do so by running the following command

```
minikube start
```
If everything is working properly,The following output should be printed to your terminal
```
Starting local Kubernetes v1.x.x cluster...
Starting VM...
Getting VM IP address...
Moving files into cluster...
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster. 
```

## Using Kubectl

At this step , Minikube should have configured kubectl for you ,to make sure that cluster is up and running properly , use the following :

```
kubectl get nodes
``` 

The following output should appear indicating one node is ready to schedule pods.

```
NAME       STATUS    AGE       VERSION
minikube   Ready     x        v1.x.x
``` 

## Using Helm

At this point we are ready to deploy to Kubernetes using Helm charts,to deploy the chart , simply use the following commands : 

```
cd deploy/helm/ruby-chart
helm upgrade ruby --namespace ns-ruby --install .
``` 