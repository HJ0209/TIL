# Docker 01



## 도커의 특징(p.1)

1. 도커 : 가상화시스템. 윈도우의 물리적 하드웨어의 물리적 리소스(ram, cpu 등)를 쪼개어 나누어 윈도우가 아닌 새로운 OS를 이용할 수 있도록 지원하는 프로그램 
   컨테이너 기반의 가상화 기술 (7년도 되지 않은 기술)
   (작업관리자 > 성능 > CPU에서 가상화 활성화 되어있는지 확인)
   일반PC, AWS, Azure, Google cloud 등에서 실행가능 / 오픈소스
   컨테이너형 가상화 : 가상화 소프트웨어 없이도 운영 체제의 리소스를 격리해 가상 운영 체제로 만드는 것
2. 장점 : 경량 가상 환경(별도의 OS 등 필요없이 바로 다운 받아서 실행 가능), 이식성(개발 및 운영환경 동등하게 재현), 컨테이너 생성 속도 빠름(과거 15분 정도에서 공유한 내용을 제외해 1분 정도로 시간 감소)
   실행 환경째로 배포하는 방식으로 어플리케이션이 운영 체제의 영향을 받지 않는다.  

cf. nginx 같은 경우에는 웹서버이므로 OS인 리눅스가 필요하고, 리눅스와 nginx 모두 설치되어야 이를 기반으로 하는 어플리케이션을 실행할 수 있다.

3. 단점 : OS의 기능을 동일하게 실행하기 위해서는 도커 부적합
4. 호스트 운영체제형 가상화 vs 컨테이너형 가상화
5. Layer 저장방식 : 유니온 파일 시스템을 이용 ; 필요한 시스템이 있을때 처음부터 전부 설치하는 것이 아니라 기존에 설치되어 있는 OS 기반 위에 중복 제외하고 설치
6. 도커 이미지 : 컨테이너 실행에 필요한 파일과 설정 값 등을 포함 (상태값X) > 실체화되어 있는 것이 컨테이너 
   과거에 직접 사이트에 방문하여 Docker hub에서 이미지들 다운(pull)받음 > 쓸 수 있는 컨테이너 형태로 이용
   이미지를 가지고 있는 Hub가 마스터 / 이미지를 이용하는 측이 클라이언트



![image-20191230173834697](images/image-20191230173834697.png)

---



## 도커의 설치(p.19)

* [hub.docker.com](hub.docker.com) 에서 window로 데스트탑 버전 다운 및 설치
  * 윈도우10이 아니거나 기업용이 아닌 경우, 툴박스 설치 필요
* crul-fsSL https://get.docker.com | sudo sh (리눅스인 경우) 
  그러나 리눅스도 가상화되어 있어 리눅스 위에 도커를 올리는 경우에는, 가상화 위의 가상화로 리소스가 감소하므로 윈도우 위에 바로 설치하는 것을 추천

* reopositories > 나만의 저장소로 다른 사람과 공유가능



---

## 도커의 세팅 (p.21)

* Advanced : 도커에 할당되어 있는 CPU, 메모리, 이미지 사이즈 등 설정
  * 다른 것들은 그대로 두고, Memory만 4GB로 변경
* cmd 실행해서 확인

```sql
C:\Users\HPE>docker version

Client: Docker Engine - Community
 Version:           19.03.5
 API version:       1.40
 Go version:        go1.12.12
 Git commit:        633a0ea
 Built:             Wed Nov 13 07:22:37 2019
 OS/Arch:           windows/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.5
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.12
  Git commit:       633a0ea
  Built:            Wed Nov 13 07:29:19 2019
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.2.10
  GitCommit:        b34a5c8af56e510852c35414db4c1f4fa6172339
 runc:
  Version:          1.0.0-rc8+dev
  GitCommit:        3e425f80a8c931f88e6d94a8c831b9d5aa481657
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

> docker에서 client, server 두 가지 버전이 나와야 한다.



---

## Docker의 예제

* cmd에서 작성 시작

```sql
C:\Users\HPE>cd work

C:\Users\HPE\Work>dir
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: A0B4-FAD1

 C:\Users\HPE\Work 디렉터리

2019-12-30  오전 09:55    <DIR>          .
2019-12-30  오전 09:55    <DIR>          ..
2019-12-30  오전 09:55    <DIR>          mongodb-4.2.2
               0개 파일                   0 바이트
               3개 디렉터리  924,814,168,064 바이트 남음

C:\Users\HPE\Work>mkdir docker\day01

