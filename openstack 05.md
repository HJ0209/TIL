20.01.14 오픈스택 05



# NOVA 설치

### 1. [오픈스택](https://docs.openstack.org/nova/rocky/install/) 사이트 연결

### 2. controller에서 확인 및 설치

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

### 3. Compute1 만들기

![image-20200110171128890](images/image-20200110171128890.png)

> 아래의 `number of cores per processor`이 CPU의 개수



* hostname 바꾸기

  ```sql
  hostnamectl > 호스트이름 확인
  
  hostnamectl set-hostname [설정할 이름] > 호스트 이름변경
  
  exit 이후 다시 연결하면 이름 바꿔져있음
  ```

  

* IP바꾸기

  ```sql
  vi /etc/sysconfig/network-scripts/ifcfg-ens33
  
  #UUID
  IPADD "10.0.0.101" 등 원하는 ip주소로 바꾸기
  ```



### 4. nova 파일 다운받기

```sql
[root@compute1 ~]# yum install openstack-nova-compute -y
```

### 5. 외부에서 내용을 복사해 사용하기

```sql
[root@compute1 ~]# cp /etc/nova/nova.conf /etc/nova/nova.conf.old
파일 복사해 두기

[root@compute1 ~]#  scp controller:/etc/nova/nova.comf
usage: scp [-12346BCpqrv] [-c cipher] [-F ssh_config] [-i identity_file]
           [-l limit] [-o ssh_option] [-P port] [-S program]
           [[user@]host1:]file1 ... [[user@]host2:]file2

```

콘트롤러 안에 `sshd` 존재하는 것을 연결할 때, `# ssh`명령어 이용

파일 전송 명령어 : # ssh / # scp / #sftp

```sql
vi /etc/nova/nova.conf

My_ip = 10.0.0.10 > 주석해제
vncserver_proxyclient_address=10.0.0.101

systemctl enable libvirtd.service openstack-nova-compute.service
systemctl start libvirtd.service
systemctl start  openstack-nova-compute.service
```

### 6. controller에서 ip추가

```sql
vi /etc/sysconfig/iptables

13번 아래에 추가 (줄 번호는 :set nu)
-A INPUT -s 10.0.0.101/32 -p tcp -m multiport --dports 5671,5672 -m comment --comment "001 amqp incoming amqp_10.0.0.101" -j ACCEPT
-A INPUT -s 10.0.0.101/32 -p tcp -m multiport --dports 5671,5672 -j ACCEPT
-A INPUT -s 10.0.0.100/32 -p tcp -m multiport --dports 5671,5672 -j ACCEPT

systemctl reload iptables
```

controller에 compute1의 방화벽 저장하는 것

완료가 되었다면, compute1에서 status 에서 날짜로 바뀐건 뜸

```sql
[root@compute1 ~]# systemctl status openstack-nova-compute.service
● openstack-nova-compute.service - OpenStack Nova Compute Server
   Loaded: loaded (/usr/lib/systemd/system/openstack-nova-compute.service; enabled; vendor preset: disabled)
   Active: active (running) since 화 2020-01-14 09:56:21 KST; 2min 46s ago
 Main PID: 2398 (nova-compute)
    Tasks: 22
   CGroup: /system.slice/openstack-nova-compute.service
           └─2398 /usr/bin/python2 /usr/bin/nova-compute

 1월 14 09:55:00 compute1 systemd[1]: Starting OpenStack Nova Compute Server...
 1월 14 09:56:21 compute1 systemd[1]: Started OpenStack Nova Compute Server.
> 뒤에 날짜 뜨면 성공!
```

```sql
[root@controller ~]# . keystonerc_admin

[root@controller ~(keystone_admin)]# openstack compute service list --service nova-compute

+----+--------------+------------+------+---------+-------+----------------------------+
| ID | Binary       | Host       | Zone | Status  | State | Updated At                 |
+----+--------------+------------+------+---------+-------+----------------------------+
|  7 | nova-compute | controller | nova | enabled | up    | 2020-01-14T01:01:35.000000 |
|  8 | nova-compute | compute1   | nova | enabled | up    | 2020-01-14T01:01:33.000000 |
+----+--------------+------------+------+---------+-------+----------------------------+

> 이렇게 compute1이 연결되었음을 확인
```

### 7. discover

```sql
 su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova

Found 2 cell mappings.
Skipping cell0 since it does not contain hosts.
Getting compute nodes from cell 'cell1': ad5a5985-a719-4567-98d8-8d148aaae4bc
Found 1 computes in cell: ad5a5985-a719-4567-98d8-8d148aaae4bc
Checking host mapping for compute host 'compute': fe58ddc1-1d65-4f87-9456-bc040dc106b3
Creating host mapping for compute host 'compute': fe58ddc1-1d65-4f87-9456-bc040dc106b3

> found1가 있는지 확인
```

```sql
vi /etc/nova/nova.conf

:set nu
:9662

[scheduler]
discover_hosts_in_cells_interval = 300
```

discover은 controller에 compute1의 메타정보를 지속적으로 업로드시키는 것

scheduler을 통해 compute1에서 작업한 내용을 300초 간격으로 정기적으로 확인해 수정사항을 업로드 하도록 지원하는 것

### 8.

과거에는 방화벽을 지원했으나, 이제 쓰지 않겠다는 설치 필요

```
[vnc]
# ...
enabled = true
server_listen = 0.0.0.0
server_proxyclient_address = $my_ip 
> 여기서 오류 많이나기 때문에 오류나면 여기서부터 살펴보기
novncproxy_base_url = http://controller:6080/vnc_auto.html
```

```
[glance] > glance와 연결필요
# ...
api_servers = http://controller:9292
```

---



## Neutron

> NOVA완성을 위한 compute1을 완성하기 위해서는 네트워크 서비스를 제공하는 `neutron`이 완성되어 있어야 하므로 먼저 작업

