# Mongo Cluster with Docker

An easy setup to create a mongodb cluster with docker.

## Run compose
``` bash
docker-compose up
```

## Connect to primary node
``` bash
docker-compose exec mongo-primary mongo -u "root" -p "password"
```

## Replica set initialization
``` js
rs.initiate({
  "_id": "replica-set",
  "members": [
    {"_id": 0, "host": "mongo-primary:27017"},
    {"_id": 1, "host": "mongo-worker-1:27017"},
    {"_id": 2, "host": "mongo-worker-2:27017"},
    {"_id": 3, "host": "mongo-worker-3:27017"}
  ]
});
```

## Master priority configuration
``` js
conf = rs.config();
conf.members[0].priority = 2;
rs.reconfig(conf);
```

## Create a Cluter admin user
``` bash
use admin;
db.createUser({
  user: "cluster_admin",
  pwd: "password",
  roles: [
    { role: "userAdminAnyDatabase", db: "admin" },
    { role: "clusterAdmin", db: "admin" }
  ]
});
db.auth("cluster_admin", "password");
```

## Create a collection
``` bash
use my_data;
db.createUser({
  user: "my_user",
  pwd: "password",
  roles: [{ role: "readWrite", db: "my_data" }]
});
db.createCollection('my_collection');
```

## Verify credentials
```
docker-compose exec mongo-primary mongo -u "my_user" -p "password" --authenticationDatabase "my_data"
```

To destroy the container, just run `docker-compose down`