---
author_profile: true
date: 2021-08-19
title: "Network Layer 10 - IPv6"
categories: 
    - Network
tag: 
    - 네트워크 계층
    - IPv6

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# IPv6

---

## IPv4 와 IPv6의 차이점

---

- IPv4 대비 확장된 주소 공간과 주소 배정의 단순화
    - IPv4에서는 이를 해결하기 위해 NAT, CIDR(Classless Inter Domain Routing - 기존 ip 클래스 무시하고 사용)등의 다양한 솔루션을 도출했었음.
- IPv4 보다 효율적인 이고 간단한 Header
- 다양한 IPv4와의 호환 옵션
- 보안 기능 지원, 모바일 IP 지원
- IPv4는 32bit, IPv6는 128bit

### IPv4 대비 확장된 주소공간

- IPv4는 32bit로 구성된 반면 IPv6는 128bit로 구성되어 있어 IPv4 대비 매우 많은 양의 주소공간을 가질 수 있다.

### Global Reachability

- IPv6의 많은 주소 공간을 활용해 전세계의 네트워크에서 고유의 주소를 가지고 네트워크에 참여할 수 있다는 것. (End to End Reachability)
- 이를 통해 어플리케이션적 보안적 측면에서 효과적이다.

### 주소의 계층화(Addressing Hierarchy)

- IPv6의 주소에서 비트 단위로 대륙, ISP, 기관 등으로 구분짓는 것
- 이러한 계층화는 주소를 알아보기 쉽게 한다.
- 여러개의 트래픽을 묶어서 전송해 전체적인 네트워크의 트래픽을 줄일 수 있다.
- IPv4의 Supernetting 처럼 여러개의 라우팅 경로를 하나로 묶을 수 있게된다.

### Aggregation

- 주소의 계층화를 통해 Prefix Aggregation이 가능하다.
- 이를 통해 라우팅 테이블의 크기를 줄일 수 있을 뿐만 아니라 효과적이고 빠른 라우팅이 가능하다.

### Stateless Auto Configuration

- 기존의 IPv4의경우 자동으로 IP를 할당받기 위해 DHCP(Stateful Auto Configuration)을 통해 IP를 할당받았다.
- 하지만 IPv6의 경우 특정 DHCP서버가 없더라도 라우터에서 자동으로 IP 구성이 가능하다.
- 라우터에서 Prefix(IPv6의 앞의 64bit)을 받고 뒤에 자신의 MAC 주소를 붙여 IP를 만든다.

### BroadCast 사용 안함

- IPv6에서는 브로트캐스트를 사용안하고 멀티캐스트를 사용해 트래픽을 감소시킨다.
- IPv6에서는 4bit의 Scope ID를 통해 멀티캐스트 전송 영역을 지정해 전송하게 된다.

### 간단해진 Header 정보

- IPv4에서 사용되던 헤더 정보를 간소화하고 정보를 없애 패킷의 처리 속도를 높였다.
- 주소길이가 4배로 늘어난 만큼 주소 확인에 있어 느려질 수 있다.

### 보안

- IPv6에서는 IPSec이 Default가 되는 등 다양한 보안 옵션이 추가되었다.

### Mobility

- 고정된 장비보다 이동성 있는 장비의 활용이 늘어나면서 이에 대한 지원을 추가함.

## IPv6 주소 형태
---

1. IPv4는 10진수로 표현했지만, IPv6는 16진수로 표현한다.
2. IPv4는 `.`을 사용해 8bit씩 4자리로 구현했다면, IPv6는 `:`을 사용해 16bit씩 8자리로 구현한다.(XXXX:XXXX:XXXX:XXXX:XXXX:XXXX:XXXX:XXXX)
3. IPv6의 16진수는 대소문자 구분이 없다.
4. 각 자리 별로 앞에 나오는 0은 생략 가능 (ex. 010f:0000:0001 → 10f:0:1)
5. `::`으로 연속되는 0000을 생략할 수 있다. (ex. 0001:0000:0000:0001 →1::1), 또한, `::`은 표현할 때 딱 한번만 사용가능하다.