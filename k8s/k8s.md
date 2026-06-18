### Pod creation
- When we make a request to create a `Pod`, `kubectl` sends the request to the `kube-apiserver`. The API server performs authentication, authorization, and admission checks, then records the desired state in `etcd`. Next, the `scheduler` selects the best `node`, and the `kubelet` on that node instructs the `container runtime` to pull the image, set up the pod sandbox (`pause container`) and networking, and start the containers. Then, it updates the `Pod`'s status back in the API server. Finally the `controller manager` starts to monitor the pod for any changes.
---
### Pause container
- In docker, each container has it is own namespace and network namespace, so we see that each container has unique IP.
- In k8s, we have the concept of Pods, so we can have multiple container in same pod, so they share same pod IP and can communicate as a localhost on different ports
- So, who will manage this shared environment ? `pause container`.
- `pause container` -> is a small container that firstly runs in the pod to manage the pod environment, especially to manage the network namespace, and it takes the pod IP, then all other containers join this namespace, and it is running as long as the pod is running but it is almost not consuming any resources.
---
### Kube-proxy
- It is the component on the worker node that is responsible about routing the traffic from `service` to the `pods`, simply it handles ==service-to-pod communication.==
                            ![[Pasted image 20260523002604.png]]
- Pod-to-Pod communication is handled by `CNI` (container network interface) and Linux kernel, CNI makes interfaces and assigns IPs for pods and makes the routing tables, then Linux kernel forwarding the packets based on the routing table.
- If pods are on different nodes, `CNI` will handle the traffic using overlay encapsulation (VXLAN).
- `CNI` is responsible to assign IPs to the pods.
---
### Container runtime
- Component that is responsible for creating the containers, hence pods.
- It starts and stop the containers.
- It pulls the image from the image registry.
- It communicates with the kernel to setup Cgroups, namespaces and configure mount points.
- k8s supports different container runtime like docker, containerd, CRI-O.
- ==k8s has standard container runtime called CRI (container runtime interface).==
- High level vs Low level (In slides).
---
### Pod lifecycle
##### Pending
- This means the `Pod` has been accepted by the cluster, but it has not yet started running.
- What happens during this phase:
	- The `scheduler` selects a `Node`.
	- The `kubelet` prepares the `Pod`.
	- The `CNI` (Container Network Interface) configures the networking.
	- The container image is pulled.
	- Volumes are mounted.
If an issue occurs in any of these steps, the `Pod` remains in the Pending state.
##### Running
- This means the `Pod` has been bound to a `Node`, and all its containers have been started.
- That’s where `probes` come in.
>Running ≠ The application is healthy.
>A container can be in a 'Running' state while the application itself is experiencing issues.

##### Succeeded
- All containers have finished their work successfully.
- This cycle is shown with the cronjobs and scripts.

##### Failed
- All containers have finished their work, but at least one has failed.

##### Unknown
- API server can not know the state of the pod.
---
### Editing pods
- We have two methods to edit the pods' specs
	1. Declarative -> Editing the YAML file and use `kubectl apply -f <file.yml>`.
	2. Imperative -> use `kubectl edit pod <pod_name>` and change what you want then save.
> The difference is when you use the declarative method the annotations of the pod in the metadata is changed, but in the imperative method the annotations still the same with previous or old data
---
### Labels and selectors
- Used to filter the k8s objects 
- You can filter the objects by different methods
	1. Equality-based requirement -> `=`, `==`, `!=`
	2. Set-based requirement -> `In`, `NotIn`, `Exists`, `DoesNotExist`

- `In` and `NotIn` used to evaluate the value of a key compared to list of values
```YAML
selector:  
matchExpressions:  
- key: tier  
operator: In  
values:  
- backend  
- api
```

> Select any pod has the key tier and the value is backend or api.

- `Exists` and `DoesNotExist` used to ensure the existence of the key no matter what its value
```YAML
matchExpressions:  
- key: env  
operator: Exists
```

> Select any Pod that has the label `env`, regardless of its value.
---
## Namespaces
- Logical space that isolate the resource from each other.
### Default namespaces
##### Default
- Contains any resource that created without specifying namespace
##### Kube-system
- Contains the core components of Kubernetes cluster like API server, Scheduler, Controller manager, Kube-proxy....
- helpful for managing the access control to these components through RBAC, as you can prevent non-admin user to access this namespace.
##### Kube-public
- Contains a public and non-sensitive information about the cluster.
- It is accessible by all users.
- Used to Bootstrapping components to be added to cluster
- مثلا Node جديدة هتدخل الـ cluster ، كل دول محتاجين شوية معلومات أساسية عن الـ cluster ، في ConfigMap اسمها cluster-info في ال kube-public جواها معلومات زي API Server endpoint وcluster CA certificate اللي ال Node محتاجاهم عشان تعرف تكلم ال API server.
- ![[Pasted image 20260531172900.png]]


- ![[Pasted image 20260531172942.png]]
##### Kube-node-lease Namespace
- The `kube-node-lease` namespace is a default system namespace in Kubernetes that stores lightweight Lease objects used for node heartbeats and cluster health monitoring.
- Each node in the cluster has its own Lease object inside this namespace. This Lease acts as a heartbeat signal that tells the control plane the node is still alive and healthy.

