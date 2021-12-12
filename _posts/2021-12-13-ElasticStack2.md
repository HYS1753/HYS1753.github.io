---
author_profile: true
date: 2021-12-13
title: "Elastic Stack 2 - Elastic Search 기본"
categories: 
    - Elastic Stack
tag: 
    - Elastic Search

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# 엘라스틱 서치 기본

---

## 1. 키바나 콘솔 사용법

키바나에서 Dev Tools에서 REST API를 호출 할 수 있도록 제공해 준다. (왼쪽 상단 토클 메뉴 -> Mannagement -> Dev Tools)

- 시스템 상태 확인
  - 엘라스틱 서치의 현재 상태를 빠르게 확인할 수 있는 방법으로 일반적으로 cat(compact and aligned text) API를 사용한다.
  - GET _cat 요청 하면 다음과 같이 cat API가 지우너하는 목록을 확인할 수 있다.
    ```
    =^.^=
    /_cat/allocation
    /_cat/shards
    /_cat/shards/{index}
    /_cat/master
    /_cat/nodes
    /_cat/tasks
    /_cat/indices
    /_cat/indices/{index}
    /_cat/segments
    /_cat/segments/{index}
    /_cat/count
    /_cat/count/{index}
    /_cat/recovery
    /_cat/recovery/{index}
    /_cat/health
    /_cat/pending_tasks
    /_cat/aliases
    /_cat/aliases/{alias}
    /_cat/thread_pool
    /_cat/thread_pool/{thread_pools}
    /_cat/plugins
    /_cat/fielddata
    /_cat/fielddata/{fields}
    /_cat/nodeattrs
    /_cat/repositories
    /_cat/snapshots/{repository}
    /_cat/templates
    /_cat/ml/anomaly_detectors
    /_cat/ml/anomaly_detectors/{job_id}
    /_cat/ml/trained_models
    /_cat/ml/trained_models/{model_id}
    /_cat/ml/datafeeds
    /_cat/ml/datafeeds/{datafeed_id}
    /_cat/ml/data_frame/analytics
    /_cat/ml/data_frame/analytics/{id}
    /_cat/transforms
    /_cat/transforms/{transform_id}
    ```
  - cat API를 통해 노드, 샤드, 템플릿등의 상태 정보나 통계 정보를 확ㅇ니할 수 있다.
  - GET _cat/indices?v 요청
    - ? 기호 뒤에는 몇가지 파라미터를 사용할 수 있다.
      - V : 컬럼의 이름(헤더)
      - s : 정렬
      - h : 헤더
    ```
    health status index                           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
    green  open   .apm-custom-link                SYbt-VpnRHeztqghD_YI2g   1   0          0            0       208b           208b
    green  open   .kibana_task_manager_1          6HSZvjvIS-qlq66t7OA3rg   1   0          5           93     39.3kb         39.3kb
    green  open   .apm-agent-configuration        hV8-fN87TjqOs-6Rfncepw   1   0          0            0       208b           208b
    green  open   .kibana-event-log-7.10.1-000001 rttbgx2uSWeRX6EqAY9UlA   1   0          1            0      5.6kb          5.6kb
    green  open   .kibana_1                       sbiDLQiuRLSKHJ0TGKJ0Ww   1   0         22            0      2.1mb          2.1mb
    ```


- 셈플 데이터 받아오기

키바나 홈 화면에서 Try our sample data를 클릭해 원하는 셈플데이터 받아 올 수 있다.


## 2. 인덱스와 도큐먼트

인덱스는 도큐먼트를 저장하는 논리적 구분자이며, 도큐먼트는 실제 데이터를 저장하는 단위이다.

![index, Document, Field 구조](/image/elasticstack/elasticsearch_structure.PNG)

- 일반적으로 엘라스틱을 이용해 시스템을 개발하면 하나의 프로젝트에서 하나의 클러스터를 생성한다.
- 클러스터 내부는 데이터 성격에 따라 여러개의 인덱스를 생성한다. 
- 인덱스 내부에는 JSON 형태로 된 다수의 도큐먼트가 존재한다.
- 도큐먼트 안에는 복수의 필드를 갖는다. 


### 2.1 도큐먼트

도큐먼트는 엘라스틱서치에서 데이터가 저장되는 기본 단위로 JSON 형태이며, 하나의 도큐먼트는 어려개의 필드(Field)와 값(Value)를 갖는다.
엘라스틱서치가 도큐먼트에 데이터를 어떻게 저장하는지 알아보고 RDB와의 차이점을 알아본다.

- 데이터 예제

  ```
  name : mike
  age : 25
  gender : male
  ```

- MySQL에서 테이블 생성과 데이터 저장
  ```
  CREATE TABLE member (
    uid int not null AUTO_INCREMENT PRIMARY KEY,
    name varchar(50) not null,
    age int not null,
    gender varchar(6) not null
  );

  INSERT INTO member(name, age, gender) values ("mike", 25, "male");
  ```

- 엘라스틱서치의 데이터 (JSON 형태 저장)
  ```
  {
    "name": "mike",
    "age" : 25,
    "gender" : "male"
  }
  ```
  - name, age, gender를 필드라고 하며 "mike", 25, "male"을 값이라고한다.
  - 아직 mapping을 배우지는 않았지만 엘라스틱 서치 매핑으로 필드들의 데이터 타입을 지정할 수 있는데 name, gender필드는 텍스트 타입, age 필드는 정수타입으로 매핑이 되었다. 
  
- MySQL과 엘라스틱서치 비교

|MySQL|엘라스틱서치|
|-|-|
|테이블|인덱스|
|레코드|도큐먼트|
|컬럼|필드|
|스키마|매핑|

### 2.2 인덱스

- 인덱스는 도큐먼트를 저장하는 논리적 단위로 RDB의 테이블과 유사한 개념이다. 
- 하나의 인덱스에 다수의 도큐먼트가 포함되는 구조인데, 딩일한 인덱스에 있는 도큐먼트는 동일한 스키마를 갖는다 
- 인덱스 이름에는 영어소문자를 비롯해 \,/,*,?,",<,>,|,#.공백, 쉼표 등을 제외한 특수문자 사용가능, 최대 255자까지.


### 2.3 도큐먼트 CRUD

