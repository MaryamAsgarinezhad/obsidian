Ansible is an open-source automation tool used for configuration management, application deployment, task automation, and orchestration. It allows you to automate the management of your infrastructure, including servers, networks, and cloud resources, using a simple and human-readable language.

Key features of Ansible include:

1. **Agentless**: Ansible operates over SSH or PowerShell, so it does not require any agent to be installed on managed hosts. This simplifies setup and management compared to agent-based solutions.
    
2. **Playbooks**: Ansible uses YAML-based playbooks to define automation tasks. Playbooks describe the desired state of your infrastructure and are used to automate complex tasks or workflows.
    
3. **Idempotency**: Ansible ensures that the desired state of your infrastructure is achieved, regardless of the current state. This means you can run the same playbook multiple times without causing unintended changes.
    
4. **Modules**: Ansible provides a large collection of built-in modules that can be used to interact with various systems and services. These modules abstract the underlying complexity of tasks like package management, file manipulation, and service management.
    
5. **Inventory**: Ansible uses an inventory file to define the hosts on which tasks will be executed. The inventory can be static or dynamic, allowing you to manage different sets of hosts easily.
    
6. **Roles**: Roles are a way to organize and reuse Ansible playbooks and related files. Roles can encapsulate a set of tasks, handlers, variables, and other components, making it easier to manage complex automation tasks.
    

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

------------------------

Ansible purpose: Provision Servers

![[Pasted image 20240511181428.png]]

Ansible should not be neessarily installed on every server, but only on the 'Ansible Control Host' which can be your local system.

- Open SSH -->  used to do administration things on a remote 