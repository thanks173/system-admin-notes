# Iptables Tutorial

## Traversing of tables and chains

When a packet first enters the firewall, it hits the hardware and then gets passed on to the proper device driver in the kernel. Then the packet starts to go through a series of steps in the kernel, before it is either sent to the correct application (locally), or forwarded to another host, or whatever happens to it.

The packet can be stopped at any of the iptables chains, or anywhere else if it is malformed. However, we are mainly interested in the iptables aspect of this lot.

```ascii
                      NETWORK
                         +
                         |
                         |
                   +-----v------+
                   |    raw     |
                   | PREROUTING |
                   +-----+------+
                         |
                         |
+----------+       +-----v------+
|  mangle  |       |   mangle   |
|  INPUT   <---+   | PREROUTING |
+----+-----+   |   +-----+------+
     |         |         |
     |         |         |
+----v-----+   |   +-----v------+
|  filter  |   |   |    nat     |
|  INPUT   |   |   | PREROUTING |
+----+-----+   |   +-----+------+
     |         |         |
     |         |         v
+----v-----+   |      ROUTING
|  LOCAL   |   +----+ DECISION +------+
| PROCESS  |                          |
+----+-----+                          |
     |                          +-----v-----+
     v                          |  mangle   |
  ROUTING                       |  FORWARD  |
  DECISION                      +-----+-----+
     +                                |
     |                                |
+----v-----+                    +-----v-----+
|   raw    |                    |  filter   |
|  OUTPUT  |                    |  FORWARD  |
+----+-----+                    +-----+-----+
     |                                |
     |                ROUTING         |
+----v-----+   +----> DECISION <------+
|  mangle  |   |         +
|  OUTPUT  |   |         |
+----+-----+   |   +-----v-------+
     |         |   |   mangle    |
     |         |   | POSTROUTING |
+----v-----+   |   +-----+-------+
|   nat    |   |         |
|  OUTPUT  |   |         |
+----+-----+   |   +-----v-------+
     |         |   |   mangle    |
     |         |   | POSTROUTING |
+----v-----+   |   +-----+-------+
|  filter  |   |         |
|  OUTPUT  +-- +         |
+----------+             v
                      NETWORK
```

If we get a packet into the first routing decision that is not destined for the local machine itself, it will be routed through the FORWARD chain. If the packet is, on the other hand, destined for an IP address that the local machine is listening to, we would send the packet through the INPUT chain and to the local machine.

## Filter table

The filter table is mainly used for filtering packets.

```bash
# Accept all packages comming from the loopback interface
iptables -A INPUT -i lo -j ACCEPT

# Accept current established and related packages
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -s 192.168.0.0/24 -j ACCEPT

# Set default policy of INPUT chain to DROP
# Remember to set this after other rules, otherwise the current ssh
# connection will get dropped and other new connection will get blocked
iptables -P INPUT DROP
```

## Nat table

This table should be used for NAT (Network Address Translation) on different packets, to translate the packet's source field or destination field.

Note that, only the first packet in a stream will hit this table. After this, the rest of the packets will automatically have the same action taken on them as the first packet.

The actual targets that do these kind of things are:

* DNAT target

The DNAT target is used to change the destination address of the packet and reroute it to the host.

```bash
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 80 -j DNAT --to-destination 192.168.0.10:80
```

* REDIRECT target

The REDIRECT target is used to redirect packets and streams to the machine itself.

```bash
# Redirect DNS traffic to local port 53
iptables -t nat -A PREROUTING -p udp -m udp --dport 53 -j REDIRECT --to-ports 53
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 53 -j REDIRECT --to-ports 53

# Redirect HTTP traffic to local port 8080
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 8080
```

* SNAT target

The SNAT target is used to rewrite the Source IP address in the IP header of the packet.

```bash
iptables -t nat -A POSTROUTING -p tcp -o eth0 -j SNAT --to-source 192.168.0.10:80
```

* MASQUERADE target

The MASQUERADE target is used basically the same as the SNAT target, but it does not require any --to-source option.

```bash
# Change the source address of outgoing packets to local computer's address
iptables -t nat -A POSTROUTING -j MASQUERADE
```
