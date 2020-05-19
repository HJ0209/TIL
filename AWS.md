# AWS

## 1. AWS란

* 아마존닷컴에서 개발한 클라우드 컴퓨팅 플랫폼
* 아마존(Amazon)에서 제공하는 클라우드 서비스로, 네트워킹을 기반으로 가상 컴퓨터와 스토리지, 네트워크 인프라 등 다양한 서비스를 제공
* 저렴한 비용 (종량 과금제 방식, 사용한 만큼 비용 청구)
* 데이터센터운영 및 유지관리에 비용을 투자할 필요가 없어 속도 및 민첩성 개선(언어 및 운영체제에 구애받지 않음)
* 물리적 서버를 구축하길 기다리는 대신 즉시 새로운 앱 배포 및 조정 가능
* 규모의 경제
* CentOS > 프리티어로 사용



---

## 2. EC2 란

* Elastic Compute Cloud
* 인스턴스 : AWS 클라우드의 가상 서버
* 크기조정 가능한 컴퓨팅 파워(가상인스턴스의 크기가 고정되어 있지 않다
* 컴퓨팅 리소스 완전제어 (인스턴스는 고객이 관리, 제어성을 고객에게 제공)
* 새로운 서버 인스턴스 확보 및 부팅 시간을 단축
* 실제로 사용한 용량만큼 지불
* 컴퓨팅 요구사항의 변화에 따라 컴퓨팅 파워 조정가능
* Linux또는 Windows 선택가능
* 안정성을 위해 여러 AWS 리전과 가용 영역에 걸쳐 배포

---



## 3. Linux 인스턴스 시작

* [설치가이드](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/EC2_GetStarted.html)

* 인스턴스를 시작할 때, 키 페어와 보안그룹을 지정해 인스턴스 보안을 설정

* 인스턴스에 연결할 때는 인스턴스 시작시, 지정한 키 페어의 프라이빗 키 지정

* **키 페어 없이 계속** 옵션을 선택하지 마십시오. 키 페어 없이 인스턴스를 시작하면 인스턴스에 연결할 수 없습니다.

* MyKeyPair 키를 다운로드하면 안전한 장소에 키를 저장해야 합니다. 키를 잃어버리면 인스턴스에 액세스할 수 없습니다. 다른 누군가가 키에 액세스하게 되면, 그 사람들이 사용자의 인스턴스에 액세스할 수 있습니다.

  **Windows 사용자**: 키 페어를 .ssh라는 하위 디렉터리에 있는 사용자 디렉터리에 저장하는 것이 좋습니다(예: C:\user\{yourusername}\.ssh\MyKeyPair.pem).

  팁: Windows Explorer에서는 폴더 이름이 마침표로 끝나지 않는 한 마침표로 시작하는 폴더 이름을 생성할 수 없습니다. 이름(.ssh.)을 입력하면 마지막에 있는 마침표가 자동으로 제거됩니다.

  **Mac/Linux 사용자**: 키 페어를 홈 디렉터리의 .ssh 하위 디렉터리에 저장하는 것이 좋습니다(예: ~/.ssh/MyKeyPair.pem).

  팁: MacOS에서는 기본적으로 키 페어가 다운로드 디렉터리에 다운로드됩니다. 키 페어를 .ssh 하위 디렉터리로 이동하려면 터미널 창에 mv ~/Downloads/MyKeyPair.pem ~/.ssh/MyKeyPair.pem 명령을 입력합니다.

![img](https://t1.daumcdn.net/cfile/tistory/997664425C5930EE1F)



## 4. 인스턴스 연결

* 리눅스는 SSH라고 하는 방식을 통해 원격제어

  윈도우는 SSH가 없기 때문에 ssh역할을 해줄 수 있는 프로그램 설치 필요 (ex. putty)

* 마우스 오른쪽 > 연결 > `독립 실행형 SSH 클라이언트` 로 연결방법 선택

```sql
C:\Users\HPE>ssh -i C:/Users/HPE/.ssh/20200115.pem ec2-user@ec2-13-112-192-203.ap-northeast-1.compute.amazonaws.com
Last login: Wed Jan 15 02:11:52 2020 from ec2-3-112-23-0.ap-northeast-1.compute.amazonaws.com

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|
```

C:/Users/HPE/.ssh/20200115.pem (저장주소)

프리티어의 경우, 사용자ID `ec2-user` 

DNS는 AWS의 인스턴스 정보에서 DNS(IPv4) 복사후 붙여넣기

* Xshell로 연결할때는 사용자ID `ec2-user` / password 아닌 키 가져오기 해서 선택 / 호스트는 `public DNS`



## 5. NoSQL 테이블 생성 및 쿼리

* [DynamoDB](https://ap-northeast-1.console.aws.amazon.com/dynamodb/home?region=ap-northeast-1#gettingStarted:) 접속하기

* [가이드](https://aws.amazon.com/ko/getting-started/tutorials/create-nosql-table/)

* 파티션 키 : 확장성을 위해 파티션에 데이터를 분산, 다양한 값을 가진 속성 선택

* 테이블 이름 : Music

  파티션 키 : Artist > 정렬 키 추가해 songTitle

  기본설정 사용

* 쿼리하기 : 아래의 칸을 `스캔`에서 `쿼리`로 변경 (검색할때 대소문자 주의)

  스캔상태에서 항목 추가 다운 가능

  ![image-20200115112811388](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115112811388.png)



---

## 6. LAMP 웹서버 설치

[가이드](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/install-LAMP.html)

* Linux + Apache + Mysql,MariaDB + PHP,Perl,Python 의 약자

  (웹서버를 구성하기 위한 최소한의 요소)

  고정 웹사이트를 호스팅하거나 데이터베이스에서 정보를 읽고 쓰는 동적 PHP 애플리케이션을 배포

1) 인스턴스 인바운드

* 내 IP주소 찾기 : http://checkip.amazonaws.com/ : 59.29.224.120
* `0.0.0.0/0`을 사용하면 모든 IPv4 주소에서 SSH을 사용하여 인스턴스에 액세스할 수 있습니다. `::/0`을 사용하면 모든 IPv6 주소를 사용해 인스턴스에 액세스

2) 보안그룹 > 편집 > ip 설정

3) 인스턴스 연결

```sql
C:\Users\HPE>ssh -i C:/Users/HPE/.ssh/20200115.pem ec2-user@ec2-13-112-192-203.ap-northeast-1.compute.amazonaws.com
Last login: Wed Jan 15 02:11:52 2020 from ec2-3-112-23-0.ap-northeast-1.compute.amazonaws.com

또는 xshell
```

4) 업데이트 설치

