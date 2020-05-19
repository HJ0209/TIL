OPENSTACK 03



# 1. Dashboard

1. 인터넷 창에서 controller IP로 접속 : `10.0.0.100`

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

   