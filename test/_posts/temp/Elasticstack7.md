---
author_profile: true
date: 2021-08-20
title: "Elastic Stack 7 - Kibana"
categories: 
    - Elastic Stack
tag: 
    - Kibana

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Kibana(키바나)

---

## 1. 키바나란?

- 키바나는 엘라스틱 스택의 관리, 모니터링, 솔루션을 총괄하는 메인 UI 이다. 

|키바나 기능|설명|
|-|-|
|데이터 분석과 시각화 툴|오픈소스 기반의 데이터 탐색 및 시각화 도구 제공|
|엘라스틱 관리|보안, 스냅샷, 인덱스 관리, 개발자 도구 등을 제공|
|엘라스틱 중앙 허브|모니터링을 비롯해 엘라스틱 솔루션을 탐색하기위한 포털 제공|

## 2. 시각화 기능(키바나)를 위한 키바나 인덱스 패턴

- 키바나 시각화 기능
    
    |시각화 기능|설명|
    |-|-|
    |Discover|데이터를 도큐먼트 단위로 탐색해 구조와 관계 등을 확인할 수 있다.|
    |Visualize|다양한 그래프 타입으로 데이터 시각화를 할 수 있다.|
    |Dashboard|그래프 지도 등을 한 곳에서 확인하면서 다양한 인사이트 얻을 수 있다.|
    |Canvas|그래프와 이미지 등을 프리젠테이션 슬라이드 처럼 구성할 수 있다.|
    |Maps| 위치 기반 데이터를 지도 위에 표시할 수 있다.|


- 인덱스 패턴
  - 키바나에서 시각화를 하기 위해서는 반드시 엘라스틱 서치 인덱스에 연결되어야만 한다.
  - 따라서 키바나를 범용적인 시각화 툴러 사용하기에는 무리가 있다.
  - 하지만 오픈소스이면서 다양한 그래프와 지도를 지우너한고 빅데이터 처리가 가능하다는 장점이 있다.
  - 키바나는 데이터 소스를 엘라스틱 서치 인덱스에서 가저오는데 이를 인덱스 패턴이라고 한다.
  - 키바나 인덱스 패턴은 인덱스 매핑 정보 등을 키바나에 사용하기 적합하게 미리 캐싱해 둔 것으로 여러개의 인덱스에 대한 메타데이터를 병합해 저장해 두었다가 검색이나 시각화 생성 시 활용하게 된다.
  - 인덱스 패턴 생성
    - 엘라스틱 서치에 단순히 인덱스를 생성한다고 해서 키바나에서 Discover, Viusalize 같은 메뉴에 들어가 보아도 해당 인덱스에 접근할 수 없다. 
    - 즉, 키바나는 엘라스틱 서치의 인덱스가 아니라 키바나 인덱스 패턴을 데이터 소스로 인식하기 때문에 인식하지 못했던 것이다.
    - 1. 엘라스틱 서치 인덱스 생성
        ```
        PUT kibana_index1/_doc/1
        {
        "name": "kim"
        }

        PUT kibana_index2/_doc/1
        {
        "name": "lee"
        }
        ```
    - 2. 키바나 왼쪽 상단 토글 메뉴 선택 Managemet > Stack Management 선택 그 중 Kibana > Index Patterns라는 링크 를 통해 인덱스 패턴을 만들 수 있다.(create index pattern)
        ![Create Index Pattern](/assets/images/Elasticstack-beats3.PNG)
    - 3. index pattern name에 kibnan_index 까지 치면 하단에 다음 그림과 같이 지정할 수 있는 인덱스를 확인할 수 있다. 또한 뒤에 *, - 와 같이 특정 패턴을 제외 혹은 추가 할 수 있다.
        ![Index Pattern name1](/assets/images/Elasticstack-beats4.PNG)
        ![Index Pattern name2](/assets/images/Elasticstack-beats5.PNG)
    - 4. 키바나 인덱스 패턴이 생성되었으며 이제 키바나 데이터 탐색과 시각화가 가능하다.
  
## 3. Discover

- Disconver 화면 구성

  ![Index Pattern name2](/assets/images/Elasticstack-beats6.PNG)

  - 툴바 : 결과를 저장하고 불러오는 등의 기능 버튼이 위치한다.
  - 인덱스 패턴 : 탐색할 인덱스 패턴을 선택할 수 있다.
  - 사이드 바 : 필드 타입과 필드명을 볼 수 있어 데이터 구조를 확인할 수 있다. 데이터가 너무 많다면 특정 필드만 선택해서 볼 수도 있다.
  - 시계열 히스토그램 : 히스토그램 형태로 시간에 따른 발생량을 확인할 수 있따. 인덱스 패턴에 시간/날짜 필드가 없는 경우에는 보이지 않는다.
  - 쿼리 바 : 쿼리를 이용해 데이터를 검색할 수 있다.
  - 타임피커 : 시계열 데이터의 시간 범위를 지정할 수 있다.
  - 필터바 쿼리를 필터처럼 사용할 수 있다.


