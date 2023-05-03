AKS Cluster Creation 
--------------------
* create a resource group
* create AKS cluster
* connect to cluster by installing `kubectl`
* config `kubectl`
```
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --node-count 1 --enable-addons monitoring --enable-msi-auth-for-monitoring  --generate-ssh-keys

az aks install-cli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Topics In Kubernetes
--------------------
1. History and Architecture
2. kubernetes Installation
   1. kubeadm
   2. minikube
   3. kind
3. objects, api-resoureces & work loads
4. Pods
   1. pods creation 
      1. imperative
      2. declarative - manifest file
   2. pod spec & restart policy
5. Controllers 
6. pod spec
7. jobs and cron jobs
8. replics set spec
9. replication controller
10. labels and selector
11. kubernetes as a service - AKS , EKS
    1.  AKS cluster Creation
12. service spec
    1.  Internal
    2.  External
13. Health checks/probes
    1.  liveness probe
    2.  readiness probe
    3.  startuo probe
14. Resource Mgmt - Pods and Containers
    1.  Requests and limits -CPU/Memory
15. Container types in Pods
    1.  Containers
    2.  init containers
    3.  ephemeral containers
16. Deployment
    1.  rolling out
    2.  rolling back
17. Annotations
    1.  change cause
18. Daemon set
19. Scheduling Pods
    1.  node selector
    2.  affinity
    3.  taints and tolerations
20. Headless service
21. Storage in k8s
    1.  volumes 
22. stateful sets

