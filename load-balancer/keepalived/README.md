# Failover & transparent loadbalancer using keepalived

* Copy all file to /etc/keepalived/
* Enable ip_forward permanently. In /etc/sysctl.conf:

```
net.ipv4.ip_forward = 1
```

* Load this option and check it is set up correctly:
```
sysctl -p
sysctl -a | grep net.ipv4.ip_forward
```

* Ensure that loadbalancer is configured correctly:

```
ipvsadm -L -n
```

* Ensure new master owns VIP:

```
ip addr list dev network_interface
```

* Check iptable rule on the slave

```
iptables -t nat --list
```

* Make sure firewall is configured to accept accept multicast and vrrp protocol (IP Protocol # 112):

```
iptables -I INPUT -i eth0 -d 224.0.0.0/8 -j ACCEPT
iptables -A INPUT -p 112 -i eth0 -j ACCEPT
iptables -A OUTPUT -p 112 -o eth0 -j ACCEPT
service iptables save
```

* Make sure communication is established:

```
tcpdump -v -i eth0 host 224.0.0.18
tcpdump -vvv -n -i eth0 host 224.0.0.18
```