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
* [Create User with necessary privileges](#create-user-with-necessary-privileges)
* [Edit the `hosts` file](#edit-the-hosts-file)
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
  5. Edit the `hosts` file




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

![Screenshot of the list of docker networks](./Assets/docker-network.png)


**Note:** If you don't create a docker network manually, docker compose will take care of it and create one on its own.


## Start instances of MongoDB
First of, pull the images from [Docker Hub](https://hub.docker.com/)

Next, run the following docker command:
```
docker-compose -f docker-compose.yaml up -d
```


![Screenshot of the list of containers](./Assets/containers.png)

Instead of 'started' you might as well see 'created' if you are running for the first time.




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


### Check the status of the replica set
Run the following command in the mongo shell:
```
rs.status()
```
It will look something like this:

<details>
  <summary>Click to reveal</summary>
  
```
{
  set: 'myReplicaSet',
  date: ISODate("2023-12-15T18:41:56.010Z"),
  myState: 2,
  term: Long("2"),
  syncSourceHost: 'mongo3:27017',
  syncSourceId: 2,
  heartbeatIntervalMillis: Long("2000"),
  majorityVoteCount: 2,
  writeMajorityCount: 2,
  votingMembersCount: 3,
  writableVotingMembersCount: 3,
  optimes: {
    lastCommittedOpTime: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
    lastCommittedWallTime: ISODate("2023-12-15T18:41:49.912Z"),
    readConcernMajorityOpTime: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
    appliedOpTime: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
    durableOpTime: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
    lastAppliedWallTime: ISODate("2023-12-15T18:41:49.912Z"),
    lastDurableWallTime: ISODate("2023-12-15T18:41:49.912Z")
  },
  lastStableRecoveryTimestamp: Timestamp({ t: 1702665649, i: 1 }),
  electionParticipantMetrics: {
    votedForCandidate: true,
    electionTerm: Long("2"),
    lastVoteDate: ISODate("2023-12-15T18:40:09.903Z"),
    electionCandidateMemberId: 2,
    voteReason: '',
    lastAppliedOpTimeAtElection: { ts: Timestamp({ t: 1702648984, i: 1 }), t: Long("1") },
    maxAppliedOpTimeInSet: { ts: Timestamp({ t: 1702648984, i: 1 }), t: Long("1") },
    priorityAtElection: 1,
    newTermStartDate: ISODate("2023-12-15T18:40:09.933Z"),
    newTermAppliedDate: ISODate("2023-12-15T18:40:10.024Z")
  },
  members: [
    {
      _id: 0,
      name: 'mongo1:27017',
      health: 1,
      state: 2,
      stateStr: 'SECONDARY',
      uptime: 122,
      optime: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
      optimeDate: ISODate("2023-12-15T18:41:49.000Z"),
      lastAppliedWallTime: ISODate("2023-12-15T18:41:49.912Z"),
      lastDurableWallTime: ISODate("2023-12-15T18:41:49.912Z"),
      syncSourceHost: 'mongo3:27017',
      syncSourceId: 2,
      infoMessage: '',
      configVersion: 1,
      configTerm: 2,
      self: true,
      lastHeartbeatMessage: ''
    },
    {
      _id: 1,
      name: 'mongo2:27017',
      health: 1,
      state: 2,
      stateStr: 'SECONDARY',
      uptime: 117,
      optime: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
      optimeDurable: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
      optimeDate: ISODate("2023-12-15T18:41:49.000Z"),
      optimeDurableDate: ISODate("2023-12-15T18:41:49.000Z"),
      lastAppliedWallTime: ISODate("2023-12-15T18:41:49.912Z"),
      lastDurableWallTime: ISODate("2023-12-15T18:41:49.912Z"),
      lastHeartbeat: ISODate("2023-12-15T18:41:54.509Z"),
      lastHeartbeatRecv: ISODate("2023-12-15T18:41:55.986Z"),
      pingMs: Long("0"),
      lastHeartbeatMessage: '',
      syncSourceHost: 'mongo3:27017',
      syncSourceId: 2,
      infoMessage: '',
      configVersion: 1,
      configTerm: 2
    },
    {
      _id: 2,
      name: 'mongo3:27017',
      health: 1,
      state: 1,
      stateStr: 'PRIMARY',
      uptime: 117,
      optime: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
      optimeDurable: { ts: Timestamp({ t: 1702665709, i: 1 }), t: Long("2") },
      optimeDate: ISODate("2023-12-15T18:41:49.000Z"),
      optimeDurableDate: ISODate("2023-12-15T18:41:49.000Z"),
      lastAppliedWallTime: ISODate("2023-12-15T18:41:49.912Z"),
      lastDurableWallTime: ISODate("2023-12-15T18:41:49.912Z"),
      lastHeartbeat: ISODate("2023-12-15T18:41:55.990Z"),
      lastHeartbeatRecv: ISODate("2023-12-15T18:41:55.988Z"),
      pingMs: Long("0"),
      lastHeartbeatMessage: '',
      syncSourceHost: '',
      syncSourceId: -1,
      infoMessage: '',
      electionTime: Timestamp({ t: 1702665609, i: 1 }),
      electionDate: ISODate("2023-12-15T18:40:09.000Z"),
      configVersion: 1,
      configTerm: 2
    }
  ],
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1702665709, i: 1 }),
    signature: {
      hash: Binary.createFromBase64("AAAAAAAAAAAAAAAAAAAAAAAAAAA=", 0),
      keyId: Long("0")
    }
  },
  operationTime: Timestamp({ t: 1702665709, i: 1 })
}
```
</details>

See in the status log above as to which one is Primary node. If say 'mongo2' is primary, get into 'mongo2' shell to create the database user.



To exit from the current mongo shell:
```
exit
```

To get into primary node shell(say 'mongo2' is primary):
```
docker exec -it mongo2 mongosh
```

### Create User with necessary privileges

```
use admin
```

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


### Edit the `hosts` file

Navigate to the following directory in your computer:

```
C:\Windows\System32\drivers\etc
```

Check for `hosts` file. Open with administrative privileges and add the following IPs to hosts mapping:

```
127.0.0.1 mongo1
127.0.0.2 mongo2
127.0.0.3 mongo3
```


### Updated Mongo URI
```
mongodb://writeUser:secret@mongo1:27017,mongo2:27017,mongo3:27017/?replicaSet=myReplicaSet
```


## Mongo Express

![Screenshot of the Mongo Express Dashboard](./Assets/mongo-express.png)


## References

* [Using Docker to Deploy a MongoDB Cluster](https://www.mongodb.com/compatibility/deploying-a-mongodb-cluster-with-docker#:~:text=To%20create%20a%20network%2C%20run%20the%20docker%20network%20create%20command.&text=The%20mongoCluster%20parameter%20here%20is,only%20needs%20to%20run%20once.)

* [Creating Database User](https://www.mongodb.com/docs/manual/reference/method/db.createUser/)

* [Connection string](https://www.mongodb.com/docs/manual/reference/connection-string/#replica-set-option)

* [IP Binding](https://www.mongodb.com/docs/manual/core/security-mongodb-configuration/#:~:text=To%20bind%20to%20all%20IPv4%20and%20IPv6%20addresses%2C%20you%20can,command%2Dline%20option%20%2D%2Dbind_ip_all%20.)

* [Authentication](https://www.mongodb.com/docs/manual/core/authentication/)