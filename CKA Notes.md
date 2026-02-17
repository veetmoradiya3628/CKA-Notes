
##### Core Concepts 

- Cluster & Nodes
- Cluster
	- Defined boundaries and secured area
	- Control plane
		- Master
		- Worker
	- Node can be virtual or physical
	- Applications runs on Worked node
	- Single node vs. Multi node cluster
- Container runtime
	- Pre requisites for container runtime setup also requires on node
	- Required to be running on the nodes before they can be added to the cluster
	- container networking
	- containerd
- kubelet
	- "Node Agent" running on each node
	- kubelet works with the container runtime on each node, to launch pods and their containers
- kubeadm
	- Initializes a secure Kubernetes cluster, manages node joining, and handles certificate upgrades.
- kubectl
	- The command-line interface for communicating with the Kubernetes API server to deploy applications, inspect, and manage cluster resources.
- kubelet
	- Ensures containers are running in Pods by interacting with the container runtime (e.g., Docker/containerd).
- Pod networks
	- CoreDNS / ClusterDNS
	- flannel, calico are important pod networks
	- flannel CIDR - 10.224.0.0/16
- Control plane vs. Worker node setup
- Master node components
	- kube-controller-manager
		- Node controller
		- Job controller
		- EndpointSlice controller
		- Service Account controller
	- kube-apiserver
	- etcd
	- kube-scheduler
- Common components
	- kubelet
	- kube-proxy
	- container runtime
- Accessing the cluster from external network
	- admin kubeconfig
	- how to generate and share admin kubeconfig
- Pods
	- Smallest deployable object
	- Ephemeral and disposable entities
	- Uses controller to implement Pod scaling and healing
	- single or multiple containers
		- Init containers and App containers
	- storage resources
		- shared storage volumes
	- Unique network IP
		- Containers share the Network Namespace
	- Pod manifest (template)
		
	```
	apiVersion: v1
	kind: Pod
	metadata:
		name: testpod
	spec:
		containers:
		- name: testcontainer
		  image: ubuntu
		  command: ['sh', '-c', 'echo Hello kubernetes!!! && sleep 60']
		restartPolicy: Never
	```
	- Phases of Pods
		- Pending
		- Running
		- Succeeded / Completed
		- Failed
		- Unknown

- Namespaces
	- default namespace
	- In Linux, namespaces partition the resources among running processes
	- In k8s, namespaces isolates the resources within the cluster
	- Cluster and namespace level resources
	```
	kubectl api-resources --namespaced=true
	```
	- Unique namespace in cluster and unique resource with name with in the scope of namespace
	- Resource limit in a Namespace
		- resource quotas

- Labels, Selectors and Annotations
	- Labels
		- Key value pairs attached to objects in a structured manner
		- key must be unique
	- Selectors
		- Used to identify a set of objects
		- Equality-based selectors
		- Set-based selectors
	- Annotations
		- Attach metadata to objects
		- Not used to identify and select objects

- Deployments
	- Describe a desired state in the Deployment
	- Deployment controller continuously monitors the instances
	- Provides a self-healing mechanism
	- an ideal way to deploy stateless applications in K8s
	- supports multiple replicas configuration
	- Deployments - ReplicaSets - Pods

- Command & Arguments
	- Container executes the default command in container image
	- override the command present in the Dockerfile
	- Make use of the command field and args field
	- Run command in shell
	- Command and arguments cannot be changed after the pod is created

- Environment variables
	-  All the environment variables specified in the container image are available to the container. In addition, we can also set environment variables for the containers that run in the pod. To set environment variables,
	- we can include the ENV feed in the YAML file and provide the values for the environment variables. ENV allows us to set environment variables for a container specifying a value directly for each variable that you name.

##### Storage, Services and Networking

###### Cluster Networking
- Each node in the cluster has a pre-assigned IP address
- Every pod gets its own IP address
- Pod network CIDR range is specified during cluster initialization
- kube-proxy enables communication between the pods
- Service Object - Load balancing between replicas
- Services also have unique IP address
- Communication within cluster
	- Container-to-container communications
	- Pod-to-pod communications
	- Pod-to-service communications
		- ClusterIP service
	- External-internal communications
		- NodePort, LoadBalancer, ExternalName services
	- Ingress controller and ingress rules
		- Reverse proxy and load balancer
	- Egress
		- Outbound traffic to outside the cluster

###### Hostname and Cluster DNS
- Hostname of the container is the name of the Pod in which the container is running
- Pod spec has an optional hostname field to specify the Pod's hostname
- Every service defined in the cluster is assigned a DNS name
- A pod's DNS search list includes own namespace and the cluster's default domain
- CoreDNS is a flexible & extensible DNS service
- ClusterIP services are assigned a DNS A record
	`my-svc.my-namespace.svc.cluster-domain.example`

###### Services
- Pods are transient & load balance and load across available replicas
- Service provides an abstraction for a set of Pods and a policy to access them
- It provides a single DNS name for a set of Pods and can load-balance across them
- Types
	- ClusterIP
	- NodePort
	- LoadBalancer
	- ExternalName
