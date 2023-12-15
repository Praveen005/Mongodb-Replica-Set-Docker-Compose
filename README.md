# Remotely connecting to MongoDB Replica Set running on Docker

## Disclaimer
> :warning: **This Configuration is purely meant for development purposes.**
> 
> Make necessary changes before using it for production applications. 

## Contents
* [Introduction](#introduction)
* [Instructions](#instructions)
* [Create a Docker Network](#create-a-docker-network)
* [Start instances of MongoDB](#start-instances-of-mongodb)
* [Initiate the Replica Set](#initiate-the-replica-set)
* [Check the status of the replica set](#check-the-status-of-the-replica-set)
* [Open Mongo Shell](#open-mongo-shell)
* [Updated Mongo URI](#updated-mongo-uri)
* [Mongo Express](#mongo-express)
* [References](#references)


## Introduction
MongoDB offers redundancy and high availability through replica sets. These groups of servers maintain identical data copies. In case of a server failure, the remaining members automatically elect a new primary, minimizing downtime and data loss.

![MongoDB Cluster/Replica Set](./Assets/mongo-replica.png)

Leveraging Docker containers, one can quickly spin up replica sets without needing a dedicated MongoDB installation on your machine.


## Instructions
The steps to create a docker cluster are as follows:
  1. Create a Docker network.
  2. Start three instances of MongoDB.
  3. Initiate the Replica Set.
  4. Create a Database User.

## Create a Docker Network
The first step is to create a Docker network. This network will let each of your containers running in this network talk to each other.

To create a network, run the docker network create command:
```
docker network create mongo-network
```

To check if the network has been created or not, run the following command:
```
docker network ls
```
[image-3]


## Start instances of MongoDB
First of, pull the images from [Docker Hub](https://hub.docker.com/)
Next, run the following docker command:
```
docker-compose -f docker-compose.yaml up -d
```
[image-4]

## Initiate the Replica Set

### Open Mongo Shell
```
docker exec -it mongo1 mongosh
```
Run the following command to initiate the replica set:
```
rs.initiate( {
   _id : "myReplicaSet",
   members: [
      { _id: 0, host: "mongo1:27017" },
      { _id: 1, host: "mongo2:27017" },
      { _id: 2, host: "mongo3:27017" }
   ]
})
```

You'll see something like this:
[Image-5]

### Check the status of the replica set
Run the following command in the mongo shell:
```
rs.status()
```
It will look something like this:
[image-6]
See in the status log above as to which one is Primary node. If say 'mongo2' is primary, get into 'mongo2' shell to create the database user.

To exit from the current mongo shell:
```
exit
```

To get into primary node shell(say 'mongo2' is primary):
```
docker exec -it mongo2 mongosh
```
Next Up:
```
use admin
```
Create User with necessary privileges:
```
db.createUser(
  {
    user: "writeUser",
    pwd: "secret",
    roles: [
      { role: "readWrite", db: "admin" },
      { role: "dbAdmin", db: "admin" }
    ]
  }
)
```
You are now good to go!

### Updated Mongo URI
```
mongodb://writeUser:secret@mongo1:27017,mongo2:27017,mongo3:27017/?replicaSet=myReplicaSet
```
