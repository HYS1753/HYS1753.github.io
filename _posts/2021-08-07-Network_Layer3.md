---
author_profile: true
date: 2021-08-07
title: "Network Layer 3 - Router"
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

# Router(라우터)

---

## 라우터 란?

### 라우터의 기능

- 데이터 패킷이 목적지까지 가는 가장 적합한 길을 찾는 `경로설정`기능
- 경로가 결정되면 해당 경로로 데이터 패킷을 전송해주는 `스위칭`기능
- 이러한 기능을 위해 라우팅 알고리즘을 통해 경로를 계산하고 라우팅 테이블을 관리한다.


- 시스코의 라우터에는 IOS(Internetwork Operating System)이라는 SW가 있으며, 보안기능, 라우팅 프로토콜 등이 들어가 있다.

## 라우팅 프로토콜

### 라우티드 프로토콜, 라우팅 프로토콜
- 라우티드 프로토콜 : TCP, IPX, AppleTalk 등 라우팅을 당하는 프로토콜으로 라우터가 라우팅 해주는 프로토콜이다.
- 라우팅 프로토콜 : 라우티드 프로토콜들에게 목적지까지 가장 좋은 경로를 찾는 프로토콜
    - RIP(Routing Information Protocol)
    - IGRP(Interior Gateway Routing Protocol)
    - OSPF(Open Shortest Path First)
    - EIGRP(Enhanced Interior Gateway Routing Protocol)
    - 등등

### Static, Dynamic Routing Protocol
- Static Routing Protocol 
    - 라우터에 사람이 일일히 경로를 입력해 주는 것
    - 장점 : 라우터의 부담이 적어 라우팅 속도 빠르고, 메모리를 절약할 수 있다. 또한, 라우터 간에 라우팅 테이블을 서로 교환할 필요가 없어 네트워크 대역퐁을 아낄 수 있다.
    - 단점 : 사람이 일일히 경로를 입력해 주어야 한다는 단점과 입력한 경로에 문제가 생길경우 고치기 전까지 그 경로로만 데이터를 보내 문제 발생
Dynamic Routing Protocol
    - 사람이 일일히 라우팅 경로를 입력할 필요 없이 라우터가 알아서 경로를 계산
    - 장점 : 자동으로 라우팅 경로를 찾아 사람이 입력할 필요 없으며 어떠한 경로에 문제가 발생하더라도 자동으로 경로를 갱신한다.
    - 단점 : 라우터에 부담을 준다. 

### Routing Table

라우터의 경로 설정을 위한 정보로 지도와 같은 역할을 한다. 

라우팅 테이블은 라우터의 RAM에 올라가기 떄문에 꺼지면 사라진다.

```
Router # show ip route
```
위의 명령어를 통해 해당 라우터의 라우팅 테이블을 확인할 수 있으며, 맨앞에 있는 영문자의 의미는 어떠한 라우팅 프로토콜을 사용했는지를 표시한다.

`I = IGRP, R = RIP, O = OSPF, C = Connected(직접연결)`

### IGP(Interior Gateway Protocol), EGP(Exterior Gateway Protocol)

- IGP(내부용 라우팅 프로토콜)과 EGP(외부용 라우팅 프로토콜)은 AS(Autonomous Systme)의 단위로 구분한다.

- AS(Autonomous Systme)이란 하나의 네트워크 관리자에 의해 관리되는 라우터들의 집단이라는 의미로 ISP(Internet Service Provider)업체들이 대표적인 AS이다.

- 라우터를 AS 라는 그룹으로 묶는 이유는 라우터의 정보를 효율적으로 관리하기 위함. 자신의 라우터들의 정보만 가지고 있고 다른 라우터는 ASBR(Autonomous System Boundary Router)로 전송만 하면 되기 때문

- **IGP(Interior Gateway Protocol)** 
    - AS 내부에서 사용되는 라우팅 프로토콜
    - RIP, IGRP, EIGRP, OSPF 등이 있다.
- **EGP(Exterior Gateway Protocol)**
    - AS간에 즉 AS 외부에서 사용되는 라우팅 프로토콜
    - EGP, BGP 등이 있다.

### CDP(Cisco Discovery Protocol)
- 시스코 라우터와 스위치에서 직접 연결된 시스코 장비를 찾는 프로토콜
- 시스코 장비 전용 프로토콜으로 타사의 장비와 호환 안됨
