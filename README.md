# Topics

* Introduction and Architecture of Kubernetes
* Difference between monolithic and microservice architecture.
* Controllers and worker nodes are the key points of their working pattern.
* Kubernetes Installation on Aws (kubeadm)
* What is Pod, and how does it work?
* Namespaces.
* Deployments
* Deployment Strategies
* Replicasets
* Kubernetes Services
* Ingress
* Cluster maintenance: Drain, Cordon, and Uncordon
* Imperative and declarative commands.
* Role-Based Access Control (RBAC)
* Service Accounts
* Labels, Selectors & Annotations
* Taints and Tolerations
* Node Selector
* Node Affinity
* Volumes, Persistent volume, Persistent volume claim
* Backup and Restore
* Daemonsets
* Environment Variable in Kubernetes with Config Maps and Secrets
* Security and Certificate details
* Secure Cluster
* Pause Containers
* Custom Resource Definition
* Editing Pods and Deployments
* Static Pods


# Introduction and Architecture of Kubernetes

Kubernetes was designed by Google and managed by the Cloud Native Computing Foundation (CNCF).

Kubernetes helps us to create mini/microservices applications while in Docker we create monolithic applications.

# Docker vs Container D

* At the beginning of the container Era, the only tool for running containers was Docker. Rkt is there, but Docker's user experience is much better than Rkt's. Then Kubernetes came to orchestrate Docker and didn't support any other container solutions. 
* Kubernetes grew in popularity, and other container runtimes wanted to work with Kubernetes.
* So Kubernetes introduce and Interface called **Container Runtime Interface** (CRI).
* CRI allows any vendor to work as a container runtime for Kubernetes.
* The only thing they have to maintain the OCI standards
* OCI stands for **Open Container Initiative** and consists of ImageSpec and RuntimeSpec.
* ImageSpec means specifications of how an image should be built.
* Runtimespec defines the standards of how any container runtime should be developed.
* So, keeping these standards in mind, that can be used by anyone to work with Kubernetes; however, Docker wasn't built to support the CRI standards because it was present way before CRI was introduced.
* So Kubernetes introduced Dockershim for a temporary basis.
* Other container runtimes worked through the CRI, but Docker continues to work without it as Docker consist of multiple tools put together called Docker CLI, volumes, auths, security, also the container runtime called runC and the daemon managed runC called ContainerD.
* So ContainerD is CRI compatible and can work directly with Kubernetes as other runtimes do.


# Kubernetes vs Docker

* Docker is a container platform.
* Kubernetes is a tool that manages containers, called a container orchestration tool.
* Containers are ephemeral in nature means containers can easily die due to 100's of issues.
* Docker platform runs on a single host.
* Docker does not have a feature of auto-healing if any containers die, is doesn't come up automatically.
* Docker does not support any enterprise-level support (Firewall, LB, API gateway, Auto-scale, Auto-heal)
* Kubernetes has a feature of auto-scaling, which auto distributes the load of containers.


# Kubernetes Cluster / Architecture

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/418356f8-bf7f-4d67-adb3-36b7c2b8ff0e)

Below are the major components of Kubernetes and what they do:

**Master Node / Controller**

A master node is where all the major processes are run and manage the cluster. There can be more than one master node for high availability.

Key Features:

* **API Server:** Entry point to Kubernetes cluster for the user interface, API, and CLI

* **Controller Manager:** Tracks and manages the containers in the cluster
  
* **Scheduler:** Determines which worker nodes will be used to host the pods.
  
* **Etcd:** A key-value store that contains the state of the cluster

**Worker Node**

* Runs the containers (inside the pods)

Key Features:
  
  **Kubelet:**
  
 * Primary Kubernetes ‘agent’ that runs on each node
    
 * Communicates to the Master via API server

# ETCD

ETCD is distributed key-value store that is Simple, Secure and Fast and used to store the state of the cluster also used to restore the exact state of cluster in case of disater recovery.

    etcdctl --version

    ETCDCTL_API=3 etcdctl snapshot save --help

    ps -aux | grep etcd   >> To get the certificate path

    ETCDCTL_API=3 etcdctl snapshot save my-backup.bkp --cert= --key= --cacert --endpoints=127.0.0.1:2379

--cacert /etc/kubernetes/pki/etcd/ca.crt     
--cert /etc/kubernetes/pki/etcd/server.crt     
--key /etc/kubernetes/pki/etcd/server.key

    ETCDCTL_API=3 etcdctl snapshot status my-backup.bkp

    ETCDCTL_API=3 etcdctl snapshot status my-backup.bkp --write-out=table

Set version 3 Permanent

    export ETCDCTL_API=3 ./etcdctl 

To put and get value into ETCD

    ./etcdctl put key1 value1 

    ./etcdctl get key1

**Characters of ETCD**

1. ETCD written in GO programming language.
2. ETCD can also be configured externally.
3. ETCD used to store configuration details.

**System requirements to install ETCD**

* High CPU Capacity
* 8gb memory for small deployments
* 16gb - 64gb for heavy deployments
* Fast disk of about 7200 RPM (Hard disk)
* 1Gbe network LAN Card for common ETCD deployments
* 10Gbe network Lan Card for large ETCD deployments

**How do I install ETCD?**

kubeadm init

# Kube-api server

* Primary management component in Kubernetes.
* When we run "kubectl" command it's first reach to Kube-APIserver.
* Kube-APIserver first authenticate the request and validates it, then it retreives the data from ETCD cluster and respond back.
* The Scheduler continuesly monitor the API server and realize that there is new pod with no node assigned.
* The Scheduler identifies the right node to place the new pod and communicates back to the Kube-API server.
* The API server then updates the information in ETCD cluster.
* The API server passes the information to kubelet on worker node.
* The kubelet then creates the pod on the worker node and instruct the Container Runtime Engine to deploy the application image.
* Once done, the kubelet updates the status back to the API server, API server updates the data back to the ETCD cluster.

Tasks API server do

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/4e17c7c8-9c3a-4c0c-9749-d14952ee9cdf)


# Kube Controller Manager

Controller manager managed different controller in Kubernetes. 

Controller manager have two types:

1. Kube-controller manager

2. Cloud-controller manager

- Controller managers are responsible for defining and maintaining the desired state of a cluster.
- Controller managers ensures that the system remains in the desired state.
- Controller managers monitor and respond to the state and health of the Pods, ensuring that the desired number of pod are up, running and healthy.
- If the state of health changes, the controller will respond accordingly.


# Kubernetes Scheduler

* 'kube-scheduler' is the default scheduler for Kubernetes and runs as part of the control plane.
* We can create our own scheduler and use instead of default scheduler.
* In a cluster, Nodes that meet the scheduling requirements for a Pod are called feasible nodes.
* If none of the nodes are suitable, the pod remains unscheduled until the scheduler is able to place it.
* The scheduler finds feasible Nodes for a Pod with the highest score among the feasible ones to run the Pod.
* The scheduler then notifies the API server about this decision in a process called binding.
* kube-scheduler selects a node for the pod in a 2-step operation:
	a. Filtering - The filtering step finds the set of Nodes where it's feasible to schedule the Pod as per the enough available resources to meet a Pod's specific resource requests.
	b. Scoring - the scheduler ranks the remaining nodes to choose the most suitable Pod placement. The scheduler assigns a score to each Node that survived filtering.
* Finally, kube-scheduler assigns the Pod to the Node with the highest ranking.

* Note: Check if there's a scheduler or not, if not then we have to manually schedule a pod like below.  Command: kubectl get pods -n kube-system

* Check for nodeName in yaml file to add the scheduling feature:

			---
			apiVersion: v1
			kind: Pod
			metadata:
			  name: nginx
			spec:
			  nodeName: node01
			  containers:
			  -  image: nginx
			     name: nginx
			...
  
# Kubelet
- Every kubelet is talking to the kube-apiserver.
- Kubelet is an agent that runs on each node and ensures that all containers are running.
- Kubelet uses the API server to get the configurations of the pod and ensures the containers described are up and running.

  To check if the kubelet is running:-

  systemctl status kubelet

# Kube-proxy