- 인덱스 생성 확인 삭제
  - `PUT index1` : index1이라는 이름의 인덱스를 생성하는 API이다. PUT은 생성이나 수정을 위한 HTTP 메서드이며, index1은 생성하려는 엘라스틱서치 인덱스 이름이다. PUT 메서드 대신, POST 메서드를 사용해도 된다.
  - `GET index1` : 인덱스를 확인하는 API
  - `DELETE index1` : 인덱스 삭제 메서드

- 도큐먼트 생성(INDEXING)
  - 앞서 도큐먼트는 반드시 하나의 인덱스에 포함돼야 한다고 했다. 
  - 엘라스틱 서치에서 도큐먼트르 ㄹ인덱스에 포함시키는 것을 인덱싱이라고 한다.
  ``` 
  ==== 입력 ====
  PUT index2/_doc/1
  {
    "name": "mike",
    "age" : 25,
    "gender" : "male"
  }
  ==== 결과 ====
  {
    "_index" : "index2",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 1,
    "result" : "created",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 0,
    "_primary_term" : 1
  }
  ```
  - index2는 인덱스 이름, _doc은 엔드포인트 구분을 위한 예약어, 숫자 1은 인덱싱 할 도큐먼트의 고유 아이디 이다. 

- 도큐먼트 생성 확인
  ```
  ==== 입력 ====
  GET INDEX2
  ==== 출력 ====
  {
    "index2" : {
      "aliases" : { },
      "mappings" : {
        "properties" : {
          "age" : {
            "type" : "long"
          },
          "gender" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          },
          "name" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          }
        }
      },
      "settings" : {
        "index" : {
          "routing" : {
            "allocation" : {
              "include" : {
                "_tier_preference" : "data_content"
              }
            }
          },
          "number_of_shards" : "1",
          "provided_name" : "index2",
          "creation_date" : "1638778712605",
          "number_of_replicas" : "1",
          "uuid" : "lkWmNdTMTRK2IeIDyhqaDg",
          "version" : {
            "created" : "7100199"
          }
        }
      }
    }
  }
  ```
  - index2 인덱스에는 mappings에 age는 long 타입, gender, name은 text타입으로 필드가 지정되었다. 우리가 데이터 타입을 지정하지 않아도 엘라스틱서치는 도큐먼트의 필드와 값을 보고 자동으로 지정하는데 이런 기능을 `다이내믹 매핑(Dynamic Mapping)`이라 한다.

- 도큐먼트 인덱싱
  - index2인덱스에 새로운 필드가 추가된 도큐먼트 인덱싱
  ```
  PUT index2/_doc/2
  {
    "name" : "jane",
    "country" : "france"
  }
  ```
  > country 필드가 추가되었고 기존의 age, gender field가 없지만 문제 없이 인덱싱 된다.

  - index2 인덱스에 데이터 타입이 잘못된 도큐먼트 인덱싱
  ```
  PUT index2/_doc/3
  {
    "name" : "kim",
    "age" : "20",
    "gender" : "female"
  }
  ```
  > 숫자 필드에 문자열이 입력되면 숫자로 변환을 시도한다. ex. age : "10" -> 10

  > 정수 필드에 소수가 입력되면 소수점 아래 자리를 무시한다. ex. age : 10.0 -> 10

- 도큐먼트 읽기
  - 1. 도큐먼트 ID를 이용해 조회하는 방법.
    ```
    ==== 입력 ====
    GET index2/_doc/1
    ==== 출력 ====
    {
      "_index" : "index2",
      "_type" : "_doc",
      "_id" : "1",
      "_version" : 1,
      "_seq_no" : 0,
      "_primary_term" : 1,
      "found" : true,
      "_source" : {
        "name" : "mike",
        "age" : 25,
        "gender" : "male"
      }
    }
    ```

  - 2. 쿼리 DSL(Domian Specific Language)라는 엘라스틱 서치가 제공하는 쿼리문을 통한 검색방법.
    ```
    ==== 입력 ====
    GET index2/_search
    ==== 출력 ====
    {
      "took" : 1,
      "timed_out" : false,
      "_shards" : {
        "total" : 1,
        "successful" : 1,
        "skipped" : 0,
        "failed" : 0
      },
      "hits" : {
        "total" : {
          "value" : 3,
          "relation" : "eq"
        },
        "max_score" : 1.0,
        "hits" : [
          {
            "_index" : "index2",
            "_type" : "_doc",
            "_id" : "1",
            "_score" : 1.0,
            "_source" : {
              "name" : "mike",
              "age" : 25,
              "gender" : "male"
            }
          },
          {
            "_index" : "index2",
            "_type" : "_doc",
            "_id" : "2",
            "_score" : 1.0,
            "_source" : {
              "name" : "jane",
              "country" : "france"
            }
          },
          {
            "_index" : "index2",
            "_type" : "_doc",
            "_id" : "3",
            "_score" : 1.0,
            "_source" : {
              "name" : "kim",
              "age" : "20",
              "gender" : "female"
            }
          }
        ]
      }
    }
    ```
  - 자세한 DSL 쿼리는 뒷부분에서 서술

- 도큐먼트 수정
  - 엘라스틱 스텍에서 별도의 도큐먼트 업데이트를 위한 특정 API는 없다. 
  - 도큐먼트를 인덱싱하는 과정에서 같은 도큐먼트 ID가 있을 경우 덮어쓰기 되는 것 인데 마치 도큐먼트가 업데이트 되는 것 처럼 보인다. 
  - update API를 이용해 특정 도큐먼트의 값을 업데이트할 수 있다.
    - _update라는 EndPoint를 추가해 특정 필드의 값만 업데이트 할 수 있다.
  - 엘라스틱 서치 도큐먼트 수정 작업은 비용이 많이 들기 때문에 권장하지 않는다. 특히 엘라스틱 서치를 로그 수집 용도로 사용한다면 개별 도큐먼트를 수정할 일은 거의 없기 때문에 로그 분석에 많이 사용된다. 
  - 따라서 개별 도큐먼트 수정이 많은 작업이라면 엘라스틱 서치가 아닌 다른 DB를 사용하는 것이 좋다.
  ```
  ==== 입력 ====
  PUT index2/_doc/1
  {
    "name": "park",
    "age" : 45,
    "gender" : "male"
  }
  ==== 출력 ====
  {
    "_index" : "index2",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 2,
    "result" : "updated",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 3,
    "_primary_term" : 2
  }
  ```

  ```
  ==== 입력 ====
  POST index2/_update/1
  {
    "doc":{
      "name" : "lee"
    }
  }
  ==== 출력 ====
  {
    "_index" : "index2",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 3,
    "result" : "updated",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 4,
    "_primary_term" : 2
  }
  ```

