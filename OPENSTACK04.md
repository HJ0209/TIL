200110 오픈스04

# OPENSTACK

manual-controller 하나 더 만들기

2GB







현재상태 확인

``` sql
# openstack-status
명령어 실행이 안되면
# yum install -y openstack-utils
```







## 3. Network Time Protocol(NTP)

https://docs.openstack.org/install-guide/environment-ntp.html

* Environment에서 `Network Time Protocol(NTP)`에서 `controller node`다운

* ```sql
  server 0.centos.pool.ntp.org iburst
  server 1.centos.pool.ntp.org iburst
  # server 2.centos.pool.ntp.org iburst
  # server 3.centos.pool.ntp.org iburst
  server 10.0.0.0.100 iburst
  ```

* ```
  1.[root@controller ~]# vi /etc/sysconfig/network-scripts/ifcfg-ens33
  #UUID=
  IPADDR="10.0.0.11"
  2.#systemctl restart network
  3.#ip a
  4.[root@controller ~]# vi /etc/hosts > 'vi'는 수정하라는 것
  127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
  ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
  10.0.0.11 controller
  10.0.0.31 compute1
  ```



## 4. OpenStack packages for RHEL and CentOS

```sql
# yum install python-openstackclient

# yum install openstack-selinux
```



## 5. SQL database (CentOS)

https://docs.openstack.org/install-guide/environment-sql-database-rdo.html

```sql
# yum install mariadb mariadb-server python2-PyMySQL

#vi /etc/my.cnf.d/openstack.cnf
#vi는 뒤에 오는 디렉토리의 파일의 내용을 수정하겠다는 의미

 bind-address = 10.0.0.11  
 # 다양한 요청 중 10.0.0.11에서 들어오는 요청만 허용해주겠다. 
 # 10.0.0.0은 모든 요청을 허용하겠다는 의미
default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
```

```sql
# systemctl enable mariadb.service
# systemctl start mariadb.service
# status로 확인해 오류없이 'active running' 상태여야 한다

# mysql_secure_installation
# all-in-one으로 설치시 root 비밀번호를 설정할 수 업어 secure통해 비밀번호 설정 가능
비밀번호가 설정되어 있지 않으므로 그냥 엔터 치고 new password 설치하겠다고 y하고 abc123설치하고 모두 y 통해 지운후 Y로 리부트
```



## 6. Message Quence

등록된 이미지를 nova가 쓰는 것

다이렉트로 처리할때까지 기다릴 필요없이 Q에다가 요청서를 내고 자기는 자기 할 일을 하는 비동기적 방식 (직접 요청하는 것을 sync방식 / 기다리는 것이 async방식)

서비스에 대한 확장을 flexible하게 할 수 있는 아키텍쳐 (디커플링=약결합 아키텍쳐)

