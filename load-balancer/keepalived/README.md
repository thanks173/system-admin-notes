# Failover & transparent loadbalancer using keepalived

## Configuration

* Copy files to /etc/keepalived/
* Enable ip_forward permanently. In /etc/sysctl.conf:

```
net.ipv4.ip_forward = 1
```

* Load ip_forward option and verify it:

```
sysctl -p
sysctl -a | grep net.ipv4.ip_forward
```

* Configure firewall to accept multicast and vrrp protocol (IP Protocol # 112):

```
iptables -I INPUT -i eth0 -d 224.0.0.0/8 -j ACCEPT
iptables -A INPUT -p 112 -i eth0 -j ACCEPT
iptables -A OUTPUT -p 112 -o eth0 -j ACCEPT
service iptables save
```

## Configuration for both UDP & TCP protocol (DNS server)

* This is instruction for load balancing on both UDP & TCP protocol. There's no easy way to health check UDP protocol with keepalived. So we will use TCP to perform health checking for servers which use both protocols (DNS server).

* Keepalived does not officially support UDP. So we need to manually configure LVS.

* Add these lines to `keepalived.service` to bring up the UDP ports when keepalived launches and to clean up after it stops.

```
ExecStartPre=-/sbin/ipvsadm -A -u <virtual-ip-address>:53 -s rr
ExecStartPre=-/sbin/ipvsadm -a -u <virtual-ip-address>:53 -r <real-server-ip-1>:53 -g
ExecStartPre=-/sbin/ipvsadm -a -u <virtual-ip-address>:53 -r <real-server-ip-2>:53 -g
ExecStopPost=-/sbin/ipvsadm -D -u <virtual-ip-address>:53
```

* Use keepalived’s notify_up / notify_down script calling feature to manually configure LVS for load balancing the corresponding UDP port. Check `keepalived_*.conf` for example.

* Check `bypass_ipvs.sh` for script to add or remove the prerouting rule for UDP protocol.

## Problem checking

* List  the virtual server table:

```
ipvsadm -L -n
```

* Ensure the master node owns VIP:

```
ip addr list dev network_interface
```

* Check iptable rule on the slave

```
iptables -t nat --list
```

* Check communication between keepalived instances:

```
tcpdump -v -i eth0 host 224.0.0.18
tcpdump -vvv -n -i eth0 host 224.0.0.18
```
