# K8 Notes

## Objects
### Pods
Pods are the smallest deployable units in Kubernetes.
They represent a single instance of a running process in a cluster, encapsulating one or more containers.
Containers within a pod share the same network namespace, enabling easy communication between them.

### Deployments
Deployments are higher-level abstractions built on top of pods to manage their lifecycle and ensure the desired state of the application.
They enable declarative updates, rollbacks, and scaling of applications by defining desired characteristics such as the number of replicas and container specifications.
Deployments automatically handle the creation and scaling of underlying ReplicaSets.

### ReplicaSet
ReplicaSets are the building blocks of Deployments, responsible for ensuring a specified number of pod replicas are running.
They allow for the scaling of pods horizontally by creating or removing replicas based on the defined criteria.
Deployments use ReplicaSets to manage and orchestrate updates to the application by creating a new ReplicaSet and gradually migrating pods.

### Namespaces
Namespaces provide a way to logically divide cluster resources into virtual clusters, allowing multiple users, teams, or applications to share a physical Kubernetes cluster without interfering with each other. 
Essentially, namespaces offer a scope for Kubernetes objects, such as pods, services, and replication controllers. They help in organizing and managing resources by providing isolation, resource quota, and access control within the cluster. 
By default, Kubernetes resources are created in the 'default' namespace, but users can create additional namespaces to organize and isolate their workloads. This abstraction simplifies cluster management, enhances security, and supports multi-tenancy in Kubernetes environments.

### Daemon Sets
A DaemonSet in Kubernetes ensures that a copy of a specific pod runs on each node in the cluster. It's typically used for system-level tasks or agents that need to be present on every node, such as logging, monitoring, or network plugins. DaemonSets automatically schedule and maintain pods on each node, ensuring consistent deployment and management of essential services across the cluster.

## Commands
### Objects:
- pods
- deployments
- replicaset

### kubectl run
`kubectl run {NAME} --image={IMAGE}`

**Note**  
You can generate the YAML file for a pod by running `kubectl run {POD-NAME} --image={IMAGE-NAME} --dry-run=client -o yaml` Create the file directly by adding `> {FILE-NAME}`

### kubectl create
`kubectl create -f {FILEPATH}`

### kubectl get
`kubectl get {OBJECT}`
Get all objects: `kubectl get all`

**Note** - get more info: `kubectl get {OBJECT} -o wide`
**Note** - get pods using labels: `kubectl get {OBJECT} --selector {KEY}={VALUE}` To use multiple selectors: `kubectl get pods --selector={KEY1}={VALUE1},{KEY2}={VALUE2}`

## kubectl delete
`kubectl delete {OBJECT} {ID}`

**Note** - If youve changed a config file you can delete the old object and replace it with: `kubectl replace --force -f {FILE}`

## kubectl describe
`kubectl describe {OBJECT}`

## kubectl edit - This will open an editor and will make the changes as soon as the file is saved
`kubectl edit {OBJECT} {OBJECT-NAME}`

**Example** - `kubectl edit replicaset myapp-replicatset` 

## kubectl scale
`kubectl scale {OBJECT} {OBJECT-NAME} {ARGS}`

**Example** - `kubectl scale replicaset myapp-replicatset --replicas=2`

## kubectl rollout
View Rollout Status:
`kubectl rollout status deployment/{deployment-name}`

Pause Rollout:
`kubectl rollout pause deployment/{deployment-name}`

Resume Rollout:
`kubectl rollout resume deployment/{deployment-name}`

History of Rollout:
`kubectl rollout history deployment/{deployment-name}`

Undo Last Rollout:
`kubectl rollout undo deployment/{deployment-name}`

Rollback to Specific Revision:
`kubectl rollout undo deployment/{deployment-name} --to-revision={revision-number}`

## kubectl set
Change image: 
`kubectl set image deployment {deployment-name} {container-name}=nginx:1.18-perl --record`

## Namespace specific
Set Default Namespace (for current context):
`kubectl config set-context --current --namespace=<namespace-name>`

Switch Default Namespace (globally):
`kubectl config set-context --current --namespace=<namespace-name>`

Resource Creation in Specific Namespace:
`kubectl create -f <resource-file> --namespace=<namespace-name>`

Get Resources in a Namespace:
`kubectl get <resource-type> --namespace=<namespace-name>`

Get all resources in all Namespaces:
`kubectl get <object> --all-namespaces`

## kubectl taint
You can taint a Node to ensure that only Tolerent Pods can be placed on them. Unless a tolerance is added to the Pod it will never be able to run on the tainted Node
`kubectl taint nodes {NODE_NAME} {KEY}={VALUE}:{TAINT_EFFECTS}`
Options for the taint effect are:
- NoSchedule: Pods will NOT be sceduled on the Node
- PreferNoSchedule: Will try to avoid placing a Pod, but no guarantees 
- NoExecute: New Pods will not be scheduled and existing Pods will be evicted (if they dont have the tolerance)

Remove a taint:
`kubectl taint {NODE_NAME} {TAINT_NAME}`

## kubectl label
To label Nodes:
`kubectl label node {NODE_NAME} {KEY}={VALUE}`

# Service Types

## ClusterIP
This is the default service type. It exposes the service on a cluster-internal IP address, making it accessible only from within the cluster.

## NodePort
Exposes the service on each node's IP at a static port. It can be accessed from outside the cluster by using <NodeIP>:<NodePort>.

