1. Create `username` and `password` to get token in **prod** and **dev**
   Curl the signup ednpoint with corresponding body 
   
1. Create new space
   config file in code
   
2. Add mapping for `ceph`
   mapping for the new teams original domain and abbreviated domain
    
3. Add member to Sre Cdn Rubika Channel

-----------------------------------

- **cdn.yml**
  
This configuration file is likely used by ==system administrators== to set up and maintain a CDN infrastructure tailored for a large organization with **multiple teams** and varied requirements regarding content delivery and caching. It allows for detailed control over caching strategies, network configurations, and resource allocation, essential for optimizing content delivery and performance across diverse operational scenarios.

This YAML file would be processed by management software or scripts that apply these configurations to the CDN infrastructure, setting up environments, adjusting parameters, and deploying resources as specified.

- **nginx_cachenode.conf.j2** and **router.conf.j2**
  
This configuration file shows an advanced use of Nginx for handling a complex CDN setup with dynamically configured paths, caching strategies, and custom request handling logic. It reflects an infrastructure where different components (like cache sizes and policies) are customized per team or service, enhancing performance and scalability. The use of Lua and template variables suggests a highly customizable and programmable environment managed through dynamic configurations possibly derived from the earlier YAML setup. This is typical in environments where performance, scalability, and responsiveness are critical, and where infrastructure needs may vary significantly across different teams or applications.

----------------------------------------------

For youtube and internet links, administrators create 'username' and 'password' for each team (using /signup endpoint), making them able to call the token application to **receive temporary keys** to make secure hashes to add to their links. 