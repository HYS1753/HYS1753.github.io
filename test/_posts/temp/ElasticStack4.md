---
author_profile: true
date: 2021-08-20
title: "Elastic Stack - Elastic Search 집계"
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

# 엘라스틱 서치 집계

---

엘라스틱 서치에서 집계(aggregation)은 데이터를 그룹핑하고 통계값을 얻는 기능으로 SQL의 Group BY 와 통계 함수를 포함하는 개념이다.

예를 들어 데이터를 날짜별로 묶거나 특정 카테고리 별로 묶어 그룹별 통계를 내는 방식.

## 1. 집계의 Req-Res 형태

- 집계를 위한 특별한 API가 제공되는 것은 아니며 search API의 요청 본문에 aggs 파라미터를 이용하면 쿼리 결과에 대한 집계를 생성할 수 있다. 

```
# 집계를 위한 기본 형태
GET index_name/_search
{
    "aggs":{
        "my_aggs": {
            "agg_type":{
                ...
            }
        }
    }
}
```

- `aggs` : 집계를 요청한다는 의미
- `my_aggs` : 사용자가 지정하는 집계의 이름, 응답 결과에서 확인 가능
- `agg_type` : 집계 타입을 의미한다. 
  - metric aggregation(메트릭 집계) : 통계나 계산에 사용
  - bucket aggregation(버킷 집계) : 도큐먼트를 그룹핑 하는데 사용된다.

```
# 집계 응답 기본 형태
"hits" : {
    "total" :{
        ...
    },
    "aggregations": {
        "my_aggs": {
            "value" : {
                ...
            }
        }
    }
}
```

## 2. Metric 집계

- 메트릭 집계는 필드의 최소/최대/합계/평균/중간값 같은 통계 결과를 보여준다.  
- 메트릭 집계 종류

|메트릭 집계|설명|
|-|-|
|avg|필드의 평균값을 계산한다.|
|min|필드의 최솟값을 계산한다.|
|max|필드의 최댓값을 계산한다.|
|sum|필드의 총합을 계산한다.|
|percenttiles|필드의 백분윗값을 계산한다.|
|stats|필드의 min, max, sum, avg, count(도큐먼트 개수)를 한번에 볼 수 있다.|
|cadinality|칠드의 유니크한 값의 개수를 보여준다.|
|geo-centroid|필드 내부의 위치 정보의 중심점 계산|

### 2.1 평균값/중간값 구하기 

```
# 평균값 구하는 집계 요청

GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs":{
    "stats_aggs":{
      "avg": {
        "field": "products.base_price"
      }
    }
  }
}

==== 결과 ====
  "aggregations" : {
    "stats_aggs" : {
      "value" : 34.88652318578368
    }
  }
```

  - `"size" : 0` : 집계에 사용한 도큐먼트를 결과에 포함하지 않음으로써 비용을 절약한다.

```
# 백분위 구하는 집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs":{
    "stats_aggs":{
      "percentiles": {
        "field": "products.base_price",
        "percents": [
          25,
          50
        ]
      }
    }
  }
}

==== 결과 ====
  "aggregations" : {
    "stats_aggs" : {
      "values" : {
        "25.0" : 16.984375,
        "50.0" : 25.638580607476637
      }
    }
  }
```

  - 백분위집계(percentiles)로 정의한 것.
  - 백분위 집계는 필드의  특정 백분위에 속하는 데이터를 찾아준다. 
  - 중간값은 50 최댓값은 100이라고 할 수 있다.

### 2.2 cardinality aggragation(카디널리티 집계)

- 카디널리티 집계는 필드의 유니크한 값들의 개수를 확인하는 집계이다.
- 필드의 중복된 값들은 제외하고 유니크한 데이터의개수만 보여준다. 
- SQL의 DISTINCT COUNT와 비슷하다.
- 일반적으로 범주형 데이터에서 유니크한 데이터를 확인하는 용도로 많이 사용된다.

```
#카디널리티 집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs":{
    "cardi_aggs":{
      "cardinality": {
        "field": "day_of_week",
        "precision_threshold": 100
      }
    }
  }
}

==== 결과 ====
  "aggregations" : {
    "cardi_aggs" : {
      "value" : 7
    }
  }
```

  - `precision_threshold`는 정확도 수치, 값이 올라가면 정확도가 올라가는 대신, 시스템 리소스르르 많이 소모하고, 값이 작으면 정확도는 떨어지는대신 시스템 리소스를 덜 소모 한다. 
  - cardinality는 매우 적은 메모리로 집합의 원소 개수를 추정할 수 있는 HyperLogLog++알고리즘 기반으로 동작하며 precision_threshold 파라미터를 이용해 정확도와 리소스를 등가 교환한다. 
  - precision_threshold는 실제 결과보다 크게 잡아야 하지만 실제 결과를 모르기 때문에 precision_threshold값을 변경해 가면서 값이 변경되지 않는 임계점을 찾는 것 또한 방법이다.
  - precision_threshold의 기본값은 3000이며 최대 40000까지 값을 설정할 수 있다.

