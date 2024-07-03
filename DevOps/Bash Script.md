-  'ifconfig' command is used to check the parameters of your linux system's network interface

```
br-7492aa201ef9: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.18.0.1  netmask 255.255.0.0  broadcast 172.18.255.255
        ether 02:42:44:a0:db:c5  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.199.1  netmask 255.255.255.0  broadcast 192.168.199.255
        ether 02:42:c3:a1:c2:ac  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp4s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 00:2b:67:c6:ce:4f  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)[[Bash Script]]
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 8861  bytes 1235078 (1.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8861  bytes 1235078 (1.2 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

vpn0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1472
        inet 10.10.10.234  netmask 255.255.255.0  destination 10.10.10.234
        inet6 fe80::24a2:7d0a:3704:8187  prefixlen 64  scopeid 0x20<link>
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        RX packets 42107  bytes 31858478 (31.8 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 37330  bytes 8951889 (8.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp0s20f3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.27.144.218  netmask 255.255.240.0  broadcast 172.27.159.255
        inet6 fe80::83c6:3a55:1aed:b567  prefixlen 64  scopeid 0x20<link>
        ether 8c:c6:81:f7:17:14  txqueuelen 1000  (Ethernet)
        RX packets 228719  bytes 93002870 (93.0 MB)
        RX errors 0  dropped 3  overruns 0  frame 0
        TX packets 56338  bytes 16876224 (16.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- The `apt` command in Linux is a package management tool used primarily in Debian-based distributions such as Ubuntu. It is used to manage software packages—installing, updating, and removing them.

- vim/nano are text editors to change the contents of a given file:
  ```bash
    vim /etc/app.con
    ```
- cat command shows the contents in that file.
- The `grep` command in a bash script is used to search for a specific pattern in files or input streams.
- the second command recursively searches for the string "pattern" in all `.txt` files in the current directory and its subdirectories.
- the first command Search recursively in a directory.
  
```shell
grep -r "pattern" directory/
find . -name "*.txt" -exec grep "pattern" {} \
```

- The `wc` command in the shell is used to display the number of lines, words, and bytes contained in a file or standard input.

```shell
  
  wc [options] [file]
```
- `-l`: Prints the number of lines in the file.
- `-w`: Prints the number of words in the file.
- `-c`: Prints the number of bytes in the file.
- `-m`: Prints the number of characters in the file.
- `-L`: Prints the length of the longest line in the file.

The command reads the contents of `words.txt`, then filters the output to show only the lines that start with an uppercase letter. A pipe (`|`) is a command in Linux and Unix-like operating systems that allows you to use the output (stdout) of one command as the input (stdin) to another command.

```shell
cat words.txt | grep '^[A-Z]'

```
This file is used by Linux systems to configure DNS (Domain Name System) name resolution settings. By opening this file with Vim, you can view and modify the DNS configuration, such as specifying DNS servers and search domains.

```shell
vim /etc/resolve.conf
```
The /etc directory is almost used to store configuration file. For changing any config file:

```shell
nano /etc/proxychains.conf
```

Nano editor commands:
- CTRL + O : save
- Enter : Read only
- CTRL + X : exit

Directories /etc and /bin store config and exec files of the system.

where to see the location of my python interpreter?
```shell
which python3
```

where to see my system's username and groupname?
```shell
whoami
groups
```


**<mark style="background: #BBFABBA6;">Linux Services:</mark>**

To run an application on your linux using services, you have to have all services installed on your local machine (you can not use any image here, but have to install services like SQL):

```shell
sudo apt update  # For Debian-based distributions (e.g., Ubuntu)
sudo apt install postgresql postgresql-contrib 

sudo systemctl start postgresql  # Start PostgreSQL service
sudo systemctl enable postgresql  # Enable PostgreSQL service to start on boot
```

Tip: Before staring any service, you have to write a "app.service" file for it. It almost acts like the dockerfile (or docker-compose)

```shell
vim  /etc/systemd/system/myapp.service

[Unit]
Description=FastAPI Service
After=network.target
Requires=postgresql.service

[Service]
User=maryam
Group=maryam
WorkingDirectory= "absolute path to your project"
Environment=DATABASE_URL=postgresql://postgres:mysecretpassword@127.0.0.1:5432/postgres
Environment=DATABASE_USER=postgres
Environment=DATABASE_PASSWORD=mysecretpassword
Environment=DATABASE_NAME=myDatabase
Environment=DATABASE_HOST=localhost
Environment=DATABASE_PORT=5432
ExecStart=/home/maryam/PycharmProjects/HelloDB/.venv/bin/uvicorn main:app --host 127.0.0.1 --port 8000
Restart=always

