# K8 Notes

## Resources
### Pods
Pods are the smallest deployable units in Kubernetes.
They represent a single instance of a running process in a cluster, encapsulating one or more containers.
Containers within a pod share the same network namespace, enabling easy communication between them.

### Deployments
Deployments are higher-level abstractions built on top of pods to manage their lifecycle and ensure the desired state of the application.
They enable declarative updates, rollbacks, and scaling of applications by defining desired characteristics such as the number of replicas and container specifications.
Deployments automatically handle the creation and scaling of underlying ReplicaSets.

### ReplicationController
ReplicationControllers are the predecessors of Deployments and are used to ensure a specified number of pod replicas are running at all times.
They continuously monitor the state of pods and create or terminate replicas as needed to maintain the desired number.
While still functional, Deployments have largely superseded ReplicationControllers due to their additional features and flexibility.

### ReplicaSet
ReplicaSets are the building blocks of Deployments, responsible for ensuring a specified number of pod replicas are running.
They allow for the scaling of pods horizontally by creating or removing replicas based on the defined criteria.
Deployments use ReplicaSets to manage and orchestrate updates to the application by creating a new ReplicaSet and gradually migrating pods.

## Commands
### Resources:
- pods
- deployments
- replicationcontroller
- replicaset

### kubectl run
kubectl run {NAME} --image={IMAGE} 

### kubectl create
kubectl create -f {FILEPATH}

### kubectl get
`kubectl get {RESOURCE}`
Get all resources: `kubectl get all`

**Note** - get more info with `kubectl get {RESOURCE} -o wide`

## kubectl delete
`kubectl delete {RESOURCE} {ID}`

## kubectl describe
`kubectl describe {RESOURCE}`

## kubectl edit - This will open an editor and will make the changes as soon as the file is saved
`kubectl edit {RESOURCE} {RESOURCE-NAME}`

**Example** - `kubectl edit replicaset myapp-replicatset` 

## kubectl scale
`kubectl scale {RESOURCE} {RESOURCE-NAME} {ARGS}`

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
