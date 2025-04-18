Ansible is an open-source automation tool used for configuration management, application deployment, task automation, and orchestration. It allows you to automate the management of your infrastructure, including servers, networks, and cloud resources, using a simple and human-readable language.

Key features of Ansible include:

1. ==**Agentless**==: Ansible operates over SSH or PowerShell, so it does not require any agent to be installed on managed hosts. This simplifies setup and management compared to agent-based solutions.
    
2. **Playbooks**: Ansible uses YAML-based playbooks to define automation tasks. Playbooks describe the desired state of your infrastructure and are used to automate complex tasks or workflows.
    
3. **Idempotency**: Ansible ensures that the desired state of your infrastructure is achieved, regardless of the current state. This means you can run the same playbook multiple times without causing unintended changes.
    
4. **Modules**: Ansible provides a large collection of built-in modules that can be used to interact with various systems and services. These modules abstract the underlying complexity of tasks like package management, file manipulation, and service management.
    
5. **Inventory**: Ansible uses an inventory file to define the hosts on which tasks will be executed. The inventory can be static or dynamic, allowing you to manage different sets of hosts easily. We can also define variables configuration purposes in the inventory files.
    
6. **Roles**: Roles are a way to organize and reuse Ansible playbooks and related files. **Roles can encapsulate a set of** tasks, handlers, variables, and other components, making it easier to manage complex automation tasks. ==They can contain .cong.j2 files to manipulate server configs using Lua language.==
   
   Tip: Playbooks execute linux commands on the server, but .conf.j2 files define Nginx server configurations and APIs using Lua.

Overall, Ansible simplifies the process of automating infrastructure management tasks, making it an essential tool for DevOps teams and system administrators.

-------------------------------------------------

```LUA
  
function validate_hash(key, uri_hash, exp_time, uri)  
  
   local secret_str = {}  
   table.insert(secret_str, exp_time)  
   table.insert(secret_str, uri)  
   table.insert(secret_str, key)  
  
   if ngx.var.secv ~= nil and ngx.var.secv ~= "" then  
      table.insert(secret_str, ngx.var.host)  
   end  
  
   local hash = ngx.encode_base64(ngx.md5_bin(table.concat(secret_str, ' ')))  
   hash = string.gsub(hash, '=', '')  
   hash = string.gsub(hash, '/', '_')  
   hash = string.gsub(hash, '+', '-')  
  
   return hash == uri_hash  
  
end
```

```shell
ansible-playbook playbooks/openresty.yml -i inventories/dev/ --ask-become-pass --limit cachenode --tags config
```

This runs tasks tagged with 'config'.
Applies on inventories in -i inventories/dev/, and applies only on cachenodes.

------------------------

Ansible purpose: Provision Servers

![[Pasted image 20240511181428.png]]

Ansible should not be necessarily installed on every server, but only on the 'Ansible Control Host' which can be your local system.

- Open SSH -->  used to do administration things on a remote serer, and ansible also uses this.
- ![[Pasted image 20240511184533.png]]
Generating an SSH key:
```shell
ssh-keygen -t ed25519 -C 'meta data about the key'
```
- more secure kind of an ssh key
- All ssh keys are stored in the .ssh folder of the pc.
- After the above command, its asks the location for storing the key and a pass phrase which makes the conection much more secure.

- SSH keys **automate** connection to remote servers without asking any password. By adding a passphrase to the key, you should enter it on every connection.

See the content of ssh keys (here public):
```shell
cat .ssh/'name of the key'.pub
```

**Copy your pub key to the server:**
```shell
ssh-copy-id -i ~/.ssh/'name of the key'.pub 'ip address of the target server'
```

- The tilde (`~`) character at the beginning of a file path is a shorthand representation for the current user's home directory.

We can have multiple keys on our workstation for multiple purposes (connection to different servers):
![[Pasted image 20240511192811.png]]

**SSH agent** is a process that can run on the background and cache your ssh pass phrase. You can gets id and check its running status.
- ![[Pasted image 20240511193213.png]]
- Now that its running, add the identity:
- ![[Pasted image 20240511193401.png]]
- Aliasing (temporary):
- ![[Pasted image 20240511193624.png]]
- Aliasing (permanent):
```shell
nano .bashrc
```
![[Pasted image 20240511193813.png]]

--------------------------------

1- Install ansible client.

2- Create the **inventory file**, for storing IP addressed of the hosts we want to configure and manage. There we write those IPs along with their domain names (if we have configured a DNS to figure them out)

3- Run an ansible command to connect to the hosts:
```shell
ansible all --key-file 'path to the ssk key directory' -i 'inventory file name' -m ping
```

- -m ping: this command specifies the module which is ping, used to connect to each of the servers we defined, and check their status.

4- Automate ping configs by creating the config file. **This file is read by ansible whenever you run it.**

- Add default confings to that file:
- ![[Pasted image 20240512120638.png]]
- Shortened command:
```shell
ansible all -m ping
```

- Get the list of hosts using:
```shell
ansible all --list-hosts
```

 - When ansible connects to a server, it pulls a list of information about that server including its processors, os, env vars,... . Get those info using the below command:
```shell
ansible all -m gather-facts --limit 'ip of the host you want to limit your request to'
```

