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
 - Launch Docker Desktop on Windows
 - minikube start
 - Create the mongodb deployment
 - Create the secret
 - Apply the secret
 - Reference values from the secret into the mongodb deployment
 - Apply the deployment
 - Create the mongodb service (internal, ClusterIP) in the same file as the deployment
 - Create the ConfigMap (contain the mongodb server address) and apply it
 - Create the mongo-express deployment and apply it
 - Create the mongo-express LoadBalancer service in the same file and reapply it
 - Run ```minikube service mongo-express-service```

 ## Second step - add a persistent volume

 **Branch persistent-volume**

 The goal is to learn how to add a persistent volume.

 ### Steps
  - Create a Persistent Volume (local for learning, in production I would use a StorageClass)
  - Turn "Headless" the mongodb service (needed for StatefulSets to allow stable network identities)
  - Replace the mongodb Deployment with a StatefulSet
  - ```kubectl apply -f config-map.yaml```
  - ```kubectl apply -f secret.yaml```
  - ```kubectl apply -f persistent-volume.yaml```
  - ```kubectl apply -f mongodb-stateful.yaml```
  - ```kubectl apply -f mongo-express.yaml```
  - ```kubectl get pods --watch```
  - ```minikube service mongo-express-service```

We don't need a PVC because the StatefulSet handle it with its volumeClaimTemplates.

### Test the persistency
 - Go on mongo-express to create a database
 - ```kubectl delete pod mongodb-statefulset-0``` : k8s delete the pod and recreate it automatically.
 - Once the pod is recreated and running, return to mongo-express and check that the created database is still present.

 ## Third step - scale the database

 **Branch pv-multi-pods**

 Currently the database has only one pod, for scalability we aim to find a way to replicate the database while keeping all replicas synchronized.

 Many databases and stateful systems provide their own Kubernetes Operators to handle this task, in our case, we will use [**MongoDB Community Operator**](https://github.com/mongodb/mongodb-kubernetes).
 
 ### Steps

 - Install [**Helm**](https://helm.sh/docs/intro/install/)
 - Install MongoDB Community Operator through Helm :
```
helm repo add mongodb https://mongodb.github.io/mongodb-kubernetes-operator
helm repo update
helm install community-operator mongodb/mongodb-kubernetes-operator
```
This will install the CRDs and deploy the operator in the cluster.

 - ```kubectl get crds | grep mongodbcommunity``` : check the CRDs
 - 