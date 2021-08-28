---
author_profile: true
date: 2021-08-29
title: "CCNA 정리 2 - Network Layer"
categories: 
    - CCNA
tag: 
    - Routing 테이블에서 경로 결정하는 순서
    - DNS(Domain Name System)
    - OSPF
    - Access List
    - DHCP
    - NAT
    - MPLS
    - Floating Static route
    - HSRP

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Network Layer
---

## Routing 테이블에서 경로 결정하는 순서

1. Longest Prefix
2. Administrative Distance

```
IN-BGP = 200	EX-EIGRP = 170
RIP = 120	IS-IS = 115
OSPF = 110	IN-EIGRP = 90
Ex-BGP = 20	Static = 1Connected = 0
```

3. Metrix
4. Cost

---

## DNS(Domain Name System)

호스트의 도메인 이름을 네트워크 주소로 바꾸거나 그 반대의 작업을 수행하는 것. 

#### DNS lookup : DNS 정보를 확인하는 명령어

---

## OSPF

- OSPF는 Convergence time(라우팅 테이블 동기화 시간)이 거리벡터 방식보다 빠르다. 
- Area라는 개념을 통해 OSPF를 작은 네트워크 단위로 계층적으로 나누어 관리하기 때문. 
- Area 사용 이유는 
    - Convergence Speed Up 
    - reduce Routing overhead
    - confine network instability to singe Areas of the network
- VLSM(Veriable Length Subnet Mask)를 지원해 IP주소 효율적 사용 가능
- OSPF의 Neighbor가 되기 위해선 Area ID, hello/dead interval time 등이 같아야 한다.
- OSPF 인터페이스 구성에 있어 필수적인 파라메터 OSPF AREA, OSPF Process ID
- OSPF의 Default 값은 Broadcast. 단, HDCL, PPP를 Serial Port에서 사용 시 Default는 Point-to-Point.
- OSPF 라우터 ID 결정 방법해당 라우터의 가장 높은 IP 주소가 ID가 된다. 하지만, 자주 바뀌는 경우 LoopBack를 설정해 이를 라우터 ID로 한다.
- DR & BDR
    1. Priority가 높은 Router가 DR이 된다.(Default 1)
    2. Priority가 같으면 Router ID 높은 Router가 DR이 된다.
    3. DR 선출 후 다음 우선순위 Router가 BDR이 된다.
- OSPF 구성 방법

```
router ospf process-id
network address wildcard-mask area area-id
→ network 150.0.150.0 0.0.0.255 area 0
```

---

## Access List

라우터에서 네트워크의 보안을 위해 사용되는 것

#### Access List 규칙

1. 엑세스 리스트는 윗줄부터 하나씩 차례로 수행된다.

2. 엑세스 리스트는 기본적으로 마지막에 deny all이 설정되어 있어 설정되지 않은 것들을 모두 deny 시켜 버린다. 따라서, 필요 시 permit any 명령어를 마지막 라인에 적어준다.

3. 엑세스 리트스의 새로운 라인은 항상 맨 마지막 줄에 추가되며, 선택적으로 중간의 값을 삭제 또는 수정할 수 없다.
만약 no access-list로 삭제 하면 모두 지워진다.

4. 인터페이스에 대한 엑세스 리스트가 정의되어 있지않으면 permit any 상태가 된다.

#### 명령어

- Access List 생성

```
access-list access-list-number {permit|deny} {source [source-wildcard]|any}

access-list 100 deny tcp host 192.14.5.33 any eq 22
```
eq domain

```
FTP = 20(data), 21(control)
SSH = 22	DNS = 53	Telnet = 23
HTTP = 80	HTTPS = 443	SMTP = 25
```

- Access List 할당 

```
access-list access-list-number {permit|deny} {source [source-wildcard]|any}

access-class 10 in
```

- ACL(Access Control List) configuration 확인 명령어

```
show ip access-list
```

#### VTP 

라우터 인터페이스를 통해 접속하는 가상 터미널 라인
- 대표적으로 Telnet 과 SSH 가 있다.
- Access List 할당 시 VTP 에 할당.

---

## DHCP(Dynamic Host Configuration Protocol)

DHCP 서버가 중앙 집중식으로 관리하는 클라이언트/서버 모델로 Host의 IP 주소를 자동을 할당해준다.

- DHCP 정보 확인 명령어 : `show ip interface`
- DHCP서버에 할당된 Ip 주소 확인 명령어 : `show ip HDCP binding`

#### Ip helper-address

- Router는 각각의 서브넷에서 발생한 Broadcast를 차단하는 역할을 한다.
- Router는 라우팅 테이블에 Broadcast 주소를 할당할 수 없고 이로 인해 Broadcast 메시지를 Drop 시킨다.
- 만약 다른 서브넷으로 Broadcast 메시지를 전송하고 싶을 경우 ip helper-address를 통해 UDP Broadcast를 다른 서브넷으로 연결해준다. 
- 따라서 각 서브넷마다 DHCP 서버가 필요해 효용성 떨어질 경우 ip helper-address를 사용한다.(DHCP Relay)
- DHCP의 IP helper-address는 Client에 가장 가까운 Router에 위치한다.

---

## NAT

one outside ip address for multiple inside hosts

- NAT 특징

1) 공인 IP주소 절약
1개의 공인IP에 여러개의 사설 IP를 할당 할 수 있어서 주소를 절약할 수 있다.

2) 네트워크 보안
NAT를 사용하면 내부에서 사용하는 IP를 외부에서 알 수 없다.

3) 효과적인 주소 할당
사설IP는 내부에서 IP를 설정하므로 관리자가 원하는대로 네트워크를 할당함으로써 라우팅 네트워크 성능이 향상되고 장애처리가 쉬워진다.

4) Port를 사용하는 방법이 있다.
NAT overload (NAT → PAT)

#### Cisco에서의 NAT 구분

- Dynamic NAT (N:N)
- Static NAT(1:N)
- PAT-Port Address Translation(1:N)
- PAT-Port Address Redirection(Port forwarding)

---

## MPLS(Multi Protocol Label Switching)

데이터 전달 기법으로 라우팅 테이블의 복잡한 검색을 회피

---

## Floating Static route

목적지 네트워크 경로를 Static으로 설정하되 만약 경로가 Down 되면 Backup 경로로 라우팅 테이블을 설정하는 Static routing의 이중화 방법이다.

---

## HSRP(Hot Stand by Router Protocol)
- 네트워크 중단 없이 지속적으로 운영하기 위한 기술으로, Host PC가 첫 번째 홉 라우터인 Default gateway의 IP에 접속하지 못할 경우 대비하는 것. 
- Standby group 안에 라우터들이 있고, 그중에서 하나가 Active Router가 된다. 만약 이 Active Router가 Down 될 경우 Active 외의 Standby Group 안의 Standby Router가 대신한다.