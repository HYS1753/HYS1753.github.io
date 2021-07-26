---
author_profile: true
date: 2021-07-00
title: "AWS Lambda"
categories: 
    - AWS
tag: 
    - Lambda function

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Lambda (람다)

---

AWS의 Lambda 서비스는 서버리스 컴퓨팅 서비스중 하나 입니다.

서비리스 컴퓨팅이란 물리적인 서버가 없는 것이 아닌 추상계층으로 서버를 서비스 제공자가 제공하기 때문에 사용자 입장에서 서버를 관리할 필요가 없는 완전 관리형 서비스 입니다.

AWS Lambda는 "개발자가 오직 소스코드 로직에만 집중 할 수 있을까?", "서버의 유휴자원을 어떻게 하면 최소화 할 수 있을까?"하는 생각에서 부터 시작된 서비스로 2018 re:Invent 행사에서 Lambda및 다양한 런타임을 선보였습니다.

## Lambda의 특징

- **완전관리형 서비스** 
    - 하드웨어, 네트워크는 물론 운영체제까지 개발자가 파알할 필요 없이 알아서 관리하는 서비스.(프로비저닝 할 필요 없음)

- **유연한 확장성** 
    - Lambda는 다른 AWS 서비스들을 호출하여 자신만의 서비스 만들기 가능

- **고가용성**
    - AWS의 여러 가용영역을 활용해 99.9999%의 가용성을 자랑

- **유휴 용량 없음**
    - 항상 켜져 있어야 하는 서버와 달리, Lambda는 요청이 올 때만 프로비저닝되어 작동하기 때문에 응답이 없을때는 청구되는 비용이 없다.

- **병렬 처리에 강함**


## AWS 컴퓨팅 서비스의 종류

![Lambda 1](/assets/images/AWS71.png){: .align-center}

## Lambda Function의 Flow

![Lambda 2](/assets/images/AWS72.png){: .align-center}

## Lambda Function 생성하기

![Lambda 3](/assets/images/AWS73.png){: .align-center}
- 상단의 Lmambda 데시보드의 함수 탭에서 함수 생성을 클릭해 람다 함수를 설정해 줍니다.

![Lambda 4](/assets/images/AWS74.png){: .align-center}
```    
- 새로 작성: 말 그대로 처음부터 작성하는 것으로 최소한의 형태의 함수만 제공

- 블루프린트 사용: AWS에서 제공하는 시작 템플릿 하용(AWS 서비스간 연동 위주)

- 리포지토리: AWS와 관련된 파트너 및 개발사가 제공하는 람다 함수 사용
```

![Lambda 5](/assets/images/AWS75.png){: .align-center}
```
- runtime: 컴퓨터 프로그램이 실행되고 있는 동안의 동작을 의미. 실행 동작 환경(JAVA, Python, Node.js)

- 역할: Lambda 함수에서 사용할 권한(타 AWS 서비스 및 트리거를 위함)
```

​- 설정을 완료하면 기본적으로 제공하는 최소한의 형태의 함수를 볼 수 있습니다.
![Lambda 6](/assets/images/AWS76.png){: .align-center}
`Lambda Design`

![Lambda 7](/assets/images/AWS77.png){: .align-center}
`Lambda Function code`

![Lambda 8](/assets/images/AWS78.png){: .align-center}
`Lambda 환경변수 및 기타 설정`