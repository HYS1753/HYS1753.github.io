---
author_profile: true
date: 2021-07-00
title: "Network Layer 7 - Routing Protocol(Link-State)"
categories: 
    - Network
tag: 
    - 네트워크 계층
    - Routing Protocol
    - Link State Algorithm

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Link State Algorithm

---

## OSPF(Open Shortest Path First)

---

### 특징

- `Convergence Time(모든 라우터가 변경된 라우팅 정보를 주고 받는데 걸리는 시간)`이 Distance-Vector 방식보다 빠르다. 이는 OSPF가 `Area`라는 개념을 통해 OSPF 네트워크를 작은 영역으로 나눠 관리하기 때문이다.
- `VLSM(Variable Length Subnet Mask)`를 지원해 IP 주소를 보다 효율적으로 사용할 수 있다. 또한, `Route Summarization`을 지원하기 때문에 여러개의 라우팅 경로를 묶어줄 수 있다.
- 네크워크 크기에 대한 제한이 없다. RIP의 경우 최대 15 Hop 까지이며, IGRP는 255 Hop 까지.
- 대역폭의 낭비가 적다. RIP의 경우 매 30초 마다 브로드캐스트를 통해 라우팅 정보를 갱신하지만, OSPF는 네트워크에 변화가 있을 때 멀티캐스트로 정보를 교환하기 때문에 더욱 효율적으로 대역폭을 사용할 수 있다.
- 정확한 경로 선택이 가능하다. RIP의 경우 Hop Count만을 통해 경로를 설정하기 때문에 부정확하지만, OSPF의 경우 다양한 조건을 통해 선택하기 때문에 더욱 정확하다. 
- OSPF는 RIP와 같이 표준 프로토콜이자, Link State Algorithm이다.
- 계층적인 구조로 여러개의 Area로 나뉘고 각 영역은 독립적으로 라우팅을 수행한다.

### OSPF의 Adjacent Neighbor 관계 구성하기

#### Hello Packet

- Hello Packet의 구성
    - Router ID
    - Hello/dead intervals (*)
    - Neighbors
    - Area-ID (*)
    - DR IP address
    - BDR IP address
    - Authentication
    - password (*)
    - Stub area flag (*)
- Hello Packet 구성에서 (*)이 무조건 같아야 OSPF의 Neighbor가 될 수 있다.
- Hello Packet은 매 10초 마다 발생하고 이웃이 되기 위한 정보가 들어있다.
- Router ID의 경우 OSPF에서 라우터를 식별하기 위한 ID이므로 중요하다.
    - 일반적으로 Router ID의 경우 해당 라우터의 가장 높은 IP 주소가 ID가 된다.
    - 해당 IP가 down되거나 수정되면 Router ID가 바뀌게 되어 OSPF 정보를 갱신해야 하는 문제점이 있다.
    - 이를 위해 `Loopback 인터페이스`를 사용한다. Loopback 인터페이스는 안정적인 라우터 ID를 구성하기 위한 인터페이스로 Loopback 인터페이스가 설정되면 다른 IP가 높은것과 상관없이 Loopbask 인터페이스가 라우터 ID가 된다.

```
새로 OSPF 라우팅에 참가하려는 라우터를 A, 기존에 참여하고 있는 라우터를 B 라고 가정
```

1. A는 `멀티케스트 주소 224.0.0.5`를 사용해 Hello Packet을 송신(Down State)
2. Hello Packet을 수신받은 B는 A를 자신의 Neighbor List(이웃 목록)에 저장한다.
3. 이후 B는 다시 A에게 자신에 대한 정보를 `유니캐스트`를 통해 패킷을 전송한다.
4. A는 수신받은 Hello Packet을 통해 자신의 Neighbor List에 추가한다.

#### DR(Designated Router) & BDR(Backup Designated Router)

OSPF에서 모든 라우터 간에 Link State 교환으로 발생하는 트래픽을 감소 시키고 Link State의 Sync(일치성)을 위해 DR, BDR을 사용한다.

OSPF에 참여하는 각 라우터는 자신의 Link State 정보를 DR과 BDR에게 알린다.

`DR` 은 이러한 Link State정보를 모두 관리하면서 Link의 상태를 항상 일치 시키는 역할을 한다.

`BDR` 은 DR이 정확히 Link 상태를 관리하는지 관찰하며, 만약 DR이 Down됬을 경우 DR의 역할을 맡게 된다.

- DR, BDR 선출
    1. Priority 가 높은 라우터가 DR이된다.
        - Priority는 Default로 1이다.
    2. Priority 가 같다면 높은 라우터 ID가 DR이 된다.
    3. DR이 선출된 후 다음 우선순위가 높은 라우터가 BDR이 된다.
    4. 만약 DR, BDR이 선출된 후 Priority가 더 높은 라우터가 나오더라도 DR, BDR은 안바뀐다.
        - DR이 down되어 BDR이 DR이 된 경우 해당 라우터가 BDR이 됨.
    5. DR, BDR의 역할을 안하는 라우터로 만들기 위해선 Priority를 0으로 수정한다.