```sql
[ec2-user@ip-172-31-33-197 ~]$ sudo yum update -y]
```

```sql
[ec2-user ~]$ sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
```

```sql
[ec2-user ~]$ sudo yum install -y httpd mariadb-server
```



> 이것 실행 안되면 linux2 ami 확인하기!

```sql
[ec2-user@ip-172-31-33-197 ~]$ cat /etc/system-release
Amazon Linux release 2 (Karoo)
> 이렇게 2로 나와야!
```

> 패키지의 현재 버전확인하기

```sql
yum info package_name
```

5) apache 웹서버 실행

```sql
sudo systemctl start httpd > 웹서버 시작

sudo systemctl enable httpd > 매번 시스템 부팅마다 시작

sudo systemctl is-enabled httpd > httpd 실행되고 있는지 확인
```



6) 인스턴스 확인

보안그룹 선택 > 인바운드 규칙보기 > 편집> HTTP추가

![image-20200115135739211](AWS.assets/image-20200115135739211.png)



7) 웹서버테스트 : 공용DNS주소(또는 공용IP)로 검색

![image-20200115135936638](AWS.assets/image-20200115135936638.png)



8) 파일 권한 설정

* Amazon Linux Apache 문서 루트는 `/var/www/html``
* ``ec2-user`를 `apache`그룹에 추가하여 `/var/www`디렉터리 소유권을 부여하고 쓰기 권한 할당 필요

