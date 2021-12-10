---
author_profile: true
date: 2021-08-20
title: "Elastic Stack - Elastic Search 검색"
categories: 
    - Elastic Stack
tag: 
    - 기본

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
    - kibana_sample_data_ecommerce 인덱스 중 customer_full_name 필드에 'Mary' 라는 용어가 포함된 도큐먼트를 검색

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
- 
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
  `tf, computed as freq / (freq + k1 * (1 - b + b * dl / avgdl)) from:"`
  - `freq` : 도큐먼트 내에서 용어가 나온 횟수 
  - `k1`, `b`는 알고리즘을 정규화 하기 위한 가중치로 엘라스틱 서치가 디폴트로 취하는 상수
  - `dl` : 필드의 길이
  - `avgdl`: 전체 도큐먼트에서 평균 필드 길이
    - dl이 작고 avgdl이 클수록 TF 값이 크게 나온다. 즉, 짧은 글에서 찾고자 하는 용어가 포함될 수록 가중치 높다는 뜻.

#### 최종 스코어

- IDF와 TF 그래고 boost 변수르 ㄹ곱하면 된다. Boost는 엘라스틱이 지정한 고정값으로 2.2로 고정이다.


## 4. 쿼리

쿼리 종류
- 리프 쿼리(leaf query)
  - 매치(match) 쿼리
  - 용어(term) 쿼리
  - 범위(range) 쿼리
- 복합 쿼리(compound query)
  - 논리(bool) 쿼리
  
### 4.1 전문 쿼리와 용어 수준 쿼리