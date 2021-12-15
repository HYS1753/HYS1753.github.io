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


### 2.3 필터(filter)

- 로그스태시의 필터는 비정형 데이터를 정형화 하고 데이터 분석을 위한 구조를 잡아준다. 
- 로그스태시의 필터를 통해 정형화된 데이터는 엘라스틱서치나 아마존 S3와 같은 스토리지에 전소오디어 분석 등의 용도로 활용된다.
- 자주 사용되는 필터 플러그인

  |필터 플러그인|설명|
  |-|-|
  |grok|grok 패턴을 사용해 메시지를 구조화된 형태로 분석한다. grok패턴은 일반적인 정규식과 유사하나 추가적으로 미리 정의된 패턴이나 필드 이름 설정, 데이터 타입 정의 등을 도와준다.|
  |dissect|간단한 패턴을 사용해 메시지를 구조화된 형태로 분석한다. 정규식을 사용하지 않아 grok에 비해 자유도는 떨어지지만 더 빠른 처리가 가능하다.|
  |mutate|필드명을 변경하거나 문자열 처리 등 일반적인 가공 함수들을 제공한다.|
  |date|문자열을 지정한 패턴의 날자형으로 분석한다.


#### 예제

- 로그 예제
  
```
# filter-ex.log
[2021-11-29 12:21:37] [info] [sy2pg] [select_rows] [START] [tablename: test1, starttime: 12:21:37]
[2021-11-29 12:21:37] [info] [sy2pg] [select_rows] [END] [endtime: 12:21:37, elapseTime: 0.354524850845]
[2021-11-29 12:21:37] [info] [sy2pg] [convert_File] [START] [tablename: test1, starttime: 12:21:37]
[2021-11-29 12:21:37] [info] [sy2pg] [convert_File] [END] [endtime: 12:21:37, elapseTime: 0.000210046768188]
[2021-11-29 12:21:37] [info] [sy2pg] [convert_File] [nothing_to_update] [resultcount: 0]
```

#### 2.3.1 정적 파일 입력 받기

  ```
  input {
    file {
      path => "D:/dev-tools/ElasticStack/logstash-7.10.1/config/filter-ex.log"
      start_position => "beginning"
      sincedb_path => "nul"
    }
  }

  output {
    stdout { }
  }
  ```

  - 로그 예제 파일을 입력으로 받는 파이프라인 설정이다.
  - start_position은 로그 스태시가 새로운 파일을 인식했을 때 파일을 어디서 읽을지 정하는 옵션
    - beginning: 파일의 처음부터 시작
    - end: 파일의 마지막 부터 시작
  - sincedb_path은 sincedb 파일 생성 여부 결정하는 것. `nul`은 파일 만들지 않는 옵션
    - sincedb:
      - sincedb 데이터베이스 파일은 파일을 어디까지 읽었는지 기록하는 파일이다.
      - start_position이 파일을 읽을떄 어디부터 읽을지 정하는데 이는 최초 한번만 적용된다.
      - 즉, 로그스태시 재시작 시 sincedb 데이터베이스 파일을 보고 기록되어 있는 위치 부터 읽는다 만약 없을경우 지정된 start_position에 의해 읽는다.
      - sincedb_path를 따로 입력하지 않으면 엘라스틱은 기본값으로 로그스태시 data/plugins/input/file 위치에 sincedb 데이터베이스 파일을 생성한다.


