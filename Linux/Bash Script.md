-  ifconfig command is used to check the parameters of your linux system's network interface

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
        RX packets 0  bytes 0 (0.0 B)
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

- vim/nano are text editors to change the contents of the given file


```vim /etc/app.config
```