---
author_profile: true
date: 2021-07-00
title: "Network Layer 9 - 기타 프로토콜"
categories: 
    - Network
tag: 
    - 네트워크 계층
    - ICMP
    - DHCP
    - HSRP
    - NAT

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Network Layer의 기타 프로토콜

---

## ICMP(Internet Control Message Protocol - 인터넷 제어 메시지 프로토콜)

---

### 특징

- IP 통신은 목적지에 패킷을 정상적으로 전달하는 방법은 있지만 에러 발생시 처리 불가
-IP 패킷은 오로지 패킷을 목적지에 도달시키기 위한 내용들로만 구성되어 있기 때문에 에러 처리에 대한 명시가 필요한데 이 에러 처리 메시지가 ICMP이다.
- ICMP는 IP 통신의 에러 상황을 출발지에 전달 & 메시지 제어 역할
- ICMP는 IPv4 패킷으로 캡슐화, Protocol ID = 1, Ping & Traceroute 명령어 사용

### ICMP 의 기능

#### ICMP Type

- 0 ~ 254까지 각각의 타입이 정의되어 있다.
- 주로 쓰이는 타입은 정보성(0, 8, 9, 10) 과 오류보고용(3, 5, 11, 12)이다.

|Type|Name|
|-|-|
|0|Echo Reply|
|3|Destination Unreachable|
|5|Redirect|
|8|Echo|
|9|Router Advertisement|
|10|Router Solicitation|
|11|Time Exceeded|
|12|Parameter Problem|

- Type 8 & 0 Echo Request & Reply(가장 많이 사용)
	- 네트워크 문제 진단시 사용, 출발지에서 목적지 IP로 ICMP Echo Request 메시지를 보내면 목적지는 Echo Reply로 응답
	- 목적지 도달 여부, RTT(Rround-Trip delay Time), hop count 확인

- Type 9 & 10 Router Advertisement & Router Solicitatiln
    - 자신이 라우터 임을 응답하고 네트워크 진입 시 라우터 정보 요청

- Type 3 Destination Unreachable & 5 Redirect
    - Type 3(Destination Unreachable) : 라우터가 IP 패킷을 라우팅 하지 못하는 경우에 발생
		- 0 = net unreachable		
        - 1 = host unreachable		
        - 2 = protocol unreachable
		- 3 = port unreachable		
        - 4 = fragmentation needed and DF set
		- 5 = source route failed
	- Type 5(Redirect) : 로컬 네트워크에 2개 이상의 경로가 있을 때 더 좋은 경로를 알려줌

- Type 11 Time Exceeded & 12 Parameter Problem
    - Type 11(Time Exceeded) TTL 값이 “0”이 되면 출발지에게 응답
		- 0 = Time ti Live Exceeded		
        - 1 = Fragment Reassembly Time Exceeded
		- IP Fragmentation : IP 패킷을 작은 패킷으로 나누어서 전송하고 목적지에서 재조합
		- MTU(Maximum Transmission Unit): IP 패킷을 전송할 수 있는 최대 크기
	- Type 12(Parameter Problem) : IP 옵션을 잘못 사용하여 라우터에 패킷 패기

## IGMP(Internet Group Management Protocol)

---

### 특징

- 특정 그룹의 모든 호스트에게 메시지가 전송되는 멀티케스팅 작업을 지원하기 위한 프로토콜
- 그룹관리
    - 그룹의 생성 및 제거, 그룹에 참가하는 호스트 참가 탈퇴 관리
- 즉, IGMP는 멀티 캐스트 그룹에 가입 탈퇴시 에 사용하는 프로토콜으로 호스트와 라우터 사이에 맴버 정보를 교환하는 프로토콜 메시지이다.
    - Message 종류
        - 질의 메시지 : 멀티캐스트 라우터가 그룹 정보를 얻기 위해 호스트에 전달(라우터가 전송)
        - 보고 메시지 : 질의의 응답으로 호스트가 보고 메시지 회신

### IGML 동작과정
- 그룹 가입: 해당 멀티 캐스트 주소를 표기한 IGMP보고 메시지를 전송
- 그룹 유지: IGMP보고 메시지를 사용해 IGMP질의에 응답해야 함
- 그룹 탈퇴: 라우터의 질의 메시지에 대해 호스트의 보고메시지 응답 없음
- 라우터는 주기적으로 질의 메시지를 보내 해당 호스트의 상태를 확인한다.
- 만약 주기적인 질의 메시지의 응답이 Time out 시간동안 들어오지 않으면 그룹에서 탈퇴 시킴


## DHCP(Dynamic Host Control Protocol - 동적 호스트 구성 프로토콜)

---

### 특징
- DHCP서버를 사용하여 클라이언트인 네트워크 장치에 IP 주소를 자동으로 할당
- 요청에 의한(On demand) IP 할당으로 효율성 극대화
- 잘못된 IP 설정으로 인한 장애 예방
- IP 변경이 잦은 호스트의 관리

### DHCP Message 포멧

