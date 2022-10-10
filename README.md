## Kubernetes cloud booster for begginers with Oracle Container Engine and Oracle Cloud Infrastructure

- 1. # Introduction

The aim of this document is to walk you through the basics of Kubernetes in Oracle Cloud with Oracle Container Engine, a self-managed Kubernetes as a service.  

## See the following resources:

- [Lab Instructions](./README.md)
- [k8s manifest for namespace](./pics/structure.png)
- [k8s manifest for deployment](./manifestDeployment.yml)
- [k8s manifest for service](./installation.md)

- [k8s architecture diagram](./pics/k8sarchi.png)




- 2. # Create a Cluster



- 3. # Access Cluster




- 4. # Create a Namespace
- 5. # Create a Deployment
- 6. # Create a Service

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


