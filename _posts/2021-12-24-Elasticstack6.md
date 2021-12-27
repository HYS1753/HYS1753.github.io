---
author_profile: true
date: 2021-12-24
title: "Elastic Stack 6 - beats"
categories: 
    - Elastic Stack
tag: 
    - beats

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Beats(비츠)

---

## 1. 비츠란?

- 비츠는 가볍고 사용하기 쉬운 데이터 수집기 이다. 
- 고 언어로 작성된 경량 프로그램이기 때문에 로그 수집을 원하는 시스템에 큰 부담을 주지 않으며, 로그 스태시, 엘라스틱 서치와 연계해 다양한 시스템 이벤트 수집할 수 있다.
- 비츠는 오픈소스 라이선스를 따르며 립비츠(libbeat) 라는 프레임워크를 제공해 사용자가 직접 필요한 비트를 구할 수 있다.
- 비츠는 수집한 데이터를 엘라스틱으로 실어 나르기 위해 가장 앞에 위치한 SW로 다음과 같은 엘라스틱에서 공식으로 지원하는 비트들이 있다. 
  - Filebeat : 로그 파일 수집
    - 로그 파일을 실시간으로 읽어들여 전송한다. 로그스태시나 엘라스틱서치에 데이터 전달 시 부하 방지 기능을 지원한다. 가장 많이 사용되는 비트 중 하나.
  - Metricbeat : 메트릭 수집
    - 운영체제를 포함하여 서비스로부터 주기적으로 통계 지표를 수집한다. 
    - 시스템의 CPU, Memory 같은 정보나 DB, Proxy 같은 특정 서비스의 통계 데이터도 수집할 수 있다.
  - Packetbeat : 네트워크 데이터 수집
    - 네트워크 패킷 분석기로 네트워크 데이터를 수집한다.
    - 트래픽, 응답시간, 사용자 패턴 분석과 같은 작업을 할 때 사용된다.
  - winlogbeat : 윈도우 이벤트 로그 수집
  - auditbeat : 감사 데이터 수집
    - 리눅스의 시스템 보안 정보를 감사하는 auditd에서 생성하는 로그를 수집한다. 
    - 사용자가 사전에 정의한 규칙에 따르지 않는 로그들을 식별하여 계정 미준수나 보안 정책에 위배되는 동작을 식별 할 수 있다.
  - heartbeat : 가동 시간 모니터링 데이터 수집
    - 서비스가 살아 있는지 확인하고 모니터링한다.
    - ping, HTTP 서비스 체크 등 서비스상태를 파악하기 위한 여러 방법들을 지원하며 cron 스캐줄 표현식을 기반으로 주기적으로 서비스 활성화 여부 판단
  - functionbeat : 서버리스 데이터 수집
  - kafkabeat : 카프카 토픽 데이터 수집
  - nginxbeat : 엔진엑스 상태 수집
  - mysqlbeat : MySQL 상태 수집
  - journalbeat : 리눅스 Systemd로 실행되는 서비스 들에 대한 로그를 수집한다.
  - custombeat : 오픈소스 커뮤니티에서 만든 비츠이다.

- 비트는 가벼운 프로그앰을 지향하기 때문에 하나의 목적만을 수행한다. 
- 데이터를 수집하는 역할만 본다면 로그스태시와 유사해 보일 수 있지만, 로그스태시는 다양한 플러그인을 포함해 범용성이 높은 만큼 무겁게 동작하지만, 비츠는 범용성을 포기하고 특정 목적만 수행하도록 가볍게 구성되어 App 성능에 영향을 미치지 않고 필요한 이벤트를 수집할 수 있다.


## 2. 비츠 설치

- 엘라스틱서치에서 제공하는 비츠와 사용자가 직접 만든 비츠의 설치 방법은 살짝 다르다.

### 2.1 엘라스틱 서치에서 제공하는 비츠 설치