Kube proxy is a process that runs on each node in the Kubernetes cluster, it's job is to look for new services and every time a new service is created it creates the appropriate rules on each node to forward traffic to those services to the backend pods.

One way to doing this through IP tables rules. In this case it creates iptables rule to each node in the cluster to forward traffic heading to the IP of the service.

# Difference between monolithic and microservice architecture.

  **Monolithic:** It means very large, united, and difficult to change. All the functionalities of a project exist in a single container. It is not very reliable, as a single bug in any module can bring down the entire monolithic application.

  **Microservices:** Collection of small, independent services that work together to perform specific tasks.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/31f9a2f8-a25a-40c5-841b-ad3050b3cd7e)                            ![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/165a9a5b-3601-4d3d-be20-28ec09aec62e)


  # Kubernetes Objects

  - Kubernetes uses objects to represent the state of the cluster. Below are the list of kubernetes objects:

    1. Namespace
    2. Pod
    3. Service
    4. Deployments
    5. Replicasets
    6. Volumes
    7. Secrets
    8. Config maps
    9. Jobs
    10. Daemonsets


   # Understand each object with Exmaples
  
  **Kubectl**: Kubectl is a command line tool to run kubernetes commands.

  **Command auto completion**

source <(kubectl completion bash)

echo "source <(kubectl completion bash)" >> ~/.bashrc

alias k=kubectl

complete -o default -F __start_kubectl k


# Installation with Kubeadm

URL for ports & protocols: https://kubernetes.io/docs/reference/networking/ports-and-protocols/

* Create two security groups for master & control plane.
* Create 2 instances 1 master & 1 datanode

Note: If you made any mistake in installation, run **"kubeadm reset"**

Pre-requisites:
* Ubuntu OS (Xenial or later)
* sudo privileges

=========Run on Both Nodes Master & Worker:================================


	curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
	curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
	echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
	sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
	chmod +x kubectl
	mkdir -p ~/.local/bin
	mv ./kubectl ~/.local/bin/kubectl

# and then append (or prepend) ~/.local/bin to $PATH
	kubectl version --client

# disable swap
	sudo swapoff -a

# Create the .conf file to load the modules at bootup
	cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
	overlay
	br_netfilter
	EOF

	sudo modprobe overlay
	sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
	cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
	net.bridge.bridge-nf-call-iptables  = 1
	net.bridge.bridge-nf-call-ip6tables = 1
	net.ipv4.ip_forward                 = 1
	EOF

# Apply sysctl params without reboot
	sudo sysctl --system

## Install CRIO Runtime
	sudo apt-get update -y
	sudo apt-get install -y software-properties-common curl apt-transport-https ca-certificates gpg

	sudo curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
	echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" | sudo tee /etc/apt/sources.list.d/cri-o.list

	sudo apt-get update -y
	sudo apt-get install -y cri-o

	sudo systemctl daemon-reload
	sudo systemctl enable crio --now
	sudo systemctl start crio.service

	echo "CRI runtime installed successfully"

# Add Kubernetes APT repository and install required packages
	curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
	echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

	sudo apt-get update -y
	sudo apt-get install -y kubelet="1.29.0-*" kubectl="1.29.0-*" kubeadm="1.29.0-*"
	sudo apt-get update -y
	sudo apt-get install -y jq

	sudo systemctl enable --now kubelet
	sudo systemctl start kubelet


=========Master Node (Only:================================
a) Initialize the Kubernetes master node.

	sudo kubeadm config images pull

	sudo kubeadm init

	mkdir -p "$HOME"/.kube
	sudo cp -i /etc/kubernetes/admin.conf "$HOME"/.kube/config
	sudo chown "$(id -u)":"$(id -g)" "$HOME"/.kube/config

# Network Plugin = calico
	kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/calico.yaml
 
 
 
After succesfully running, your Kubernetes control plane will be initialized successfully.

b) Generate a token for worker nodes to join:

 	kubeadm token create --print-join-command

c) Expose port 6443 in the Security group for the Worker to connect to Master Node

Worker Node (Only):
a) Run the following commands on the worker node.

	sudo kubeadm reset pre-flight checks


#####################################################
  **Pods**
   
  * Pod is a collection of containers that can run on a host. This resource is created by clients and scheduled onto hosts.
    
  * Pods are ephemeral in nature, which means the same pod cannot be redeployed once dies, but another pod will be up with the same configurations.

k get pods			# Get pods information

k get pods -o wide		# Get IP address on which node the pod is deployed

k get pods -o wide -v=7		# Verbose level 7

  **Kubernetes Namespaces**

Namespaces are like virtual cluster inside the cluster. A namespace isolates the resources from the resources of other namespace. For example, we must have diffrent names for objects such as pods,services and deployments live in a namespace but we can have same name for these objects in two diffrent namespaces.

   kubectl get namespaces

   kubectl get pod -n kube-system

   kubectl get pods --all-namespaces

   kubectl create namespace demo-k8-namespace

  **Get all resources in a namespace and all resources in all namespace**
  
   kubectl get all

   kubectl get all -A

  **Set specific namespace permanently**

   kubectl config set-context $(kubectl config current-context) --namespace=kube-system

   kubectl get config

   kubectl config view | grep namespace

  **Create namespace through manifest file**

  ![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/940b9663-f3b5-44f2-9628-9b2eb5455da7)

  kubectl apply -f ns.yaml
  
Note: In above command, apply means create and run

**Run a pod in diffrent namespace**

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/64be4a13-9154-4eb5-bf54-e1e5b392c29f)

kubectl apply -f demo.yaml -n demo-k8-namespace

  **Man page of pod:** kubectl explain pod

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/5be73522-ff1b-449d-bb74-1efa52f5aa0f)

  **Note:** Container does not have any IP address but Pods have.

          kubectl get pods -o wide

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/510a593f-90ec-46f3-93c9-67c427fdd077)


* In above snapshot, we can see 1/1 under **Ready** section.

  The second part is number of container in the pod.

  The first part is number of container that are running.

* To get inside the pod

        kubectl exec -it nginx -- bash

* If there are multiple containers, go to specific container with "-c"

        kubectl exec -it <pod-name> -c <container-name> -- bash

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/f26bb514-eff3-4922-b42d-727a7d46db80)
  
Run a demo pod to **Check logs**

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/9d453fed-8ba9-440b-83e2-7bb9ad433ea3)

**Print the logs for the last 6 hours for a pod**

kubectl logs --since=6h <pod_name>

kubectl logs <pod_name> --since-time="2025-08-21T12:00:00Z"

**Get the most recent 50 lines of logs for a pod**

kubectl logs --tail=50 <pod_name>

**Print the logs for a pod and follow new logs**

k logs -f test-pod

**Print the logs for a container in a pod**

kubectl logs -c <container_name> <pod_name>

**View the logs for a previously failed pod**

kubectl logs --previous <pod_name>

**View logs for all containers in a pod**

kubectl logs <pod_name> --all-containers

  **Delete pods by two ways**

  kubectl delete -f demo.yaml -n demo-k8-namespace

  kubectl delete pod nginx

# Objects and their versions

      kubectl api-resources

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/1d047ee9-ca8f-47e2-870b-da61ac3bf02b)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/2d367e61-e1f2-4314-ab31-9aace0f09bd9)

  # How kubernetes works in production enviroment?

- To create a container we write a command.

  Example: docker run -it httpd /bin/bash

- Instead of writing a command on CLI write a YAML/manifest file that is enterprise-level feature.
  
- But, In a production environment it is not suggested to create a pod directly, instead we create a **Deployment**.
  
- A **deployment** will create a Replicaset.
  
- A **replicaset** will create a Pod.

**Deployment:** It is a object, by which we can manage the scaling of the application while maintaining desired state and actual state of the pods, we can scale up and down of the pods.
Basically, kubernetes deployment has enterprise level feature called as Auto-healing, Auto-scaling and Zero-downtime.

**Replicaset:**   It is a default controller in Kubernetes. It ensures that a specified number of pod replicas are running at any given time. It is used to automatically replace any pods that fail, are deleted, or terminated.
The term Replica set is replaced by Replication controller, both can be used but new and updated term is Replicaset.

Kubernetes has a lot of default and custom controllers.

