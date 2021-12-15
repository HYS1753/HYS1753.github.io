---
author_profile: true
date: 2021-12-14
title: "Elastic Stack 3 - Elastic Search 검색"
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

# 엘라스틱 서치 검색

---

## 1. 쿼리 컨텍스트와 필터 컨텍스트

### 1.1 쿼리 컨텍스트
- 질의에 대한 유사도를 계산해 이를 기준으로 더 정확한 결과를 먼저 보여준다.
- 연관성에 따른 스코어 결과를 제공
- 쿼리 컨텍스트 실행 
```
GET kibana_sample_data_ecommerce/_search
{
"query": {
    "match": {
    "category": "clothing"
    }
}
}
```
- `_search`는 검색 쿼리를 위해 엘라스틱에서 제공하는 REST API이다.
- `match`는 전문 검색을 위한 쿼리로 역인덱싱된 용어를 검색할 때 사용한다.
- 위 쿼리의 뜻은, 해당 인덱스에 있는 category 필드의 역인덱스 테이블에 clothing용어가 있는 도큐먼트를 찾아달라는 의미이다.

 ```
결과

{
"took" : 128,
"timed_out" : false,
"_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
},
"hits" : {
    "total" : {
    "value" : 3927,
    "relation" : "eq"
    },
    "max_score" : 0.20545526,
    "hits" : [
    {
        "_index" : "kibana_sample_data_ecommerce",
        "_type" : "_doc",
        "_id" : "pxqLjn0B6maQmOVUN9BV",
        "_score" : 0.20545526,
        "_source" : {
        "category" : [
            "Men's Clothing"
        ],
```
- 해당 결과에서는 hits.total.value 가 3927개를 찾았음을 의미하며 
- hits 아래 _score가 큰 것 부터 검색한 결과가 출력된다.

### 1.2 필터 컨텍스트
- 유사도는 계산하지 않고 일치 여부에 따른 결과만을 반환한다.
- yes, no 의 결과를 제공
- 필터 컨텍스트 실행
```
GET kibana_sample_data_ecommerce/_search
{
"query": {
    "bool": {
    "filter": {
        "term": {
        "day_of_week": "Friday"
        }
    }
    }
}
}
```
- 필터 컨텍스트와 쿼리 컨텍스트를 구분하는 특별한 API가 있는것은 아니며 모두 search api를 사용한다.
- 필터 컨텍스트는 논리(bool)쿼리 내부의 filter타입에 적용된다. 
- 다음은 kibana_sample_data_ecommerce인덱스의 day_of_week가 Friday인 도큐먼트를 찾아달라는 요청이다.

```
결과

{
"took" : 134,
"timed_out" : false,
"_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
},
"hits" : {
    "total" : {
    "value" : 770,
    "relation" : "eq"
    },
    "max_score" : 0.0,
    "hits" : [
        {
        "_index" : "kibana_sample_data_ecommerce",
        "_type" : "_doc",
        "_id" : "vRqLjn0B6maQmOVUN9BV",
        "_score" : 0.0,
        "_source" : {
        "category" : [
            "Women's Shoes",
            "Women's Clothing"
        ],
        "currency" : "EUR",
        "customer_first_name" : "Mary",
        "customer_full_name" : "Mary Barber",
        "customer_gender" : "FEMALE",
        "customer_id" : 20,
        "customer_last_name" : "Barber",
        "customer_phone" : "",
        "day_of_week" : "Friday",
        "day_of_week_i" : 4,
        ...
```

- 위의 결과에서 hits.value 는 770개로 770개의 도큐먼트를 찾았다는 의미 이다.
- 또한 스코어를 계산하지 않았기 때문에 _score 부분이 0.0 으로 나온다. 단순히 day_of_week가 Friday인지 아닌지만 확인하기 때문이다.


## 2. 쿼리 스트링과 쿼리 DSL

### 2.1 쿼리 스트링

- 쿼리 스트링은 REST API의 URI 주소에 쿼리문을 작성하는 방식으로 실행해 볼 수 있어 간단하다. 
- 실행

```
GET kibana_sample_data_ecommerce/_search?q=customer_full_name:Mary
```
>  kibana_sample_data_ecommerce 인덱스 중 customer_full_name 필드에 'Mary' 라는 용어가 포함된 도큐먼트를 검색

- 결과
  
```
{
  "took" : 113,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 154,
      "relation" : "eq"
    },
    "max_score" : 3.4912553,
    "hits" : [
      {
        "_index" : "kibana_sample_data_ecommerce",
        "_type" : "_doc",
        "_id" : "qBqLjn0B6maQmOVUN9BV",
        "_score" : 3.4912553,
        "_source" : {
          "category" : [
            "Women's Clothing"
          ],
          "currency" : "EUR",
          "customer_first_name" : "Mary",
          "customer_full_name" : "Mary Bailey",
          ...
```
  - 154개의 도큐먼트를 검색할 수 있다.
  
