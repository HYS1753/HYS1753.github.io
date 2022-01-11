---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 5"
categories: 
    - Spring
tag: 
    - Java
    - Spring
    - SpringMVC

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Spring MVC + Gradle Project 5 (DB 사용 2)

---

## **1. Mybatis 설정 및 사용**

### 1. Spring MVC + Gradle Project 4 (DB 사용 1)에 이어서 작성

### 2. PostgreSQL TEST 테이블 및 데이터 생성

```
create table springmvc.test (
	english varchar(200) not null,
	korean varchar(200),
	CONSTRAINT test_pk PRIMARY KEY (english)
)

insert into springmvc.test values('culture', '문화, 교양');
insert into springmvc.test values('experience', '경험');
insert into springmvc.test values('education', '교육');
insert into springmvc.test values('symbol', '상징');
insert into springmvc.test values('effect', '결과, 영향, 효과');
insert into springmvc.test values('liberty', '자유');
insert into springmvc.test values('affair', '사건, 일');
insert into springmvc.test values('comfort', '안락, 위안');
insert into springmvc.test values('tradition', '전통, 전설');
insert into springmvc.test values('subject', '학과, 주제, 주어');

select * from test
```
- test Table

    |english|korean|
    |-|-|
    |culture|문화, 교양|
    |experience|경험|
    |education|교육|
    |symbol|상징|
    |effect|결과, 영향, 효과|
    |liberty|자유|
    |affair|사건, 일|
    |comfort|안락, 위안|
    |tradition|전통, 전설|
    |subject|학과, 주제, 주어|

### 3. 