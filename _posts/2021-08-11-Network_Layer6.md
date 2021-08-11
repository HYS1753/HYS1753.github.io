---
author_profile: true
date: 2021-08-11
title: "Network Layer 6 - Routing Protocol(Distance-Vector)"
categories: 
    - Network
tag: 
    - 네트워크 계층
    - Routing Protocol
    - Distance Vector Algorithm

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Distance Vector Algorithm

---

## RIP(Routing Infomation Protocol)

---

### 특징

- IGP(Interior Gateway Protocol)이자, Dynamic Routing Protocol 중 한가지 프로토콜
- Distance-Vector(거리벡터) 알고리즘을 사용하는 라우팅 프로토콜으로 라우터간의 거리를 표시하는 Hop Count를 기준으로 목적지까지의 최적 경로를 찾는다.
- RIP의 최대 Hop Count는 15이다. 이상은 Unreachable(looping 방지 및 Convergence time 감소 목적)
- RIP의 라우팅 테이블 업데이트 주기는 30 seconds.
- RIP는 단순한 라우팅 방법이자 오래된 라우팅 프로토콜으로 모든 라우터에서 지원하는 표준 라우팅 프로토콜이다.
- RIP는 라우터의 메모리를 적게 차지한다는 장점이 있다.
- Hop Count만 의존해 경로를 결정하기 때문에 회선에 대한 속도, 신뢰성을 고려하지 못한다는 단점이 있다. 
- 또한, Hop Count의 최대 15의 제한 떄문에 소규모 네트워크에 적합하다.

### Router에서 RIP 구성

```
Router(config) # router rip         // RIP 라우팅을 시작하겠다는 의미
Router(config-router) # network network-number  // RIP에 참여하는 네트워크 지정
Router(config) # no router rip      // RIP 라우팅 명령 취소 
```

- 위와 같은 3개의 명령어로 RIP 구성이 가능하다.
- RIP는 참여하는 네트워크의 서브넷 마스크를 입력하지 않는다. 이로 인해 클래스에 정의안된 150.150.100.0/24 의 경우 /24 가 아닌 class에 따라 /16으로 인식한다.

#### 다음과 같은 구성으로 RIP를 설정하는 방법

![RIP](/assets/images/2021-08-11/NET_RIP.PNG){: .align-center}

- 명령어

```
interface ethernet 0/0
ip address 192.168.150.1 255.255.255.0
no shutdown
interface serial 0
ip addresss 192.168.100.1 255.255.255.0
no shutdown
router rip
network 192.168.100.0
network 192.168.150.0
```

- 전체 명령어(Packet Tracer - Router1에서의 명령어)

```
Router>enable
Router#config terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#interface fa 0/1
Router(config-if)#ip address 192.168.200.1 255.255.255.0
Router(config-if)#no shutdown

Router(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up

Router(config-if)#exit
Router(config)#interface fa 0/0
Router(config-if)#ip address 192.168.150.2 255.255.255.0
Router(config-if)#no shutdown

Router(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up

Router(config-if)#exit
Router(config)#router rip
Router(config-router)#network 192.168.150.0
Router(config-router)#network 192.168.200.0
```

- 설정한 구성 확인(show ip protocol)

```
Router#show ip protocol
Routing Protocol is "rip"
Sending updates every 30 seconds, next due in 2 seconds
Invalid after 180 seconds, hold down 180, flushed after 240
Outgoing update filter list for all interfaces is not set
Incoming update filter list for all interfaces is not set
Redistributing: rip
Default version control: send version 1, receive any version
  Interface             Send  Recv  Triggered RIP  Key-chain
  FastEthernet0/0       1     2 1   
  FastEthernet0/1       1     2 1   
Automatic network summarization is in effect
Maximum path: 4
Routing for Networks:
	192.168.150.0
	192.168.200.0
Passive Interface(s):
Routing Information Sources:
	Gateway         Distance      Last Update
	192.168.150.1        120      00:00:16
Distance: (default is 120)
```

**Routing Protocol is "rip"**  : 현재 IP에서 동작중인 라우팅 프로토콜
**Sending updates every 30 seconds, next due in 2 seconds** : RIP는 30초 마다 라우팅 테이블을 업데이트하고, 업데이트 경과 시간 표시
**Invalid after 180 seconds, hold down 180, flushed after 240** : 라우팅 정보를 30초 안에 못받게 될경우 Invalied Time(180)초 동안 기다린다. 기다려도 안들어올 경우 Hold Down 상태가 되고 라우팅 테이블에 Possibly down 표시한다. 그 이후로 1분이 더 지나게 되면 테이블에서 해당 정보 지움(Flush Time)
**Routing for Networks** : 현재 RIP가 적용되고 있는 네트워크 
**Routing Information Sources** : 라우팅 정보를 얻어오는 위치 표시, Distance는 `Administrative Distance`로 라우팅 정보에 대한 신뢰도 를 표시

