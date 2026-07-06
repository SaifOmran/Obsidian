- We add the machine’s public SSH key to GitHub so GitHub can verify that the machine making the `push` or `pull` owns the corresponding private key.  
  
- We should clone the repository using SSH instead of HTTPS to avoid entering username/password or token credentials repeatedly.  
  
- `eval` executes text output as a shell command.  
  
Example:  
  
```bash  
eval $(ssh-agent)  
```  
  
This starts the SSH agent and configures the current shell environment to communicate with it.  
  
The SSH agent stores SSH authentication data such as:  
- private keys  
- decrypted passphrases in memory  
  
so you do not need to enter the passphrase every time.  
  
- To add your private key to the SSH agent:  
  
```bash  
ssh-add  
```  
  
or explicitly:  
  
```bash  
ssh-add ~/.ssh/id_ed25519  
```  
  
Then:  
- enter the passphrase once (if the key has one)  
- the agent keeps the key loaded for future SSH/Git operations.

- You can make an alias and put it in `.bashrc` for easier management
```bash
alias ssha='eval $(ssh-agent) && ssh-add'
```

 ---
 ```Ansible
 ansible all -m apt -a  update_cache=true --become --ask-become-pass
 ```

- `--become` -> means execute the command with root privilege using sudo
- `--ask-become-pass` -> means to ask about the sudo password
---
### The when conditional
In Ansible, the `when` keyword is ==a conditional statement that controls whether a task or block of tasks executes==. It evaluates a raw [Jinja2 expression](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_conditionals.html); if the result is `true`, the task runs, and if it is `false` or undefined, it skips.

- Basic Syntax
```yaml
- name: Install Apache on Ubuntu
  ansible.builtin.apt:
    name: apache2
    state: present
  when: ansible_distribution == "Debian"
```

- We can match on multiple values
```yaml
- name: Install Apache on Ubuntu
  ansible.builtin.apt:
    name: apache2
    state: present
  when: ansible_distribution in ["Debian", "Ubuntu"]
```

- Also we can use `and`, `or`
- Common Use Cases
	- **Host Facts:** Running tasks conditionally based on gathered facts (e.g., OS type, memory, or architecture).
	- **Registered Variables:** Checking the output of a previous task before deciding what to do next.

---
### Variables
- `host_vars` and `group_vars`
  - Loaded automatically by Ansible.
  - Based on inventory host/group names.

- `vars_files`
  - Must be explicitly referenced in the playbook.
  - Used to load variables from arbitrary files.
Example:
```yaml
vars_files:
  - vars/common.yml
```
Use `group_vars`/`host_vars` for inventory-specific variables, and `vars_files` for reusable playbook variables.

---
### Ansible Tags
- Tags are labels assigned to tasks, blocks, plays, or roles.
- They allow you to run or skip specific parts of a playbook instead of executing the entire playbook.

##### Assigning Tags
```yaml
---
- hosts: webservers
  tasks:
    - name: Install Nginx
      yum:
        name: nginx
        state: present
      tags:
        - packages
        - web

    - name: Copy Nginx Configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      tags:
        - configuration
        - web
```

##### Running Tags
Run only tasks with a specific tag:
```bash
ansible-playbook site.yml --tags "packages"
```

Run multiple tags:
```bash
ansible-playbook site.yml --tags "packages,configuration"
```

##### Skipping Tags
Skip tasks with a specific tag:
```bash
ansible-playbook site.yml --skip-tags "configuration"
```

##### Listing Tags
Show all available tags in a playbook:
```bash
ansible-playbook site.yml --list-tags
```

#### Special Tags
###### always
- Task always runs by default.
- Can be skipped using:
```bash
ansible-playbook site.yml --skip-tags always
```

###### never
- Task never runs by default.
- Runs only when explicitly requested:
```bash
ansible-playbook site.yml --tags never
```

###### tagged
- Runs only tasks that have at least one tag.
```bash
ansible-playbook site.yml --tags tagged
```

###### untagged
- Runs only tasks that do not have any tags.
```bash
ansible-playbook site.yml --tags untagged
```

###### all
- Runs all tasks in the playbook (default behaviour).
```bash
ansible-playbook site.yml --tags all
```
---
### files directory
In Ansible, a `files/` directory is a designated folder used to store static assets such as configuration files, shell scripts, or certificates—that do not require variable substitution or processing. When you use relative paths in certain tasks, Ansible automatically searches this directory to find the source files.

So, when we use the relative path in `copy` package in the next section, we notice that Ansible searches for the source file in `files` directory. 

---

### Ansible Copy Module
##### Ansible `files/` Directory

