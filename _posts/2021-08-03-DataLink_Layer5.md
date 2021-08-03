---
author_profile: true
date: 2021-08-03
title: "DataLink Layer 5 - VLAN"
categories: 
    - Network
tag: 
    - 데이터링크 계층
    - VLAN

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# VLAN(Virtual LAN)

---

## VLAN 이란

- 한대의 스위치를 마치 여러대의 분리된 스위치처럼 사용하고, 여러 개의 네트워크 정보를 하나의 포트(Trunk)를 통해 전송하는 것.
- VLAN은 Hub 나 Bridge에서는 지원하지 않는 기능이기에 VLAN을 지원하는 장비는 스위치 이상의 장비이다.
- VLAN은 한대의 스위치를 여러개의 네트워크(Broadcast Domain)로 나누기 위해 사용하는 것으로, 한대에 스위치에 나눠진 VLAN1, 2, 3 ... 등은 바로 통신할 수 없고 `오직 라우터를 통해서만` 통신이 가능하다. 

### VLAN의 종류

- **Port 기반 VLAN**
    - 여러개의 VLAN을 설정하고 각각의 LAN에 물리적인 포트 지정 VLAN변경이 필요한 호스트는 물리적인 포트 또는 스위치의 VLAN 설정을 변경
    - ex. Vlan 1 : FastEthernet 0~3, Vlan 2 : FastEthernet 4~6 ...
- **MAC 주소 기반 VLAN** 
    - 각 호스트 또는 네트워크 장비의 MAC 주소를 각각의 VLAN에 정의 호스트가 이동되어도 VLAN변경 필요 없음, 신규 호스트 연결 시 설정 변경 필요
    -  ex. Vlan 1 : MAC Address aaaa.aaaa.aa00~10 Vlan 2 : MAC Address aaaa.aaaa.aa11~20 ...
- **IP 주소 기반 VLAN**
    - IP 주소 서브넷 기반으로 VLAN을 나누는 방법
        - IP(Internet Portocol) : 3 계층에서 사용하는 프로토콜 ex.) 192.168.10.1
        - 서브넷 : IP 주소의 네트워크 영역의 크기를 나눈 것
    - ex. Vlan 1 : 102.168.1.0/24 Vlan 2 : 192.168.2.0/24 ...



## Trunking(트렁킹)

- 각 스위치에 여러개의 VLAN이 있으면 원래는 각 VLAN 별로 링크를 만들어 주어야 하지만, 그렇게 되면 너무 많은 링크가 필요로 하게 된다.
- 이를 해결하기 위한 방법으로 여러 VLAN이 하나의 링크를 통해 다른 스위치 또는 라우터로 이동시키는 것을 트렁킹이라 한다.
- 같은 링크를 통해 데이터가 이동하나 VLAN 사이의 데이터 통신을 반드시 라우터를 통해야 한다. 
- 트렁킹을 구현하기 위해 각 VLAN 별로 자신의 패킷을 전송할 때 테그를 붙이는데 이 방법에 따라 `ISL 트렁킹`, `IEEE 802.1Q` 방식의 트렁킹으로 분류된다.
    - **ISL 트렁킹**
        - 시스코에서 만든 독자적인 트렁킹 프로토콜(비표준)
    - **IEEE 802.1Q 트렁킹**
        - IEEE에서 지정한 트렁킹 표준 프로토콜
        - ISL 트렁킹과 달리 Native VLAN의 개념이 있다. 
        - Native VLAN은 다른 VLAN들이 Tag를 붙여서 구분하나, 네트워크 내에 딱 한개 존재하는 Native VLAN은 Tag를 붙이지 않고 전송한다.(Untagged 트래픽)
        - IEEE 802.1Q Tagging Format
            - 이더넷 프레임에 삽임되며 4 Byte로 구성된다.
            - Pre - Destination Address - Source Address - **TAG** - Type/Len - Data - FCS
            - ![Tagging format](/assets/images/2021-08-03/Net_VLAN_TAG.PNG)
            - TPID(Tag Protocol IDentifier) : 태그되지 않은 프레임과 태깅된 프레임 구별
		    - TCI(Tag Control Information) : 태그 제어정보
		        1. PCP(Priority Code Point) : 프레임의 우선순위
		        2. DEI(Drop Eligible Indicator) : 트래픽 혼잡시 제거되기 적합한 프레임 표기
		        3. VID(VLAN Identifier) : VLAN이 어느 프레임에 속하는를 결정

## VTP(VLAN Trunking Protocol)

- 스위치들 간에 VLAN 정보를 서로 주고받아 스위치 들이 가지고 있는 VLAN 정보를 항상 일치 시켜 주기 위한 프로토콜이다.
- VTP가 없으면 하나의 VLAN을 변경하기 위해 구성되어 있는 모든 스위치의 VLAN 정보를 일일히 수정해야 하지만, VTP를 사용하면 자동으로 변경사항을 변경해 준다.
- VTP는 시스코만의 프로토콜으로 시스코 장비에서만 사용 가능하다.
- VTP에서의 VLAN 정보는 스위치 간의 Trunk Link를 통해서만 전달되며, 스위치 간의 트렁킹 방식이 ISL이든 IEEE 802.1Q이든 상관없이 전달이 가능하다.

