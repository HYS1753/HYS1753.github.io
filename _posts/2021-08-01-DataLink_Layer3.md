---
author_profile: true
date: 2021-08-01
title: "DataLink Layer 3 - STP(Spanning Tree Protocol)"
categories: 
    - Network
tag: 
    - 데이터링크 계층
    - STP

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Bridge & Switch 2 - STP

---

## Bridge와 Switch 의 문제점 Looping(루핑)

- 어떠한 스위치나 브리지가 연결된 목적지까지의 경로가 2개 이상이 될 경우 반드시 루핑이 발생한다.
- 예를 들어 한 노드에서 브로드캐스팅 하고 브리지는 브로드캐스팅 주소이므로 Flooding을 하게 된다. 이때 세그먼트를 넘어가면 반대 쪽으로도 계속 왔다 갔다 해 looping이 발생하게 된다.
- 이러한 looping 문제를 해결하기 위한 방법이 스패닝 트리 알고리즘(Spanning Tree Algorithm)이다.

## Spanning Tree Protocol(STP)

스패닝 트리 프로토콜이란, 스위치나 브리지에서 발생할 수 있는 루필을 미리 막기 위해 두 개 이상의 경로가 발생하면 하나를 제외하고 나머지 경로를 자동으로 막아두었다가, 기존 경로에 문제가 생길 경우 막았던 경로를 풀어서 데이터를 전송시키는 것.

### 기본개념
- **브리지 ID(Bridge ID)**
    - 브리지나 스위치들이 통신할 때 서로를 확인하기 위해 하나씩 가지고 있는 번호

        |Bridge Priority|MAC Address|
        |-|-|
        |2byte(16bit)|6byte(48bit)|
        
    - 위의 표와 같이 Bridge ID를 구성한다.
    - Bridge Priority는 0 ~ 65535 까지의 숫자가 들어갈 수 있으며 Default 값은 중간값인 32786이다.
- **Path Cost**
    - 브리지가 얼마나 가까이, 그리고 빠른링크로 연결되어 있는지 알아내기 위한 값.
    - 기존에는 `1000Mbps / 두 장비 사이의 링크 대역폭`의 공식으로 Cost값을 계산하였다. 즉, Cost 값이 작을 수록 속도는 빠른 것이다.
    - 하지만, Gbps 단위의 전송속도가 발전되면서 소숫점 단위가 발생해 문제가 발생했다.
    - 이러한 문제를 해결하기 위해 IEEE에서는 Path Cost를 정수표준으로 각 대역 링크 속도에 맞는 Cost 값을 정해 두었다.

        |Bandwidth(대역폭)|STP Cost(Path Cost)|
        |-|-|
        |4Mbps|250|
        |**10Mbps**|**100**|
        |16Mbps|62|
        |45Mbps|39|
        |**100Mbps**|**19**|
        |155Mbps|14|
        |622Mbps|6|
        |**1Gbps**|**4**|
        |**10Gbps**|**2**|

### Spanning Tree Protocol 의 동작

- **동작 과정**
    1. 네트워크(Broadcast Domain) 당 한개의 루트 브리지(Root Bridge)를 가진다.
    2. 루트브리지가 아닌 나머지 모든 브리지(Non-Root-Bridge)는 무조건 하나의 루트 포트(Root Port)를 가진다.
    3. 세그먼트(Segment)당 하나씩의 데지그네이티드 포트(Designated Port: 지정포트)를 갖는다.

- 추가
    - Collision Domain 과 Broadcast Domain 과의 차이점 
    ![Collision, Broadcast Domin](/assets/images/2021-08-01/CollisionBroadcastDomain.PNG){: .align-center}
    
    - Root Port(루트포트) : 루트 브리지에 가장 빨리 갈 수 있는 포트
    - Segment(세그먼트) : 브리지 또는 스위치 간에 서로 연결된 링크

### STP에서의 브리지 우선 순위 결정 방법
- 루트브리지, 루트 포트, 데지그네이티드 포트 등의 브리지 간의 우선순위를 정할 때 사용하는 순서

- **BPDU(Bridge Protocol Data Unit)**
    - 스패닝 트리 정보를 브리지들 끼리 주고 받기 위한 특수한 프레임
    - 매 2초에 한번씩 뿌려주는 프레임
    - Root BID, Root Path Cost, Sender BID, Port ID 와 같은 정보가 실려 있음.

- **선정 순서**
    1. 가장 작은 BID를 가진 브리지
    2. 루트 브리지까지 Path Cost가 낮은 브리지
    3. Sender BID가 낮은 브리지
    4. Port ID가 낮은 브리지

### Root Bridge 선정

- 무조건 낮은 BID를 가지는 브리지가 Root Bridge가 된다.

- 선정 순서
    1. 브리지가 맨 처음 부팅(시작)하면 아직 BPDU를 주변으로 부터 받지 못하였기 때문에 실질적으로 자기 자신이 Root Bridge라고 생각하고 주변으로 Sender BID, Root BID 모두 자신의 BID를 넣어 전송한다.
    2. 다른 브리지도 1번과 같은 과정을 거치며 이후에 들어온 BPDU의 Sender BID가 자신의 BID 보다 낮으면 상대방이 Root Bridge가 되기 떄문에 이후의 BPDU에 Root BID를 해당 Sender BID로 변경하여 전송한다.
    3. 또는 이후에 들어오는 BPDU의 Sender BID가 자기자신의 BID보다 크면 기존 그대로 전송해 네트워크 전체의 Root Bridge를 찾을 수 있게 된다.
    4. 추가적으로 네트워크 관리자가 임의의 브리지를 Root Bridge로 하기 위해서 `spanning-tree vlan 1 priority 100`과 같은 명령어로 Bridge Priority를 변경해 강제로 고정시킬 수 있다.