- 도큐먼트 삭제
  - 특정 도큐먼트를 삭제하기 위해서는 인덱스명과 도큐먼트 아이디를 알 고 있어야 한다.
  ```
  ==== 입력 ====
  DELETE index2/_doc/2
  ==== 출력 ====
  {
    "_index" : "index2",
    "_type" : "_doc",
    "_id" : "2",
    "_version" : 2,
    "result" : "deleted",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 5,
    "_primary_term" : 2
  }
  ```
  - 결과 확인
  ```
  ==== 입력 ====
  GET index2/_search
  ==== 출력 ====
  {
    "took" : 1,
    "timed_out" : false,
    "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
    },
    "hits" : {
      "total" : {
        "value" : 2,
        "relation" : "eq"
      },
      "max_score" : 1.0,
      "hits" : [
        {
          "_index" : "index2",
          "_type" : "_doc",
          "_id" : "3",
          "_score" : 1.0,
          "_source" : {
            "name" : "kim",
            "age" : "20",
            "gender" : "female"
          }
        },
        {
          "_index" : "index2",
          "_type" : "_doc",
          "_id" : "1",
          "_score" : 1.0,
          "_source" : {
            "name" : "lee",
            "age" : 45,
            "gender" : "male"
          }
        }
      ]
    }
  }
  ```

## 3. 응답 메시지

앞서 엘라스틱 서치에서 제공하는 API를 이용해 REST API들을 호출했다.
이번에는 요청한 REST API 의 겨로가 메시지의 구조를 알아본다.

- 엘라스틱 서치 REST API 응답 코드

|코드|상태|해결방법|
|-|-|-|
|200, 201|정상적으로 수행함||
|4xx|클라이언트 오류|클라이언트에서 문제점 해결|
|404|요청한 리소스가 없음|인덱스나 도큐먼트가 존재하는지 체크|
|405|요청 메소드(GET, POST 등)을 지원하지 않음|API 사용법 다시 확인|
|429|요청 과부화(Busy)|재전송, 노드 추가 같은 조치|
|5xx|서버 오류|엘라스틱서치 로그 확인 후 조치|

- 예를 들어 존재하지 않는 인덱스에 접근할려 하면 다음과 같은 오류 발생(404)

```
==== 입력 ====
GET index5
==== 출력 ====
{
  "error" : {
    "root_cause" : [
      {
        "type" : "index_not_found_exception",
        "reason" : "no such index [index5]",
        "resource.type" : "index_or_alias",
        "resource.id" : "index5",
        "index_uuid" : "_na_",
        "index" : "index5"
      }
    ],
    "type" : "index_not_found_exception",
    "reason" : "no such index [index5]",
    "resource.type" : "index_or_alias",
    "resource.id" : "index5",
    "index_uuid" : "_na_",
    "index" : "index5"
  },
  "status" : 404
}
```

## 4. 벌크 데이터 

데이터 CRUD 동작을 할 떄는 REST API를 호출해 하나하나 도큐먼트를 요청하는 것 보다 벌크로 한번에 요청하는 것이 효율적이다.

API를 20번 호출해 20개의 도큐먼트를 인덱싱할 경우 20번의 HTTP통신이 발생하는데 API를 한번 호출해 20개의 도큐먼트를 인덱싱하면 훨씬 빠르고 경제적이다.

엘라스틱 서치에서는 이를 위해 bulk API를 지원하는데 REST API 콜 횟수를 줄여 성능을 높인다. 

### 4.1 벌크 데이터 포멧
```
POST _bulk
{"index" : {"_index" : "test", "_id": "1"}}
{"field1" : "value1"}
{"create" : {"_index" : "test". "_id" : "3"}}
{"field1" : value3"}
{"update" : {"_id" : "1", "_index" : "test"}}
{"_doc" : {"field2" : "value2"}}
{"delete" : {"_index" : "test", "_id" : "2"}}
```

- test, field1, field2, value1, value2, value3, 1, 2, 3은 모두 사용자가 입력해야 하는 값.
- index, _index, _id, create, update, doc, delete는 키워드 이다.
- bulk API는 도큐먼트 읽기는 지원하지 않고 도큐먼트 생성/수정/삭제 만 지원한다. 벌크 데이터 포맷을 보면 삭제(DELETE)만 한줄로 작성하고 나머지 작업들(index, create, update)은 두 줄로 작성된다.
- 각 줄 사이에는 쉼표 등 별도의 구분자가 없고 라인 사이 공백을 허용하지 않느다.
- JSON 문법처럼 보이지만 복수의 JSON구조를 줄바꿈 문자열로 구분하는 NDJSON(New-Line Delimited JSON)형태이다. 

### 4.2 벌크 데이터 요청

```
POST _bulk
{"index" : {"_index" : "index2", "_id": "4"}}
{"name" : "park", "age" : 30, "gender" : "female"}
{"index" : {"_index" : "index2", "_id": "5"}}
{"name" : "jung", "age" : 50, "gender" : "male"}
```

- 파일을 이용한 벌크 요청
  - bulk_index2
  ```
  ==== 파일 생성 ====
  D:\ElasticStack\Example>copy con bulk_index2
  {"index":{"_index":"index2","_id":"6"}}
  {"name":"hong","age":10, "gender":"female"}
  {"index":{"_index":"index2","_id":"6"}}
  {"name":"choi", "age":90, "gender":"male"}
          1개 파일이 복사되었습니다.
  ==== 실행 ====
  curl -H "Content-Type: application/x-ndjon" -XPOST localhost:9200/_bulk --data-binary @./bulk_index2
  ==== 실행 결과
  {"took":200,"errors":false,"items":[{"index":{"_index":"index2","_type":"_doc","_id":"6","_version":2,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":7,"_primary_term":2,"status":200}},{"index":{"_index":"index2","_type":"_doc","_id":"6","_version":3,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":8,"_primary_term":2,"status":200}}]}
  ```
  - 설명
    - -H는 curl의 헤더 옵션으로 DBJSON 타입의 컨텐츠를 사용한다는 의미
    - -X는 요청 메소드를 기술하는데 여기서는 POST를 사용했다.
    - localhost:9200은 엘라스틱 서치가 동작하는 호스트의 주소이다.
    - _bluk는 bluk API를 호출하는 것이다.
    - --data-binary는 POST 메소드에 만들어 둔 파일을 바이너리 형태로 전송해주는 파라미터이다.