### VTP 도메인 구성
- **VTP Domain Name** : VTP로 동기화할 이름으로 같은 Name끼리 VTP동기화 한다. 초기 는 Null
- **VTP Mode** : 헤딩 스위치의 VTP Mode(VTP Server, Client, Transparent)
- **config Revision** : VLAN 구성이 수정된 횟수로 해당 Revision 값이 다르면 VTP 동기화 한다. 초기 설정은 0
- **VLAN** : VLAN 구성(이름), 초기 설정은 VLAN 1.

### VTP 메시지 종류

- **Summary Advertisement**
    - VTP 서버가 자기자신에게 연결되어 있는 스위치들에게 5분에 한번씩 전달하는 메시지
    - 자신이 관리하는 VTP 도메인의 구성에 대한 Revision Number를 전송한다.
    - Summary Advertisement를 수신한 스위치는 Revision Number를 통해 자신의 VLAN 정보가 최신인지 아닌지 판단한다.
    - 만약 VLAN의 구성에 변화가 생길 시에는 5분을 기다리지 않고 바로 전송한다.
- **Subset Advertisement**
    - VLAN의 구성이 변경되었을 때나 VTP Client로 부터 Advertisement Request 메시지를 받았을때 해당 메시지가 전송된다.
    - 실제 VLAN 정보가 Subset Advertisement 메시지를 통해 전달된다.
- **Advertisement Request**
    - 클라이언트 스위치가 VTP 서버에 Summary Advertisement, Subset Advertisement 메시지를 요청하는 용도로 사용되는 메시지
    - 클라이언트 스위치는 다음과 같은 상황에서 Advertisement Request 메시지를 VTP 서버로 전송한다.
        1. 자신의 Revision Number보다 높은 Summary Advertisement 메시지를 받은 경우
        2. VTP 도메인 이름이 바뀐 경우
        3. Subset Advertisement 메시지를 수신하지 못한 경우
        4. 스위치가 리셋된 경우

### VTP Mode

- **VTP Server Mode**
    - VLAN 생성, 삭제, 이름 수정을 수행할 수 있음
    - VTP 도메인 안에 있는 다른 스위치 들에게 VTP 도메인 이름, VALN 구성, Revision Nunmber를 전달해 줄 수 있다.
    - VTP 서버는 VTP 도메인의 모든 구성을 NVRAM(비휘발성 메모리)에 저장해 전원이 꺼저도 저장된 구성을 가지고 있다.
- **VTP Client Mode**
    - VLAN 생성, VLAN 삭제, VLAN 이름 변경이 불가하다.
    - VTP Server로 부터 전달해준 구성 정로를 통해 동기화 한다.
    - 연결된 다른 스위치로 Server 로 부터 받은 구성 정보를 전달한다.
    - VTP Client는 구성정보를 NVRAM에 저장하지 않아 꺼지면 정보가 사라저 다시 Server로 부터 받아야 한다.(메모리 적은 스위치에 적합)
- **VTP Transparent Mode**
    - VTP 도메인 영역안에 존재하지만, Server로 부터 구성정보를 받아 업데이트 하거나, 자신이 업데이트한 VLAN 정보를 다른 스위치에 전달하지 않는다.
    - 직접 VLAN 생성, 삭제 등이 가능하다
    - 서버로 부터 구성 메시지를 받아도 자신의 정보는 수정하지 않지만, 연결된 다른 스위치로 Server의 구성 메시지를 전달은 해줌

### VTP 동작 과정

1. 한 스위치의 구성 정보를 수정한다.

    ```
    - VTP Domain Name : VTPTest
    - VTP Mode : Server
    - config Revision : 2
    - VLAN : 1, 2, 3
    ```
2. Server 가된 수정한 스위치가 연결된 다른 스위치로 Summary Advertisement 메시지를 전송한다.
3. 이를 수신한 Client 스위치는 VTP Domain Name을 수정하고 Revision Number를 확인한다.
4. 만약 수신한 Revision Number가 자신의 구성보다 높으면 Advertisement Request 메시지를 Server 스위치에 전달해 VLAN 구성을 요청한다.
5. Advertisement Request를 수신한 Server 스위치는 Summary Advertisement, Subset Advertisement을 해당 Client 스위치에 전달해 VLAN 정보를 일치 시킨다.
6. 만약 Transparent Mode의 스위치가 있다면 자신의 구성을 상관하지 않고 들어오는 대로 다시 연결된 스위치에게 VTP 메시지를 전달만 해준다.

### 추가
VTP Pruning(푸루닝) : 가지치기라는 뜻으로 VLAN 트래픽 중 갈 필요 없는 Trunk 쪽으로 데이터가 흘러갈 경우 해당 링크를 끊음