```
# 유니크한 필드값 찾기(용어 집계 요청)
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs":{
    "cardi_aggs":{
      "terms": {
        "field": "day_of_week"
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "cardi_aggs" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "Thursday",
          "doc_count" : 775
        },
        {
          "key" : "Friday",
          "doc_count" : 770
        },
        {
          "key" : "Saturday",
          "doc_count" : 736
        },
        {
          "key" : "Sunday",
          "doc_count" : 614
        },
        {
          "key" : "Tuesday",
          "doc_count" : 609
        },
        {
          "key" : "Wednesday",
          "doc_count" : 592
        },
        {
          "key" : "Monday",
          "doc_count" : 579
        }
      ]
    }
  }
```

### 2.3 검색 결과 내에서 집계

- day_of_week 가 Monday인 도큐먼트만으로 메트릭을 집계할 경우 다음과 같이 수행 가능하다.

```
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "query": {
    "term": {
      "day_of_week": "Monday"
    }
  },
  "aggs": {
    "query_aggs": {
      "sum": {
        "field": "products.base_price"
      }
    }
  }
}

==== 결과 ====
  "aggregations" : {
    "query_aggs" : {
      "value" : 45457.28125
    }
  }
```
  - 집계를 하기 전에 `query`를 통해 도큐먼트의 범위를 제한한다.
  - 용어 수주 ㄴ쿼리인 용어 쿼리를 이용해 Monday인 것만 골라내고 이를 통해 해당 필드의 합을 집계 한다.


## 3. Bucket 집계

특정 기준에 맞춰 도큐먼트를 그루핑 하는 역할을 하는 것이 버킷 집계이다.

- 버킷 집계의 종류

|버킷 집계|설명|
|-|-|
|histogram|숫자 타입 필드를 일정 간격으로 분류한다.|
|date_histogram|날짜/시간 타입 필드를 일정 날짜/시간 간격으로 분류한다.|
|range|숫자 타입 필드를 사용자가 지정하는 범위 간격으로 분류한다.|
|date_range|날짜/시간 타입 필드를 사용자가 지정하는 날짜/시간 간격으로 분류한다.|
|terms|필드에 많이 나타나는 용어(값)들을 기준으로 분류한다.|
|significant_terms|terms 버킷과 유사하나, 모든 값을 대상으로 하지 않고 인덱스 내 전체 문서 대비 현재 검색 조건에서 통계적으로 유의미한 값들을 기준으로 분류한다.|
|filters|각 그룹에 포함시킬 문서의 조건을 직접 지정한다. 이떄 조건은 일반적으로 검색에 사용되는 쿼리와 동일하다.|

### 3.1 히스토그램 집계

- 히스토그램 집계는 숫자 타입 필드를 일정 간격(interval)기준으로 구분해주는 집계이다. 

```
# 히스토그램 집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs": {
    "histogram_aggs": {
      "histogram": {
        "field": "products.base_price",
        "interval": 100
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "histogram_aggs" : {
      "buckets" : [
        {"key" : 0.0, "doc_count" : 4672},
        {"key" : 100.0, "doc_count" : 263},
        {"key" : 200.0, "doc_count" : 12},
        {"key" : 300.0, "doc_count" : 1},
        {"key" : 400.0, "doc_count" : 1},
        {"key" : 500.0, "doc_count" : 0},
        {"key" : 600.0, "doc_count" : 0},
        {"key" : 700.0, "doc_count" : 0},
        {"key" : 800.0, "doc_count" : 0},
        {"key" : 900.0, "doc_count" : 0},
        {"key" : 1000.0, "doc_count" : 1}
      ]
    }
  }
```

### 3.2 범위집계

- 히스토그램 집계는 간단하지만 각 버킷의 범위를 동일하게 지정할 수 밖에 없다는 단점이 있다.
- 이러한 경우 범위집계(range aggregation)을 이용해 버킷의 범위를 사용자가 지정할 수 있다.