* [Openstack](https://docs.openstack.org/neutron/rocky/install/) install 페이지로연결



### 0. 내용

```sql
[root@controller ~(keystone_admin)]# ls
anaconda-ks.cfg                cirros-0.3.5-powerpc-disk.vmdk  openstack.old
cirros-0.3.5-powerpc-disk.img  keystonerc_admin                openstack.txt

[root@controller ~(keystone_admin)]# grep NEU openstack.txt
CONFIG_NEUTRON_INSTALL=y
# CONFIG_NEUTRON_OVS_BRIDGE_IFACES,
# CONFIG_NEUTRON_LB_INTERFACE_MAPPINGS, CONFIG_NEUTRON_OVS_TUNNEL_IF.
CONFIG_NEUTRON_KS_PW=3f36ff8e92e848a9
CONFIG_NEUTRON_DB_PW=5c057084fe7143de
CONFIG_NEUTRON_L3_EXT_BRIDGE=br-ex
CONFIG_NEUTRON_METADATA_PW=e2cffa23568a41e0
CONFIG_NEUTRON_METERING_AGENT_INSTALL=y
CONFIG_NEUTRON_FWAAS=n
> 방화벽인 FWAAS가 n으로 설치되어 있지 않음
방화벽이 설치되어 있어야 다른 여러 서비스 제공 가능
```

* 중요한 정보의 경우에는 외부에서 바로 접속할 수 없도록 제한 필요
  - 서브네트워크 형태로 중간에 접속 제한필요
  - 서브네트워크를 중개하는 역할을 하는 라우터 필요

* HA구조

  * DNS는 HA구조와 달리 하나의 호스트가 다운되면 이를 다른 곳에서 알지 못하고 요청했다가 응답을 받지 못할 수 있음 > 고가형성 ip address는 아님

  * HA는 back의 webserver의 상태를 주기적으로 체크해 하나의 웹서버가 다운되어도 다른 웹서버에 요청해 고객에게 지속적으로 서비스 제공

    (클라이언트는 요청에 대해 무조건 응답을 받을 수 있어 어떤 웹서버가 다운되었는지 알 수 없음)

* 가상머신(INSTANCE) 확인 : 가상머신도 하나의 프로세스로 동작

```sql
[root@controller ~(keystone_admin)]# ps -ef|grep kvm

[root@controller ~(keystone_admin)]# virsh list --all
 Id    이름                         상태
----------------------------------------------------

[root@controller ~(keystone_admin)]# ovs-vsctl show
    Bridge br-int
    Bridge br-tun
    Bridge br-ex
```

### 1. 라우터 IP 확인 및 연결

```sql
[root@controller ~]# ip netns
qrouter-ec1ac10d-bb1b-43d1-8f13-b654ad1a2371 (id: 1)
qdhcp-5c0b47cc-78f8-467b-a688-bbd533d80b43 (id: 0)
> ip 확인

[root@controller ~]# ip netns exec qrouter-ec1ac10d-bb1b-43d1-8f13-b654ad1a2371 /bin/bash
> qrouter로 연결

[root@controller ~]# ip a > 연결되었는지 ip로 확인
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
11: qr-f2fc1aeb-b9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether fa:16:3e:c5:c4:18 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.254/24 brd 10.1.1.255 scope global qr-f2fc1aeb-b9
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:fec5:c418/64 scope link 
       valid_lft forever preferred_lft forever
12: qg-50a2939f-10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether fa:16:3e:1b:b0:cb brd ff:ff:ff:ff:ff:ff
    inet 192.168.108.22/24 brd 192.168.108.255 scope global qg-50a2939f-10
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:fe1b:b0cb/64 scope link 
       valid_lft forever preferred_lft forever
```

```sql
[root@controller ~]# iptables -t nat -L
SNAT, DNAT 확인
```

* 라우터에서 계속 작업하면 오류나므로, 꼭 `exit` 통해 연결 끊고 작업하기



### 2. Controller Node

* 포트확인

  ```sql
  [root@controller ~]# ss -nlp|grep 9696
  tcp    LISTEN     0      128       *:9696                  *:*                   users:
  (("neutron-server",pid=2938,fd=7),("neutron-server",pid=2936,fd=7),("neutron-server",pid=2933,fd=7),("neutron-server",pid=2932,fd=7),("neutron-server",pid=2929,fd=7),("neutron-server",pid=2928,fd=7),("neutron-server",pid=1557,fd=7))
  
  ```



* install page에서는 linux 컨트롤을 이용함
* flat, vlan, vxlan > 관련 파라미터들도 함께 설정 필요

* `PROVIDER_INTERFACT_NAME`는 우리는 `ens33`



#### 네트워크노드

* metadata agent : controller의 DB인 neutron, mysql을 compute노드에서 정보를 받아 사용하도록 정보를 가운데서 중개하는 역할 담당

* ```sql
  vi /etc/neutron/metadata_agent.ini
  
  [DEFAULT]
  # ...
  nova_metadata_host = controller
  metadata_proxy_shared_secret = METADATA_SECRET
  ```

* ```sql
  vi /etc/nova/nova.conf
  
  [neutron]
  # ...
  url = http://controller:9696
  auth_url = http://controller:5000
  auth_type = password
  project_domain_name = default
  user_domain_name = default
  region_name = RegionOne
  project_name = service
  username = neutron
  password = NEUTRON_PASS
  service_metadata_proxy = true
  metadata_proxy_shared_secret = METADATA_SECRET
  ```

* default와 neutron의 `metadata_proxy_shared_secret` 값이 동일해야! (랜덤패스워드)



* 테이블 제작

  ```sql
  su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf \
    --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron
    
  pwd
  /var/lib/mysql
  ls neutron
  이를 통해 neutron이 잘 제작되었는지 확인 필요
  ```

* 라우터 시작

  ```sql
  # systemctl enable neutron-l3-agent.service
  # systemctl start neutron-l3-agent.service
  ```

  

### 3. Compute Node

> `compute1`에서 작업해야함을 꼭 확인! `controller` 아님!



1. 구성요소 설치

   ```sql
   yum install openstack-neutron-linuxbridge ebtables ipset
   ```

2.  `neutron.conf` 파일 수정

   ```sql
   [root@compute1 ~]# cd /etc/neutron
   [root@compute1 neutron]# ls
   conf.d  neutron.conf  plugins  rootwrap.conf
   
   [root@compute1 neutron]# cp neutron.conf neutron.conf.old
   [root@compute1 neutron]# scp controller:/etc/neutron/neutron.conf neutron.conf
   > 옛날 파일 복사해 저장해두기
   
   [root@compute1 neutron]# vi /etc/neutron/neutron.conf
       761 #connection=mysql+pymysql://neutron:5c057084fe7143de@10.0.0.100/neutron
   > connection 쓰지 않도록 주석처리
   ```

3. `linuxbridge_agent`

   ```sql
   [root@compute1 neutron]# vi /etc/neutron/plugins/ml2/linuxbridge_agent.ini
       
       146 [linux_bridge]
       147 physical_interface_mappings = provider:ens33
   
      205 [vxlan]
       206 enable_vxlan = true
       207 local_ip = 10.0.0.101
       208 l2_population = true
   
       182 [securitygroup]
       183 enable_security_group = true
       184 firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver
   ```

4. mod파일 올리기

```sql
[root@compute1 neutron]# modprobe br_netfilter
> mod 파일올리기 (브릿지 기반의 방화벽을 지원하기 위한 모듈)

[root@compute1 neutron]# lsmod|grep br_netfilter
> mod 파일 올라갔는지 list확인
br_netfilter           22256  0 
bridge                151336  1 br_netfilter

[root@compute1 neutron]# sysctl -a|grep nf-cal
net.bridge.bridge-nf-call-arptables = 0
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
sysctl: reading key "net.ipv6.conf.all.stable_secret"
sysctl: reading key "net.ipv6.conf.default.stable_secret"
sysctl: reading key "net.ipv6.conf.ens33.stable_secret
```

5. 시스템 시작하기

```sql
[root@compute1 neutron]# systemctl enable neutron-linuxbridge-agent.service
Created symlink from /etc/systemd/system/multi-user.target.wants/neutron-linuxbridge-agent.service to /usr/lib/systemd/system/neutron-linuxbridge-agent.service.

[root@compute1 neutron]# systemctl start neutron-linuxbridge-agent.service

[root@compute1 neutron]# systemctl status neutron-linuxbridge-agent
● neutron-linuxbridge-agent.service - OpenStack Neutron Linux Bridge Agent
   Loaded: loaded (/usr/lib/systemd/system/neutron-linuxbridge-agent.service; enabled; vendor preset: disabled)
   Active: active (running) since 화 2020-01-14 14:23:58 KST; 900ms ago
  Process: 14318 ExecStartPre=/usr/bin/neutron-enable-bridge-firewall.sh (code=exited, status=0/SUCCESS)
 Main PID: 14324 (neutron-linuxbr)
    Tasks: 1
   CGroup: /system.slice/neutron-linuxbridge-agent.service
           └─14324 /usr/bin/python2 /usr/bin/neutron-linuxbridge-agent --config-file /usr/share/neutron/neutron-dist.conf --config-file /etc/neutron/neutron.conf --config-fil...

 1월 14 14:23:58 compute1 systemd[1]: Starting OpenStack Neutron Linux Bridge Agent...
 1월 14 14:23:58 compute1 neutron-enable-bridge-firewall.sh[14318]: net.bridge.bridge-nf-call-iptables = 1
 1월 14 14:23:58 compute1 neutron-enable-bridge-firewall.sh[14318]: net.bridge.bridge-nf-call-ip6tables = 1
 1월 14 14:23:58 compute1 systemd[1]: Started OpenStack Neutron Linux Bridge Agent.

```

6. 오픈스택 확인하기

```sql
[root@compute1 neutron]# yum install openstack-utils -y

[root@compute1 neutron]# openstack-status
```

* 콘트롤러에서 확인하기

  ```sql
  [root@controller ~]# . keystonerc_admin
  
  [root@controller ~(keystone_admin)]# openstack network agent list
  
  > 'Linux bridge agent(host: compute1)' 네트워크가 올라와야한다.
  ```

* Linux bridge agent 추가 오류시

  ```
  [root@compute1 neutron]# vi linuxbridge-agent.log 
  [root@compute1 neutron]# getenforce
  Enforcing
  [root@compute1 neutron]# setenforce 0
  [root@compute1 neutron]# systemctl restart neutron-linuxbridge-agent
  [root@compute1 neutron]# vi linuxbridge-agent.log 
  [root@compute1 neutron]# vi /etc/selinux/config 
  SELINUX=disabled
  ```



---

# Launch an instance

> [install-guide](https://docs.openstack.org/install-guide/launch-instance.html)



## 1. flavor 만들기

```sql
[root@controller ~(keystone_admin)]# openstack flavor create --id 0 --vcpus 1 --ram 64 --disk 1 m1.nano
+----------------------------+---------+
| Field                      | Value   |
+----------------------------+---------+
| OS-FLV-DISABLED:disabled   | False   |
| OS-FLV-EXT-DATA:ephemeral  | 0       |
| disk                       | 1       |
| id                         | 0       |
| name                       | m1.nano |
| os-flavor-access:is_public | True    |
| properties                 |         |
| ram                        | 64      |
| rxtx_factor                | 1.0     |
| swap                       |         |
| vcpus                      | 1       |
+----------------------------+---------+

[root@controller ~(keystone_admin)]# openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 0  | m1.nano   |    64 |    1 |         0 |     1 | True      |
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
+----+-----------+-------+------+-----------+-------+-----------+
```



## 2. Public Key를 My Key로 등록

```sql
[root@controller ~(keystone_admin)]# ls .ssh
authorized_keys  id_rsa  id_rsa.pub  known_hosts
> pub키가 있으므로 이를 mykey로 등록

# .keystonerc_demo 
> 데모환경으로 전환

[root@controller ~(keystone_admin)]# openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| fingerprint | b9:1d:64:d1:c3:11:2e:d7:6d:73:47:69:40:2c:dd:6d |
| name        | mykey                                           |
| user_id     | cfd1ca710d9445579955e922aa883a11                |
+-------------+-------------------------------------------------+
> 마이키로 등록

[root@controller ~(keystone_admin)]# openstack keypair show mykey
+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| created_at  | 2020-01-14T06:15:46.000000                      |
| deleted     | False                                           |
| deleted_at  | None                                            |
| fingerprint | b9:1d:64:d1:c3:11:2e:d7:6d:73:47:69:40:2c:dd:6d |
| id          | 1                                               |
| name        | mykey                                           |
| updated_at  | None                                            |
| user_id     | cfd1ca710d9445579955e922aa883a11                |
+-------------+-------------------------------------------------+
```

* ```sql
  $ openstack subnet create --network selfservice \
    --dns-nameserver 8.8.4.4 --gateway 172.16.1.1 \
    --subnet-range 172.16.1.0/24 selfservice
    > B클래스 사설 IP인데 원래는 서브넷 레인지가 16으로 24로 써서 C클래스처럼 씀
  ```

* 

```sql
# openstack image create "cirros" --container-format bare --disk-format qcow2 --file ./cirros-0.3.5-x86_64-disk.img

```

```
openstack server create --flavor m1.nano --image cirros --nic net-id=63a24a4b-39ff-4699-b0bf-c95c355a9e01 --security-group dc5326b3-7e03-4c09-8495-064156ebcd20 --key-name mykey selfservice-instance
```



ctrl + 6 + ]

```
openstack server create --flavor m1.nano --image cirros --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group default --key-name mykey selfservice-instance
```

---

```sql
[root@controller ~(keystone_admin)]# openstack flavor create --id 0 --vcpus 1 --ram 64 --disk 1 m1.nano
+----------------------------+---------+
| Field                      | Value   |
+----------------------------+---------+
| OS-FLV-DISABLED:disabled   | False   |
| OS-FLV-EXT-DATA:ephemeral  | 0       |
| disk                       | 1       |
| id                         | 0       |
| name                       | m1.nano |
| os-flavor-access:is_public | True    |
| properties                 |         |
| ram                        | 64      |
| rxtx_factor                | 1.0     |
| swap                       |         |
| vcpus                      | 1       |
+----------------------------+---------+
[root@controller ~(keystone_admin)]# openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 0  | m1.nano   |    64 |    1 |         0 |     1 | True      |
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
+----+-----------+-------+------+-----------+-------+-----------+
[root@controller ~(keystone_admin)]# ls .ssh
authorized_keys  id_rsa  id_rsa.pub  known_hosts
[root@controller ~(keystone_admin)]# ls
anaconda-ks.cfg                cirros-0.3.5-powerpc-disk.vmdk  openstack.old
cirros-0.3.5-powerpc-disk.img  keystonerc_admin                openstack.txt
[root@controller ~(keystone_admin)]# openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
^H^H

+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| fingerprint | b9:1d:64:d1:c3:11:2e:d7:6d:73:47:69:40:2c:dd:6d |
| name        | mykey                                           |
| user_id     | cfd1ca710d9445579955e922aa883a11                |
+-------------+-------------------------------------------------+
[root@controller ~(keystone_admin)]# 
[root@controller ~(keystone_admin)]# 
[root@controller ~(keystone_admin)]# keypair show
-bash: keypair: command not found
[root@controller ~(keystone_admin)]# openstack keypair show
usage: openstack keypair show [-h] [-f {json,shell,table,value,yaml}]
                              [-c COLUMN] [--max-width <integer>]
                              [--fit-width] [--print-empty] [--noindent]
                              [--prefix PREFIX] [--public-key]
                              <key>
openstack keypair show: error: too few arguments
[root@controller ~(keystone_admin)]# openstack keypair show mykey
+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| created_at  | 2020-01-14T06:15:46.000000                      |
| deleted     | False                                           |
| deleted_at  | None                                            |
| fingerprint | b9:1d:64:d1:c3:11:2e:d7:6d:73:47:69:40:2c:dd:6d |
| id          | 1                                               |
| name        | mykey                                           |
| updated_at  | None                                            |
| user_id     | cfd1ca710d9445579955e922aa883a11                |
+-------------+-------------------------------------------------+
[root@controller ~(keystone_admin)]# openstack security group rule create --proto icmp default
More than one SecurityGroup exists with the name 'default'.
[root@controller ~(keystone_admin)]# openstack security group rule create --proto tcp --dst-port 22 default
More than one SecurityGroup exists with the name 'default'.
[root@controller ~(keystone_admin)]# openstack security group rule create --proto icmp default
More than one SecurityGroup exists with the name 'default'.
[root@controller ~(keystone_admin)]# openstack security group rule create --proto icmp default
More than one SecurityGroup exists with the name 'default'.
[root@controller ~(keystone_admin)]# openstack status security group
openstack: 'status security group' is not an openstack command. See 'openstack --help'.
Did you mean one of these?
  catalog list
  catalog show
  compute agent create
  compute agent delete
  compute agent list
  compute agent set
  compute service delete
  compute service list
  compute service set
  trust create
  trust delete
  trust list
  trust show
  tsigkey create
  tsigkey delete
  tsigkey list
  tsigkey set
  tsigkey show
[root@controller ~(keystone_admin)]# openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 0  | m1.nano   |    64 |    1 |         0 |     1 | True      |
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
+----+-----------+-------+------+-----------+-------+-----------+
[root@controller ~(keystone_admin)]# ^C
[root@controller ~(keystone_admin)]# . keystonerc demo
-bash: keystonerc: 그런 파일이나 디렉터리가 없습니다
[root@controller ~(keystone_admin)]# . keystonerc_demo
-bash: keystonerc_demo: 그런 파일이나 디렉터리가 없습니다
[root@controller ~(keystone_admin)]# ls .ssh
authorized_keys  id_rsa  id_rsa.pub  known_hosts
[root@controller ~(keystone_admin)]# openstack security group status
openstack: 'security group status' is not an openstack command. See 'openstack --help'.
Did you mean one of these?
  security group create
  security group delete
  security group list
  security group rule create
  security group rule delete
  security group rule list
  security group rule show
  security group set
  security group show
  security group unset
  secret container create
  secret container delete
  secret container get
  secret container list
  secret delete
  secret get
  secret list
  secret order create
  secret order delete
  secret order get
  secret order list
  secret store
  secret update
[root@controller ~(keystone_admin)]# openstack security group list
+--------------------------------------+---------+----------------+----------------------------------+------+
| ID                                   | Name    | Description    | Project                          | Tags |
+--------------------------------------+---------+----------------+----------------------------------+------+
| 2ff70252-1f1d-43fb-83be-043ff4a2298a | class1  | http and ssh   | f4c51202e05b415b85935d6ea7aa0ac2 | []   |
| 788946b9-b9a4-4ade-bb79-d4ec63723e8f | default | 기본 보안 그룹 | f4c51202e05b415b85935d6ea7aa0ac2 | []   |
| 9edf1d11-5306-49a3-9683-1d061af40fea | default | 기본 보안 그룹 |                                  | []   |
| dc5326b3-7e03-4c09-8495-064156ebcd20 | default | 기본 보안 그룹 | c1f084caf39e4f7b9e9ca4ab77b505cf | []   |
+--------------------------------------+---------+----------------+----------------------------------+------+
[root@controller ~(keystone_admin)]# openstack security group rule create --proto icmp dc5326b3-7e03-4c09-8495-064156ebcd20+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| created_at        | 2020-01-14T06:55:32Z                 |
| description       |                                      |
| direction         | ingress                              |
| ether_type        | IPv4                                 |
| id                | 83743922-1beb-40af-98d7-eaad5210b04f |
| name              | None                                 |
| port_range_max    | None                                 |
| port_range_min    | None                                 |
| project_id        | c1f084caf39e4f7b9e9ca4ab77b505cf     |
| protocol          | icmp                                 |
| remote_group_id   | None                                 |
| remote_ip_prefix  | 0.0.0.0/0                            |
| revision_number   | 0                                    |
| security_group_id | dc5326b3-7e03-4c09-8495-064156ebcd20 |
| updated_at        | 2020-01-14T06:55:32Z                 |
+-------------------+--------------------------------------+
[root@controller ~(keystone_admin)]# openstack security group rule create --proto tcp --dst-port 22 dc5326b3-7e03-4c09-8495-064156ebcd20
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| created_at        | 2020-01-14T06:55:48Z                 |
| description       |                                      |
| direction         | ingress                              |
| ether_type        | IPv4                                 |
| id                | 6b88db86-fea9-41df-88be-9eccd5df8121 |
| name              | None                                 |
| port_range_max    | 22                                   |
| port_range_min    | 22                                   |
| project_id        | c1f084caf39e4f7b9e9ca4ab77b505cf     |
| protocol          | tcp                                  |
| remote_group_id   | None                                 |
| remote_ip_prefix  | 0.0.0.0/0                            |
| revision_number   | 0                                    |
| security_group_id | dc5326b3-7e03-4c09-8495-064156ebcd20 |
| updated_at        | 2020-01-14T06:55:48Z                 |
+-------------------+--------------------------------------+
[root@controller ~(keystone_admin)]# openstack network create selfservice
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | UP                                   |
| availability_zone_hints   |                                      |
| availability_zones        |                                      |
| created_at                | 2020-01-14T06:55:54Z                 |
| description               |                                      |
| dns_domain                | None                                 |
| id                        | b99a54f3-c273-4b43-b9e6-af4e8afbd4b5 |
| ipv4_address_scope        | None                                 |
| ipv6_address_scope        | None                                 |
| is_default                | False                                |
| is_vlan_transparent       | None                                 |
| mtu                       | 1450                                 |
| name                      | selfservice                          |
| port_security_enabled     | True                                 |
| project_id                | c1f084caf39e4f7b9e9ca4ab77b505cf     |
| provider:network_type     | vxlan                                |
| provider:physical_network | None                                 |
| provider:segmentation_id  | 87                                   |
| qos_policy_id             | None                                 |
| revision_number           | 2                                    |
| router:external           | Internal                             |
| segments                  | None                                 |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tags                      |                                      |
| updated_at                | 2020-01-14T06:55:54Z                 |
+---------------------------+--------------------------------------+
[root@controller ~(keystone_admin)]# openstack subnet create --network selfservice   --dns-nameserver 8.8.4.4 --gateway 172.16.1.1   --subnet-range 172.16.1.0/24 selfservice
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| allocation_pools  | 172.16.1.2-172.16.1.254              |
| cidr              | 172.16.1.0/24                        |
| created_at        | 2020-01-14T06:56:01Z                 |
| description       |                                      |
| dns_nameservers   | 8.8.4.4                              |
| enable_dhcp       | True                                 |
| gateway_ip        | 172.16.1.1                           |
| host_routes       |                                      |
| id                | c50e0791-0027-4c5a-9180-fa2f371aaefb |
| ip_version        | 4                                    |
| ipv6_address_mode | None                                 |
| ipv6_ra_mode      | None                                 |
| name              | selfservice                          |
| network_id        | b99a54f3-c273-4b43-b9e6-af4e8afbd4b5 |
| project_id        | c1f084caf39e4f7b9e9ca4ab77b505cf     |
| revision_number   | 0                                    |
| segment_id        | None                                 |
| service_types     |                                      |
| subnetpool_id     | None                                 |
| tags              |                                      |
| updated_at        | 2020-01-14T06:56:01Z                 |
+-------------------+--------------------------------------+
[root@controller ~(keystone_admin)]# openstack router create router
+-------------------------+--------------------------------------+
| Field                   | Value                                |
+-------------------------+--------------------------------------+
| admin_state_up          | UP                                   |
| availability_zone_hints |                                      |
| availability_zones      |                                      |
| created_at              | 2020-01-14T06:56:10Z                 |
| description             |                                      |
| distributed             | False                                |
| external_gateway_info   | None                                 |
| flavor_id               | None                                 |
| ha                      | False                                |
| id                      | fac49205-1f91-4b40-a200-23f2717b59c4 |
| name                    | router                               |
| project_id              | c1f084caf39e4f7b9e9ca4ab77b505cf     |
| revision_number         | 1                                    |
| routes                  |                                      |
| status                  | ACTIVE                               |
| tags                    |                                      |
| updated_at              | 2020-01-14T06:56:10Z                 |
+-------------------------+--------------------------------------+
[root@controller ~(keystone_admin)]#  openstack router add subnet router selfservice
[root@controller ~(keystone_admin)]# openstack router set router --external-gateway ext1
[root@controller ~(keystone_admin)]# . keystonerc_admin 
[root@controller ~(keystone_admin)]# openstack port list --router router
+--------------------------------------+------+-------------------+-------------------------------------------------------------------------------+--------+
| ID                                   | Name | MAC Address       | Fixed IP Addresses                                                            | Status |
+--------------------------------------+------+-------------------+-------------------------------------------------------------------------------+--------+
| 31af53a2-8721-4233-8925-32d80b02d410 |      | fa:16:3e:7f:63:09 | ip_address='172.16.1.1', subnet_id='c50e0791-0027-4c5a-9180-fa2f371aaefb'     | ACTIVE |
| b3e035e7-bea8-49ff-9a03-a2bda9b829c3 |      | fa:16:3e:3d:bd:7e | ip_address='192.168.108.17', subnet_id='eaa911b9-b245-4617-ab66-a8dd7518b194' | ACTIVE |
+--------------------------------------+------+-------------------+-------------------------------------------------------------------------------+--------+
[root@controller ~(keystone_admin)]# openstack image list

[root@controller ~(keystone_admin)]# ls
anaconda-ks.cfg                cirros-0.3.5-powerpc-disk.vmdk  openstack.old
cirros-0.3.5-powerpc-disk.img  keystonerc_admin                openstack.txt
[root@controller ~(keystone_admin)]# yum install -y wget
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.kakao.com
 * centos-qemu-ev: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
base                                                                                                | 3.6 kB  00:00:00     
centos-ceph-luminous                                                                                | 2.9 kB  00:00:00     
centos-openstack-rocky                                                                              | 2.9 kB  00:00:00     
centos-qemu-ev                                                                                      | 2.9 kB  00:00:00     
extras                                                                                              | 2.9 kB  00:00:00     
updates                                                                                             | 2.9 kB  00:00:00     
Resolving Dependencies
--> Running transaction check
---> Package wget.x86_64 0:1.14-18.el7_6.1 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===========================================================================================================================
 Package                  Arch                       Version                                Repository                Size
===========================================================================================================================
Installing:
 wget                     x86_64                     1.14-18.el7_6.1                        base                     547 k

Transaction Summary
===========================================================================================================================
Install  1 Package

Total download size: 547 k
Installed size: 2.0 M
Downloading packages:
wget-1.14-18.el7_6.1.x86_64.rpm                                                                     | 547 kB  00:00:01     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : wget-1.14-18.el7_6.1.x86_64                                                                             1/1 
  Verifying  : wget-1.14-18.el7_6.1.x86_64                                                                             1/1 

Installed:
  wget.x86_64 0:1.14-18.el7_6.1                                                                                            

Complete!
[root@controller ~(keystone_admin)]# wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
--2020-01-14 15:58:20--  http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
Resolving download.cirros-cloud.net (download.cirros-cloud.net)... 64.90.42.85, 2607:f298:6:a036::bd6:a72a
Connecting to download.cirros-cloud.net (download.cirros-cloud.net)|64.90.42.85|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 13267968 (13M)
Saving to: ‘cirros-0.3.5-x86_64-disk.img’

100%[=================================================================================>] 13,267,968  3.34MB/s   in 3.8s   

2020-01-14 15:58:24 (3.34 MB/s) - ‘cirros-0.3.5-x86_64-disk.img’ saved [13267968/13267968]

[root@controller ~(keystone_admin)]# openstack image create "cirros" --container-format bare --disk-format qcow2 --file ./cirros-0.3.5-x86_64-disk.img
+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field            | Value                                                                                                                                                                                      |
+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| checksum         | f8ab98ff5e73ebab884d80c9dc9c7290                                                                                                                                                           |
| container_format | bare                                                                                                                                                                                       |
| created_at       | 2020-01-14T06:58:38Z                                                                                                                                                                       |
| disk_format      | qcow2                                                                                                                                                                                      |
| file             | /v2/images/cf43702e-c539-46ce-a5ab-45c56cd561b2/file                                                                                                                                       |
| id               | cf43702e-c539-46ce-a5ab-45c56cd561b2                                                                                                                                                       |
| min_disk         | 0                                                                                                                                                                                          |
| min_ram          | 0                                                                                                                                                                                          |
| name             | cirros                                                                                                                                                                                     |
| owner            | c1f084caf39e4f7b9e9ca4ab77b505cf                                                                                                                                                           |
| properties       | os_hash_algo='sha512', os_hash_value='f0fd1b50420dce4ca382ccfbb528eef3a38bbeff00b54e95e3876b9bafe7ed2d6f919ca35d9046d437c6d2d8698b1174a335fbd66035bb3edc525d2cdb187232', os_hidden='False' |
| protected        | False                                                                                                                                                                                      |
| schema           | /v2/schemas/image                                                                                                                                                                          |
| size             | 13267968                                                                                                                                                                                   |
| status           | active                                                                                                                                                                                     |
| tags             |                                                                                                                                                                                            |
| updated_at       | 2020-01-14T06:58:38Z                                                                                                                                                                       |
| virtual_size     | None                                                                                                                                                                                       |
| visibility       | shared                                                                                                                                                                                     |
+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
[root@controller ~(keystone_admin)]# openstack image list
+--------------------------------------+--------+--------+
| ID                                   | Name   | Status |
+--------------------------------------+--------+--------+
| cf43702e-c539-46ce-a5ab-45c56cd561b2 | cirros | active |
+--------------------------------------+--------+--------+
[root@controller ~(keystone_admin)]# openstack network list
+--------------------------------------+-------------+--------------------------------------+
| ID                                   | Name        | Subnets                              |
+--------------------------------------+-------------+--------------------------------------+
| 5c0b47cc-78f8-467b-a688-bbd533d80b43 | int1        | b3c23200-3565-4c07-8581-e311d368ff93 |
| b99a54f3-c273-4b43-b9e6-af4e8afbd4b5 | selfservice | c50e0791-0027-4c5a-9180-fa2f371aaefb |
| b9bfe3d1-c73f-4acd-9c8b-adbe06d303e5 | ext1        | eaa911b9-b245-4617-ab66-a8dd7518b194 |
+--------------------------------------+-------------+--------------------------------------+
[root@controller ~(keystone_admin)]# openstack server create --flavor m1.nano --image cirros --nic net-id=63a24a4b-39ff-4699-b0bf-c95c355a9e01 --security-group default --key-name mykey selfservice-instance
No Network found for f86403ab-adfe-4447-af09-ba92a164dcb5
[root@controller ~(keystone_admin)]# ^C
[root@controller ~(keystone_admin)]# openstack server create --flavor m1.nano --image cirros --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group dc5326b3-7e03-4c09-8495-064156ebcd20 --key-name mykey selfservice-instance
No Network found for f86403ab-adfe-4447-af09-ba92a164dcb5
[root@controller ~(keystone_admin)]# openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 0  | m1.nano   |    64 |    1 |         0 |     1 | True      |
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
+----+-----------+-------+------+-----------+-------+-----------+
[root@controller ~(keystone_admin)]# openstack network list
+--------------------------------------+-------------+--------------------------------------+
| ID                                   | Name        | Subnets                              |
+--------------------------------------+-------------+--------------------------------------+
| 5c0b47cc-78f8-467b-a688-bbd533d80b43 | int1        | b3c23200-3565-4c07-8581-e311d368ff93 |
| b99a54f3-c273-4b43-b9e6-af4e8afbd4b5 | selfservice | c50e0791-0027-4c5a-9180-fa2f371aaefb |
| b9bfe3d1-c73f-4acd-9c8b-adbe06d303e5 | ext1        | eaa911b9-b245-4617-ab66-a8dd7518b194 |
+--------------------------------------+-------------+--------------------------------------+
[root@controller ~(keystone_admin)]# openstack server create --flavor m1.nano --image cirros --nic net-id=b99a54f3-c273-4b43-b9e6-af4e8afbd4b5 --security-group dc5326b3-7e03-4c09-8495-064156ebcd20 --key-name mykey selfservice-instance
+-------------------------------------+-----------------------------------------------+
| Field                               | Value                                         |
+-------------------------------------+-----------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                        |
| OS-EXT-AZ:availability_zone         |                                               |
| OS-EXT-SRV-ATTR:host                | None                                          |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                          |
| OS-EXT-SRV-ATTR:instance_name       |                                               |
| OS-EXT-STS:power_state              | NOSTATE                                       |
| OS-EXT-STS:task_state               | scheduling                                    |
| OS-EXT-STS:vm_state                 | building                                      |
| OS-SRV-USG:launched_at              | None                                          |
| OS-SRV-USG:terminated_at            | None                                          |
| accessIPv4                          |                                               |
| accessIPv6                          |                                               |
| addresses                           |                                               |
| adminPass                           | yxoaJ7MuiVfW                                  |
| config_drive                        |                                               |
| created                             | 2020-01-14T07:02:11Z                          |
| flavor                              | m1.nano (0)                                   |
| hostId                              |                                               |
| id                                  | 65df82b2-c934-4231-92a5-23caf6effc81          |
| image                               | cirros (cf43702e-c539-46ce-a5ab-45c56cd561b2) |
| key_name                            | mykey                                         |
| name                                | selfservice-instance                          |
| progress                            | 0                                             |
| project_id                          | c1f084caf39e4f7b9e9ca4ab77b505cf              |
| properties                          |                                               |
| security_groups                     | name='dc5326b3-7e03-4c09-8495-064156ebcd20'   |
| status                              | BUILD                                         |
| updated                             | 2020-01-14T07:02:11Z                          |
| user_id                             | cfd1ca710d9445579955e922aa883a11              |
| volumes_attached                    |                                               |
+-------------------------------------+-----------------------------------------------+
[root@controller ~(keystone_admin)]# openstack server list
+--------------------------------------+----------------------+--------+------------------------+--------+---------+
| ID                                   | Name                 | Status | Networks               | Image  | Flavor  |
+--------------------------------------+----------------------+--------+------------------------+--------+---------+
| 65df82b2-c934-4231-92a5-23caf6effc81 | selfservice-instance | BUILD  | selfservice=172.16.1.5 | cirros | m1.nano |
+--------------------------------------+----------------------+--------+------------------------+--------+---------+
[root@controller ~(keystone_admin)]# openstack console url show selfservice-instance
+-------+-------------------------------------------------------------------------------------------+
| Field | Value                                                                                     |
+-------+-------------------------------------------------------------------------------------------+
| type  | novnc                                                                                     |
| url   | http://10.0.0.100:6080/vnc_auto.html?path=%3Ftoken%3D30b28c1b-832d-4fac-a5e6-c8d7be6b6aea |
+-------+-------------------------------------------------------------------------------------------+
[root@controller ~(keystone_admin)]# virsh list --all
 Id    이름                         상태
----------------------------------------------------
 1     instance-00000001              실행중

[root@controller ~(keystone_admin)]# virsh console1
오류:알려지지 않은 명령: 'console1'
[root@controller ~(keystone_admin)]# virsh console 1
도메인 instance-00000001에 연결되었습니다
Escape character is ^]
openstack floating ip create ext1
Password: 

Login incorrect
selfservice-instance login: abc123
Password: 
Login incorrect
selfservice-instance login: root
Password: 
Login incorrect

login as 'cirros' user. default password: 'cubswin:)'. use 'sudo' for root.
selfservice-instance login: openstack floating ip create ext1
Password: 
Login incorrect
selfservice-instance login: e
login as 'cirros' user. default password: 'cubswin:)'. use 'sudo' for root.
selfservice-instance login: 
login as 'cirros' user. default password: 'cubswin:)'. use 'sudo' for root.
selfservice-instance login: cirros
Password: 
$ openstack floating ip create ext1
-sh: openstack: not found
$ openstack server add floating ip selfservice-instance 10.0.0.216
-sh: openstack: not found
$ exit

login as 'cirros' user. default password: 'cubswin:)'. use 'sudo' for root.
selfservice-instance login: exit
Password: 
 
login as 'cirros' user. default password: 'cubswin:)'. use 'sudo' for root.
selfservice-instance login: 

login as 'cirros' user. default password: 'cubswin:)'. use 'sudo' for root.
selfservice-instance login: cirros
Password: 
$ 
[root@controller ~(keystone_admin)]# openstack project create --domain default --description "Service Project" service
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | Service Project                  |
| domain_id   | default                          |
| enabled     | True                             |
| id          | fab729c426cd4d48882760bede6450e9 |
| is_domain   | False                            |
| name        | service                          |
| parent_id   | default                          |
| tags        | []                               |
+-------------+----------------------------------+
[root@controller ~(keystone_admin)]# openstack project create --domain default --description "Demo Project" myproject
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | Demo Project                     |
| domain_id   | default                          |
| enabled     | True                             |
| id          | 2f946e136f4e4075b95b0f7b90e198d9 |
| is_domain   | False                            |
| name        | myproject                        |
| parent_id   | default                          |
| tags        | []                               |
+-------------+----------------------------------+
[root@controller ~(keystone_admin)]# openstack user create --domain default --password abc123 myuser
+---------------------+----------------------------------+
| Field               | Value                            |
+---------------------+----------------------------------+
| domain_id           | default                          |
| enabled             | True                             |
| id                  | 57247194d2f945679f81c370d259a0ca |
| name                | myuser                           |
| options             | {}                               |
| password_expires_at | None                             |
+---------------------+----------------------------------+
[root@controller ~(keystone_admin)]# penstack role create myrole
-bash: penstack: command not found
[root@controller ~(keystone_admin)]# openstack role create myrole
+-----------+----------------------------------+
| Field     | Value                            |
+-----------+----------------------------------+
| domain_id | None                             |
| id        | 01afdd3021eb46a99c537c4e07746085 |
| name      | myrole                           |
+-----------+----------------------------------+
[root@controller ~(keystone_admin)]# openstack role add --project myproject --user myuser myrole
[root@controller ~(keystone_admin)]# openstack project create demo
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| domain_id   | default                          |
| enabled     | True                             |
| id          | dbf4bfd339f94cfabffbbcb332c3ab26 |
| is_domain   | False                            |
| name        | demo                             |
| parent_id   | default                          |
| tags        | []                               |
+-------------+----------------------------------+
[root@controller ~(keystone_admin)]# openstack user create --domain default \
>   --password abc123 demo
+---------------------+----------------------------------+
| Field               | Value                            |
+---------------------+----------------------------------+
| domain_id           | default                          |
| enabled             | True                             |
| id                  | ad1b51ea565d49f6a65c83842adf7e3d |
| name                | demo                             |
| options             | {}                               |
| password_expires_at | None                             |
+---------------------+----------------------------------+
[root@controller ~(keystone_admin)]# openstack role add --project demo --user demo _member_

[root@controller ~(keystone_admin)]# 
[root@controller ~(keystone_admin)]# vi keystonerc_demo 
[root@controller ~(keystone_admin)]# . keystonerc_admin 
[root@controller ~(keystone_admin)]# openstack flavor create --id 0 --vcpus 1 --ram 64 --disk 1 m1.nano
ID가 0인 플레이버가 이미 있습니다. (HTTP 409) (Request-ID: req-87492d8a-dfed-4e00-98b1-08dd197e3737)
[root@controller ~(keystone_admin)]#  . keystonerc_demo 
-bash: rt: command not found
[root@controller ~(keystone_demo)]# . keystonerc_demo
-bash: rt: command not found
[root@controller ~(keystone_demo)]# ls .ssh
authorized_keys  id_rsa  id_rsa.pub  known_hosts
[root@controller ~(keystone_demo)]# openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| fingerprint | b9:1d:64:d1:c3:11:2e:d7:6d:73:47:69:40:2c:dd:6d |
| name        | mykey                                           |
| user_id     | ad1b51ea565d49f6a65c83842adf7e3d                |
+-------------+-------------------------------------------------+
[root@controller ~(keystone_demo)]# openstack keypair list
+-------+-------------------------------------------------+
| Name  | Fingerprint                                     |
+-------+-------------------------------------------------+
| mykey | b9:1d:64:d1:c3:11:2e:d7:6d:73:47:69:40:2c:dd:6d |
+-------+-------------------------------------------------+
[root@controller ~(keystone_demo)]# openstack security group rule create --proto icmp default1
Error while executing command: No SecurityGroup found for default1
[root@controller ~(keystone_demo)]# ^C
[root@controller ~(keystone_demo)]# openstack security group rule create --proto tcp --dst-port 22 default
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| created_at        | 2020-01-14T07:22:59Z                 |
| description       |                                      |
| direction         | ingress                              |
| ether_type        | IPv4                                 |
| id                | 08a76669-43dc-49f5-bd40-33b0d03fe727 |
| name              | None                                 |
| port_range_max    | 22                                   |
| port_range_min    | 22                                   |
| project_id        | dbf4bfd339f94cfabffbbcb332c3ab26     |
| protocol          | tcp                                  |
| remote_group_id   | None                                 |
| remote_ip_prefix  | 0.0.0.0/0                            |
| revision_number   | 0                                    |
| security_group_id | db6ca6f5-63df-419e-ac40-f1cb66cc0233 |
| updated_at        | 2020-01-14T07:22:59Z                 |
+-------------------+--------------------------------------+
[root@controller ~(keystone_demo)]# openstack security group rule create --proto icmp default
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| created_at        | 2020-01-14T07:23:06Z                 |
| description       |                                      |
| direction         | ingress                              |
| ether_type        | IPv4                                 |
| id                | 68d6573a-d133-4951-955c-09eedd3a101e |
| name              | None                                 |
| port_range_max    | None                                 |
| port_range_min    | None                                 |
| project_id        | dbf4bfd339f94cfabffbbcb332c3ab26     |
| protocol          | icmp                                 |
| remote_group_id   | None                                 |
| remote_ip_prefix  | 0.0.0.0/0                            |
| revision_number   | 0                                    |
| security_group_id | db6ca6f5-63df-419e-ac40-f1cb66cc0233 |
| updated_at        | 2020-01-14T07:23:06Z                 |
+-------------------+--------------------------------------+
[root@controller ~(keystone_demo)]# openstack network create selfservice
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | UP                                   |
| availability_zone_hints   |                                      |
| availability_zones        |                                      |
| created_at                | 2020-01-14T07:23:13Z                 |
| description               |                                      |
| dns_domain                | None                                 |
| id                        | 63a24a4b-39ff-4699-b0bf-c95c355a9e01 |
| ipv4_address_scope        | None                                 |
| ipv6_address_scope        | None                                 |
| is_default                | False                                |
| is_vlan_transparent       | None                                 |
| mtu                       | 1450                                 |
| name                      | selfservice                          |
| port_security_enabled     | True                                 |
| project_id                | dbf4bfd339f94cfabffbbcb332c3ab26     |
| provider:network_type     | None                                 |
| provider:physical_network | None                                 |
| provider:segmentation_id  | None                                 |
| qos_policy_id             | None                                 |
| revision_number           | 2                                    |
| router:external           | Internal                             |
| segments                  | None                                 |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tags                      |                                      |
| updated_at                | 2020-01-14T07:23:14Z                 |
+---------------------------+--------------------------------------+
[root@controller ~(keystone_demo)]# openstack subnet create --network selfservice   --dns-nameserver 8.8.4.4 --gateway 172.16.1.1   --subnet-range 172.16.1.0/24 selfservice
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| allocation_pools  | 172.16.1.2-172.16.1.254              |
| cidr              | 172.16.1.0/24                        |
| created_at        | 2020-01-14T07:23:20Z                 |
| description       |                                      |
| dns_nameservers   | 8.8.4.4                              |
| enable_dhcp       | True                                 |
| gateway_ip        | 172.16.1.1                           |
| host_routes       |                                      |
| id                | 129879cd-5756-4ce6-aff8-0a67b378d311 |
| ip_version        | 4                                    |
| ipv6_address_mode | None                                 |
| ipv6_ra_mode      | None                                 |
| name              | selfservice                          |
| network_id        | 63a24a4b-39ff-4699-b0bf-c95c355a9e01 |
| project_id        | dbf4bfd339f94cfabffbbcb332c3ab26     |
| revision_number   | 0                                    |
| segment_id        | None                                 |
| service_types     |                                      |
| subnetpool_id     | None                                 |
| tags              |                                      |
| updated_at        | 2020-01-14T07:23:20Z                 |
+-------------------+--------------------------------------+
[root@controller ~(keystone_demo)]#  openstack router create router
+-------------------------+--------------------------------------+
| Field                   | Value                                |
+-------------------------+--------------------------------------+
| admin_state_up          | UP                                   |
| availability_zone_hints |                                      |
| availability_zones      |                                      |
| created_at              | 2020-01-14T07:23:29Z                 |
| description             |                                      |
| external_gateway_info   | None                                 |
| flavor_id               | None                                 |
| id                      | 441a537e-d1ae-4117-93bf-7c542c182199 |
| name                    | router                               |
| project_id              | dbf4bfd339f94cfabffbbcb332c3ab26     |
| revision_number         | 1                                    |
| routes                  |                                      |
| status                  | ACTIVE                               |
| tags                    |                                      |
| updated_at              | 2020-01-14T07:23:29Z                 |
+-------------------------+--------------------------------------+
[root@controller ~(keystone_demo)]# openstack router add subnet router selfservice
[root@controller ~(keystone_demo)]# openstack router set router --external-gateway ext1
[root@controller ~(keystone_demo)]# . keystonerc_admin
[root@controller ~(keystone_admin)]# openstack port list --router router
More than one Router exists with the name 'router'.
[root@controller ~(keystone_admin)]# yum install -y wget
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.kakao.com
 * centos-qemu-ev: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
Package wget-1.14-18.el7_6.1.x86_64 already installed and latest version
Nothing to do
[root@controller ~(keystone_admin)]# wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
--2020-01-14 16:25:17--  http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
Resolving download.cirros-cloud.net (download.cirros-cloud.net)... 64.90.42.85, 2607:f298:6:a036::bd6:a72a
Connecting to download.cirros-cloud.net (download.cirros-cloud.net)|64.90.42.85|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 13267968 (13M)
Saving to: ‘cirros-0.3.5-x86_64-disk.img.1’

100%[=================================================================================>] 13,267,968  3.79MB/s   in 3.3s   

2020-01-14 16:25:21 (3.79 MB/s) - ‘cirros-0.3.5-x86_64-disk.img.1’ saved [13267968/13267968]

[root@controller ~(keystone_admin)]# 2 --file ./cirros-0.3.5-x86_64-disk.img
-bash: 2: command not found
[root@controller ~(keystone_admin)]# penstack server create --flavor m1.nano --image cirros \
>   --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group default \
> ^Ckey-name mykey selfservice-instance
[root@controller ~(keystone_admin)]# openstack network list
+--------------------------------------+-------------+--------------------------------------+
| ID                                   | Name        | Subnets                              |
+--------------------------------------+-------------+--------------------------------------+
| 5c0b47cc-78f8-467b-a688-bbd533d80b43 | int1        | b3c23200-3565-4c07-8581-e311d368ff93 |
| 63a24a4b-39ff-4699-b0bf-c95c355a9e01 | selfservice | 129879cd-5756-4ce6-aff8-0a67b378d311 |
| b99a54f3-c273-4b43-b9e6-af4e8afbd4b5 | selfservice | c50e0791-0027-4c5a-9180-fa2f371aaefb |
| b9bfe3d1-c73f-4acd-9c8b-adbe06d303e5 | ext1        | eaa911b9-b245-4617-ab66-a8dd7518b194 |
+--------------------------------------+-------------+--------------------------------------+
[root@controller ~(keystone_admin)]# openstack server create --flavor m1.nano --image cirros --nic net-id=63a24a4b-39ff-4699-b0bf-c95c355a9e01 --security-group dc5326b3-7e03-4c09-8495-064156ebcd20 --key-name mykey selfservice-instance
+-------------------------------------+-----------------------------------------------+
| Field                               | Value                                         |
+-------------------------------------+-----------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                        |
| OS-EXT-AZ:availability_zone         |                                               |
| OS-EXT-SRV-ATTR:host                | None                                          |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                          |
| OS-EXT-SRV-ATTR:instance_name       |                                               |
| OS-EXT-STS:power_state              | NOSTATE                                       |
| OS-EXT-STS:task_state               | scheduling                                    |
| OS-EXT-STS:vm_state                 | building                                      |
| OS-SRV-USG:launched_at              | None                                          |
| OS-SRV-USG:terminated_at            | None                                          |
| accessIPv4                          |                                               |
| accessIPv6                          |                                               |
| addresses                           |                                               |
| adminPass                           | QyF4szNs3PHU                                  |
| config_drive                        |                                               |
| created                             | 2020-01-14T07:28:09Z                          |
| flavor                              | m1.nano (0)                                   |
| hostId                              |                                               |
| id                                  | 2a5afd11-d679-47c4-843f-0f63c0888724          |
| image                               | cirros (cf43702e-c539-46ce-a5ab-45c56cd561b2) |
| key_name                            | mykey                                         |
| name                                | selfservice-instance                          |
| progress                            | 0                                             |
| project_id                          | c1f084caf39e4f7b9e9ca4ab77b505cf              |
| properties                          |                                               |
| security_groups                     | name='dc5326b3-7e03-4c09-8495-064156ebcd20'   |
| status                              | BUILD                                         |
| updated                             | 2020-01-14T07:28:09Z                          |
| user_id                             | cfd1ca710d9445579955e922aa883a11              |
| volumes_attached                    |                                               |
+-------------------------------------+-----------------------------------------------+
[root@controller ~(keystone_admin)]# . keystonerc_demo
-bash: rt: command not found
[root@controller ~(keystone_demo)]# openstack port list --router router
+--------------------------------------+------+-------------------+---------------------------------------------------------------------------+--------+
| ID                                   | Name | MAC Address       | Fixed IP Addresses                                                        | Status |
+--------------------------------------+------+-------------------+---------------------------------------------------------------------------+--------+
| 5b569b91-d2ed-4a1a-9d92-0cd7fc1e9a26 |      | fa:16:3e:8b:77:10 | ip_address='172.16.1.1', subnet_id='129879cd-5756-4ce6-aff8-0a67b378d311' | ACTIVE |
+--------------------------------------+------+-------------------+---------------------------------------------------------------------------+--------+
[root@controller ~(keystone_demo)]# yum install -y wget
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.kakao.com
 * centos-qemu-ev: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
Package wget-1.14-18.el7_6.1.x86_64 already installed and latest version
Nothing to do
[root@controller ~(keystone_demo)]# wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
--2020-01-14 16:33:17--  http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
Resolving download.cirros-cloud.net (download.cirros-cloud.net)... 64.90.42.85, 2607:f298:6:a036::bd6:a72a
Connecting to download.cirros-cloud.net (download.cirros-cloud.net)|64.90.42.85|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 13267968 (13M)
Saving to: ‘cirros-0.3.5-x86_64-disk.img.2’

100%[=================================================================================>] 13,267,968  4.07MB/s   in 3.1s   

2020-01-14 16:33:21 (4.07 MB/s) - ‘cirros-0.3.5-x86_64-disk.img.2’ saved [13267968/13267968]

[root@controller ~(keystone_demo)]# openstack server create --flavor m1.nano --image cirros --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group default --key-name mykey selfservice-instance
Could not find resource cirros
[root@controller ~(keystone_demo)]# openstack image list

[root@controller ~(keystone_demo)]# openstack image create 

```

---

```sql
    1  applying 192.168.108.151_mongodb.pp
    2  cd /var/tmp/packstack
    3  dir
    4  vi ifcfg-br-ex
    5  ip a
    6  openstack-status
    7  yum install -y openstack-utils
    8  openstack-status
    9  grep rabbit /etc/*/*conf
   10  ls -l /etc/keystone/keystone.conf
   11  vi /etc/keystone/keystone.conf
   12  . keystonerc_admin
   13  .keystonerc_admin
   14  hostnamectl
   15  ip a
   16  vi /etc/sysconfig/iptables
   17  systemctl reload iptables
   18  .keystonerc_admin
   19  . keystonerc_admin
   20  openstack compute service list --service nova-compute
   21  auth_url = http://controller:5000/v3
   22  memcached_servers = controller:11211
   23  auth_type = password
   24  project_domain_name = Default
   25  user_domain_name = Default
   26  project_name = service
   27  username = nova
   28  password = NOVA_PASS
   29  su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova
   30  vi /etc/nova/nova.conf
   31  ls
   32  grep NEU openstack.txt
   33  ps -ef|grep kvm
   34  virsh list --all
   35  ovs-vsctl show
   36  ip netns
   37  ip netns exec qrouter-ec1ac10d-bb1b-43d1-8f13-b654ad1a2371
   38  ip netns exec qrouter-ec1ac10d-bb1b-43d1-8f13-b654ad1a2371 /bin/bash
   39  ss -nlp|grep 9696
   40  . keystonerc_admin
   41  openstack network agent list
   42  openstack-status
   43  openstack network agent list
   44  openstack flavor create --id 0 --vcpus 1 --ram 64 --disk 1 m1.nano
   45  openstack flavor list
   46  ls .ssh
   47  ls
   48  openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
   49  keypair show
   50  openstack keypair show
   51  openstack keypair show mykey
   52  openstack security group rule create --proto icmp default
   53  openstack security group rule create --proto tcp --dst-port 22 default
   54  openstack security group rule create --proto icmp default
   55  openstack status security group
   56  openstack flavor list
   57  . keystonerc demo
   58  . keystonerc_demo
   59  ls .ssh
   60  openstack security group status
   61  openstack security group list
   62  openstack security group rule create --proto icmp dc5326b3-7e03-4c09-8495-064156ebcd20
   63  openstack security group rule create --proto tcp --dst-port 22 dc5326b3-7e03-4c09-8495-064156ebcd20
   64  openstack network create selfservice
   65  openstack subnet create --network selfservice   --dns-nameserver 8.8.4.4 --gateway 172.16.1.1   --subnet-range 172.16.1.0/24 selfservice
   66  openstack router create router
   67   openstack router add subnet router selfservice
   68  openstack router set router --external-gateway ext1
   69  . keystonerc_admin 
   70  openstack port list --router router
   71  openstack image list
   72  ls
   73  yum install -y wget
   74  wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
   75  openstack image create "cirros" --container-format bare --disk-format qcow2 --file ./cirros-0.3.5-x86_64-disk.img
   76  openstack image list
   77  openstack network list
   78  openstack server create --flavor m1.nano --image cirros --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group default --key-name mykey selfservice-instance
   79  openstack server create --flavor m1.nano --image cirros --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group dc5326b3-7e03-4c09-8495-064156ebcd20 --key-name mykey selfservice-instance
   80  openstack flavor list
   81  openstack network list
   82  openstack server create --flavor m1.nano --image cirros --nic net-id=b99a54f3-c273-4b43-b9e6-af4e8afbd4b5 --security-group dc5326b3-7e03-4c09-8495-064156ebcd20 --key-name mykey selfservice-instance
   83  openstack server list
   84  openstack console url show selfservice-instance
   85  virsh list --all
   86  virsh console1
   87  virsh console 1
   88  openstack project create --domain default --description "Service Project" service
   89  openstack project create --domain default --description "Demo Project" myproject
   90  openstack user create --domain default --password abc123 myuser
   91  penstack role create myrole
   92  openstack role create myrole
   93  openstack role add --project myproject --user myuser myrole
   94  openstack project create demo
   95  openstack user create --domain default   --password abc123 demo
   96  openstack role add --project demo --user demo _member_
   97  vi keystonerc_demo 
   98  . keystonerc_admin 
   99  openstack flavor create --id 0 --vcpus 1 --ram 64 --disk 1 m1.nano
  100   . keystonerc_demo 
  101  . keystonerc_demo
  102  ls .ssh
  103  openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
  104  openstack keypair list
  105  openstack security group rule create --proto icmp default1
  106  openstack security group rule create --proto tcp --dst-port 22 default
  107  openstack security group rule create --proto icmp default
  108  openstack network create selfservice
  109  openstack subnet create --network selfservice   --dns-nameserver 8.8.4.4 --gateway 172.16.1.1   --subnet-range 172.16.1.0/24 selfservice
  110   openstack router create router
  111  openstack router add subnet router selfservice
  112  openstack router set router --external-gateway ext1
  113  . keystonerc_admin
  114  openstack port list --router router
  115  yum install -y wget
  116  wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
  117  2 --file ./cirros-0.3.5-x86_64-disk.img
  118  penstack server create --flavor m1.nano --image cirros   --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group default \
  119  openstack network list
  120  openstack server create --flavor m1.nano --image cirros --nic net-id=63a24a4b-39ff-4699-b0bf-c95c355a9e01 --security-group dc5326b3-7e03-4c09-8495-064156ebcd20 --key-name mykey selfservice-instance
  121  . keystonerc_demo
  122  openstack port list --router router
  123  yum install -y wget
  124  wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
  125  openstack server create --flavor m1.nano --image cirros --nic net-id=f86403ab-adfe-4447-af09-ba92a164dcb5 --security-group default --key-name mykey selfservice-instance
  126  openstack image list
  127  history
```



![image-20200114170813697](images/image-20200114170813697.png)

요렇게 나타나야 함

---

# Cinder

## 0. 확인

```sql
[root@controller ~(keystone_demo)]# vgs
  VG             #PV #LV #SN Attr   VSize   VFree   
  cinder-volumes   1   1   0 wz--n- <20.60g 1012.00m
  cl               1   3   0 wz--n- <75.88g    4.00m
  
[root@controller ~(keystone_demo)]# pvs
  PV         VG             Fmt  Attr PSize   PFree   
  /dev/loop1 cinder-volumes lvm2 a--  <20.60g 1012.00m
  > cinder volume 있는 지 확인 필요
  /dev/sda2  cl             lvm2 a--  <75.88g    4.00m

[root@controller ~(keystone_demo)]# losetup -a
/dev/loop0: [64768]:102150288 (/srv/loopback-device/swiftloopback)
/dev/loop1: [64768]:842234 (/var/lib/cinder/cinder-volumes)

[root@controller ~(keystone_demo)]# ls -l /var/lib/cinder/cinder-volumes
-rw-r-----. 1 root root 22118662144  1월 14 09:11 /var/lib/cinder/cinder-volumes

[root@controller ~(keystone_demo)]# lvs
  LV                  VG             Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  cinder-volumes-pool cinder-volumes twi-a-tz-- 19.57g             0.00   10.55                           
  home                cl             -wi-ao---- 10.00g                                                    
  root                cl             -wi-ao---- 60.00g                                                    
  swap                cl             -wi-ao---- <5.88g                                                    
[root@controller ~(keystone_demo)]# lsblk
NAME                                          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                                             8:0    0  100G  0 disk 
├─sda1                                          8:1    0    1G  0 part /boot
└─sda2                                          8:2    0 75.9G  0 part 
  ├─cl-root                                   253:0    0   60G  0 lvm  /
  ├─cl-swap                                   253:1    0  5.9G  0 lvm  [SWAP]
  └─cl-home                                   253:2    0   10G  0 lvm  /home
sr0                                            11:0    1  4.1G  0 rom  
loop0                                           7:0    0    2G  0 loop /srv/node/swiftloopback
loop1                                           7:1    0 20.6G  0 loop 
├─cinder--volumes-cinder--volumes--pool_tmeta 253:3    0   20M  0 lvm  
│ └─cinder--volumes-cinder--volumes--pool     253:5    0 19.6G  0 lvm  
└─cinder--volumes-cinder--volumes--pool_tdata 253:4    0 19.6G  0 lvm  
  └─cinder--volumes-cinder--volumes--pool     253:5    0 19.6G  0 lvm  

```

> pool : 
>
> `pool_tmeta`와 `pool_tdata`로 구분해 파일 저장

### 1. cinder 생성

```sql
[root@controller ~(keystone_demo)]# cinder create --name demo-v1 1
+------------------------------+--------------------------------------+
| Property                     | Value                                |
+------------------------------+--------------------------------------+
| attachments                  | []                                   |
| availability_zone            | nova                                 |
| bootable                     | false                                |
| consistencygroup_id          | None                                 |
| created_at                   | 2020-01-14T07:51:39.000000           |
| description                  | None                                 |
| encrypted                    | False                                |
| id                           | 10f2022c-c49e-4d7b-8fef-edcb368f528d |
| metadata                     | {}                                   |
| multiattach                  | False                                |
| name                         | demo-v1                              |
| os-vol-tenant-attr:tenant_id | dbf4bfd339f94cfabffbbcb332c3ab26     |
| replication_status           | None                                 |
| size                         | 1                                    |
| snapshot_id                  | None                                 |
| source_volid                 | None                                 |
| status                       | creating                             |
| updated_at                   | None                                 |
| user_id                      | ad1b51ea565d49f6a65c83842adf7e3d     |
| volume_type                  | iscsi                                |
+------------------------------+--------------------------------------+

[root@controller ~(keystone_demo)]# cinder list
+--------------------------------------+----------+---------+------+-------------+----------+-------------+
| ID                                   | Status   | Name    | Size | Volume Type | Bootable | Attached to |
+--------------------------------------+----------+---------+------+-------------+----------+-------------+
| 10f2022c-c49e-4d7b-8fef-edcb368f528d | creating | demo-v1 | 1    | iscsi       | false    |             |
+--------------------------------------+----------+---------+------+-------------+----------+-------------+

[root@controller ~(keystone_demo)]# lvs > logical volume 보는 것
  LV                  VG             Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  cinder-volumes-pool cinder-volumes twi-a-tz-- 19.57g             0.00   10.55                           
  home                cl             -wi-ao---- 10.00g                                                    
  root                cl             -wi-ao---- 60.00g                                                    
  swap                cl             -wi-ao---- <5.88g    
```

* 만약 cinder volume이 없는 경우에는

  ```sql
  vgs
  
  vgimport cinder-volumes
  vgchange -a y cinder-volumes
  
  ```

  

### 2. 연결

``` sql
# nova volume-attach selfservice-instance 10f2022c-c49e-4d7b-8fef-edcb368f528d auto
> []부분에는 cinder list로 확인한 ID 

# lsblk
```



### 3. cinder 내용확인

```sql
# cinder show vol1
```





---

# Swift

## 1.  컨테이너생성

```sql
swift post d1
> post는 컨테이너 생성 (d1 생성하기)
```

## 2. 이미지 업로드 및 확인

```sql
[root@controller ~(keystone_demo)]# swift upload d1 cirros-0.3.5-x86_64-disk.img
cirros-0.3.5-x86_64-disk.img

[root@controller ~(keystone_demo)]# swift list d1 --lh
 12M 2020-01-14 08:11:44 application/octet-stream cirros-0.3.5-x86_64-disk.img
 12M
```

## 3. 이미지 다운로드

```sql
[root@controller ~(keystone_demo)]# cd /var/tmp

[root@controller tmp(keystone_demo)]# swift download d1
cirros-0.3.5-x86_64-disk.img [auth 0.638s, headers 1.697s, total 1.869s, 10.772 MB/s]
```

