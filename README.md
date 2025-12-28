<h1>Kubectl command</h1> 
<p>I make this file because this file easily accessable for everyone.</p>

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
# To see namespace
<p>Use this command</p>

```
kubectl get ns
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
kubectl describe pod/<pod-name> -n <namespace>
```
# To see each pods

```
kubectl get pods -n <namespace>
```
# Deployment 
<p>In deployment you first delete pod.yml file</p>

```
kubectl delete -f pod.yml
```
<p>Then you deploy</p>

```
kubectl apply -f deployment.yml
```
<p>To see deployment pods</p>

```
kubectl get deployment -n <namespace>
```
<p>To scale this deployment</p>

```
kubectl scale deployment.app/nginx-deployment -n nginx --replicas=5
```
<p>To see more information about pods</p>

```
kubectl get pods -n nginx -o wide
```
<p>To update image in deployment</p>

```
kubectl set image deployment/nginx-deployment -n nginx <container_name>=<image_name>:<version>
```
<p>To delete deployment sets</p>

```
kubectl delete -f deployment.yml
```
# When you add many service
## To add others survice in there
<p>Add all service in same namespace.</p>
<p>Now run this command where your kind cluster file present</p>

```bash
kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
```
<p>You see output like this</p>

**Example**
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
aditya@Aditya:~/devops/kubernetes/kind-cluster$ 
```
<p>Now you run this command</p>

```
kubectl get ns
```
<p>You see a namespace build in there. Name is <b>ingress-nginx</b></p>

**Example output**
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl get ns
NAME                 STATUS   AGE
default              Active   5d
ingress-nginx        Active   4m46s
kube-node-lease      Active   5d
kube-public          Active   5d
kube-system          Active   5d
local-path-storage   Active   5d
nginx                Active   3d14h
aditya@Aditya:~/devops/kubernetes/kind-cluster$ 
```
<p>Now you run this command to see pods in this new namespace</p>

```bash
kubectl get pods -n ingress-nginx
```
**Example Output**
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-gn86m        0/1     Completed   0          8m23s
ingress-nginx-admission-patch-fcnmt         0/1     Completed   2          8m23s
ingress-nginx-controller-66fdf84d85-6jpdl   1/1     Running     0          8m23s
aditya@Aditya:~/devops/kubernetes/kind-cluster$ 
```
<p><b>STATUS Completed</b> mean is those are jobs and its completed</p>
<p><b>STATUS Running</b> mean is those are pods and its running</p>

<p>Now you run this to see services in this new namespace</p>

```bash
kubectl get service -n ingress-nginx
```
**Example Output**
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl get service -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.96.179.123   <pending>     80:32686/TCP,443:30343/TCP   14m
ingress-nginx-controller-admission   ClusterIP      10.96.220.81    <none>        443/TCP                      14m
aditya@Aditya:~/devops/kubernetes/kind-cluster$
```
<p>You see <b>TYPE LoadBalancer</b>. This service you expose letter</p>
<p>Now go your code folder and make a file name ingress.yml</p>
<p>And put this code in this file</p>

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: <choose_your_name>
  # name: nginx-notes-ingress
  namespace: <enter_common_namespace_for_both_service>
  # namespace: nginx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: / #optional. when you use both invisible port app like port 80. 
spec:
  rules: 
  - http: 
      paths: 
      - pathType: Prefix 
        path: <enter_your_path>
        # path: /nginx
        backend: 
          service:
            name: nginx-service
            port:
              number: 80
      - pathType: Prefix
        path: <enter_your_path>
        # path: /
        backend: 
          service:
            name: notes-app-service
            port:
              number: 8000
```
<p>Now apply</p>

```bash
kubectl apply -f ingress.yml
```
<p>Now see you ingress</p>

```bash
kubectl get ingress -n nginx
```
**Example output**
```
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ kubectl get ingress -n nginx
NAME                  CLASS    HOSTS   ADDRESS     PORTS   AGE
nginx-notes-ingress   <none>   *       localhost   80      71s
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ 
```
<p>Now see everything</p>

```bash
kubectl get all -n nginx
```
**Example Output**
```
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ kubectl get all -n nginx
NAME                                      READY   STATUS    RESTARTS      AGE
pod/nginx-deployment-6f59995b97-7k8lq     1/1     Running   2 (81m ago)   15h
pod/nginx-deployment-6f59995b97-thqxp     1/1     Running   2 (81m ago)   15h
pod/notes-app-deployment-978b5946-t2knn   1/1     Running   0             48m

NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/nginx-service       ClusterIP   10.96.68.204    <none>        80/TCP     13h
service/notes-app-service   ClusterIP   10.96.184.149   <none>        8000/TCP   48m

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment       2/2     2            2           15h
deployment.apps/notes-app-deployment   1/1     1            1           48m

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-6f59995b97     2         2         2       15h
replicaset.apps/notes-app-deployment-978b5946   1         1         1       48m
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ 
```
<p>Now use this command to expose this service</p>

```bash
kubectl get service -n ingress-nginx
```
**Example Output**
```
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ kubectl get service -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.96.179.123   <pending>     80:32686/TCP,443:30343/TCP   44m
ingress-nginx-controller-admission   ClusterIP      10.96.220.81    <none>        443/TCP                      44m
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ 
```
<p>You see in <b>NAME ingress-nginx-controller</b> we need to expose this service</p>
<p>To expose this service</p>

```bash
sudo -E kubectl port-forward service/ingress-nginx-controller -n ingress-nginx 80:80
```
<p>If you see port already in use error then visit my another github repo</p>

```github
[Port clean](https://github.com/Aditya-das-4707-e/Ghost-Port-Cleanup-Tool)
```
<p><b>Now you see your app is running</b></p>

<h2><b>Go to next step</b></h2>

# ReplicaSets (optional use only / education purpose)
<p>To apply replicasets</p>

```
kubectl apply -f replicasets.yml
```
<p>To see replicaset pod</p>

```
kubectl get replicasets -n <namespace>
```
<p>To delete replicasets</p>

```
kubectl delete -f replicasets.yml
```
# DaemonSets 
<p>To apply daemonsets</p>

```
kubectl apply -f daemonsets.yml
```
<p>to see demonsets pods</p>

```
kubectl get pods -n <namespace>
```
<p>To delete deamonsets</p>

```
kubectl delete -f daemonsets.yml
```
# Job
<p>To apply job</p>

```
kubectl apply -f job.yml
```
<p>To see job</p>

```
kubectl get job -n <namespace>
```
<p>To see pod</p>

```
kubectl get pods -n <namespace>
```
<p>To see the logs</p>

```
kubectl logs pod/<job_name> -n <namespace>
```
<p>To delete this job</p>

```
kubectl delete -f job.yml
```
# Cron-job
<p>To apply cron job</p>

```
kubectl apply -f cron-job.yml
```
<p>To see cron job</p>

```
kubectl get cronjob -n <namespace>
```

<p>To see pods</p>

```
kubectl get pods -n <namespace>
```
<p>To delete this pods</p>

```
kubectl delete -f cron-job.yml
```
# (1) Persistent Volume
<p>To create persistentVolume</p>
<p>I add my own storage path there. You please enter own storage path there. To enter own path you just go to code file and change storage path</p>

```
kubectl apply -f persistentVolume.yml
```
<p>To see Persistent Volume</p>

```
kubectl get pv -n <namespace>
```
<p>To delete this</p>

```
kubectl delete pv/local-pv
```
<p>Now you see STATUS section is Available</p>

# (2) Persistent Volume Claim
<p>To claim persistent volume use persistent volume claim</p>
<p>To use</p>

```
kubectl apply -f persistentVolumeClaim.yml 
```
<p>To see Persistent Volume</p>

```
kubectl get pv -n <namespace>
```
<p>Now you see STATUS section is Bound</p>
<p>To delete this</p>

```
kubectl delete pvc/local-pvc
```
# Service
<p>To apply service</p>

```
kubectl apply -f service.yml
```
<p>To see all resorces</p>

```
kubectl get all -n <namespace>
```
<p> <b>(For local computer)</b> To see this service in browser locally (Your local laptop)</p>

```
kubectl port-forward service/<your_service_name>-service -n <namespace> <enter_your_port(anything)>:80
```
<p> <b>(For cloud computer)</b> To see this service in browser locally (Cloud EC2 instence)</p>

```
sudo -E kubectl port-forward service/<your_service_name>-service -n <namespace> <enter_your_port(anything):80 --address=0.0.0.0
```