```
# 범위집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs": {
    "range_aggs": {
      "range": {
        "field": "products.base_price",
        "ranges": [
          {"from": 0, "to": 50},
          {"from": 50, "to": 100},
          {"from": 100, "to":200},
          {"from": 200, "to": 1000}
        ]
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "range_aggs" : {
      "buckets" : [
        {
          "key" : "0.0-50.0",
          "from" : 0.0,
          "to" : 50.0,
          "doc_count" : 4341
        },
        {
          "key" : "50.0-100.0",
          "from" : 50.0,
          "to" : 100.0,
          "doc_count" : 1902
        },
        {
          "key" : "100.0-200.0",
          "from" : 100.0,
          "to" : 200.0,
          "doc_count" : 263
        },
        {
          "key" : "200.0-1000.0",
          "from" : 200.0,
          "to" : 1000.0,
          "doc_count" : 13
        }
      ]
    }
  }
```

### 3.3 용어 집계

- 용어집계는 필드의 유니크한 값을 기준으로 버킷을 나눌 때 사용된다.

```
# 용어집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs":{
    "term_aggs":{
      "terms": {
        "field": "day_of_week",
        "size": 7
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "term_aggs" : {
        "doc_count_error_upper_bound" : 0,
        "sum_other_doc_count" : 0,
        "buckets" : [
            {"key" : "Thursday", "doc_count" : 775},
            {"key" : "Friday", "doc_count" : 770},
            {"key" : "Saturday", "doc_count" : 736},
            {"key" : "Sunday", "doc_count" : 614},
            {"key" : "Tuesday", "doc_count" : 609},
            {"key" : "Wednesday", "doc_count" : 592},
            {"key" : "Monday", "doc_count" : 579}
        ]
    }
}
```

- day_of_week 필드의 값 기준으로 도큐먼트 수가 많은 순서대로 버킷을 요청한다.
- `size`는 기본값으로 10 이며 size로 지정한 숫자가 생성되는 버킷보다 작은 경우 size로 지정된 버킷만 보이게 된다.
- 다른 집계 결과에는 없고 용어 집계에서만 볼 수 있는 2개의 특별한 결과 값이 있다.
  - `doc_count_error_upper_bound`는 버킷이 잠재적으로 카운트 하지 못할 도큐먼트의 수
  - `sum_other_doc_count`는 버킷에는 있지만 size 때문에 보이지 않는 도큐먼트 수 이다.

#### 용어 집계가 정확하지 않은 이유
- 용어 집계 부정확도를 표시하는 이유는 분산 시스템의 집계 과정에서 발생하는 잠재적인 오류 가능성 때문이다.
- 분산시스템에서는 데이터를 여러 노드에서 분산하고 취합하는 과정에서 오류가 발생할 수 있다.
- 엘라스틱서치는 샤드에 도큐먼트를 저장하고 이를 분산하는데, size 설정값과 샤드 개수 등에 의해 집계에 오류가 발생할 수 있다.

#### 용어 집계 정확성 높이기
- 고속 처리를 위한 리소스와 속도 간 트레이드오프의 일환으로 리소스 소비량을 늘리면 정확도를 높일 수 있다.

```
# 용어 집계 오류 확인 요청
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs":{
    "term_aggs":{
      "terms": {
        "field": "day_of_week",
        "size": 6,
        "show_term_doc_count_error": true
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "term_aggs" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 579,
      "buckets" : [
        {
          "key" : "Thursday",
          "doc_count" : 775,
          "doc_count_error_upper_bound" : 0
        },
        {
          "key" : "Friday",
          "doc_count" : 770,
          "doc_count_error_upper_bound" : 0
        },
        ...
```
- 위와 같이 용어 집계를 요청할 때 show_term_doc_count_error 파라미터를 추가하면 doc_count_error_upper_bound 값을 버킷마다 확인할 수 있다.
- 해당 값이 0 이면 오류가 없다는 뜻이며 오류가 있을 경우 샤드 크기 파라미터를 늘리면 해결이 가능하다.

```
GET kibana_sample_data_ecommerce/_search
{
  "size":0,
  "aggs":{
    "term_aggs":{
      "terms": {
        "field": "day_of_week",
        "size": 6,
        "shard_size": 100
      }
    }
  }
}
```
- 샤드 크기는 용어 집계 과정에서 개별 샤드에서 집계를 위해 처리하는 개수를 의미한다.
- 샤드 크기를 크게 하면 정확도가 올라가는대신 리소스 사용량이 늘어 성능은 떨어진다.
- 샤드크기는 기본적으노 `size * 1.5 + 10` 으로 계산되는데 여기서 size는 버킷의 수이다.

## 4. 집계의 조합

