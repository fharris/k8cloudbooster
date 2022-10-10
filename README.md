## k8cloudbooster
Simple Exercises to Learn Kubernetes Very Very Basics

#Start the exercices here:

https://fharris.github.io/k8cloudbooster/


4.1.	Exercise Namespaces

Edit file namespace-dev.json in order to configure your own namespace:

 

In this case, I’ve called my namespace “k8-booster”.


Now we are ready to create our namespace

kubectl create -f namespace-dev.json

 

We can double check the namespace was created in the cluster:

kubectl get namespaces 
	
 

Let’s set a context named k8-booster in our cluster:


First, you need to get some info about your cluster. Best place to that is in your kubeconfig file, which should be in the .kube hidden folder:

cat .kube/config | grep -in context

Take note of the user and cluster :

 

Also with:
kubectl config get-contexts

 

You’ll use that now to build the following command:

kubectl config set-context context-cqe7y7j3nwq --namespace=k8-booster-demo-01

 

And now confirm it changed:
kubectl config get-contexts

 


I can also create a new context for it instead of using the current:
kubectl config set-context k8-booster --namespace=k8-booster --cluster=cluster-c2genzygrtg --user=user-c2genzygrtg

Which will create a context called k8-booster associated to the namespace k8-booster.

 


And if I create a new context I will need to switch to it…. the local context of our kubeconfig to the context we just created:

kubectl config use-context k8-booster

 


Finally lets double check we’re working in the context:

kubectl config current-context

 


Let’s start exploring some other basic Kubernetes concepts:
![image](https://user-images.githubusercontent.com/17484224/194843406-41c338bc-1bab-4f5a-9c8c-191794b5b083.png)




# 4.1.	Exercise Namespaces

Edit file namespace-dev.json in order to configure your own namespace:

 

In this case, I’ve called my namespace “k8-booster”.


Now we are ready to create our namespace

kubectl create -f namespace-dev.json

 

We can double check the namespace was created in the cluster:

kubectl get namespaces 
	
 

Let’s set a context named k8-booster in our cluster:


First, you need to get some info about your cluster. Best place to that is in your kubeconfig file, which should be in the .kube hidden folder:

cat .kube/config

Take note of the user and cluster :

 

You’ll use that now to build the following command:

kubectl config set-context k8-booster --namespace=k8-booster --cluster=cluster-c2genzygrtg --user=user-c2genzygrtg

Which will create a context called k8-booster associated to the namespace k8-booster.

 


Now, let’s switch the local context of our kubeconfig to the context we just created:

kubectl config use-context k8-booster

 


Finally lets double check we’re working in the context:

kubectl config current-context

 


Let’s start exploring some other basic Kubernetes concepts:
![image](https://user-images.githubusercontent.com/17484224/194840558-9b8ca6b4-7a0f-4658-bb49-8d1e2f3ea33c.png)








