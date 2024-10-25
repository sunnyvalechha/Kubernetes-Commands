# Topics

* Introduction and Architecture of Kubernetes
* Difference between monolithic and microservice architecture.
* Controllers and worker nodes are the key points of their working pattern.
* What is Pod and how it works?
* Namespaces.
* Deployments
* Replicasets
* Kubernetes Services
* Ingress
* Cluster maintenance Drain, Cordon, and Uncordon
* Imparative and declarative commands.
* Role Base Access Control (RBAC)
* Service Accounts
* Labels and Selectors
* Taints and Tolerations
* Volumes, Persistent volume, Persistent volume claim
* Backup and Restore
* Daemonsets
* Environment Variable in Kubernetes with Config maps and Secrets
* Security and Certificate details
* Pause Containers


# Introduction and Architecture of Kubernetes

Kubernetes was designed by Google and managed by the Cloud Native Computing Foundation (CNCF).

Kubernetes helps us to create mini/microservices applications while in docker we create monolithic applications.

# Docker vs Container D

* At the beginning of the container Era, the only tool for running containers was Docker. Rkt is there, but Docker's user experience is much better than Rkt's. Then Kubernetes came to orchestrate Docker and didn't support any other container solutions. 
* Kubernetes grew in popularity and other container runtimes wanted to work with Kubernetes.
* So Kubernetes introduce and Interface called **Container Runtime Interface** (CRI).
* CRI allows any vendor to work as a container runtime for Kubernetes.
* The only thing they have to maintain the OCI standards
* OCI stands for **Open container Initiative** and consists of ImageSpec and RuntimeSpec.
* Imagespec means specifications of how and image should be built.
* Runtimespec defines the standards of how any container runtime should be developed.
* So keeping these standards in mind that can be used by anyone to work with Kubernetes, however Docker was'nt built to support the CRI standards because it was present way before than CRI introduced.
* So Kubernetes introduced Dockershim for temporary basis.
* Other container runtimes worked through the CRI but Docker continue to work without it as Docker consist multiple tools put together called Docker CLI, volumes, auths, security also the container runtime called runC and the daemon managed runC called ContainerD.
* So the ContainerD is CRI compatible and can work directly with Kuberntes as other runtimes do.


# Kubernetes Cluster / Architecture

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/418356f8-bf7f-4d67-adb3-36b7c2b8ff0e)

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

Command:

*  k scale replicaset new-replica-set --replicas=5
*  

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




**Labels and Selectors**

* Labels are key and value pair
* Labels are used to define the object, the object can be pod or nodes.
* Selectors are used to identify the labels.
* Labels must be placed under metadata

    kubectl get pods --show-labels

Imparitive way

    kubectl label pod test-abels level=mid

List pod which match label

    kubectl get pods -l Company=amdocs

    kubectl get pods -l Company=IBM --overwrite

    kubectl get pods -l Company!=amdocs   >> Not Found

    kubectl delete pods -l Company!=amdocs  >> delete pod which does not match label
    
To check the Selectors run below command

    kubectl get pods --selectors app=App1

Note: In Replicaset or Deployments labels are defined at 2 places under metadata, one at top and one at bottom under template section 

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/303c4c00-0f37-417e-abea-f46ee1f0c01f)

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/d4786122-23a9-440e-8f9b-7801febc7ed3)

Currently 2 type of selectors are present.
* Equlity based - which shows labels equal to or not equal to 
* Set based  - where have to select between multiple labels

  in, notin, exist
  
  example: "env in (development, prod, dev)", "env notin (development, prod, dev)"

**Annotations**

Annotations are used to provide contextual information. Labels are for kubernetes while Annotations are for Humans.
Annotations are used for non-identifying information.

    kubectl annotate pod nginx "app=nginx-web-server"

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/dadd320b-9b82-4a46-b3b4-f54872d0e5a0)




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

A DaemonSet ensures that on all nodes run a copy of a Pod. As nodes are added to the cluster, copy of Pod are added to them. As nodes are removed from the cluster, that copy should be deleted. Deleting a DaemonSet will clean up the Pods it created. 

We can only create daemonset through yaml file, there is no seprate command of Imparitive approach to create DS.

We do not need to mention Replicas count here as daemonset automatically create a copy of pod on every node automatically and delete once the node is removed from the cluster.

Some typical uses of a DaemonSet are:

* running a cluster storage daemon on every node
* running a logs collection daemon on every node
* running a node monitoring daemon on every node

Run a daemonset: Take DS example from K8 document, create a daemonset yaml file and run a pod

* kubectl get daemonset
* kubectl describe daemonset


StatefulSet is the controller that manages the deployment and scaling of a set of Stateful pods. A stateful pod in Kubernetes is a pod that requires persistent storage and a stable network identity to maintain its state all the time, even during pod restarts or rescheduling. These pods are commonly used for stateful applications such as databases or distributed file systems as these require a stable identity and persistent storage to maintain data consistency.


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



# Kube-bench (Security and compliance)

  
# Fluentd / fluent-bit / logstash (Cluster log collection)


# Manually schedule a Pod on a node

* Usually when we run a pod definition file a scheduler automatically find a suitable node and and run a pod onto that node.
* What happens if there is no Scheduler available?
* So if we run a pod it will go into Pending state.
* Every pod has a feild called "Node Name" that by default is not set, usually we don't specify this feild when we create a yaml file.
* We can specify the node in the yaml file at the time of creating a pod file but what if the pod is already created.
* One way to assign a node to an existing pod is to create a binding object and send a POST request to the Pod's binding API
