- The `files/` directory is a special **Ansible convention** used to store static files such as:
  - Configuration files
  - Shell scripts
  - Certificates
  - HTML files
  - Any file that does **not** require variable substitution

- Modules such as `copy` automatically search the `files/` directory when the `src` parameter is specified using a **relative path**.

Example project structure:

```text
project/
├── playbook.yml
└── files/
    └── default_site.html
```

Playbook:

```yaml
- name: Copy HTML file
  copy:
    src: default_site.html
    dest: /var/www/html/index.html
```

- In this example, Ansible automatically looks for:

```text
files/default_site.html
```

because `src` uses a relative path.

> **Note:** The `files/` directory is intended for static files. If the file requires variables or Jinja2 expressions, use the `template` module with the `templates/` directory instead.


#### Common Parameters

| Parameter | Description |
|------------|------------|
| `src` | Source file on the control node |
| `dest` | Destination path on the managed host |
| `content` | Text content to write directly to a file |
| `owner` | File owner |
| `group` | File group |
| `mode` | File permissions |

#### Copy a Local File
```yaml
- name: Copy local configuration file
  ansible.builtin.copy:
    src: /etc/configs/app.conf
    dest: /etc/myapp/app.conf
    owner: root
    group: root
    mode: '0644'
```

#### Create a File Using Inline Content
```yaml
- name: Create a custom banner file
  ansible.builtin.copy:
    content: "Welcome to this server managed by Ansible.\n"
    dest: /etc/motd
    mode: '0644'
```

### Explanation
- Creates the file `/etc/motd`.
- Writes the provided text directly into the file.
- No source file is required.
- `\n` adds a new line at the end of the text.
---
### Ansible `service` Module
- The `service` module is used to manage system services on managed hosts.
- It can start, stop, restart, reload, enable, or disable services.
- It provides a generic interface that works across different Linux distributions (systemd, SysVinit, Upstart).

##### Common Parameters

| Parameter | Description |
|-----------|-------------|
| `name` | Service name |
| `state` | Desired service state |
| `enabled` | Enable or disable service at boot |

##### Service States

| State | Description |
|-------|-------------|
| `started` | Start the service if it is not running |
| `stopped` | Stop the service if it is running |
| `restarted` | Restart the service |
| `reloaded` | Reload the service configuration (if supported) |

---
##### Start a Service
```yaml
- name: Start Apache
  service:
    name: httpd
    state: started
```
---
##### Stop a Service
```yaml
- name: Stop Apache
  service:
    name: httpd
    state: stopped
```
---
##### Restart a Service
```yaml
- name: Restart Apache
  service:
    name: httpd
    state: restarted
```
---
##### Reload a Service
```yaml
- name: Reload Apache configuration
  service:
    name: httpd
    state: reloaded
```
---
##### Enable a Service at Boot
```yaml
- name: Enable Apache
  service:
    name: httpd
    enabled: yes
```
---
##### Start and Enable a Service
```yaml
- name: Start and enable Apache
  service:
    name: httpd
    state: started
    enabled: yes
```
---
##### Stop and Disable a Service
```yaml
- name: Stop and disable Apache
  service:
    name: httpd
    state: stopped
    enabled: no
```
---
##### Notes
- `started` starts the service only if it is not already running.
- `restarted` always restarts the service.
- `reloaded` reloads the configuration without fully restarting the service (if supported).
- `enabled: yes` configures the service to start automatically during system boot.
- The `service` module is distribution-independent and automatically uses the appropriate service manager (e.g., systemd).
---
### Ansible `lineinfile` Module

- The `lineinfile` module is used to add, modify, or remove a single line in a text file.
- It is commonly used to update configuration files without replacing the entire file.

##### Common Parameters

| Parameter | Description |
|-----------|-------------|
| `path` | Path to the target file |
| `line` | Line to add or replace |
| `regexp` | Regular expression used to find an existing line |
| `state` | `present` (default) or `absent` |
| `create` | Create the file if it does not exist |
| `backup` | Create a backup before modifying the file |
| `insertafter` | Insert the line after a matching line |
| `insertbefore` | Insert the line before a matching line |

##### Add a Line
```yaml
- name: Add a DNS server
  lineinfile:
    path: /etc/resolv.conf
    line: "nameserver 8.8.8.8"
```

##### Replace a Line
```yaml
- name: Change SSH port
  lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^#?Port'
    line: 'Port 2222'
```

##### Remove a Line
```yaml
- name: Remove a DNS server
  lineinfile:
    path: /etc/resolv.conf
    line: "nameserver 8.8.8.8"
    state: absent
```

##### Create a File
```yaml
- name: Create a configuration file
  lineinfile:
    path: /tmp/app.conf
    line: "enabled=true"
    create: yes
```

