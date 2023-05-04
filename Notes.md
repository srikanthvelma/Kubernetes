Kubernetes Concepts Preparation
-------------------------------
### Need for k8s
* High Avalbility (HA)
  * When we run applications in docker containers,if the container fails,
    we need to start manually
  * if node itself fails, all containers running on that m/c shuld be recreated on another machins
  * the above can achieved by k8s
* Auto Scaling
  * container scaling both horizontal and vertical can be done by k8s
* Zero Down time deployments
  * k8s can handle `Zero down time deployments`, `rollout & roll back`
* k8s is described as `production grade Container management`
### History
* Google had history of running applications on containers
* to handle containers they developed mgmt tools -`Borg` & `Omega`
* google has started project called kubernetes nad then handed over to `CNCF` 
* `CNCF` - `Cloud Native Container Foundation`
### Terms
* **Distributed System**:- distributed system is a collection of independent components located on different machines that share messages with each other in order to achieve common goals.
* **Node**:- A Node is a worker machine in Kubernetes and may be either a virtual or a physical machine, depending on the cluster, Each Node is managed by the control plane. A Node can have multiple pods, and the Kubernetes control plane automatically handles scheduling the pods across the Nodes in the cluster. 
* **Cluster**:- A Kubernetes (K8s) cluster is a grouping of nodes that run containerized apps in an efficient, automated, distributed, and scalable manner. K8s clusters allow engineers to orchestrate and monitor containers across multiple physical, virtual, and cloud servers.
* **State**:- There are three possible container states: Waiting , Running , and Terminated . To check the state of a Pod's containers, you can use kubectl describe pod <name-of-pod> 
* **Stateful Applications**:- A stateful app is a program that saves client data from the activities of one session for use in the next session. The data that is saved is called the application’s state. here the client data will be stored in locally
* **Stateless Applicaions**:- A stateless app will store its client data in other server like db server
* **Monolith**:- a monolithic application describes a single-tiered software application in which the user interface and data access code are combined into a single program from a single platform. A monolithic application is self-contained and independent from other computing applications.
* **Microservices**:- microservices architecture is a collection of smaller, independently deployable services.
* **Desired State**:-
* **Declarative vs Imperative**:-
* **Pet Vs Cattle**:-
### K8S Architecture
#### Control Plane Components
  * `kube-api sever`: all communication of k8s cluster,Internal or external and exposes functionalityover http protocol
  * `etcd`: memory of k8s cluster
  * `kube scheduler`: Creating k8s objects and scheduling on right node
  * `controller manager`: Responsible for Desired state, if not do the neccessary steps
  * `cloud controller manager`: 
#### Node Components
  * `kubelet`: an agent of control,plane on node
  * `kube-proxy`: Responsible for Networking for containers on node
  * `Container Run time`: container technology to be used in k8s (docker - cri dockerd)
* `kubectl`: command line interface for k8s
* `kubernetes api-resources`: all objects which can be created by k8s
* `workloads`:
  * pods
  * jobs
  * cron jobs
  * service
  * replica set
  * replication controller
  * stateful set
  * daemon set
  * deployment 
### Pods
* The smallest unit of creation of k8s
* primitive of k8s
* Pod will have Containers  -- single or multiple
* Every Pod will gets an ip address 
* Pod gets assigned to node
* we can create resources by 2 ways
  * Imperative : telling what exactly to do step by step
  * Declarative: telling only desired state-- manifest files
* **Container state**: `waiting`,`Running`&`Terminated`
* **Container Restart Policy**
    * always
    * on failure
    * never
### Controllers
* Controllers are k8s objects which runs on other resources of k8s
* Controllers maintain Desired state
* some controllers are
  * Relipcation controllers/ Replica set
  * Jobs
  * Cron jobs
  * Stateful sets
  * Daemon set
  * Deployments
![preview](images/k8snt1.png)
### K8s Jobs
* Jobs
  * A Job will run a script / perform a given task and once it is completed, it will stop the process and runs only when it needed
  * **Ex**: to run backup before the running app
```yaml
---
apiVersion: batch/v1
kind: Job
metadata:
  name: hellojob
spec:
  template:
    metadata:
      name: jobpod
    spec:
      containers:
        - image: alpine
          command:
            - sleep
            - 10s
```
* Cron Job
  * A Cron JOb run a script /perform a task at a given schedule or given intrvals
  * **Ex:** scheduling a task to run every hour or run at 6 am daily
```yaml
---
apiVersion: batch/v1
kind: CronJob
metadata:
  name: periodicjob
spec:
  schedule: '* * * * *' 
  jobTemplate:
    metadata:
      name: getlivedata
    spec:
      template:
        metadata:
          name: livedatapod
        spec:
          containers:
            - name: alpine
              image: alpine
              command:
                - sleep
                - 3s
```
* A Job restart policy cannot be `Always` as job will never finish
* Jobs will Have `backoffLimit` to limit the no of restarts
* Jobs will `activeDeadLine` seconds to limit the time period of execution
### Replica Set
* A Replica sets purpose is to maintain stable set of replica pods at a given time
* it is often used to guarantee that the availbility of a spcified number of identical pods
* A Replica set is defined by the following fields
  * Selector: specifies how to identify the pods which it can acquire 
  * No of replicas: indicates how many pods it should maintain
  * Pod template: specifies Data of new pods it should create
