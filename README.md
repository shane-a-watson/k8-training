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


# ETCD – Commands

(Optional) Additional information about ETCDCTL UtilityETCDCTL is the CLI tool used to interact with ETCD.ETCDCTL can interact with ETCD Server using 2 API versions – Version 2 and Version 3.  By default it’s set to use Version 2. Each version has different sets of commands.
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