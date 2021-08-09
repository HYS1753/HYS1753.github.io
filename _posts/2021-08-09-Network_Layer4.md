---
author_profile: true
date: 2021-08-09
title: "Network Layer 4 - Router 구성(명령어)"
categories: 
    - Network
tag: 
    - 네트워크 계층
    - Router

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Router의 구성

---

## Router에 명령어를 입력하는 방법
- Console Port(Cable)과 터미널 에뮬레이터를 사용한 명령 전송
    - Putty와 같은 에뮬레이터를 사용한다.
    - 시리얼포트(Serial = COM)를 컴퓨터에 직접 연결하므로 컴퓨터가 필요하다는 단점이 있다.
    - 따라서 라우터 처음 구성 또는 고장이 있을때 주로 이 방법을 사용
- AUX(Auxiliary) 포트를 사용한 모뎀 연결
    - 라우터의 AUX 포트에 모뎀을 연결시켜 모뎀을 통해 명령어 전송
    - 이 방식의 경우 텔넷 사용 불가
- TELNET 접속(가상 터미널)
    - 대부분 라우터를 관리할 떄 가장 많이 사용하는 방법
    - 라우터의 IP 주소만 알고 있고 네트워크에 연결되어 있으며 어디서든 라우터를 구성할 수 있다.
- Network Managemetn System(NMS: 네트워크 관리 시스템)
    - 그래픽 방식으로 라우터의 구성을 확인해 가장 쉬운 방법
- TFTP(Trivial File Transfer Protocol) 방식
    - 앞서 4가지 방식은 라우터에 직접 명령을 내리는 방식이라면 TFTP는 다르다.
    - 이미 다른곳에서 만들어 놓은 라우터 구성 파일을 TFTP 서버에 저장해 두었다가 라우터로 다운로드 받는 방식

## Router의 사용자 모드 종류

- **User Mode** 
    - ">" 표시가 있는 모드를 유저 모드라 한다. (ex. Router >)
    - 현재 장비의 상태 및 테스트를 수행하는 모드이다.
    - Ping, Trace 와 같은 작업을 수행할 수 있다. 
    - 하지만, 라우터의 구성파일을 보거나 수정은 할 수 없다.(네트워크 관리자가 아닌 일반 유저가 구성을 변경하면 안되기 때문)
