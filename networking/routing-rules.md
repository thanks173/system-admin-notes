# Routing configuration for multiple uplinks/providers

## Set up the main routing table

```bash
ip route add $SUBNET_1 dev $INTERFACE_1 src $IP_1
ip route add $SUBNET_2 dev $INTERFACE_2 src $IP_2

ip route add default via $GATEWAY_1
```

## Creates two additional routing tables.

```bash
echo "200 rt-table-1" >> /etc/iproute2/rt_tables
echo "201 rt-table-2" >> /etc/iproute2/rt_tables
```

## Set up routing in the additional tables

### Build a route to the gateway and set default gateway for each table

```bash
ip route add $SUBNET_1 dev $INTERFACE_1 src $IP_1 table rt-table-1
ip route add default via $GATEWAY_1 dev $INTERFACE_1 table rt-table-1

ip route add $SUBNET_2 dev $INTERFACE_2 src $IP_2 table rt-table-2
ip route add default via $GATEWAY_2 dev $INTERFACE_2 table rt-table-2
```

### Specific what routing table to route with

```bash
ip rule add from $SUBNET_1 table rt-table-1
ip rule add to $SUBNET_1 table rt-table-1

ip rule add from $SUBNET_2 table rt-table-2
ip rule add to $SUBNET_2 table rt-table-2
```

### OPTIONAL: Routing inbound/outbound traffic (from other subnet) with specific table

```bash
ip rule add from $SUBNET_X table rt-table-2
ip rule add to $SUBNET_X table rt-table-1
```