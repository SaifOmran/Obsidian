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
- When we use `--permanent` option the configuration is saved in */etc/firewalld/zones/[zone_name.xml]*, and we have to reload the service using `firewall-cmd --reload` (by logic as the files under */etc* are read while booting up, and reload command make the system re-read these file while services are running).
### Firewall commands

##### Zone Management

| Command                                | Description                            |
| -------------------------------------- | -------------------------------------- |
| `firewall-cmd --get-default-zone`      | Show the default zone                  |
| `firewall-cmd --set-default-zone=ZONE` | Set the default zone                   |
| `firewall-cmd --get-zones`             | List all available zones               |
| `firewall-cmd --get-active-zones`      | Show active zones and their interfaces |
| `firewall-cmd --zone=ZONE --list-all`  | Show all settings of a zone            |

---
##### Interface Management

| Command                                                         | Description                          |
| --------------------------------------------------------------- | ------------------------------------ |
| `firewall-cmd --zone=ZONE --add-interface=IFACE`                | Add interface to a zone (Runtime)    |
| `firewall-cmd --zone=ZONE --change-interface=IFACE`             | Move interface to another zone       |
| `firewall-cmd --permanent --zone=ZONE --change-interface=IFACE` | Move interface permanently           |
| `firewall-cmd --zone=ZONE --remove-interface=IFACE`             | Remove interface from a zone         |
| `firewall-cmd --get-zone-of-interface=IFACE`                    | Show which zone an interface belongs |

---
##### Services

| Command | Description |
|---------|-------------|
| `firewall-cmd --get-services` | List all predefined services |
| `firewall-cmd --list-services` | List allowed services in the default zone |
| `firewall-cmd --zone=ZONE --list-services` | List services in a specific zone |
| `firewall-cmd --add-service=http` | Allow a service (Runtime) |
| `firewall-cmd --remove-service=http` | Remove a service (Runtime) |
| `firewall-cmd --permanent --add-service=http` | Allow a service permanently |
| `firewall-cmd --permanent --remove-service=http` | Remove a service permanently |

---
##### Ports

| Command | Description |
|---------|-------------|
| `firewall-cmd --add-port=8080/tcp` | Open a TCP port (Runtime) |
| `firewall-cmd --remove-port=8080/tcp` | Close a TCP port |
| `firewall-cmd --permanent --add-port=8080/tcp` | Open a port permanently |
| `firewall-cmd --permanent --remove-port=8080/tcp` | Close a port permanently |
| `firewall-cmd --list-ports` | List open ports |

---
##### Runtime & Permanent

| Command | Description |
|---------|-------------|
| `firewall-cmd --reload` | Reload permanent configuration into runtime |
| `firewall-cmd --runtime-to-permanent` | Save current runtime configuration permanently |

---
##### Information

| Command | Description |
|---------|-------------|
| `firewall-cmd --state` | Check if firewalld is running |
| `firewall-cmd --version` | Show firewalld version |
| `firewall-cmd --list-all` | Show all settings of the default zone |
| `firewall-cmd --zone=ZONE --list-all` | Show all settings of a specific zone |

---
### Important Notes

##### Runtime vs Permanent

| Runtime | Permanent |
|----------|-----------|
| Active immediately | Saved on disk |
| Lost after reload/restart | Survives reboot |
| No `--permanent` | Uses `--permanent` |

Example:

```bash
firewall-cmd --add-service=http
```

Runtime only.

```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
```

Permanent + applied immediately after reload.

---

### Services vs Ports

Prefer **Services** whenever possible.

```bash
firewall-cmd --add-service=http
```

instead of

```bash
firewall-cmd --add-port=80/tcp
```

Because a **Service** already knows:
- Port(s)
- Protocol(s)

---

### Default Zone

If you don't specify `--zone`, the command applies to the **Default Zone**.

Example:

```bash
firewall-cmd --permanent --add-service=ssh
```

Equivalent to:

```bash
firewall-cmd --permanent --zone=<default_zone> --add-service=ssh
```

Check the default zone:

```bash
firewall-cmd --get-default-zone
```

---

### Interface Rule

Each **Network Interface** belongs to **one Zone only**.

```
ens33 → public
ens34 → internal
```

Traffic entering through an interface is filtered using the rules of its assigned zone.

---
### Recommended RHCSA Workflow

```bash
# 1. Add the rule
firewall-cmd --permanent --add-service=http

# 2. Apply changes
firewall-cmd --reload

# 3. Verify
firewall-cmd --list-all
```

### Rich Rules
##### Overview
- **Rich Rules** provide advanced firewall rules beyond simple services and ports.
- Use them to:
  - Allow/Deny specific IP addresses or networks.
  - Filter traffic based on services or ports.
  - Accept, Reject, or Drop connections.

> **Use Services/Ports for simple rules.**  
> **Use Rich Rules for advanced filtering.**
---
##### Common Commands

| Command | Description |
|---------|-------------|
| `--add-rich-rule='RULE'` | Add a rich rule (Runtime) |
| `--remove-rich-rule='RULE'` | Remove a rich rule |
| `--permanent --add-rich-rule='RULE'` | Add a rich rule permanently |
| `--list-rich-rules` | List all rich rules |

---
##### Syntax
```bash
firewall-cmd --add-rich-rule='RULE'
```
---
##### Examples
###### Allow SSH only from one IP
```bash
firewall-cmd --permanent \
--add-rich-rule='rule family="ipv4" source address="192.168.1.10" service name="ssh" accept'
```
---
###### Block one IP
```bash
firewall-cmd --permanent \
--add-rich-rule='rule family="ipv4" source address="192.168.1.100" drop'
```
---
##### Rule Actions

| Action | Description |
|---------|-------------|
| `accept` | Allow traffic |
| `reject` | Reject traffic and send a response |
| `drop` | Silently discard traffic |

---
##### Notes
- If `--zone` is omitted, the rule is added to the **default zone**.
- Use `--permanent` to save the rule permanently.
- Run `firewall-cmd --reload` after adding permanent rules.
- Rich Rules can filter by:
  - Source IP
  - Source Network
  - Service
  - Port
  - Protocol
---
##### Quick Revision

| Task | Command |
|------|---------|
| Add rich rule | `--add-rich-rule='RULE'` |
| Remove rich rule | `--remove-rich-rule='RULE'` |
| List rich rules | `--list-rich-rules` |
| Save permanently | `--permanent` |
| Apply changes | `--reload` |

> **Memory Tip**
>
> - **Service** → Allow a service for everyone.
> - **Port** → Open a specific port.
> - **Rich Rule** → Apply conditions (IP, subnet, service, port, action).