### Pod creation
- When we make a request to create a `Pod`, `kubectl` sends the request to the `kube-apiserver`. The API server performs authentication, authorization, and admission checks, then records the desired state in `etcd`. Next, the `scheduler` selects the best `node`, and the `kubelet` on that node instructs the container runtime to pull the image, set up the pod sandbox (`pause container`) and networking, and start the containers. Finally, it updates the `Pod`'s status back in the API server.
### Pause container
- In docker, each container has it is own namespace and network name space, so we see that each container has unique IP.
- In k8s, we have the concept of Pods, so we can have multiple container in same pod, so they share same pod IP and can communicate as a localhost on different ports
- So, who will manage this shared environment ? `pause container`.
- `pause container` -> is a small container that firstly runs in the pod to manage the pod environment, especially to manage the network namespace, and it takes the pod IP, then all other containers join this namespace, and it is running as long as the pod is running but it is almost not consuming any resources.
### Kube-proxy
- It is the component on the worker node that is responsible about routing the traffic from `service` to the `pods`, simply it handles service-to-pod communication.
                            ![[Pasted image 20260523002604.png]]
- Pod-to-Pod communication is handled by `CNI` (container network interface) and Linux kernel, CNI makes interfaces for pods and the routing tables, then Linux kernel forwarding the packets based on the routing table.
- If pods are on different nodes, `CNI` will handle the traffic using overlay encapsulation (VXLAN).
- `CNI` is responsible to assign IPs to the pods.
### Container runtime
- Component that is responsible for creating the containers, hence pods.
- It starts and stop the containers.
- It pulls the image from the image registry.
- k8s supports different container runtime like docker engine, containerd, CRI-O.
- ==k8s has standard container runtime called CRI (container runtime interface).==

