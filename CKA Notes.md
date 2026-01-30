
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