#### 2.3.2 문자열 자르기

  ```
  #logstash-test.conf
  input {
    file {
      path => "D:/dev-tools/ElasticStack/logstash-7.10.1/config/filter-ex.log"
      start_position => "beginning"
      sincedb_path => "nul"
    }
  }

  filter {
    mutate {
      split => { "message" => " " }
    }
  }

  output {
    stdout { }
  }
  ```

  - mutate 플러그인은 필드를 변형하는 다양한 기능을 제공하고 있다.
  - 필드 이름 변경, 삭제 등의 작업이 가능하다. 
  - mutate 옵션

    |mutate option|설명|
    |-|-|
    |split|쉼표 같은 구분 문자를 기준으로 문자열을 배열로 나눈다.|
    |rename|필드 이름을 바꾼다.|
    |replace|해당 필드값을 특정 값으로 바꾼다.|
    |uppercase|문자를 대문자로 변경한다.|
    |lowercase|문자를 소문자로 변경한다.|
    |join|배열을 쉼표 같은 구분 문자로 연결해 하나의 문자열로 합친다.|
    |gsub|정규식이 일치하는 항목을 다른 문자열로 대체한다.|
    |merge|특정 필드를 다른 필드에 포함시킨다.|
    |ceorce|null인 필드에 기본값을 넣어준다.|
    |strip|필드 값의 좌우 공백을 제거한다.|

  - mutate는 많은 옵션이 있어서 순서가 중요하다.
    - coerce → rename → update → replace → convert → gsub → uppercase → capitalize → lowercase → strip → remove → split → join → merge → copy 
  - 로그스태시 실행 결과
  
    ```
    D:\dev-tools\ElasticStack\logstash-7.10.1>.\bin\logstash.bat -f .\config\logstash-test.conf --log.level error
    Using JAVA_HOME defined java: C:\Program Files\Java\jdk1.8.0_301
    WARNING, using JAVA_HOME while Logstash distribution comes with a bundled JDK
    Sending Logstash logs to D:/dev-tools/ElasticStack/logstash-7.10.1/logs which is now configured via log4j2.properties
    {
          "@version" => "1",
              "host" => "DESKTOP-USER",
        "@timestamp" => 2021-12-14T00:57:51.567Z,
              "path" => "D:/dev-tools/ElasticStack/logstash-7.10.1/config/filter-ex.log",
          "message" => [
            [0] "[2021-11-29",
            [1] "12:21:37]",
            [2] "[info]",
            [3] "[sy2pg]",
            [4] "[select_rows]",
            [5] "[START]",
            [6] "[tablename:",
            [7] "test1,",
            [8] "starttime:",
            [9] "12:21:37]"
        ]
    }
    ...
    ```

    - 다음 결과와 같이 message 필드 문자열 공백을 기준으로 구분되어 배열 형태의 데이터가 된다.
    - 구분된 문자들은 `필드명[숫자]` 와 같이 접근할 수 있다.

  - 필터 플러그인 공통 옵션
    - logstash-test.conf의 필터 부분에 다은과 같이 추가하면 다음과 같은 결과가 나오게 된다.
      - 필터 플러그인은 순서대로 동작하는데 split에 의해 message 필ㄷ의 문자열이 공백을 기준으로 분리되고, id 필드를 추가하고 필드 값은 message[2]를 사용한다. 마지막으로 message 부분을 지운다.

      ```
      # 필터 부분 추가
      filter {
        mutate {
          split => { "message" => " " }
          add_field => { "id" => "%{[message][2]}" }
          remove_field => "message"
        }
      }

      ==== 결과 ====
      {
                "path" => "D:/dev-tools/ElasticStack/logstash-7.10.1/config/filter-ex.log",
            "@version" => "1",
          "@timestamp" => 2021-12-14T01:05:29.971Z,
                "host" => "DESKTOP-USER",
                  "id" => "[info]"
      }
      ```

    - 필터 플러그인 공통 옵션

      |공통 옵션|설명|
      |-|-|
      |add_field|새로운 필드를 추가할 수 있다.|
      |add_tag|성공한 이벤트에 태그를 추가할 수 있다.|
      |enable_metric|메트릭 로깅을 활성화하거나 비활성화 가능, 기본적으로 활성화되어 있으며, 수집한 데이터는 로그 스태시 모니터링에서 해당 필터의 성능을 분석할 때 사용한다.|
      |id|플러그인 아이디를 설정한다. 모니터링 시 아이디를 이용해 특정 플러그인을 쉽게 찾을 수 있다.|
      |remove_filed|필드를 삭제할 수 있다.|
      |remove_tag|성공한 이벤트에 붙은 태그를 제거할 수 있다.|


#### 2.3.3 문자열 파싱(dissert)

