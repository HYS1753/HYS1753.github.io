---
author_profile: true
date: 2021-07-21
title: "Physical Layer"
categories: 
    - Network
tag: 
    - 물리계층
    - UTP, STP, 광케이블
    - 허브(Hub)
# 목차
toc: true  
toc_sticky: true 
# sidebar:
#     nav: "docs"
---

# 물리계층(Physical Layer)

물리계층은 전기적, 기계적, 기능적인 특성을 이용해 통신케이블로 데이터를 전송한다.

물리계층은 데이터를 전달만 할 뿐 이 데이터가 무엇인지, 에러가 있는지 어떻게 전송하는지와 같은 추가적인 처리를 하지 않는다.

- 통신 단위 : bit
- 대표 장비 : 통신케이블(UTP), 리피터, 허브

---

##  통신케이블

- **UTP(Unshielded Twisted-Pair)** : 물리계층에서 주로 사용하는 대표적인 케이블
    - Direct Cable(T568-B - T568-B) : 서로 다른 장비 간에 연결할 떄 사용(라우터 - 스위치, PC - 스위치)
    - Cross Cable(T568-B - T568-A) : 서로 같은장비 간에 연결할 떄 사용(라우터 - 라우터, 스위치 - 스위치)

    ![Cross-Direct](/assets/images/2021-07-21/crossdirect.PNG){: .align-center}
        - T568-A : 주띠-주-초띠-파-파띠-초-갈띠-갈
        - T568-B : 초띠-초-주띠-파-파띠-주-갈띠-갈
        - 주황색은 수신, 초록색은 송신
        - 따라서 같은 장비간에는 연결방향이 같아 Cross Cable을 사용해 연결방향을 바꿔준다.
        - 다른 장비 간에는 연결방향이 다르기 때문에 Direct Cable을 사용한다.

- **STP(Shieled Twisted-Pair)** : UTP와 달리 케이블 주위를 어떤 절연체로 감싸 EMI를 줄여 성능이 좋은 케이블, UTP에 비해 비싸다는 단점이 있으며 이미 UTP로 구성된 네트워크가 많아 STP 보다 UTP가 주로 사용
- **광케이블** : 광섬유를 사용한 케이블


## 허브(Hub)

- 멀티포트 리키터라고도 말할수 있으며 말 그대로 들어온 데이터를 그대로 재전송해주는 장비
- 즉, 한 포트로 들어온 데이터를 연결된 나머지 모든 포트로 재전송 시켜주는 역할을 한다.
- 이더넷을 사용하는 허브는 CSMA/CD(Carrier Sense Multiple Access/Collsion Detection)의 적용을 받기 때문에 같은 허브에 연결되어 있느느 모든 PC는 같은 "Collision Domain"안에 있다고 하며 한 PC가 통신중일 떄는 다른 PC는 통신할 수 없는 문제점이 생긴다.
- 이러한 허브의 문제점을 해결하는 방법은 Collision Domain을 나누는 방식으로 해결할 수 있는데 이를 위한 장비는 DataLink Layer의 브리지(Bridge)와 스위치(Switch)가 있다.