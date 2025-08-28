# Kubernetes-learning
Learning Kubernetes through experimentation.

## Setup

I'm working on Windows, I have multiple minor issues when I try several things with minikube and docker, so I will work into the WSL2 environment.

Docker Desktop for Windows allows to use it from within WSL2.
I clone my repository within the WSL2 environment to dodge any further Windows Linux conflicts.

```
    cd ~/repositories/
    git clone <github project>
```

### Install minikube and kubectl within WSL2

Follow [these steps](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management) to install the required tools.


## First step - simple mongodb environment application without persistent volume

**Branch simple-mongodb-deployment**

The goal here is to learn to deploy the following :

- Mongodb
- Mongo-express (web-application)

We won't use Helm charts.

### Architecture

We don't want the mongodb to be publicly reachable, so we will expose it only within the k8s cluster.

We will access the mongodb only through mongo-express, so it will be publicly reachable.

We will not set a Persistent Volume (PV).

### Steps
 - Launch Docker Desktop on Windows.
 - minikube start
 - Create the mongodb deployment
 - Create the secret
 - Apply the secret
 - Reference values from the secret into the mongodb deployment
 - Apply the deployment
 - Create the mongodb service (internal, ClusterIP) in the same file as the deployment
 - Create the mongo-express deployment and apply it
 - Create the mongo-express LoadBalancer service in the same file and reapply it
 - Run ```minikube service mongo-express-service```