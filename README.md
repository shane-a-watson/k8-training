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