- [엘라스틱 비츠 다운로드](https://www.elastic.co/kr/downloads/beats/)
- 원하는 비츠 다운로드 
- 동작 순서
  - 비트를 다운로드 한다
  - 비트 설정 파일을 수정한다.
  - 엘라스틱서치와 키바나 대시보드를 사용할 수 있도록 설정한다.
  - 비트를 시작한다.
  - 키바나 대시보드에서 데이터를 확인한다.

## 3. FileBeat

### 3.1 파일 비트 구성 요소
  
  |구성요소|내용|
  |-|-|
  |input|설정파일에서 하베스터에 대한 입력 소스를 정한다. 파일 비트는 하나 혹은 여러개의 입력을 받을 수 있다.|
  |harvester|입력에 명시된 파일을 직접 수집하는 주체이다. 파일은 하나의 하베스터를 가지며 하베스터는 파일을 한 줄 씩 읽고 내보내는 역할을 한다. 또한 파일을 열고 닫는 역활도 한다. 하베스터가 실행되는 동안에는파일 디스트럽터가 열려 있다.|
  |spooler|하베스터가 수집한 이벤트를 엘라스틱서치나 로그스태시와 같은 장소로 전달한다.|

### 3.2 다운로드 및 내부 구조
- [파일비트 다운로드](https://www.elastic.co/kr/downloads/beats/filebeat)
- 압축 파일 구조
  - `filebeat.exe` : 실행파일
  - `filebeat.yml` : 설정파일
  - `module` : 사용가능한 모듈 있는 디렉터리

### 3.3 파일 비트 실행

- filebeat.yml 이라는 설정파일이 있고 해당 파일을 가지고 파일 비트를 수정한다.
- 서정파일 내에서 설정 가능한 모든 파라미터는 [온라인문서](https://www.elastic.co/guide/en/beats/filebeat/7.10/configuring-howto-filebeat.html)
- 파일비트 설정파일 작성 방식이 로그스태시와 비슷해 보이지만 플러그인을 통해 입력 /필터/출력을 설정하는 로그스태시와 달리 파일비트는 복잡한 설정이 없다.
- 설정파일 예시

    ```
    filebeat.inputs:
    - type: log
      enabled: true
      paths:
        - D:\dev-tools\ElasticStack\elasticsearch-7.10.1\logs\*.log

    setup.kibana:
      host: "localhost:5601"

    output.elasticsearch:
      hosts: ["localhost:9200"]
    ```

- 파일 비트 인풋 타입

    |인풋 타입|설명|
    |-|-|
    |log|가장 기본이 되는 타입으로 파일 시스템의 지정한 경로에서 로그 파일을 읽어들인다.|
    |container|도커 같은 컨테이너의 로그를 수집하기 위한 입력으로, 파일을 읽어들인다는 점에서 log와 비슷하다.|
    |s3|log 타입과 유사하나 아마존 웹 서비스의 S3 버킷에 위치한 파일을 읽어들인다.|
    |kafka|다른 타입들과는 다르게 파일을 읽어들이는 대신 파프카의 토픽을 읽어들인다.|

    - 인풋은 타입에 따라 설정해야 하는 내용이 달라지는데. 인풋으로 설정할 수 모든 타입은 온라인 문서르르 참조해 작성한다.
    - 위의 예제에서는 인풋 타입을 log로 설정했는데 log는 paths에 지정된 파일의 로그를 한 줄씩 가져오는 것을 의미한다.

- 파일 비트 아웃풋 타입

    |아웃풋 타입|설명|
    |elasticsearch|가장 많이 사용되는 타입으로 수집한 이벤트를 엘라스틱 서치로 직접 인덱싱 한다.|
    |logstash|다수의 비츠를 사용해 엘라스틱 서치로 전송되는 인덱싱 리퀘스트의 양이 많거나 비츠나 인제스트 노드 수준에서 처리하기 어려운 가공 작업이 필요할 때 별도의 로그스태시를 구축한 후 수집한 이벤트를 전송한다. 다수의 인덱싱 요청이 로그스태시에서 단일 벌크 리퀘스트로 묶여 인덱싱 효율의 개선을 기대할 수 있다. 이때 전송한 이벤트는 로그스태시의 beats 인풋을 이용해 입력받을 수 있다.|
    |kafka|파일 비트에서 1차적으로 수집한 이벤트를 카프카로 전송한다. 카프카는 좀 더 안정적인 수집 파이프라인을 구성할 때 신뢰할 만한 중간 저장소/큐 이므로 수집 중에 장애 발생 시 데이터 손실을 최소화하기 위한 방안으로 활용된다. 최종적으로 엘라스틱 서치의 인덱싱을 원할 경우 이후 다시 파일 비트의 카프카 인풋을 사용하거나 로그 스태시의 카프카 인풋을 이용해 입력할 수 있다.|
    |console|수집한 이벤트를 시스템 콘솔에 출력한다. 일반적으로 수집이 정상적으로 이뤄지는 지 입력 설정을 테스트하기 위한 목적으로 사용된다.|

- 파일 비트 실행

    - 파일비트를 실행하기전에 setup 옵션을 실행한다.
    - `.\filebeat.exe setup -e` 실행
    - `-e` 옵션은 모니터에 오류나 로그를 보여주는 옵션
    - 엘라스틱 서치 인덱스 템플릿, 수명주기, 정책과 같은 인덱스 관리 정보와 인제스트 파이프라인, 그리고 키바나 샘플 대시보드까지 설치하기 때문에 setup하는데 시간이 걸린다. 
    - `loaded ingest pipielines` 까지 출력이 된다면 셋업이 완료된 것.

    ```
    ...
    2021-12-22T11:28:04.087+0900    INFO    [index-management]      idxmgmt/std.go:298      Loaded index template.
    2021-12-22T11:28:05.594+0900    INFO    [index-management]      idxmgmt/std.go:309      Write alias successfully generated.
    Index setup finished.
    Loading dashboards (Kibana must be running and reachable)
    2021-12-22T11:28:05.596+0900    INFO    kibana/client.go:119    Kibana url: http://localhost:5601
    2021-12-22T11:28:06.629+0900    INFO    kibana/client.go:119    Kibana url: http://localhost:5601
    ...
    2021-12-22T11:29:18.418+0900    INFO    [esclientleg]   eslegclient/connection.go:314   Attempting to connect to Elasticsearch version 7.10.1
    Loaded Ingest pipelines
    ```

    - 완료 후 키바나의 dashboard로 가면 다음과 같이 샘플 대시보드들이 생성된 것을 확인할 수 있다.

    ![filebeat setup 이후 생성된 샘플](/assets/images/2021-12-24/Elasticstack-beats1.PNG)


    - setup이 완료 되면 `.\filebeat.exe -e`를 통해 파일 비트를 실행시킨다.
    - 이후 키바나의 Management->Stack Management -> Index Managemet 에 들어가면 현재 클러스터의 인덱스를확인할 수 있는데 filebeat-* 이라는 인덱스를 확인할 수 있다.

    ![filebeat index](/assets/images/2021-12-24/Elasticstack-beats2.PNG)

### 3.4 FileBeat 설정

- filebeat 디렉터리 아래 filebeat.reference.yml 파일이 있는데 해당 파일은 파일 비트의 모든 설정에 대한 설명과 기본값이 적혀 있는 참조 파일이다. 

#### 3.4.1 ignore_older(새로운 파일 탐색시 최근 파일만 읽어오는 설정)

- ignore_older 옵션은 인풋 타입이 log인 경우에 사용할 수 있는 옵션이다.
- 값으로는 10h, 10m 과 같이 타임스트링 형식으로 작성한다. 
  ```
  filebeat.inputs:
    - type: log
      enabled: true
      paths:
        - D:\dev-tools\ElasticStack\elasticsearch-7.10.1\logs\*.log
      ignore_older: 12h
  ```
- 기본 값은 0 으로 특정 값을 입력하지 않으면 파일의 생성/수정과 무관하게 모든 내용을 읽어들인다.
- 예시 처럼 12h 로 되어 있으면 최근 12시간 전의 로그만 수집하겠다는 옵션이다.

#### 3.4.2 include_lines(특정 라인이나 파일을 추가/제외하는 옵션)

- 파일 비트 수준에서 복잡한 정제 작업을 수행할 수는 없지만 간단한 정제 작업을 비츠에서 처리하면 엘라스틱 서치나 로그스태시에서 처리하는 작업량을 줄일 수 있다. 
- include_lines는 특정 라인을 정규식 표현식을 이용해 필터링하고 매칭된 라인은 비츠에서 수집하지 않는다. 
  ```
  filebeat.inputs:
    - type: log
      enabled: true
      paths:
        - D:\dev-tools\ElasticStack\elasticsearch-7.10.1\logs\*.log
      include_lines: ['^ERR', '^WARN']
      exclude_lines: ['^DBG']
      exclude_files: ['\.gz$']
  ```
  - 위의 예시에서는 DBG로 시작하는 로그 라인은 파일 비트 내부에서 버리고, .gz 확장자를 가진 파일을 무시한다.
  - 즉, 특정 폴더 내부에 분석해야 하는 로그 파일과 분석하지 않아도 되는 파일들이 섞여 있을 때 사용한다

#### 3.4.3 multiline(멀티라인 옵션)

- 자바와 같은 로그 패턴은 스택 트레이스 정보를 가지고 있기 때문에 실제로 하나의 오류가 멀티 라인으로 표현된다.(여러줄로 표현됨)
- 멀티 라인을 하나의 라인처럼 처리하는 방법을 `코덱`을 통해 해 보았는데 용어만 다를 뿐 사용법은 비슷하다.
- 멀티라인 옵션은 인풋타입이 log 인경우에만 사용이 가능하다 
- multiline 설정에는 pattern, negate, match라는 세가지 하위 옵션이 있으며 이 세가지 설정을 조합해 읽어들인 줄이 로그의 끝인지 다음줄을 계속 읽는지 결정한다.
- 하위 옵션
  - multiline.pattern 
    - 정규식을 이용해 패턴을 지정한다.
    - 패턴과 일치하는 라인이 나타나면 멀티라인으로 인식한다. 
    - `multiline.pattern: '^[[:spae:]]'` 와 같이 작성하게 되면 라인의 첫번째 문자가 공백이면 멀티라인 패턴으로 인식한다는 것이다.
  - multiline.negate
    - 해당 옵션이 true일 떄 패턴의 일치 조건을 반전시킨다. 
    - 즉 true가 위의 pattern과 함꼐 쓰인다면 공백이 아닐때 멀티라인으로 인식하는 것이다. 
  - multiline.match
    - 멀티라인을 처리하는 방법으로 before와 after를 지정할 수 있다.


### 3.5 모듈

- 모듈은 많이 사용되고 잘 알려진 시스템 데이터를 수집하기 위한 일반적인 설정을 사전에 정의해 둔 것이다. 
- 모듈을 사용하면 복잡한 가공이 필요한 이벤트인 경우에도 최소한의 비츠 설정으로 손쉽게 로그들을 수집할 수 있다.
- 파일 비트의 경우 다음과 같은 모듈들을 지원한다.
    |모듈|설명|
    |-|-|
    |aws|AWS의 CloudWatch, CloudTrail과 같은 서비스에서 발생하는 로그들을 수집할 수 있다.|
    |cef|시스로그를 통해 CEF(Common Event Format) 이벤트르르 입력받을 수 있다.|
    |cisco|시스코사의 ASA, Nexus 등 네트워크 장비에서 발생되는 이벤트를 수집한다.|
    |elasticsearch|엘라스틱서치의 클러스터, GC, 감시로그 등을 수집할 때 사용한다.|
    |googlecloud|구글 클라우드 플랫폼의 VPC 플로우, 방화벽로그 등을 수집할 수 있다.|
    |logstash|로그스태시에서 발생한 로그들을 수집할 수 있다|

- 모듈 작동 순서
  - 비트를 다운로드 한다
  - 비스 설정 파일을 수정한다.
  - 모듈을 활성화하고 모듈 설정파일을 수정한다.
  - 엘라스틸서치와 키바나 대시보드르르 사용할 수 있게 설정한다.
  - 비트르르 시작한다
  - 키바나 대시보드에서 데이터를 확인한다.

- 비츠 설정 파일 수정
  - 인풋은 모듈 설정 파일에서 설정하기 때문에 아웃풋만 기존 `filebeat.yml`에 작성한다.
    ```
    output.elasticsearch:
      hosts: ["localhost:9200"]
    
    setup.kibana:
      host: "localhost:5601"

    filebeat.config.modules:
      path: ${path.config}/modules.d/*.yml
    ```
    - `${path.config}` 는 파일 비트가 설치된 경로 
    - 파일 비트 경로 변수
      - path.home : 파일비트 설치 경로 
      - path.config : 파일비트 구성 파일 경로
      - path.data : 파일 비트 데이터 경로 (/data)
      - path.logs : 파일비트 로그 경로(/logs)

- 모듈 활성화
  - cmd에서 `.\filebeat.exe modules enable logstash kibana elasticsearch`
  - 위와 같이 적어주면 하단에 설정되었다고 나오게 된다. (ex. Enable logstash)
  - 또한 `.\filebeat.ext modules list`  와 같이 설정하면 설정된 모듈 리스트 확인 가능
  - 

- 모듈 설정 파일 수정
  - 모듈 설정 파일은 파일비트 디렉터리 아래에 modules.d 라는 디렉터라 아래 있다. 
  - 비활성화된 모듈은 disabled확장자가 붙어있고 확성화되면 사라진다.
  - logstash 모듈은 일반 로그와 스로우 로그 수집을 지원한다. 
  - 로그스태시 실행 중 발생된 대부분의 로그는 일반로그에 속하며 파이프라인 내에서 과도하게 처리 시간이 지연되는 이벤트의 경우 별도의 파일에 슬로우 로그로 기록된다.
  - /modules.d/logstash.yml  설정
    ```
    # Module: logstash
    # Docs: https://www.elastic.co/guide/en/beats/filebeat/7.10/filebeat-module-logstash.html

    - module: logstash
    # logs
    log:
        enabled: true

        # Set custom paths for the log files. If left empty,
        # Filebeat will choose the paths depending on your OS.
        var.paths: [D:\dev-tools\ElasticStack\logstash-7.1-.1\logs\*.log]

    # Slow logs
    slowlog:
        enabled: true
        # Set custom paths for the log files. If left empty,
        # Filebeat will choose the paths depending on your OS.
        #var.paths:
    ```
  - 이후 setup 명령을 통해 filebeat를 설정하고 실행한다.
  - 다음 로그스테시를 실행시키면 키바나의 [Filebeat Logstash] Logstash Logs ECS 에 들어가서 확인해 볼 수 있다.


## 4. 모니터링

- 모니터링은 비트가 데이터 입출력을 잘하고 있는지 동작중인 호스트의 리소스들은 문제 없는지 등을 엘라스틱서치와 키바나를 통해 확인할 수 있는 기능이다.
- filebeat.yml 파일 비트 설정 파일에서 모니터링 설정을 추가한다. 
    ```
    monitoring.enabled: true
    monitoring.elasticsearch:
      hosts: ["localhost:9200"]
    ```