* We can increase (scale out) as well decrease (scale in) the replica count
* Deployment is a higher-level concept that manages ReplicaSets and provides declarative updates to Pods along with a lot of other useful features
```yaml
---
apiVersion: apps/v1
kind: ReplicaSet
metadata: 
  name:            # RS name
  labels:          # labels for RS
spec:              # RS spec
  minReadySeconds:
  replicas:
  selector:        # we can use 2 types - equality and set based
    matchExpressions:    # set based
      - key:
        operator:
        values:
    # we can equality based
    matchLabels:
      app: nginx          # we can write any key value pair
  template:               # pod template
    metadata:             # pod metadata
      name:               # pod name
      labels:             # labels for pod
        app: nginx        # examples key value pair
    spec:                 # pod spec
      containers:
        - name:           # container name
          image:          
          ports:
            - containerPort:      # application port
```
### Labels and Selector 
* labels are key value pairs assigned as metadata to objects for indetification
* labels are key value pairs assigned to the objects such as pods
* Labels are intended to be used to specify identifying attributes of objects that are meaningful and relevant to users, but do not directly imply semantics to the core system
* Labels can be used to organize and to select subsets of objects.
* Labels can be attached to objects at creation time and subsequently added and modified at any time.
*  Each object can have a set of key/value labels defined. Each Key must be unique for a given object.
```yaml
metadata: 
  labels: 
    key1 : value1
    key2 : value2
```
* via `label selctor` the client /user can identify set of objects
* 2 types of label selectors
  * equality based
    * equals
    * not equals
  ```yaml
    environment = production
    tier != frontend
  ```
  * set based
    * in
    * not in
    * exists
  ```yaml
    environment in (production, qa)
    tier notin (frontend, backend)
    partition
    !partition
  ```
### Annotations
* Annotations are used to attach arbitrary non identifying metadata to objects
* clents such tools and libraries can retrieve this data
* Labels can be used to select objects and to find collections of objects that satisfy certain conditions. In contrast, annotations are not used to identify and select objects. 
* The metadata in an annotation can be small or large, structured or unstructured, and can include characters not permitted by labels.
* Ex: time stamps,release ids,git branch,image hashes,image registry 
```yaml
metadata:
  name: annotations-demo
  annotations:
    imageregistry: "https://hub.docker.com/"
```
### k8s as a service
* Every cloud providers offers k8s as aservice
* AKS,EKS,GKE
* K8s as a service basically means the master nodes will be managed by cloud provider
* less admin.nodes can be scaled
### Service
* In Kubernetes, a Service is a method for exposing a network application that is running as one or more Pods in your cluster.
* generally in k8s every pod will get unique ip & name
* so in k8s deployments pods will be created and deleted dynamically
* so connecting to pods using their name/ip is not sensible
* so here service will come into play
* service is k8s object will get unique ip address which is virtual ip which helps in forwarding traffic from one pod to another
* service basically works by using label and label selectors
* by using service we can expose the pods `internally` and `externally`
* Fields used in service are
  * selector and ports in service spec
  * which are already defined in pod spec and metadata
  * `labels in pod` - `selector in svc`
  * `port in pod` - `target port in svc`
  * Field `type` is used define for type od communication - `internal or external`
  * for Internal - `ClusterIP` 
  * for External - `LoadBalancer`
  * and field `ClusterIp - None` is used for `Headless Service`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app.kubernetes.io/name: proxy
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
      - containerPort: 80
        name: http-web-svc

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app.kubernetes.io/name: proxy
  type: ClusterIP
  ports:
  - name: name-of-service-port
    protocol: TCP
    port: 80
    targetPort: 8080
``` 
* `ingress` is also kind of service in k8s runs on HTTP protocal
### Health checks/Probes for containers in k8s pods
* k8s supports 3 kinds of checks
#### liveness probe
* if this check fails kubelet will restart the container.
* The kubelet uses liveness probes to know when to restart a container
#### readyness probe
* The kubelet uses readiness probes to know when a container is ready to start accepting traffic.
* if this check fails the pod will be removed from service (pod will not get requests from service)
* One use of this signal is to control which Pods are used as backends for Services. When a Pod is not ready, it is removed from Service load balancers.
#### startup probe
* The kubelet uses startup probes to know when a container application has started
* after succeeding this check only, it enables liveness and readiness 

### Run pods with specific Resources - CPU /Memory
* When you specify a Pod, you can optionally specify how much of each resource a container needs. The most common resources to specify are CPU and memory (RAM); there are others.
* When you specify the resource request for containers in a Pod, the kube-scheduler uses this information to decide which node to place the Pod on.
* When you specify a resource limit for a container, the kubelet enforces those limits so that the running container is not allowed to use more of that resource than the limit you set
#### Requests and Limits
* If the node where a Pod is running has enough of a resource available, it's possible (and allowed) for a container to use more resource than its `request` for that resource specifies   
* a container is not allowed to use more than its resource `limit`.
### Container types in pods 