## 5. 매핑(Mapping)

- 관계형 데이터베이스는 테이블을 만들 떄 반드시 스키마 설계가 필요하다..
- 여기서 말하는 스키마는 테이블을 구성하는 구성요서 간의 논리적인 관계와 정의를 의미한다.
- 구조를 명확히 하지 않고는 테이블이 생성되지 않고 추후에 인덱싱 컬럼이나 조인 컬럼 등에서도 문제가 발생하기 때문이다.
- 엘라스틱서치도 관계형 데이터베이스의 스키마와 비슷한 역할을 하는 것이 있는데 이를 Mapping이라고 한다.
- 쉽게 말하자면 JSON 형태의 데이터를 루씬이 이해할 수 있또록 바꿔주는 작업.
- 엘라스틱 서치가 검색 엔진으로 전문 검색과 대용량 데이터를 빠르게 실시간 검색할 수 있는 이유는 매핑이 있기 때문인데 매핑을 엘라스틱 서치가 자동으로 하면 다이내믹 매핑, 사용자가 직접하면 명시적 매핑이다. 

### 5.1 다이내믹 매핑

- 엘라스틱서치의 모든 인덱스는 매핑정보를 갖고 있지만 유연한 활용을 위해 인덱스 생성 시 매핑 정의를 강제하지 않는다. 앞에서 index2 인덱스를 만들 때 우리는 따로 매핑을 설정하지 않았다.
- 하지만 도큐먼트가 인덱싱 되었던 이유는 엘라스틱 서치의 다이내믹 매핑기능 때문.

|원본 소스 데이터 타입|다이내픽 매핑으로 변환된 데이터 타입|
|-|-|
|null|필드를 추가하지 않음|
|boolean|boolean|
|float|float|
|interger|long|
|object|object|
|string|string 데이터 형태에 따라 date, text/keyword 필드|

- 앞서 age 필드에 20이라는 값이 있으면 20을 숫자(integer)로 인식한다. 원본이 integer 타입이기 떄문에 엘라스틱서치는 인덱스 생성 시 age 필드를 long 타입의 필드로 매핑한다.
- 하지만 숫자 타입은 무조건 범위가 가장 넓은 long으로 매핑되기 때문에 효율적이지 못하다.
- 또한, county, gender와 같은 범주형 데이터는 전문 검색 보다는 일반적으로 집계나 정렬 필터링을 위해 사용되기 때문에 키워드 타입으로 지정하는 것이 좋다.


### 5.2 명시적 매핑

- 인덱스 매핑을 직접 정의하는 것을 명시적 매핑(explicit mapping)이라고 한다. 
- 명시적 인덱스 설정 방법은 다음과 같이 mappings-properties아래 필드명, 필드 타입 지정해 주면 된다.
  ```
  PUT "인덱스 명"
  {
    "mappings":{
      "properties":{
        "필드명":"필드 타입"
        ...
      }
    ... 
    }
  }
  ```

- index3 예시
  ```
  ==== 실행 ====
  PUT inedex3
  {
    "mappings":{
      "properties":{
        "age":{"type":"short"},
        "name":{"type":"text"},
        "gender":{"type":"keyword"}
      }
    }
  }
  ==== 결과 ====
  {
    "acknowledged" : true,
    "shards_acknowledged" : true,
    "index" : "inedx3"
  }
  ```

  - index3(명시적 매핑)와 index2의 비교
  ```
  ==== 실행 ====
  GET index2/_mapping
  ==== 결과 ====
  {
    "index2" : {
      "mappings" : {
        "properties" : {
          "age" : {
            "type" : "long"
          },
          "country" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          },
          "gender" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          },
          "name" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          }
        }
      }
    }
  }
  ============
  ==== 실행 ====
  GET index3/_mapping
  ==== 결과 ====
  {
    "inedx3" : {
      "mappings" : {
        "properties" : {
          "age" : {
            "type" : "short"
          },
          "gender" : {
            "type" : "keyword"
          },
          "name" : {
            "type" : "text"
          }
        }
      }
    }
  }
  ```

### 5.3 매핑 타입

| 데이터 형태 | 데이터 타입 | 설명 |
|-|-|-|
|텍스트|text|전문 검색이 필욯나 데이터로 텍스트 분석기가 텍스트를 작은 단위로 분리한다.|
| |keyword|정렬이나 집계에 사용되는 텍스트 데이터로 분석을 하지 않고 원문을 통쨰로 인덱싱|
|날짜|date|날짜/시간 데이터|
|정수|byte|부호 있는  8비트 데이터 (-128 ~ 127)|
| |short|부호 있는 16비트 데이터 (-32768 ~ 32767)|
| |integer|부호 있는 32비트 데이터 (-2^32 ~ 2^31 -1)|
| |long|부호 있는 64비트 데이터 (-2^63 ~ 2^63-1)|
|실수|scaled_float|float데이터에 특정 값을 곱해서 정수형으로 바꾼 데이터, 정확도는 떨어지나 집계 등에서 효율적으로 사용 가능하다.|
| |hlaf_float|16비트 부동 소수점 실수 데이터
| |double|32비트 부동 소수점 실수 데이터|
| |float|64비트 부동소수점 실수 데이터|
|불린|boolean|참/거짓 데이터로 True/False 데이터만 가짐|
|IP주소|ip|ipv4, ipv6 타입 ip 주소를 입력할 수 있다.|
|위치정보|geo-point|위도 경도 값을 갖는다.|
| | geo-shape|하나의 위치 포인트가 아닌 임의의 지형|
|범위 값|integer_range, long_range, float_range, double_range, ip_range, date_range| 범위를 설정할 수 있는 데이터 |
|객체형|object|계층 구조를 갖는 형태로 필드 안에 다른 필드들이 들어갈 수 있다|
|배열형|nested|배열형 객체를 저장한다. 객체를 따로 인덱싱하여 객체가 하나로 합쳐지는 것을 막고 배열 내부의 객체에 쿼리로 접근할 수 있다.|
| |join| 부모 자식 관계를 표현 할 수 있다.|

### 5.4 멀티 필드를 활용한 문자열 처리

