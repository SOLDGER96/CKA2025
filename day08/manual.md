## 📌 Kubernetes Replication 
Replication in Kubernetes ensures the desired number of Pod replicas are always running. The Replication Controller manages Pods across Nodes:

- If a Pod crashes, it creates a new one.
- If there are too many, it removes extras.
- It balances Pods across Nodes for high availability and scalability.

👉 In the diagram: 5 nginx Pods are maintained across Node1 (2 Pods) and Node2 (3 Pods).
![image alt](https://raw.githubusercontent.com/SOLDGER96/CKA2025/refs/heads/main/assets/day08/kubReplController.png)

---

## YAML Script for creaing 3 replicas of a Pod
```YAML
# Save file as rc.yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
  replicas: 3
```
##### Command to run the above yml
    $ kubectl apply -f rc.yml
##### View Running Pods
    $ kubectl get pods

##### Sample Output
```bash
$ kubectl apply -f rc.yml 
```
Output: replicationcontroller/nginx-rc configured
```bash
$ kubectl get pods
```
```bash
NAME             READY   STATUS    RESTARTS   AGE
nginx-rc-7xtnr   1/1     Running   0          72s
nginx-rc-jsfvm   1/1     Running   0          10s
nginx-rc-xff79   1/1     Running   0          10s
```

##### Get info about Replica Pods
```bash
$ kubectl get rc
NAME       DESIRED   CURRENT   READY   AGE
nginx-rc   3         3         3       4m38s
```
##### Get info about specific Pods
```bash
$ kubectl get pods
```
###### Output:
```bash
NAME             READY   STATUS    RESTARTS   AGE
nginx-rc-7xtnr   1/1     Running   0          35m
nginx-rc-jsfvm   1/1     Running   0          33m
nginx-rc-xff79   1/1     Running   0          33m
```
---
```bash
$ kubectl describe pods nginx-rc-xff79
```
###### Output:
```bash
Name:             nginx-rc-xff79
Namespace:        default
Priority:         0
Service Account:  default
Node:             cka-cluster-control-plane/172.18.0.2
Start Time:       Thu, 21 Aug 2025 08:12:55 +0530
Labels:           env=demo
Annotations:      <none>
Status:           Running
IP:               10.244.0.7
IPs:
  IP:           10.244.0.7
Controlled By:  ReplicationController/nginx-rc
Containers:
  nginx:
    Container ID:   containerd://c154e942234396cb6bb70e9d7dbadf980980eaa2d6d489a395f91605b2c2b4f8
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:33e0bbc7ca9ecf108140af6288c7c9d1ecc77548cbfd3952fd8466a75edefe57
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 21 Aug 2025 08:12:57 +0530
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-d4tms (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-d4tms:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  34m   default-scheduler  Successfully assigned default/nginx-rc-xff79 to cka-cluster-control-plane
  Normal  Pulling    34m   kubelet            Pulling image "nginx"
  Normal  Pulled     34m   kubelet            Successfully pulled image "nginx" in 2.097s (2.097s including waiting). Image size: 72324501 bytes.
  Normal  Created    34m   kubelet            Created container: nginx
  Normal  Started    34m   kubelet            Started container nginx

```
---
## 🔑 Key Differences Between ReplicaSet and ReplicationController

| Aspect               | **ReplicationController (RC)**                                 | **ReplicaSet (RS)**                                                                        |
| -------------------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| **Definition**       | Ensures a specified number of pod replicas are running.        | Newer, advanced version of RC that provides more powerful pod selection.                   |
| **Selector Support** | Only supports **equality-based selectors** (e.g., `env=prod`). | Supports **both equality-based and set-based selectors** (e.g., `env in (prod, qa)`).      |
| **Usage**            | Legacy – was the original way to manage pod replicas.          | Modern replacement – used internally by Deployments and preferred today.                   |
| **API Version**      | Found in older API groups (`v1`).                              | Belongs to `apps/v1` API group.                                                            |
| **Recommendation**   | Deprecated, not recommended for new workloads.                 | Recommended way to directly manage replicas (though usually managed via a **Deployment**). |
| **Practical Usage**  | Rarely used now.                                               | Used by **Deployments**, **DaemonSets**, **StatefulSets**, etc.                            |

## Creating Pods using ReplicaSet
```YAML
#save file as rs.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
  replicas: 5
  selector:
    matchLabels:
      env: demo
```
Run the above yml
```bash
$ kubectl apply -f rs.yml
```
Output:
```bash
$ kubectl get pods
```
```bash
NAME             READY   STATUS              RESTARTS   AGE
nginx-rs-2kx4p   1/1     Running             0          2m11s
nginx-rs-75g45   0/1     ContainerCreating   0          3s
nginx-rs-csbk4   1/1     Running             0          2m11s
nginx-rs-hsjvj   1/1     Running             0          3s
nginx-rs-qx22p   1/1     Running             0          2m11s
```

##### Edit config using kubectl edit
```bash
$ kubectl edit rs/nginx-rs
```
- make changes according and save the file using ':wq' as this file opens using vim editor
```vim
:wq
```
##### Change replicas using shell command
```bash
$ kubectl scale --replicas=7 rs/nginx-rs
```
```bash
$ kubectl get pods
```
```bash
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-2kx4p   1/1     Running   0          17m
nginx-rs-75g45   1/1     Running   0          14m
nginx-rs-9fzgg   1/1     Running   0          8s
nginx-rs-csbk4   1/1     Running   0          17m
nginx-rs-hsjvj   1/1     Running   0          14m
nginx-rs-lmw6b   1/1     Running   0          8s
nginx-rs-qx22p   1/1     Running   0          17m
```
---
## 🚀 Deployment in Kubernetes

A Deployment in Kubernetes is a higher-level abstraction that manages applications by creating and managing ReplicaSets, which in turn manage Pods.

It provides declarative updates for your application, meaning you just tell Kubernetes the desired state, and it will make sure the cluster matches it.

##### 🔑 Key Features of Deployment
![image](https://github.com/SOLDGER96/CKA2025/blob/main/assets/day08/deploymentController.png?raw=true)

- Manages ReplicaSets
    - Automatically creates ReplicaSets and Pods.
    - Ensures the desired number of replicas are always running.
- Rolling Updates
    - Updates your application gradually with zero downtime.
    - Supports rollback if something goes wrong.
- Self-healing
    - If a pod or node fails, the Deployment replaces the pods automatically.

- Scaling
    - Easy to scale up or scale down pods with a single command or YAML change.

- Versioned History
    - Maintains history of rollouts so you can rollback to a previous version.

##### Create a kind: deploy yaml file
```YAML
# Save as deploy.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
  replicas: 4 
  selector:
    matchLabels:
      env: demo
```
Run this Deployment (Make sure you delete existing pods)
```bash
$ kubectl apply -f deploy.yml
```
##### Get all objects of a deoloyment
```bash
$ kubectl get all
```
Output:
```bash
NAME                                READY   STATUS    RESTARTS   AGE
pod/nginx-deploy-7fff95c694-2c7d5   1/1     Running   0          44s
pod/nginx-deploy-7fff95c694-9r2qx   1/1     Running   0          44s
pod/nginx-deploy-7fff95c694-9sh5v   1/1     Running   0          44s
pod/nginx-deploy-7fff95c694-vc49p   1/1     Running   0          44s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   14h

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deploy   4/4     4            4           44s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deploy-7fff95c694   4         4         4       44s
```

##### Change image of deployment
```bash

$ kubectl set image deploy/nginx-deploy nginx=nginx:1.9.1
```
##### Check for any changes
```bash
$ kubectl rollout history deploy/nginx-deploy
```
Output:
```bash
deployment.apps/nginx-deploy 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```
##### Check if change took place or not
```bash
$ kubectl describe deploy/nginx-deploy
```
##### Get back to previous version
```bash
$ kubectl rollout undo deploy/nginx-deploy
```
Output:
```
deployment.apps/nginx-deploy 
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
```

## Getting a yaml file for deployments
```bash
$ kubectl create deploy deploy/nginx-new --image=nginx --dry-run=client -o yaml > genDeploy.yml
```
```bash
$ cat genDeploy.yml 
```
```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: deploy/nginx-new
  name: deploy/nginx-new
spec:
  replicas: 1
  selector:
    matchLabels:
      app: deploy/nginx-new
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: deploy/nginx-new
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}

```
