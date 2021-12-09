---
author_profile: true
date: 2021-07-00
title: "CCNA 정리 5 - Etc"
categories: 
    - CCNA
tag: 
    - AAA

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# ETC(기타)
---

## Three Tier network topologies

- Core Layer : backborn Network, Fast, Merge 역할
- Distribution Layer 
- Access Layer : Connect to End Detect


## AAA

- Authentication : 사용자가 네트워크 장비, 서비스 접근 허용
- Authorization : 사용자의 접근 권한 인가
- Accouning : 어카운팅. 요금 부과

## ACS(Access Control Server)

- AAA(인증, 권한부여, 계정)프레임워크를 위한 중앙 집중식 관리 시스템을 위해 사용되는 서버가 ACS(엑세스 제어 서버)
- Client와 ACS 간의 통신에는 TACACS+ 와 RADIUS 두가지 프로토콜이 사용된다.

|TACACS+|RADIUS|
|-|-|
|Cisco 독점 프로토콜|표준 프로토콜|
|TCP를 통한 전송|UDP를 통한 전송|
|TCP 49 port|인증, 권한부여 → UDP1812, 계정 → UDP1813 port|
|AAA가 각각 분리 전송|인증 권한 부여는 결합|
|AAA 패킷은 암호화 전송|암호만 암호화해 전송|
|장치 관리에 사용|네트워크 엑세스에 사용|



## SNMP(Simple Network Manage Protocol)

- 시스템이나 네트워크 관리자가 원격으로 Network 장비 모니터링, 환경설정 등을 위한 Protocol
- 대략 20개 정도의 네트워크 백업 가능
- 관리 시스템을 Manager, 관리 대상을 Agent로 표시
- Application 계층 프로토콜으로 UDP로 전송
- MIB : 관리 되어야 할 특정 정보를 객체화 한 집합체
- SMI : MIB 정의를 위한 일반적인 구조
- 보안이 취약함

비슷한 것으로 NETFLOW(네트워크 흐름 모니티링)

## IP-SLA 
스위치 라우터 품질 측정

## CDP(Cisco Discovery Protocol)
DataLink Layer를 통해 주변 장비를 찾으며, Cisco 독점

## LLDP(Link Layer Discovery Protocol)
CDP와 비슷하게 주변을 찾는 프로토콜, 적용 시 lldp run
CDP와 똑같이 Datalink Layer를 통해 찾으며, 표준 프로토콜

## EEM(Embedded Event Protocol)	
IOS의 SW 구성요소로 Router에서 발생하는 Event 추출 분류

## SNMP(Simple Network Management Protocol)	
간단한 네트워크 구성 시 사용

## ARP(Address Resolution Protocol)
IP→Mac주소변환, Dynamic ARP는 수신만 검사, 송신 검사 x

## NETCONF
네트워크 장치 구성을 설치, 삭제 조정 하기 위해 IETF에서 정의한 프로토콜

## NTP(Network Time Protocol)	
네트워크로 컴퓨터 시스템 간 시간 동기화를 위한 프로토콜
Server Only 모드로 만들기 위해선 Master로 설정함.
- Configure an authentication key pair for NTP and specify whether the key will be trusted or untrusted.
(NTP에 대한 인증 키 쌍을 구성하고 키를 신뢰할 수 있는지     아닌지 지정한다.)
- Set the IP address of the NTP server and the public key. (NTP 서버의 IP 주소, 공용키 설정)
- Enable NTP client mode.(NTP Client Mode 활성화)
- Enable NTP authentication. (NTP 인증 사용)
- Verify the NTP configuration (NTP 구성 확인)


## Cloud(클라우드)

- IaaS : os부터 사용자가 설정
- PaaS : Data, Application만 사용자가 설정
- SaaS : Application 까지 모두 Service Provider가 제공

## infra Structure 구성 관리 도구

- Ansible : Python언어로 구성 YAML에서 명령 스크립팅, 
순차적으로 실행되는 SSH를 통해 노드에 Push함
TCP 사용, 클라이언트 온리
- Puppet : Ruby 기반의 오픈소스 도구, 서버 클라이언트
- Chef : 서버 클라이언트

## 추가적인 약어

**VSAT(Very Small Aperture terminal)**
- 소형 안테나 지구 쪽, 대역폭 낮다

**ISDN(Integrated Service Digital Network)**
- 여러 음성, 문자, 화상 등의 통신서비스를 하나의 디지털 통신망으로 종합해 제공하도록 통합한 것 , 대역폭 낮다
  
**WSA(Web security Application)**
- Proxy Caching 성능 향상
- 
**ASA(Adaptive Security Applicaion)**

**ISATAP(Infra-Site Tunnel Addressing Protocol)**
- IPv4 네트워크에서 IPv6 패킷을 전송하기 위한 것

**GRE(Generic Routing Encapsulation)**
- Cisco에서 개발한 터널링 프로토콜으로 새로운 IP 헤더를 Encapsulation해서 패킷을 전송하는 VPN 기능을 제공한다.

**IPSec(Internet Portocol Security)**

**IP SLA(Service level agreement)**
- 스위치 라우터 간 성능 품질 측정
  
**SOHO(Small Office Home Office)**