---
author_profile: true
date: 2021-08-20
title: "Elastic Stack - logstash"
categories: 
    - Elastic Stack
tag: 
    - logstash

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Logstash(로그스태시)

---

## 1. 로그스태시란?

- 로그스태시는 플러그인 기반의 오픈소스데이터 처리 파이프라인 도구이다.
- 데이터 전처리 과정을 별도의 어플리케이션 작성 없이 비교적 간단한 설정만으로 수행할 수 있다.
- 데이터를 저장하기 전에 사용자가 원하는 형태로 변경할 수 있는 강력한 기능을 제공한다.
- 장애 대응 로직이나, 성능 저하 요인으르 쉽게 파악할 수 있는 모니터링 API 간단한 조정으로 성능을 튜닝할 수 있는 파라미터들도 제공한다.

|데이터 수집|데이터 가공 및 변환|데이터 저장|분석 및 시각화|
|-|-|-|-|
|비츠, 데이터|로그스태시|엘라스틱 서치|키바나|

### 1.1 특징
- 플러그인 기반
  - 로그스태시의 파이프라인을 구성한느 각 요소들은 전부 플러그인 형태로 만들어져 있다.
  - 기본으로 제공되는 플러그인을 제외하고도 수많은 커뮤니티 플러그인을 찾을 수 있고, 뿐만 아니라 비교적 간단한 코드로 전용 플러그인을 작성해 로그 스태시의 다른 장점을 가져가면서 고유한 로직을 포함시킬 수도 있다.
- 모든 형태의 데이터 처리
  - JSON, XML과 같은 구조화된 텍스트 뿐만 아니라 다양한 형태의 데이터를 입력받아 가공한 다음 저장할 수 있다.
  - 특히, 이벤트 데이터(시간에 따라 발생하는 데이터)를 처리하는데 최적화 되어 있다. 
  - 성능
    - 자체적으로 내장되어 있는 메모리와 파일 기반의 큐르르 사용하므로 처리속도와 안정성이 넢다 또한 인덱싱할 도큐먼트의 수와 용량을 종합거으로 고려해 벌크 인덱싱을 수행할 뿐만 아니라 파이프라인 배치 크기 조정을 통해 병목 현상을 방지하고 성능을 최적화 할 수 있다.
  - 안정성
    - 엘라스틱서치의 장애 상황에 대응하기 위한 재시도 로직이나 오류가 발생한 도큐먼트를 따로 보관하는데드 레터 큐 를 내장하고 있다. 
    - 파일 기반의 큐를 사용할 경우 띃하지 않은 로그스태시의 장애상황에서도 도큐먼트 유실을 최소화 할 수 있다.

### 1.2 로그스태시 설치