C:\Users\HPE\Work>cd docker\day01
```

* 이후 Visual Studio Code 실행

  * Extiension 에서 docker 검색해 보기 쉽게 설치

  * ```visual basic
    #! /bin/sh
    
    echo "Hello,World!"
    ```

  * ```dockerfile
    FROM ubuntu:16.04 #우분투 이미지를 다운받아 와서
    
    COPY helloworld /usr/local/bin  #bin에 복사해
    RUN chmod +x /usr/local/bin/helloworld 
    
    CMD ["helloworld"] #helloworld 단어로 실행
    ```

* 다시 cmd와서

```sql
C:\Users\HPE\Work\docker\day01>docker image build -t helloworld:latest .
# build해서 최신 이미지로 만듬 & 태그 (마지막에 띄어쓰기 하고 마침표 빼먹지 않도록 주의하기)

Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM ubuntu:16.04
16.04: Pulling from library/ubuntu
3386e6af03b0: Pull complete
49ac0bbe6c8e: Pull complete
d1983a67e104: Pull complete
1a0f3a523f04: Pull complete
Digest: sha256:181800dada370557133a502977d0e3f7abda0c25b9bbb035f199f5eb6082a114
Status: Downloaded newer image for ubuntu:16.04
 ---> c6a43cd4801e
Step 2/4 : COPY helloworld /usr/local/bin
 ---> dc314ad65502
Step 3/4 : RUN chmod +x /usr/local/bin/helloworld
 ---> Running in b8063773d072
Removing intermediate container b8063773d072
 ---> 1d326f6c12f6
Step 4/4 : CMD ["helloworld"]
 ---> Running in 9fc04d250ed1
Removing intermediate container 9fc04d250ed1
 ---> df21527d0c3b
