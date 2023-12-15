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

![Screenshot of the list of docker networks](./Assets/docker-network.png)





## Start instances of MongoDB
First of, pull the images from [Docker Hub](https://hub.docker.com/)

Next, run the following docker command:
```
docker-compose -f docker-compose.yaml up -d
```


![Screenshot of the list of containers](./Assets/containers.png)

Instead of 'Started' you might as well see 'Created' if you are running for the first time.




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
