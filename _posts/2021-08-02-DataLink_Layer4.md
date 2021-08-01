---
author_profile: true
date: 2021-08-02
title: "DataLink Layer 4 - Switch 명령어 실습"
categories: 
    - Network
tag: 
    - 데이터링크 계층
    - Switch 명령어

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Switch 명령어 실습

---

## CISCO 스위치 및 라우터의 CLI 모드

- **User Mode** 
    - ">" 표시가 있는 모드를 유저 모드라 한다. (ex. Router >)
    - 현재 장비의 상태 및 테스트를 수행하는 모드이다.
    - Ping, Trace 와 같은 작업을 수행할 수 있다. 
    - 하지만, 라우터의 구성파일을 보거나 수정은 할 수 없다.(네트워크 관리자가 아닌 일반 유저가 구성을 변경하면 안되기 때문)
- **Privileged Mode**
    - "#" 표시가 있는 모드를 프리빌리지 모드라 한다. (ex. Router #)
    - User Mode에서 사용이 불가 했던 장비의 구성을 보거나 수정할 수 있다. 
    - 즉, 사용할 수 있는 명령어의 제약이 없는 모드다.
    - Privileged Mode로 진입하기 위해선 User Mode에서 `enable` 을 통해 진입한다. 반대로 다시 User Mode로 변경하기 위해선 `disable`을 통해 변경
- **Config Mode**
    - "(config) #" 표시가 있는 모드를 컨피그 모드라 한다. (ex. Router (config) #)
    - 장비의 구성 파일을 변경하는 경우에 사용하는 모드
    - User Mode에서는 진입할 수 없으며 Privileged Mode에서만 진입 가능
    - `config terminal` 명령어로 진입하며 나올 때는 `exit` 또는 `ctrl + Z`로 나온다.
- **Setup Mode**
    - 장비를 처음 구매해서 파워를 킨 경우
    - 징비에 구성파일이 없는경우 라우터가 부팅하면서 자동으로 들어가는 모드
- **RXBOOT**
    - 장비의 패스워드를 모르는 경우나 장비의 이미지 파일(IOS)에 문제가 생긴경우 복구를 위한 모드 
    - ROMMON 모드라고도 부른다.
- 정리

|User Mode||Privileged Mode||Config Mode|
|-|-|-|-|-|
|Router>|→|Router#|→|Router(config) #|
||enable||config terminal||
|Router>|←|Router#|←|Router(config) #|
||disable||exit(ctrl+Z)||


## 스위치 명령어 실습

### User Mode에서 Privileged Mode로 변환
```
Switch> enable
```
![enable](/assets/images/2021-08-02/Net_enable.PNG){: .align-center}

### Privileged Mode에서 Config Mode로 변환
```
Switch# config terminal
```
![config termianl](/assets/images/2021-08-02/Net_config_terminal.PNG){: .align-center}

### 스위치 구성, 각 포트들의 현재 상황 확인
```
Switch #show interface status
```
![interface_status](/assets/images/2021-08-02/Net_show_interface_status.PNG){: .align-center}

- Status
    - 각 포트의 연결 상태 
    - 상단의 이미지에서는 아무것도 연결안한 상태이기 때문에 notconnect 표시
- vlan
    - Default Vlan은 1
- Duplex
    - 통신방식 
    - Half Duplex는 한번에 한쪽에서만 전송
    - Full Duplex는 양방향 전송
    - Auto의 경우 상대 편의 산태에 따라 맞추겠다는 의미
- Speed
    - 10Mbps, 100Mbps 모두 가능하지만 Auto이므로 상대방에 맞추겠다는 의미

### Switch에 IP주소 할당하기
- Switch는 Router와 달리 반드시 IP를 할당해야 하는 것은 아니다. 
- 하지만, Switch의 관리를 위해 나중에 Telnet 등으로 사용하기 위해
- Router의 경우 각 인터페이스(이더넷, 시리얼 등)별로 모두 다른 네트워크 이기 때문에 각각 IP를 부여한다.
- 반면 Switch는 네트워크를 나눌 수 없으므로(Broadcast Domain)모든 포트가 같은 네트워크 이기 때문에 대표로 1개의 IP주소를 할당한다.
- Cisco의 Catalyst 2950 스위치의 경우 IP 주소를 부여하기 위해 VLAN 인터페이스에서 배정한다.
- IP 주소를 할당하기 위해선 Switch의 구성을 바꾸는 것이기 때문에 Config Mode에서 수행한다.

```
Switch(config) # interface vlan 1       // Config Mode에서 vlan 인터페이스모드로 들어가는 명령
Switch(config-if) # ip address 192.168.100.1 255.255.255.0
Switch(config-if) # exit
Switch(config) # ip default-gateway 192.168.1.1
Switch(config) # exit
Switch #
Switch # show interface vlan 1
```

![Switch_IP](/assets/images/2021-08-02/Net_switch_ip.PNG){: .align-center}

- `interface vlan 1` 명령을 통해 구성모드에서 vlan 인터페이스 모드로 들어간다.
- `ip address 192.168.100.1 255.255.255.0` 명령을 통해 ip 주소를 192.168.100.1로 세팅하고 서브넷 마스크는 255.255.255.0로 세팅한다. 
- 스위치에 IP 주소만 할당한다고 스위치가 통신을 할 수 없다. 외부 네트워크로 통신하기 위해서는 Default Gateway를 설정해 주어야 한다(Router Interface Ip)
- `ip default-gateway 192.168.1.1` 예시로 192.168.1.1의 IP interface를 가진 Router를 Default Gateway로 설정한다.
- `show interface vlan` 명령어를 통해 변경한 구성을 확인할 수 있다.
- ip Address 설정은 vlan 인터페이스 모드에서, default gateway 설정은 구성 모드에서 수행한다.

### Switch Port 별 Speed, Duplex 설정하기

```
Switch # config terminal
Switch(config) # iterface fastethernet 0/1
Switch(config-if) # speed ?
Switch(config-if) # speed 10
Switch(config-if) # duplex ?
Switch(config-if) # duplex half
Switch(config-if) # exit
Switch(config) # exit
Switch # show interface fastethernet 0/1
```

![Speed, Duplex](/assets/images/2021-08-02/Net_switch_speed_duplex.PNG){: .align-center}

### Switch MAC Address Table 확인하기

- Cisco 스위치가 MAC Address 저장하는 방식
    - Dynamic 방식
        - 자동으로 MAC Address를 배워 Collision Domain을 나눠줌
        - 자동으로 aging 하며 MAC Address를 갱신한다.
    - Permanent 방식 
        - 네트워크 관리자가 수동으로 직접 MAC Address 입력
        - 네트워크 관리자가 수동으로 삭제하기 이전까지는 지워지지 않는다.

- MAC Address Table 확인하기
```
Switch # show mac-address-table
```
![mac address table](/assets/images/2021-08-02/Net_mac_address_table.PNG){: .align-center}

- Permanent 방식으로 수동으로 MAC Address Table에 추가
    - 일반 config 모드에서 수행된다.
    ```
    Switch(config) # mac-address-table static mac-addr vlan-id interface iterface-id
    ---
    Switch(config) # mac-address-table static aaaa.aaaa.aaaa vlan 1 interface fastEthernet 0/4
    ```
    - 결과

    ![mac address static](/assets/images/2021-08-02/Net_mac_address_static.PNG){: .align-center}