Default: Node, Deployment, Replicaset, Service, Cronjob, StatefulSet (controllers)
Custom: ArgoCD




vim deployment.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/88c974b3-2c8d-43b8-a717-76620267911d)

      kubectl apply -f deploy.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/1b83abed-5483-4c3c-abd8-8e3ec81aa14a)

Note: In above scenerio, we have create one resouce called deployment and rest taken care automatically because deployment provide auto-healing and zero-downtime in kubernetes.
      Deployment take help of replicaset.
      Replicaset is a kubernetes controller. A go-lang application which kubernetes has written and ensures for the specific behaviour 

      kubectl get pods -w

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/bfcf3a42-28d9-47ea-9145-8f2d8376992d)

Delete the pod nginx 

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/7c2521ac-f6d7-4594-8583-cc672a4fb03b)

Even before deleting the pod, the new pod will created

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/42794592-adde-4a96-a922-8cc0ed107636)

Command:

*  k scale replicaset new-replica-set --replicas=5

=============================================================================
# Requests and Limits

* **Memory resource units**

Limits and requests for memory are measured in bytes. You can express memory as a plain integer or as a fixed-point number using one of these quantity suffixes: E, P, T, G, M, k. You can also use the power-of-two equivalents: Ei, Pi, Ti, Gi, Mi, Ki. For example, the following represent roughly the same value:

* 128974848, 129e6, 129M,  128974848000m, 123Mi

Pay attention to the case of the suffixes. If you request 400m of memory, this is a request for 0.4 bytes. Someone who types that probably meant to ask for 400 mebibytes (400Mi) or 400 megabytes (400M).

Error Faced:

1. Insufficient memory
2. OOM error (out of memory)
  
=============================================================================
# Deployment strategies

1. Recreate
2. Rolling Updates
3. Blue-Green
4. Canary
5. Progressive Delivery
6. A/B Testing
7. Shadow

* **Recreate deployment strategy**: involves terminating all existing pods running the old version of an application before creating new ones with the updated version. This approach ensures a clean break between the old and new versions but results in a period of downtime during the transition. **Not used in Production**

* **Rolling update** is the default strategy for updating applications. It allows for the gradual replacement of old application pods with new ones, ensuring minimal downtime and continuous service availability during the update process. **No downtime and some usage in production**.

* **Blue-green deployment strategy** involves having two identical environments, "blue" (the current live environment) and "green" (the environment with the new application version), to facilitate zero-downtime deployments. The strategy switches traffic from the live environment (blue) to the new environment (green) after thorough testing, allowing for quick rollbacks if issues arise. This used in **Prod**

* **Canary deployment** strategy allows for the gradual rollout of a new application version to a small subset of users before releasing it to the entire user base. This approach helps minimize risk by allowing for the early detection of issues in the new version before they impact all users.

* Strategies 5, 6, and 7 are generally not used in production and require Argo CD

Practicals:

**non-declarative commands:**

	kubectl set image deployment/nginx-deployment -n nginx-ns <container-name>=1.27.3
	kubectl rollout status deployment/nginx-deployment -n nginx-ns
	kubectl rollout history deployment/nginx-deployment -n nginx-ns
	kubectl rollout undo deploy

# Re-create

* kubectl create ns recreate
* kubectl get pods -n recreate
* git clone https://github.com/LondheShubham153/kubestarter.git
* cd kubestarter/Deployment_Strategies/Recreate-deployment/
* Create a deployment and service with the "kubectl apply" command.
* Verify: kubectl get svc -n recreate
* kubectl describe svc recreate-service -n recreate
* Try to access with public IP with port 3000 (not accessible).
* kubectl port-forward --address 0.0.0.0 service/recreate-service 3000:3000 -n recreate &

<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/48e35e0d-7e57-4cdf-b9ae-2af15b872a88" />


**Dep** 

	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: online-shop
	  namespace: recreate
	  labels:
	    app: online-shop
	spec:
	  replicas: 3
	  strategy:
	    type: Recreate
	  selector:
	    matchLabels:
	      app: online-shop
	  template:
	    metadata:
	      labels:
	        app: online-shop
	    spec:
	      containers:
	        - name: online-shop
	          image: amitabhdevops/online_shop
	          resources:
	            limits:
	              cpu: "500m"
	              memory: "512Mi"
	            requests:       
	              cpu: "200m"
	              memory: "256Mi"

**svc**

	apiVersion: v1
	kind: Service
	metadata:
	  name: recreate-service
	  namespace: recreate
	spec:
	  selector:
	    app: online-shop
	  type: NodePort
	  ports:
	    - protocol: TCP
	      port: 3000
	      targetPort: 3000
	      nodePort: 30000


Note: Now, run the pods without the footer image and open a duplicate tab with the watch command to see the status of the pods re-creation

* kubectl set image deployment/online-shop online-shop=amitabhdevops/online_shop_without_footer -n recreate
* kubectl port-forward --address 0.0.0.0 service/recreate-service 3000:3000 -n recreate &

<img width="634" height="121" alt="image" src="https://github.com/user-attachments/assets/6600a1f7-2fb0-41dc-ae00-443be09908ce" />

* kubectl delete -f .	# delete all svc, dep, pods, rs, not files



# Rolling Update

* kubectl create ns rollingupdate
* kubectl apply -f deploy-rolling.yml
* cp recreate-service.yml rollingupdate-svc.yml
* kubectl apply -f rollingupdate-svc.yml
* kubectl apply -f rollingupdate-svc.yml
* kubectl get all -A
* I created a mistake in a deployment image, the name should be "without footer" apply this deployment and watch pods.

<img width="487" height="100" alt="image" src="https://github.com/user-attachments/assets/87e45ebe-67f4-4c13-916b-60591adbc299" />

<img width="534" height="163" alt="image" src="https://github.com/user-attachments/assets/806654f6-9277-47df-8b66-a8932cf86792" />

* This will throw an error image ErrImagePull

After making the necessary corrections, all pods are in a ready state.

<img width="610" height="165" alt="image" src="https://github.com/user-attachments/assets/28261146-eb64-4a76-a89b-e4746f2d2b0c" />

* kubectl port-forward --address 0.0.0.0 service/rolling-service 3000:3000 -n rollingupdate &
* kubectl delete -f .	# delete all svc, dep, pods, rs, not files

	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: online-shop
	  namespace: rollingupdate
	  labels:
	    app: online-shop
	spec:
	  replicas: 3
	  strategy:
	    type: RollingUpdate
	    rollingUpdate:
	      maxSurge: 1         # At the time of update how many extra pods required so no downtime will faced
	      maxUnavailable: 0   # We don't have any unavailable pods
	  selector:
	    matchLabels:
	      app: online-shop
	  template:
	    metadata:
	      labels:
	        app: online-shop
	    spec:
	      containers:
	        - name: online-shop
	          image: amitabhdevops/online_shop
	          resources:
	            limits:
	              cpu: "500m"
	              memory: "512Mi"
	            requests:       
	              cpu: "200m"
	              memory: "256Mi"


	apiVersion: v1
	kind: Service
	metadata:
	  name: rolling-service
	  namespace: rollingupdate
	spec:
	  selector:
	    app: online-shop
	  type: NodePort
	  ports:
	    - protocol: TCP
	      port: 3000
	      targetPort: 3000
	      nodePort: 30000

  # Blue-Green

* kubectl create ns bluegreen
* touch blue-deployment-wo-footer.yml
* kubectl apply -f blue-deployment-wo-footer.yml

Note: This service runs on port 3001


	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  labels:
	    app: online-shop-blue
	  name: online-shop-blue
	  namespace: blue-green-ns
	spec:
	  replicas: 3
	  selector:
	    matchLabels:
	      app: online-shop-blue
	  template:
	    metadata:
	      labels:
	        app: online-shop-blue
	    spec:
	      containers:
	      - image: amitabhdevops/online_shop_without_footer
	        name: online-shop-blue
	        resources:
	          limits:
	            cpu: "500m"
	            memory: "512Mi"
	          requests:
	            cpu: "200m"
	            memory: "256Mi"
	
	---
	apiVersion: v1
	kind: Service
	metadata:
	  name: online-shop-blue-deployment-service
	  namespace: blue-green-ns
	spec:
	  selector:
	    app: online-shop-blue
	  type: NodePort
	  ports:
	    - protocol: TCP
	      port: 3001
	      targetPort: 3000
	      nodePort: 30001

