Topics:

* Introduction and Architecture of Kubernetes
* Difference between monolithic and microservice architecture.
* Controllers and worker nodes their keypoints and their working pattern.
* What is Pod and how it works.
* Namespaces.
* Deployments
* Replicasets
* Kubernetes Services
* Cluster maintainence Drain, Cordon and Uncordon
* Imparative and Declaritive commands.
* Role Base Access Control (RBAC)
* Service Accounts
* Labels and Selectors
* Taints and Tolerations
* Kubernetes Secrets
* Volumes, Persistent volume, Persistent volume claim
* Backup and Restore
* Daemonsets
* Config maps
* Enviroment Variable in Kubernetes
* Security and Certificate details


# Introduction and Architecture of Kubernetes

Kubernetes was designed by Google and managed by the Cloud Native Computing Foundation (CNCF).

Kubernetes helps us to create mini/microservices applications while in docker we create monolithic applicatons.

# Docker vs Container D

* At the begining of container Era, there were only tool to run container is Docker. Rkt is there but docker's user experience is much better than rkt. Then kubernetes came to orchestrate Docker and did'nt support any other container solutions. 
* Kubernetes grew in popularity and other container runtime wanted to work with kubernetes.
* So Kubernetes introduce and Interface called **Container Runtime Interface** (CRI).
* CRI allows any vendor to work as a container runtime for Kubernetes.
* Only thing they have to maintain the OCI standards
* OCI stands for **Open container Initiative** and consist of ImageSpec and RuntimeSpec.
* Imagespec means specifications how and image should be built.
* Runtimespec is define the standards how any container runtime should be developed.
* So keeping these standards in mind that can be used by anyone to work with Kubernetes, however Docker was'nt built to support the CRI standards because it was present way before than CRI introduced.
* So Kubernetes introduced Dockershim for temporary basis.
* Other container runtimes worked through the CRI but Docker continue to work without it as Docker consist multiple tools put together called Docker CLI, volumes, auths, security also the container runtime called runC and the daemon managed runC called ContainerD.
* So the ContainerD is CRI compatible and can work directly with Kuberntes as other runtimes do.


# Kubernetes Cluster / Architecture

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/91777215-f712-474f-9ff8-82b13fe2588f)

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

Primary management component in Kubernetes. When we run "kubectl" command it's first reach to Kube-APIserver. Kube-APIserver first authenticate the request and validates it, then it retreives the data from ETCD cluster and respond back. The Scheduler continuesly monitor the API server and realize that there is new pod with no node assigned. The Scheduler identifies the right node to place the new pod and communicates back to the Kube-API server. The API server then updates the information in ETCD cluster. API server passes the information to kubelet on worker node, the kubelet then creates the pod on worker node and instruct the Container Runtime Engine to deploy the application image. Once done, the kubelet updates the status back to the API server, API server updates the data back to the ETCD cluster.

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

- It is a component of master node which is responsible for scheduling tasks for the worker nodes and storing resources information of each node.
- When pod is created, the scheduler finds the best node to run the pod.

Two operations of kube-scheduler:

1. Filtering
2. Scoring

* Filtering based upon available CPU and RAM.
* When two nodes having same resources then scoring comes into the picture.

# Kubelet
- Every kubelet is talking to kube-api server.
- Kubelet is an agent which runs on each node ensures that all containers are running.
- Kubelet uses API server to get the configurations of the pod and ensures the containers described are up and running.

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

Document used by Densify: https://www.densify.com/kubernetes-autoscaling/kubernetes-service-load-balancer/

* In the World of kubernetes the pods are created through replicaset via deployment and the pods can be easily dead and non operation that is fine, but the IP address of the pod will be changed every time when the pod is dead, that is the Issue kubernetes services are resolving.

* On top of deployemnt Service will be created. So instead of accessing pod directly the request will re-direct to the Service and acts as a **Load Balancer** because it uses a component called as kube-proxy.

* Here, service will directly connected to pods but there might a scnerio where service also faced the same problem because the pod Ip can be changed. So service also resolving this issue via **service discovery** by using **Labels and Selectors**.

* Another thing that service offers is **Expose the pod to external world**.

The Short name of service is "svc"

* Whenever a service is created an endpoint is automatically created by the same name.

      kubectl describe service/<service-name>

* Types of Services:-
  
  1. ClusterIP: This is the default service, If we do not mention any type in manifest file so ClusterIP automatically selected. We cannot use the IP outside the cluster when used as ClusterIP basically it is accessible within the cluster. Dependent applications can interact with other applications internally using the ClusterIP service.

  2. NodePort: NodePort services are accessible outside the cluster. It creates a mapping of pods to its hosting node/machine on a static port. For example, you have a node with IP address 10.0.0.20 and a Redis pod running under it. NodePort will expose 10.0.0.20:30038, assuming the port exposed is 30038, which you can then access outside the Kubernetes cluster.

  3. Load Balancer: This service type creates load balancers in various Cloud providers like AWS, GCP, Azure, etc., to expose our application to the Internet. The Cloud provider will provide a mechanism for routing the traffic to the services. The most common example usage of this type is for a website or a web app.
 
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
    
    kubectl create deployment my-deployment --image=nginx 

    kubectl create deployment my-deployment --image=nginx --dry-run -o yaml

Note: Dry-run will not run command and -o will give a sample yaml, we can re-direct the yaml file to a text or yaml file. Here "=client" is used as new method.

    kubectl create deployment my-deployment --image=nginx --dry-run=client -o yaml > newdep.yaml

    
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

# Configmaps and Secrets

A ConfigMap is a dictionary of configuration settings. This dictionary consists of key-value pairs of strings. Kubernetes provides these values to your containers. Use a ConfigMap to keep your application code separate from your configuration. A ConfigMap stores configuration settings for your code. Store connection strings, public credentials, hostnames, and URLs in your ConfigMap.

An information is stored through API server in etcd and this information can read by anyone so secrets is used when a store information has some sensitive information like token or passwords that can't be stored in etcd as anyone can read those critical information.

With the secrets the data is first encrypted at REST than stored in etcd, but what is anyone can run "kubectl describe secrets" and get the information stored in Secrets.

To prevent this kubernetes suggested to use strong RBAC. Ex: no-one have the access to the secrets this concecpt is called as 'least privilege' in kubernetes

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







  

  





  















