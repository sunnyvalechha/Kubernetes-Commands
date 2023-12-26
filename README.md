Topics:

* Introduction and Architecture of Kubernetes
* Difference between monolithic and microservice architecture.
* Controllers and worker nodes their keypoints and their working pattern.
* What is Pod and how it works.
* Namespaces.
* Deployments
* Replicasets
* Imparative and Declaritive commands.
* Labels and Selectors
* Cluster maintainence Drain, Cordon and Uncordon
* Taints and Tolerations
* Role Base Access Control (RBAC)
* Kubernetes Services
* Kubernetes Secrets
* Volumes, Persistent volume, Persistent volumee claim
* Backup and Restore
* Daemonsets
* Config maps
* Enviroment Variable in Kubernetes
* Security and Certificate details


# Introduction and Architecture of Kubernetes

Kubernetes was designed by Google and managed by the Cloud Native Computing Foundation (CNCF).

Kubernetes helps us to create mini/microservices applications while in docker we create monolithic applicatons.


# Kubernetes Cluster / Architecture

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/84a5c531-1a7e-45f6-803f-3318aebf1629)

Below are the major components of Kubernetes and what they do:

**Master Node / Controller**

A master node is where all the major processes are run and manage the cluster. There can be more than one master node for high availability.

Key Features:

* **API Server:** Entry point to Kubernetes cluster for the user interface, API, and CLI

* **Controller Manager:** Tracks and manages the containers in the cluster
  
* **Scheduler:** Determines which worker nodes will be used when based on the application being scheduled
  
* **Etcd:** A key-value store that contains the state of the cluster

**Worker Node**
  
* Runs the containers (inside the pods)

Key Features:
  
  **Kubelet:**
  
 * Primary Kubernetes ‘agent’ that runs on each node
    
 * Communicates to the Master via API server

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

  **Pods**
   
  * Pod is a collection of containers that can run on a host. This resource is created by clients and scheduled onto hosts.
    
  * Pods are ephimeral in nature, means the same pod cannot redeployed once die, but another pod will be up with the same configurations.

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
  
Note: In above comnand, apply means create and run

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

- Instead of writing command on CLI write a YAML/manifest file that is enterprise level feature.
  
- But, In a production enviroment it is not suggested to create a pod directly, instead we create a **Deployment**.
  
- A **deployment** will create a Replicaset.
  
- A **replicaset** will create a Pod.

**Deployment:** It is a object, by which we can manage the scaling of the application while maintaining desired state and actual state of the pods, we can scale up and down of the pods.
Basically, kubernetes deployment has enterprise level feature called as Auto-healing, Auto-scaling and Zero-downtime.

**Replicaset:**   It is a default controller in kubernetes. It ensures that a specified number of pod replicas are running at any given time. It is used to automatically replace any pods that fail, deleted, or terminated.
The term Replica set is replaced by Replication controller, both can be used but new and updated term is Replicaset


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

# Service

* Whenever a service is created an endpoint is automatically created by the same name.

      kubectl get endpoints

      kubectl describe service/<service-name>

  





====================================================================================

**Kubernetes Declarative vs imperative Commands**

1. Impative method: Configuration defines directly on Command line and run against Kubernetes cluster.
2. Declarative method: Configuration defines through manfest files and then applies those definitions to the cluster.








**Introduction to ETCD**

ETCD is a consistent and distributed key-value store used for discovering services.

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

**Introduction to Kubeapi server**

The Kubernetes API server validates and configures data for the api objects like pods, services, replicationcontrollers, and others. 

**Controller Manager**

Controller manager have two types:

1. Kube-controller manager

2. Cloud-controller manager

- Controller managers are responsible for defining and maintaining the desired state of a cluster.
- Controller managers ensures that the system remains in the desired state.
- Controller managers monitor and respond to the state and health of the Pods, ensuring that the desired number of pod are up, running and healthy.
- If the state of health changes, the controller will respond accordingly.

**Kubernetes Scheduler**

- It is a component of master node which is responsible for scheduling tasks for the worker nodes and storing resources information of each node.
- When pod is created, the scheduler finds the best node to run the pod.

Two operations of kube-scheduler:

1. Filtering
2. Scoring

* Filtering based upon available CPU and RAM.
* When two nodes having same resources then scoring comes into the picture.

**Kubelet**
- Every kubelet is talking to kube-api server.
- Kubelet is an agent which runs on each node ensures that all containers are running.
- Kubelet uses API server to get the configurations of the pod and ensures the containers described are up and running.

  To check if the kubelet is running:-

  systemctl status kubelet

  

  





  















