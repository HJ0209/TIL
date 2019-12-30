# Mongo DB

1. C:\Users\HPE\Work\
2. `tree`라는 명령어 입력하면 폴더 기록 나옴

mongdb > 27017 

node01 > 27018 (Primary)

node02> 27019 (Secondary)

node03 > 27020 (Secondary)

arbiter > 27030 (Arbiter)



C:\Users\HPE>**cd** Work\mongodb-4.2.2

C:\Users\HPE\Work\mongodb-4.2.2>**mongod --dbpath .\data\node01 --port 27018 --replSet myapp**

이렇게 노드1,2,3,arbiter까지 연결





* mongod : 서버 실행 / mongo : 클라이언트 실행

mongo --host 127.0.0.1 --port 27018



* show dbs > 
* use [DB이름]
* show collections;
* db.collection.save();

* db.collection.find();
* db.books.save({title: "MongoDb basic"}); _ book이 없으면 이를 생성해 저장
* 



* cmd 창에서 앞에 '선택'나오면 안된다.
* 





Microsoft Windows [Version 10.0.17134.1184]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\HPE>**mongo --host 127.0.0.1 --port 27018** (호스트를 node01로 연결 / 127.0.0.1 은 내 컴퓨터 IP 주소)
MongoDB shell version v4.2.2
connecting to: mongodb://127.0.0.1:27018/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("0e500670-8358-4dfd-a87b-58ab27eab2c6") }
MongoDB server version: 4.2.2
Server has startup warnings:
2019-12-29T18:25:43.721-0700 I  CONTROL  [initandlisten]
2019-12-29T18:25:43.721-0700 I  CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2019-12-29T18:25:43.723-0700 I  CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2019-12-29T18:25:43.724-0700 I  CONTROL  [initandlisten]
2019-12-29T18:25:43.725-0700 I  CONTROL  [initandlisten] ** WARNING: This server is bound to localhost.
2019-12-29T18:25:43.726-0700 I  CONTROL  [initandlisten] **          Remote systems will be unable to connect to this server.
2019-12-29T18:25:43.727-0700 I  CONTROL  [initandlisten] **          Start the server with --bind_ip <address> to specify which IP
2019-12-29T18:25:43.728-0700 I  CONTROL  [initandlisten] **          addresses it should serve responses from, or with --bind_ip_all to
2019-12-29T18:25:43.729-0700 I  CONTROL  [initandlisten] **          bind to all interfaces. If this behavior is desired, start the
2019-12-29T18:25:43.730-0700 I  CONTROL  [initandlisten] **          server with --bind_ip 127.0.0.1 to disable this warning.

2019-12-29T18:25:43.731-0700 I  CONTROL  [initandlisten]
---
Enable MongoDB's free cloud-based monitoring service, which will then receive and display
metrics about your deployment (disk utilization, CPU, operation statistics, etc).

The monitoring data will be available on a MongoDB website with a unique URL accessible to you
and anyone you share the URL with. MongoDB may use this information to make product
improvements and to suggest MongoDB products and deployment options to you.

To enable free monitoring, run the following command: db.enableFreeMonitoring()
To permanently disable this reminder, run the following command: db.disableFreeMonitoring()
---