### 2.2 쿼리 DSL

- REST API의 요청 본문 안에 JSON 형태로 쿼리를 작성한다.
- 쿼리 DSL은 엘라스틱 서치의 모든 쿼리 스펙을 지원하기 때문에 매우 강력하며 복잡한 쿼리를 구현할 수 있다. 
- 실행
  
```
GET kibana_sample_data_ecommerce/_search
{
  "query": {
    "match": {
      "customer_full_name": "Mary"
    }
  }
}
```

- 결과

```
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
      "value" : 154,
      "relation" : "eq"
    },
    "max_score" : 3.4912553,
    "hits" : [
      {
        "_index" : "kibana_sample_data_ecommerce",
        "_type" : "_doc",
        "_id" : "qBqLjn0B6maQmOVUN9BV",
        "_score" : 3.4912553,
        "_source" : {
          "category" : [
            "Women's Clothing"
          ],
          "currency" : "EUR",
          "customer_first_name" : "Mary",
          "customer_full_name" : "Mary Bailey",
        ...
```

## 3. 유사도 스코어

쿼리 컨텍스트는 엘라스틱에서 지원하는 다양한 스코어 알고리즘을 사용할 수 있다.

기본적으로는 BM25 알고리즘을 이용해 유사도 스코어를 제공한다.

유사도 스코어는 질의문과 도큐먼트의 유사도를 쵸현하는 값으로 스코어가 높을 수록 찾고자 하는 도큐먼트에 가깝다는 사실을 의미한다. 

- 다음은 쿼리를 요청하고 스코어가 어떤식으로 계산되는지 알아보기 위해 쿼리에 explain옵션을 추가해 본다.
- 쿼리 마지막 부분에 explain: true를 추가하면 쿼리 내부적인 최적화 방법과 어떤 경로로 검색되었으며 어떤 기준으로 스코어가 계산되었는지 확인 가능하다.

```
"hits" : [
      {
        "_shard" : "[kibana_sample_data_ecommerce][0]",
        "_node" : "57np52dJRmWZOTadCDMXgQ",
        "_index" : "kibana_sample_data_ecommerce",
        "_type" : "_doc",
        "_id" : "-RqLjn0B6maQmOVUOdLn",
        "_score" : 8.268259,
        "_source" : {
          "category" : [
            "Men's Shoes",
            "Men's Clothing"
          ],
          "currency" : "EUR",
          "customer_first_name" : "Robert",
          "customer_full_name" : "Robert Cross",
          "customer_gender" : "MALE",
          "customer_id" : 29,
          "customer_last_name" : "Cross",
          "customer_phone" : "",
          "day_of_week" : "Wednesday",
          "day_of_week_i" : 2,
          "email" : "robert@cross-family.zzz",
          "manufacturer" : [
            "Elitelligence",
            "Low Tide Media"
          ],
          "order_date" : "2021-12-08T11:51:22+00:00",
          "order_id" : 568578,
          "products" : [
            {
              "base_price" : 46.99,
              "discount_percentage" : 0,
              "quantity" : 1,
              "manufacturer" : "Elitelligence",
              "tax_amount" : 0,
              "product_id" : 17925,
              "category" : "Men's Shoes",
              "sku" : "ZO0520005200",
              "taxless_price" : 46.99,
              "unit_discount_amount" : 0,
              "min_price" : 24.43,
              "_id" : "sold_product_568578_17925",
              "discount_amount" : 0,
              "created_on" : "2016-12-14T11:51:22+00:00",
              "product_name" : "Boots - tan",
              "price" : 46.99,
              "taxful_price" : 46.99,
              "base_unit_price" : 46.99
            },
            {
              "base_price" : 32.99,
              "discount_percentage" : 0,
              "quantity" : 1,
              "manufacturer" : "Low Tide Media",
              "tax_amount" : 0,
              "product_id" : 16500,
              "category" : "Men's Clothing",
              "sku" : "ZO0421104211",
              "taxless_price" : 32.99,
              "unit_discount_amount" : 0,
              "min_price" : 16.82,
              "_id" : "sold_product_568578_16500",
              "discount_amount" : 0,
              "created_on" : "2016-12-14T11:51:22+00:00",
              "product_name" : "Casual Cuffed Pants",
              "price" : 32.99,
              "taxful_price" : 32.99,
              "base_unit_price" : 32.99
            }
          ],
          "sku" : [
            "ZO0520005200",
            "ZO0421104211"
          ],
          "taxful_total_price" : 79.98,
          "taxless_total_price" : 79.98,
          "total_quantity" : 2,
          "total_unique_products" : 2,
          "type" : "order",
          "user" : "robert",
          "geoip" : {
            "country_iso_code" : "SA",
            "location" : {
              "lon" : 45,
              "lat" : 25
            },
            "continent_name" : "Asia"
          },
          "event" : {
            "dataset" : "sample_ecommerce"
          }
        },
        "_explanation" : {
          "value" : 8.268259,
          "description" : "weight(products.product_name:pant in 594) [PerFieldSimilarity], result of:",
          "details" : [
            {
              "value" : 8.268259,
              "description" : "score(freq=1.0), computed as boost * idf * tf from:",
              "details" : [
                {
                  "value" : 2.2,
                  "description" : "boost",
                  "details" : [ ]
                },
                {
                  "value" : 7.1974354,
                  "description" : "idf, computed as log(1 + (N - n + 0.5) / (n + 0.5)) from:",
                  "details" : [
                    {
                      "value" : 3,
                      "description" : "n, number of documents containing term",
                      "details" : [ ]
                    },
                    {
                      "value" : 4675,
                      "description" : "N, total number of documents with field",
                      "details" : [ ]
                    }
                  ]
                },
                {
                  "value" : 0.52217203,
                  "description" : "tf, computed as freq / (freq + k1 * (1 - b + b * dl / avgdl)) from:",
                  "details" : [
                    {
                      "value" : 1.0,
                      "description" : "freq, occurrences of term within document",
                      "details" : [ ]
                    },
                    {
                      "value" : 1.2,
                      "description" : "k1, term saturation parameter",
                      "details" : [ ]
                    },
                    {
                      "value" : 0.75,
                      "description" : "b, length normalization parameter",
                      "details" : [ ]
                    },
                    {
                      "value" : 5.0,
                      "description" : "dl, length of field",
                      "details" : [ ]
                    },
                    {
                      "value" : 7.3161497,
                      "description" : "avgdl, average length of field",
                      "details" : [ ]
                    }
                  ]
                }
              ]
            }
          ]
        }
      },
      ...
```