[Install]
WantedBy=multi-user.target
```
**network.target** is a systemd target unit in Linux that represents the completion of network initialization. When a service is set to start after network.target, it means that the service will wait until the network has been initialized before starting


- Here the host should be localhost (cause we have no container)
- Take care for the Require field (your application pending)

- You can change the database in which you are storing the tables in (create one and connect to it, and include it in your service file)
```shell
  sudo -su postgres
  psql
  CREATE DATABASE myDatabase;
  \c myDatabase   (connects to your database)
```


After creating the service:
```shell
sudo systemctl enable fastapi.service
sudo systemctl start fastapi.service
```


**Running an application on a remote server:**

1- download the image and save it on your local machine:
```shell
docker save > image_name.tar image_name
```
2- connect to the server using ssh command (ping then connect)

- SSH Command in Linux provides a secure encrypted connection between two hosts over an insecure network.

- Telnet is used to establish a connection and access a remote computer's command line interface. Also is used when ICMP command is closed on the server so we can't ping (ping uses ICMP protocol). But telnet uses TCP.
  
  ```shell
  ssh -p 5566 dev@10.19.5.224
```
To be able to run the above command you need to have ssh access (public key)
```shell
cd .ssh
ls
cat id_rsa.pub
```
- we give this public key to the person who wants to download and run our image on the server.

3- Load the image to the server:
```shell
sudo docker load -i /home/maryam/Desktop/image_name.img
```
4- Run the docker-compose file

5- Open postgres container to visit the database. **If the server does not have "psql" installed, connect to the server's postgres client from your local machine to check the changes applied by the application**.

- Since the docker compose is running on the remote server, the postgres container is running there so we should connect to the remote server using postgres:
  
```shell
  psql -h "hostname" -U postgres
```

Here's how SSH key authentication works:

- When you attempt to log in to a remote system using SSH, your SSH client presents your public key to the server.
- The server checks whether the public key presented by the client matches any of the authorized public keys stored in its configuration.
- If there's a match, the server allows the client to authenticate without needing a password.


The `curl` command is a versatile and widely-used command-line tool for transferring data to or from a server, using various protocols such as HTTP, HTTPS, FTP, FTPS, and more. It supports a wide range of options and features, making it suitable for various tasks like downloading files, testing APIs, performing network requests, and debugging.

Here are some common use cases of the `curl` command:

1. **Downloading Files**: You can use `curl` to download files from a remote server to your local system. For example:

- `curl -O https://example.com/file.txt`
    
- **Sending HTTP Requests**: `curl` can be used to send HTTP requests and receive responses. You can specify custom HTTP methods, headers, request bodies, and more. For example:
    
- `curl -X POST -H "Content-Type: application/json" -d '{"key": "value"}' https://api.example.com/endpoint`
    
- **Testing APIs**: Developers often use `curl` to test RESTful APIs by sending requests and inspecting responses. This can help verify the behavior and functionality of an API endpoint. For example:
    
- `curl https://api.example.com/users`
    
- **Debugging Network Issues**: `curl` can be useful for diagnosing network-related problems by retrieving information from remote servers and inspecting HTTP response headers and statuses. For example:
    
- `curl -v https://example.com`
    
- **Uploading Files**: In addition to downloading files, `curl` can also upload files to a server using HTTP POST requests. For example:

```perl
curl -F "file=@localfile.txt" https://example.com/upload
```

---------------------------------------
View the services of your system:

![[Pasted image 20240430143848.png]]

----------------------------------------

`dig` (domain information groper) and `nslookup` (name server lookup) are both command-line tools used for querying DNS (Domain Name System) servers to obtain DNS-related information. They can be used to look up various types of DNS records for a given domain name, such as A records (IPv4 address), AAAA records (IPv6 address), MX records (mail exchange), and others.

The main differences between the two are:

- `dig` is more flexible and powerful, providing detailed information and supporting more query options. It is the preferred tool for DNS troubleshooting and advanced DNS queries.
- `nslookup` is an older tool that is simpler to use but lacks some of the advanced features and flexibility of `dig`. It is still widely used and available on many systems for basic DNS lookups.

Here are some common examples of how to use these commands:

- `dig example.com`: Perform a basic DNS lookup for the A record of `example.com`.
- `dig -t mx example.com`: Query the MX (mail exchange) records for `example.com`.
- `nslookup example.com`: Perform a basic DNS lookup for the A record of `example.com` using `nslookup`.
- `nslookup -query=mx example.com`: Query the MX records for `example.com` using `nslookup`.

