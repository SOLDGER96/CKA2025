## 1. Two types to run a pod
#####  Imperative 
    $ kubectl run nginx --image nginx:latest

##### Declarative
    $ Using a JSON or a YAML file
## 2. Get Information about Pod
    $ kubectl explain pod

## 3. Sample YAML to create pod
    $ vim pod.yml
```YAML

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    env: demo
    type: frontend
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
```
## 4. Run this yaml to run above Pod
    $ kubectl create -f pod.yml

## 5. Delete a existing Pod
    $ kubectl delete pod <name_of_Pod>

## 6. View Running Pods
    $ kubectl get pods

## 7. If you made changes to yaml file and want to make the same with the Pod do this
###### --Modify .yml file
###### --Use this command
    $ kubectl apply -f pod.yml

## 8. If Pod is not working as intended then
######  1. Use
    $ kubectl explain pod <name_of_pod>
- Here you will get whats wrong with the pod
###### 2. Make appropriate changes
    $ kubectl edit pod <namOfPod>
- it directly opens in vi editor so make changes according to the requirement

## 9. How to enter inside a Pod
    $ kubectl exec -it <nameOfPod> -- sh 

## 10. Getting a Pod in .yml format using a pod run using imperative format
    $ kubectl run nginx --image nginx:latest --dry-run=client -o yaml
##### Output
```YAML
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx:latest
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
- Now you can copy this yaml and make changes according to the given requirements

## 11. Getting a Pod in .yml format using a pod run using imperative format
    $ kubectl run nginx --image nginx:latest --dry-run=client -o json
Output
```YAML
{
    "kind": "Pod",
    "apiVersion": "v1",
    "metadata": {
        "name": "nginx",
        "creationTimestamp": null,
        "labels": {
            "run": "nginx"
        }
    },
    "spec": {
        "containers": [
            {
                "name": "nginx",
                "image": "nginx:latest",
                "resources": {}
            }
        ],
        "restartPolicy": "Always",
        "dnsPolicy": "ClusterFirst"
    },
    "status": {}
}

```

## 12. Get details about Pod
    $ kubectl describe pod <nameOfPod>

## 13. Get labels of any Pod
    $ kubectl get pods nginx-pod --show-labels

## 14. Get on which node a Pod is running
    $ kubectl get pods 
NAME &nbsp;&nbsp;&nbsp;&nbsp; READY   STATUS  &nbsp;&nbsp;  RESTARTS   AGE
nginx-pod   &nbsp; 1/1 &nbsp;&nbsp;&nbsp;&nbsp;    Running&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   0 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 28m

    $ kubectl get pods -o wide
NAME READY   STATUS    RESTARTS   AGE   IP           NODE                        NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          28m   10.244.0.5   cka-cluster-control-plane   <none>           <none>