```sql
[ec2-user@ip-172-31-37-101 ~]$ sudo usermod -a -G apache ec2-user
> 사용자(ec2-user)를 apache 그룹에 추가

[ec2-user@ip-172-31-37-101 ~]$ exit
logout하고 다시 연결

[ec2-user@ip-172-31-37-101 ~]$ groups
ec2-user adm wheel apache systemd-journal
> apache 그룹에 가입된 것 확인

[ec2-user@ip-172-31-37-101 ~]$ sudo chown -R ec2-user:apache /var/www
> /var/www 및 그 콘텐츠의 그룹 소유권을 apache 그룹으로 변경
# chown : 파일의 소유자와 그룹을 변경

[ec2-user@ip-172-31-37-101 ~]$ sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
> 쓰기 권한 추가(/var/www와 그 하위디렉토리)

[ec2-user@ip-172-31-37-101 ~]$ find /var/www -type f -exec sudo chmod 0664 {} \;
> 그룹쓰기 권한 추가(/var/www와 그 하위디렉토리)
```

* 이제 `ec2-user`와 `apache` 그룹의 향후 멤버는 Apache document root에서 파일 추가, 삭제, 편집

  사용자는 정적 웹 사이트 또는 PHP 애플리케이션과 같은 콘텐츠를 추가가능



## 7. LAMP 서버테스트

1) `Apache`문서 루트에서 PHP 파일을 생성

```sql
echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
```

2) [웹에서 확인](http://ec2-3-113-28-116.ap-northeast-1.compute.amazonaws.com/phpinfo.php) : PHP 정보

* PHP와 마리아DB는 데이터베이스 관리

3) `phpinfo.php` 파일 삭제 : 보안상의 이유로 인터넷에 공개되면 안됨

```sql
rm /var/www/html/phpinfo.php
```



## 8. 데이터베이스 서버 보안 설정

* MariaDB의 여러 기능들은 보안문제로 프로덕션 서버에서는 비활성되거나 제거되어야 한다 > 암호 설정하고, 보안성이 낮은 기능 제거

```sql
sudo systemctl start mariadb
# sudo : 다른 사용자의 보안권한, 슈퍼유저로서 프로그램 구동

sudo mysql_secure_installation > 처음에 그냥 엔터 후 Y눌러서 비밀번호 설정
이후 익명 사용자, 원력루트, 테스트 DB 모두 제거 및 비활성화 & 리로드
```



