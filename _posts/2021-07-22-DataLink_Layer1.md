---
author_profile: true
date: 2021-07-21
title: "DataLink Layer 1 - 데이터링크 계층의 기능"
categories: 
    - Network
tag: 
    - 데이터링크 계층
    - 주요 기능
    - Framing
    - 회선제어
    - 흐름제어
    - 오류제어
    - ARP
# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# 데이터링크 계층(DataLink Layer)

---

물리계층에서 송수신 되는 데이터의 오류와 흐름을 관리해 안전한 데이터 전송을 돕는 계층

같은 네트워크(Collision Domain)안에서 발생하는 오류복구, 흐름제어를 수행하며, Mac Address를 통한 통신이 가능하게 해준다.

2 개의 부 계층으로 구성되어 있다.
    1. MAC(Media Access Control) : 물리적인 부분으로 매체간의 연결방식을 제어하고 1계층과 연결하는 역할
    2. LLC(Logical Link Control) : 논리적인 부분으로 Frame을 만들고 3계층과 연결

- 통신 단위 : 프레임(Frame)
**이더넷 프레임의 구조**
![이더넷 프레임 구조](/assets/images/2021-07-21/Ethernet_frame.PNG){: .align-center}
- 대표 장비 : 브리지, 스위치
- MAC 주소 : 48bit(6Byte) 로 6자리 16진수로 표현한다. 앞의 3자리는 OUI(Organization Unique Identifier - 제조사 식별코드), 나머지 3자리는 제조사 내 일련번호
- 주요 프로토콜 : ARP(IP to Mac), STP, VTP(Cisco 전용) 등

---

## 데이터링크 계층의 주요 기능

- **Framing**
    - 데이터그램을 캡슐화하여 프레임 단위로 만들고 헤더와 트레일러를 추가한다.
    - 헤더는 목적지, 출발지 주소, 데이터 내용을 정의한다.
    - 트레일러는 비트 에러를 감지한다.

- **회선제어**
    - 신호간의 충돌이 발생하지 않도록 제어하는 기능
        - ENQ/ACK 방법(1:1)
        - Polling 방법(1:N) 
        Select(송신자가 나머지 수신자들을 선택) 모드
        Poll(수신자에게 데이터 수신여부 확인 후 전송) 모드

- **흐름제어(Flow Control)**
    - 송신자와 수신자의 데이터 처리속도를 해결하기 위한 기능
    - 2계층은 Feedback 방식의 Flow Control을 수행, 3,4계층은 Rate기반의 Flow Control 수행
        - Stop & Wait 방식
            1. Sender가 데이터 송신(Frame)하고 Receiver의 응답을 기다림(Wait)
            2. Receiver가 수신하면 ACK 전송
            3. 만약 Sender가 송신 후 ACK가 수신되지 않으면 일정시간(Time out Interval)이후 재전송한다.
        - Sliding Window 방식
            1. Stop & Wait 방식처럼 하나 보내고 ACK기다리는 방식이 아니라 여러 프레임을 연속으로 전송한다.
            2. ACK 를 수신하면 Window size를 늘리며 전송한다.

- **오류제어(Error Control)**
    - 전송 중에 오류나 손실 발생 시 수신측은 에러를 탐지 및 재전송 요청한다.
    - ARQ(Automatic Repeat Request) : 프레임 손상 시 재전송이 수행되는 과정을 의미
        - Stop & Wait ARQ 방식
            1. Sender가 데이터를 송신(Frame)
            2. Receiver 측으로 ACK 수신하면 다음 프레임 전송
            3. 만약 NAK 수신 시 해당 프레임 재전송
        - Go Back N ARQ(Sliding Window 방식) 방식
            1. Sender가 여러개의 프레임을 전송한다.
            2. Receiver 측에서 만약 2번 프레임에 문제가 발생하면 Sender로 NAK 2으로 응답한다.
            3. Sender는 NAK을 확인하고 2번 프레임을 포함한 나머지를 재전송한다.
        - Selective Repeat ARQ
            1. Go Back N 방식은 하나가 잘못되어도 뒷부분을 전부 버려 비효율적이기 때문에 이를 개선시켜 에러가 발생한 프레임만 선별해 재전송

