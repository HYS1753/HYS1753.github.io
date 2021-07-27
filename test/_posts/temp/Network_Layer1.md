---
author_profile: true
date: 2021-07-00
title: "Network Layer 1 - 네트워크 계층의 기능"
categories: 
    - Network
tag: 
    - 네트워크 계층

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# 네트워크 계층(Network Layer)

---

OSI 7 Layer의 3계층으로 Packet Forwarding 과 네트워크 간 라우터를 통한 Routing을 수행하는 계층이다.

IP(Internet Protocol)주소를 사용하여 통신하는 계층적인 구조를 가지고 있다. 

- 통신 단위 : 패킷(Packet)
**IPv4 헤더의 구조**
![IPv4 헤더 구조](/assets/images/NET_IPv4.PNG){: .align-center}
    - Version : IP 버전, IPv4
	- Header Length(HLEN) : 헤더의길이, 4바이트(위 그림의 한줄) 단위 최소 5(20바이트) ~ 15(60바이트)
	- Type of Service : 서비스 품질
	- Total Packet Length :　IP 패킷 전체의 길이 바이트 단위 최대 65,535
	- Identifier, Flags, Offset : IP Fragment 필드로 단편화와 재조합, 큰패킷이 작은 패킷으로 전송되는 경우
	- Time to live : IP 패킷의 수명
	- Protocol ID : 데이터에 포함되어 있는 상위계층의 프로토콜 정보, TCP 6, UDP 17
	- Header Checksum : 오류검출
	- Source IP Address, Destination IP Address : 출발지와 목적지의 IP 주소
	- IP Header Options & Padding: 옵션, 거의 사용되지 않음 시험/디버깅 용도, 통신에는 미관여
- 대표 장비 : 라우터

## IP 주소

- TCP/IP 프로토콜을 만들 때 이 프로토콜을 사용하는 모든 장비를 구분하기 위해 만들어진 주소가 IP주소이다. 
- IP 주소는 이진수 32bit로 구성되어 있으며, 이진수 8자리마다 점을 찍어 4개의 옥텟(Octet)으로 구분하고 주로 각 옥텟을 십진수로 표현한다.
- 보통 라우터에는 두개 이상의 IP주소를 할당한다. 한개는 Serial 인터페이스용으로 외부 인터넷에 연결되는 것으로 ISP(Internet service Provider)에 문의해 IP주소를 할당 받는다. 나머지는 Ethernet 인터페이스로 네트워크에 할당된 IP 주소로 할당한다.
- 