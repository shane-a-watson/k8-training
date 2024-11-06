# K8 Notes

## AKS
az login
az account set --subscription {SUB_NAME}
If using an AKS, log in with: az aks get-credentials --resource-group {RESOURCE_GROUP_NAME} --name {CLUSTER_NAME}

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

### Shorthand versions:
- Pod: po
- Service: svc
- ReplicaSet: rs
- Deployment: deploy
- StatefulSet: sts
- DaemonSet: ds
- Job: job
- Namespace: ns
- ConfigMap: cm
- Secret: secret
- PersistentVolume: pv
- PersistentVolumeClaim: pvc

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
Rolling updates are the default deployment strategy for Kubernetes. This means one pod will go downa nd a new one comes up rather than all updated at onces.
The alternitive is the "Recreate Strategy" this is when all the pods are brought down and replaced

You can update the manifest and then run:
`kubectl apply -f {MANIFEST_FILE}`
or 
`kubectl set image deplopyment/{deployment-name} \ {CONTAINER_NAME}={NEW_IMAGE}`
This way will not update the file though so it will be out of date

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

The API Server will have a config file, located at: `/etc/kubernetes/manifests/kube-apiserver.yaml`

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


## Logging and Montioring

### Metric Server
Install metric server:
It can be cloned from github: `git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git`

Once installed:
View Node performance: `kubectl top node`
View Pod performance: `kubectl top pod`

### kubectl logs
Inspect logs: `kubectl logs {POD_NAME}`
If there are more than 1 container in the Pod then you need to specify the container you want the logs for.

## Docker and Kubernetes

There are certain arguments that can override the ones configured in the Dickerfile. 
For example, in Docker you use ENTRYPOINT to run a program upon startup, and CMD to run a command. 
These can both be overriden in the Pod manifest, example below:

```
apiVersion: v1
kind: Pod
metadata:
  name: sleeper-pod
spec:
  containers:
  - name: ununtu-sleeper
    image: ununtu-sleeper
    command: ["sleep2.0"] --- This is what overrides the Dockerfile ENTRYPOINT
    args: ["10"] --- This is what overrides the Dockerfile CMD
```

## Config Maps
You can create config maps in both the Imperative and declarative ways. 

Imperative (Using commands, no manifest file):
`kubectl create configmap {CONFIG_NAME} --from-literal={KEY}={VALUE}`
or 
`kubectl create configmap {CONFIG_NAME} --from-file={FILEPATH}`

Declarative (Using a file):
`kubectl create -f {PATH_TO_CONFIG_FILE}`

## Secrets
You can create Secrets both the Imperative and declarative ways. 
Imperative (Using commands, no manifest file):
`kubectl create secret generic {SECRET_NAME} --from-literal={KEY}={VALUE}`
example: `kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123`
or 
`kubectl create secret generic {SECRET_NAME} --from-file={FILEPATH}`

Declarative (Using a file):
`kubectl create -f {PATH_TO_CONFIG_FILE}`

To encode secrets (linux OS) run: 
`echo -n "{VALUE}" | base64`

## Init Containers
In a multi-container pod, each container is expected to run a process that stays alive as long as the POD’s lifecycle. 
But at times you may want to run a process that runs to completion in a container, that’s where initContainers comes in.
An initContainer is configured in a pod like all other containers, except that it is specified inside a initContainers section
When a POD is first created the initContainer is run, and the process in the initContainer must run to a completion before the real container hosting the application starts.
You can configure multiple such initContainers as well, like how we did for multi-containers pod. In that case, each init container is run one at a time in sequential order.

## Drain, Cordon and Uncordon
If a node goes down or has planned maintanance there is a way to remove all pods from it and move them to another node.
`kubectl drain {NODE_NAME}`

Once its drained it can be rebooted. When it comes back up it will still be unscehuleable. 
To allow pods to be placed back on it it needs to be uncordoned:
`kubectl uncorden {NODE_NAME}`

You can also cordon a node which means that no pods will be placed onto it:
`kubectl cordon {NODE_NAME}`

## Backups
### Resources
You can back up the entire Kubernetes deployed estate with: `kubectl get all --all-namspaces -o yaml > all-deployed-services.yaml`

Some tools will do this for you, eg. Velero will take backups of your estate.

### ETCD Backup
To make use of etcdctl for tasks such as back up and restore, make sure that you set the ETCDCTL_API to 3. 
This can be done as follows:
`export ETCDCTL_API=3`

When configuring etcd you specify the location the etcd files should be stored, this is what should be backed up.

You can create a snapshot of the etcd by using the etcd control utility: `ETCDCTL_API=3 etcdctl snapshot save {SNAPSHOT_NAME}.db`
View the status of the back up with: `ETCDCTL_API=3 etcdctl snapshot status {SNAPSHOT_NAME}.db`

To restore the backup:
1. Stop the kube api service: `service kube-apiserver stop`
2. Run the restore command (this created a new data directory): `ETCDCTL_API=3 etcdctl snapshot restore {SNAPSHOT_NAME}.db --data-dir {NEW_DATA_DIR_PATH}`
3. Reconfigure the etcd data file to use the new data directory.
4. Reload the service daemon: `systemctl daemon-reload`
5. Restart the etcd service: `service etcd restart`
6. Start the kube api server service: `Service kube-apiserver start`

* Note - You may have to authenticate to run the etcd commands, eg. 
```
ETCDCTL_API=3 etcdctl \
    snapshot save {SNAPSHOT_NAME}.db \
    --endpoints=https://127.0.0.1:2379 \ 
    --cacert=/etc/etcd/ca.crt
    --cert=/etc/etcd/etcd-server.crt
    --key=/etc/etcd/etcd-server.key
```

Since our ETCD database is TLS-Enabled, the following options are mandatory:

– cacert: verify certificates of TLS-enabled secure servers using this CA bundle
– cert: identify secure client using this TLS certificate file
– endpoints=[127.0.0.1:2379]: This is the default as ETCD is running on master node and exposed on localhost 2379.
– key: identify secure client using this TLS key file

## KubeConfig
View the current KubeConfig: `kubectl config view`

Update current context: `kubeclt config user-context {USERNAME}@{CLUSTER}`

For more info: `kubectl config -h`

### Switching context:
To switch contect: `kubectl config --kubeconfig={FILEPATH_TO_KUBECONFIG} use-context {CONTEXT_NAME}`

## Permission check
Check if you can do somthing by running: `kubectl auth can-i {ACTION} {OBJECT}`
example: `kubectl auth can-i create deployments`

Check other users permissions: `kubectl auth can-i {ACTION} {OBJECT} --as {USER}`

## kubectl roles
You can get / describe / delete roles by running: `kubectl {ACTION} role`
Additionally you can find out more info on by checking the rolebinding `kubectl {ACTION} rolebinding`

Create a role:
`kubectl create role {ROLE_NAME} --namespace={NAMESPACE} --verb={ACTION},{ACTION} --resource={OBJECTS} --dry-run=client -o yaml > {FILENAME}.yaml`

Create a role Binding:
`kubectl create rolebinding {BINDING_NAME} --namespace={NAMESPACE} --role={ROLE_NAME} --user={USER_NAME} --dry-run=client -o yaml > {FILENAME}.yaml`