### 3.1 스코어 알고리즘 BM25

- BM25는 검색 추천에 많이 사용되는 알고리즘으로 TF(Term Frequency), IDF(Inverse Document Frequency) 개념에 문서 길이를 고려한 알고리즘이다. 
- 검색어가 문서에서 얼마나 자주 나타나는지, 검색어가 문서 내에서 중요한 용어인지 등을 판단하는 근거를 제공한다.

#### IDF 계산

- 문서 빈도(Documnent Frequency)는 특정 용어가 얼마나 자주 등장했는지를 의미하는 지표이다.
- 하지만 일반적으로 to, the와 같이 관사, 부사 등은 자주 나타나지만 중요한 용어가 아닐 수 있다.
- 따라서 도큐먼트 내에서 발생 빈도가 적을 수록 가중치를 높게 주는 것을 IDF라 한다. 
- IDF 계산식은 다음과 같다.
  
  `idf, computed as log(1+(N-n+0.5)/(n+0.5)) from:",`
    - `n`은 검색했던 용어(Term)가 몇개의 도큐먼트에 있는지 알려주는 값. 
      - 위에서 n은 3
    - `N`은 인덱스의 전체 도큐먼트 수 이다. 
      - 위에서 N은 4657
  
#### TF 계산

- 용어빈도(Term Frequency)는 특정 용어가 하나의 도큐먼트에 얼마나 많이 등장했는지를 의미하는 지표
- 일반적으로 특정 용어가 도큐먼트에서 많이 반복되었다면 그 용어는 도큐먼트의 주제와 연관되어 있을 확률이 높다.
- TF 계산식은 다음과 같다
  - `tf, computed as freq / (freq + k1 * (1 - b + b * dl / avgdl)) from:"`
  - `freq` : 도큐먼트 내에서 용어가 나온 횟수 
  - `k1`, `b`는 알고리즘을 정규화 하기 위한 가중치로 엘라스틱 서치가 디폴트로 취하는 상수
  - `dl` : 필드의 길이
  - `avgdl`: 전체 도큐먼트에서 평균 필드 길이
    - dl이 작고 avgdl이 클수록 TF 값이 크게 나온다. 즉, 짧은 글에서 찾고자 하는 용어가 포함될 수록 가중치 높다는 뜻.

#### 최종 스코어

- IDF와 TF 그래고 boost 변수를 곱하면 된다. Boost는 엘라스틱이 지정한 고정값으로 2.2로 고정이다.