- 앞서 mutate 플러그인의 split 옵션을 이용해 문자열을 분리하면 하나의 구분자만 이용해서 데이터를 나눠야 한다는 단점이 있다. dissect 플러그인은 패턴을 사용해 문자열을 분석하고 주요 정보를 필드로 추출하는 기능을 한다.
- logstash_test.conf 의 filter부분을 다음과 같이 수정한다.
  
  ```
  filter {
    dissect {
      mapping => {"message" => "[%{timestamp}] [%{level}] [%{class}] [%{method}] [%{process}] [%{message}]"}
    }
  }
  ```

  - 결과

  ```
  {
        "message" => "tablename: test1, starttime: 12:21:37",
      "timestamp" => "2021-11-29 12:21:37",
        "@version" => "1",
          "method" => "select_rows",
      "@timestamp" => 2021-12-14T01:26:08.085Z,
        "process" => "START",
          "level" => "info",
            "path" => "D:/dev-tools/ElasticStack/logstash-7.10.1/config/filter-ex.log",
          "class" => "sy2pg",
            "host" => "DESKTOP-USER"
  }
  ```

  - dissect 플러그인의 mapping 옵션에 구분자 형태를 정의하고 필드를 구분한다. 
  - `%{필드명}`으로 작성하면 중괄호 안의 필드명으로 새로운 필드가 만들어 진다. 
  - `%{}` 외의 문자들은 모두 구분자 역할을 한다. 예를들면 다음과 같다.
    - log : [2021-11-29 12:21:37]^[info]^[sy2pg]^[select_rows] -> ^ 는 띄어쓰기
    - 구분자 : [%{timestamp}] [%{level}] [%{class}] [%{method}]

- dissert 플러그인에 사용할 수 있는 필드 기호들 
  - `->` : `%{필드명->}`으로 작성 시 공백이 몇 칸이던 하나의 공백으로 인식한다.
  - `%{?필드명}`, `%{}` : 이렇게 작성한 필드명은 결과에 포함되지 않는다.
    - `%{?->}` 다음과 같이 적으면 공백을 하나의 필드로 만든 다음 무시하는 의미가 된다.
  - `%{+필드명}` : 여러개의 필드를 하나의 필드로 합쳐서 표현 ex. IP, Port 합치기

#### 2.3.4 문자열 파싱(grok)

- grok은 정규식을 이용해 문자열을 파싱할 수 있따. 
- 정규 표현식은 특정한 규칙을 갖는 문자열을 표현하는 언어이다.
- 패턴을 이용해 %{패턴:필드명} 형태로 데이터에서 특정 필드를 파싱할 수 있다.
- grok 패턴

  |패턴명|설명|
  |-|-|
  |NUMBER|십진수를 인식한다. 부호와 소수점을 포함할 수 있다.|
  |SPACE|스페이스, 탭 등 하나 이상의 공백을 인식한다.|
  |URI|URI르르 인식한다. 프로토콜, 인증정보, 호스트, 경로, 파라미터를 포함할 수 있다.|
  |IP|IP 주소를 인식한다. IPv4, IPv6 모두 인식 가능|
  |SYSLOGBASE|시스로그의 일반적인 포맷에서 타임스탬프, 중요도, 호스트, 프로세스정보까지 메시지 외의 헤더 부분을 인식한다.|
  |TIMESTAMP_ISO8601|ISO8601 포멧의 타임스탬프를 인식한다. 0000-00-00T12:00:00+09:00와 같은 형태이며 타임존까지 정확한 정보를 기록하고 로그에선 많이 쓰이는 날짜 포멧이기에 grok 표현식을 나타낼 떄도 유용하다.|
  |DATA| 이패턴의 직전 패턴부터 다음 패턴 사이를 모두 인식한다. 특별히 인식하고자 하는 값의 유형을 신경쓸 필요가 없으므로 특별히 값이 검증될 필요가 없다면 가장 많이 쓰이는 패턴 중 하나이다.|
  |GREEDYDATA|DATA 타입과 동일하나, 표현식 가장 뒤에 위치시킬 경우 해당 위치부터 이벤트의 끝까지 값으로 인식한다.|

- fliter 예시

  ```
  