- 쿼리 바
  - 쿼리 바는 KQL(Kibana Query Language)와 루씬 쿼리 스트링 두가지 언어를 지원한다. 
  - KQL의 경우 거의 자동완성이 가능하기 떄문에 이를 활용해 쿼리를 작성할 수 있다.
  - `Cancelled : true and DestCountry : "US" `
  - cancelled가 true 이면서 Destcountry가 US인 도큐먼트를 kibana_sample_data_flight 인덱스에서 뽑아올 수 있다.

- 필터 바
  - 쿼리 바와 하는 역할이 비슷하짐나 필드를 개별적으로 처리할 수 있다.
  - 즉, 쿼리바에서 썼던 `Cancelled : true and DestCountry : "US" `를 cancelled, DestCountry 를 각각으로 나누어 설정할 수 있으며 각 필터는 and 연산이 된다.


## 4. Visualization(시각화)

- 엘라스틱 서치에 저장된 데이터를 그래프나 표, 지도 등 다양한 타입으로 보여주는 역할을 한다.
- 라인, 바, 파이 차트부터 시계열 비주얼 빌더(Time Series Visual Builder) 태그 클라우드 등 다양한 시각화 타입을 지원한다. 
- Visualize 메뉴 사용은 어렵지 않지만 엘라스틱 서치의 집계(aggregation)을 모르면 이해가 쉽지 않다. 
  - 메트릭 집계: 평균 최소 최대와 같은 수량을 계산한다.
  - 버킷 집계 : 특정 기준에 맞춰 데이터를 분리한다. 서브 버킷을 생성할 수 있다.
  - 파이프라인 집계 : 집계 결과를 입력으로 받아 다시 집계를 한다. 부모 형제 집계 유형이 있다.
- 시각화 타입은 대부분 메트릭 집계와 버킷 집계를 통해 그래프를 그리고, 좀 더 복잡한 그래프는 파이프라인 집계를 이용한다.

### 시각화 실습

1. Kibana의 좌상단 토글메뉴에서 Kibana > Visualize > Create visualization 클릭

![create visualization](/assets/images/Elasticstack-beats7.PNG){: .align-center}

2. 위와 같이 다양한 형태의 시각화 유형을 선택할 수 있으며 여기서는 Virtical 을 선택한다.

![virtical virtualization](/assets/images/Elasticstack-beats8.PNG){: .align-center}

3. 먼저 Metrics와 Bucket을 설정해야 한다. 
   1. Metrics는 평균값, 최솟값, 최댓값 같은 통계를 보여주고 그래프 상에서 Y축에 속한다.
   2. Bucket은 특정 기준으로 데이터를 나누는 역할을 하며 그래프 상에서 X 축에 해당한다. 
4. Bucket 설정
   1. Bucket의 Add를 누르게 되면 X-axis, Split series, Split chart 의 메뉴가 나오게 된다.
   2. 각각 x-axis는 X 축을 의미하고, Split series는 서브 버킷 용도, split chart는 그래프를 버킷 기준으로 쪼갤 떄 사용한다.
   3. X-axis는 X 축을 어떤 기준으로 나눠야 하는지 정하는 화면이다.
   4. X 축 버킷 집계 종류는 다음과 같다
      1. Date Histogram : 날짜/시간 데이터 타입을 가진 필드만 사용 가능 일정한 주기를 기준으로 버킷을 구분한다.
      2. Date Range : 날짜/시간 데이터 타입을 가진 필드만 사용가능, 사용자가 임의 범위를 지정해 버킷을 구분한다.
      3. Filters :  필터 적용 가능
      4. Histogram : 일정한 주기를 기준으로 버킷을 구분한다.
      5. Ipv4 Range : IP 타입을 가진 필드만 사용가능하며 Ip범위를 지정해 버킷을 구분한다.
      6. Range : 사용자가 임의의 범위를 지정해 버킷을 구분한다.
      7. Significant Terms : 필드의 유니크한 값 중 통계적으로 의미있는 용어를 기준으로 구분
      8. Terms : 필드의 유니크한 값을 기준으로 구분한다.
   5. Date Histogram을 사용하면 다음과 같이 출력된다.

![Date Histogram](/assets/images/Elasticstack-beats9.PNG){: .align-center}

   6. Range 를 사용하면 다음과 같다. 

![range](/assets/images/Elasticstack-beats10.PNG){: .align-center}

   7. Terms를 이용해 특정 필드의 고유값을 기준으로 데이터를 구분하고 용어집계를 통해 x축을 그린다. 

![terms](/assets/images/Elasticstack-beats11.PNG){: .align-center}

5. Metrics 
   1. bucket이 x축을 설정한다면 Metricsㄴ는 Y축을 정하는데 x 축을 어떤 통곗값으로 표현할지 보여주는 것이다.
   2. Bucket에서 Aggregation-Histogram, Field-dayOfWeek, Minimum interval - 1로설정한다.
   3. Metrics는 특별한 설정을 하지 않으면 Count값이 기본 설정이다.
   4. Metrics에서 Y-axis를 설정하고 Aggregation - average, field- AvgTicketPrice로 설정하면 다음과 같이 출력된다.

