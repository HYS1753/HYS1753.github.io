---
author_profile: true
date: 2021-07-00
title: "CCNA 정리 1 - DataLink"
categories: 
    - CCNA
tag: 
    - Switch Port Mode
    - VLAN
    - VTP(VLAN Trunking Protocol)
    - Switch의 STP(Spanning Tree Protocol)
    - EtherChannel
    - Port Security

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# DataLink Layer
---
## Switch Port Mode

- Access : VLAN
- Dynamic : negotiation Acces or Trunk
- Trunk 

---

## VLAN

- 한 대의 스위치를 마치 여러대의 분리된 스위치처럼 사용하고, 여러개의 네트워크 정보를 하나의 포트(Trunk)를 통해 전송하는 것.
- VLAN은 한 대의 스위치를 여러개의 네트워크(Broadcast Domain)로 나누기 위해 사용하는 것으로 각각의 VLAN은 바로 통신하지 못하고 Router를 통해서만 통신 가능하다.

#### VLAN Hopping Attack
일반적으로 Access 할 수 없는 VLAN의 트래픽에 접근하는 것.

---

## VTP(VLAN Trunking Protocol)

연결된 스위치 끼리 VLAN 정보를 주고 받아 자동으로 동기화 해주는 프로토콜

- 자동 Default VLAN은 1
- VLAN 사용 시 802.11Q(dot1q) 사용

#### Physical interface VS Sub interface

**Physical Interface**

- 1개의 분리된 VLAN 인터페이스
- 대역폭 경합 없음
- Mode Switch Port에 연결
- 덜 복잡하다.

**Sub Interface**

- 여러개의 VLAN을 위한 1개의 분리된 인터페이스
- 대역폭 경합 있음
- Trunk Mode Switch Port에 연결
- 복잡하다.

---

## Switch의 STP(Spanning Tree Protocol)

- Root Bridge는 무조건 낮은 BID를 가지는 브리지
- BID(Bridge Priority + MAC Address)
    - Bridge Priority는 0 ~ 65535까지 가능하며 Defualt는 중간 값인 32786이다.
- Non Root Bridge는 반드시 Root Bridge까지 가는 가장 작은 Path Cost를 가진 Root Port를 가진다.
- 한 세그먼트(브리지간의 연결)사이에 더 작은 Path Cost를 가지는 Port가 Designated Port가 된다.

#### STP 상태변화

1. **Disable** 
    포트고장, 관리자 포트다운
2. **Blocking**
	Disable에서 장치 켰을 때 상태, BPDU만 송수신
3. **Listening**
	Blocking 상태에서 Root Port, Designated Port가 	선정되면 Listening 상태가 된다.
4. **Learning**
	Listening 상태에서 15초(Forwarding Delay) 동안	변화가 없으면 Learning 상태가 되고 MAC Address 	Table을 구성한다.
5. **Forwarding**
	Learning 상태에서 15초 동안 네트워크 변화 없이 	유지되면 데이터 전송 가능한 Forwarding 상태가 된	다.

#### PortFast
- Listening, Learning 상태를 거치지 않고 Blocking 	상태에서 바로 Forwarding 상태로 변환. 
- Access Point에서만 사용하며 스위치간 연결에 	PortFast 설정 시 Spanning Tree Loop 발생 가능
- PortFast가 활성화된 인터페이스는 BPDU 수신 x

#### RSTP(Rapid STP) 
blocking Listening, Learning 상태를 Discard(차단)하기 때문에 가장 빠른 STP, 이때 Access Point는 PortFast로 구성

#### bpduguard
STP 보호 기술로 해당 포트로 BPDU 들어오면 포트를 Down 시키는 것

---

## EtherChannel

- 다수의 Port를 하나의 논리적 Port로 묶어서 사용하는 기술
- EtherChannel은 Cisco에서 사용하는 용어
- 다른 벤더는 Link-Aggregation or Trunk라 표현
- 2개 이상의 Switch를 다수의 L2 포트로 연결하면 Ring 구조가 되어 STP에 의해 하나의 Link를 제외하고 나머지는 Blocking된다. 하지만, EtherChannel을 사용하면 다수의 포트가 하나의 논리적인 포트로 인식되어 Blocking 포트가 선출안되고 다 전송, 

#### EtherChannel의 Negotiation Protocol 

1. **PAgP(Port Aggregation Protocol)**
    - Cisco 전용 프로토콜, 최대 8개 port를 논리적 Port로
    - Desirable(능동), Auto(수동) 방식으로 협상
2. **LACP(Link Aggregation Control Protocol)**
    - 표준(IEEE) 방식 프로토콜으로 최대 16개 논리적 Port로 묶을 수 있지만 실제로 전송은 8개 나머지는 Backup
    - Active(능동), Passive(수동) 방식으로 협상

---

## Port Security

특정 Port에 학습할 수 있는 MAC 주소 수 제한, 또는 인가된 MAC만 접속하도록 제한하는 것.

```show port-security interface ethernet0/0[type_num]```

```
Port Security : Enable
Port Status : secure-up / secure-shutdown
Violation Mode : Shutdown / Protect / Restrict
Aging Time : 0 ~ 1440Aging Type : Absolute / Inactivity
Secure Static Address Aging : Enabled / Disabled
Maximum MAC Address : 3 # 최대 학습 가능 MAC주소
Total MAC Address : 2 # 학습된 MAC 주소 개수
Configured MAC Address : 1 # Static 학습한 MAC 개수
Sticky MAC Address : 1 # Dynamic, Sticky 학습한 MAC
Last Source Address:VLAN : 0001.01AA.33DD.1 # 마지막 학습한 MAC 주소
Security Violation Count :０ # 위반된 MAC 주소 횟수, Violation Mode가 Restrict, Shutdown일 때만 count 진행
```

#### MAC-Address 학습 방법
- Static : 수동으로 학습을 원하는 MAC 지정, NVRAM 저장
- Dynamic : 스위치가 스스로 MAC 학습, NVRAM 저장 x
- Sticky : 스위치가 스스로 MAC 학습, NVRAM 저장 o

#### Violation Mode 
- ShutdownPort 위반한 경우 Port를 Shutdown(닫아버림).Port security 정책 위반에 의해 차단된 경우 인터페이스 설정 확인하면 ’err-disabled‘로 표시된다.
- protect : 위반한 MAC 주소의 패킷 Drop, 허용된 호스트는 사용 가능
- RestrictProtect와 같지만 Log를 발생시킨다.

#### Aging Time Type
- Absolute aging time 만료되어야 MAC 주소를 삭제
- Inactivityaging time 동안 Data Treffic이 없을 경우 MAC 삭제
