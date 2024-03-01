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

## Commands
### Objects:
- pods
- deployments
- replicaset

### kubectl run
`kubectl run {NAME} --image={IMAGE}`

**Note** - You can generate the YAML file for a pod by running `kubectl run {POD-NAME} --image={IMAGE-NAME} --dry-run=client -o yaml` Create the file directly by adding `> {FILE-NAME}`

### kubectl create
`kubectl create -f {FILEPATH}`

### kubectl get
`kubectl get {OBJECT}`
Get all objects: `kubectl get all`

**Note** - get more info with `kubectl get {OBJECT} -o wide`

## kubectl delete
`kubectl delete {OBJECT} {ID}`

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