##### Create a Backup
```yaml
- name: Modify SSH configuration
  lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^PermitRootLogin'
    line: 'PermitRootLogin no'
    backup: yes
```

##### Notes
- Modifies **one line** at a time.
- `regexp` searches for an existing line to replace.
- If no matching line is found, the line is added.
- `state: absent` removes the matching line.
- Idempotent: running the task multiple times does not create duplicate lines.

##### Comparison

| Module | Use Case |
|---------|----------|
| `copy` | Copy an entire static file |
| `template` | Copy a file with Jinja2 variables |
| `lineinfile` | Add, modify, or remove a single line |
| `replace` | Replace multiple matching text patterns |
| `blockinfile` | Insert or manage a block of text |

---
### Ansible `register` Parameter
- The `register` parameter is used to save the output of a task into a variable.
- The registered variable can be used in later tasks for debugging, conditions, or extracting command results.

##### Basic Syntax
```yaml
- name: Check uptime
  command: uptime
  register: uptime_result
```

##### Access the Output
```yaml
- name: Display command output
  debug:
    var: uptime_result.stdout
```

##### Common Registered Attributes

| Attribute | Description |
|-----------|-------------|
| `stdout` | Standard output as a string |
| `stdout_lines` | Standard output as a list of lines |
| `stderr` | Error output |
| `stderr_lines` | Error output as a list |
| `rc` | Return (exit) code |
| `changed` | Whether the task changed the system |
| `failed` | Whether the task failed |

##### Example
```yaml
- name: Check Apache status
  command: systemctl is-active httpd
  register: apache_status

- name: Display status
  debug:
    msg: "Apache status is {{ apache_status.stdout }}"
```

##### Use with Conditions
```yaml
- name: Check if Apache is running
  command: systemctl is-active httpd
  register: apache_status
  ignore_errors: yes

- name: Start Apache if it is not running
  service:
    name: httpd
    state: started
  when: apache_status.stdout != "active"
```

##### Check the Exit Code
```yaml
- name: Verify file exists
  command: ls /tmp/test.txt
  register: file_check
  ignore_errors: yes

- name: Display exit code
  debug:
    msg: "Exit code: {{ file_check.rc }}"
```

##### Notes
- `register` stores the output of a task in a variable.
- The registered variable is available only during the current playbook execution.
- It is commonly used with `when` conditions and the `debug` module.
- `register` does not create a permanent variable; it exists only for the duration of the play.
---
### Ansible `user` Module
- The `user` module is used to manage user accounts on managed hosts.
- It can create, modify, or delete users, set passwords, groups, shells, home directories, and account expiration.
##### Common Parameters

| Parameter | Description |
|-----------|-------------|
| `name` | Username |
| `state` | `present` or `absent` |
| `password` | User password (hashed) |
| `shell` | Login shell |
| `home` | Home directory |
| `create_home` | Create the home directory |
| `groups` | Supplementary groups |
| `append` | Add user to groups without removing existing ones |
| `uid` | User ID |
| `comment` | User description |
| `expires` | Account expiration date (Unix timestamp) |

---
##### Create a User
```yaml
- name: Create user saif
  user:
    name: saif
    state: present
```
---
##### Create a User with a Home Directory and Shell
```yaml
- name: Create user saif
  user:
    name: saif
    shell: /bin/bash
    create_home: yes
    state: present
```
---
##### Add User to Groups
```yaml
- name: Add user to groups
  user:
    name: saif
    groups:
      - wheel
      - docker
    append: yes
```

> `append: yes` adds the user to the specified groups without removing existing group memberships.

---
##### Set a Password
```yaml
- name: Set user password
  user:
    name: saif
    password: "{{ 'P@ssw0rd' | password_hash('sha512') }}"
```

> The password must be **hashed**. Do not use plain text passwords.
---
##### Create a User with a Specific UID
```yaml
- name: Create user with UID
  user:
    name: saif
    uid: 1050
```
---
##### Remove a User
```yaml
- name: Remove user
  user:
    name: saif
    state: absent
```
---
##### Remove a User and Home Directory
```yaml
- name: Remove user and home directory
  user:
    name: saif
    state: absent
    remove: yes
```
---
##### Lock a User Account
```yaml
- name: Lock user account
  user:
    name: saif
    password_lock: yes
```
---
##### Unlock a User Account
```yaml
- name: Unlock user account
  user:
    name: saif
    password_lock: no
```
---
##### Notes
- `state: present` creates the user if it does not exist.
- `state: absent` removes the user account.
- `remove: yes` also deletes the user's home directory.
- `append: yes` preserves existing group memberships.
- Passwords must be stored as **hashed values**, not plain text.
- The module is idempotent; running it multiple times does not recreate or modify the user unless changes are required.