RDBMS에서 GroupBy로 그루핑한 다음 통계 함수를 사용하는 것 처럼 버킷 집계와 메트릭 집계를 조합하면 다양한 그룹별 통계를 계산할 수 있다.

### 4.1 버킷 집계와 메트릭 집계
- 집계의 가장 기본적인 형태인 버킷 집계로 도큐먼트를 그루핑한 후 각 버킷에 집계별 메트릭 집계를 사용하는 방식으로 집계해 본다.

```
# 버킷 집계 후 메트릭 집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size": 0,
  "aggs": {
    "term_aggs": {
      "terms": {
        "field": "day_of_week",
        "size": 5
      },
      "aggs": {
        "avg_aggs": {
          "avg": {
            "field": "products.base_price"
          }
        }
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "term_aggs" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 1171,
      "buckets" : [
        {
          "key" : "Thursday",
          "doc_count" : 775,
          "avg_aggs" : {
            "value" : 34.68040897713688
          }
        },
        {
          "key" : "Friday",
          "doc_count" : 770,
          "avg_aggs" : {
            "value" : 34.665464386512184
          }
        },
        ...
```

- 요청을 해석하면 term_aggs 용어 집계를 통해 요일별로 버킷을 나누는데 상위 5개의 버킷만 사용하고, 메트릭 집계인 평균 집계(avg_aggs)는 용어집계(term_aggs)내부에서 호출되어 각 버킷 내부에서 products.base_price 필드의 평균값을 구한다.

```
# 버킷 집계 후 다수의 메트릭 집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size": 0,
  "aggs": {
    "term_aggs": {
      "terms": {
        "field": "day_of_week",
        "size": 5
      },
      "aggs": {
        "avg_aggs": {
          "avg": {
            "field": "products.base_price"
          }
        },
        "sum_aggs": {
          "sum": {
            "field": "products.base_price"
          }
        }
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "term_aggs" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 1171,
      "buckets" : [
        {
          "key" : "Thursday",
          "doc_count" : 775,
          "avg_aggs" : {
            "value" : 34.68040897713688
          },
          "sum_aggs" : {
            "value" : 58020.32421875
          }
        },
        ...
```

- 버킷 집계 내부에서 2개의 메트릭 집계가 동작하도록 한다. 

### 4.2 서브 버킷 집계

- sub bucket 서브 버킷은 버킷 안에서 다시 버킷 집계를 요청하는 집계이다.
- 버킷 집계로 버킷을 생성한 다음 버킷 내부에서 다시 버킷 집계를 하는데 트리구조와 유사하다.

```
# 서브 버킷 생성 요청
GET kibana_sample_data_ecommerce/_search
{
  "size": 0,
  "aggs": {
    "histogram_aggs": {
      "histogram": {
        "field": "products.base_price",
        "interval": 100
      },
      "aggs": {
        "term_aggs": {
          "terms": {
            "field": "day_of_week",
            "size": 2
          }
        }
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "histogram_aggs" : {
      "buckets" : [
        {
          "key" : 0.0,
          "doc_count" : 4672,
          "term_aggs" : {
            "doc_count_error_upper_bound" : 0,
            "sum_other_doc_count" : 3128,
            "buckets" : [
              {
                "key" : "Thursday",
                "doc_count" : 775
              },
              {
                "key" : "Friday",
                "doc_count" : 769
              }
            ]
          }
        },
        ...
```

- 해당 집계는 히스토그램 집계로 나누어진 버킷 내부에서 다시 day_of_week 필드를 유니크한 값 기준으로 구분하는데 2개의 버킷만 만든다.
- 히스토그램 집계는 products.base_price를 100단위 버킷으로 나눈 다음 그 안에 다시 요일별로 나눈 것.
- 서브버킷을 많이 만들수록 만들어야 하는 버킷의 수는 기하급수적으로 늘어나기 때문에 집계의 성능 및 클러스터에 과부하가 일어나지 않도록 주의해야 한다.

## 5. 파이프라인 집계

pipeline 집계는 이전 결과를 다음 단계에서 이용하는 파이프라인 개념을 차용한 것이다.

엘라스틱 파이프라인 집계는 이전 집계로 만들어진 결과를 입력으로 삼아 다시 집계하는 방식이다.

이 과정에는 부모집계(parent aggregation)과 형제 집계(sibling aggregation)라는 두가지 유형이 있다. 

두 집계 방식의 가장 큰 차이는 집계가 작성되는 위치이다.

- 부모집계(parent aggregation)
    ```
    {
        "aggs": {
            ...
            "aggs": {
                ...
                "부모 집계"
            }
        }
    }
    ```
    - 기존 집계 결과를 이용해 새로운 집계를 생성한다. 결과는 기존 집계 내부에서 가져온다.