* touch green-deployment.yaml

	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  labels:
	    app: online-shop-green
	  name: online-shop-green
	  namespace: bluegreen
	spec:
	  replicas: 3
	  selector:
	    matchLabels:
	      app: online-shop-green
	  template:
	    metadata:
	      labels:
	        app: online-shop-green
	    spec:
	      containers:
	      - image: amitabhdevops/online_shop
	        name: online-shop-green
	        resources:
	          limits:
	            cpu: "500m"
	            memory: "512Mi"
	          requests:
	            cpu: "200m"
	            memory: "256Mi"
	
	---
	apiVersion: v1
	kind: Service
	metadata:
	  name: online-shop-green-deployment-service
	  namespace: bluegreen
	spec:
	  selector:
	    app: online-shop-green
	  type: NodePort
	  ports:
	    - protocol: TCP
	      port: 3000
	      targetPort: 3000
	      nodePort: 30000

  
* kubectl apply -f green-deployment.yaml

Note: Now, we can see both blue & green deployment/pods are running

<img width="899" height="305" alt="image" src="https://github.com/user-attachments/assets/e15723a9-c5fc-4597-94a2-f8506ea19dad" />

* **Open another tab and run** "watch kubectl get pods -n blue-green"

Run the live environment **Blue environment is Live**


* kubectl port-forward --address 0.0.0.0 service/online-shop-blue-deployment-service 30001:3001 -n bluegreen &
* Google: http://3.6.86.31:30001/

Now, port forward the Green environment

* kubectl port-forward --address 0.0.0.0 service/online-shop-green-deployment-service 30000:3000 -n bluegreen &
* Google: http://3.6.86.31:30000/

Note: on port 30000, the web page opened without a footer.
Note: Consider that blue is a live environment. We verified that the green environment is working fine with the footer, so we have to move traffic to the Green environment.

* vim blue-deployment-wo-footer.yml

In blue-deployment, change in service's section 

	selector:
	    app: online-shop-green

* kubectl apply -f blue-deployment-wo-footer.yml
* pkill -f "kubectl port-forward"		# kill cache of port-forwarding
* kubectl get all -n bluegreen			# Check for PORT(S) in service/online-shop-blue-deployment-service
* kubectl port-forward --address 0.0.0.0 service/online-shop-blue-deployment-service 30001:3001 -n bluegreen &
* Google: http://3.6.86.31:30001/

Note: With no downtime, we have changed our traffic from blue to green Env by just changing the name in blue-dep Service and forwarding the port.

# Canary

* kubectl delete -f .

Note: Version 1 is Apache deployment, Version 2 is nginx deployment. When we deploy a canary, our audience can see different versions of the deployments. For this purpose, we need an ingress/ingress-controller.

	kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/kind/deploy.yaml
	kubectl get pods -n ingress-nginx		# Generally pods went into pending state

* If the ingress controller pod remains in the Pending state due to node selector issues, remove the node selector:

	kubectl patch deployment ingress-nginx-controller -n ingress-nginx --type=json \
	-p='[{"op": "remove", "path": "/spec/template/spec/nodeSelector"}]'

	kubectl get pods -n ingress-nginx
	kubectl apply -f namespace.yaml
	kubectl apply -f nginx-configmap.yaml		# Used as a webpage
	kubectl apply -f apache-configmap.yaml		# Used as a webpage

 	kubectl apply -f nginx-deployment.yaml
  	kubectl apply -f apache-deployment.yaml

  Note: nginx-deployment is **v1** and apache-deployment is **v2** also, note that our app: web in all deployments, even in the canary-service.yaml

	kubectl apply -f canary-service.yaml
	kubectl apply -f ingress.yaml

  









=============================================================================
# Service

Document used by Densify: https://www.densify.com/kubernetes-autoscaling/kubernetes-service-load-balancer/

* In the World of Kubernetes, the pods are created through a replicaset via deployment, and the pods can be easily dead and non-operational, which is fine. Still, the IP address of the pod will be changed every time the pod dies, which is the issue that Kubernetes services are resolving.

* On top of the deployment Service will be created. So instead of accessing the pod directly, the request will redirect to the Service and acts as a **Load Balancer** because it uses a component called kube-proxy.

* Here, service will be directly connected to pods, but there might be a scenario where service also faces the same problem because the pod IP can be changed. So service also resolves this issue via **service discovery** by using **Labels and Selectors**.

* Another thing that the service offers is **Expose the pod to the external world**.

The Short name of the service is "svc"

* Whenever a service is created, an endpoint is automatically created by the same name.

      kubectl describe service/<service-name>

* Types of Services:-
  
  1. ClusterIP: This is the default service. If we do not mention any type in the manifest file so ClusterIP is automatically selected. We cannot use the IP outside the cluster when used as ClusterIP; basically, it is accessible within the cluster. Dependent applications can interact with other applications internally using the ClusterIP service.

  2. NodePort: NodePort services are accessible outside the cluster. It creates a mapping of pods to their hosting node/machine on a static port. For example, you have a node with IP address 10.0.0.20 and a Redis pod running under it. NodePort will expose 10.0.0.20:30038, assuming the port exposed is 30038, which you can then access outside the Kubernetes cluster.

  3. Load Balancer: This service type creates load balancers in various Cloud providers like AWS, GCP, Azure, etc., to expose our application to the Internet. The Cloud provider will provide a mechanism for routing the traffic to the services. The most common example usage of this type is for a website or a web app.
 
* Practical: Create 2 YAML files, 1 is for deployment, 1 is for service

![image](https://github.com/user-attachments/assets/65578ba0-2993-465f-9ef5-dc3574266728)


![image](https://github.com/user-attachments/assets/4ac4cbda-c86c-4d25-be02-72fd73c10fa1)


**Labels and Selectors**

* Labels are key/value pairs that are attached to objects such as Pods, ReplicaSet, Deployments, Services, etc..
* In manifest file Labels must be placed under metadata.
* Selectors are used to identify the labels.
* Currently 2 type of selectors are present (Equlity based and Set based)

1. Equlity based - which shows labels equal to or not equal to

		environment = production
		tier != frontend

		kubectl get pods --selector env=dev --no-headers | wc -l   # either use '--selector' or '-l' both are same.
		kubectl get pods -l environment=production,tier=frontend
		kubectl get pods -l Company=amdocs
    	kubectl get pods -l Company=IBM --overwrite
   		kubectl get pods -l Company!=amdocs   # Not Found
		kubectl delete pods -l Company!=amdocs  # delete pod which does not match label

3. Set based  - where have to select between multiple labels - (in, notin, exist)

		kubectl get pods -l 'environment in (production),tier in (frontend)'
		kubectl get pods -l 'environment,environment notin (frontend)'

    kubectl get pods-- show-labels

- Set label Imparitive way

    kubectl label pod testpod level=mid
    
- To check the Selectors run below command

    kubectl get pods --selectors app=App1

Note: In Replicaset or Deployments labels are defined at 2 places under metadata, one at top and one at bottom under template section 

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/303c4c00-0f37-417e-abea-f46ee1f0c01f)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/d4786122-23a9-440e-8f9b-7801febc7ed3)

		apiVersion: v1
		kind: Pod
		metadata:
		  name: label-demo
		  labels:
		    environment: production
		    app: nginx
		spec:
		  containers:
		  - name: nginx
		    image: nginx:1.14.2

# Annotations

* Annotations are used to provide contextual information.
* Labels are for kubernetes while Annotations are for Humans.
* Annotations are used for non-identifying information just like mobile number, email id, or a application version, to mention in the yaml file.

    kubectl annotate pod nginx "app=nginx-web-server-new-deployment"

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/dadd320b-9b82-4a46-b3b4-f54872d0e5a0)

# Node Selector

