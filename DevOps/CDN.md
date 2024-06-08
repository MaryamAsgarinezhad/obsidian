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

- Tip: when creating a new space for a team, it actually creates a specific folder with the team's name and specific memory and ram on each cachenode, and does the caching based on repeated links for each team on their own folder.

3- **nginx_cachenode.conf.j2** and **router.conf.j2**
  
This configuration file shows an advanced use of Nginx for handling a complex CDN setup with dynamically configured paths, caching strategies, and custom request handling logic. It reflects an infrastructure where different components (like cache sizes and policies) are customized per team or service, enhancing performance and scalability. The use of Lua and template variables suggests a highly customizable and programmable environment managed through dynamic configurations possibly derived from the earlier YAML setup. This is typical in environments where performance, scalability, and responsiveness are critical, and where infrastructure needs may vary significantly across different teams or applications.

-------------------------------------------------

Some micellaneous stuff:

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

-----------------------------------------------------

**OpenResty Web Platform**

- OpenResty is a dynamic web platform that builds on the robust core of Nginx, integrating the standard Nginx core with a variety of third-party modules and Lua scripting capabilities. This integration enhances Nginx’s functionality, turning it into a more capable web server that can also execute application logic and handle network traffic management more efficiently.
- **Modular Architecture**: OpenResty includes a range of modules that extend its capabilities beyond simple HTTP serving, including support for web sockets, secure web connections, and direct interaction with other databases and storage systems.
- **Efficient Content Delivery**: OpenResty can be used to develop a fully-featured web application or service **directly on the Nginx server**, leveraging Nginx’s efficient handling of HTTP requests and Lua’s flexibility for scripting complex behaviors.

### **OpenResty Based CDN**

An OpenResty based CDN (Content Delivery Network) leverages the OpenResty web platform to efficiently serve web content to end-users with high performance and reduced latency. OpenResty achieves this by extending the capabilities of Nginx with Lua to handle web traffic at the edge of the network, closer to users. This setup is particularly effective for dynamic content generation, traffic shaping, and sophisticated request routing that are crucial for a CDN.

#### Key Aspects of an OpenResty Based CDN:

1. **Edge Computing**: OpenResty can execute complex Lua scripts directly at the edge server level, allowing decisions to be made on-the-fly without needing to query back-end servers. This capability is crucial for CDNs where edge nodes determine how best to serve content based on factors like the geographic location of the user, the type of content requested, and current network conditions.
    
2. **Dynamic Content Handling**: Unlike traditional CDNs that may primarily focus on caching static assets, an OpenResty based CDN can dynamically modify content at the edge. This might include altering images, compressing files on demand, or even generating personalized content based on user interactions.
    
3. **High Performance**: OpenResty utilizes the event-driven architecture of Nginx and the JIT compilation power of LuaJIT, making it extremely fast and capable of handling a large number of concurrent connections with minimal resource consumption.
    
4. **Security Enhancements**: CDNs often include security measures such as DDoS mitigation, bot management, and SSL/TLS termination. OpenResty can enhance these aspects with custom Lua scripts, providing flexible and powerful security configurations at the edge.

------------------------------------------------------

The conf.j2 files can also determine the logging format of the HTTP requests sent to hosts:

- ![[Pasted image 20240515183205.png]]

In Nginx, the variables `upstream_connect_time`, `upstream_header_time`, and `upstream_response_time` are automatically available for use in your log formats when Nginx is configured as a reverse proxy. These variables do not need to be explicitly defined by the user; they are built into Nginx and are populated when Nginx makes requests to upstream servers.

- **`$upstream_connect_time`**: This variable records the time in seconds with millisecond resolution that it took to establish a connection to an upstream server.
- **`$upstream_header_time`**: This records the time in seconds with millisecond resolution between establishing a connection to an upstream server and receiving the first byte of the response header.
- **`$upstream_response_time`**: This captures the time in seconds with millisecond resolution taken to receive the entire response from the upstream server, not just the header.

These variables are typically used in an environment **where Nginx acts as a reverse proxy or load balancer.**
An "upstream server" refers to any server or service to which Nginx forwards requests for processing. Nginx acts as a reverse proxy or load balancer in front of these upstream servers.

### Role of an Upstream Server

1. **Processing Requests**: An upstream server is responsible for handling requests sent by Nginx. It processes these requests and returns the appropriate responses back to Nginx, which then forwards them to the client (end user). This setup allows Nginx to delegate complex application logic or resource-intensive tasks to these backend servers.
    