- 라우팅 테이블 확인(show ip route)

```
Router#show ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

R    192.168.100.0/24 [120/1] via 192.168.150.1, 00:00:20, FastEthernet0/0
C    192.168.150.0/24 is directly connected, FastEthernet0/0
C    192.168.200.0/24 is directly connected, FastEthernet0/1
```

`R    192.168.100.0/24 [120/1] via 192.168.150.1, 00:00:20, FastEthernet0/0` R은 RIP를 통해 찾아낸 경로를 의미(C는 직접 연결됨), 뒤의 IP 주소는 발견한 목적지 의미, [120/1]은 RIP의 Distance 값과, Hop count 의미, 


## IGRP(Interior Gateway Routing Protocol)

---

### 특징

- IGP(Interior Gateway Protocol)이자, Dynamic Routing Protocol 중 한가지 프로토콜
- Distance-Vector(거리벡터) 알고리즘을 사용하는 라우팅 프로토콜이다.
- RIP의 최대 Hop Count는 15인데 반해 IGRP는 255까지 가능해 비교적 큰 규모의 네트워크에서도 사용이 가능하다.
- IGRP는 RIP와 달리 Hop Count만을 가지고 경로를 파악하지 않고 다음과 같은 5개의 요소를 파악해 경로를 선택한다.
    - Bandwidth(대역폭) : 전송속도를 의미하며 Default 값 으로 1.544Mbps이다.
        - 이를 수정하기 위해선 `Router(config-if) # bandwidth Kbps단위`로 할 수 있다.
        - 확인은 `Router # sh int interface명`으로 한다.
    - Delay : 경로를 통해 도착할 때까지 걸리는 지연시간.
    - Reliability : 신뢰성으로 목적지까지 제대로 도착한 패킷과 에러가 발생한 패킷의 비율로 Keepalive를 이용해 측정한다. 이 값은 자동으로 계산되는 값으로 0 ~ 255까지의 범위를 가지고 있다.
    - MTU : Maximum Transmission Unit으로 최대 전송유닛 크기.(Byte 단위)
- IGRP는 RIP 와 달리 90초 마다 한번씩 라우팅 정보를 갱신한다.
- IGRP는 시스코에서 만들어낸 프로토콜으로 시스코 장비끼리만 사용가능하며, VLSM 사용 불가.

### Router에서 IGRP 구성

```
Router(config) # router igrp AS(Autonomous System) number     // IGRP 라우팅을 시작하겠다는 의미
Router(config-router) # network network-number  // IGRP에 참여하는 네트워크 지정
Router(config) # no router igrp      // IGRP 라우팅 명령 취소 
```

- 위와 같은 3개의 명령어로 RIP 구성이 가능하다.

#### 위의 RIP로 구성한 네트워크를 IGRP로 변경

- 명령어

```
no router rip
router igrp 200
network 192.168.100.0
network 192.168.150.0
```

- 설정한 구성 확인(show ip protocol)

```
Router#show ip protocol
Routing Protocol is "igrp 200"
Sending updates every 90 seconds, next due in 2 seconds
Invalid after 270 seconds, hold down 280, flushed after 630
Outgoing update filter list for all interfaces is not set
Incoming update filter list for all interfaces is not set
Default networks flagged in outgoing updates
Default networks accepted from incoming updates
IGRP metric weight K1=1, K2=0, K3=1, K4=0, K5=0
IGRP maximum hopcount 100
IGRP maximum metric variance 1
Redistributing: igrp
Routing for Networks:
	192.168.150.0
	192.168.200.0
Routing Information Sources:
	Gateway         Distance      Last Update
	192.168.150.1        100      00:00:16
Distance: (default is 100)
```

**확인할 수 있는 정보**

현재 IP 라우팅 프로토콜로는 igrp 200을 사용하고 있음
라우팅 정보의 업데이트 시간은 매 90초마다 일어나고 있음을 알 수 있음
Invalid Time : 270, Hold down Time : 280, Flush Time 630 임을 알 수 있음
`IGRP maximum hopcount 100`으로 최대 hop count가 100이다 이는 Default로 최대 255까지 늘릴 수 있다.
RIP의 `Administrative Distance`는 120 이였지만 igrp는 100임을 확인할 수 있다. 만약 두가지의 라우팅 프로토콜 전부 사용할 경우 낮은 Distance인 igrp 를 사용한다.

- 라우팅 테이블 확인

```
Router#show ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

I    192.168.100.0/24 [100/8576] via 192.168.150.1, 00:00:20, FastEthernet0/0
C    192.168.150.0/24 is directly connected, FastEthernet0/0
C    192.168.200.0/24 is directly connected, FastEthernet0/1
```

