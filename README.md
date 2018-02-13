# Welcome to Ruby & Kubernetes Sample

This repository will show you how to use a deploy a containerized sample ruby project using Docker Compose ,Kubernetes & Helm.This repository is configured for automated build to publish the docker image to DockerHub at [https://hub.docker.com/r/codehut/ruby-docker-kubernetes-sample/](https://hub.docker.com/r/codehut/ruby-docker-kubernetes-sample/)

# Prerequisites
You should have the following installed to your local machine :  
1 - [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) : a command line tool used to execute commands and talk to kubernetes.  
2 - [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)  will be used to test kubernetes deployments on your local machine.  
3 - [Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) will be used to deploy to Kubernetes  

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

At this point we are ready to deploy to Kubernetes using Helm charts,to deploy the chart , simply use the following commands and let the magic begin ! 

```
cd deploy/helm/ruby-chart
helm upgrade ruby --namespace ns-ruby --install .
``` 
> the later command tells helm to upgrade the release named **ruby** in kubernetes namespace **ns-ruby** and install it if not exists ,you can also use **install** and **upgrade** commands separately.

And the output should be something like this indicating required resources have been scheduled
```
Release "ruby" does not exist. Installing it now.
NAME:   ruby
LAST DEPLOYED: Mon Feb 12 22:10:29 2018
NAMESPACE: ns-ruby
STATUS: DEPLOYED

RESOURCES:
==> v1/Secret
NAME             TYPE    DATA  AGE
ruby-postgresql  Opaque  1     1s
ruby-redis       Opaque  1     1s

==> v1/ConfigMap
NAME       DATA  AGE
cmap-ruby  6     1s

==> v1/PersistentVolumeClaim
NAME             STATUS   VOLUME                                    CAPACITY  ACCESSMODES  STORAGECLASS  AGE
ruby-postgresql  Bound    pvc-c6402a14-1030-11e8-82c5-080027ebd94a  8Gi       RWO          standard      1s
ruby-redis       Bound    pvc-c640a28a-1030-11e8-82c5-080027ebd94a  8Gi       RWO          standard      35s


==> v1/Service
NAME             CLUSTER-IP      EXTERNAL-IP  PORT(S)       AGE
ruby-postgresql  10.106.204.210  <none>       5432/TCP      1s
ruby-redis       10.98.204.37    <none>       6379/TCP      1s
svc-ruby         10.109.112.128  <nodes>      80:30001/TCP  1s

==> v1beta1/Deployment
NAME             DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
ruby-postgresql  1        1        1           0          1s
ruby-redis       1        1        1           0          1s
dep-ruby         1        1        1           0          1s

``` 
Now you can use **kubectl** to check if the desired resources have been scheduled by doing the following :

```
kubectl get pods --namespace ns-ruby

NAME                              READY     STATUS    RESTARTS   AGE
dep-ruby-86bb796bc4-hjl9b         1/1       Running   0          7m
ruby-postgresql-68dcd8554-k7jv7   1/1       Running   0          7m
ruby-redis-68cd79c4f8-mlmvl       1/1       Running   0          7m

``` 
> Note: Pods may take some time to be in **Running** state to fetch the required images. 
```
kubectl get services --namespace ns-ruby

NAME              CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
ruby-postgresql   10.106.204.210   <none>        5432/TCP       14m
ruby-redis        10.98.204.37     <none>        6379/TCP       14m
svc-ruby          10.109.112.128   <nodes>       80:30001/TCP   14m
```
```
kubectl get configmaps --namespace ns-ruby
NAME        DATA      AGE
cmap-ruby   6         15m
```


## Access The Application

Wohoo ! Now we should be able to access our application ,  first of all we should know the IP address of the currently running MiniKube VM ,run this command and the address should be printed out.

```
minikube ip
x.x.x.x
```

Now try this IP address in your browser with port 30001
http://x.x.x.x:30001

> Note: For the sake of this demo , the service is configured to be a NodePort service and listen to port 30001 , in production if you are using a cloud provider (AWS or Google Cloud ) you will need to change it to LoadBalancer type or start using Ingresses . 

And we're done ! this how the page should look like :

![enter image description here](https://lh3.googleusercontent.com/_TbuUBv0eda-His6fumHUbSzpyurZ2ExJwJyp6xZsTyhFS_v8835S6E3vKCsioFmI6Xh7jQ-2cyW)

## Using Helm Values

You may need to change the chart configuration ,for instance , a production environment configuration will use the same chart but with different number of replicas,you can use the same helm chart to deploy multiple releases with different values for different environments by passing a different **values.yaml** while installing or upgrading a chart. for the sake of this demo , we will change the configuration of the currently existing chart by changing **svc-ruby** service nodePort to 30002 instead of 30001.just open **deploy/helm/ruby-chart/values.yaml** file and change apply this change

> Note: for the sake of this demo , helm values file has been added to the code repository ,otherwise for production usage, values file should reside in Jenkins or fetched by Jenkins during the pipeline.

```
    service:
        name: svc-ruby
        port: 80
        targetPort: 8000
        nodePort: 30002
        # nodePort changed to 30002 instead of 30001
        type: NodePort
```
Then run the helm upgrade command again  

```
cd deploy/helm/ruby-chart
helm upgrade ruby --namespace ns-ruby --install .
``` 
Now if you opened minikube IP address in your browser but with port 30002 you should see the application running. Wohoo !  