- ClusterIP
	- Exposes the service within the cluster only
	- Service maps any incoming traffic to targetPort
- NodePort
	- Exposed on each Node's IP
	- Accessible from external networks on NodeIP:NodePort
	- Automatically creates ClusterIP service internally
- LoadBalancer service
	- Exposes the service using a cloud provider's load balancer
	- ClusterIP services are automatically created
- ExternalName service
	- Does not have selectors; instead uses DNS names
	- No proxying of any kind is set up for external name
	- Use case for services without selectors

###### Ingress controller and Ingress API
- Ingress Controller
	- Runs in a cluster and is used to manage external access to the services in a cluster
	- Kubernetes supports and maintains AWS, GCE, and nginx ingress controllers
	- Other controllers examples
		- AKS Application Gateway Ingress Controller
		- Istio Ingress is an Istio based ingress controller
		- Ambassador API Gateway
- Ingress
	- K8s resource runs in a namespace, name-based virtual hosting
- One external public IP to Ingress Controller 
- More than one ingress controller deployment possible in single cluster
- Annotation on ingress object to determine which Ingress Controller to connect
- Ingress Rules
	- Ingress spec has all the information needed on route information
	- list of rules matched against all incoming requests
	- backend = service + port
- Types of Ingress
	1. Single service ingress
	2. Name based virtual hosting
	3. Simple fanout
- Secured Ingress with TLS private key and certificate

###### CNI - Container Network Interface And Network Policy
- CNI
	- Network needs before any applications
	- K8s supports CNI plugins for networking
	- Defines basic execution flow & configuration format for network operations
	- Consists specs for network configs
- Network policy
	- Network policies are implemented by the network plugin
	- Application-centric construct which specify how a pod is allowed to communicate with various network entities
	- Based on
		- Pods
		- Namespaces
		- IP Blocks
	- Ingress vs. Egress

###### Volumes & Persistent Storage
- Pods are Ephemeral
- Data saved within container is temporary
- Data outside the container filesystem
- K8s Volumes provides a storage to store the data
- Volume is directory
- Volumes can be mounted to container at a defined path
- Several types of Volumes
	1. emptyDir
		- Empty when pod starts
		- Stores the data on the same node where pod runs
	2. hostPath
		- Mounts a file or directory from the host node's filesystem into the pod
		- it will last even if pod is recreated
- Some other types of Volumes commonly used are
	- AWS EBS CSI
	- Azure Disk CSI
	- Azure File CSI
	- GCE Persistent Disk CSI
	- Portworx CSI volumes
	- NFS
- Persistent Volumes
	- Persistent Volumes provide an API for users and administrators
	- how storage is provided from how its consumed
	- PV is provisioned by administrator
	- PVC
		- Is a request for storage by a user or application developer
		- PVCs consume PV resources
	- PVs are not namespace resource
	- PVCs are namespace resource
	- Access Modes
		- ReadWriteOnce (RWO)
		- ReadOnlyMany (ROX)
		- ReadWriteMany (RWX)
- Storage classes
	- Static provisioning without storage classes
	- Dynamic provisioning with storage classes
	- PVC to PV binding is a one-to-one mapping
	- Its not namespace scoped
- CSI - Container storage interface
- Volume binding modes
- Volume phases
	- PVs are claimed by PVCs
	- Volume can be in one of the following phase
		- Available
		- Bound
		- Released
		- Failed
- Reclaim Policies
	- Retain
	- Recycle
	- Delete
##### Kubernetes: Scaling Workloads

###### Replication Controller & Replica Sets

- Replication Controller
	- **Definition:** The original Kubernetes mechanism for ensuring a specified number of identical pod replicas are running at all times.
	- **Function:** Automatically replaces pods if they crash, fail, or are deleted.
	- **Selector Type:** Supports only **equality-based** label selectors (e.g., `app=nginx`).
	- **Status:** Largely replaced by ReplicaSets; considered legacy.
- Replica Set
	- **Definition:** The next-generation, improved version of the Replication Controller that maintains a stable set of pod replicas.
	- **Function:** Ensures high availability by maintaining a desired number of pods, usually managed by Deployments.
	- **Selector Type:** Supports **set-based** label selectors (e.g., `app in (nginx, apache)`).
	- **Usage:** Preferred for new workloads and used implicitly by Deployments to handle rolling updates.
- Replication Controller uses an Equity based selector
- ReplicaSets use Set-based selector
- Deployment will automatically creates a ReplicaSet

###### Init Containers
- Run
	- In a Pod
	- Before the App containers
	- Completion
- Common usecases
	- Creating config file from env variables
	- Settings up cache for app
	- Migrate or load database
	- Download plugins

###### Config Maps & Secrets
- Secret as base64 encoded while Config Map is without base64
- Config map & secret can be created via command or can refer data from file itself
- For using on Pod
	- Mount as Volumes
	- Mount as Env Vars
- docker registry secret is Usecase
- Generic and docker-config type secrets
- Config maps for
	- non-sensitive information
	- stored in plain text
	- size limit of 1 MB per object
	- created from literal, files or directories