**확인할 수 있는 정보**

`I    192.168.100.0/24 [100/8576] via 192.168.150.1, 00:00:20, FastEthernet0/0` 으로 IGRP를 사용해 192.168.100.0 네트워크를 찾았고 
192.168.150.1를 통해 갈 수 있다. 또한, 이 라우팅 정보의 Distance는 100, Matric 값은 8576아다.


## BGP(Border Gateway Protocol)

---

### 특징
- 현재 인터넷에서 쓰이는 가장 대표적인 EGP 라우팅 프로토콜
- AS to AS(ISP(internet service provider) to ISP) 연결 간 사용
- 경로 벡터 라우팅 프로토콜 사용(루핑 방지), 변경 또는 추가된 부분만 업데이트
- 빠른 속도 보다는 조직 또는 단체 간 맺어진 정책에 의거하여 안정성에 따른 최적경로 결정

## 구성
1. eBGP : 서로다른 AS 간의 연결 및 라우팅 정보 교환 – external
2. iBGP : 동일 AS 내에서 BGP 라우팅 정보 교환 – internal

## BGP 구성방법

```
Router ID : 라우터 식별용 IP 설정
Neighbor : OSPF처럼 자동으로 탐지가 불가능해 직접 인접 라우터의 AS 번호를 설정해 준다.(Connected 인터페이스로 Next hop 설정)
Network : BGP에 참여하는 Network 
```

### BGP 메시지 
- OPEN : 인접 라우터와 연결된 후 보내는 메시지(BGP 버전, AS 번호, Hold Time, Operation parameter)
- UPDATE : 경로에 대한 속성 값(Unreachable Route, Path Attribute, Network Layer Rechablility)
- NOTIFICATION : 에러가 감지되면 에러코드를 보내고 BGP 연결 종료
- KEEPALIVE : 주기적으로 인접 라우터와의 연결을 확인

### BGP State Machine
피어 라우터와 동작을 결정하기 위해 6가지의 상태 머신을 사용한다.
1. Idle : 모든 자원을 초기화 하고 피어 연결 준비 상태
2. Connect : 연결이 완료되기를 기다리는 상태
3. Active : 연결 실패 이후 다시 연결을 시도하는 상태
4. Open Sent : Open 메시지를 보내는 상태
5. Open Confirm : Open 메시지를 받은 상태
6. Established : KEEPALIVE 메시지를 받은 상태

## Disatance Vector 방식의 문제점

---

- Convergence Time(업데이트 된 라우팅 정보가 전체 네트워크에 전달되는 시간)이 많이 걸린다는 문제점.
- 라우팅 정보가 전달되는 지연 문제로 라우팅 테이블이 꼬여 Looping 현상이 생길 수 있다.
- 이러한 문제점을 해결하기위한 방법이 고안됨(루핑 방지법)

    1. Maximum Hop Count
        - 최대 홉 카운트 개수를 규정(RIP=15)하고 이 이상 넘어갈 경우 unreachable로 간주하고 Flush Time이후 테이블에서 삭제
    2. Hold Down Timer
        - 라우팅 정보에서 한 네트워크에 대한 다운이 표시되면 해당 네트워크를 Hold Down 시킨다(Hold Time Timer 동작).
        - Hold Time Timer가 동작되는 동안에는 해당 네트워크에 대한 메트릭스 값(RIP = Hop Count)가 기존에 가지고 있는 것 보다 크면 무시한다.
    3. Split Horizon
        - 라우팅 정보가 들어온 곳으로는 같은 네트워크 정보를 내보내지 않는다.
        - 이는 연결된 두 라우터 사이에만 looping을 방지하기 때문에 전체 네트워크의 looping을 막는 것은 아님
    4. Route Poisoning
        - 다운된 네트워크의 메트릭스의 MAX 값(RIP의 경우 15)이상으로 설정해 사용할 수 없는 네트워크로 만듬
    5. Poison Reverse
        - Split Horizon의 변형된 방법으로 라우팅 정보를 받은쪽에 안알려 주는 것이 아닌 Route Poisoning 처럼 무한대 값으로 변경해서 전송
    
- VLSM(Variable Lenght Subnet Mask) 사용 불가
    - VLSM은 IP 주소의 효율적인 이용을 위해 한 Router에 접속되는 네트워크마다 서로 다른 Subnet Mask를 주도록 하는 방법
    - RIP, IGRP의 경우 라우팅 테이블안에 Subnet mask 정보가 들어있지 않아 이 기능을 사용할 수 없다. 
    - 사용하기 위해서는 EIGRP, OSPF 같은 프로토콜을 사용하면 된다.
