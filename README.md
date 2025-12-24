# Kubectl command 
I make this file because this file easily accessable for everyone. 
# First create node
<p>First go kind-cluster folder and write this command to create node</p>

```
kind create cluster --name <choose a name> --config=config.yml
```
# To create namespace
  <p>Go to this section where namespace.yml file present</p>
  
```
kubectl apply -f namespace.yml
```
# To create pod inside the namespace 
<p>Go to this section where namespace.yml file present</p>

```
kubectl apply -f pod.yml
```
# To intaract with pod 
```
kubectl exec -it pod/<pod-name> -n <namespace> -- bash
```
# To debug this pod and show everything
```
kubectl describe pod/nginx-pod -n nginx
```

