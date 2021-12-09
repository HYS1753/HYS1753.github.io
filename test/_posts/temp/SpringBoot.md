---
author_profile: true
date: 2021-08-20
title: "SpringBoot(SpringToolSuite4) 설치"
categories: 
    - Spring
tag: 
    - Java
    - Spring
    - SpringBoot

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# SpringBoot(SpringToolSuite4) 설치

---

## 1. Spring Boot(STS4) 설치 및 기타 환경설정
![STS4](https://github.com/spring-projects/sts4/wiki/images/sts4-big.gif)
- Springtoolsuite4 설치
  - [STS 다운로드](https://github.com/spring-projects/sts4/wiki/Previous-Versions)
  - 윈도우 기준 win32.x86_64.self-extracting.jar 선택
  - 압축해제(ex. java -jar spring-tool-suite-4-4.10.0.RELEASE-e4.19.0-win32.win32.x86_64.self-extracting.jar)
- 필요 시 다음 과정 추가
  - jdk 버전 고정
    - [jdk 다운로드](https://www.oracle.com/kr/java/technologies/javase/jdk11-archive-downloads.html)
    - sts-4.xx.x.RELEASE/SpringToolSuite4.ini 파일의 `--vm`부분 다음과 같이 수정
    - Before : `plugins/org.eclipse.justj.openjdk.hotspot.jre.full.win32.../jre/bin`
    - After : `jdk경로/bin/javaw.exe`
  - lombok.jar 추가
    - [lombok.jar 다운로드](https://projectlombok.org/download)
    - 다운받은 lombok.jar sts-4.xx.x.RELEASE 디렉터리 아래로 이동
    - SpringToolSuite4.ini 파일에 다음과 같이 설정 추가
    - `-javaagent:sts-4.10.0.RELEASE경로/lombok.jar`
  - Code Template 사용시 user 정보 추가
    - -vmargs 아래 다음과 같이 설정 추가
    - `Duser.name=user_name`
  - gradle 다운로드
    - [gradle 다운로드](https://gradle.org/releases/)에서 원하는 complete 버전 다운로드
  - STS Workspace 설정
    - STS를 한번 실행하고 나면 `sts-4.10.0.RELEASE경로/configuration/org.eclipse.ui.ide.prefs`이름의 파일 생성되어 있는데 이를 다음과 같이 변경
    ```
    MAX_RECENT_WORKSPACES=10
    RECENT_WORKSPACES=                      # 사용할 workspace 경로
    RECENT_WORKSPACES_PROTOCOL=3
    SHOW_RECENT_WORKSPACES=true             # True: STS시작시 과거 workplace 기록표시
    SHOW_WORKSPACE_SELECTION_DIALOG=true    # True: STS시작시 workspace 선택
    eclipse.preferences.version=1
    ```

- STS 설정
  - Character encoding set 설정
    - Window - Preferences
      - General
        - Content Types - Content Type Box 내 Java Class File 선택 - Default Encoding을 `UTF-8`로 수정.
        - Workspace - Text file encoding - other 선택(UTF-8)
        - Editors - Text Editors - Spelling - Encoding 부분 Defualt(UTF-8) 선택
      - XML
        - XML Files - Encoding(ISO 10646/Unicode(UTF-8)) 설정
      - Gradle
        - Gradle distribution - Local installation directory에 다운받은 Gradle 경로 설정
      - Java
        - Code-Style -CodeTemplate 원하는 주석 생성(옵션)