- 텍스트 타입
  - 일반적으로 문장을 저장하는 매핑 타입으로 사용된다. 
  - 텍스트 타입으로 지정된 문자열은 분석기(Analyzer)에 의해 토큰으로 분리되고, 이렇게 분리된 토큰들은 인덱싱되는데 이를 역인덱싱이라 한다.
  - 이때 역인덱스에 저장된 토큰들을 용어(term)라고 한다. 
  ```
  === 텍스트 타입을 가진 text_index 생성====
  PUT text_index
  {
    "mappings":{
      "properties": {
        "contents" : {"type": "text"}
      }
    }
  }
  ==== 결과 ====
  {
    "acknowledged" : true,
    "shards_acknowledged" : true,
    "index" : "text_index"
  }
  ==== text_index인덱스에 도큐먼트 인덱싱 ====
  PUT text_index/_doc/1
  {
    "contents":"beautiful day"
  }
  ==== 결과 ====
  {
    "_index" : "text_index",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 1,
    "result" : "created",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 0,
    "_primary_term" : 1
  }
  ==== text_index 인덱스 전문 쿼리 ====
  GET text_index/_search
  {
    "query": {
      "match": {
        "contents": "day"
      }
    }
  }
  ==== 결과 ====
  {
    "took" : 668,
    "timed_out" : false,
    "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
    },
    "hits" : {
      "total" : {
        "value" : 1,
        "relation" : "eq"
      },
      "max_score" : 0.2876821,
      "hits" : [
        {
          "_index" : "text_index",
          "_type" : "_doc",
          "_id" : "1",
          "_score" : 0.2876821,
          "_source" : {
            "contents" : "beautiful day"
          }
        }
      ]
    }
  }
  ```
  - 설명
    - "beautiful day 는 타입이 텍스트이기 때문에 분석기에 의해 [beautiful, day]와 강은 용어 단위로 분리되어 역인덱스에 저장된다.
    - 즉 텍스트 타입으로 매핑된 값들은 분석기에 의해 문자들이 용어단위로 쪼개진다
    - match는 전문 검색을 할 수 있는 쿼리이며, contents 필드에 있는 역인덱싱된 용어 중 일치하는 용어가 있는 도큐먼트를 찾는 쿼리 문이다 

- 키워드 타입
  - 키워드 타입은 카테고리나 사람이름, 브랜드 등 규칙성이 있거나 유의미한 값들의 집함, 즉 범주형 데이터를 주로사용한다.
  - 키워드 타입은 텍스트 타입과 다르게 분석기를 거치지 않고 문자열 전체가 하나의 용어로 인덱싱 된다.
  - 키워드 타입으로 매핑된 데이터는 부분 일치 검색은 어렵지만 대신 완전 일치 검색을 위해 사용할 수 있으며 집계나 정렬에 사용할 수 있다.
  ```
  ==== 키워드 타입을 가진 keyword_index 인덱스 생성 ====
  PUT keyword_index
  {
    "mappings": {
      "properties": {
        "contents":{"type": "keyword"}
      }
    }
  }
  ==== 결과 ====
  {
    "acknowledged" : true,
    "shards_acknowledged" : true,
    "index" : "keyword_index"
  }
  ==== keyword_index 인덱스에 도큐먼트 인덱싱 ====
  PUT keyword_index/_doc/1
  {
    "contents": "beautiful day"
  }
  ==== 결과 ====
  {
    "_index" : "keyword_index",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 1,
    "result" : "created",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 0,
    "_primary_term" : 1
  }
  ==== keyword_index 인덱스 전문 쿼리 ====
  GET keyword_index/_search
  {
    "query": {
      "match": {
        "contents": "beautiful"
      }
    }
  }
  ==== 결과 ====
  {
    "took" : 72,
    "timed_out" : false,
    "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
    },
    "hits" : {
      "total" : {
        "value" : 0,
        "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
    }
  }
  ```
    - 설명 : keyword는 용어로 분리되지 않기 때문에 한 단어로 결과 찾을 수 없다.

- 멀티필드
  - 단일 필드 입력에 대해 여러 하위 필드를 정의하는 기능으로 이를 위해 field라는 매핑 파리미터가 사용된다. 
  ```
  ==== 멀티필드를 갖는 multifield_index 생성 ====
  PUT multifield_index
  {
    "mappings": {
      "properties": {
        "message" : {"type": "text"},
        "contents" : {
          "type": "text",
          "fields": {
            "keyword": {"type":"keyword"}
          }
        }
      }
    }
  }
  ==== 결과 ====
  {
    "acknowledged" : true,
    "shards_acknowledged" : true,
    "index" : "multifield_index"
  }
  ==== multifield_index 인덱스에 도큐먼트 인덱싱 ====
  PUT multifield_index/_doc/1
  {
    "message": "1 document",
    "contents": "beautiful day"
  }

  PUT multifield_index/_doc/2
  {
    "message": "2 document",
    "contents": "beautiful day"
  }

  PUT multifield_index/_doc/3
  {
    "message": "3 document",
    "contents": "wonderful day"
  }
  ==== 결과 ====
  # PUT multifield_index/_doc/1
  {
    "_index" : "multifield_index",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 1,
    "result" : "created",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 0,
    "_primary_term" : 1
  }

  # PUT multifield_index/_doc/2
  {
    "_index" : "multifield_index",
    "_type" : "_doc",
    "_id" : "2",
    "_version" : 1,
    "result" : "created",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 1,
    "_primary_term" : 1
  }

  # PUT multifield_index/_doc/3
  {
    "_index" : "multifield_index",
    "_type" : "_doc",
    "_id" : "3",
    "_version" : 1,
    "result" : "created",
    "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
    },
    "_seq_no" : 2,
    "_primary_term" : 1
  }
  ==== multifield_index 인덱스 전문 쿼리 ====
  GET multifield_index/_search
  {
    "query": {
      "match": {
        "contents": "day"
      }
    }
  }
  ==== 결과 3개 전부 출력 ====
  {
    "took" : 694,
    "timed_out" : false,
    "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
    },
    "hits" : {
      "total" : {
        "value" : 3,
        "relation" : "eq"
      },
      "max_score" : 0.13353139,
      "hits" : [
        {
          "_index" : "multifield_index",
          "_type" : "_doc",
          "_id" : "1",
          "_score" : 0.13353139,
          "_source" : {
            "message" : "1 document",
            "contents" : "beautiful day"
          }
        },
        {
          "_index" : "multifield_index",
          "_type" : "_doc",
          "_id" : "2",
          "_score" : 0.13353139,
          "_source" : {
            "message" : "2 document",
            "contents" : "beautiful day"
          }
        },
        {
          "_index" : "multifield_index",
          "_type" : "_doc",
          "_id" : "3",
          "_score" : 0.13353139,
          "_source" : {
            "message" : "3 document",
            "contents" : "wonderful day"
          }
        }
      ]
    }
  }
  ==== multiful_index 인덱스 용어 쿼리====
  GET multifield_index/_search
    {
      "query": {
        "term": {
          "contents.keyword": "day"
        }
      }
    }
    ==== 결과 ====
    결과 없음. 키워드 타입은 문자 분리 안됨.
    ==== multifield_index 인덱스 집계 쿼리 ====
    GET multifield_index/_search
  {
    "size": 0,
    "aggs": {
      "contents": {
        "terms": {
          "field": "contents.keyword"
        }
      }
    }
  }
  ==== 결과 ====
  {
    "took" : 1,
    "timed_out" : false,
    "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
    },
    "hits" : {
      "total" : {
        "value" : 3,
        "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
    },
    "aggregations" : {
      "contents" : {
        "doc_count_error_upper_bound" : 0,
        "sum_other_doc_count" : 0,
        "buckets" : [
          {
            "key" : "beautiful day",
            "doc_count" : 2
          },
          {
            "key" : "wonderful day",
            "doc_count" : 1
          }
        ]
      }
    }
  }
  ```

