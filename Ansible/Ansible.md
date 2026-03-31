### What is Ansible?
- Ansible is an open-source IT automation tool used for configuration management, application deployment, and infrastructure provisioning. It simplifies complex tasks by using human-readable YAML "playbooks" to orchestrate workflows, allowing systems to be managed securely without agents on target nodes, typically connecting via SSH.
### Ansible Architecture
- ![[Pasted image 20260323210144.png]]

- Ansible is agentless tool, there is no need to install daemon on the target host.
### Ansible configuration files
- All config files are in /etc/ansible
- ansible.cfg (/etc/ansible/ansible.cfg) -> the **primary configuration file** for Ansible, used to define global settings and customize how the automation tool behaves during execution.
- ![[Pasted image 20260324060429.png]]
- hosts (/etc/ansible/hosts) -> The Ansible hosts file, often referred to as the **inventory file**, is a text file that lists the remote nodes (servers, switches, routers) managed by Ansible.
- inventory -> list of managed nodes (hosts) that the automation control node communicates with and it can be in any path, but should be defined in the ansible.cfg file.
- ![[Pasted image 20260324060528.png]]
### Commands
#### Ad-hoc commands
- These commands are used for testing or debugging, as they can be run quickly.
```Linux
ansible <hosts> -m <module> -a "<arguments>" [options]
```

| options |                   |
| ------- | ----------------- |
| `-i`    | inventory file    |
| `-u`    | username          |
| `-k`    | ask password      |
| `-b`    | become (sudo)     |
| `-K`    | ask sudo password |

### How Ansible Finds the Inventory File
- Ansible searches for the inventory file in the following order (highest priority first)
1. **Command Line Option**
```Linux
ansible all -m ping -i inventory
```
2. **Environment Variable**
	- Export ANSIBLE_INVENTORY=./inventory
3. **ansible.cfg File**
	- Ansible searches for ansible.cfg itself in: 
	1. Current directory.
	2. Home directory of logged user.
	3. Default file -> /etc/ansible/ansible.cfg
4. **Default Location** -> /etc/ansible/hosts

---
### Playbooks
- An Ansible playbook is a YAML file containing a blueprint of automation tasks, used to define policies or configurations for managed nodes. They allow complex, multi-machine deployments to be automated, repeated, and shared easily. Playbooks map specific hosts to tasks, which are executed in order to achieve a desired state on remote systems.
- Playbook -> Plays -> Tasks -> Modules.
---
### Modules
- To list all docs and modules on the control node
```Linux
ansible-doc -l
```

- To grep specific module
```Linux
ansible-doc -l | grep <name>
```

- To see the doc of the module and its syntax
```Linux
ansible-doc <module_name>
```
##### command
- The [ansible.builtin.command](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/command_module.html) module is used to execute basic commands on target hosts without processing them through a shell.
##### shell
- The [ansible.builtin.shell](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/shell_module.html) module is a core Ansible tool used to execute commands on target nodes through a shell (defaulting to `/bin/sh`), allowing for features like pipes (`|`), redirections (`>`), and environment variable expansion. While similar to the `command` module, ==the `shell` module is distinct in that it interprets shell operators, making it ideal for complex command sequences(redirection and pipelines) but less secure against command injection than the `command` module.==
##### debug
- The [ansible.builtin.debug](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/debug_module.html) module is a primary tool for troubleshooting playbooks by printing messages, variables, and expression results during execution.
##### yum_repository
- Used for adding, modifying, or removing YUM repository configuration (`.repo`) files.
![[Pasted image 20260324230918.png]]