- Why kube-node-lease Exists
- Originally, Kubernetes nodes used to send heartbeats by continuously updating their full `NodeStatus` objects.
- This approach caused several problems in large clusters because NodeStatus objects are relatively large and expensive to update frequently. Continuous updates created:
	- High load on the kube-apiserver
	- Increased etcd storage and write overhead
	- Additional network traffic
	- More cryptographic processing

- To solve this scalability issue, Kubernetes introduced the `kube-node-lease` namespace.
- Instead of updating the entire Node object, each node now updates a small Lease object, which is much lighter and more efficient.
- This significantly improves cluster scalability and reduces overall control plane overhead.

- How Node Leases Work?
	1. Heartbeat Updates
		- The `kubelet` running on each node periodically updates its Lease object inside the `kube-node-lease` namespace.
		- Specifically, it renews the: spec.renewTime field, this timestamp acts as the node heartbeat.
	 2. Failure Detection
		- The Kubernetes control plane continuously watches these Lease objects.
		- If a node stops renewing its lease within the expected time window, the control plane assumes the node may be unhealthy or unreachable.
		- Depending on the situation, the node is then marked as:
			- NotReady
			- Unreachable
			- Failed
	3. Self-Healing Actions
		- Once Kubernetes detects that a node is no longer healthy, self-healing mechanisms begin automatically.
		- Examples include:
		- Rescheduling Pods to healthy nodes
		- Replacing failed workloads
		- Maintaining the desired cluster state
- This allows Kubernetes to recover from node failures automatically.

> The `kube-node-lease` mechanism reduces the overhead of heartbeat communication while improving the scalability and responsiveness of Kubernetes clusters.
---
### Deployment Update and Rollout
- Any change inside the Pod template section of a Deployment (such as changing the image, environment variables, labels, or commands) triggers a new `rollout`.

- ==During the rollout==, Kubernetes creates a new `Deployment revision` that stores the updated configuration and keeps previous revisions for rollback purposes.

- The Deployment creates a new ReplicaSet based on the updated Pod template.

- ==The old ReplicaSet is usually not deleted immediately. Instead, it is scaled down while the new ReplicaSet is scaled up.==

- The new ReplicaSet creates new Pods with the updated configuration, while the ==old Pods are terminated gradually== according to the `rolling update strategy`.

- Keeping old ReplicaSets allows Kubernetes to roll back to a previous stable version if the new version fails.

- This process is called a ==Rolling Update== and ==helps achieve minimal or zero downtime during application updates.==
---
### Networking
- A **network plugin** in Kubernetes (often called a **CNI plugin**) is ==an external software component responsible for provision, configuration, and management of networking interfaces for Pods across a cluster==. Kubernetes purposely does not include a built-in networking implementation. Instead, it relies on these third-party plugins to fulfil the core Kubernetes network model requirements.

- ==Calico, Cilium, and Flannel== are three of the most widely used third-party Container Network Interface (CNI) plugins in **Kubernetes** clusters.
	-  Calico Uses Layer 3 routing via BGP (Border Gateway Protocol) and provides highly advanced network policy enforcement.
	- Cilium Leverages eBPF (Extended Berkeley Packet Filter) technology directly in the Linux kernel for ultra-high-performance routing, deep observability, and security filtering.
	- Flannel A lightweight overlay option that establishes basic VXLAN connectivity across nodes, making it ideal for dev environments or smaller setups.

- In a Kubernetes cluster, **`cni0`** is a virtual Linux network bridge created on a worker node by your CNI (==Container Network Interface==) plugin. It acts as a virtual switch, connecting Pods running on the same node to each other and to the rest of the cluster's network.

- What `cni0` Does ?
	- **Pod Communication:** It connects one end of a Pod's virtual ethernet (`veth`) pair to the host node's root network, allowing Pods to communicate locally on the node.
	- **Traffic Routing:** It forwards traffic from Pods to the node's physical interface (e.g., `eth0`) when a Pod needs to communicate with a service or a Pod on a different node.
	- **Gateway:** It often holds the default gateway IP address (e.g., `10.244.x.1`) for all Pods running on that specific node.

- In a Kubernetes cluster, **veth** (short for **Virtual Ethernet**) is ==a Linux kernel feature that acts like a virtual network cable used to connect a Pod to the worker node's network space through cni0==.

- How a veth Pair Works ?
	- A veth device is always created in an interconnected pair (a "veth pair"). Any network traffic that enters one end of the virtual cable immediately shoots out of the other end.
	- When a Container Network Interface (CNI) plugin creates a Pod, it configures the pair across two namespaces
	- **The Pod End (`eth0`)**: One end of the veth pair is pushed inside the Pod’s network namespace. Inside the Pod, this interface is renamed to `eth0` and is assigned the unique Pod IP address.
	- **The Host End (`vethXXXX`)**: The other end remains outside in the worker node's "root" network namespace. On the host, it appears with a randomly generated name like `veth01a2b3c` or `vethpl789`.

- 