* A node selector is a field in a Pod's yaml file that allows you to limit/force on which node a Pod can be scheduled on.
* It works by matching labels applied to nodes with the key-value pairs specified in the nodeSelector field of a Pod.

		kubectl label nodes <node-name> <key>=<value>

		apiVersion: v1
		kind: Pod
		spec:
		  name: my-pod
		metadata:
		- name: my-pod-for-node-selector
		  containers:
		  - name: nginx
		    image: nginx
		  nodeSelector:
		      disktype: bigSizedisk

  * Above, we have mentioned the "disktype: bigSizedisk" but how k8 know which is the bigsizeddisk?
  * These are the key-value pairs "disktype: bigSizedisk" which are Labels assigned to the nodes.
  * We have Label our node before creating this pod.

  		kubectl label nodes node01 disktype: bigSizedisk

* Once the pod is created the pod is placed on the desired node.
* For complex rules 'Node selector' is not enough, we need to configure 'Node Affinity' rules.

# Node Affinity

* Node Affinity is a mechanism that allows you to guide the Kubernetes scheduler in placing pods on specific nodes based on node labels.
* It offers a more expressive and flexible way to control pod placement compared to the nodeSelector.

- Types of Node Affinity:

- Node affinity comes in two main types, "during scheduling" and "ignored during execution":

1. **requiredDuringScheduling**IgnoredDuringExecution (Hard Affinity):

* This is a strict rule: the pod will only be scheduled on nodes that satisfy all the specified label matching conditions.
* If no matching node is found, the pod will remain in a **Pending state** until a suitable node becomes available.
* **IgnoredDuringExecution** means that if a node's labels change after the pod has been scheduled, the running pod is not affected. It will not be evicted or rescheduled.

2. **preferredDuringScheduling**IgnoredDuringExecution (Soft Affinity):

* This is a preference rather than a strict requirement.
* The scheduler will try to place the pod on nodes that satisfy the conditions, but if no such nodes are available, the pod can still be scheduled on other nodes.
* You can assign a weight to preferred rules, allowing you to prioritize certain preferences over others.
* Similar to hard affinity, **ignoredDuringExecution** means label changes after scheduling do not impact running pods.

* Add the affinity section under "spec.template.spec".

Example: 1

		affinity:
		        nodeAffinity:
		          requiredDuringSchedulingIgnoredDuringExecution:
		            nodeSelectorTerms:
		            - matchExpressions:
		              - key: color
		                operator: In
		                values:
		                - blue

<img width="480" height="563" alt="image" src="https://github.com/user-attachments/assets/67572977-9e92-435b-8db1-1ee72fd8280c" />

		kubectl get nodes --show-labels

Example: 2

		affinity:
		        nodeAffinity:
		          requiredDuringSchedulingIgnoredDuringExecution:
		            nodeSelectorTerms:
		            - matchExpressions:
		              - key: node-role.kubernetes.io/control-plane
		                operator: Exists

















# How kube-proxy and services work together | Kube-proxy working in kubernetes

* Ever tried to ping cluster-IP service. It will never work as it is exist in ETCD only.
* There is no "process" running on service IP, no compute used by this name. So how pods are connected to outer world?

Check how many rules present now

      iptables -t nat --list | wc -l

Create 1 deployment (3 replicas)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/ce41e0cd-e6aa-44a4-ad54-ff5bdf4315ca)

      kubectl expose deployment nginx-deployment --name nginx-svc

      kubectl get svc

      kubectl describe svc nginx-svc

Note: This service has 3 endpoints as we have mention 3 replicas of deployment it works as a round robin load-balancer, we hit the IP 10.109.78.189 it send traffic to Endpoint.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/f0711d94-6298-4685-ba71-46d2975ee18d)

Now, if we check the count of IP tables, it will increase from 139 to 159

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/2e53fe9c-845e-4112-bfb5-32ccbd0eca7e)

If we check the IP in netstat, there is no IP present

      netstat -tulpn | grep 10.109.78.189

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/db337728-f78f-4f8c-803c-907dfa5659c0)

above snap scenerio shows like this 

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/4734ffd7-6f21-43bb-89b3-0aa70e886ce6)

Note: Kube-Proxy continuesly monitors if there is any changes in control plane data, also kube-proxy runs as a daemon-set (working on all nodes) that is why as soon as we expose our pod kube-proxy makes an entry on IP-tables and the count increases to 159. 

Here we are focusing on service chain >> Chain KUBE-SVC-*
      
      iptables -t nat --list

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/9e522b42-f071-423e-8ba1-ef0521ffc98e)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/fa42ec5b-5970-4986-b117-f1dae11e7c77)

      iptables -t nat -L KUBE-SERVICES -n | column -t

So basically, if anybody hits the service IP iptable remove the service Ip and send traffic to the POD IP (Cluster IP) as endpoints and these entries are done on all node even on controller due to daemon set.


# Ingress

https://www.geeksforgeeks.org/what-is-kubernetes-ingress/

 “Ingress is an API object that manages external access to the services in a Cluster“. The role of Ingress is instead of Service, the request goes first to Ingress and it does the forwarding then to the Service.

 Why Ingress? - Kubernetes services does not have Load balancing capabilities so Kubernetes introduced Ingress and all the load balancing companies write their own Ingress controller. 

 If we create a Service of Load balancer mode the Cloud Provider will charge for each IP address.

 Note: If we create a Ingress yaml file called as Ingress resource it will not work without Ingress Controllers.

 Ingress controller is a Load balancer that we can choose as per our requirements. Suppose we want Nginx LB we have to create Nginx ingress controller and so on with F5, Ambasador, HA Proxy. 

 KeyPoints of Ingress:-
 1. Path based routing
 2. Host based routing
 3. SSL termination









**Node-Selector**

* nodeSelector is the simplest recommended form of node selection constraint. You can add the nodeSelector field to your Pod specification and specify the node labels you want the target node to have. Kubernetes only schedules the Pod onto nodes that have each of the labels you specify.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/260ace6d-ac36-4aa1-bd98-3f109c2e496b)
 
  **Draining a Kubernetes node**

  Draining: When performing maintenance, sometimes we may need to remove the node from the service. To do this we need to **drain** the node. Pods running on these node will gracefully terminated and reschedules on anohter node. 
 
  During this activity all our services should be able to continue to run even if we remove a node from service.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/a1f26c2f-9c1c-4d12-aec9-78dff59e2aba)

      kubectl drain <node-name>

      kubectl drain <node-name> --ignore-daemonsets

  Note: In above command, "--ignore-daemonsets" when draining a node we need to ignore daemonsets because of pod are tied to each other also some of the system created pods that were created during installatoins and they can't just moved from one node to another.

  Once the node is drained and we have finished our maintenence tasks, we want to run pods again on that node, we cannot specifically run the same pod again on the same node, this process done automatically.
  
      kubectl uncordon <node-name>

  Note: In above command we are instructing kubernetes that node is ready now to run pods.

  Practical:-

  We have 2 nodes

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/6b3c9119-9e55-42ba-ac8c-68b812f7662b)

      kubectl run nginx --image=nginx

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/d74f96f4-c4f5-4503-9490-c36481adc3c5)

vim deploy.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 8

    kubectl apply -f deploy.yml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/b00f8df1-c09c-4ca8-b8fa-44704ae4b726)

    ERROR:

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/9de38d85-aa71-43e1-947e-3d09f5b05d32)

    kubectl drain ip-192-168-21-155.ec2.internal --ignore-daemonsets --force

Note: --force will delete the pod without deployment because there is no deployment

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/ff777aa6-50b5-417c-acf6-7e0a74d2bacb)

  Again if I check for pods, I can see the pods with deployment are running on only one node.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/5aa676d8-50d7-4991-b0ca-bbcae0c97be9)

  * If we check for nodes, we can see that Scheduling is disabled on drained node.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/f55a974e-3e90-464d-8a21-32a310a47a5a)

  * After uncordon the node the staus is again ready

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/fa094469-ce7e-4a51-8f41-74db60dfc789)



====================================================================================

# Kubernetes Declarative vs imperative Commands

1. Impative method: Configuration defines directly on Command line and run against Kubernetes cluster.
2. Declarative method: Configuration defines through manfest files and then applies those definitions to the cluster.

Commands:

    kubectl run nginx --image=nginx

    kubectl run custom-nginx --image=nginx --port=8080
    
    kubectl create deployment my-deployment --image=nginx --replicas=4 -n new-namespace

