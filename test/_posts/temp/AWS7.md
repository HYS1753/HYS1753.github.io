---
author_profile: true
date: 2021-07-00
title: "AWS Cloud Infra"
categories: 
    - AWS
tag: 
    - Cloud Infra

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Amazon AWS Cloud Infra(클라우드 인프라)

---

AWS는 전세계 다양한 지역에 데이터 센터(Resion)를 운영중이며 안정적으로 서비스를 제공하기 위해 가용영역(AZ)를 통해 서비스를 합니다.


AWS에서는 "AWS 글로벌 클라우드 인프라는 지구와 지그 밖의 모든 위치에서 사용할 수 있는 가장 안전하고 광범위하며 안정적인 클라우드 컴퓨팅 환경입니다. 클릭 한 번으로 전 세계 모든 위치에 애플리케이션 워크로드를 배포하거나 한 자릿수 밀리초의 지연 시간으로 최종 사용자에게 더 가까운 위치에 특정 애플리케이션을 배포해야 하는 경우 언제 어디서나 필요할 때 AWS의 글로벌 인프라를 사용할 수 있습니다." 라고 설명하고 있습니다.

​![Cloud Infra 1](/assets/images/AWS48.png){: .align-center}

- 현재 AWS 22개의 지리적 Resion내에 69개의 가용영역을 운영하고 있으며 추가적으로 5개의 Resion과 16개의 가용영역을 추가할 계획이라고 합니다.(2020.1.27일 기준)

​![Cloud Infra 2](/assets/images/AWS49.png){: .align-center}

- 한국 NorthEast-2(seoul)Resion은 2016년부터 서비스가 시작되었습니다.

​![Cloud Infra 3](/assets/images/AWS50.png){: .align-center}

​
- AWS의 클라우드 인프라를 구축함으로서 위와 같은 보안, 가용성, 성능, 국제적 입지, 확장성, 유연성 등에서 이점을 가진다고 설명을 하고 있습니다. 즉 간단히 AWS의 클라우드 인프라의 종류에 대해 정리하면 아래와 같습니다.
    - **Resion(리전)**
        - 지역 데이터 센터로 물리적으로 떨어진 데이터 센터를 구축함으로 글로벌 시장에서의 지연 감소 및 속도를 증가 할 수 있습니다.
    - **AZ(가용영역)**
        - 일종의 백업 센터로, 여러개의 AZ가 모여 1개의 Resion을 형성합니다. 자연재해 같은 상황으로 Resion이 사용불가해 졌을 경우 AZ를 통해 서비스를 합니다. 즉, 가용성을 높이기 위한 영역입니다.
    - **Edge Location(엣지 로케이션)**
        - CDN(Content Delivery Network)을 이루는 캐시서버로, 데이터 속도 개선을 위한 서비스입니다.

​

더 자세한 AWS의 클라우드 인프라에 대한 설명은 [AWS 소개](aws.amazon.com)를 확인해 주시기 바랍니다.