- Secrets for
	- sensitive information
	- stored base64 encoded
	- size limit of 1 MB per object and 64 kb per key-value pair
	- created from literal values or files

###### Scaling Applications
- Two ways to do 
	1. Scaling manually with kubectl
	2. Horizontal Pod Autoscaling (HPA)
- Robust self-healing application deployment
	1. Pods should be defined to ReplicaSet or RS from Deployment
	2. Deployments define from ReplicaSets and Strategies for updates
		- exception: restartPolicy: Never
	3. Define and Use labels to identify meaningful attributes of your application
- Features from self-healing application
	1. Automatically recovers from an unhealthy state
	2. Makes certain at least one copy of the application is running at all times
	3. Maintains a consistent network identity
	4. Liveness and Readiness probes
- Using rollouts
	1. rollout status
	2. rollout history
	3. rollout undo
- API versions and Resources

##### Kubernetes: Pods and Schedulers

###### Stateful Sets
- Like Deployments, schedules pods with identical specifications
- Maintain a sticky identity - each scheduled pod gets a persistent identifier maintained across rescheduling
- Ordered deployment and updating (in order pods were created)
- Consistent names
- Persistent storage
- Ordinal names (0...)
- Stable Hostnames
- Scaling
	- Rolling Updates
	- On Delete

###### Jobs and Cron Jobs
- Jobs
	- Job creates one more more pods to execute a task tracking success and failures
	- As pod complete, successes are tracked, when enough done, the job is complete
	- meant to run one thing to completion
- Cron Jobs
	- Same features as Jobs
	- Scheduled to run on schedule
	- can run multiple jobs in parallel
	- can retry failed jobs
	- can be configured to delete jobs after they have completed

###### DeamonSets
- Runs a pod on all cluster nodes that meet criteria
- Useful for cluster-level operations pods: logging, monitoring and background tasks
- Automatically creates and removes pods as nodes are added and removed
- Can use rollout strategies as deployment do namely
	- OnDelete
	- RollingUpdate
- Cannot create DeamonSet without YAML
- Tolerations
- Node Selector
- Ways to refers to DeamonSets

###### K8s Schedulers
- Watches for newly created Pods to find a place to schedule them
	- Resource requirements
	- Availability of resources
	- Pod Affinity / Anti-affinity
	- Tolerations
	- Priority
- Filtering and scoring
- Custom schedulers
- Taints and tolerations
- Scheduling framework includes the phases as 
	- Pre Enque
	- Scheduling cycle
	- Binding cycle

###### Assigning Nodes to Pods
- Taints: use of repel pods
- Tolerations: allows pods to schedule on nodes with matching taints
- Affinity and Anti-affinity
	- Node Affinity
	- Pod Affinity
	- Pod Anti-affinity
- Usages
- Scheduling gates (1.26)
	- avoids unnecessary churn in scheduler
	- gives us control of when to be considered for scheduling
	- gates must be manually removed

- Resource Limits
	- Requests and Limits
	- Resources
	- CPU, memory, Huge Pages
	- checking limits / events
	- reasons to use limits
	- CPU
		- Quantity
	- Memory
		- Units - E P T G M k
	- Huge pages
		- Performance,
		- Fragmentation
		- Conserve Memory
- Manifest management
	- Template
		- Environments
		- Applications
		- Deployment strategies
	- Tooling
		- kubectl
		- YQ
		- Helm
		- Kustomize
	- Templating with Code like Jinja, Golang or Ansible

###### Static Pods
- Pod not managed by kube API server
- bound to kubelet agent on Node
- kubelete agent will manage and restart if required
	- bound to one kubelete on one node
- Visible (but not controller) by API server
- Created via manifest in  a directory watched by kubelet
- Pod without replication requirement
- Pod that can not scale and not managed by control plane

##### Kubernetes: Cluster Architecture, Installation & Configuration

- Kubeadm for creating a Kubernetes cluster
###### RBAC
- Authentication (AuthN)
- Authorization (AuthZ)
	- ABAC
	- RBAC
	- Webhook
- Admission control
- Subjects
	- User
	- Groups
	- Service Accounts
- Role
- Role binding
- Cluster Role
- Cluster Role Binding
- aggregation Rule
	- Matches to the label combine rules
- System is a reserved prefix - can not use with Users and Groups
- Categories
	- API Discovery
	- User-Facing
	- Core component
	- Built in

- High availability in K8s cluster
	- Use more than one Control Plane (master) node
	- Front the cluster with Load Balancer
	- Using more than one region (or Data center)
	- Ensuring regular backups of certs and etcds
- Etcd HA
	- Stacked Etcd
	- External Etcd Cluster
- LB in front of the Cluster API server
- Multiple instances of 
	- Kube Scheduler
	- Kube Controller Manager
	- Cloud Controller Manager
- Multiple worker pools
- Replication and Pod Autoscaling
- Node level failure handling
- Load Balancer and service redundancy
- Cluster-level fault tolerance 
	- multiple CP
- Perform cluster upgrades
	- drain and cordon

- Cluster and Application monitoring
	- Probes and parameters
	- Probes test
		- Command
		- HTTP GET
		- TCP Check
- Etcd backup and restore
