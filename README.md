## Prerequisites
* Your device is running a recent version of Linux, Windows or macOS
* [Docker](https://docs.docker.com/install/) and [Docker Compose](https://docs.docker.com/compose/install/) are already installed on your device
* You'll need a MongoDB client in order to connect to the cluster. You may use the [MongoDB Shell](https://docs.mongodb.com/mongodb-shell/install/) or [MongoDB Compass](https://docs.mongodb.com/compass/current/install/).

## Start, Run & Connect

1. Launch a command line terminal (e.g. bash, zsh, cmd) and start the container for the configuration server:
```
docker compose up -d configsvr
```

2. Connect to the configuration server from the MongoDB Shell:
```
mongosh mongodb://<Your IP Address>:10001
```

3. Initialize the configuration server:
```
rs.initiate(
  {
    _id: "config_rs",
    configsvr: true,
    members: [
      {_id: 0, host: "<Your IP Address>:10001"}
    ]
  }
)
```

If you want to check if the initialization was successful, you can use:
```
rs.status()
```

Leave the mongosh session with:
```
exit
```

4. Start the containers for the shards:
```
docker compose up -d mongodb1 mongodb2 mongodb3
```

5. Connect to any of the shards:
```
mongosh mongodb://<Your IP Address>:20001
```

6. Initialize the shards:
```
rs.initiate(
  {
    _id: "shard_rs",
    members: [
      {_id: 0, host: "<Your IP Address>:20001"},
      {_id: 1, host: "<Your IP Address>:20002"},
      {_id: 2, host: "<Your IP Address>:20003"}
    ]
  }
)
```

Check the initialization:
```
rs.status()
```

Leave the mongosh session with:
```
exit
```

7. Start the container for the MongoDB Router:
```
docker compose up -d mongos
```

8. Connect to the router via mongosh:
```
mongosh mongodb://<Your IP Address>:30001
```

9. Add the shards to the cluster:
```
sh.addShard("shard_rs/192.168.178.56:20001,192.168.178.56:20002,192.168.178.56:20003")
```

Check if all shards were added:
```
rs.status()
```

