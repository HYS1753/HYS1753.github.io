---
author_profile: true
date: 2021-07-00
title: "DataLink Layer 5 - VLAN 명령어 실습"
categories: 
    - Network
tag: 
    - 데이터링크 계층
    - VLAN
    - 명령어

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# VLAN(Virtual LAN) 먕량아

---

## VLAN 구성

- VLAN 구성은 Config Mode에서 가능

### VTP 구성
```
Switch(config) # vtp ?
domain      VTP의 도메인 이름 변경/설정
mode        해당 스위치의 VTP Mode 설정(Server, Client, Transparent)
password    VTP 도메인에 비밀번호 설정
prunimg     프루닝 설정
```

- Switch(config) # vtp domain cisco
- Switch(config) # vtp mode server
- Switch(config) # show vtp status (변경된 vtp 설정 값 확인)

### Trunkport 구성 (switchport)

```
Switch(config) # interface fa 0/1   // FastEthernet 0/1 설정으로 이동
Switch(config-if) # switchport mode trunk   // 해당 포트를 Trunk모드로 변경
Switch(config-if) # switchport trunk encapsulation dotlq    
// VLAN 트렁킹 모드(dotlq : IEEE 802.1Q, isl : Cisco ISL, negotoate : 상대 모드에 맞춤)
Switch # show interface fa0/1 switchport    // 설정한 Trunk 구성 확인
Switch # show interface trunk               // 스위치 전체에서 Trunk 설정된 포트 간략히 보여줌.
```

### VLAN 구성

- VLAN은 default VLAN 1으로 초기 셋팅 되어 있다.
- 각 스위치 마다 만들 수 있는 VLAN의 개수가 다르다.(show vtp status로 확인 가능)
- 스위치의 IP주소는 VLAN 1에 한다.(스위치에 IP주소 할당은 단순히 관리를 위함)
- VLAN을 추가 및 삭제하는 작업은 Server, Transparent 모드에서만 가능하다.

#### VLAN 생성
```
Switch # configure terminal
Switch(config) # vlan 2             // config-vlan 모드 진입
// 과거 CatOS 떄의 vlan database 로 vlan configuration 모드로 갈 수 있으나 과거 방식이므로 사용 x
Switch(config-vlan) # name cisco    // 만든 VLAN의 이름을 변경
Switch(config-vlan) # no vlan 2     // 만든 VLAN 2 삭제
Switch # show vlan                  // 구성되어 있는 vlan 확인
```

#### 각 포트를 VLAN에 할당
```
Switch(config) # interface fastEthernet 0/5     // 인터페이스 구성 모드
Switch(config-if) # switchport access vlan 2    // 해당 인터페이스를 vlan 2로 할당
Switch(config-if) # no switchport access vlan 2 // vlan 2로 할당한 해당 인터페이스를 default인 vlan1으로 변경
Siwtch # show vlan                              // 구성된 vlan확인
```

### VLAN 구성 정리
```
// Switch의 IP주소 설정
Switch(config) # interface vlan 1
Switch(config-if) # ip address 10.10.10.2 255.255.255.0
Switch(config-if) # exit
Switch(config) # ip default-gateway 10.10.10.1

// vtp 설정
Switch(config) # vtp mode transparent       // server, client, transparent
Switch(config) # vtp domain cisco           // vtp 도메인 네임 설정
// Switch # show vtp status                 // vtp 상태 확인

// VLAN 설정
Swtich(config) # vlan 2                     // vlan 2 생성
Switch(config-vlan) # name cisco2           // vlan 2 이름 설정
Switch(config-vlan) # exit  
Switch(config) # interface fa0/1            // 인터페이스 구성 모드
Switch(config-if) # switchport mode trunk   // 해당 인터페이스를 트렁크 모드로 변경
Switch(config-if) # switchport trunk encapsulation dotlq    // IEEE 802.1Q 사용
Switch(config-if) # switchport trunk allowed vlan all
Switch(config-if) # exit
Switch(config) # interface fa0/5
Switch(config-if) # switchport access vlan 2        // fa0/5를 vlan 2 로 할당.
```