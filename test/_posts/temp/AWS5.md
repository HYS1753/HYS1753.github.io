---
author_profile: true
date: 2021-07-00
title: "AWS LisghtSail"
categories: 
    - AWS
tag: 
    - LightSail
    - 라이트세일

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# LightSail(라이트세일) 구현

---


라이트 세일이란 라이트하게 서버를 사용해야 할 사용자에게 적은 비용으로 사용할 수 있으며, 또한 추후에 더 큰 서버가 필요할 경우 이미 익숙한 환경(서비스 제공자 플랫폼)에 의존하도록 하는 입문용 서비스로 SaaS에 해당합니다. 또한 모든 리소스 등을 라이트 세일이 관리합니다.


라이트 세일은 기본적으로 사용자에게 서버를 제공해 준다는 점에서 AWS에서 제공하는 서비스중 하나인 EC2와 비슷합니다. 하지만 EC2는 사용자가 일일히 대부분의 서버의 성능을 컨트롤 할 수 있어 고사양의 서버가가 필요한 비교적 전문적 지식이 있는 사용자가 사용하기에 적합한 반면, LightSail의 경우 성능은 제한적이지만 누구나 간단하게 서버를 구성 할 수 있다는 점에서 장점이 있습니다.

LightSail과 Wordpress를 사용하여 개인 블로그를 만들어 보는 실습을 통해 자세히 LightSail에 대해 알아보도록 하겠습니다.

## 1. LightSail에 접속하기

- AWS에 root 계정으로 로그인하고 LightSail서비스에 접속하면 다음과 같은 페이지를 볼 수 있습니다.

![LightSail 1](/assets/images/AWS22.png){: .align-center}

## 2. LightSail 인스턴스 생성

- 1번에서 접속한 LightSail 서비스 페이지에서 인스턴스 생성 버튼을 클릭합니다.

![LightSail 2](/assets/images/AWS23.png){: .align-center}

- 생성할 LightSail의 기본설정을 합니다. 인스턴스의 위치는 한국(NorthEast -2)로 선택을 하고, WordPress를 사용하기 위해 플랫폼은 Linux/Unix, 블루프린트는 WordPress를 선택합니다. (LightSail에서는 Wordpress뿐만 아니라 다양한 서비스를 블루프린트로 제공합니다.)

![LightSail 3](/assets/images/AWS24.png){: .align-center}

- LightSail은 서버의 용량 및 성능에 따라 기본 요금이 다릅니다. 사용자가 필요한 용량 및 성능을 확인하고 알맞는 플랜을 선택하면 됩니다.

- 이번에는 AWS에서 첫달 무료로 제공해주는 플랜인 $3.5로 선택을 합니다.

- 인스턴스 설정의 마지막 단계인 리소스의 이름을 입력해 줍니다. 입력을 완료한 후 인스턴스 생성 버튼을 누르면 LightSail인스턴스의 생성은  완료됩니다.

![LightSail 4](/assets/images/AWS25.png){: .align-center}
![LightSail 5](/assets/images/AWS26.png){: .align-center}

- 인스턴스 생성 버튼 클릭하면 설정한 리소스의 이름의 인스턴스가 대기중인 상태로 표시가 됩니다. 
- 대기중은 AWS에서 사용자가 설정한 LightSail 설정 값을 토대로 인스턴스를 생성하고 있는 것이며, 몇 분이 지나면 자동으로 오른쪽 그림과 같이 실행중 표시가 표시되게 됩니다.

## 3. 생성한 LightSail의 Wordpress 접속하기

![LightSail 6](/assets/images/AWS27.png){: .align-center}

- 2번에서 생성한 인스턴스에서 자신의 LightSail IP주소를 확인하고, 이 주소를 인터넷 브라우저에 입력해 접속합니다.

![LightSail 7](/assets/images/AWS28.png){: .align-center}

- 접속이 완료되면 위의 그림과 같이 WordPress 기본 화면이 출력되게 됩니다. 

## 4. WordPress 설정하기

- 3번에서 접속한 Wordpress주소뒤에 /admin을 추가하면 관리자 페이지로 접속할 수 있습니다.( ex. http://15.123.123.123/admin)

![LightSail 8](/assets/images/AWS29.png){: .align-center}

- 접속을 완료하게 되면 위와같은 관리자 로그인 페이지가 출력되게 됩니다. 관리자 ID, PW를 알기위해 다시 LightSail 인스턴스로 돌아가 CLI 버튼을 클릭합니다.

![LightSail 9](/assets/images/AWS30.png){: .align-center}

- CLI 버튼을 클릭하면 위와 같은  Bitnami를 볼 수 있으며 명령어로 cat bitnami_credentials를 입력해 줍니다.

```
cat bitnami_credentials 
```

- 입력하면 관리자 ID와 PW를 확인할 수 있으며 이를 관리자 로그인 페이지에 동일하게 입력하면 관리자로 로그인 할 수 있습니다.

![LightSail 10](/assets/images/AWS31.png){: .align-center}

 - 이제 관리자 페이지를 통해 사용자가 원하는데로 Blog를 꾸밀 수 있으며, 다양한 플러그인을 통해 글쓰기 및 기능을 추가가 가능합니다.

## 5.LightSail 안전한 종료(삭제)

- LightSail은 기본적으로 유료 서비스이기 때문에 사용하지 않고 생성만 해두어도 비용이 청구됩니다. 지속적으로 위의 실습 예제를 사용하지 않을 경우 예제 완료 후 바로 `삭제` 하는것을 추천드립니다.

![LightSail 11](/assets/images/AWS32.png){: .align-center}

- LightSail 인스턴스에서 CLI버튼 좌측의 .3개 버튼을 클릭해 삭제를 진행합니다.

​