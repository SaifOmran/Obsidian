- Outputs a full list of all managed hosts within the currently targeted inventory

```Ansible
ansible all --list-hosts
```

- Collects system information from target hosts  
  It gathers information such as:
  - OS distribution
  - IP address
  - Hostname
  - CPU details
  - Memory (RAM)
  - Network interfaces
  - Kernel version

```Ansible
ansible all -m gather_facts
```