See live configuration of pod, which k8s automatically creates

    kubectl create deployment my-deployment --image=nginx --dry-run=client -o yaml

Note: Dry-run will not run command and -o will give a sample yaml, we can re-direct the yaml file to a text or yaml file. Here "=client" is used as new method.

    kubectl create deployment my-deployment --image=nginx --dry-run=client -o yaml > newdep.yaml

    kubectl create deploy webapp image=nginx:latest --replicas=3

    kubectl run redis --image=redis:alpine --labels="tier=db"

    kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml

    k run custom-nginx --image=nginx --port=8080

    k create deployment webapp --image=kodekloud/webapp-color --replicas=3

    k create deployment redis-deploy -n dev-ns --image=redis --replicas=2

* create a service of type ClusterIP by the same name (httpd)

    k run httpd --image=httpd:alpine --port=80 --expose

    

    
====================================================================================

**Role Base Access Control (RBAC)**

* Role-based access control allows to control what users are allowed to do and access within the cluster. Example we can allow developer to read metadata and logs from pods but do not make any chaanges to them.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/124ba973-c744-41a7-8b3a-48dc6bf822fb)

* Understand, Roles and ClusterRoles are Kubernetes objects that define a set of permissions. These permissions determine what users can do in the cluster.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/4d7ec469-de5c-4324-9689-70011be396e1)

* A Role defines permissions within a particular namespace.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/b7484986-cce8-4577-b2da-59f37972801a)

* ClusterRole defines cluster-wide permissions not specific to a single namespace.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/9cee1520-0d1f-4514-9852-b7950aeacf65)

* Another two things we should aware of is, RoleBinding and ClusterRoleBinding are objects that connect Roles and ClusterRoles to users.

* RoleBinding are object that link users to roles.

  ![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/c5cd73ce-4019-4892-9d55-c1df3fb1cfc3)

* ClusterRoleBinding link users to ClusterRoles.

Practical:

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/4db452d1-639c-4d39-b4d2-4ec30d4f4924)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/24b21df8-2bf8-4a70-82c9-637a18a9ed68)

    kubectl apply -f role.yaml

    kubectl get role

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/4138fdce-212c-4fb1-8cf8-4ee33b2b09a2)

    vim rolebinding.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/1b438af4-cb1f-42fa-afc4-f2233d07d429)

    kubectl apply -f rolebinding.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/8a4e1822-ac24-4334-9829-17fc85f93456)

    kubectl auth can-i get pod --as jane

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/f90e681f-07dd-4a60-83ac-abc59d767ab1)

In the above snaps we can see the user jane is able to **get, watch or list** but unable to create or delete the pod in **default** namespace

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/3fc088ea-531a-43ea-af41-cc5f9853070e)

**PRACTICAL**

Create 2 instances, 1st for k8 cluster (minikube installed), 2nd for jump server

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/a049ee79-0ea7-4314-8cae-31ed8b871a45)


Install kubectl on jump server

      snap install kubectl --classic

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/5227d9eb-3a82-415d-8484-c0ea1585ba6f)

      root@jump-workstation:~# mkdir .kube

From master, copy content from /root/.kube and paste on jump server same location config file

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/38e82cb6-a88c-417d-abe0-35ba5b9b7d16)


**ClusterRole**

vim clusterrole.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/36897e6c-111a-4598-9cf0-d4e6975340c3)

    kubectl apply -f clusterrole.yaml

 * We will create another file called rolebinding.yaml in which we give the reference of above clusterrole so we can bind the secret with namespace.

vim cluster-rolebinding.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/176ebdb2-cb60-42eb-9e0b-c4d041736f96)

    kubectl apply -f cluster-rolebinding.yaml

* Verify

    kubectl auth can-i get secret --as dev -n development

====================================================================================

# Service Accounts

A service account is a type of non-human account . It is used authenticating to the API server or implementing identity-based security policies. Basically if pods need to communicate with kubernetes API, you can use Service Accounts to control their access.

Everytime a pod is created, kubernetes will assign a default service account.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/1abc24c2-0bac-4ec2-b4f2-7bc80a93a831)

To manage service accounts just like any other user, using RBAC. Bind service account with RoleBindings or ClusterRoleBindings to provide access to Kubernetes API functionality.

    kubectl create sa new-sa-account

    kubectl get sa -A

If want to see where the token has mounted of service account. Check under Mounts by doing 

kubectl describe sa <sa-name> 

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/faddf39d-af43-4f7e-a238-c4fa2134a87c)

    
    vim my-serviceaccount.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/df158d40-049e-4735-851c-d6a124c65a5f)

    kubectl apply -f my-serviceaccount.yaml

Create service account another way

    kubectl create serviceaccount my-service-account -n kube-system

    kubectl get sa

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/654435a9-aebe-419d-99a8-6a1f1b3536a6)

* Create a RoleBinding to bind with Service Account

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/cbb5da89-14d3-4f1a-928e-e60064a268fc)

====================================================================================

# Configmaps

It is an API object mainly used to store non-confidential data. Data stored in ConfigMap is stored as key-value pairs. ConfigMaps are configuration files that pods may use as command-line arguments, environment variables, or configuration files on a disc.

Commands: Imperative approach

	kubectl create configmap --help

# Create a file and put the below-listed variables into a file.

# APP_COLOR: blue
# APP_MODE: prod

	kubectl create configmap my-config --from-file=/root/map.txt
 	kubectl get configmap	
  	kubectl describe configmaps my-config

# Direct in command line
	
 	kubectl create cm my-con-1 --from-literal=Name=Sunny --from-literal=Sur=Valechha

Commands: Declarative approach

	apiVersion: v1
	kind: ConfigMap
	metadata:
    	  name: app-config-map
	data:
    	  APP_COLOR: blue
    	  APP_MODE: prod

       kubectl apply -f .yaml




# Secrets

Doc: https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/

A secret in Kubernetes can be defined as an object that contains a small quantity of sensitive data like a password, a token, or a key. It contains information that is otherwise stored in a container image or pod specification. The main advantage of a secret is that, we do not have to include sensitive or confidential data in the application code. There is less risk of losing or exposing confidential data during the workflow of creating viewing, and editing Pods because they can be created independently in the pods in which they are being used. Secretes can be considered similar to ConfigMaps but the main difference between them is that they are specially designed to store and hold confidential data.

Things to remember:
* Secrets can be used as a pod environment variable.
* Secrets are not encrypted, they are encoded.
* Secrets are not encrypted in ETCD, they are enables encryption at rest.
* Do not store Secrets / confidential information in source code manager (GitHub) along with code.
* Anyone have access to create a pods in a namespace can access the secrets in the same namespace. To prevent this configure least-privilege access to Secrets (RBAC).
* Consider third party secrets store providers like AWS, Azure, GCP, Terraform vault.
* An information is stored through API server in etcd and this information can read by anyone so secrets is used when a stored information has some sensitive data like token or passwords that can't be stored in etcd as anyone can read those critical information.
* With the secrets the data is first encrypted at REST than stored in etcd, but what is anyone can run "kubectl describe secrets" and get the information stored in Secrets.
* To prevent this kubernetes suggested to use strong RBAC. Ex: no-one have the access to the secrets this concecpt is called as 'least privilege' in kubernetes
  
**Types of Secrets**

When creating a Secret, you can specify its type using the type field of the Secret resource.

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/40e4a571-9435-43c3-a0fd-5d8c2ca23897)

**Opaque Secrets**

Opaque is the default Secret type if you don't specify a type in a Secret manifest. When you create a Secret using kubectl, you must use the **generic** subcommand to indicate an Opaque Secret type.

**Practical**

Imparative Way: 

    kubectl create secret generic <secret-name> --from-literal=<key>=<value>

    kubectl create secret generic app-secret --from-literal=DB_Host=mysql

    kubectl get secret

    kubectl describe secret

Create multiple secrets through --from-literal command multiple times

    kubectl create secret generic app-secret1 --from-literal=DB_Host=mysql --from-literal=DB2=Postgresql --from-literal=DB3=MariaDB

    kubectl create secret generic app-secret-1 --from-literal=Username=mysql --from-literal=Password=paswrd

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/064162b0-5d83-4f3e-b095-fb0304e55c16)