* phpMyAdmin : 데이터베이스를 보고 편집하는데 사용하는 웹 기반 데이터베이스 관리도구

  ```sql
  sudo yum install php-mbstring -y > php 항목 설치
  
  sudo systemctl restart httpd > apache 다시 시작
  
  sudo systemctl restart php-fpm > php-fpm 다시시작
  
  cd /var/www/html > apache 문서루트인 /var/www/html로 이동
  
  wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz 
  > 최신릴리스 소스 패키지를 인스턴스로 파일 직접 다운
  
  mkdir phpMyAdmin && tar -xvzf phpMyAdmin-latest-all-languages.tar.gz -C phpMyAdmin --strip-components 1
  > phpMyAdmin 폴더 생성하고 해당 폴더로 패키지의 압축 풀기
  
  rm phpMyAdmin-latest-all-languages.tar.gz
  > tarball 삭제 (tar: 리눅스 압축파일 형태)
  
  sudo systemctl start mariadb > mysql 서버 시행중이지 않으면 시행
  ```

  * [웹서버에 접속가능](http://ec2-3-113-28-116.ap-northeast-1.compute.amazonaws.com/phpMyAdmin/) : root / abc123

![image-20200115141904708](AWS.assets/image-20200115141904708.png)

## 9. WordPress 블로그 호스팅

*  WordPress 블로그를 호스팅하는 웹 서버를 사용자가 완전히 제어

   WordPress 블로그를 호스팅하는 웹 서버를 사용자가 완전히 제어

* 탄력적 IP 주소(EIP)는 WordPress 블로그를 호스팅하는 데 사용 중인 인스턴스와 연결하는 것이 가장 바람직

  인스턴스의 퍼블릭 DNS 주소가 설치 위치를 바꾸거나 위반하는 것을 방지할 수 있기 때문

* 블로그에 사용할 도메인 이름이 아직 없을 경우에는 먼저 Route 53에 도메인 이름을 등록



### 1. WORDPRESS 설치 패키지 다운 및 압축해제

```SQL
wget https://wordpress.org/latest.tar.gz

tar -xzf latest.tar.gz > workdpress라는 폴더로 압축해제
```



### 2. DB사용자 및 생성

*  WordPress 설치 시 블로그 게시물, 사용자 의견 등의 정보를 데이터베이스에 저장
* 블로그의 데이터베이스와 이 데이터베이스에 대해 정보 읽기 및 저장 권한이 있는 사용자를 생성

```sql
sudo systemctl start mariadb > 데이터베이스 서버(mariadb)시작

mysql -u root -p > root사용자로 로그인
```

```sql
MariaDB [(none)]> CREATE USER 'wordpress-user'@'localhost' IDENTIFIED BY 'your_strong_password';
Query OK, 0 rows affected (0.01 sec)
> 사용자 및 암호 생성 (저기 이름이랑 패스워드 바꿨어야 ^^;)
> 작은따옴표('')는 명령어를 구분하는 용도로 사용하기 때문에 암호에 사용하지 말기

MariaDB [(none)]> CREATE DATABASE `wordpress-db`;
Query OK, 1 row affected (0.00 sec)
# 데이터베이스 생성 (wordpress-db부분에서 이름 변경하기)
# ''(백틱)가 이름을 앞뒤로 묶는 것

MariaDB [(none)]> GRANT ALL PRIVILEGES ON `wordpress-db`.* TO "wordpress-user"@"localhost";
Query OK, 0 rows affected (0.00 sec)
# 데이터베이스에 대한 전체 권한을 wordpress 사용자에게 부여 
#  `wordpress-db`"wordpress-user" 요기를 위에서 설정한 이름에 맞게 바꾸기

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)
# flush : 새로고침 > 변경사항 적용

exit
```

### 3. wp-config.php 파일 생성 및 편집

* WordPress 설치 폴더는 `wp-config-sample.php`라는 샘플 구성 파일

  복사 > 구성에 맞도록 편집

```sql
cp wordpress/wp-config-sample.php wordpress/wp-config.php
> 복사

nano wordpress/wp-config.php 
> nano 대신 vim 등의 텍스트편집기 이용 가능

define('DB_NAME', 'wordpress-db');
define('DB_USER', 'wordpress-user');
define('DB_PASSWORD', 'your_strong_password');
> 각자 설정한 파일 이름, 사용자이름, 비밀번호 지정
```

### 4. Apache 웹 서버에 대한 파일 권한 수정

``` sql
[ec2-user@ip-172-31-37-101 html]$ sudo chown -R apache /var/www
> /var/www 의 파일 소유권 및 콘텐츠를 apache사용자에게 허용
# chown : 파일의 소유자와 그룹 변경(사용자)
# -R : Recursive (재귀)_자기자신 부르는 함수

[ec2-user@ip-172-31-37-101 html]$ sudo chgrp -R apache /var/www
> /var/www 그룹 소유권을 apache그룹에 허용

[ec2-user@ip-172-31-37-101 html]$ sudo chmod 2775 /var/www
[ec2-user@ip-172-31-37-101 html]$ find /var/www -type d -exec sudo chmod 2775 {} \;
> 디렉터리 권한 변경해 쓰기 권한 추가 및 하위 디렉터리에서 그룹ID 설정
# chmod : change mode의 축약어로 사용권한 변경 ()
# 2775 : 
맨 앞의 2가 특수권한 : setGID > 파일 처음 만들었을 때 권한으로 실행
Linux : 특수 / user / group / other

[ec2-user@ip-172-31-37-101 html]$ find /var/www -type f -exec sudo chmod 0664 {} \;
> 그룹쓰기 권한 추가
```



-----

## 설명

1. AWS 계정 

* root 계정
  * 이메일과 credit이용해 가입 > root 계정
  * root 계정은 모두 권한 보유 but 보안상 root계정으로 다이렉트로 로그인해서 사용하는 것을 권장하지 않음

* IAM user
  * IAM이라는 identity 서비스를 제공 > root 대신 IAM user를 생성해 사용하는 것을 권장
  * IAM user을 group으로 생성해 활용가능, 부여한 url로 로그인 가능
  * user로 첫 로그인시, 권한이 아무것도 없음 > policy가 결합된 role을 user에게 특정서비스만 이용하게 부여가능
  * admin과 group들을 생성해 group별로 특정 권한을 부여하고, user에게 접근할 수 있도록
* User
  * web console에서 접근 가능 > ID. PW
  * CLI 접근 가능 > access key와 secret key로 접근 가능
    * access key를 통해 인증하기 위해서는 코드에 넣어서 사용가능 but 보안 위험
    * role에 넣어서 사용하는 것을 추천 > role에 policy를 적용
      * EC2 생성할 때 access할 수 있게 사용가능 (특정 instance, Lambda)
    * Dynamic Token Service(STS) : 일정시간이 지나면 로그아웃하게 되는 매커니즘\
      * dynamic : 시간이 지나면 time out <-> static 방식
* instance에 role을 부여하게 되면 다른 서비스 이용 접근이 deny됨
  * policy 적용된 서비스에만 접근가능한 형태가 되는것 
* 우리는 이메일로 접근하지만, 내부적으로는 유니크한 아이디가 발급되는것
* root를 대신할 수 있는 admin user을 하나 만들고 목적별로 role 부여해 접근 제한해야



2. EC2가 S3나 cloudwatch 등에 접근 가능



3. AWS 서비스
   * 관리형서비스 :  EB(PaaS) / Dynamic DB / RDS
     * 관리를 AWS에서 해줌. 대신 비용비쌈
   * DIY방식(EC2기반)_VPC / IaaS(cloudformation)
     * 고객이 직접 관리해야. 대신 저렴
   * Openstack, Trove, Swift 등이 대부분 관리형 > 관리는 해당 provider에서 관리



4. global 서비스

   * Route53, IAM, STS, CloudFront

   Region 서비스

   * S3, cloudtrain

   AZ 서비스

   * EC2, EBS
   * VPC-큰 주소, 이 주소를 또 쪼개서 사용하는게 서브넷
   * AWS에서는 VPC의 IP 주소가 10.0.0.0/16이 가장 큰 값
     * 10.0이 네트워크 주소, 0.0이 호스트 주소인데, 이를 서브네팅해서 또 쪼개서 이용가능
     * C클래스 형태로 서브넷에 맞게 부여
     * 로컬 라우팅 테이블 생성이 됨 > VPC 안에 서브넷으로 분리되었지만 기본적으로 생성된 로컬 라우팅 테이블로 통신됨. 따라서 별도의 로컬 라우팅테이블 만들 필요 없음
     * private subnet은 사설 IP이기 때문에, 외부에서 통신할 수 없음 > 라이팅 테이블 엔트리에 따라서 외부와 통신 가능(public 망에 routing을 생성)

   CDN : content delivery network

   

   > AZ-2a, AZ-2c 등으로 서비스 이름 나타나는데, AZ-2가 region의 이름이고 ac 등 영어가 가용존으로 2개 이상의 데이터센터 제공(하나의 데이터센터가 망가져도 다른 데이터 센터를 통해 이중화 구조로 설계)
   >
   > > 한국은 3개 제공



5. AWS ELB (AWS의 LB)

* public 망에 인증 도메인 주소를 받는 것 > 인증 DNS가 발급되고, 이를 통해 웹에서 접근 가능

  DIY 통해 올린 인스턴스를 내가 직접 target group을 설정할 수 있고, autoscaling을 통해 연결해 자동으로 관리할 수 

* Loadbalancer
  * NLB : L4 Switch 등 VIP 제공
  * ALB : VIP 제공하지 않음 > 이게 public 망에 올라감



* Auto Scalling group
  * cpu 사용률이 얼마 이상, 이하 되면 자동으로 EC2를 생성하거나 제거
  * 하위 리소스 그룹으로 lauch confidental 사용 (주로 80,8080포트를 설정)









instance 자체에서도 퍼블릭 IP제공하나 이는 껐다 킬때마다 IP 바뀜

\> 인스턴스 꺼져있을때도 지속적인 ip를 제공하기 위해 탄력적 IP 제공

Loadbalancing : 부하 나누는 방법

auto scaling : 자동적으로 서버 범위 조정 (접속 많을 때 늘렸다가 감소가능)

온디맨드 : 사용량에 따라 조절(사용하지 않는 서비스 끄기)

4계층

TCP : 연결성

UDP : 비연결성(중간에 끊기든 상관없이 일방적으로 제공) ex. 스트리밍

7계층

http

https