- OpCode : 1 Request(Client->Server), 2 Reply(Server->Client)
- Hardware Type : 1, Ethernet
- Hardware address length : 6, MAC address
- Hop count : 0에서 시작, 네트워크 망 이동 시 증가
- Seconds : IP 할당 후 경과한 초의수
- Flags : 서버 응답에 대해서 0 unicast 또는 broadcast 응답 구분 값
- Transaction ID : 클라이언트가 선택하는 랜덤 수, 요청과 응답 매칭
- Client IP : 최초 0.0.0.0
- YourIP : 할당될 IP
- Server IP
- Gateway IP
- Client Hardware address
- Server Host name
- Boot file name
- Options : DHCP 메시지 타입 포함
    |value|Message Type|Descrition|
    |-|-|-|
    |1|DHCPDISCOVER|클라이언트가 서버를 찾기 위해 브로트캐스팅|
    |2|DHCPOFFER|서버가 클라이언트에게 할당 IP 주소 제시|
    |3|DHCPREQUEST|클라이언트가 원하는 구성 요청|
    |4|DHCPDECLINE|이미 사용중인 IP의 경우 거부|
    |5|DHCPACK|요청 수락|
    |6|DHCPNAK|요청 거부|
    |7|DHCPRELEASE|클라이언트가 IP 해제 요청|
    |8|DHCPINFROM|클라이언트가 추가 설정 정보 요청|

### DHCP 설정 과정

- IP 할당
    1. DHCPDISCOVER : DHCP 서버 찾기
    2. DHCPOFFER : DHCP 서버는 요청한 PC에게 IP를 제안한다.
    3. DHCPREQUEST : PC는 제안 받은 IP 할당을 요청한다.
    4. DHCPACK : DHCP 서버 요청 수락

- IP 갱신
    - 지정된 IP 갱신 시간이 되면 갱신을 요청한다.
    1. DHCPREQUEST : DHCP 서버로 IP 재할당 요청
    2. DHCPACK : DHCP서버는 IP 확인후 수락

- IP 헤제
    - 사용중인 PC가 Down 되는 경우
    1. DHCPRELEASE : PC에 사용중이던 IP반환 요청

## HSRP

---

### 특징

- HSRP는 시스코 장비에서만 사용되는 전용 프로토콜이다.
- HSRP는 라우터가 고장나는 것에 대비하여 라우터 한대를 더 구성한 후 메인 라우터가 고장 날 경우 자동으로 설정한 라우터가 메인 라우터의 역할을 대신하는 역할이다.
- 메인 라우터가 다운되어 다른 라우터가 대신 일을 하더라도 메인 라우터가 Default Gateway이고 이것이 다운될 경우 네트워크 안에 있는 호스트가 모르는 Ip 주소를 못찾아 간다는 문제점이 있다.
- 이러한 문제를 해결하기 위한 프로토콜이 HSRP로 실제로 존재하지 않는 가상의 라우터 IP 주소를 Default Gateway로 설정하고, 그 주소에 대해 Active 라우터와 Standby 라우터의 역할을 두어 해결한다.

## ARP(Address Resolution Protocol)

---

### 특징
IP 주소와 MAC 주소 사이의 변환을 담당한다.
- 라우터의 주소 얻는 방법
    - 송신 호스트의 IP주소 : 송신 호스트의 하드 디스크에서 얻을 수 있음(환경 저장되어 있음)
	- 수신 호스트의 IP주소 : 사용자가 제공(목적지: DNS서버를 사용해 도메인으로 상대방IP적음)
	- 송신 호스트의 MAC주소: 송신 호스트의  LAN카드에서 얻음
	- 수신 호스트의 MAC주소: IP 주소를 매개 변수로 하여 ARP프로토콜로 얻음

### ARP(IP → MAC)

- 특정 호스트의 IP주소로부터 MAC주소를 제공하는 프로토콜, ARP request라는 특수 패킷을 브로드캐스팅
- IP주소에 해당하는 호스트만 ARP reply로 MAC주소 회신한다.
- 효율 향상을 위해 캐시 기능을 제공
- 송신 호스트가 ARP request와 reply를 처리하는 과정에서 패킷을 수신한 모든 호스트는 송신 호스트의 IP주소와 MAC주소 매핑값 획득 가능(네트워크 부하 최소화) 

### RARP(MAC → IP)

- IP 주소를 모를떄 특정 호스트의 MAC주소로 IP주소를 찾는 프로토콜
- 하드 디스크가 없는 시스템은 자신의 IP주소를 알 수 없음, 보통 네트워크에서 RARP기능 전담 서버 하나이상 존재


## NAT(Network Addrss Translation)

---

### 특징

- 한쪽 네트워크의 IP 주소가 다른 네트워크로 넘어갈 떄 변환이 되어 넘어가는 것
- 내부 네트워크에서 사용하는 비공인 주소를 `inside Local 주소`라고 한다. 
- 외부로 나갈 떄 변환되어 나가는 주소를 `inside Global 주소`라고 한다.
- 즉 NAT은 `inside Local 주소`를 `inside Global 주소`로 바꾸는 과정
- NAT을 사용하는 이유
    1. 내부의 네트워크에는 비공인 IP 주소를 사용하고 외부 인터넷으로 나가는 경우에만 공인 IP 주소를 사용하고자 하는 경우
    2. 기존에 사용하던 ISP에서 새로운 ISP로 바꾸면서 내부 전체의 IP를 바꾸지 않고 기존의 IP 주소 그래도 사용하고자 하는 이유
    3. 2개의 인트라넷을 서로 합하려다 보니 두 네트워크의 IP가 겹치는 경우
    4. TCP 로드 분배가 필요한 경우

