**Command auto completion**

source <(kubectl completion bash)

echo "source <(kubectl completion bash)" >> ~/.bashrc

alias k=kubectl

complete -o default -F __start_kubectl k

**Kubectl**: Kubectl is a command line tool to run kubernetes commands. 

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

**Man page of pod:** kubectl explain pod

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/5be73522-ff1b-449d-bb74-1efa52f5aa0f)

**Note:** Container does not have any IP address but Pod have.

kubectl get pods -o wide

![image](https://github.com/sunnyvalechha/Kubernetes-Commands/assets/59471885/7717a09b-bd0f-4b75-8bad-9cdd2ebc5e02)





**Kubernetes Declarative vs imperative Commands**





1. Impative method: Configuration defines directly on Command line and run against Kubernetes cluster.
2. Declarative method: Configuration defines through manfest files and then applies those definitions to the cluster.