Successfully built df21527d0c3b
Successfully tagged helloworld:latest
SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host. All files and directories added to build context will have '-rwxr-xr-x' permissions. It is recommended to double check and reset permissions for sensitive files and directories.
```

```sql
C:\Users\HPE\Work\docker\day01>docker image ls (#is아님 LS_list인듯)
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
helloworld          latest              df21527d0c3b        7 minutes ago       123MB
ubuntu              16.04               c6a43cd4801e        11 days ago         123MB
```



---



```sql
C:\Users\HPE\Work\docker\day01>docker container run hello-world:latest
#run = creat+start
# docker run hello-world:latest로 서도 가능

Unable to find image 'hello-world:latest' locally
#이런 파일이 없으므로 인터넷에서 다운받아 실행하겠다
latest: Pulling from library/hello-world
1b930d010525: Pull complete
Digest: sha256:4fe721ccc2e8dc7362278a29dc660d833570ec2682f4e4194f4ee23e415e1064
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

* docker image ls = docker images #이미지 목록 #list
* docker container ls =docker ps #현재 실행하고 있는 이미지 목록 #process

```sql
C:\Users\HPE\Work\docker\day01>docker run -it ubuntu:16.04 /bin/bash
root@c38c109e6182:/#
#ubuntu 를 실행 / hostname은 c38c109e6182

다른 터미널에서 docker ps를 쳐서 run되었는지 확인 > 각 창에서 실행하면 각각 독립적으로 실행된다

C:\Users\HPE>docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
c38c109e6182        ubuntu:16.04        "/bin/bash"         20 seconds ago      Up 18 seconds                           bold_moser
```

```sql
C:\Users\HPE\Work\docker\day01>docker stop c38c109e6182
#`docker ps`통해 확인한 실행되고 있는 docker 모두 직접 써서 stop
c38c109e6182

C:\Users\HPE\Work\docker\day01>docker rm c38c109e6182
#도커 안의 모든 컨테이너 삭제
c38c109e6182

C:\Users\HPE\Work\docker\day01>docker rmi c38c109e6182
#도커 안의 이미지 삭제
```

---

* docker hub에서 이미지 다운받기

```sql
C:\Users\HPE\Work\docker\day01>docker image pull gihyodocker/echo:latest
#pull이 다운받겠다는 명령어

latest: Pulling from gihyodocker/echo
723254a2c089: Downloading [============================>                      ]   25.3MB/45.12MB
abe15a44e12f: Download complete
409a28e3cc3d: Download complete
503166935590: Downloading [=============>                                     ]  13.73MB/50.02MB
abe52c89597f: Downloading [=========>                                         ]  11.24MB/57.5MB
ce145c5cf4da: Waiting
96e333289084: Waiting
39cd5f38ffb8: Waiting
22860d04f4f1: Waiting
7528760e0a03: Waiting
```

```sql
C:\Users\HPE\Work\docker\day01>docker run -t -p 9000:8080 
gihyodocker/echo:latest
#앞의 9000은 host 서버 / 8080은 게스트 서버

2019/12/30 08:36:07 start server
```

![image-20191230173850150](images/image-20191230173850150.png) > 인터넷에서 확인 가능

```sql
C:\Users\HPE>docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                    NAMES
6d7c517f4a65        gihyodocker/echo:latest   "go run /echo/main.go"   3 minutes ago       Up 3 minutes        0.0.0.0:9000->8080/tcp   epic_noether
```

---

go 디렉터리

* docker image build -t example/echo:latest .

```sql
C:\Users\HPE\Work\git\cloud-computing\04.Docker\go>docker image build -t example/echo:latest .
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM golang:1.9
1.9: Pulling from library/golang
55cbf04beb70: Pull complete
1607093a898c: Pull complete
9a8ea045c926: Pull complete
d4eee24d4dac: Pull complete
9c35c9787a2f: Pull complete
8b376bbb244f: Pull complete
0d4eafcc732a: Pull complete
186b06a99029: Pull complete
Digest: sha256:8b5968585131604a92af02f5690713efadf029cc8dad53f79280b87a80eb1354
Status: Downloaded newer image for golang:1.9
 ---> ef89ef5c42a9
Step 2/4 : RUN mkdir /echo
 ---> Running in a84018bbea00
Removing intermediate container a84018bbea00
 ---> 351ae2fd47f4
Step 3/4 : COPY main.go /echo
 ---> 8f236bb82b20
Step 4/4 : CMD ["go", "run", "/echo/main.go"]
 ---> Running in 1b4f2f8700ba
Removing intermediate container 1b4f2f8700ba
 ---> 195fe55f1be4
Successfully built 195fe55f1be4
Successfully tagged example/echo:latest
SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host. All files and directories added to build context will have '-rwxr-xr-x' permissions. It is recommended to double check and reset permissions for sensitive files and directories.
```



* docker run -t -p 10000:8080 example/echo:latest

  (9000번을 쓰고 있어서 10000으로 변경)

* docker ps

```sql
C:\Users\HPE\Work\git\cloud-computing\04.Docker\go>docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                    NAMES
421b01d79e27        gihyodocker/echo:latest   "go run /echo/main.go"   19 minutes ago      Up 19 minutes       0.0.0.0:9000->8080/tcp   cool_leakey
```

10000-> 8080으로 우회되는 것 확인



* docker exec -it (ID붙여넣기) bash

#옆에 ls /echo/main.go

```sql
C:\Users\HPE\Work\git\cloud-computing\04.Docker\go>docker exec -it 421b01d79e27 bash
root@421b01d79e27:/go# ls/echo/main.go
bash: ls/echo/main.go: No such file or directory
root@421b01d79e27:/go#
```



---

1. cmd 에서 vagrant 실행

C:\Users\HPE\Work>cd ..

C:\Users\HPE>cd study

C:\Users\HPE\study>cd vagrant

C:\Users\HPE\study\vagrant>vagrant up node01
Bringing machine 'node01' up with 'virtualbox' provider...
==> node01: Checking if box 'centos/7' version '1905.1' is up to date...
==> node01: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> node01: flag to force provisioning. Provisioners marked to run always will still run.



2. vagrant에서 

Last login: Mon Dec 30 02:32:11 2019 from 10.0.2.2
[vagrant@node01 ~]$ echo hi there
hi there
[vagrant@node01 ~]$ echo "hello, World"
hello, World
[vagrant@node01 ~]$ touch helloworld
[vagrant@node01 ~]$ vi helloworld
[vagrant@node01 ~]$ touch helloworld
[vagrant@node01 ~]$ cat helloworld
hello, world

[vagrant@node01 ~]$ .helloworld
-bash: .helloworld: command not found
[vagrant@node01 ~]$ touch Dockerfile
[vagrant@node01 ~]$ vi Dockerfile
[vagrant@node01 ~]$ 
[vagrant@node01 ~]$ 
[vagrant@node01 ~]$ ll
total 12
drwxrwxrwx. 1 vagrant vagrant 4096 Dec 23 08:33 data
-rw-rw-r--. 1 vagrant vagrant  109 Dec 30 06:41 Dockerfile
-rw-rw-r--. 1 vagrant vagrant   14 Dec 30 06:38 helloworld
drwxrwxr-x. 3 vagrant vagrant   18 Dec 30 02:48 mongo
[vagrant@node01 ~]$ docker
-bash: docker: command not found
[vagrant@node01 ~]$ cat Dockerfile
FROM ubuntu:16.04

COPY helloworld /usr/local/bin
RUN chmod +x /usr/local/bin/helloworld

CMD ["helloworld"]
[vagrant@node01 ~]$ 
[vagrant@node01 ~]$ 
[vagrant@node01 ~]$ 
[vagrant@node01 ~]$ 
[vagrant@node01 ~]$ 
[vagrant@node01 ~]$ cp clear
cp: missing destination file operand after ‘clear’
Try 'cp --help' for more information.
[vagrant@node01 ~]$ cat Dockerfile
FROM ubuntu:16.04

COPY helloworld /usr/local/bin
RUN chmod +x /usr/local/bin/helloworld

CMD ["helloworld"]
[vagrant@node01 ~]$ cp helloworld /usr/local/bin
cp: cannot create regular file ‘/usr/local/bin/helloworld’: Permission denied
[vagrant@node01 ~]$ sudo cp helloworld /usr/local/bin
[vagrant@node01 ~]$ sudo chmod +x /usr/local/bin/helloworld
[vagrant@node01 ~]$ helloworld
/usr/local/bin/helloworld: line 1: hello,: command not found
[vagrant@node01 ~]$ sudo chmod +m /usr/local/bin/helloworld
chmod: invalid mode: ‘+m’
Try 'chmod --help' for more information.
[vagrant@node01 ~]$ sudo chmod +x /usr/local/bin/helloworld
[vagrant@node01 ~]$ helloworld
/usr/local/bin/helloworld: line 1: hello,: command not found
[vagrant@node01 ~]$ 