### OSPF의 Routing Table 구성 방법
```
- LSA(Link State Advertisement) Packet
    - 라우터의 인터페이스 상태 및 인접관계를 형성하는 상태에 대한 정보가 들어 있는 패킷
    - 이러한 정보를 통해 목적지까지의 최단거리를 구할 수 있다.

- LSDB(Link State Database)
    - LSA를 통해 구성된 각 OSPF Area 내 전체 망 정보, 링크 상태 및 경로 정보가 들어있다.
```
- DR, BDR의 경우
1. OSPF가 설정된 라우터는 Hello Packet(244.0.0.5 멀티캐스트주소)을 통해 Neighbor Router를 찾는다.
2. 이 Hello Packet을 통해 DR과 BDR을 알 수 있게 된다.
3. 자신이 가지고 있느 LSA 정보를 LSU 패킷(224.0.0.6 멀티캐스트주소)을 통해 DR로 전송한다.
4. 전송된 LSU는 DR과 BDR에게 전송되고 BDR은 자신의 타이머를 세팅해 DR이 수신한 LSA를 다른 OSPF의 라우터에게 전송하는지 확인한다.
5. 만약 DR이 BDR의 타이머 안에 LSA를 전송하지 않으면 BDR이 DR이 되고 새로운 BDR을 선출한다.
6. DR은 수신받은 LSA를 다른 OSPF 라우터들에게 멀티캐스트 주소(224.0.0.5)를 통해 재전송하고, 보낸 라우터들로 부터 LSAck를 수신받아 잘 도착했는지 확인한다.
7. 만약 링크가 끊어지면 해당 라우터는 DR에게 즉시 알리고 DR은 다시 OSPF에 참여하는 라우터들에게 재전송해 준다. 수신한 라우터는 Flooding을 통해 빠르게 정보를 업데이트 한다.

- DR, BDR이 아닌 라우터

1. DR을 통해 수신한 LSA를 통해 라우팅 정보를 수집하고 LSDB를 구축한다.
2. LSA 교환이 끝나면 이를 근거로 SPF(Shortest Path First)알고리즘을 통해 최적의 경로를 계산하고 이를 라우팅 테이블에 저장한다.
3. 이후 주기적으로 Hello Packet을 전송해 정상 상태임을 알림
4. 만약 네트워크의 상태가 변하면 위의 과정을 통해 다시 라우팅 테이블을 만든다.

### OSPF Message 종류

**1. Hello Packet**
    - 인접 라우터 및 로컬 링크 상태 검색, 관계를 설정하고 주요 매개변수 전달한다.
    - 일정 간격으로 인접 라우터 들의 상태(Keepalive) 확인
**2. DBD/DDP(Database Description Packet)**
    - OSPF 정보 구축을 위해 자신의 LSDB에 있는 LSA를 요약한 정보를 알려주는 패킷.
**3. LSR(Link State Request Packet)**
    - 상대 라우터로 부터 DBD를 수신 받고 자신에게 없는 LSA 정보가 있으면 이를 정보를 요청할 떄 사용되는 패킷.
**4. LSU(Link State Update Packet)**
    - 네트워크 변화 발생하거나 LSR을 수신받았을 때 인접 라우터에게 해당 라우팅 정보를 전송할 때 사용하는 패킷
    - 즉, LSA를 실어 나르는 패킷
**5. LSAck(Link State Acknowledgement Packet)**
    - DDP, LSR, LSU 등의 메시지 수신 확인, 신뢰성 확보 

## OSPF 구성

```
router ospf process-id      // ospf enable
network address wildcard-mask area area-id      // ospf에 운용할 네트워크 정의
```

- `router ospf process-id`
    - process-id : 한 라우터에서 여러개의 ospf를 돌릴 때 구별하는 id, 같은 ospf끼리 꼭 같을 필요는 없으나 일치 시키는게 좋음
    - Ex. router ospf 100
- `network address wildcard-mask area area-id `
    - address : network ip 주소
    - wildcard-mask : 서브넷 마스크와 같은것이지만 반대로 
        - 서브넷마스크 255.255.0.0 → 와일드카드 마스크 0.0.255.255
    - area : 확장성 있는 라우팅을 위해 ospf의 영역을 나누는 것.
    - area-id : 같은 area에 들어갈 osfp 참여 라우터는 같은 id를 가짐
    - Ex. network 150.100.150.0 0.0.0.255 area 0

```
show ip protocols       // 현재 라우터에서 사용하고 있는 라우팅 프로토콜 확인
show ip route           // 현재 라우터의 라우팅 테이블 확인
show ospf interface     // OSPF의 인터페이스 확인
show ip ospf neighbor   // OSPF로 구성한 라우팅 프로토콜의 주변 neighbor 확인 
```

## OSPF의 링크 종류
1. Point to Point : 라우터와 라우터가 1:1로 연결. 이 경우 DR, BDR 안정함
2. Trasient : 여러개의 라우터가 동일한 Area 내에서 Bus를 통해서 연결
3. Stub : 하나의 Area에 1개의 라우터만 연결
4. Virtual : 물리적으로 백본영역과 연결이 어려운 상태에서 가상으로 연결