## 6. 인덱스 템플릿

인덱스 템플릿은 주로 설정이 동일한 복수의 인덱스를 만들 떄 사용한다. 
관리 편의성, 성능 등을 위해 인덱스를 파티셔닝 하는 일이 많은데 이때 파티셔닝 되는 인덱스들은 설정이 같아야 한다.

### 6.1 템플릿 확인
`GET _index_tempate` or `GET _index_tempalte/ilm-history` or `GET _index_template/ilm*`

### 6.2 템플릿 설정

인덱스 템플릿을 생성할 때 다양한 설정을 할 수 있지만, 일반적으로 매핑과 세팅 설정을 가장 많이 한다. 

- 템플릿 생성
  ```
  ==== test_template 인덱스 템플릿 생성 ====
  PUT _index_template/test_template
  {
    "index_patterns": ["test_*"],
    "priority": 1,
    "template": {
      "settings": {
        "number_of_shards": 3,
        "number_of_replicas": 1
      },
      "mappings":{
        "properties":{
          "name":{"type":"text"},
          "age":{"type":"short"},
          "gender":{"type":"keyword"}
        }
      }
    }
  }
  ==== 결과 ==== 
  {
    "acknowledged" : true
  }
  ```
  - 템플릿 생성시 주로 사용되는 파리미터
  
  |파라미터|설명|
  |-|-|
  |index_patterns|새로 만들어지는 인덱스 중에 인덱스 이름이 인덱스 패턴과 매칭되는 경우 이 템플릿이 적용된다. 여기서는 test_ 로 시작되는 이름을 가진 모든 인덱스들을 매핑한다.|
  |priority|인덱스 생성 시 이름에 매칭되는 템플릿이 둘 이상일 때 템플릿이 적용되는 우선순위를 정할 수 있다. 숫자가 가장 높은 템플릿이 우선이다.|
  |template| 새로 생성되는 인덱스에 적용되는 settings, mappings 같은 설정|

### 6.3 템플릿 적용

위에서 만든 test_template이 적용될 수 있또록 인덱스 패턴에 맞는 인덱스를 만들어본다.
템플릿을 적용할 때 기억해야 하는 것이 있다. 
`템플릿을 만들기전에 이미 존재하던 인덱스는 비록 템플릿 패턴과 일치하더라도 템플릿이 적용되지 않는다.` 

```
==== test_template 템플릿이 적용된 test_index1 인덱스 설정 ====
PUT test_index1/_doc/1
{
  "name": "kim",
  "age": 10,
  "gender": "male"
}
==== 결과 =====
{
  "_index" : "test_index1",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
==== test_index1 매핑 확인 ====
GET test_index1/_mapping
==== 결과 위의 패턴과 같음을 확읺 할 수 있다. ====
{
  "test_index1" : {
    "mappings" : {
      "properties" : {
        "age" : {
          "type" : "short"
        },
        "gender" : {
          "type" : "keyword"
        },
        "name" : {
          "type" : "text"
        }
      }
    }
  }
}
```
- 만약 템플릿과 다른 형식의 데이터가 인덱스로 만들어진다면 오류 발생 
```
==== 템플릿 매핑값과 다른 도큐먼트 인덱싱 ====
PUT test_index2/_doc/1
{
  "name" : "lee",
  "age" : "19 years"
}
==== 결과 에러 400 ====
{
  "error" : {
    "root_cause" : [
      {
        "type" : "mapper_parsing_exception",
        "reason" : "failed to parse field [age] of type [short] in document with id '1'. Preview of field's value: '19 years'"
      }
    ],
    "type" : "mapper_parsing_exception",
    "reason" : "failed to parse field [age] of type [short] in document with id '1'. Preview of field's value: '19 years'",
    "caused_by" : {
      "type" : "number_format_exception",
      "reason" : "For input string: \"19 years\""
    }
  },
  "status" : 400
}
```

### 6.4 템플릿 삭제

```
==== 템플릿 삭제 ====
DELETE _index_template/test_template
==== 결과 ====
{
  "acknowledged" : true
}
```

### 6.5 다이내믹 템플릿

로그 시스템이나 비정형화된 데이터를 인덱싱하는 경우 구조를 알 수 없어 필드 타입을 정확히 정의하기 어렵고 필드의 개수를 정할 수 없을 경우도 있다.

다이내믹 템플릿은 이처럼 매핑을 정확하게 정할 수 없거나 대략적인 데이터 구조만 알고 있을 때 사용할 수 있는 방법이다. 

```
==== 다이내믹 템플릿 추가 - index 생성 시 dynamix_templates 사용 ====
PUT dynamic_index1
{
  "mappings": {
    "dynamic_templates" : [
      {
        "my_string_fields": {
          "match_mapping_type": "string",
          "mapping": {"type":"keyword"}
        }
      }
    ]
  }
}
==== 다이내믹 매핑이 적용된 인덱스에 도큐먼트 인덱싱 ====
PUT dynamic_index1/_doc/1
{
  "name" : "mr.kim",
  "age" : 40
}
==== 인덱스 구조 확인(매핑 확인) ====
GET dynamic_index1/_mapping
==== 결과 ====
{
  "dynamic_index1" : {
    "mappings" : {
      "dynamic_templates" : [
        {
          "my_string_fields" : {
            "match_mapping_type" : "string",
            "mapping" : {
              "type" : "keyword"
            }
          }
        }
      ],
      "properties" : {
        "age" : {
          "type" : "long"
        },
        "name" : {
          "type" : "keyword"
        }
      }
    }
  }
}

```

