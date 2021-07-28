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


- Network Layer의 기능
    - **라우팅(Routing)** 
        - 송,수신 호스트 사이의 패킷 전달 경로를 최적의 경로로 선택하는 기능
    - **혼잡 제어**
        - 혼잡이란 네트워크 내에 패킷 수가 과도하게 증가하는 현상이다.
        - 이러한 혼잡이 발생하면 네트워크 전체의 전송 속도가 급격히 떨어지게 된다.
        - 따라서 네트워크 계층에선 이러한 혼잡을 제어한다.
        - 혼잡의 원인
            1. 기본적으로 네트워크의 처리 능력보다 지나치게 많은 패킷이 입력되면 혼잡 발생
		    2. 라우터 내부의 버퍼 용량이 부족 심화되면 패킷 더 이상 저장 불가 초래
		    3. 송신 호스트는 타임아웃 동작으로 패킷 재전송, 네트워크 내 패킷수 증가
		    4. 버퍼 용량을 늘리면 패킷 전송 지연도 증가, 잃어버리는 패킷을 없을지 몰라도 출력이 적으므로
		    5. 패킷 전송 지연 시간이 송신 호스트가 설정한 타임아웃 시간보다 크면 패킷 재전송 과정 증가
		    6. 네트워크 패킷양 증가와, 중복패킷 수신 현상 증가. 결과적으로 네트워크 혼잡도 증가. 악순환
		    7. 초기 전송 지연이 점점 증가할 때 타임 아웃 시간이 작으면 혼잡도가 급격히 증가
		    8. 패킷 분실 처리 하면 재전송 현상 발생해 복잡도 증가
		    9. 수신패킷에 대해 즉각 응답하는 방식을 수신 패킷 모두에게 응답패킷 발생, 의도적 피기배킹 사용하면(데이터에 응답메시지 같이 보냄) 응답 시간이 느려져 타임아웃 증가
		    10. 패킷 생존시간 작게 하면 타임아웃 증가(재전송 증가), 너무크게 하면 네트워크 불필요한 부하
        - 흐름제어와 혼잡제어의 차이
            1. 흐름제어 : 송수신 호스트 사이에 논리적인 점재점 전송속도의 차이를 다룬다.
            2. 혼잡제어 : 서브넷에서 네트워크 전송 문제를 다룬다.
        - ECN(Explicit Congestion Notification) 패킷 사용
            1. 라우터는 트래픽 양을 모니터해 출력 선로의 사용정도가 한계치를 초과하면 주의 표시(ECN 패킷)
            2. ECN 패킷을 전달받은 송신 호스트는 정해진 비율에 따라 송신 패킷량 줄인다.
            3. 임의의 시간 경과 후에도 ECN 패킷이 지속적으로 들어오면 추가로 송신 패킷량 줄임
            4. 즉, ECN 패킷을 통해 라우터에서 데이터 량을 조절한다.
    - **패킷의 분할과 병합**
        - IP 헤더를 제외한 패킷의 전송 데이커의 크기는 380 byte이다.
        - 전송계층에서 전달 받은 데이터가 너무 크면 여러개의 패킷으로 나누어 전송하게 되는데 이를 분할과 병합이라 한다.
        - IPv4의 Identification, Flag(DF, MF),Fragment Offset 필드를 통해 구분되어진다.
        - `Identification`: 분할한 패킷에 동일한 구분자를 부여해 수신호스트가 다시 패킷을 병합할 수 있도록 한다.
        - `Flag` : 패킷의 분할 및 병합의 사용여부를 표시한다.
            1. DF : 값을 1로 하면 패킷 분할을 막아, 병합기능이 없는 수신호스트 일떄 사용한다. 중간에 처리 가능한 크기보다 크면 나머지는 버린다.
            2. MF : 해당 필드가 1이면 분할 패킷이 뒤에 계속 있음을 알림, 마지막 분할 패킷은 0 표시
        - `Fragment Offset(분할 오프셋)` 
            1. 분할 전 원래 패킷의 데이터에 위치하는 상대 주소값을 저장한다. 
            2. 저장할 떄는 8Byte 단위로 저장한다.
            3. ex. 저장된 값이 64이면 64x8=512 번째에 위치한 분할된 패킷이다.
    
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

TCP/IP 프로토콜을 만들 때 이 프로토콜을 사용하는 모든 장비를 구분하기 위해 만들어진 주소가 IP주소이다. 


IP 주소는 이진수 32bit로 구성되어 있으며, 이진수 8자리마다 점을 찍어 4개의 옥텟(Octet)으로 구분하고 주로 각 옥텟을 십진수로 표현한다.


보통 라우터에는 두개 이상의 IP주소를 할당한다. 한개는 Serial 인터페이스용으로 외부 인터넷에 연결되는 것으로 ISP(Internet service Provider)에 문의해 IP주소를 할당 받는다. 나머지는 Ethernet 인터페이스로 네트워크에 할당된 IP 주소로 할당한다.


IP 주소는 Network Part(네트워크 부분)과 Host Part(호스트 부분)으로 나뉘어져 있다. 하나의 네트워크란 하나의 Broadcast 영역을 말하며, 뒤의 호스트 부분은 각 PC가 다 달라야 한다.


- IP 주소의 Class
    - A, B, C, D, E로 구성되어 있으며 이를 토앻 어디까지가 네트워크 부분이고 어디까지가 호스트 부분인지 알 수 있다.
    ![IP Class](/assets/images/NET_IP_Class.PNG){: .align-center}
    
        |Class A|1|7|24|
        |-|-|-|-|
        ||0|Network #|Host #|

        |Class B|1|1|14|16|
        |-|-|-|-|-|
        ||1|0|Network #|Host #|


        |Class C|1|1|1|21|8|
        |-|-|-|-|-|-|
        ||1|1|0|Network #|Host #|
    
    - Class A : 네트워크 번호가 1 ~ 126으로 시작하고, 한 네트워크 내에 들어갈 수 있는 호스트 수는 16,777214
    - Class B : 네트워크 번호가 128.1 ~ 191.255으로 시작하고, 한 네트워크 내에 들어갈 수 있는 호스트 수는 65,534
    - Class C : 네트워크 번호가 192.0.0 ~ 223.255.255으로 시작하고, 한 네트워크 내에 들어갈 수 있는 호스트 수는 254
    - Class D : 네트워크 번호가 224 ~ 239으로 시작하고, 멀티캐스트용 주소이다.
    - Class E : 네트워크 번호가 240 ~ 255으로 시작하고, 연구용 주소이다.