- 형제 집계(sibling aggregation)
    ```
    {
        "aggs":{
            ...
            "aggs": {
                ...
            },
            "형제 집계"
        }
    }
    ```
    - 기존 집계를 참고해 집계를 수행한다, 결과는 기존 집계와 동일선상에서 나온다.

- 파이프라인 집계의 종류

|형제/부모 집계|집계 종류|설명|
|-|-|-|
|형제 집계|min_bucket|기존 집계 중 최솟값을 구한다.|
| |max_bucket|기존 집계 중 최댓값을 구한다.|
| |avg_bucket|기존 집계의 평균값을 구한다.|
| |sum_bucket|기존 집계의 총합을 구한다.|
| |stat_bucket|기존 집계의 min, max, sum, count, avg를 구한다.|
| |percentile_bucket|기존 집계의 백분위 값을 구한다.|
| |movinf_avg|기존 집계의 이동평균을 구한다. 단, 기존 집계는 순차적인 데이터 구조여야 한다.|
|부모 집계|derivative|기존 집계의 미분을 구한다.|
| |cumulative_sum|기존 집계의 누적 합을 구한다.|

### 5.1 부모집계

- 부모 집계는 이전 집계 결과를 입력으로 사용하는 집계이기 때문에 단독으로 사용할 수 없고 반드시 먼저 다른 집계가 있어야 한다.
- 부모 집계는 이전 집계 내부에서 실행한다. 또한, 그 결괏값도 기존 집계 내부에서 나타난다.

```
# 누적합을 구하는 부모 집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size": 0,
  "aggs": {
    "histogram_aggs": {
      "histogram": {
        "field": "products.base_price",
        "interval": 100
      },
      "aggs": {
        "sum_aggs": {
          "sum": {
            "field": "taxful_total_price"
          }
        },
        "cum_sum": {
          "cumulative_sum": {
            "buckets_path": "sum_aggs"
          }
        }
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "histogram_aggs" : {
      "buckets" : [
        {
          "key" : 0.0,
          "doc_count" : 4672,
          "sum_aggs" : {
            "value" : 348124.12890625
          },
          "cum_sum" : {
            "value" : 348124.12890625
          }
        },
        {
          "key" : 100.0,
          "doc_count" : 263,
          "sum_aggs" : {
            "value" : 44002.0
          },
          "cum_sum" : {
            "value" : 392126.12890625
          }
        },
        ...
```

- products.base_price를 100 기준으로 버킷을 나누거 각 버킷에서 taxful_total_price의 합을 구하는 집계로 구성되어 있다. 
- 부모 집계는 sum_aggs를 입력으로 받아 최종적으로 각 버킷의 누적합(cumulative_sum)을 구한다. 
- 파이프라인 집계는 받느시 버킷 경로를 입력해야 하는데 입력으로 사용했던 sum_aggs 집계를 넣으면 된다.

### 5.2 형제 집계

- 부모 집계는 기존 집계 내부에서 집계 작업을 하는 반면, 형제 집계는 기존 집계 외부에서 기존 집계를 이용해 집계 작업을 한다.

```
# 총합을 구하는 형제 집계 요청
GET kibana_sample_data_ecommerce/_search
{
  "size": 0,
  "aggs": {
    "term_aggs": {
      "terms": {
        "field": "day_of_week",
        "size": 2
      },
      "aggs": {
        "sum_aggs": {
          "sum": {
            "field": "products.base_price"
          }
        }
      }
    },
    "sum_total_price": {
      "sum_bucket": {
        "buckets_path": "term_aggs>sum_aggs"
      }
    }
  }
}

==== 결과 ====
"aggregations" : {
    "term_aggs" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 3130,
      "buckets" : [
        {
          "key" : "Thursday",
          "doc_count" : 775,
          "sum_aggs" : {
            "value" : 58020.32421875
          }
        },
        {
          "key" : "Friday",
          "doc_count" : 770,
          "sum_aggs" : {
            "value" : 58341.9765625
          }
        }
      ]
    },
    "sum_total_price" : {
      "value" : 116362.30078125
    }
  }
```
- day_of_week 필드 기준으로 요일 별 버킷을 나눈다. 
- sum_aggs에서 product.base_price 필드의 총합을 구한다.
- 다음으로 sum_backet 형제 집계를 이용해 기존 버킷별 합을 구한 집계를 다시 합친다.
- 파이프라인 집계의 버킷 경로는 `>` 기호를 통해 하위 경로를 표현할 수 있다.