---------------------------------------

The `route -n` command in Linux is used to display the kernel routing table in a format that uses numeric addresses instead of trying to resolve symbolic host names. This is particularly useful for troubleshooting or when you want to avoid the overhead of name resolution.

This command is very helpful for network administration, allowing admins to quickly view and understand the routing configurations and how traffic will be directed through the network interfaces on a Linux system.

--------------------------------

Find the processes running on a specific (here 8000) port and killing it

```shell
ps -aux | grep 8000
kill -9 "process id"
```

-----------------------------------
man 'name of the command like su'  ---> : describes the command

Chmod command:

![[Pasted image 20240503181832.png]]
![[Pasted image 20240503181903.png]]
![[Pasted image 20240503182016.png]]------------------------------------------------

**SED**
The `sed` command in Linux stands for **Stream Editor**. It is used to perform basic text transformations on an input stream (a file or input from a pipeline).

### Common Features of `sed`

- **Pattern matching**: `sed` can perform actions like search and replace based on regular expressions.
- **Filtering**: `sed` can process only the lines that match a particular pattern.
- **Substitution**: It is most commonly used for substituting text using patterns.
- **Editing**: Despite being non-interactive, it can perform complex edits on text files, such as inserting, deleting, or replacing lines of text.


### Flags and Options

- `-i`: Edits files in-place (actually changes the original files).
- `-e`: Allows the use of multiple editing commands in a single run.
- `-n`: Suppresses automatic printing of pattern space (sed normally outputs all input data if not explicitly suppressed).

### Example Commands

1. **Substitute and Replace**:
    

- `sed 's/foo/bar/' file.txt  # Replaces the first occurrence of 'foo' with 'bar' in each line of file.txt`
    
- **Global Replacement**:
    
- `sed 's/foo/bar/g' file.txt  # Replaces all occurrences of 'foo' with 'bar' in each line`
    
- **In-place Editing**:
    
- `sed -i 's/foo/bar/g' file.txt  # Makes changes directly in file.txt, replacing 'foo' with 'bar'`
    
- **Delete Lines**:
    
- `sed '/pattern to match/d' file.txt  # Deletes lines that match the pattern`
    
- **Print specific lines**:
    

`sed -n '5p' file.txt  # Only prints the 5th line of the file`

---------------------------

**Touch**

**Creating Multiple Files**:


- `touch file1.txt file2.txt file3.txt`
    
    This command creates three new files.
    
- **Updating the Access Time Only**:
    
- `touch -a filename.txt`
    
    This updates only the access time of `filename.txt`.
    
- **Setting a Specific Time**:
    
- `touch -t 202101011230.00 filename.txt`
    
    This sets the modification and access times of `filename.txt` to 12:30 PM on January 1, 2021.
    
- **Prevent File Creation**:
    

`touch -c non_existent_file.txt`

This command will not create `non_existent_file.txt` if it does not exist; it simply does nothing and does not throw an error.

-------------------------------------

The `echo` command in Linux is used primarily to display lines of text or string variables to the terminal (stdout). It's a fundamental command in shell scripting and programming in Unix-like systems, frequently employed for outputting messages, managing output for logs, or for concatenating strings.

----------------------------------------

- move a file to a new directory and then view its conditions with datails:
  
  ![[Pasted image 20240503185226.png]]
----------------------------------

Install a new .deb app:
```shell
Dpkg -i "file.deb"
```
--------------------------------------------

![[Pasted image 20240503195519.png]]

```shell
netstat -nltp
```

this shows all active ports with their bounded IPs. you can connect to each running application by connecting to its IP and port specified here.

If the ip is not localhost, you can  connect to the app from external systems too.

---------------------

```shell
curl --location --request POST 'sre-api-auth.pr.mci.dev/api/v1/auth/signin' \
--header 'Content-Type: application/json' \
--data-raw '{
    "username":"admin@mci.dev",
    "password":"!@#123qwe"
}'
```
Curl is used to convert postman command to shell commands. The above code can be executed in postman too. This specific command is used to connect to the network as ADMIN user.

**The output token is used to be authorized as an admin.**


![[Pasted image 20240503202804.png]]this is the site that we can desemble the auth code. the Iat and exp are epoch times

--------------------------------
```python
pip freeze > requirements.txt
```
rewrites all requiremnts defined in .venv to the txt file.

----------------------
Make .venv your execution directory (only sees the .venv requirements instead of global ones):

```python
source ../.venv/bin/activate
```

