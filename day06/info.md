
🔹 Step 1: Install Docker
Kind runs Kubernetes inside Docker containers.
 If you don’t already have Docker:
$ sudo apt update
$ sudo apt install -y ca-certificates curl gnupg lsb-release


Add Docker’s GPG key and repo:
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
 	$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


Install Docker:
$ sudo apt update
$ sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


Add your user to the docker group (so you don’t need sudo every time):
$ sudo usermod -aG docker $USER
$ newgrp docker


Verify Docker:
$ docker run hello-world



🔹 Step 2: Install kind
Download the latest release:
$ curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64


Make it executable:
$ chmod +x ./kind
$ sudo mv ./kind /usr/local/bin/kind


Verify:
$ kind --version



🔹 Step 3: Create Your First Cluster
$ kind create cluster --name demo-cluster


Check with:
$ kubectl cluster-info --context kind-demo-cluster



✅ Now you have a working kind cluster on Ubuntu.










Aspect
kind
kubectl
Full Form
Kubernetes IN Docker
Kubernetes Control CLI
Purpose
Creates and manages local Kubernetes clusters inside containers (for testing, learning, CI/CD).
Used to interact with any Kubernetes cluster (local or remote) via the Kubernetes API.
Scope
Cluster lifecycle management: create, delete, configure test clusters.
Cluster interaction: deploy apps, inspect resources, manage workloads, debug issues.
Runs On
Docker / Podman (needs a container runtime).
Any machine with network access to a Kubernetes cluster + valid kubeconfig.
Example Use Case
“I want to spin up a 1-control-plane + 2-worker test cluster on my laptop.”
“I want to list pods running on my cluster or deploy a new service.”
Who Uses It
Developers, learners, CI/CD pipelines for lightweight clusters.
Kubernetes admins, DevOps, SREs, developers — anyone managing workloads.
Commands Example
kind create cluster --name demo → creates a Kubernetes cluster in Docker containers.
kubectl get pods → lists pods running in the connected cluster.
Dependency
Needs Docker/Podman installed to create clusters.
Needs a valid kubeconfig file to connect to an existing cluster.
Relationship
Provides the cluster for local testing.
Talks to the API server of that cluster (including ones created by kind).




CREATING KIND CLUSTERS
	$ kind create cluster –name cka-cluster1

CREATING KIND CLUSTERS WITH ONE CONTROL AND 2 WORKER NODES
	$ vim config.yml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker

	$ kind create cluster –image kindest/node:v1.32.5@sha256:e3b2327e3a5ab8c76f5ece68936e4cafaa82edf58486b769727ab0b3b97a5b0d –name cka-cluster2 –config config.yml

COMMAND TO VIEW WHICH CLUSTER IS BEING USED
	$ kubectl config get-contexts

TO CHANGE CONTEXT
	$ kubectl config use-context <name_of_cluster>