## 4. 쿼리

- 쿼리 종류
  - 리프 쿼리(leaf query)
    - 매치(match) 쿼리
    - 용어(term) 쿼리
    - 범위(range) 쿼리
  - 복합 쿼리(compound query)
    - 논리(bool) 쿼리
  
### 4.1 전문 쿼리와 용어 수준 쿼리

- 전문 쿼리
  - 전문 검색 시 사용하며, 검색 할 필드는 인덱스 매핑 시 텍스트 타입으로 매핑한다.
  - 예시
    
    ```
    PUT full_index/_doc/1
    {
      "contents": "Elastic Stack Hello World."
    }
    ```

  - 위의 도큐먼트를 인덱싱 하면 conetents 필드는 텍스트 타입으로 매핑되며 분석기에 의해 [elastic, stack, hello, world] 로 토큰화 된다.

    ```
    GET full_index/_search
    {
      "match": {
        "contents": "elastic"
      }
    }
    ```

  - 이후 위와 같이 매치 쿼리를 사용해 전문 검색을 하게 되면 검색어인 elastic이 분석기에 의해 토큰화 되고 토큰화된 도큐먼트 용어([elastic, stack, hello, world])가 매칭되어 스코어를 계산하고 검색을 한다. 
  - 즉, 전문 검색은 블로그처럼 텍스트가 많은 필드에서 특정용어를 검색할 때 사용된다. (구글, 네이버에서 검색어 이용 검색을 전문 검색으로 함.)
  - 전문 쿼리의 종류
    - match query
    - match phrase query
    - multi-match query
    - query string query
    - 등등

- 용어 수준 쿼리
  - 정확히 일치하는 용어를 찾기 위해 사용되며, 인덱스 매핑 시 필드를 키워드 타입으로 매핑한다.
  - 예시

    ```
    PUT term_index/_doc/1
    {
      "category": "Elastic"
    }
    ```

  - 위와 같이 category 필드를 갖는 도큐먼트를 인덱싱하고 키워드 타입으로 매핑하며, 이때 분석기는 사용하지 않고 그대로 사용한다.

    ```
    GET term_index/_search
    {
      "term":{
        "category":"elastic"
      }
    }
    ```

  - 위와 같이 용어(Term) 쿼리를 사용하는데 이때 또한 분석기를 거치지 않고 그대로 검색한다. 이렇게 도큐먼트 용어를 매칭하는데 위는 대소문자 차이로 매칭이 안된다.
  - 즉, 용어수준 쿼리는 전문 검색과 달리 정확한 용어를 검색할 때 사용한다. 
  - 일반적으로 숫자, 날짜, 범주형 데이터를 정확하게 검색할 때 사용되면 RDB의 WHERE절과 비슷한 역할을 한다. 
  - 용어 수준 쿼리 종류
    - term query
    - terms query
    - fuzzy query

### 4.2 match query(매치 쿼리)