2. **Scaling and Load Balancing**: Multiple upstream servers can be used to distribute the load of incoming requests, improving the reliability and scalability of web applications. Nginx can distribute traffic among several servers based on various methods like round-robin, least connections, or even based on server response times.

------------------------------------------

- The domains we put in our cdnized links (like zbn or sbn), refer to IP addresses corresponding to one or more load balancer. DNS uses round robin algorithms to send requests to multiple loadbalancers.

- We need loadbalancers to get content from the right cachenodes (because cachenodes are not duplicated) using a hashing and a mod number.
  We need cachenodes for scalability, high speed and multiple point of failure.

----------------------------------------------

### Endpoint Manual Configuration

Alternatively, if the service doesn't use a selector, it might be manually mapped to specific pods through an ==**`Endpoints`**== object, as seen in another part of your provided code. This `Endpoints` resource manually specifies the IP addresses of the pods to which the service routes traffic. This method is less common and typically used in special scenarios where automatic pod discovery via selectors is not desired.

```shell
apiVersion: v1
kind: Service
metadata:
  labels:
    name: nebula-dev
  name: nebula-dev
  namespace: gse-graph-dev
spec:
  type: ClusterIP
  ipFamilyPolicy: SingleStack
  ports:
  - port: 9100
    protocol: TCP
    targetPort: 9100
    name: metrics
  - port: 9200
    protocol: TCP
    targetPort: 9200
    name: stats
  - port: 9900
    protocol: TCP
    targetPort: 9900
    name: domains
---
apiVersion: v1
kind: Endpoints
metadata:
  labels:
    name: nebula-dev
  name: nebula-dev
  namespace: gse-graph-dev
  
subsets:
- addresses:
  - ip: 172.16.8.15
  ports:
  - name: metrics
    port: 9100
    protocol: TCP
  - name: stats
    port: 9200
    protocol: TCP
  - name: domains
    port: 9900
    protocol: TCP
- addresses:
  - ip: 172.16.8.16
  ports:
  - name: metrics
    port: 9100
    protocol: TCP
  - name: stats
    port: 9200
    protocol: TCP
  - name: domains
    port: 9900
    protocol: TCP
- addresses:
  - ip: 172.16.8.14
  ports:
  - name: metrics
    port: 9100
    protocol: TCP
  - name: stats
    port: 9200
    protocol: TCP
  - name: domains
    port: 9900
    protocol: TCP

---
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: nebula-dev
  namespace: gse-graph-dev
spec:
  endpoints:
  - interval: 30s
    path: /metrics
    port: stats
    scheme: http
    scrapeTimeout: 100s
  - interval: 30s
    path: /metrics
    port: metrics
    scheme: http
    scrapeTimeout: 100s
  - interval: 30s
    path: /metrics
    port: domians
    scheme: http
    scrapeTimeout: 100s
  selector:
    matchLabels:
      name: nebula-dev

```

**`kind: ServiceMonitor`**: Specifies that the resource type is a `ServiceMonitor`, which is responsible for ==defining how Prometheus should discover and scrape targets (services) within the cluster.==

- **`selector:`**
    - **`matchLabels:`**
        - **`name: clickhouse-{{ cluster_name }}`**: This is a label selector that ==tells Prometheus to apply this ServiceMonitor configuration== ==to any services that have a label== `name` with the value `clickhouse-{{ cluster_name }}`. This should correspond to the labels given to the Kubernetes `Service` resources that expose metrics for ClickHouse instances.


--------------------------------

If the config file faced error and there is no error in the error.log file, then:

```shell
openresty -t
```

if you want to see the error with the service:

```shell
journalctl -xeu openresty.service
```

--------------------------------------

The difference between .conf.j2 filles (generally config files) and ansible playbooks or tasks:
- is that .conf.j2 files are written to define the "**openresty.service**" on the nodes, but ansible files are written to apply linux commands on the nodes.

-------------------------

```lua
location /delete_cache { content_by_lua_block { local file_to_delete = ngx.var.arg_file if file_to_delete then local lfs = require("lfs") local cache_path = "/srv/cache/openresty/" local file_path = cache_path .. file_to_delete local attr = lfs.attributes(file_path) if attr and attr.mode == "file" then os.remove(file_path) ngx.say("File deleted: " .. file_to_delete) else ngx.say("File not found: " .. file_to_delete) end else ngx.say("No file specified for deletion") end } }
```