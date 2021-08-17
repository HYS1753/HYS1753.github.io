---
author_profile: true
date: 2021-08-16
title: "Network Layer 8 - Access List"
categories: 
    - Network
tag: 
    - 네트워크 계층
    - Access List

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# Access List

---

라우터에서 네트워크의 보안을 위해 사용되는 것이 Access List 이다.

## Access List 종류

**1. Standard Access List**
    - 접근하는 네트워크의 출입통제를 `출발지의 주소`만을 확인하고 결정
**2. Extended Access List**
    - 접근하는 네트워크의 출발지 주소, 목적지 주소, 프로토콜, 포트번호 등 다양한 조건을 확인 후 결정
**3. Dynamic Access List**
    - User Name과 Password를 통해 결정하는 방식

- 만약 Access List에 걸려서 네트워크에 접속하지 못하는 경우 Host Unreachable 표시가 나타나게 된다.

## Access List의 규칙

1. 엑세스 리스트는 윗줄부터 하나씩 차례로 수행된다.
2. 엑세스 리스트는 기본적으로 마지막에 `deny all`이 설정되어 있어 설정되지 않은 것들을 모두 deny 시켜 버린다. 따라서, 필요 시 `permit any` 명령어를 마지막 라인에 적어준다.
3. 엑세스 리트스의 새로운 라인은 항상 맨 마지막 줄에 추가되며, 선택적으로 중간의 값을 삭제 또는 수정할 수 없다.
    - 만약 no access-list로 삭제 하면 모두 지워진다.
4. 인터페이스에 대한 엑세스 리스트가 정의되어 있지않으면 permit any 상태가 된다.

# Standard Access List

---

## Standard Access List 의 순서
- inbound
    1. 패킷이 들어온다.
    2. Access List가 설정되있는가?
        - 되어 있으면 3번으로 이동
        - 안되어 있으면 Permit으로 간주 패킷전달
    3. Access List 안의 출발지 주소와 일치하는가?
        - 일치하면 deny또는 Permit 결정
        - 일치하지 않으면 4번 이동
    4. Access List안의 다음 값으로 검사 3번 이동
        - 만약 마지막이라면 deny또는 Permit 수행

- outbound
    1. 패킷이 들어온다.
    2. 해당하는 인터페이스 확인
    3. 해당하는 인터페이스의 Access List가 있는지 확인
    4. 이후 inbound의 3번과정부터 수행

## Standard Access List 명령어

```
Router(config) # access-list access-list-number {permit|deny} {source [source-wildcard]|any}
```

- access-list-number : 1~99 까지의 숫자 입력가능, 각 종류에 따라 정해져 있음
- `{permit|deny}` : 해당 조건일 경우 permit 할지 deny 할지 결정
- `{source [source-wildcard]|any}` : 출발지 주소와 와잍드카드 마스크를 입력하거나 모든 주소를 할당할 때는 any 설정
- ex. `access-list 2 deny 210.100.150.1`
- ex. `access-list 2 permit 210.100.150.0 0.0.0.255`


```
Router(config-if) # ip access-group acccess-list-number {in|out}
```

- 각 인터페이스에서 access list를 설정하는 방법
- acccess-list-number : access-list 명령으로 정해준 숫자와 일치되어야 한다.
- {in|out} : 인터페이스의 나가는 방향으로 access list를 설정할지 들어오는 방향으로 설정할지 결정.
- ex. `ip access-group 2 out`

```
Route # show ip access-lists
Standard IP access list 2
    deny 210.100.150.1
    permit 210.100.150.1, wildcard bits 0.0.0.255
```

- 설정한 Standard Access List 확인.

## 텔넷 포트에서의 Access List 설정(Access Class)

```
// 기존 라우터에서 access list 설정
access-list 10 permit 210.100.150.0 0.0.0.255
// 텔넷 설정
line vty 0 4
access-class 10 in 
password password
login
```

# Extended Access List

---

## 특징

- Standard의 경우 출발지 주소만 제어하는 반면 Extended는 출발지, 목적지 주소 모두 제어할 수 있다.
- Standard의 경우 전체 TCP/IP에 대한 제어만 하는 반면, Extended는 ip, tcp, udp, icmp 등 각 특정 프로토콜에 대해 제어할 수 있다.
- Standard의 경우 0~99까지의 `Access List Number`로 사용하지만, Extended는 100~199 까지의 `Access List Number`를 사용한다.

## Extended Access List 의 순서

1. 패킷이 들어온다.
2. Access List 가 존재 하는가?
    - 존재하면 3번으로 이동
    - 없으면 permit
3. Access List 안의 출발지 주소와 일치 하는가?
4. Access List 안의 목적지 주소와 일치 하는가?
5. Access List 안의 프로토콜과 일치하는가?
6. Access List 안의 프로토콜 옵션과 일치하는가?
7. Access List 를 적용해 Deny할지 Permit 할지 정함
8. 만약 3~7의 과정에서 불일치하는 것이 하나라도 있으면 다음 Access List 로 3번 부터 다시 수행

## Extended Access List 명령어

```
Router(config) # access-list access-list-number {permit|deny}
protocol source source-wildcard [operator port]
destination destination-wildcard [operator port] [established] [log]
```

- ex. `access-list 101 permit ip 0.0.0.0 255.255.255.255 any`
    - 0.0.0.0 255.255.255.255 는 any와 같은 의미임
- ex. `accedd-list 101 permit ip 210.100.150.0 0.0.0.255 any
- `established` 는 TCP 데이터 그램이 ACK 또는 RST bit 이 Set 되어 들어오는 경우만 match가 발생하게 됨.
    - `access-list 104 permit tcp any 210.100.150.0 0.0.0.255 established` 의 경우 210.100.150.0에서는 외부와 connection을 맺을 수 있지만 외부에서는 connection 불가
    - 즉, 210.100.150.0 네트워크 안의 호스트는 외부와 연결할 수 있지만, 외부에서의 연결을 차단하는 효과가 있다.


```
Router(config-if) # ip access-group access-list-number {in|out}
```

- Standard Access List 와 같이 각 인터페이스에서 access list를 설정하는 방법.