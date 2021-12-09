---
author_profile: true
date: 2021-08-20
title: "Docker 2 명령어 및 사용"
categories: 
    - Docker
tag: 
    - Docker
    - DB
    - Python

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Docker 명령어 및 사용

---

## 1. Docker 명령어

```
docker run -i -t ubuntu:14.03
```

- `-i` : 상호 입출력 지원
- `-t` : tty 활성화 해 bash 사용

```
exit, ctrl + D
```

- docker 종료 및 컨테이너 정지

```
ctrl + P, Q
```

- docker 쉘에서만 나오고 백그라운드로 컨테이너 실행

```
docker pull centos:7
```

- 해당하는 이미지 내려 받음

```
docker image
```

- 저장된 이미지 확인


```
docker create -i -t --name mycentos centos:7
```

- 컨테이너 생성할 때 run 이 아닌 create로도 사용가능하다. 
- 하지만 run을 실행했을 때 처럼 바로 쉘로 진입하지는 않는다. 
- create는 컨테이너를 생성할 뿐 컨테이너로 들어가지 않기 때문


```
docker start mycentos
socker attach mycentos
```

- 따라서 위와 같이 컨테이너를 시작하고 내부러 들어간다. 

```
docker ps
docker ps -a
```

- 지금까지 생성한 컨테이너의 목록을 확인
- ps 명령어는 정지되지 않은 컨테이너만 출력한다. 
- 정지된 컨테이너까지 볼려며 ㄴ뒤에 -a 옵션 추가한다.

```
docker rm mycentos
```

- 컨테이너 삭제

```
docker stop mycentos
```

- 컨테이너가 실행 중이면 삭제 불가능, 정지 

```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

- 도커에 설정된 모든 컨테이너 삭제 

---

## 2. Docker Network

mycentos 에서 ifconfig 실행
명령어 없을 떄 `yum -y install net-tools` 로 설치 후 재 실행

```
[root@279fcf1c5951 /]# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 1842  bytes 19551980 (18.6 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1211  bytes 68867 (67.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- 컨테이너는 가상머신과 마찬가지로 가상 IP를 할당 받는다. 기본적으로 도커는 컨테이너에 172.17.0.x의 IP를 순차적으로 할당한다. 
- 현재 172.17.0.2를 할당 받은 eth0 인터페이스와 로컬 호스트인 lo 인터페이스가 있다.
- 아무런 설정을 하지 않았다면 이 컨테이너는 외부에서 접근 할 수 없으며 도커가 설치된 호스트에서만 접근할 수 있따. 
- 외부에 컨테이너의 app을 노출하기 위해서는 eth0의 ip와 port를 호스트의 Ip와 port로 바인딩해야 한다.

따라서 아파치 웹 서버를 설치해 외부에 노출 해 본다.

```
exit
docker run -i -t --name mywebserver -p 80:80 ubuntu:14.04
/# apt-get update
/# apt-get install apache2 -y
/# service apache2 start
/# grep ServerName /etc/apache2/apache2.conf
-- 아무것도 없으면
/# echo "ServerName localhost" >> /etc/apache2/apache2.conf
/# grep ServerName /etc/apache2/apache2.conf
ServerName localhost
/# service apache2 restart
```
- virtual box에서 ip 포워딩 80포트 실행시 윈도우 상에서도 접속이 가능해진다.
- 윈도우 -> virtualbox(centos) -> docker container(mywebserver) -> apache2


### Docker Network 확인

```
[user@localhost ~]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
801cf186eb6f   bridge    bridge    local
d0f2c9cdaadd   host      host      local
44444d4744c0   none      null      local
```

---

## 3. Docker Container Application

- 기존의 데이터베이스 웹서버가 동일하게 있던것과 달리 도커는 따로 따로 구분 가능핟.
- 데이터베이스와 워드프레스 웹 서버 컨테이너를 연동해 워드 프레스 기반 블로그 서비스 만들어 본다.

```
docker run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7

docker run -d -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password --name wordpress --link wordpressdb:mysql -p 80
wordpress
```

- 첫번쨰 명령어
  - mysql 이미지를 사용해 DB 컨테이너 생성
  - `-d` : -u, -t 가 컨테이너 내부로 집입하도록 attach가능한 상태로 설정했다면, -d는 detached 모드로 컨테이너를 실행한다. Detached 모드는 백그라운드에서 동작하는 app으로 실행하도록 설정한다. 또한, -d 옵션으로 run을 실행하면 입출력이 없는 상태로 컨테이너를 실행한다. 컨테이너 내부에세ㅓ 프로그램이 터미널을 차지하는 포그라운드로 실행돼 사용자의 입력을 받지 않는다. Detached모드인 컨테이너는 반드시 컨테이너에서 프로그램이 실행 돼야 하며 포그라운드 프로그램이 실행되지 않으면 컨테이너는 종료 된다.
  - `docker exec -i -t wordpressdb /bin/bash` : -d 옵션으로 상호 입출력 가능한 쉘이 없을 경우 해당 명령어를 사용해 내부의 쉘을 사용할 수 있다.
  - `-e` : 컨테이너 내부의 환경번수를 설정하는 것. 컨테이너화 된 애플리케이션은 환병변수에서 값을 가져와 쓰는 경우가 많으므로 자주 사용하는 옵션 중 하나이다. 
- 두번째 명령어
  - 미리준비된 워드프래스 이미지를 이용해 워드프레스 웹 서버 컨테이너 생성
  - -p 옵션으로 80을 입력했으므로 호스트의 포트 중 하나와 컨테이너의 80번 포트가 연결된다.
  - docker ps 명령어로 어느 포트와 연결되었는지 확인(:::49153->80/tcp)
```
[user@localhost ~]$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                     NAMES
880f2e78614d   wordpress   "docker-entrypoint.s…"   14 seconds ago   Up 10 seconds   0.0.0.0:49153->80/tcp, :::49153->80/tcp   wordpress
0c56a59cb57a   mysql:5.7   "docker-entrypoint.s…"   2 minutes ago    Up 2 minutes    3306/tcp, 33060/tcp                       wordpressdb
```

```
[user@localhost ~]$ docker port wordpress
80/tcp -> 0.0.0.0:49153
80/tcp -> :::49153
```

- http://192.168.56.1:49153/ 로 접속하면 wordrpess 설정화면 볼 수 있다.

---

## 4. Docker Container logging

컨테이너 내부에서 어떤일이 일어나는지 아는 것을 디버깅 뿐만 아니라 운영 측면에서도 중요하다.
애플리케이션 레벨에서 로그가 기록되도록 개발해 별도의 로깅 서비스를 쓸 수도 있지만, 도커는 컨테이너의 표준 출력, 에러 로그를 별도의 메타데이터 파일로 저장하며 이를 확인하는 명령어를 제공한다.

```
docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=1234 mysql:5.7
docker logs mysql
docker logs --tail 2 mysql
docker logs --since 1474372879 mysql    # unix 시간으로 특정시간 이후 log
docker logs -f -t mysql     # -f  는 로그 스트림 확인, -t는 타임 스탬프 출력
```

이러한 로그는 json 형태로 도커 내부에 저장되게 된다. 
```
cat /var/lib/docker/containers/${CONTAINER_ID}/${CONTAINER_ID}-josn.log
```

```
docker run -i -t --log-opt amx-size=10k --log-opt max-file=3 --name log-test ubuntu:14.04
```

- 위와 같이 로그의 최대크기 및 갯수를 정할 수 있다.