------------------------------------------------

 Modifying configs: For modification purposes, you need 'become' permission (just as you need sudo command for some commands on your local server). For example:
 
 ```shell
 ansible all -m apt -a update_cache=true --become --ask-become-pass
```
 - '-m apt' gives you the access to the apt module to modify its parameter, update_cache.
 - '--ask-become-pass' asks you the password of the 'become'

Install a package (vim-nox) here on all of your servers:

```shell
ansible all -m apt -a name=vim-nox --become --ask-become-pass
```

- You can go to the servers and see if it is installed by :
```shell
  apt search vim-nox
```

------------------------------------------------

Create and run the first playbook.
Playbooks carryout the commands we want to run on our servers.

1- Create the playbook yaml file:

```shell
vim install_apache.yml
```
 
 - YAML, which stands for "YAML Ain't Markup Language" (a recursive acronym), is a human-readable data serialization format. It is commonly used for configuration files and in applications where data is being stored or transmitted. YAML is designed to be more readable and user-friendly than other data serialization formats such as XML or JSON, although it can serve similar purposes.
 
2- Config example:

- Define the general configs at the beginning.
- Write each task separatly with its name and variables in each 'task' part.
- ![[Pasted image 20240513151823.png]]

3- Run the playbook against ansible servers: (No need for )

```shell
ansible-playbook --ask-become-pass 'name of the playbook'
```
- Then the settings will be applied to our servers.

4- Check the ansible output after running commands against servers. Check the fields like **changed, unreachable, failed, skipped, rescued**, ... . 

----------------------------------------

'When' in playbooks:

- Run the task in specific conditions:  
![[Pasted image 20240513163336.png]]

- Get the information about servers using ''gather_facts" module to **help you write your when conditions**. Consider the parameters it gives you to filter your commands, like 'ansible_product name'.

-----------------------------------------

Improve your playbook:

- Variables that we use in every playbook are already defined in the inventories.
- Use specific Ansible modules to write more flexible playbooks.

Create server groups:

- ![[Pasted image 20240527173150.png]]
- ![[Pasted image 20240527173531.png]]
-------------------------------------

Manage files:

The Ansible '**copy**'/'template' module is used to move a raw/executed(jinja) file into the server.
- ![[Pasted image 20240527184206.png]]

The **unarchive** module in Ansible **helps unpack compressed archives** like tar.gz, zip, and others on remote machines. This module offers convenience and consistency in managing deployments and configurations that involve archived files. Functionality: Unpacks archives on remote machines after optionally copying them from the local system.
- ![[Pasted image 20240527185900.png]]

Use Ansible 'lineinfile' module to change a line in a file on a server:

The 'regexexp' is used to search for lines that start with that regex to be changed.
- ![[Pasted image 20240527191527.png]]

-------------------------------------------

Manage services:

Use Ansible 'service' module to perform systemd commands on the servers:
- ![[Pasted image 20240527191156.png]]

Restart a service on the server when a line in a file is changed:
==(**when**: httpd.changed  ,   **register**: httpd )==
'register' module stores the changes made.
- ![[Pasted image 20240527191851.png]]

--------------------

Manage (server's os) users:

Use Ansible user module to add a new user to a group:
- ![[Pasted image 20240527192921.png]]

Use Ansible 'authorized_key' add the new user's ssh key (makes the user able to log into the server):
- ![[Pasted image 20240527193501.png]]

Tip: 'changed_when: false' makes the task not to be considered as a change when it changes!

-------------------------------

Roles:

write the tasks of the previous playbooks in distinct task files like this:
![[Pasted image 20240601110607.png]]

- The task files need to be places in the directory **==named to specifiy the role we want to run==** (like db_servers), which we define in the role files for.
- the directory is like: roles/name_of_the_role/tasks/**main.yml**
![[Pasted image 20240601111714.png]]

- The above code is located in a playbook that we run like "ansible-playbook playbook_name"
- Here the ansible ==looks for a directory called 'roles'== and ==looks for the name we specified for in the '-roles' section in that file.== Then it looks for a ==task folder inside==, and the **==main.yml==** file inside the task folder.
- It knows the host groups based on the inventory file.

-----------------------------------

Host variables:

We create a new directory to store that files that contain variables (here host_vars/), and then create the host.yml file:
![[Pasted image 20240601113433.png]]

Then create the host variables in it:
![[Pasted image 20240601113601.png]]
==We will use these in our task-books.== Since we defined these vars, we are able to use them like this in our playbooks:
![[Pasted image 20240601114213.png]]

-------------------------------

Handlers:

One way to restart the server in case anything is changed is this:
![[Pasted image 20240601114842.png]]

but it does not handle multiple changes, just the change of the corresponding '==register==' name. Handlers do this in an effective way.
Make a new **directory called handlers in the location of tasks, and create a main.yml file in it**:
![[Pasted image 20240601120600.png]]

==Then it will be triggered by the 'notify: restart_apache' line in each task:==
![[Pasted image 20240601120718.png]]

-----------------------------------------

Templates:

Make a new **directory called templates in the location of tasks (base tasks in most of the times).** Then you can add a config file (from local server and remote host), like the sshd_config to this directory in a jinja2 format:
![[Pasted image 20240601130425.png]]
- jinja2 is the default type of template files in ansible.

1- Add variables to those files from our 'host variables'. Or try any other way to create a template file with variables.
2- Add a template command (used to copy template files with variables) to your role tasks:
![[Pasted image 20240601132130.png]]

