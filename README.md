## Kubernetes cloud booster for begginers with Oracle Container Engine and Oracle Cloud Infrastructure

- 1. # Introduction

The aim of this document is to walk you through the basics of Kubernetes in Oracle Cloud with Oracle Container Engine, a self-managed Kubernetes as a service.  

## See the following resources:

- [Lab Instructions](./README.md)
- [k8s manifest for namespace](./pics/structure.png)
- [k8s manifest for deployment](./manifestDeployment.yml)
- [k8s manifest for service](./installation.md)

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

- 3. # Create a Deployment
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

- 5. # Check Logs

- 6. # Scalling the App

- 7. # Housekeeping

Before we close this tutorial let’s make some housekeeping and delete all the resources we created. This is specially important because resources such as the Load Balancer might cost you money depending on the cloud provider where you are running it:

```
kubectl -n k8-booster delete deployment,pod,svc --all    
```
or 
```
kubectl delete namespace k8-booster-demo-01 --force --grace-period=0 
```