>  서비스는 3가지 종류 존재 : [RabbitMQ](https://www.rabbitmq.com/), [Qpid](https://qpid.apache.org/), and [ZeroMQ](http://zeromq.org/). 

1. Install the package:

   ```
   # yum install rabbitmq-server
   ```

2. Start the message queue service and configure it to start when the system boots:

   ```
   # systemctl enable rabbitmq-server.service
   # systemctl start rabbitmq-server.service
   ```

3. Add the `openstack` user:

   ```
   # rabbitmqctl add_user openstack RABBIT_PASS
   뒤에 RABBIT_PASS가 비밀번호!
   
   Creating user "openstack" ...
   ```

   Replace `RABBIT_PASS` with a suitable password. 

   `sender`와 `reciever`간에 소통하기 때문에 서로 인증하기 위한 비밀번호 필요

   

4. Permit configuration, write, and read access for the `openstack` user:

   ```
   # rabbitmqctl set_permissions openstack ".*" ".*" ".*" 
   각각 * write / *read / *access 권한임
   
   Setting permissions for user "openstack" in vhost "/" ...
   ```

5. controller에서 확인

   ```sql
   # grep rabbit /etc/*/*conf
   
   /etc/keystone/keystone.conf:#rabbit_userid = guest
   /etc/keystone/keystone.conf:#rabbit_password = guest
   ```

   manual에서 확인

   ```sql
   [root@controller ~]# rabbitmqctl status
   Status of node rabbit@controller
   ```

## 7. Memcached

DB의 오버헤드로 인한 성능저하를 개선하기 위해 설정

* 설치방법

1. Install the packages:

   ```
   # yum install memcached python-memcached
   ```

2. 수정

```sql
# vi /etc/sysconfig/memcached
```

- `i`통해 insert상태로 바꾼 후, 옵션 변경 (controller 대신 `10.0.0.11`로 바꿔도 가능)

  ```
  OPTIONS="-l 127.0.0.1,::1,controller"
  ```

3. 확인

   ```sql
   # systemctl enable memcached.service
   # systemctl start memcached.service
   ```

   ```sql
   # systemctl status memcached.service
   ● memcached.service - memcached daemon
      Loaded: loaded (/usr/lib/systemd/system/memcached.service; enabled; vendor preset: disabled)
      Active: active (running) since 금 2020-01-10 10:39:00 KST; 23s ago
    Main PID: 3628 (memcached)
      CGroup: /system.slice/memcached.service
              └─3628 /usr/bin/memcached -p 11211 -u memcached -m 64 -c 1024 -l 127.0.0.1,::1, 10.0.0.11
   
   
   # ss -nlp|grep 11211
   tcp    LISTEN     0      128    127.0.0.1:11211                 *:*                   users:(("memcached",pid=3628,fd=26))
   tcp    LISTEN     0      128       [::1]:11211              [::]:*                   users:(("memcached",pid=3628,fd=27))
   ```

## 8. KEYSTONE

1. Use the database access client to connect to the database server as the `root` user:

   ```
   $ mysql -u root -p
   ```

   비밀번호 abc123치고 mariaDB에 접속

2. Create the `keystone` database:

```
MariaDB [(none)]> CREATE DATABASE keystone;
```

3. Grant proper access to the `keystone` database:

```
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
IDENTIFIED BY 'KEYSTONE_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
IDENTIFIED BY 'KEYSTONE_DBPASS';

MariaDB [(none)]> exit
```

 비밀번호 'KEYSTONE_DBPASS'로 설정하는 것

4. 설치

```sql
# yum install openstack-keystone httpd mod_wsgi
```

controller에서 확인

```sql
[root@controller packstack]# ls -l /etc/keystone/keystone.conf
-rw-r-----. 1 root keystone 120342  1월  9 10:11 /etc/keystone/keystone.conf
```

그룹 내부에는 rw / r 만 권한 있고, 나머지는 아예 권한 없느 ㄴ것을 확인



5. DB연결

```sql
# ...
connection =  mysql+pymysql://keystone:KEYSTONE_DBPASS@controller/keystone
> 742번째 줄 바꾸기

provider = fernet
> 2827번째 줄 바꾸기 / 암호화하기
```



6.  키스톤 설치

```sql
# vi /etc/keystone/keystone.conf

# su -s /bin/sh -c "keystone-manage db_sync" keystone
```

```ㄴ비
# ls /var/lib/mysql/keystone/ 
목록 쳤을 때, 이런 목록들이 보여야 함!
access_token.frm                 endpoint.frm             idp_remote_ids.ibd   policy.frm                  registered_limit.ibd   token.frm
access_token.ibd                 endpoint.ibd             implied_role.frm     policy.ibd                  request_token.frm      token.ibd
application_credential.frm       endpoint_group.frm       implied_role.ibd     policy_association.frm      request_token.ibd      trust.frm
application_credential.ibd       endpoint_group.ibd       limit.frm            policy_association.ibd      revocation_event.frm   trust.ibd
application_credential_role.frm  federated_user.frm       limit.ibd            project.frm                 revocation_event.ibd   trust_role.frm
application_credential_role.ibd  federated_user.ibd       local_user.frm       project.ibd                 role.frm               trust_role.ibd
assignment.frm                   federation_protocol.frm  local_user.ibd       project_endpoint.frm        role.ibd               user.frm
assignment.ibd                   federation_protocol.ibd  mapping.frm          project_endpoint.ibd        sensitive_config.frm   user.ibd
config_register.frm              group.frm                mapping.ibd          project_endpoint_group.frm  sensitive_config.ibd   user_group_membership.frm
config_register.ibd              group.ibd                migrate_version.frm  project_endpoint_group.ibd  service.frm            user_group_membership.ibd
consumer.frm                     id_mapping.frm           migrate_version.ibd  project_tag.frm             service.ibd            user_option.frm
consumer.ibd                     id_mapping.ibd           nonlocal_user.frm    project_tag.ibd             service_provider.frm   user_option.ibd
credential.frm                   identity_provider.frm    nonlocal_user.ibd    region.frm                  service_provider.ibd   whitelisted_config.frm
credential.ibd                   identity_provider.ibd    password.frm         region.ibd                  system_assignment.frm  whitelisted_config.ibd
db.opt                           idp_remote_ids.frm       password.ibd         registered_limit.frm        system_assignment.ibd
```



7.

```sql
# keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
# keystone-manage credential_setup --keystone-user keystone --keystone-group keystone
# keystone-manage bootstrap --bootstrap-password ADMIN_PASS   --bootstrap-admin-url http://controller:5000/v3/   --bootstrap-internal-url http://controller:5000/v3/   --bootstrap-public-url http://controller:5000/v3/   --bootstrap-region-id RegionOne
> 비밀번호는 'ADMIN_PASS'
```

8. 서버네임 지정

   ```sql
   vi /etc/httpd/conf/httpd.conf
   
   :set nu 통해 줄 보기 > 95번째 줄
   
   ServerName controller
   :wq! 로 저장하고 나오기
   ```

9. 네트워크 설정

   ```sql
   # ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/
   
   # systemctl enable httpd.service
   # systemctl start httpd.service
   ```

   ```sql
   # export OS_USERNAME=admin
   # export OS_PASSWORD=ADMIN_PASS
   # export OS_PROJECT_NAME=admin
   # export OS_USER_DOMAIN_NAME=Default
   # export OS_PROJECT_DOMAIN_NAME=Default
   # export OS_AUTH_URL=http://controller:5000/v3
   # export OS_IDENTITY_API_VERSION=3
   ```

   ```sql
   [root@controller mysql]# openstack user list
   +----------------------------------+-------+
   | ID                               | Name  |
   +----------------------------------+-------+
   | 802d6ed0398241089a76978bd48dcbc6 | admin |
   +----------------------------------+-------+
   이렇게 나오는지 확인!
   ```

   

## 9. Create a domain, projects, users, and roles

1. 도메인 생성

   ```sql
   $ openstack domain create --description "An Example Domain" example
   
   +-------------+----------------------------------+
   | Field       | Value                            |
   +-------------+----------------------------------+
   | description | An Example Domain                |
   | enabled     | True                             |
   | id          | 2f4f80574fd84fe6ba9067228ae0a50c |
   | name        | example                          |
   | tags        | []                               |
   +-------------+----------------------------------+
   ```

2. 서비스 프로젝스 생성

   ```sql
   $ openstack project create --domain default \
     --description "Service Project" service
   
   +-------------+----------------------------------+
   | Field       | Value                            |
   +-------------+----------------------------------+
   | description | Service Project                  |
   | domain_id   | default                          |
   | enabled     | True                             |
   | id          | 24ac7f19cd944f4cba1d77469b2a73ed |
   | is_domain   | False                            |
   | name        | service                          |
   | parent_id   | default                          |
   | tags        | []                               |
   +-------------+----------------------------------+
   ```

3. 데모 프로젝트 생성

   ```sql
   $ openstack project create --domain default \
     --description "Demo Project" myproject
   
   +-------------+----------------------------------+
   | Field       | Value                            |
   +-------------+----------------------------------+
   | description | Demo Project                     |
   | domain_id   | default                          |
   | enabled     | True                             |
   | id          | 231ad6e7ebba47d6a1e57e1cc07ae446 |
   | is_domain   | False                            |
   | name        | myproject                        |
   | parent_id   | default                          |
   | tags        | []                               |
   +-------------+----------------------------------+
   ```

4. 유져 생성

   ```sql
   $ openstack user create --domain default \
     --password abc123 myuser
   
   User Password:
   Repeat User Password:
   +---------------------+----------------------------------+
   | Field               | Value                            |
   +---------------------+----------------------------------+
   | domain_id           | default                          |
   | enabled             | True                             |
   | id                  | aeda23aa78f44e859900e22c24817832 |
   | name                | myuser                           |
   | options             | {}                               |
   | password_expires_at | None                             |
   +---------------------+----------------------------------+
   ```

5. 마이롤 생성 (일반 사용자)

   ```sql
   openstack role create myrole
   
   +-----------+----------------------------------+
   | Field     | Value                            |
   +-----------+----------------------------------+
   | domain_id | None                             |
   | id        | 997ce8d05fc143ac97d83fdfb5998552 |
   | name      | myrole                           |
   +-----------+----------------------------------
   
    openstack role add --project myproject --user myuser myrole
   ```





----

## client environment scripts

### 1. Controller 확인

```sql
[root@controller ~]# . keystonerc_admin
[root@controller ~(keystone_admin)]# cat keystonerc_admin
unset OS_SERVICE_TOKEN
    export OS_USERNAME=admin
    export OS_PASSWORD='abc123'
    export OS_REGION_NAME=RegionOne
    export OS_AUTH_URL=http://10.0.0.100:5000/v3
    export PS1='[\u@\h \W(keystone_admin)]\$ '
    
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_IDENTITY_API_VERSION=3
```

### 2. manual 확인

```yml
vi admin-openrc

export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=ADMIN_PASS
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
export PS1='[\u@\h \W(keystone_admin)]\$ '
```

```sql
vi demo-openrc

export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=myproject
export OS_USERNAME=myuser
export OS_PASSWORD=abc123
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
export PS1='[\u@\h \W(keystone_myuser)]\$ '
```

### 3. admin-openrc

```sql
[root@controller ~] . admin-openrc

[root@controller ~(keystone_admin)]# openstack token issue
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                                                                   |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| expires    | 2020-01-10T05:26:03+0000                                                                                                                                                                |
| id         | gAAAAABeF_zbLjFh-NyHacUrV73Bv7hOnCFhqvNtee0Dd3RlBwHJ2HwjilFD4svy_G5EqE-Lrbo77ZfX6fJla43hMXSNAN6IlJUn6B23c88JwvVQ8dQGD8c9Qa9bn0dia3TPph0N-YtD3rBPjex4aPKbQfCVblET26nEv3Yx7YuJeyrN36c0Fng |
| project_id | 699ff56d30024d7785ef7f62834c614d                                                                                                                                                        |
| user_id    | 802d6ed0398241089a76978bd48dcbc6                                                                                                                                                        |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

---

## Glance

https://docs.openstack.org/glance/rocky/install/

### 1. MYsql 접속 및 DB생성

To create the database, complete these steps:

- Use the database access client to connect to the database server as the `root` user:

  ```
  $ mysql -u root -p
  ```

- Create the `glance` database:

  ```
  MariaDB [(none)]> CREATE DATABASE glance;
  ```

- Grant proper access to the `glance` database:

  ```
  MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
    IDENTIFIED BY 'GLANCE_DBPASS';
    
  MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
    IDENTIFIED BY 'GLANCE_DBPASS';
  ```

  Replace `GLANCE_DBPASS` with a suitable password.

  

### 2. CLI

Source the `admin` credentials to gain access to admin-only CLI commands:

```
$ . admin-openrc
```

To create the service credentials, complete these steps:

- Create the `glance` user:

  ```
  $ openstack user create --domain default --password GLANCE_PASS glance
  
  User Password:
  Repeat User Password:
  +---------------------+----------------------------------+
  | Field               | Value                            |
  +---------------------+----------------------------------+
  | domain_id           | default                          |
  | enabled             | True                             |
  | id                  | 3f4e777c4062483ab8d9edd7dff829df |
  | name                | glance                           |
  | options             | {}                               |
  | password_expires_at | None                             |
  +---------------------+----------------------------------+
  ```

- Add the `admin` role to the `glance` user and `service` project:

  ```
  $ openstack role add --project service --user glance admin
  ```

- Create the `glance` service entity:

  ```
  $ openstack service create --name glance \
    --description "OpenStack Image" image
  
  +-------------+----------------------------------+
  | Field       | Value                            |
  +-------------+----------------------------------+
  | description | OpenStack Image                  |
  | enabled     | True                             |
  | id          | 8c2c7f1b9b5049ea9e63757b5533e6d2 |
  | name        | glance                           |
  | type        | image                            |
  +-------------+----------------------------------+
  ```

### 3. API 이미지 생성

Create the Image service API endpoints:

```
$ openstack endpoint create --region RegionOne \
  image public http://controller:9292

+--------------+----------------------------------+
| Field        | Value                            |
+--------------+----------------------------------+
| enabled      | True                             |
| id           | 340be3625e9b4239a6415d034e98aace |
| interface    | public                           |
| region       | RegionOne                        |
| region_id    | RegionOne                        |
| service_id   | 8c2c7f1b9b5049ea9e63757b5533e6d2 |
| service_name | glance                           |
| service_type | image                            |
| url          | http://controller:9292           |
+--------------+----------------------------------+

$ openstack endpoint create --region RegionOne \
  image internal http://controller:9292

+--------------+----------------------------------+
| Field        | Value                            |
+--------------+----------------------------------+
| enabled      | True                             |
| id           | a6e4b153c2ae4c919eccfdbb7dceb5d2 |
| interface    | internal                         |
| region       | RegionOne                        |
| region_id    | RegionOne                        |
| service_id   | 8c2c7f1b9b5049ea9e63757b5533e6d2 |
| service_name | glance                           |
| service_type | image                            |
| url          | http://controller:9292           |
+--------------+----------------------------------+

$ openstack endpoint create --region RegionOne \
  image admin http://controller:9292

+--------------+----------------------------------+
| Field        | Value                            |
+--------------+----------------------------------+
| enabled      | True                             |
| id           | 0c37ed58103f4300a84ff125a539032d |
| interface    | admin                            |
| region       | RegionOne                        |
| region_id    | RegionOne                        |
| service_id   | 8c2c7f1b9b5049ea9e63757b5533e6d2 |
| service_name | glance                           |
| service_type | image                            |
| url          | http://controller:9292           |
+--------------+----------------------------------+
```

### 4. Install the packages:

```
# yum install openstack-glance
```

### 5. 파일 내용 수정

* `/etc/glance/glance-api.conf`파일 내용 수정

```
#vi  /etc/glance/glance-api.conf
앞에 '/'치고 검색할 내용 입력 > 검색할 곳에서 i 눌러 insert상태 만들기
:set nu 하면 줄 번호보임

[database]
# ...
connection = mysql+pymysql://glance:GLANCE_DBPASS@controller/glance

[keystone_authtoken]
# ...
www_authenticate_uri  = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = GLANCE_PASS

[paste_deploy]
# ...
flavor = keystone

[glance_store]
# ...
stores = file,http
default_store = file
filesystem_store_datadir = /var/lib/glance/images/

esc 누르고 :wq!로 저장하기
```

*  `/etc/glance/glance-registry.conf`  파일 수정

```sql
vi /etc/glance/glance-registry.conf


connection = mysql+pymysql://glance:GLANCE_DBPASS@controller/glance

[keystone_authtoken]
www_authenticate_uri  = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = GLANCE_PASS

[paste_deploy]
# ...
flavor = keystone
```

### 6. 이미지 올리기

```sql
# su -s /bin/sh -c "glance-manage db_sync" glance
```

### 7. 서비스 올리기

```sql
# systemctl enable openstack-glance-api.service \
  openstack-glance-registry.service
# systemctl start openstack-glance-api.service \
  openstack-glance-registry.service
  
# yum install -y openstack-utils
```

### 8. wget

```sql
yum install -y wget
```

http://download.cirros-cloud.net/0.3.5/ 에서 0.3.5에서   `cirros-0.3.5-powerpc-disk.img `   다운

disk.img에서 우클릭해 링크주소복사

```sql
wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-powerpc-disk.img

[root@controller ~(keystone_admin)]# ls
admin-openrc  anaconda-ks.cfg  cirros-0.3.5-powerpc-disk.img  demo-openrc
[root@controller ~(keystone_admin)]# file    cirros-0.3.5-powerpc-disk.img  
cirros-0.3.5-powerpc-disk.img: QEMU QCOW Image (v2), 65802240 bytes


[root@controller ~(keystone_admin)]# openstack image create "cirros" --file cirros-0.3.5-powerpc-disk.img --disk-format qcow2 --container-format bare --public
여기서 파일이름 할때에는 c까지만 치고 tab키 이용해 오류 줄이기!
+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field            | Value                                                 

[root@controller ~(keystone_admin)]# openstack image list
+--------------------------------------+--------+--------+
| ID                                   | Name   | Status |
+--------------------------------------+--------+--------+
| 216f02ea-d53c-4882-9aac-023774ec198e | cirros | active |
+--------------------------------------+--------+--------+

[root@controller ~(keystone_admin)]# ls /var/lib/glance/images
216f02ea-d53c-4882-9aac-023774ec198e

[root@controller ~(keystone_admin)]# ls -l /var/lib/glance/images/
합계 16696
-rw-r-----. 1 glance glance 17094656  1월 10 15:17 216f02ea-d53c-4882-9aac-023774ec198e

[root@controller ~(keystone_admin)]# glance image-show 216f02ea-d53c-4882-9aac-023774ec198e
+------------------+----------------------------------------------------------------------------------+
| Property         | Value                                               
+------------------+----------------------------------------------------------------------------------+
| checksum         | 89f90326360a1d0d6d15e018aafcfb45                            
[root@controller ~(keystone_admin)]#history로 지금까지 실행한 명령어 확인 가능
```

### 9.

controller에서

```sql
[root@controller images(keystone_admin)]# cd
[root@controller ~(keystone_admin)]# scp 10.0.0.11:/root/ci* .
```

admin에서 작업

현재 controller에 이미지 없기 때문에 이미지를 manual로부터 가져와야 하므로 scp ㅣㅇ용해 현재 디렉토리(.)로 이동

```sql
# qemu-img info cirros-0.3.5-powerpc-disk.img 

image: cirros-0.3.5-powerpc-disk.img
file format: qcow2
virtual size: 63M (65802240 bytes)
```

이미지의 크기 및 포맷 구체적으로 확인 가능

```sql
[root@controller ~(keystone_admin)]# qemu-img convert -O vmdk cirros-0.3.5-powerpc-disk.img cirros-0.3.5-powerpc-disk.vmdk

[root@controller ~(keystone_admin)]# ls -l ci*
-rw-r--r-- 1 root root 17094656  1월 10 15:28 cirros-0.3.5-powerpc-disk.img
-rw-r--r-- 1 root root 45416448  1월 10 15:31 cirros-0.3.5-powerpc-disk.vmdk

```

img 이미지를 vmdk(가상머신디스크포맷)로 변환가능

---

### 10. 

콘트롤러에서 작업

```sql
[root@controller ~(keystone_admin)]# mkdir /win > win 폴더 제작
[root@controller ~(keystone_admin)]# vmhgfs-fuse /win > 공유폴더로 접근
```

공유폴더하기 전에 가상머신에서 VM>settings>share 선택 필요

```sql
# cp cirros-0.3.5-powerpc-disk.vmdk /win/share
```

share에 생산된 vmdk를 openstack폴더에 cirros 폴더를 만들어 복사 붙여넣기

VMware에서 cirros 가상머신 새로 만들기 (새로 생성이 아닌 이미 있는 vmdk use 선택

### 11. port 확인

```sql
[root@controller ~(keystone_admin)]# ss -nlp|grep 9292
tcp    LISTEN     0      128       *:9292                  *:*                   users:(("glance-api",pid=23093,fd=4),("glance-api",pid=23092,fd=4),("glance-api",pid=23041,fd=4))

```

nova에서 9292로 요청이 들어오면 파일 나눠줌

---

### 12. 클라우드 이미지 사용하기

cloud.centos.org/centos/7/images/

```sql
# wget http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-1508.qcow2c
```

```sql
# openstack image create "centos7" --file CentOS-7-x86_64-GenericCloud-1508.qcow2c --disck-format qcow2 --container-format bare --public
```

----



## NOVA

https://docs.openstack.org/nova/rocky/install/

### controller에서 확인

```sql
[root@controller ~(keystone_admin)]# mysql -uroot

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| cinder             |
| glance             |
| information_schema |
| keystone           |
| mysql              |
| neutron            |
| nova               |
| nova_api           |
| nova_cell0         |
| nova_placement     |
| performance_schema |
| test               |
+--------------------+
12 rows in set (0.04 sec)

[root@controller ~(keystone_admin)]# cd /var/lib/mysql

[root@controller mysql(keystone_admin)]# ls
aria_log.00000001  glance       ibdata1            mysql       nova        nova_placement      test
aria_log_control   ib_logfile0  keystone           mysql.sock  nova_api    performance_schema
cinder             ib_logfile1  multi-master.info  neutron     nova_cell0  tc.log

[root@controller mysql(keystone_admin)]# ls nova

[root@controller mysql(keystone_admin)]# ss -nlp|grep 8774
tcp    LISTEN     0      128       *:8774                  *:*                   users:(("nova-api",pid=3031,fd=6),("nova-api",pid=3030,fd=6),("nova-api",pid=3024,fd=6),("nova-api",pid=3022,fd=6),("nova-api",pid=1553,fd=6))
# ss -nlp|grep [포트번호] : 포트번호가 개방되었는지 확인하는 명령어
# 프로세스 이름은 "nova-api"

[root@controller mysql(keystone_admin)]# ss -nlp|grep 8778
# httpd에서 웹서비스 진행
>키스톤 : 5000 / 호라이즌 : 80 / nova : 8778
```

* 콘솔 커넥트가 잘 안된다면 `vnc`가 잘못된것! (controller / 10.0.0.100 이 들어가야함)

* instance console 접속

  ```sql
  # .keystonerc_stack1
  # nova list
  # virsh list --all
  # virsh console 1 > 웹에 접속하지 않고도 콘솔 접속 가능
  # ctrl 누른 상태에서 ^] > 디스커넥트
  ```

* 

---

# compute1 만들기

![image-20200110171128890](images/image-20200110171128890.png)

> 아래의 `number of cores per processor`이 CPU의 개수

