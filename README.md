## Kubernetes cloud booster for begginers with Oracle Container Engine and Oracle Cloud Infrastructure

- 1. # Introduction

The aim of this document is to walk you through the basics of Kubernetes in Oracle Cloud with Oracle Container Engine, a self-managed Kubernetes as a service.  

## See the following resources:

- [Lab Instructions](./README.md)
- [k8s manifest for namespace](./pics/structure.png)
- [k8s manifest for deployment](./manifestDeployment.yml)
- [k8s manifest for service](./manifestDeployment.yml)

- [k8s architecture diagram](./pics/k8sarchi.png)


- 2. # Create a Namespace

We can simply create imperatively a namespace with something like:
```
kubectl create ns k8-booster
```
But lets try to do it declaratively:

Edit file [namespace-dev.json](./namespace-dev.json)  to configure your own namespace:
```
{"kind": "Namespace",
 "apiVersion": "v1",
 "metadata": {
        "name": "k8-booster",
        "labels": {
        "name": "k8-booster"
}
}
}
```
In this case, I’ve called my namespace “k8-booster”.


After editing the file we are ready to create our namespace:
```
kubectl create -f namespace-dev.json
```
We can double check the namespace was created in the cluster:
```
kubectl get namespaces 
```
If we now run, we get a list of the existing contexts:
```
kubectl config get-contexts
```

...and which context and namespace are we using...
We can now instruct our context to use the namespace we just created for this exercise:
```
kubectl config set-context context-cqe7y7j3nwq --namespace=k8-booster-demo-01

```

Optional:
Another option is to create a new context for it instead of using the current context:
```
kubectl config set-context k8-booster --namespace=k8-booster --cluster=cluster-c2genzygrtg --user=user-c2genzygrtg
```
And if we create a new context we will need to switch to it…. the local context of our kubeconfig to the context we just created:
```
kubectl config use-context k8-booster
```
Finally lets double check we’re working in the context:
```
kubectl config current-context
```

- 3. # Create a Deployment
Open file [manifestDeployment.yml](./manifestDeployment.yml) and take a look:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloudnativebasicspring-deployment
spec:
  selector:
    matchLabels:
      app: cloudnativebasicspring
  replicas: 3
  template:
    metadata:
      labels:
        app: cloudnativebasicspring
    spec:
      containers:
      - name: cloudnativebasicspring
        image: fharris/cloudnativebasic-spring-docker:latest
        ports:
        - containerPort: 8080
```
Here we are going to create a Deployment of an app called cloudnativebasicspring.
The deployment consists in 3 replicas (i.e. 3 pods) of the app to be deployed.

This app has a docker image ready to pull and use in fharris/cloudnativebasic-spring-docker:latest . By default Kubernetes is going for the public docker hub. In this case, if you wanted to point to a particular docker register, OCIR (Oracle Cloud Registry, for example) you would need to write something like:
```
image: <region-key>.ocir.io/<tenancy-namespace>/<repo-name>/<image-name>:<tag>
```
This container is going to expose its internal port 8080. We are using a public docker hub repository. We could use instead Oracle OCIR, but we would need to configure some security steps in order to use it. For a beginners tutorial, docker hub is fine.

Let’s check if there are any deployments already made:

Run:
```
kubectl get pods
```
No deployments made yet. This is a brand-new namespace. There are probably deployments in this cluster, but they are in other namespaces.

So, lets execute kubectl, this time against this deployment configuration file:
```
kubectl apply -f manifestDeployment.yml
```
If I now run: 
```
kubectl get deployments
```

I can see that a deployment was made and that 3 of the 3 replicas are ready and available.

When I run:
```
kubectl get pods -o wide
``` 
I can actually see that there are 3 pods running across 2 physical nodes. I can also see the internal IP of the container.  The name of the pod is actually the hostname.

The deployment configuration guarantees through a replica set that there are always 3 pods running at any giving time. This is one of the strongest features of Kubernetes: resiliency. Ultimately, this characteristic contributes also to the High Availability of our app. If we run:
```
kubectl get rs
```
We can actually see the existing replicas, how many should exist and how many are ready.

If we want more detail regarding the deployment configuration, lets run:
```
kubectl describe deployments
```

All you can get a lot of info regarding the deployment, the replica sets, the container image. Even an event log. With this we have our application running in Kubernetes. Let’s now see how to access our application. 

- 4. # Create a Service

If you remember. We can’t actually call a pod directly. Not only the Kubernetes definition and best practices try to avoid that, as we also implemented an extra layer of security and privacy when we decided to provision this cluster in OCI with private node workers. They don’t even have a public IP. 

In order to manage external access, we need a construct known as Service. There are different types of services. 

Let’s take a look on our service declaration opening file manifestService.yml:
```
apiVersion: v1
kind: Service
metadata:
  name: cloudnativebasicspring-service
spec:
  type: LoadBalancer
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: cloudnativebasicspring
```

As you can see, its type is LoadBalancer. It could be a a NodePort or a ClusterIP, etc.
The fact that I’m using a LoadBalancer type means that it will be attached to the Load Balancer specific definition of my Cloud provider. In this case Oracle Cloud.
I will expose the port 8080 of my loadbalancer and map it to the port 8080 of my target container which is in our case the app cloudnativebasicspring. 

So, when we run:
```
kubectl apply -f manifestService.yml 
```
And immediately after run:
```
kubectl get services
```
We can see that the Service is already created, but not ready as the External IP is still in pending state.
We the external IP is assigned, we can finally get access to our app through our service:

If we open a browser with the http://EXTERNAL-IP:8080

We should be able to see our app running.

- 5. # Check Logs

Another cool thing we can do with kubectl is to check the logs from the running pod.

So, if we run:

kubectl logs podname 

which in our case would be something such as: 
```
kubectl logs cloudnativebasicspring-deployment-78cb5675c6-zmc5w
```
We can observe our spring app logs.


- 6. # Scalling the App

When we talked about Deployments, we mentioned that a Deployment knows how to create a Replica Set which will guarantee that there is always a minimum number of replicas of that pod running in the cluster. In our case, we’ve defined 3 replicas.

Let’s imagine that one of our pods has an issue and gets deleted or unstable. To simulate that we are going to delete a pod:

Before let’s list all the pods again:
```
kubectl get pods -o wide
```
And we can actually see that we have 3 pods distributed across different nodes. If you notice, the IPs are different!
Let’s now delete one of the pods and see what happens:
```
kubectl delete pod cloudnativebasicspring-deployment-78cb5675c6-zmc5w
```
We can see that immediately a new pod is provisioned:

The number of running replicas is guaranteed by Kubernetes.

We can also explicitly scale up and scale down to increase or decrease the number of running replicas.
In our Deployment manifest we can change the number from 3 to 10 for example and apply the change declaratively.

But lets do it imperatively:
```
kubectl scale deploy cloudnativebasicspring-deployment --replicas=5
```

- 7. # Housekeeping

Before we close this tutorial let’s make some housekeeping and delete all the resources we created. This is specially important because resources such as the Load Balancer might cost you money depending on the cloud provider where you are running it:

```
kubectl -n k8-booster delete deployment,pod,svc --all    
```
or 
```
kubectl delete namespace k8-booster-demo-01 --force --grace-period=0 
```