Note: Here, we are creating a manifest file and the Username and password are not base64 encoded so the format will not be accepted.

Wrong:

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/63171e4b-2017-4e5a-bee3-d00d706181e9)

Corrert:

    echo -n 'mysql' | base64

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/c35abb2d-e5e6-45da-9038-838982c6626c)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/cf49d7db-bc63-4f31-ba78-b6ded767d210)

Now the manifest file is correct and secret will be created

But this process have a drawbacks, below command and you can see the encoded value given to the secret.
 
    kubectl get secrets app-secret-1 -o yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/770964ae-835d-410e-b02c-22afdabff5ee)

The way we can encoded it can easily decoded also, check below

    echo 'bXlzcWw=' | base64 --decode; echo

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/c5a3b8ea-0243-4a75-8169-4799c1783aaa)


Two ways to inject a secrets with Pod defintion file:

A - Here pod created but status is Error because we have'nt created a secret

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/21991d32-caf8-4890-8114-9ea7f560d90a)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/d771d036-607b-425e-9146-b22db37aaf12)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/8411a741-f953-490c-bec8-ca553a30f62a)

B - Use "envFrom" parameter in manifest. Now create a secret

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/a83c8436-7f8c-417a-a30c-7de3a215fd0d)

    kubectl create secret generic app-secret-1 --from-literal=Username=mysql --from-literal=Password=paswrd

* Secrets can be see by another method, Go to document link attached on heading, we can see the password like mention in snap below

      ETCDCTL_API=3 etcdctl \
        --cacert=/etc/kubernetes/pki/etcd/ca.crt   \
       --cert=/etc/kubernetes/pki/etcd/server.crt \
       --key=/etc/kubernetes/pki/etcd/server.key  \
       get /registry/secrets/default/my-secret | hexdump -C

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/08ef4750-06b3-473f-941c-3c54398ad560)

Solution, How to encrypt the data at REST

# Encrypt data at REST

ps -aux | grep kube-api | grep "encryption-provider-config"		#If not output means no encryption enabled.

# Need to create manifest encryption file.

head -c 32 /dev/urandom | base64					# Generate a random key
dkPPQ/lM8Cyle9QX0kDUSOoU5bTPF/HUBG2oKL/VTb8= 

vim secretkey.yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: dkPPQ/lM8Cyle9QX0kDUSOoU5bTPF/HUBG2oKL/VTb8=
      - identity: {}
		
mkdir /etc/kubernetes/enc
mv secretkey.yaml /etc/kubernetes/enc/

vim /etc/kubernetes/manifests/kube-apiserver.yaml

- --encryption-provider-config=/etc/kubernetes/enc/secretkey.yaml		# Add the list in last

- name: enc																# Add this section at last under volumeMounts: section
  mountPath: /etc/kubernetes/enc
  readonly: true
  

  - name: enc												# Add in section under volumes: (below volumeMounts:)
    hostPath:
      path: /etc/kubernetes/enc
      type: DirectoryOrCreate

kubectl get pod									# After making above changes this command should give output(whatever pod or no pod)
crictl pods										# check pods with containerd (kube-apiserver-controlplane) this pod
ps aux | grep kube-api | grep encr				# --encryption-provider-config=/etc/kubernetes/enc/secretkey.yaml
kubectl create secret generic new-secret --from-literal=key2=topsecret	# Create new secret to see if encryption is fine.
ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key get /registry/secrets/default/new-secret | hexdump -C # Validate, change secret name





=======================================================================================================
# Volumes in Kubernetes, Persistent Volume / Persistent Volume Claim

* EmptyDir: When 2 containers in a pod shares a same storage within a pod, this concept is called EmptyDir.
* We will create a EBS volume within the AWS and share volume with multiple worker nodes and this concept called as as Persistent Volume. This acts like a NFS (a shared storage)
* From the EBS volume we will create small parts of volume these small chunks called as PV or PV objects.
* This PV is created through a manifest file where we will claim the volume which required as in 3rd point. In order to use PV we need to claim it first. 
* So even if the pod is deleted, the storage will be persist same as disk in linux will persist even if we delete the folder.

  Note: Create an EBS volume in same AZ where instance is created.

vim pv.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/030c56d0-844a-46a9-b675-0bf5724e6542)

kubectl apply -f pv.yaml

kubectl get pv

To claim that volume we need to create PVC

vim pv-claim.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/eb56539e-ebee-46ef-a56f-71328088931c)

kubectl apply -f pv-claim.yaml

kubectl get pvc

To use above pvc in a pod

vim pod-pvc.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/8c2abf37-6fe8-4a7e-b13c-1a543aabd6eb)

kubectl apply -f pvc.yaml

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/b20d52d9-9283-4e3e-86eb-d2b0074edebf)

Go inside the pod / container

kubectl exec <pod-name> -it -- /bin/bash

cd /tmp/persistent (directory created at this location)

* Create file with random content

To test the scenerio

kubectl delete pod <pod-name>

Note: New pod must created as part of our deployment

Go inside the new pod, check if the "/tmp/persistent" path must be present in the new created pod also.

=======================================================================================================================




* Whenver we login to cluster the information stored at kube config file.

      cat ~/.kube/config



# Daemonsets and Stateful sets

* A DaemonSet ensures that on all nodes run a copy of a Pod.
* As nodes are added to the cluster, copy of Pod are added to them.
* As nodes are removed from the cluster, that copy should be deleted.
* Deleting a DaemonSet will clean up the Pods it created.
* We can only create daemonset through yaml file, there is no Imparitive approach to create DS.
* We don't need to mention Replicas count as daemonset automatically create a copy of pod on every node automatically and once deleted the pod is removed from the cluster.

- Some typical uses of a DaemonSet are:

* running a cluster storage daemon on every node
* running a logs collection daemon on every node
* running a node monitoring daemon on every node

Run a daemonset: Take DS example from K8 document, create a daemonset yaml file and run a pod

		kubectl get daemonset
		kubectl describe daemonset

* An easy way to create a DaemonSet is to start by generating a Deployment YAML using:

		kubectl create deployment elasticsearch --image=registry.k8s.io/fluentd-elasticsearch:1.20 -n kube-system --dry-run=client -o yaml > fluentd.yaml

* Yaml Example:

		apiVersion: apps/v1
		kind: DaemonSet
		metadata:
		  creationTimestamp: null
		  labels:
		    app: elasticsearch
		  name: elasticsearch
		  namespace: kube-system
		spec:
		  selector:
		    matchLabels:
		      app: elasticsearch
		  template:
		    metadata:
		      creationTimestamp: null
		      labels:
		        app: elasticsearch
		    spec:
		      containers:
		      - image: registry.k8s.io/fluentd-elasticsearch:1.20
		        name: fluentd-elasticsearch
		        resources: {}

* StatefulSet is the controller that manages the deployment and scaling of a set of Stateful pods.
* A stateful pod in Kubernetes is a pod that requires persistent storage and a stable network identity to maintain its state all the time, even during pod restarts or rescheduling.
* These pods are commonly used for stateful applications such as databases or distributed file systems as these require a stable identity and persistent storage to maintain data consistency.


# Pause Containers

The “pause container” is a special, internal container created and managed by Kubernetes within each pod. Its primary purpose is to provide network namespace and IPC (Inter-Process Communication) namespace for all other containers within the same pod.

**Practical:-**

* Run the test pod (nginx)
* Check the Ip of pod by running "-o wide" command
* Get the Ip, ssh to the node and run "docker ps -a | grep -i boxtest" you will see there is pause container created just before the the main container.

Kubernetes continuesly monitors this container and if K8 does not find the pause container then k8 will assign new Ip to the Pod. Kubernetes monitors the Pod with the help of Pause containers.

* Manually stop the pause container on worker node then check on master the restart must be 1 and IP will be changed. So we can observe that new container and pause container also created.
  
![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/a6a28c32-efdd-46da-a902-d709faea007c)

# Taints and Tolerations

- Kubernetes schedules workloads across nodes based on resource availability and constraints. But what if you want to prevent certain workloads from running on specific nodes? That’s where Taints and Tolerations comes in.

