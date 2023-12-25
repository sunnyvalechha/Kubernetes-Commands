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

  **Kubectl**: Kubectl is a command line tool to run kubernetes commands. 

  **Kubernetes Objects**

  - Kubernetes uses objects to represent the state of the cluster.

  List of kubernetes objects are:

  1. Pod
  2. Service
  3. Namespace
  4. Volume
  5. Replicasets
  6. Secrets
  7. Config maps
  8. Deployments
  9. Jobs
  10. Daemonsets

**Command auto completion**

source <(kubectl completion bash)

echo "source <(kubectl completion bash)" >> ~/.bashrc

alias k=kubectl

complete -o default -F __start_kubectl k

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

**Delete pods by two ways**

kubectl delete -f demo.yaml -n demo-k8-namespace

kubectl delete pod nginx

**Pods**

Pod is a collection of containers that can run on a host. This resource is created by clients and scheduled onto hosts.

Pods are ephimeral in nature, means the same pod cannot redeployed once die, but another pod will be up with the same configurations.

**Man page of pod:** kubectl explain pod

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/5be73522-ff1b-449d-bb74-1efa52f5aa0f)

**Note:** Container does not have any IP address but Pods have.

kubectl get pods -o wide

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/7717a09b-bd0f-4b75-8bad-9cdd2ebc5e02)

**Check logs**

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

**View the logs for all containers in a pod**

kubectl logs <pod_name> --all-containers

* How kubernetes works in production enviroment?

- To create a container we write a command.

  Example: docker run -it httpd /bin/bash

- Instead of writing command on CLI write a YAML/manifest file that is enterprise level feature


**Kubernetes Declarative vs imperative Commands**





1. Impative method: Configuration defines directly on Command line and run against Kubernetes cluster.
2. Declarative method: Configuration defines through manfest files and then applies those definitions to the cluster.