- 대표적인 전문 쿼리이자, 전문 쿼리의 가장 기본이 되는 쿼리이다. 
- 전체 텍스트 중에서 특정 용어나 용어들을 검색할 때 사용한다.
- 매치 쿼리를 사용하기 위해 검색하고 싶은 필드(컬럼)을 알아야 한다. 
- 모를 시 쿼리를 작성할 수 없으므로 `GET index_name/_mapping` 으로 인덱스에 포함된 필드가 어떤 것이 있는지 확인 후 작성한다.
- 다음은 kibana_sample_data_ecommerce 예제 데이터를 통한 매치 쿼리 예시이다.

  ```
  ==== 쿼리 ====
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["customer_full_name"],
    "query": {
      "match": {
        "customer_full_name": "Mary"
      }
    }
  }

  ==== 결과 ====
  "hits" : [
        {
          "_index" : "kibana_sample_data_ecommerce",
          "_type" : "_doc",
          "_id" : "qBqLjn0B6maQmOVUN9BV",
          "_score" : 3.4912553,
          "_source" : {
            "customer_full_name" : "Mary Bailey"
          }
        },
        {
          "_index" : "kibana_sample_data_ecommerce",
          "_type" : "_doc",
          "_id" : "7hqLjn0B6maQmOVUN9BV",
          "_score" : 3.4912553,
          "_source" : {
            "customer_full_name" : "Mary Allison"
          }
        },
        ...
  ```

  - 위의 쿼리의 `"_source"` 는 뒤에 적힌 필드만 보여달라는 요청이며, query 부분을 통해 customer_full_name 필드에서 Mary 라는 용어가 있는 도큐먼트를 찾아달라 요청한다.
  - 전문 쿼리의 경우 분석기를 통해 토큰화 되기 때문에 Mary는 mary로 토큰화 된다.

  ```
  ==== 쿼리 ====
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["customer_full_name"],
    "query": {
      "match": {
        "customer_full_name": "mary bailey"
      }
    }
  }

  ==== 결과 ====
  "hits" : [
        {
          "_index" : "kibana_sample_data_ecommerce",
          "_type" : "_doc",
          "_id" : "qBqLjn0B6maQmOVUN9BV",
          "_score" : 9.155506,
          "_source" : {
            "customer_full_name" : "Mary Bailey"
          }
        },
        {
          "_index" : "kibana_sample_data_ecommerce",
          "_type" : "_doc",
          "_id" : "LRqLjn0B6maQmOVUN9FW",
          "_score" : 5.6642504,
          "_source" : {
            "customer_full_name" : "Abd Bailey"
          }
        },
        ...
  ```

  - 위의 쿼리는 customer_full_name 필으데어 mary와 bailey 두 용어를 검색하는 매치 쿼리 요청이다. 
  - 검색어인 mary bailey는 분석기에 의해 각각의 단어로 토큰화 되고 각 용어간 공백은 OR로 인식한다. 
  - 즉, customer_full_name 필드에 mary 나 bailey가 하나라도 포함된 도큐먼트가 있다면 매칭되었다고 판단한다.
  
  ```
  ==== 쿼리 ====
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["customer_full_name"],
    "query": {
      "match": {
        "customer_full_name": {
          "query": "mary bailey",
          "operator": "and"
        }
      }
    }
  }

  ==== 결과 ====
  "hits" : {
      "total" : {
        "value" : 3,
        "relation" : "eq"
      },
      "max_score" : 9.155506,
      "hits" : [
        {
          "_index" : "kibana_sample_data_ecommerce",
          "_type" : "_doc",
          "_id" : "qBqLjn0B6maQmOVUN9BV",
          "_score" : 9.155506,
          "_source" : {
            "customer_full_name" : "Mary Bailey"
          }
        },
        ...
  ```

  - mary bailey 로 검색하지만 OR가 아닌 둘다 모두 포함된 도큐먼트를 찾기 위해선 위의 쿼리 처럼 operator에 and를 붙여서 검색한다.
  - operator가 명시되어 있지않으면 기본값이 OR 이기 떄문에 위의 쿼리에서 전체 검색 된 것.

### 4.3 match phrase query(매치 프레이즈 쿼리)

