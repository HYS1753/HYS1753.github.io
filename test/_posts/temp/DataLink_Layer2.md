---
author_profile: true
date: 2021-07-00
title: "DataLink Layer 2 - Bridge & Switch 기능"
categories: 
    - Network
tag: 
    - 데이터링크 계층
    - bridge
    - switch

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Bridge & Switch 의 기능

---

## 개요

- Physical Layer에서 사용되는 장비인 Hub가 단순히 신호를 증폭해 각 단말을 이어주는 역할을 했다면 Bridge, Switch는 더 나아가 `Collision Domain`을 나눠주는 역할을 한다.
- 즉, 이더넷에서 사용하는 CSMA/CD 때문에 허브에 연결된 각 단말들 중 두 단말이 통신을 시작하게 되면 나머지는 통신이 불가해 진다. 이를 해결하기 위해 Bridge, Switch를 사용하게 된다.

## Bridge & Switch의 기능

- Learning 
    - 출발지의 MAC Address가 브리지/스위치의 MAC Address Table에 없으면 해당 주소를 저장한다. 
    - 이를 토대로 통신 시 나눈 Collision Domain을 내 외부로 통신할지 안할 지 정한다.

- Flooding(Broadcasting)
    - 브리지/스위치로 들어온 프레임의 목적지가 MAC Address Table에 없으면 연결된 전체 포트로 전달한다.
    - 브로드케스트, 멀티캐스트의 경우에도 발생

- Forwarding(Unicasting)
    - 브리지/스위치가 목적지 MAC Address를 자신의 MAC Address Table에 가지고 있고, 이 목적지가 출발지의 MAC Address와 다른 세그먼트(다른 Collision Domain)에 있을 때 발생한다.
    - Flooding이 모든 포트로 프레임을 뿌리는 것과 달리 오직 해당하는 포트로만 프레임을 전송한다.

- Filtering(Collision Domain)
    - 브리지/스위치가 목적지 MAC Address를 자신의 MAC Address Table에 가지고 있고, 이 목적지가 출발지의 MAC Address와 같은 세그먼트에 있을 때 발생한다.
    - 즉, 동일 네트워크 안에서의 프레임 전송이므로 다른 세그먼트로 넘어가지 않도록 막는 기능
    - Forwarding = 다른 세그먼트, Filtering = 같은 세그먼트

- Aging
    - 브리지/스위치의 효율적인 Table 저장 공간을 위해 일정 시간동안 해당 MAC Address 로 부터 프레임이 들어오지 않으면 Table에서 삭제 하는 것.
    - Default 300초(5분)

- 정리
    |상태|기능|
    |-|-|
    |프레임 유입|1. 신규 : 출발지 주소 저장(Learning)|
    |-|2. 기존: Aging, 타이머 재시작(Refreshing)|
    |목적지가 Unknown or Broadcast or Multicast|3. Flooding|
    |목적지를 알고 다른 세그먼트 내 존재|4. Forwarding|
    |목적지를 알고 같은 세그먼트 내 존재|5. Filtering|

## Bridge와 Switch의 차이

- 둘다 DataLink Layer에서 동작하는 장비라는 점에서 동일하지만 지원하는 기능이 다르다.
- 차이점

    |기능|브리지|스위치|
    |-|-|-|
    |처리속도|SW로 처리속도 느림|ASIC(HW)로 처리속도 빠름|
    |포트간 속도|같은속도의 포트끼리만 연결|다른 속도를 가진 포트도 연결 가능|
    |포트 수|적음|많음|
    |처리 방식|Store-and-forwarding|Cut-through, Store-and-forwarding|

    - Store-and-forwarding
        - 일단 들어오는 프레임을 전부 받아 들인 후 처리를 시작하는 방식
        - 프레임을 전부 받아 드리고, 에러는 없는지, 출발지 주소는 어디인지, 목적지주소는 어디인지 등을 파악해 처리한다.
        - 만약 에러 발생 시 프레임을 버리고 재전송을 요청해 에러 복구능력이 뛰어나다
        - 따라서 회선에 에러가 자주 발생하거나 출발지, 목적지의 전송매체가 다른경우 자주 사용되는 방식이다.
    - Cut-through
        - 들어오는 프레임의 목적지 주소만 본 후 바로 전송하는 방식
        - 즉, 처음 48bit만 확인해 목적지 주소를 확인하고 전송해 이전 Store-and-forwarding 방식에 비해 훨씬 빠르다는 장점이 있다.
        - 하지만 에러 복구 능력은 부족하다.
    - Fragment-free
        - Store-and-forwarding과 Cut-through방식의 장점을 섞은 것이다.
        - Cut-through 방식과 달리 처음 48bit가 아닌 512bit를 확인해 에러감지와 속도 모두 우수한 방법