### Non Root Bridge의 Root Port 선정

- Root Bridge가 아닌 Non Root Bridge는 반드시 한 개의 Root Port를 가져야 한다.
- Root Port는 Root Bridge까지 Path Cost가 가장 적게 드는 포트이다.
- Root Bridge에서 출발하는 Port는 0을 시작해 IEEE에서 정의한 각 Bandwidth별 Cost값을 계산해 Path Cost를 구한다.
- 각 Non Root Bridge에 연결된 포트들 중에서 가장 낮은 Cost값을 가진 Port를 Root Port로 선정한다.

### Designated Port 선정

- 루트 브리지까지의 Path Cost, 즉 한 세그먼트(브리지 간의 연결)상에서 Root Path Cost를 서로 비교 해 더 작은 Cost를 가진 포트가 데지그네이티드 포트로 선정된다.
- Root Bridge에 연결된 모든 살아있는 Port는 Designated Port가 된다.
- Non Root Bridge 간에 연결된 세그먼트는 Cost계산으로 1개를 선정한다.
- 만약 세그먼트 양단의 Cost가 같다면 `STP에서의 브리지 우선순위 결정방법`의 순서에 때라 Designated Port 한개를 선출한다.

### STP의 5가지 상태 변화

- Disabled
    - 포트가 고장나서 사용할 수 없거나, 네트워크 관리자가 포트를 일부러 Down 시킨 상태
- Blocking
    - 브리지, 스위치를 맨 처음 켜거나 Disabled 되어있는 포트를 관리자가 다시 살렸을 때 해당 포트는 Blocking 상태가 된다.
    - 데이터 전송은 안되고 오로지 BPDU 만 주고 받는 상태
- Listening
    - Blocking 상태에 있던 포트가 Root Port, Designated Port로 선정되면 해당 포트는 Listening 상태가 된다.
    - 만약 새로운 스위치가 네트워크에 참여하거나 네트워크의 구성이 달라지게 되면 언제든지 다시 Blocking 상태가 된다.
- Learning
    - Listening 상태에 있던 브리지, 스위치 포트가 Forwading Delay 디폴트 시간인 15초를 유지하면 Learning 상태로 전환
    - Learning 상태가 된 브리지, 스위치는 비로서 MAC Address Table을 만들게 된다.
- Forwarding
    - Learning 상태에서 Forwarding Delay 디폴트 시간인 15초동안 또 한번 유지하게 된다면 Forwarding상태로 전환된다.
    - 이 Forwarding 상태가 되어야 비로서 데이터를 전송할 수 있다.
    - 즉, Blocking 상태에 있던 포트가 Listening -> Learning -> Forwarding 상태까지 오기 위해선 Forwarding Delay 2번 즉 30초가 필요하다.

- 정리

    |상태|BPDU 전송|MAC Address Table 생성|Data 전송|
    |-|-|-|-|
    |Disabled|X|X|X|
    |Blocking|O|X|X|
    |Listening|O|X|X|
    |Learning|O|O|X|
    |Forwarding|O|O|O|

- STP 개선 프로토콜
    - STP는 Blocking에서 데이터 전송하기 위한 Forwarding까지 시간이 많이 걸리기 때문에 비효율적인 측면이 있음 이러한 문제를 해결하기 위해 다양한 프로토콜으로 발전하고 있다.
    - RSTP(Rapid Spanning Tree Protocol)
        - IEEE 802.1w 에 정의되어 있으며, STP를 적용하면 포워딩 상태까지 30~50초 걸릴 것을 Listening, Learning 상태를 생략해 컨버전스 타임을 1~2초 내외로 단축 시킨다.
    - Port Fast
    - Up-link Fast
    - Backbone Fast
    - MST(Multiple Spanning Tree)
        - IEEE 802.1s 에 정의되어 있으며, 네트워크 그룹이 많아지면 STP 또는 RSTP BPDU 프레임이 많아지고 스위치에 부하가 발생하게 된다. 
        - 이때 여러개의 STP 구룹으로 묶어서 효율적으로 관리하는 방식이 MST 이다.

### STP 도중 연결이 끊길 경우 재설정 과정

- 기본 용어
    - Hello Time(헬로 타임)
        - Root Bridge가 얼마만에 한번씩 Hello BPDU를 전송하는지에 대한 시간.
        - Root Bridge는 Hello Time마다 한번씩 연결된 모든 브리지들에게 BPDU를 전송한다.
        - Default Hello Time 은 2 Seconds
    - Max Age(맥스 에이지)
        - 브리지들이 루트 브리지로 부터 Hello BPDU를 받지 못하면 Max Age 만큼의 시간동안 기다린 후 STP의 구조를 변경 시킨다.
        - 즉, 브리지들이 Root Bridge와 연결이 손실되었다고 인지하는 시간.
        - Default Max Age 는 20 Seconnds
    - Forwarding Delay(포워딩 딜레이)
        - STP의 상태 변화중 Listening -> Learning, Learning -> Forwarding으로 상태가 변하는데 걸리는 시간이다.
        - Default Forwarding Delay는 15 Seconds

- 재설정 과정
    1. STP로 연결된 스위치, 브리지들은 Hello Time 마다 Root Bridge로 부터 Hello Packet을 BPDU를 통해 수신한다.
    2. 만약 Hello Packet을 Max Age 시간동안 Root Bridge로 부터 수신하지 못하면 해당 Port를 닫고 다른 포트로 부터 Hello Packet을 수신한다.(Blocking 상태의 Port도 BPDU는 수신할 수 있음)
    3. Hello Packet이 다른 Port로 부터 수신되면 해당 Port를 Root Port로 변경하고 Forwarding Delay시간만큼 대기하며 Blocking 상태에서 Forwarding 상태로 변경한다.