myapp:PRIMARY> rs.add("127.0.0.1:27019")
{
        "ok" : 1,
        "$clusterTime" : {
                "clusterTime" : Timestamp(1577669178, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        },
        "operationTime" : Timestamp(1577669178, 1)
}
myapp:PRIMARY> rs.add("127.0.0.1:27020")
{
        "ok" : 1,
        "$clusterTime" : {
                "clusterTime" : Timestamp(1577669183, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        },
        "operationTime" : Timestamp(1577669183, 1)
}
myapp:PRIMARY> rs.add("127.0.0.1:27030", {arbiterOnly:true })
{
        "ok" : 1,
        "$clusterTime" : {
                "clusterTime" : Timestamp(1577669194, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        },
        "operationTime" : Timestamp(1577669194, 1)
}
myapp:PRIMARY> db.isMaster(
...
... );
{
        "hosts" : [
                "localhost:27018",
                "127.0.0.1:27019",
                "127.0.0.1:27020"
        ],
        "arbiters" : [
                "127.0.0.1:27030"
        ],
        "setName" : "myapp",
        "setVersion" : 4,
        "ismaster" : true,
        "secondary" : false,
        "primary" : "localhost:27018",
        "me" : "localhost:27018",
        "electionId" : ObjectId("7fffffff0000000000000002"),
        "lastWrite" : {
                "opTime" : {
                        "ts" : Timestamp(1577669194, 1),
                        "t" : NumberLong(2)
                },
                "lastWriteDate" : ISODate("2019-12-30T01:26:34Z"),
                "majorityOpTime" : {
                        "ts" : Timestamp(1577669194, 1),
                        "t" : NumberLong(2)
                },
                "majorityWriteDate" : ISODate("2019-12-30T01:26:34Z")
        },
        "maxBsonObjectSize" : 16777216,
        "maxMessageSizeBytes" : 48000000,
        "maxWriteBatchSize" : 100000,
        "localTime" : ISODate("2019-12-30T01:26:43.500Z"),
        "logicalSessionTimeoutMinutes" : 30,
        "connectionId" : 1,
        "minWireVersion" : 0,
        "maxWireVersion" : 8,
        "readOnly" : false,
        "ok" : 1,
        "$clusterTime" : {
                "clusterTime" : Timestamp(1577669194, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        },
        "operationTime" : Timestamp(1577669194, 1)
}
myapp:PRIMARY> db.isMaster( );
{
        "hosts" : [
                "localhost:27018",
                "127.0.0.1:27019",
                "127.0.0.1:27020"
        ],
        "arbiters" : [
                "127.0.0.1:27030"
        ],
        "setName" : "myapp",
        "setVersion" : 4,
        "ismaster" : true,
        "secondary" : false,
        "primary" : "localhost:27018",
        "me" : "localhost:27018",
        "electionId" : ObjectId("7fffffff0000000000000002"),
        "lastWrite" : {
                "opTime" : {
                        "ts" : Timestamp(1577669205, 1),
                        "t" : NumberLong(2)
                },
                "lastWriteDate" : ISODate("2019-12-30T01:26:45Z"),
                "majorityOpTime" : {
                        "ts" : Timestamp(1577669205, 1),
                        "t" : NumberLong(2)
                },
                "majorityWriteDate" : ISODate("2019-12-30T01:26:45Z")
        },
        "maxBsonObjectSize" : 16777216,
        "maxMessageSizeBytes" : 48000000,
        "maxWriteBatchSize" : 100000,
        "localTime" : ISODate("2019-12-30T01:26:45.892Z"),
        "logicalSessionTimeoutMinutes" : 30,
        "connectionId" : 1,
        "minWireVersion" : 0,
        "maxWireVersion" : 8,
        "readOnly" : false,
        "ok" : 1,
        "$clusterTime" : {
                "clusterTime" : Timestamp(1577669205, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        },
        "operationTime" : Timestamp(1577669205, 1)
}







---

Microsoft Windows [Version 10.0.17134.1184]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\HPE>cd study

C:\Users\HPE\study>dir
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: A0B4-FAD1

 C:\Users\HPE\study 디렉터리

2019-12-23  오후 03:55    <DIR>          .
2019-12-23  오후 03:55    <DIR>          ..
2019-12-23  오전 10:05    <DIR>          .vagrant
2019-12-23  오전 10:00    <DIR>          cloud-computing
2019-12-23  오후 04:06    <DIR>          vagrant
               0개 파일                   0 바이트
               5개 디렉터리  932,463,927,296 바이트 남음

C:\Users\HPE\study>vagrant up

C:\Users\HPE\study>vagrant status
A Vagrant environment or target machine is required to run this
command. Run `vagrant init` to create a new Vagrant environment. Or,
get an ID of a target machine from `vagrant global-status` to run
this command on. A final option is to change to a directory with a
Vagrantfile and to try again.

C:\Users\HPE\study>

C:\Users\HPE\study>cd vagrant

C:\Users\HPE\study\vagrant>vagrant status
Current machine states:

node01                    aborted (virtualbox)
node02                    not created (virtualbox)
node03                    not created (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.

C:\Users\HPE\study\vagrant>vagrant up
Bringing machine 'node01' up with 'virtualbox' provider...
Bringing machine 'node02' up with 'virtualbox' provider...
Bringing machine 'node03' up with 'virtualbox' provider...
==> node01: Checking if box 'centos/7' version '1905.1' is up to date...
==> node01: Clearing any previously set forwarded ports...
==> node01: Clearing any previously set network interfaces...
==> node01: Preparing network interfaces based on configuration...
    node01: Adapter 1: nat
    node01: Adapter 2: bridged
==> node01: Forwarding ports...
    node01: 22 (guest) => 19211 (host) (adapter 1)
    node01: 80 (guest) => 10080 (host) (adapter 1)
    node01: 3306 (guest) => 13306 (host) (adapter 1)
==> node01: Running 'pre-boot' VM customizations...
==> node01: Booting VM...
==> node01: Waiting for machine to boot. This may take a few minutes...
    node01: SSH address: 127.0.0.1:19211
    node01: SSH username: vagrant
    node01: SSH auth method: private key



----



* SQL 과 MongoDB
  * Creat table members()     vs db.mdmbers.insert()
  * insers into memberts()    vs db.members.insert()
  * update members              vs 
  * select * from members;   vs db.members.find()
  * delete vs remove({type : "ACE"}) > 타입 ACE라고 되어 있는 것 삭제 / 안에 아무것도 없으면 전부 삭제
* --auth : 인증처리를 요청하게 되는 것 (데이터베이스 목록 등 확인)





---



## Replica

> 빅데이터 환경에서 예기치 못한 시스템 장애로 인한 데이터 유실을 예방 위해 저장 및 복구 위한 백업 솔루션 : CLI (Mongo client) > Primary의 데이터를 Secondary로 복제해두기



* Master(Primary) & Slave(Secondary)
  * 마스터가 클라이언드와 연결하는 역할 수행
  * 마스터 서버가 장애 발생하더라도 복제서버를 이용해 바른 복제 가능(Slave는 master서버와 동일한 구조를 가지고 있음)
  * Replica는 최소 2개 이상의 노드로 구성되어야 한다.  (하나는 서버운영. 하나는 마스터복구?)



* RelicaSets 
  * Arbiter은 master가 오류난 경우에 slave에서 선출하기 위해 투표하는 역할 수행
  * rs.add : 새로운 복제 서버 추가 / rs.remove : 서버 삭제



* CAP정리
  * 일관성 / 가용성 / 분단허용성 : 이 3가지를 모두 함께 가질 수 없음
  * 일관성+가용성 : 관계형 / 일관성+분단 허용성 : 몽고DB





---

* mongo가 설치되면 mongo version 으로 버전 확인 (mongo  명령어 입력 > shell version v4.0.14)







---

# 서버1에서 서버2로 연결되는지 확인

```sql
mkdir -p ./mongo/data
mongod --dbpath ./mongo/data --port 40000 --bind_ip_all
```

bind_ip_all 은 모든 ip 허용하겠다는 의미



```sql
ifconfig > 네트워크 주소 확인
```

외부에서 사용할 수 있는 IP확인 (vagrant에서 설정한 Ip가 나타남 - 10.0.0.12)



* show dbs;
* use bookstore;
* db.books.save({title:"java"})



```mysql
myapp:PRIMARY> db.isMaster() >> myapp:primary라고 떴는지 확인 필요

"node01:40001" > 다른 애들은 추가하지 않아 노드01만 나타남
"primary" : "node01:40001" > 이게 중요!
```

```mysql
rs.initiative > 초기화
myapp:PRIMARY> rs.add("10.0.0.12:40002"); > "10.0.0.12" 대신 "node02"로 입력해도 가능(=rs.add("node02:40002"))
rs.add("10.0.0.13:40003",{arbietrOnly:true})
```

```sql
ping 10.0.0.11 > 이걸로 네트워크 연결되었는지 확인 가능
```



* 리눅스 cat /etc/hostname : 호스트 이름변경 가능 

  cat /etc/hosts : 



* 저장)   `:wq!`



```sql
use bookstore
db.books.save({title: "Java"});
db.books.find();
```

이를 통해 노드1,2 간에 네트워크 연결이 되었는지 확인 필요

