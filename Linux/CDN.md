1. Create `username` and `password` to get token in **prod** and **dev**
   Curl the signup ednpoint with corresponding body 
   
2. Create new space
   config file in code
   
3. Add mapping for `ceph`
   mapping for the new teams original domain and abbreviated domain

-----------------------------------

1- For youtube and internet links (not the ceph) ones, administrators create 'username' and 'password' for each team (using /signup endpoint), giving them a token making them able to call the token application to **receive temporary keys** to make secure hashes to add to their links. 

2- **cdn.yml**
  
This configuration file is likely used by ==system administrators== to set up and maintain a CDN infrastructure tailored for a large organization with **multiple teams** and varied requirements regarding content delivery and caching. It allows for detailed control over caching strategies, network configurations, and resource allocation, essential for optimizing content delivery and performance across diverse operational scenarios.

This YAML file would be processed by management software or scripts that apply these configurations to the CDN infrastructure, setting up environments, adjusting parameters, and deploying resources as specified.

3- **nginx_cachenode.conf.j2** and **router.conf.j2**
  
This configuration file shows an advanced use of Nginx for handling a complex CDN setup with dynamically configured paths, caching strategies, and custom request handling logic. It reflects an infrastructure where different components (like cache sizes and policies) are customized per team or service, enhancing performance and scalability. The use of Lua and template variables suggests a highly customizable and programmable environment managed through dynamic configurations possibly derived from the earlier YAML setup. This is typical in environments where performance, scalability, and responsiveness are critical, and where infrastructure needs may vary significantly across different teams or applications.

-------------------------------------------------

Some micellaneous stuff:

- OpenResty is a robust web platform that integrates the standard Nginx core with a variety of third-party modules, particularly those developed for extending Nginx with capabilities to run Lua scripts. It's designed to help build scalable web applications, web services, and dynamic web gateways more efficiently by leveraging Nginx's event-driven model. The platform significantly enhances the programmability of Nginx using the Lua programming language, making it a powerful tool for developing complex web-based systems.

- Fluent Bit is an open-source log processor and forwarder, which allows for collecting data like logs, metrics, and traces from different sources, enriching this data, and sending it to multiple destinations. It is part of the Fluentd ecosystem, designed to be lightweight and efficient, with a small memory footprint, making it particularly well-suited for environments like containers and embedded systems.



-----------------------------------

We need to add new cacha nodes and load balancers when we need to scale CDN in high traffic situations:

- **Add New Cache Node Check List**
  ![[Pasted image 20240514180750.png]]
  1- Add the ansible playbooks on your new VM to make it a cachenode.
  
  2- Add config playbooks to loadbalancers to recognize these cachenodes.


- **Add new Load-Balancer**
  ![[Pasted image 20240514181944.png]]
  1- You need Os roles for security of the reverse proxy. (Loadbalancers are seen as your servers as a proxy)