- match_mapping_type은 조건문 혹은 매핑트리거 이다. 조건에 만족할 경우 트리거링 된다.
- 다이내믹 템플릿 조건문

|조건문|설명|
|-|-|
|match_mapping_type|데이터 타입을 확인하고 타입(boolean, date, double, long, object, string)들 중 일부를 지정한 매핑 타입으로 변경한다.|
|match| 필드명이 패턴과 일치할 경우 매핑 타입으로 변경한다.|
|unmatch|match패턴과 일치하는 경우에 제외할 패턴을 설정할 수 있다.|
|match_patterhn|match 패턴에서 사용할 수 있는 파라미터를 조정한다. 예를 들어 정규식이나 와일드 패턴 등을 지정한다.|
|path_match, path_unmatch|match, unmatch와 비슷하지만 점이 들어가는 필드명에 사용|

## 7. 분석기

엘라스틱 서치는 전문 검색을 지원하기 위해 역인덱싱 기술을 사용한다. 전문 검색은 장문의 문자열에서 부분 검색을 수행하는 것이며, 역인덱싱은 장문의 문자열을 분석해 작은 단위로 쪼개어 인덱싱하는 기술이다. 

역인덱싱을 이용한 전문 검색에서 양잘의 결과를 얻기 위해서는 문자열을 나누는 기준이 중요하며, 이를 지원하기 위해 엘라스틱서치는 캐릭터필드, 토크나이저, 토큰 필터로 구성되어 있는 분석기 모듈을 가지고 있다.

```
참고

앞으로 토큰과 용어라는 단어가 많이 사용되는데 두 단어가 헷갈리기 쉽다. 
                  
cute dog -> 분석기
             ↓
           캐릭터 필터 -> 토크나이저 -> 토큰 필터 -> 인덱스
           (cute dog)   (cute, dog)   (cute, dog)    |
                                                     ↓
                                                  용어(Term)

cute dog 라는 문자열이 분석기를 거처 인덱스에 저장된다고 가정하면 
분석기는 먼저 캐릭터 필터를 통해 원문에서 불필요한 문자들을 제거한다. 
이 과정까지는 문자열 자체가 분리되지 않기 때문에 그저 필터링된 문자열 정도 이다.
이후 분석기는 토크나이저를 이용해 필터링된 문자열을 자르게 되는데. 이때 잘린 단위를 토큰이라고 한다. 
이러한 토큰들은 복수의 토큰 필터를 거치며 정제되는데 정제 후 최종으로 역인덱스에 저장되는 상태의 토큰들을 용어(Term)이라고 하낟. 
토큰은 분석기 내부에서 일시적으로 존재하는 상태이고, 인덱싱되어있는 단위, 또 검색에 사용되는 단위는 모두 용어이다.
```

### 7.1 분석기 구성

|구성요소|설명|
|-|-|
|캐릭터 필터|입력받은 문자열을 변경하거나 불필요한 문자들을 제거한다.|
|토크나이저|문자열으르 토큰으로 분리한다. 분리할 때 토큰의 순서나 시작 끝 위치도 기록|
|토큰 필터|분리된 토큰들의 필터 작업을 한다. 대소문자 구분 형태소 분석 등의 작업이 가능하다.|

- 역인덱싱
  - 책 뒤의 찾아보기 처럼 많이 쓰는 단어를 선별해 그 단어가 몇 페이지에 있는지 알려주는 것이 대표적인 색인(역인덱싱)의 예이다.
- 분석기 API
  - 엘라스틱 서치는 필터와 토크나이저를 테스트 해볼 수 있는 analyze라는 이름의 REST API를 제공하고 있다. 
  - 분석기 API 사용법은 간단하게 analyzer에 원하는 분석기를 선택하고 text에 문자열 입력 
  ```
  ====스톱 분석기 테스트 ====
  POST _analyze
  {
    "analyzer": "stop",
    "text":"The 10 most loving dog breeds."
  }

  ==== 스톱 테스트 결과 ====
  {
    "tokens" : [
      {
        "token" : "most",
        "start_offset" : 7,
        "end_offset" : 11,
        "type" : "word",
        "position" : 1
      },
      {
        "token" : "loving",
        "start_offset" : 12,
        "end_offset" : 18,
        "type" : "word",
        "position" : 2
      },
      {
        "token" : "dog",
        "start_offset" : 19,
        "end_offset" : 22,
        "type" : "word",
        "position" : 3
      },
      {
        "token" : "breeds",
        "start_offset" : 23,
        "end_offset" : 29,
        "type" : "word",
        "position" : 4
      }
    ]
  }
  ```
  - 위의 결과 처럼 text에 적었던 문자열이 4개의 토큰으로 분리되었고 'The' 10 등은 스톱 분석기에 의해 토큰으로 분류되지 않는다.
  - `스톱 분석기` 는 불용어(데이터 집합에 출연하는 빈도는 매우 높지만 의미가 없는 단어)를 처리한다.

- 분석기 종류
  |분석기|설명|
  |-|-|
  |standard|특별한 설정이 없으면 엘라스틱서치가 기본으로 사용하는 분석기, 영문법을 기준으로 한 스탠다드 토크나이저와 소문자 변경 필터, 스톱 필터가 포함되어 있다.[the, 10, most, loving, dong, breeds]|
  |simple|문자만 토큰화 한다. 공백, 숫자, 하이픈이나 작은 따옴표 같은 문자는 토큰화 하지 않는다.[the, most, loving, dog, breed]|
  |whitespace|공백을 기준으로 구분하여 토큰화 한다. [The, 10, most, loving, dong, breeds]|
  |stop|simple분석기와 비슷하지많 스톱필터가 포함되었다. 스톱 필터에 의해 the가 제거된다.[most, loving, dog, breeds]|

