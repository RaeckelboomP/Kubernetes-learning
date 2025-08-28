# Kubernetes-learning
Learning Kubernetes through experimentation.

## First step - simple mongodb environment application without persistent volume

**Branch simple-mongodb-deployment**

The goal here is to learn to deploy the following :

- Mongodb
- Mongo-express (web-application)

We won't use Helm charts.

### Architecture

We don't want the mongodb to be publicly reachable, so we will expose it only within the k8s cluster.
We will access the mongodb only through mongo-express, so it will be publicly reachable.

### Steps
 - Launch Docker Desktop
 - minikube start
 - Create the mongodb deployment
 - Create the mongodb service