![terms](/assets/images/Elasticstack-beats12.PNG){: .align-center}

6. Metrics에서 파이프라인 집계 사용
   1. Derivative(미분) 집계 수행
   2. 앞서 5번에서 수행한 그래프에 추가적으로 Metrics를 추가해 파이프라인 집계를 해본다.
   3. Metrics에 Y-axias를 추가하고 Aggregation - Derivative, Metrics -(앞서 만든)Metrics:Average AvgTicketPrice 선택하면 다음과 같은 결과가 출력된다.
   4. 이는 앞서 출력한 막대 그래프에 변화량이 추가된 것이다. 

![terms](/assets/images/Elasticstack-beats13.PNG){: .align-center}



## 5. 엘라스틱 서치 SQL

- 기존 SQL 문법에 익숙한 사용자를 위한 엘라스틱 서치에서 제공하는 SQL 이다.
- 엘라스틱 서치는 관계형 데이터 베이스가 아니기 때문에 SQL문법 지원이 쉬운 일은 아니다.
- 기본적인 저장구조나 동작 방식이 관걔형 DB와 다르기 때문이다. 
- 따라서, SQL 쿼리로 요청하면 내부적으로 엘라스틱 서치의 쿼리 DSL 형태로 변형하고 최적화하는 형태로 동작한다. 

### EX. SELECT

```
SELECT select_expr [, ...]
[FROM TABLE_NAME]
[WHERE CONDITION]
[GROUP BY GROUPING_ELEMNENT [, ...]]
[HAVING CONDITION]
[ORDER BY EXPRESSION [ASC|DESC][, ...]]
[LIMIT [COUNT]]
[PIVOT (aggregation_expr FOR column IN (value[[AS]alias[, ...]]))]
```
- 실제 사용
    
    ```
    POST _sql?format=txt
    {
    "query": """
    DESCRIBE kibana_sample_data_flights
    """
    }
    ```

- 결과
    
    ```
          column      |     type      |    mapping    
    ------------------+---------------+---------------
    AvgTicketPrice    |REAL           |float          
    Cancelled         |BOOLEAN        |boolean        
    Carrier           |VARCHAR        |keyword        
    Dest              |VARCHAR        |keyword        
    DestAirportID     |VARCHAR        |keyword        
    DestCityName      |VARCHAR        |keyword        
    DestCountry       |VARCHAR        |keyword        
    DestLocation      |GEOMETRY       |geo_point      
    DestRegion        |VARCHAR        |keyword        
    DestWeather       |VARCHAR        |keyword        
    DistanceKilometers|REAL           |float          
    DistanceMiles     |REAL           |float          
    FlightDelay       |BOOLEAN        |boolean        
    FlightDelayMin    |INTEGER        |integer        
    FlightDelayType   |VARCHAR        |keyword        
    FlightNum         |VARCHAR        |keyword        
    FlightTimeHour    |VARCHAR        |keyword        
    FlightTimeMin     |REAL           |float          
    Origin            |VARCHAR        |keyword        
    OriginAirportID   |VARCHAR        |keyword        
    OriginCityName    |VARCHAR        |keyword        
    OriginCountry     |VARCHAR        |keyword        
    OriginLocation    |GEOMETRY       |geo_point      
    OriginRegion      |VARCHAR        |keyword        
    OriginWeather     |VARCHAR        |keyword        
    dayOfWeek         |INTEGER        |integer        
    timestamp         |TIMESTAMP      |datetime       
    ```

- 좀더 복잡한 쿼...리

    ```
    POST _sql?format=txt
    {
    "query": """
    SELECT Dest FROM kibana_sample_data_flights
    WHERE OriginCountry='US'
    ORDER BY DistanceMiles
    DESC LIMIT 10
    """
    }
    ```

- 결과

    ```
                        Dest                    
    --------------------------------------------
    King Shaka International Airport            
    Melbourne International Airport             
    Melbourne International Airport             
    Melbourne International Airport             
    Sydney Kingsford Smith International Airport
    King Shaka International Airport            
    King Shaka International Airport            
    Sydney Kingsford Smith International Airport
    Melbourne International Airport             
    Melbourne International Airport         
    ```

- 위의 복잡한 쿼리를 엘라스틱서치 DSL 로 변환하면 다음과 같다.

    ```
    GET kibana_sample_data_flights/_search
    {
    "size": 10,
    "query": {
        "term": {
        "OriginCountry": {
            "value": "US",
            "boost": 1.0
        }
        }
    },
    "_source": false,
    "stored_fields": "_none_",
    "docvalue_fields": [
        {
        "field" : "Dest"
        }
    ],
    "sort": [
        {
        "DistanceMiles": {
            "order": "desc",
            "missing": "_first",
            "unmapped_type" : "float"
        }
        }
    ]
    }
    ```

    - Limit 10에 해당하는 부분은 "size": 10 으로 변환된다.
    - Where 절은 term 필터를 사용하고 
    - Order by는 sort를 이용한다.
