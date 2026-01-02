- Firewall is used to control the inbound and outbound traffic for the server
- Types of firewall:
	1. Network firewall (FortiGate for example).
	2. Host-based firewall (on the operating system).
### Firewall Architecture
- The Linux kernel provides the ==netfilter== framework for network traffic operations such as packet filtering, network address translation, and port translation.
- The ==nftables== packet classification framework builds upon the netfilter framework to apply firewall rules to network traffic.
- The ==firewalld service== is a dynamic firewall manager, and is the recommended front end to the nftables framework. The Red Hat Enterprise Linux 9 distribution includes the firewalld package.
- The ==firewalld service== simplifies firewall management by classifying network traffic into zones. ==A network packet's assigned zone depends on criteria such as the source IP address of the packet or the incoming network interface==. Each zone has its own list of ports and services that are either open or closed.
- ![[Pasted image 20260102164148.png]]
- In each zone, we can configure the source IP, ingress interface and the ports.
- The packet is assigned to only one zone.
- IP is assigned to one zone also.
### Work flow of the firewalld service
- When the packet arrives to the firewall, it checks its source IP, if it is associated with zone, the firewall assigns the packet to this zone, if the IP isn't associated with any zone, the firewall checks the ingress interface, if it is associated with zone, the firewall will assigns the packet to this zone, if the IP isn't associated with any zone neither the incoming interface, the firewall will assign the packet to the default zone.
- The default zone is often ==public==.
### Predefined Zones
- The firewalld service uses predefined zones, which you can customize. ==By default, all zones permit any incoming traffic which is part of an existing session initiated by the system (reply to the server request), and also all outgoing traffic (server reply)==. The following table details the initial zone configuration (trusted, home, work, internal, public).
	- The path of the predefined zones is */usr/lib/firewalld/zones*.
### Predefined Services
- The firewalld service includes a number of predefined configurations for common services, to simplify setting firewall rules. For example, instead of researching the relevant ports for an NFS server, use the predefined nfs configuration create rules for the correct ports and protocols. The following table lists the predefined service configurations that the firewalld service uses in its default configuration.
- The path of the predefined services is */usr/lib/firewalld/services*.
### Permanent vs Runtime configurations
- If we apply a new rule in a zone without using `--permanent` option, this rule will be applied directly on the kernel (netfilter) and it will be removed after the reboot of the system.
- When we use `--permanent` option the configuration is saved in */etc/firewalld/zones/[zone_name.xml]*, and we have to reload the service using `firewalld-cmd --reload` 
### Firewall commands
- If the firewalld service is stopped we use `firewalld-offline-cmd` for offline configuration.
- If the firewalld service is running we use `firewalld-cmd` for online configuration.
- We can add rule by its name using `firewall-cmd --permanent --add-service=ssh`, or by the port number using `firewall-cmd --permanent --add-port=22/tcp`
- 