### 7.2 토크나이저
- 분석기는 반드시 하나의 토크나이저를 포함해야 한다.
- 토크나이저는 문자열을 분리해 토큰화 하는 역할을 한다.
  
  |토크나이저|설명|
  |-|-|
  |standard|스탠다느 분석기가 사용하는 토크나이저로, 특별한 설정이 없으면 기본 토크나이저로 사용된다. 쉼표, 점 같은 기호를 제거해 텍스트 기반으로 토큰화 한다.|
  |lowercase|텍스트 기반으로 토큰화하며 모든 문자를 소문자로 변경해 토큰화 한다.|
  |ngram|원문으로부터 N 개의 연속된 글자 단위를 모두 토큰화 하낟. 예를들어 '엘라스틱서치'라는 원문을 2gram으로 토큰화 하면 [엘라, 라스, 스틱, 틱서, 서치]와 같이 연속된 두 글자를 모두 추출한다. 사실상 원문으로 부터 검색할 수 있는 거의 모든 조합을 얻어낼 수 있기 떄문에 정밀한 검색에 장점이 있즈미나, 토크나이징을 수행한 N 개 이하의 글자수로는 검색이 불가능하며 모든 조합을 추축하기 때문에 저장공간을 많이 차지하는 단점이 있다.|
  |uax_url_email|스탠다드 분석기와 비슷하지만 URL이나 이메일을 토큰화하는데 강점이 있다.|


### 7.3 필터
- 분석기는 하나의 토크나이저와 다수의 필터로 조합된다.
- 분석기에서 필터는 옵션으로 하나 이상을 포함하라 수 있지만, 없어도 분석기를 돌리는데 문제는 없다.
- 대부분의 엘라스틱에서 제공하는 분석기들은 하나 이상의 필터를 포함하고 있다. 
- 필터는 단독으로 사용할 수 없고 반드시 토크나이저가 있어야 한다.
  ```
  ==== uppercase 필터 테스트 ====
  POST _analyze
  {
    "tokenizer": "standard",
    "filter": ["uppercase"],
    "text": "The 10 most loving dog breeds."
  }
  ==== 결과 ====
  {
    "tokens" : [
      {
        "token" : "THE",
        "start_offset" : 0,
        "end_offset" : 3,
        "type" : "<ALPHANUM>",
        "position" : 0
      },
      {
        "token" : "10",
        "start_offset" : 4,
        "end_offset" : 6,
        "type" : "<NUM>",
        "position" : 1
      },
      {
        "token" : "MOST",
        "start_offset" : 7,
        "end_offset" : 11,
        "type" : "<ALPHANUM>",
        "position" : 2
      },
      {
        "token" : "LOVING",
        "start_offset" : 12,
        "end_offset" : 18,
        "type" : "<ALPHANUM>",
        "position" : 3
      },
      {
        "token" : "DOG",
        "start_offset" : 19,
        "end_offset" : 22,
        "type" : "<ALPHANUM>",
        "position" : 4
      },
      {
        "token" : "BREEDS",
        "start_offset" : 23,
        "end_offset" : 29,
        "type" : "<ALPHANUM>",
        "position" : 5
      }
    ]
  }
  ```
  - 캐릭터 필터
    - 캐릭터 필터는 토크나이저 전에 위치하며 문자들을 전처리 하는역할을 하느데. HTML 문법을 제거/변경 하거나 특정 문자가 왔을 때 다른 문자로 대체하는 일들을 한다. 예를 들어 &nbsp; 문자 (웹에서 공백을 의미하는 문자)가 오면 공백으로 바꾸는 작업 등을 캐릭터 필터 에서 진행하면 편하다. 엘라스틱 서치가 제공하는 대부분의 분석기는 캐릭터 필터가 포함되어 있지 않다. 이를 사용하기 위해서는 커스텀 필터를 사용해 분석한다.
  - 토큰 필터
    - 토큰 필터는 토크나이저에 의해 토큰화 되어 있는 문자들에 필터를 적용한다.
    - 토큰들을 변경하거나 삭제하고 추가하는 등의 작업이 가능하다.
    - 토큰 필터 역시 종류가 많고 자주 변경된다.
    
      |필터|설명|
      |-|-|
      |lowercase|모든 문자를 소문자로 변환한다. 반대는 upppercase필터|
      |stemmer|영어 문법을 분석하는 필터, 언어마다 고유한 문법이 있어, 필터 하나로 모든언어를 대응하기는 어렵다. 한글의 경우 아리랑, 노리 같은 오픈소스가 있다.|
      |stop|기본 필터에서 제고하지 못하는 특정한 단어를 제거할 수 있다.|

### 7.4 커스텀 분석기

- 엘라스틱서치에서 제공하는 내장 분석기들 중 원하는 기능을 만족하는 분석기가 없을 떄 사용자가 직접 토크나이저, 필터 등을 조합해 사용할 수 있는 분석기다.
- 필터들의 조합이나 순서에 따라 특별한 형태의 분석기를 만들 수 있다.

#### 7.5 커스텀 분석기 설정
customer_analyzer라는 인덱스를 하나 만들고 안에는 my_analyzer라는 커스텀 분석기를 가지고 있게 설정한다. 해당 커스텀 분석기에서는 원하는 토크나이저, 캐릭터 필터, 토큰 필터를 적용해 본다.

- customer_analyzer라는 새로운 인덱스에서 settings에 analysis 파라미터를 추가하고, 안에 필터와 분석기를 만들 것이다. 
- 먼저 분석기의이름을 my_analyzer로 하고 타입을 custom으로 하면 커스텀 분석기를 의미하게 된다.
- 분석기에는 반드시 토크나이저가 필요해 standard토크나이저를 사용했고, 캐릭터 필터는 사용하지 않는다.
- 토큰 필터는 2개를 사용하는데 엘라스틱에 내장된 lowercase 필터와 사용자가 만든 my_stopword를 사용한다. 
```
==== 커스텀 분석기를 적용한 customer_analyzer인덱스 생성 ====
PUT customer_analyer
{
  "settings": {
    "analysis": {
      "filter": {
        "my_stopwords":{
          "type" : "stop",
          "stopwords": ["lions"]
        }
      },
      "analyzer": {
        "my_analyzer":{
          "type":"custom",
          "char_filter":[],
          "tokenizer":"standard",
          "filter":["lowercase", "my_stopwords"]
        }
      }
    }
  }
}
==== 커스텀 분석기 테스트 ====
GET customer_analyer/_analyze
{
  "analyzer": "my_analyzer",
  "text": "Cats Lions Dogs"
}

==== 결과 ====
{
  "tokens" : [
    {
      "token" : "cats",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "dogs",
      "start_offset" : 11,
      "end_offset" : 15,
      "type" : "<ALPHANUM>",
      "position" : 2
    }
  ]
}
```