* Taints are applied to nodes to hold-off specific pods unless those pods have a matching toleration.
* Tolerations are applied to pods, allowing them to bypass taints and run on tainted nodes.
* Labels v/s Taints: Labels are used to attract/schedule pods to certain nodes and taints are used to repel pods away from certain nodes.

- Kubernetes supports three taint effects:
1. NoSchedule → Pod will not scheduled on the node unless they have a matching toleration.
2. PreferNoSchedule → System will try to avoid placing a pod on a node, but does not gaurantee it.
3. NoExecute → Immediately evicts existing pods unless they have a matching toleration.

Imparitive command - taints (Node level):

		kubectl describe node node01 | grep Taints*			# check if any taints exists on a node.
		kubectl taint nodes node-name key=value:taint-effect
		kubectl taint nodes node01 app=blue:NoSchedule
		kubectl taint node node01 spray=mortein:NoSchedule

- Note: Notice, that we also have a master node and a taint is automatically set on the master node so that it will not host any pod. We can change this behavior.

Yaml:

		apiVersion: v1
		kind: Pod
		metadata:
		  name: bee
		spec:
		  containers:
		  - image: nginx
		    name: bee
		  tolerations:
		  - key: "spray"
		    value: "mortein"
		    effect: NoSchedule
		    operator: "Equal"

Note: We have a same taint and tolerations on both node and Pod, hence the pod is scheduled on node01

<img width="816" height="441" alt="image" src="https://github.com/user-attachments/assets/3b0d26b7-336a-433a-a407-1621a8bfb552" />

- Un-taint the node:

		kubectl taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-		# "just add '-' at last to untaint, otherwise same command for taint & un-taint"

# Kube-bench (Security and compliance)

  
# Fluentd / fluent-bit / logstash (Cluster log collection)


# Manually schedule a Pod on a node

* Usually when we run a pod definition file a scheduler automatically find a suitable node and and run a pod onto that node.
* What happens if there is no Scheduler available?
* So if we run a pod it will go into Pending state.
* Every pod has a feild called "Node Name" that by default is not set, usually we don't specify this feild when we create a yaml file.
* We can specify the node in the yaml file at the time of creating a pod file but what if the pod is already created.
* One way to assign a node to an existing pod is to create a binding object and send a POST request to the Pod's binding API




# Validate Kubernetes YAML with Polaris

# K8 Backup & Restore on S3 Bucket using Velero


# Secure K8 Cluster

Top 7 Priorities:

1. Secure your API server
2. RBAC
3. Network Policies
4. Encrypted at REST (ETCD)
5. Use secure container images
6. Cluster monitoring
7. Frequent Upgrades


# Custom Resource Definition

* Kubernetes provides a powerful API for managing resources like Pods, Deployments, and Services, but what if you need to manage resources that Kubernetes doesn’t inherently support? This is where Custom Resource Definitions (CRDs) come in. CRDs allow you to define your own custom resources, extending the Kubernetes API to fit your specific needs.

* So, to extend the capabilities of the Kubernetes API, Kubernetes has 3 resources:
1. CRD	- Custom resource definition	--> Define a new type of API for Kubernetes via yaml file 
2. CR	- Custom resource		--> 
3. Custom controller

* Example of the Kubernetes API:
1. Istio Mesh
2. ArgoCD

**Here are some reasons why you might want to use CRDs:**

1. Abstraction: CRDs allow you to abstract away complex logic into custom resources, making it easier to manage and interact with your applications.
2. Consistency: By defining custom resources, you ensure that your applications are consistently managed across different environments.
3. Automation: CRDs enable automation by allowing you to define custom controllers that can watch and reconcile the state of custom resources.

<img width="1365" height="617" alt="image" src="https://github.com/user-attachments/assets/1f534399-9b9f-4027-80b0-3142221d71a2" />

Note: To create a custom resource in Kubernetes we must know how to write code in GoLang.


# Editing Pods and Deployments

- Remember, you CANNOT edit specifications of an existing POD other than the below.

* spec.containers.image
* spec.initContainers.image
* spec.activeDeadlineSeconds
* spec.tolerations

- You cannot edit the environment variables, service accounts, resource limits of a running pod.
- But if you really want to, you have 2 options:
  1. Run the "kubectl edit pod <pod name>" command.  This will open the pod specification in an vi editor. Then edit the required properties. When you try to save it, you will be denied. This is because you are attempting to edit a field on the pod that is not editable.

<img width="770" height="670" alt="image" src="https://github.com/user-attachments/assets/c87500f1-b201-46cf-8cbe-733abf3bc8d4" />

<img width="660" height="115" alt="image" src="https://github.com/user-attachments/assets/759cbb3e-cbe1-4583-9ef6-031fe34fcef6" />

* You can then delete the existing pod by running the command:

		kubectl delete pod webapp

* Then create a new pod with your changes using the temporary file

		kubectl create -f /tmp/kubectl-edit-ccvrq.yaml

2. The second option is to extract the pod definition in YAML format to a file using the command

		kubectl get pod webapp -o yaml > my-new-pod.yaml

* Then make the changes to the exported file using vi editor. Save the changes

		vi my-new-pod.yaml

* Then delete the existing pod

		kubectl delete pod webapp

* Then create a new pod with the edited file

		kubectl create -f my-new-pod.yaml


- Edit Deployments:

* With Deployments you can easily edit any field/property of the POD template.
* Since the pod template is a child of the deployment specification, with every change the deployment will automatically delete and create a new pod with the new changes.
* So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command

		kubectl edit deployment my-deployment

# Static Pods

- Static Pods in Kubernetes are a unique type of Pod that are managed directly by the Kubelet daemon on a specific node, rather than by the Kubernetes control plane (API server, scheduler, etc.).
- This means they are not observed or controlled by the API server in the same way as regular Pods managed by Deployments or ReplicaSets.

Key characteristics of Static Pods:

* Direct Kubelet Management: The Kubelet on a node is responsible for creating, starting, and restarting static Pods based on manifest files placed in a configured directory "/etc/kubernetes/manifests/".
* Node-Specific: Static Pods are always bound to a particular Kubelet on a specific node and cannot be scheduled across the cluster by the Kubernetes scheduler.


# Priority classes

* A PriorityClass in Kubernetes is a cluster-scoped (non-namespaced) object that allows you to define a mapping between a priority class name and an integer value representing the priority.
* This mechanism is crucial for managing workload scheduling and resource allocation within a Kubernetes cluster.
* Range: The value can be anything, ranging from ( 1 billion to '- 2 billion' ) -2,147,483,648 to 1,000,000,000
* Higher value, higher priority: Pods with a higher PriorityClass value are considered more important and will be favored by the Kubernetes scheduler during resource allocation.

Defining a priority class:

		    apiVersion: scheduling.k8s.io/v1
		    kind: PriorityClass
		    metadata:
		      name: high-priority
		    value: 1000000
		    description: "This priority class should be used for critical application components."
	  		preemptionPolicy: never # means do no kill old pods only apply on new pods/workloads

Assigning Priority to Pods:

		    apiVersion: v1
		    kind: Pod
		    metadata:
		      name: my-critical-app
		    spec:
		      containers:
		      - name: my-container
		        image: my-image:latest
		      priorityClassName: high-priority

- Scheduling and Preemption:

* Scheduling Order: When the Kubernetes scheduler needs to place Pods on nodes, it prioritizes Pods with higher priorityClassName values.
* Higher priority Pods will be scheduled before lower priority ones if resources are available.

* Preemption: If a cluster is under resource pressure and a higher-priority Pod needs to be scheduled but no node has sufficient resources.
* Kubernetes can preempt (evict) lower-priority Pods from nodes to free up resources for the higher-priority Pod.
* The preemptionPolicy field within a PriorityClass can be used to control this behavior (e.g., PreemptLowerPriority or Never).

		kubectl get pc		# show default priority classes which are part of the k8 cluster

		kubectl describe pc system-node-critical	# see the value for any preemption policy

		kubectl create pc high-priority -o yaml > hp.yml		# create new priority class then keep the required parameters

* You can compare the priority classes on all pods using the following command:

  		kubectl get pods -o custom-columns="NAME:.metadata.name,PRIORITY:.spec.priorityClassName"



