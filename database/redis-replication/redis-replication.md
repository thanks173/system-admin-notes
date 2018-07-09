# Fault-tolerant master/slave redis cluster using sentinel

## Set up slave node

Add this line to redis.conf

```
slaveof <master-ip> <master-port>
```

## Start redis server and redis sentinel on each node

```
redis-server redis.conf
redis-server sentinel.conf --sentinel
```

## Query the sentinel process for address of master redis instance

```
redis-cli -h <sentinel-ip> -p <sentinel-port> sentinel get-master-addr-by-name <master-group-name>
```