## LoadBalancer
Creates an external load balancer that routes traffic to the service. This is useful when the application needs to be accessed from outside the cluster, and the cloud provider supports it.

## ExternalName
Maps the service to the contents of the externalName field (e.g., a DNS name).

# MiniKube
Return service detail: 
`minikube service {SERVICE}`

## Concepts
### Scheduler:
The Kubernetes Scheduler is responsible for assigning workloads, represented by Pods, to available nodes in the cluster. It takes into consideration factors such as resource requirements, affinity/anti-affinity rules, and load balancing, ensuring efficient utilization of the cluster resources.

### Kubelet:
Kubelet is an agent running on each node in the Kubernetes cluster. It manages the containers on the node, ensuring they are running as expected by interacting with the container runtime. Kubelet also communicates with the control plane components, reporting the node's status and handling tasks like starting, stopping, and monitoring containers.

### Controller:
Controllers in Kubernetes are responsible for regulating the desired state of the cluster. They continuously monitor the current state, compare it with the desired state declared by the user, and take corrective actions to ensure the cluster converges to the desired state. Examples include the ReplicaSet controller, Deployment controller, and StatefulSet controller.

### Kube-proxy:
Kube-proxy is a network proxy running on each node that maintains network rules to allow communication between Pods within the cluster and external traffic. It ensures that service requests are appropriately forwarded to the correct backend Pods and handles load balancing, providing a consistent and reliable network environment.

### API Server:
The Kubernetes API Server acts as the front-end to the Kubernetes control plane. It exposes the Kubernetes API, which allows users and other components to interact with the cluster. API Server validates and processes requests, updating the cluster state stored in etcd, a distributed key-value store. It serves as the primary interface for cluster management, receiving commands from kubectl and other Kubernetes components.

# ETCD 

### Overview
etcd is a distributed key-value store essential for Kubernetes, serving as the primary data store for the cluster's configuration and state information. It facilitates cluster coordination by storing data related to nodes, pods, and other resources, ensuring consistency across multiple nodes. With a focus on high availability, etcd operates in a clustered environment, allowing for fault tolerance and redundancy. Its role extends to providing a watch mechanism, enabling real-time updates and coordination among different components within the Kubernetes cluster. Overall, etcd plays a critical role in maintaining the reliability and consistency of the cluster state, ensuring seamless communication and coordination among various Kubernetes components.

### Commands
Additional information about ETCDCTL UtilityETCDCTL is the CLI tool used to interact with ETCD.ETCDCTL can interact with ETCD Server using 2 API versions – Version 2 and Version 3.  By default it’s set to use Version 2. Each version has different sets of commands.
For example, ETCDCTL version 2 supports the following commands:

`etcdctl backup`
`etcdctl cluster-health`
`etcdctl mk`
`etcdctl mkdir`
`etcdctl set`

Whereas the commands are different in version 3

`etcdctl snapshot save`
`etcdctl endpoint health`
`etcdctl get`
`etcdctl put`

To set the right version of API set the environment variable ETCDCTL_API command

`export ETCDCTL_API=3`

When the API version is not set, it is assumed to be set to version 2. And version 3 commands listed above don’t work. When API version is set to version 3, version 2 commands listed above don’t work.

Apart from that, you must also specify the path to certificate files so that ETCDCTL can authenticate to the ETCD API Server. The certificate files are available in the etcd-master at the following path. We discuss more about certificates in the security section of this course. So don’t worry if this looks complex:

`--cacert /etc/kubernetes/pki/etcd/ca.crt`
`--cert /etc/kubernetes/pki/etcd/server.crt`
`-key /etc/kubernetes/pki/etcd/server.key`

So for the commands, I showed in the previous video to work you must specify the ETCDCTL API version and path to certificate files. Below is the final form:

`kubectl exec etcd-controlplane -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl get / --prefix --keys-only --limit=10 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key"`


## Create files with commands
Create an NGINX Pod

`kubectl run nginx --image=nginx`

Generate POD Manifest YAML file (-o yaml). Don’t create it(–dry-run)

`kubectl run nginx --image=nginx --dry-run=client -o yaml`

Create a deployment

`kubectl create deployment --image=nginx nginx`

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) and save it to a file.

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

Make necessary changes to the file (for example, adding more replicas) and then create the deployment.

`kubectl create -f nginx-deployment.yaml`

Create Daemon Set
`kubectl create daemonset <daemonset-name> --image=<image-name> --namespace=<namespace>`


## Impreitive Commands
In the exam the expectation is that you will use Impreretive Commands, these are the commands that expalin exactly how to do something rather that delaratove which just explains the end state. 

Some useful impreretive commands:

Create an Pod:
`kubectl run nginx --image=nginx`

Create a deployment:
`kubectl create deployment --image=nginx nginx`

Generate Deployment with 4 Replicas:
`kubectl create deployment nginx --image=nginx --replicas=4`

You can also scale a deployment using the kubectl scale command.
`kubectl scale deployment nginx --replicas=4`

Another way to do this is to save the YAML definition to a file and modify:
`kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx-deployment.yaml`

Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379:
`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml`
(This will automatically use the pod’s labels as selectors)

Or

`kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml`
(This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

Create a Service named nginx of type NodePort to expose pod nginx’s port 80 on port 30080 on the nodes:
`kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml`

