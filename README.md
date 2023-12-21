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

  







**Kubernetes Declarative vs imperative Commands**





1. Impative method: Configuration defines directly on Command line and run against Kubernetes cluster.
2. Declarative method: Configuration defines through manfest files and then applies those definitions to the cluster.





