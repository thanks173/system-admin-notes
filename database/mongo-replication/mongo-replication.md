# MongoDB Replication

## Initiation

1. Start the mongo instance with the replica set name
* Command-line options

```
mongod --replSet "<replica-set-name>"
```

* Configuration file

```
replication:
   replSetName: "<replica-set-name>"
```

2. Connect to mongo shell

```javascript
rs.initiate()
```

## Add new member to replica set

1. Add the new member with no priority and no vote

```javascript
rs.add( { host: "<new-member-ip>:<new-member-port>", priority: 0, votes: 0 } )
```

2. Check member-id and other config with `rs.conf()`

3. For safety purpose, wait for the member synchronized and switched into SECONDARY state (use mongo-cli to check), then update its priority and votes.

```javascript
var cfg = rs.conf()
cfg.members[<member-id>].priority = 1 // check member-id in rs.conf()
cfg.members[<member-id>].votes = 1
rs.reconfig(cfg)
```

## Check status

```javascript
rs.status()
rs.printSlaveReplicationInfo()
```