- **Privileged Mode**
    - "#" 표시가 있는 모드를 프리빌리지 모드라 한다. (ex. Router #)
    - User Mode에서 사용이 불가 했던 장비의 구성을 보거나 수정할 수 있다. 
    - 즉, 사용할 수 있는 명령어의 제약이 없는 모드다.
    - Privileged Mode로 진입하기 위해선 User Mode에서 `enable` 을 통해 진입한다. 반대로 다시 User Mode로 변경하기 위해선 `disable`을 통해 변경
- **Config Mode**
    - "(config) #" 표시가 있는 모드를 컨피그 모드라 한다. (ex. Router (config) #)
    - 장비의 구성 파일을 변경하는 경우에 사용하는 모드
    - User Mode에서는 진입할 수 없으며 Privileged Mode에서만 진입 가능
    - `config terminal` 명령어로 진입하며 나올 때는 `exit` 또는 `ctrl + Z`로 나온다.
- **Setup Mode**
    - 장비를 처음 구매해서 파워를 킨 경우
    - 징비에 구성파일이 없는경우 라우터가 부팅하면서 자동으로 들어가는 모드
- **RXBOOT**
    - 장비의 패스워드를 모르는 경우나 장비의 이미지 파일(IOS)에 문제가 생긴경우 복구를 위한 모드 
    - ROMMON 모드라고도 부른다.
- 정리

|User Mode||Privileged Mode||Config Mode|
|-|-|-|-|-|
|Router>|→|Router#|→|Router(config) #|
||enable||config terminal||
|Router>|←|Router#|←|Router(config) #|
||disable||exit(ctrl+Z)||

## Router의 HW 구성

- RAM 
    - 운영체제(Cisco의 경우 IOS)
    - 라우팅 테이블
    - 라우터 구성파일(라우터 주소, 라우팅 프로토콜, 보안 등)
- NVRAM(Non Volatile RAM)
    - 비 휘발성 메모리로 전원이 꺼저도 살아있는 데이터
    - 라우터의 구성 파일이 저장된다. 라우터 시작 시 RAM으로 올림
    - NVRAM 구성 파일 확인 명령어 `show config`
    - RAM 구성 파일 확인 명령어 `show running-config`
- Flash Memory
    - IOS 와 같은 운영체제가 저장되는 것.
    - 전원이 없어도 사라지지 않음, NVRAM 보다 용량 큼
- ROM
    - PC의 CMOS와 같은것 저장
    - POST(Power On Self test)
- Interface
- Console Port
- Auxliliary Port

# Router 명렁어

---

## Router 정보 확인

- Prrvileged Mode에서 진행

`show version`
    라우터의 기본적인 내용, 사용중인 SW 정보, HW 정보, 인터페이스 정보 등 확인

`show interface (interface No)`
    현재 라우터가 가지고 있는 모든 인터페이스 표시, 또한, 그 인터페이스의 상황을 확인하는 명령어
    인터페이스 명을 뒤에 지정해서 해당 인터페이스만 볼 수 도 있음
    
`show rum`, `show start`
    라우터의 구성파일(configuration)을 확인하는 명령어
    show run 명령어는 RAM 에서 실질적으로 동작하고 있는 구성파일(핸재 구성파일)
    show start 명령어는 NVRAM에 저장된 구성파일(백업구성파일)

`show flash`
    IOS 의 이미지 정보 확인

`show processes cpu`
    라우터의 동작 상태 확인, CPU 로드율 확인

## Router 명령 입력

- setup을 통해 Router에 명령을 입력할 수 있지만 한정적이기 때문에 사용 잘 안함
- 대부분 Configure 모드에서 명령을 수행한다.
- configure Mode는 Privileged Mode에서 진입할 수 있으며 진입 후 `Route(config)#`으로 바뀐다.

### configure 명령
    - configure memory
        - NVRAM 에 있는 백업 구성파일을 현재의 RAM에 덮어씌워 사용
    - configure network
    - configure overwrite-network
    - configure terminal
        - 사람이 직접 터미널 앞에서 명령하거나 입력하는 모드로 라우터 구성 변경 시 사용
        - 구성에서 Privileged Mode로 나오게 된 후 구성 값을 NVRAM으로 저장하는 명령어는 `write memory`이다.

### setup Mode 에서 구성하는 내용을 configure Mode에서 구성해 보기
```
Router > enable                 // User Mode에서 Privileged Mode로 전환
password:********               // 설정한 Secret, password 입력
Router # conf t                 // Privileged Mode에서 Configure Mode로 전환

// 라우터 이름 및 PW 설정
Router(config) # hostname RouterTest        // Router의 Host name 변경
RouterTest(config) # enable secret secretpassword   // Privileged Mode로 전환할 때 사용하는 secret password 설정
RouterTest(config) # enable password password   // Privileged Mode로 전환할 때 사용하는 password 설정

// 라우터 Telnet 설정
RouterTest(config) # line vty 0 4   // vty는 Virtual Line, 0 4 는 0~4번 총 5개의 텔넷이 동시에 사용가능하다는 뜻, Line Configure Mode로 전환
RouterTest(config-line) # login     // 텔넷 로그인시 PW 요청하게끔 전환
RouterTest(config-line) # password telnetpassword   // 텔넷 PW 설정
RouterTest(config-line) # exit      // Line configure Mode에서 나가기

// 라우터 인터페이스 별 IP 할당
RouterTest(config) # ipx routing    // IPX 라우팅 사용 명령
RouterTest(config) # interface ethernet 0   // ethernet 0 interface 구성 모드로 전환 (int e 0 으로 줄일 수 있음)
RouterTest(config-if) # no shutdown        // 라우터는 기본적으로 대부분 인터페이스이 shutdown 되어 있음 이를 사용하기 위해선 no shutdown 명령어로 interface를 살린다.
RouterTest(config-if) # ip addr 192.168.0.1 255.255.255.0  // 해당 interface의 IP 주소와 subnet mask 설정
RouterTest(config-if) # ipx network 1       // IPX 네트워크 번호 설정
RouterTest(config-if) # exit                // 해당 interface 구성 종료
RouterTest(config) # int s 0                // Serial Port 0 Configure Mode 진입
RouterTest(config-if) # ip addr 152.120.100.5 255.255.0.0   // 해당 포트의 IP 주소와 Subnet Mask 설정
RouterTest(config-if) # no shut             // 해당 포트 활성화
RouterTest(config-if) # exit

// 라우터의 Routing Protocol 설정
RouterTest(config) # router igrp 200        // 라우팅 프로토콜 설정모드 igrp 프로토콜을 사용하고 AS 번호 200 설정
RouterTest(config-router) # network 152.100.0.0
RouterTest(config-router) # no network 152.100.0.0  // 위의 명령 제거
RouterTest(config-router) # exit
RouterTest(config) # ctrl + Z

// 라우터 구성 확인 및 저장
RouterTest # sh run                 // 현재까지 구성한 라우터 구성 확인
RouterTest # write memory           // 생성한 구성을 NVRAM으로 저장
```

## Router의 암호
- Consol Port
    - 콘솔 포트로 접속할 때 묻는 암호
    ```
    Router(config) # line con 0
    Router(config-line) # login
    Router(config-line) # password password
    ```
- Aux Port
    - Auc 포트로 접속할 떄 묻는 암호
    ```
    Router(config) # line aux 0
    Router(config-line) # login
    Router(config-line) # password password
    ```
- Virtual terminal Password
    - 텔넷을 사용할때 입력하는 PW
    - 텔넷에서 해당 PW를 사용해 접송한 경우 User Mode로 접속
    ```
    Router(config) # line vty 0 4
    Router(config-line) # login
    Router(config-line) # password password
    ```
- User Mode to Privilege Mode
    - enable secret
        - User Mode에서 Privilege Mode로 접속할 때 묻는 PW
        - 라우터 구성파일에서 해당 암호는 암호화 되어 보여짐
        ```
        Router(config) # enable secret password
        ```
    - enable password
        - User Mode에서 Privilege Mode로 접속할 때 묻는 PW
        - 라우터 구성파일에서 해당 암호는 바로 보이게 된다.
        - 만약 secret과 paswword 모두 설정된 경우 secret이 우선적으로 적용된다.
        - secret과 password는 같은 비밀번호 불가능
        ```
        Rotuer(config) # enable pass password
        ```
추가적으로 각 포트에 pw를 설정할 경우 일정 시간 이후에 아무 입력이 없으면 자동으로 타임아웃 되는 명령어가 있음 `exec-timeout`
```
Router(config) # line con 0
Router(config-line) # exec-timeout 10 30    // 10분 30초 뒤 timeout
Router(config-line) # exec-timeout 0 0      // 설정한 timeout 삭제
Router(config-line) # no exec-timeout       // 설정한 timeout 삭제
```