- [Logstash 공식 홈페이지](https://www.elastic.co/kr/logstash/)
- Logstash 설치 전 JDK가 설지 되어 있어야 함. [JDK 설치](https://www.oracle.com/java/technologies/downloads/)
- [Logstash 과거 버전 다운로드](https://www.elastic.co/kr/downloads/past-releases#logstash) 에서 7.10.1 버전 다운로드 
- 로그스태시 실행
  - `로그스태시 저장 경로\bin\logstash.bat -e "input { stdin {} } output { stdout {} }"`
  - 로그스태시를 실행하기 위해서는 반드시 파이프라인 설정이 필요하다.
  - 일반적으로 파이프라인은 따로 설정파일을 만들어서 기록하거나 config 폴더의 pipeliines.yml에 기록하는데 -e 옵션을 사용하게 되면 콘솔에서 직접 파이프라인을 설정할 수 있다.
  - 위의 예시는 운영체제의 표준 입력으로 전달 받은 메시지를 다시 표준 출력으로 표시하는 것.
  
  ```
    D:\dev-tools\ElasticStack\logstash-7.10.1\bin>.\logstash.bat -e "input { stdin {} } output { stdout {} }" --log.level error
    Using JAVA_HOME defined java: C:\Program Files\Java\jdk1.8.0_301
    WARNING, using JAVA_HOME while Logstash distribution comes with a bundled JDK
    Sending Logstash logs to D:/dev-tools/ElasticStack/logstash-7.10.1/logs which is now configured via log4j2.properties
    The stdin plugin is now waiting for input:
    hello world
    {
        "@version" => "1",
            "host" => "DESKTOP-USER",
        "message" => "hello world\r",
        "@timestamp" => 2021-12-13T08:21:14.883Z
    }
  ```
  - logstash를 실행하고 hello world를 입력하게 되면 다음과 같은 결과를 얻을 수 있다.
  - 위의 `--log.level error`는 로그 중 error 레벨 미만의 로그는 감추는 옵션이다.
  - ctrl+c를 통해 종료 가능
  - 로그 레벨

    |로그레벨|설명|
    |-|-|
    |fatal|시스템 동작을 멈출 정도의 심각한 오류 발생 시 나타나는 로그|
    |error|시스템 동작을 멈추지는 않지만 오류가 발생했을 시 나타나는 로그|
    |warn|잠재적인 오류를 포함하는 경고성 로그|
    |info|진행 상황이나 상태 변경 등의 정보를 알리기 위한 로그|
    |debug|개발 과정에서 디버깅하기 위한 로그|
    |trace|시스템 진행 과정 추적을 위한 로그|


## 2. 파이프라인(Pipeline)

- 파이프라인은  데이터를 입력받아 실시간으로 변경하고 이를 다른 시스템에 전달하는 역할을 하는 로그스태시의 핵심 기능이다.
- 파이프라인은 입력, 필터, 출력 이라는 세가지 구성요소로 이루어 진다.
- 이중 입력, 출력은 필수 요소이고 필터는 옵션 요소이다.
- 데이터 소스 → 로그스테시 파이프라인(입력 → 필터 → 출력) → 엘라스틱서치
- 로그스태시는 JSON 형태로 데이터를 출력하는데 @version, @timestamp는 로그스태시가 만든 필드로 사용자가 만든 필드와 충돌 날 것을 대비해 앞에 @기호가 붙어 있다. 
- message, host 필드는 데이터와 시스템 사용자를 나타낸다.
- 즉, @ 붙은 필드는 로그스태시가 만든 필드, 없는 필드는 수집하여 얻은 필드라고 생각하면 된다.

### 2.1 파이프라인의 기본 템플릿
```
input {
    {입력 플러그인}
}

filter {
    {필터 플러그인}
}

output {
    {출력 플러그인}
}
```
- 용도나 형태에 맞춰 이미 만들어진 수많은 플러그인이 있기 때문에 필요한 기능을 지원하는 플러그인을 검색해 템플릿에 추가하면 된다. 

### 2.2 입력(input)

- 파이프라인의 가장 앞부분에 위치하며 소스 원본으로 부터 데이터를 입력받는 단계
- 직접 대상에 접근해 읽어들이는 경우도 있지만 서버를 열어놓고 받아드리는 형태의 구성도 가능하다.
- 파일, 통계, 웹, DB, 스트림 → `로그스태시` → 분석, 보관, `엘라스틱서치`, 모니터링, 알림
- 자주 사용하는 입력 플러그인 

    |입력 플러그인|설명|
    |-|-|
    |file|리눅스의 tail -f 명령처럼 파일을 스트리밍하여 이벤트를 읽어 드린다.|
    |syslog|네트워크를 통해 전달되는 Syslog를 수신한다.|
    |kafka|카프카와 토픽에서 데이터를 읽어들인다.|
    |jdbc|JDBC 드라이버로 지정한 일정마다 쿼리르르 실행해 결과를 읽어들인다.|

- 실습
  - logstash가 설치된 디렉터리의 config 디렉터리아래 logstash-test.conf 파일을 만든다.
  해당 파일은 굳이 .conf가 아니여도 가능하나 소스관리를 위해 .conf로 한다.
  - .conf 파일에 다음과 같은 pipeline 작성
  
  ```
  input {
      file {
          path => "elasticsearch-7.10.1(엘라스틱서치  위치)/logs/elasticsearch.log"
          start_position => "beginning"
      }
  }

  output {
      stdout { }
  }
  ```

  - 입력으로는 file 플러그인을 사용하며 출력으로는 표준 출력 플러그인을 사용했다.
  - 파일 플러그인에는 여러 옵션이 있는데 path는 읽어들일 파일의 위치를 결정한다.
  - 해당 파일에 로그가 쌓이면 실시간으로 해당 파일의 변경을 감지해 읽어들인다.
  - start_position은 최초 파일을 발견했을 때 파일을 읽을 위치로 파일의 시작 위치부터 읽어들일지 끝부분부터 새로운 라인만 읽어들일지 정할 수 있따.
  - `로그스태시위치\bin\logstash.bat -f .\config\logstash-test.conf`로 실행
  - `-f` 옵션은 설정파일이나 폴더를 지정하는 옵션으로 상단에 만든 .conf 파일을 설정에 사용한다.
  
  ```
    {
        "@version" => "1",
            "path" => "D:/dev-tools/ElasticStack/elasticsearch-7.10.1/logs/elasticsearch.log",
        "message" => "[2021-12-13T09:31:08,275][INFO ][o.e.p.PluginsService     ] [DESKTOP-USER] loaded module [percolator]\r",
        "@timestamp" => 2021-12-13T08:56:07.384Z,
            "host" => "DESKTOP-USER"
    }
  ```

  - 위와 같이 출력되는 것을 확인할 수 있으며, 이중 message에 적힌 구문을 분석해 의미있는 데이터로 변환하는 것이 중요한 로그스태시의 업무아다.


p230 6.3.2 필터 부터 다시 시작.