----------------------------
Use docker commands without "sudo" permission:

```python
newgrp docker
groups $USER (here should show the 'docker" group)

 sudo usermod -aG docker $USER
```

-----------------------
The `pwd` command in Unix-like operating systems, including Linux, stands for "print working directory." It outputs the full pathname of the current working directory, which indicates where you are currently located within the filesystem.

-----------------------------
rm -r outputs: deletes the file

sudo rm -r outputs: deletes the file when the owner is root

ls -l outputs/: shows files in the directory with the owner of the fie

------------------------
If we execute the command by which file "x" creates using "sudo", access of that file is granted to the super user.

----------------------
important shell commands: 

netstat -ntupl |grep 8000
kill -9 .process N.O.

- The command "netstat -ntupl | grep 8000" is used to list all **active network connections and listening ports**, then filter the output to show only those associated with port 8000.

- |: This is a pipe symbol used to pass the output of the netstat command to the next command.
——————————————
ps aux

- The command ps aux is a Linux command used to display information about all **running processes** on the system. 

- ps: Stands for "process status." It is used to view information about currently running processes.
——————————————
systemctl status 'service name'
- checks the status of a service


cd /etc/systemd/system/ ls
- lists all system services

cat fastapi.service
- shows the config file a the specific service
- check the 'restart' field (if marked as always, the service will not stop unless you kill it)

sudo systemctl stop fastapi.service

---------------------

In the context of a filesystem, a "binary" typically refers to a binary file that is directly executable by a computer's operating system. These files are called "binaries" because they contain compiled code in binary format, which is a representation of executable machine code that the computer's CPU (Central Processing Unit) can directly read and execute.

A filesystem is a method and data structure that an operating system uses to control how data is stored and retrieved on a storage device such as a hard drive, SSD, USB drive, or memory card. Without a filesystem, storing information on a device would be one large body of data with no way to tell where one piece of information stops and the next begins.

------------------------------------------

- In Unix-like operating systems, including Linux, `/var` is a directory that contains variable data files. It stands for "variable," as opposed to the more static data in directories like `/bin` and `/sbin`. The `/var` directory typically includes files that are expected to grow in size or change frequently during normal system operation, such as log files (`/var/log`), spool directories (`/var/spool`), and temporary files (`/var/tmp`).


--------------------------------------------------

Adding new .venv to a project:

```shell
cd 'project directory'
python3 -m venv venv
source venv/bin/activate
```

- Now run the project and install the requirements that raise error.
- After installing all packages, pip freeze into requirements.

------------------------------------------

Copy the contents of a file into another:

```shell
cp 'file1' 'file2'
```

------------------------------------

```shell
sudo sh -c 'echo "deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list'
```

This command is used to add a new repository to the list of software sources in Ubuntu or Debian-based systems.

**`sh -c`**: This tells the system to use the shell (`sh`) to execute the command contained within the following single quotes. The `-c` option stands for "command", telling `sh` to execute the string that follows as a command.

**`echo "..."`**: The `echo` command is used here to output the string inside the quotes. The string in this case is a repository source line.

**`deb`**: Indicates that the line describes a binary repository.

 **`pgadmin4 main`**: This refers to the repository component. The term `main` usually indicates that the repository contains free, open-source software supported by the repository maintainers.

- A **binary repository** is a type of software repository that stores binary files, which are the compiled versions of software. These are ready-to-install software packages that have been pre-compiled for a specific operating system and hardware architecture. This contrasts with a source repository, which contains the source code of software that must be compiled before it can be installed.

--------------------------------------

- Set your default browser:
```shell
xdg-settings set default-web-browser firefox.desktop
```

-----------------------------------------

To view accessibility of files in a directory:

```shell
ls -l
```

------

```shell
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.debsudo dpkg -i cuda-keyring_1.1-1_all.debsudo apt-get updatesudo apt-get -y install cuda-toolkit-12-5
```

- **`wget` Command**: This retrieves the `cuda-keyring` package from NVIDIA's server, which contains the GPG keys required to authenticate other CUDA packages.
    
- **`sudo dpkg -i` Command**: This installs the downloaded keyring package. The `dpkg` command is used to install, remove, and manage packages on Debian-based systems like Ubuntu.
    
- **`sudo apt-get update` Command**: Refreshes the repository indexes. This is important after adding new repositories or keyrings to ensure that the package database is up to date.
    
- **`sudo apt-get -y install cuda-toolkit-12-5` Command**: Installs the CUDA Toolkit. The `-y` flag automatically answers "yes" to all prompts, allowing the installation to proceed without manual intervention.