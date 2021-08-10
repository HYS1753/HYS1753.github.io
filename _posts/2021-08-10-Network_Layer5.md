---
author_profile: true
date: 2021-08-10
title: "Network Layer 5 - Routing Protocol"
categories: 
    - Network
tag: 
    - 네트워크 계층
    - Routing Protocol
    - Static Routing Protocol
    - Dynamic Routing Protocol

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Static Routing Protocol

---

네트워크 관리자가 직접 라우터에 경로를 입력하는 방법

Static Routing 구성에는 Configure Mode에서 진행

## Static Routing 명령

```
Router(config) # ip route network [mask] {address | interface} [distance]
```
- 위와 같은 명령어를 통해 Static Routing 을 설정한다.
- 예를 들어 목적지 네트워크 : 150.150.0.0, 서브넷 마스크 : 255.255.0.0 다음 홉 라우터 IP : 203.210.100.1 이라 한다면 명령어는 다음과 같다
```
Router(config) # ip route network 150.150.0.0 255.255.0.0 203.210.100.1
```

## Default Routing 명령

경로를 찾아내지 못한 모든 네트워크는 모두 설정한 Default Route 로 가게끔 하는 명령

- Defualt Route 만드는 방법
    1. Default Network 사용
        ```
        ip default-network 203.240.10.0
        ```
        - 위와 같이 default Network를 사용하여 설정할 수 있다. 하지만 제약이 많음
        - ip default-network 뒤에 오는 ip 주소는 항상 Class를 맞춰야 한다.
            - 즉, 150.100.10.0 은 Class B 이기 때문에 /16 이기 때문에 150.100.0.0이라고 주어야 함.
            - RIP 에서 Default Route 구성할 경우 RIP 프로토콜에 정의된 네트워크만 가능하다.
            - 반면 IGRP의 경우 반대로 IGRP 프로토콜에 정의되어 있지 않은 네트워크만 가능하다.
    2. Static 명령어 사용
        ```
        ip route 0.0.0.0 0.0.0.0 203.240.10.0
        ```
        - 0.0.0.0 0.0.0.0 은 Default Route를 의미 

# Dynamic Routing Protocol

---

## Distance-Vector 알고리즘과 Link-State 알고리즘

![Routing Protocol](/assets/images/2021-08-10/Net_Routing_Protocol.PNG){: .align-center}

### Distanc Vector Algorithm
- 이름 그래도 인근 라우터의 거리와 방향을 위주로 만들어진 라우팅 프로토콜
- 따라서, 목적지 라우터까지의 모든 경로를 라우팅 테이블에 저장하는 것이 아닌 hop-count, Neighbo-Router(인접 라우터)같은 정보만 저장한다.
- 장점
    - 라우팅 테이블의 정보를 줄일 수 있어 메모리 절약
    - 라우팅 구성이 간단해 라우터에 부담이 적음
- 단점
    - 라우팅 테이블의 변화가 없더라도 정해진 시간마다 라우팅 테이블의 업데이트가 발생해 네트워크의 트래픽 증가(RIP의 경우 30초)
    - Convergence Time(라우팅 테이블의 변화가 모든 라우터에 동기화 되는 시간)이 너무 느리다. 
    - 이러한 느린 단점으로 Hop-Count는 최대 15개 

### Link State Algorithm
- 거리벡터 알고리즘과 달리 라우텅 테이블에 목적지까지의 모든 경로를 다 저장한다.
- SPF(Shortest Path First) 알고리즘 계산을 통해 최적의 경로를 찾는다.
- 장점
    - 목적지까지의 모든 경로를 알고 있기 때문에 링크의 변화가 생겨도 이를 알아내는데 시간이 단축됨
    - 라우팅 테이블의 변화가 거리벡터 알고리즘에 비해 자주 발생하지 않고, 교환되더라도 테이블의 변화된 것만 교환해 네트워크 트래픽이 상대적으로 적다.
- 단점
    - 목적지까지의 모든 경로를 라우팅 테이블에 저장하기 때문에 메모리 소모가 크다.
    - SPF 등의 각족 연산이 필요해 라우터에 부담을 준다. 