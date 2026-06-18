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
- The `ansible.builtin.copy` module is used to copy files or text content from the control node to managed hosts.
- It can copy:
  - Local files using `src`
  - Inline text using `content`

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