- 전문 쿼리의 한 종류로 주로 구(phrase)를 검색할 때 사용한다. 
- 구는 동사가 아닌 2개 이상의 단어가 연결되어 만들어지는 단어이다. 
- "엘라스틱 스택" 과 같이 여러 단어가 모여서 뜻을 이루는 단어이며, 순서 또한 중요하다. "스택 엘라스틱"을 검색하는 것은 아니기 때문
- 매치 프레이즈 쿼리 예시

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["customer_full_name"],
    "query": {
      "match_phrase": {
        "customer_full_name": 
        "mary bailey"
      }
    }
  }
  ```

    - 위의 매치 프레이즈 쿼리는 매치 쿼리와 같이 mary, bailey가 토큰화 되는 것 까지는 같지만, 검색에 사용된 용어 모두가 포함되면서 순서까지 일치해야 한다.
    - 즉, bailey mary는 검색 대상이 아님
    - 매치 프레이즈 쿼리는 검색 시 많은 리소스를 요구하기 떄문에 자주 사용하지 않는것을 지향한다.


### 4.4 term qeury(용어 쿼리)

- 용어 쿼리는 용어 수준 쿼리의 대표적인 쿼리이다. 
- 사용방법은 매치 쿼리와 비슷하지만 매치 쿼리는 전문검색으로 분석기에 의해 토큰화되고 매칭을 수행했지만, 용어검색은 분석기를 통한 토큰화를 진행하지 않는다.
- 즉, mary bailey라고 검색하면 정확히 해당 용어가 있는 경우에만 매칭이 된다. 
- 분석기가 사용되지 않으므로 대소문자 등도 정확하게 일치 해야 한다.
- 용어 쿼리 예시
  
  ```
  ==== 쿼리 ====
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["customer_full_name"],
    "query": {
      "term": {
        "customer_full_name": 
        "Mary Bailey"
      }
    }
  }

  ==== 결과 ====
  "hits" : {
      "total" : {
        "value" : 0,
        "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
    }
  ```

  - 위의 용어 쿼리를 수행하면 도큐먼트를 하나도 찾을 수 없다. 이는 customer_full_name 필드가 텍스트로 매핑되어 있어 토큰화 되어 있기 때문에 Mary Bailey 전체 용어가 검색되지 않은 것이다. 
  - 또한 Mary 만 검색하여도 분석기에 의해 대문자가 소문자로 변결되어 mary로 매핑되어 검색할 수 없다.
  - 따라서 강제하진 않지만 용어 쿼리는 키워드 타입으로 매핑된 필드에서 사용되어야 한다.

  ```
  ==== 쿼리 ====
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["customer_full_name"],
    "query": {
      "term": {
        "customer_full_name.keyword": 
        "Mary Bailey"
      }
    }
  }

  ==== 결과 ====
  "hits" : {
      "total" : {
        "value" : 3,
        "relation" : "eq"
      },
      "max_score" : 7.1974354,
      "hits" : [
        {
          "_index" : "kibana_sample_data_ecommerce",
          "_type" : "_doc",
          "_id" : "qBqLjn0B6maQmOVUN9BV",
          "_score" : 7.1974354,
          "_source" : {
            "customer_full_name" : "Mary Bailey"
          }
        },
        ...
  ```

  - 위와 같이 customer_full_name.keyword 필드에서 Mary Bailey를 검색하면 의도대로 검색되는 것을 확인할 수 있다.

### 4.5 terms query(용어들 쿼리)

- 용어 수준 쿼리의 일종으로 여러 용어들을 검색한다.
- 키워드 타입으로 매핑된 필드에서 사용해야 하며, 분석기를 거치지 않았기 때문에 대소문자도 신경써야 한다.
- 용어들 쿼리 예시

  ```
  ==== 쿼리 ====
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["day_of_week"],
    "query": {
      "terms": {
        "day_of_week": 
        ["Monday", "Sunday"]
      }
    }
  }

  ==== 결과 ====
  "hits" : {
      "total" : {
        "value" : 1193,
        "relation" : "eq"
      },
      "max_score" : 1.0,
      "hits" : [
        {
          "_index" : "kibana_sample_data_ecommerce",
          "_type" : "_doc",
          "_id" : "pxqLjn0B6maQmOVUN9BV",
          "_score" : 1.0,
          "_source" : {
            "day_of_week" : "Monday"
          }
        },
        ...
  ```

  - 해당 인덱스의 day_of_week필드에서 월요일과 일요일인 도큐먼트 전체를 검색한다.

### 4.6 multi match query(멀티 매치 쿼리)

- 여러개 의 필드에서 검색하기위한 멀티 매치 쿼리는 전문 검색 쿼리의 일종이다.
- 지금까지 쿼리를 이용해 검색할 때는 반드시 필드명을 적어야 했었다. 그 이유는 엘라스틱 서치가 필드를 기준으로 찾으려는 용어나 구절을 검색하기 때문이다.
- 하지만 검색하고자 하는 용어나 구절이 정확히 어떤 필드에 있는지 모르는 경우나 여러개의 필드에서 검색할 필요가 있을 경우가 있고, 이떄 멀티 매치 쿼리를 사용한다.
- 멀티 매치 쿼리 예시

  ```
  ==== 쿼리 ====
  GET kibana_sample_data_ecommerce/_search?explain=true
  {
    "_source": ["customer_first_name", "customer_last_name", "customer_full_name"],
    "query": {
      "multi_match": {
        "query": "mary",
        "fields": [
          "customer_full_name",
          "customer_first_name",
          "customer_last_name"
          ]
      }
    }
  }
  
  ==== 결과 ====
  "hits" : {
    "total" : {
      "value" : 154,
      "relation" : "eq"
    },
    ...
  ```

  - `_search?explain=true` 은 개별 필드의 스코어가 어떻게 계산되었는지 알 수 있고, 어떻게 선정되어 ㅆ는지 또한 알 수 있다.
  - 위의 쿼리에서 3개의 필드에 대해 mary라는 용어로 매치 쿼리를 하고 3개의 필드에서 개별 스코어를 구한 다음에 그 중 가장 큰 값을 대표 스코어로 한다. 

  ```
  GET kibana_sample_data_ecommerce/_search?explain=true
  {
    "_source": ["customer_first_name", "customer_last_name", "customer_full_name"],
    "query": {
      "multi_match": {
        "query": "mary",
        "fields": [
          "customer_*_name"
          ]
      }
    }
  }
  ```

  - 검색하려는 필드가 너무 많을 때 필드명에 *(와일드 카드)를 사용해 이름이 유사한 복수의 필드를 검색할 수 있다.

#### boosting(부스팅)

- 부스팅 기법이란 여러개의 필드 중 특정 필드에 가중치를 두는 방법으로 멀티 매치 쿼리에서 자주 사용된다.
- 부스팅 기법 예시
  ```
  GET kibana_sample_data_ecommerce/_search?explain=true
  {
    "_source": ["customer_first_name", "customer_last_name", "customer_full_name"],
    "query": {
      "multi_match": {
        "query": "mary",
        "fields": [
          "customer_full_name^2",
          "customer_first_name",
          "customer_last_name"
          ]
      }
    }
  }
  ```
  - 위의 쿼리에서 처럼 full_name^2로 특정 필드에 스코어 값을 n 배 해주는 효과를 가지고 있다.

### 4.7 range query(범위 쿼리)

- 특정 날짜나 숫자의 범위를 지정해 범위 안에 포함된 데이터들을 검색할 때 사용한다.
- 날짜/숫자/IP 타입에는 범위쿼리가 가능하지만, 문자형, 키워드 타입에는 범위쿼리를 사용할 수 없다.
- 범위쿼리 예시

  ```
  GET kibana_sample_data_flights/_search
  {
    "query": {
      "range": {
        "timestamp": {
          "gte": "2021-12-15",
          "lt": "2021-12-16"
        }
      }
    }
  }
  ```

  - 이는 2021-12-15 00:00:00 부터 2021-12-15 13:59:59 까지의 데이터를 찾는것.
  - 이때, 쿼리에서 사용되는 날짜/시간 포멧과 저장된 날짜/시간 표멧이 맞아야 검색이 가능하다.
  - 즉, 2021/12/15로 검색하면 오류 발생
  - 검색 범위를 지정하는 파라미터
  
  |파라미터|설명|
  |-|-|
  |gte|기준 값보다 크거나 같은 값|
  |gt|기준 값보다 큰 값|
  |lte|기준 값보다 작거나 같은 값|
  |lt|기준 값보다 작은 값|

- 범위 데이터 타입
  - 엘라스틱 서치 데이터 타입 중 범위 데이터 타입이 있다. integer_range, float_range, long_range, double_range, date_range, ip_range의 총 6가지의 타입을 지원한다. 
  - 예제
    ```
    # 날짜 시간 범위 타입을 갖는 인덱스 생성
    PUT range_test_index
    {
      "mappings":{
        "properties": {
          "test_date":{
            "type":"date_range"
          }
        }
      }
    }

    # 날짜 시간 범위를 갖는 도큐먼트 인덱싱
    PUT range_test_index/_doc/1
    {
      "test_date":{
        "gte": "2021-12-01",
        "lt": "2021-12-08"
      }
    }

    # 날짜 시간 범위 데이터 필드에 날짜 시간 데이터 인덱싱
    PUT range_test_index/_doc/2
    {
      "test_date": "2021-12-01"
    }
    -> 오류 발생 test_date는 날짜 시간 범위타입이기 때문에 기본 날짜 시간 타입(date)는 다르다.

    # 범위 검색 
    GET range_test_index/_search
    {
      "query": {
        "range": {
          "test_date": {
            "gte": "2021-12-01",
            "lte": "2021-12-13",
            "relation": "within"
          }
        }
      }
    }
    ```

    - relation에 들어갈 수 있는 값들 

    |값|설명|
    |-|-|
    |intersects(기본값)|쿼리 범위 값이 도큐먼트의 범위 데이터를 일부라도 포함하기만 하면 됨|
    |contains|도큐먼트의 범위 데이터가 쿼리 범위 값을 모두 포함해야 한다.|
    |within|도큐먼트 범위 데이터가 쿼리 범위 값 내에 전부 속해야 한다.|

### 4.8 bool query(논리 쿼리)

- 논리 쿼리는 복합쿼리(compound query)로 앞서 수행한 쿼리들에 조합할 수 있다.
- 논리 쿼리 작성 포멧과 지원하는 타입들
```
GET index_name/_search
{
  "query":{
    "bool":{
      "must":[{쿼리 문}, ...],
      "must_not":[{쿼리 문}, ...],
      "should":[{쿼리 문}, ...],
      "filter":[{쿼리 문}, ...],
    }
  }
}
```
- 논리 쿼리 타입

|타입|설명|
|-|-|
|must|쿼리를 실행하여 참인 도큐먼트를 찾는다. 복수의 쿼리를 실행하면 AND연산 수행|
|must_not|쿼리를 실행하여 거짓인 도큐먼트를 찾는다. 다른 타입과 같이 사용할 경우 도큐먼트에서 제외한다.|
|should|단독으로 사용 시 쿼리를 실행하여 참인 도큐먼트를 찾는다. 복수의 쿼리를 실행하면 OR 연산을 한다. 다른 타입과 같이 사용할 경우 스코어에만 활용한다.|
|filter|쿼리를 실행하여 yes, no 형식의 필터 컨텍스트를 수행한다.|


- must type

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "query": {
      "bool": {
        "must": {
          "match":{"customer_first_name" : "mary"}
        }
      }
    }
  }
  ```

  - 지금 상태로는 customer_first_name 필드에서 mary인 모든 도큐먼트를 찾지만 복수개의 쿼리를 AND로 검색하기 위해선 []로 묶어 준다.

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "query": {
      "bool": {
        "must": [
          {"match":{"customer_first_name" : "mary"}},
          {"term":{"day_of_week": "Sunday"}}
        ]
      }
    }
  }
  ```

- must_noy type

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "query": {
      "bool": {
        "must_not": {
          "match": {"customer_full_name": "mary"}
        }
      }
    }
  }
  ```

  - must_not 타입은 거짓인 도큐먼트를 찾는다. 즉 위의 쿼리에서는 mary가 포함되지 않은 모든 도큐먼트를 검색한다.
  - 이를 사용하면 특정 조건의 쿼리를 제외할 수 있다. 다음은 customer_full_name에 mary가 들어간 도큐먼트를 모두 찾고 그 중에서 cusomer_last_name에 bailey가 들어간 도큐먼트를 제외하는 쿼리이다.

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "query": {
      "bool": {
        "must": {
          "match": {"customer_first_name": "mary"}
        }, 
        "must_not": {
          "term": {"customer_last_name": "bailey"}
        }
      }
    }
  }
  ```

- should type

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["day_of_week", "customer_full_name"], 
    "query": {
      "bool": {
        "should": [
          {"term": {"day_of_week": "Sunday"}},
          {"match": {"customer_full_name": "mary"}}
        ]
      }
    }
  }
  ```

  - should 타입에 하나의 쿼리 단독으로 사용한다면 must 타입과 같은 결과를 얻을 수 있지만 복수개의 쿼리를 사용할 경우 OR의 효과를 가진다.(AND는 MUST 타입)


- filter type

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": ["products.base_price"], 
    "query": {
      "bool": {
        "filter": {
          "range": {
            "products.base_price": {
              "gte": 30,
              "lte": 60
            }
          }
        }
      }
    }
  }
  ```

  - filter는 must와 같은 동작을 하지만 필터 컨텍스트로 동작하기 때문에 유사도 스코어에 영향을 미치지 않는다. 즉 예, 아니오의 두가지 결과만 제공할 뿐 유사도를 고려하지 않는다.


### 4.9 패턴 검색

- 검색하려는 검색어가 길거나 검색어를 정확히 알지 못하지만 대략적인 키워드나 몇개의 알파벳만 낭고 있다면 패턴을 이용해 검색할 수 있다.
- 패턴 검색에는 `와일드카드 쿼리`, `정규식 쿼리` 두가지 방법이 존재한다. 
- 두 쿼리 모두 용어 수준 쿼리에 해당하기 때문에 분석기에 의해 분리된 용어를 찾기 위한 쿼리이다. 

#### 와일드카드 쿼리
- 와일드 카드는 용어를 검색할 때 * 과 ?를 사용할 수 있다.
- `*`은 공백까지 포함하여 글자 수에 상관없이 모든 문자를 매칭할 수 있다.
- `?`은 오직 한 문자만 매칭할 수 있다.
- 검색하려는 용어의 맨 앞에 * 또는 ?를 사용하면 매우 느려저 맨앞에는 와일드 카드를 두지 않는다.
- 예시

  ```
  GET kibana_sample_data_ecommerce/_search
  {
    "_source": "customer_full_name",
    "query": {
      "wildcard": {
        "customer_full_name.keyword": "M?r*"
        }
      }
    }
  }
  ```

#### 정규식 쿼리
- 정규식은 특정한 패턴을 가진 문자열을 표현하기 위한 형식 언어이다.
- `.`
  - 점(`.`)은 하나의 문자를 의미하고 어떤 문자가 와도 상관없이 매칭되었다고 판단.
- `+`
  - `+` 기호 앞 문자와 같은 문자가 한번이상 반복되면 매칭되었다고 판단.
- `*`
  - `*` 기호 앞 문자와 같은 문자가 0번 혹은 여러번 반복되면 매칭되었다고 판단.
- `?`
  - `?` 기호 앞문자와 같은 문자가 0번 호는 한번 나타나면 매칭되었다고 판단.
- `()`
  - `()` 기호는 문자를 그루핑하여 반복되는 문자들을 매칭 시킨다. 
- `[]`
  - `[]` 기호는 문자를 클래스화 하여 특정 범위의 문자들을 매칭한다. [a-z], [^k]