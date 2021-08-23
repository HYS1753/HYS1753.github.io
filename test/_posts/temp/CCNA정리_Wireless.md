---
author_profile: true
date: 2021-07-00
title: "CCNA 정리 4 - Wireless"
categories: 
    - CCNA
tag: 
    - WIFI의 암호화
    - WLC
    - WLAN Controller GUI

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Wireless(무선)
---


## WIFI의 암호화

- WFA(Wi-Fi Alliance)에서 정의한 보호 엑세스로 WPA(Wi- Fi Protected Access)가 있다.
- WPA3는 WPA2에서 발전한 형태
    - WPA3 Personal : PSK(사전공유키) 대신 SAE(Simultaneous Authentication of Equals)를 사용해 오프라인 사전 공격, 키 복구 및 메시지 위조와 같은 공격에 맞설 수 있음
    - WPA3 Enterprise : 강력한 인증 및 Link Layer 암호화 방법과 민감함 보안 환경을 위한 선택적인 192비트 보안 모드 

- DSSS(Direct Sequence Spread Spectrum)
    - 원래 무선 신호에 높은 주파수의 확산 코드를 곱(XOR)해 원래 신호의 대역폭을 향상 시키는 것
    - CSMA가 DSSS를 활용한 기술이다.

- 무선 채널에서 만약 동시사용이 필요할 경우 
    - 1, 6, 11 또는 1, 7, 13을 사용한다.

## WLC(Wireless LAN Controler)

- 무선 AP 들을 조인시켜 관리하는 컨트롤 역할을 한다.
- LWAPP(Lightweight Access Point Protocol – 무선 Access Point 제어 프로토콜)을 사용한다.
    - Local Mode : AP create two CAPWAP tunnel to WLC(Management, Data Traffic tunnel)AP에는 WLC와 연결을 통해 네트워크 스위치와 연결함.
    - Flex Mode : Controller와 연결이 끊어져도 AP 작동 가능


## WLAN Controller GUI 

SSID, Profile Name