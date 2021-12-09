---
author_profile: true
date: 2021-08-20
title: "SQLD 6 - SQL(DDL)"
categories: 
    - SQL
tag: 
    - ORACLE
    - SQLD

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# SQLD - SQL 문(DDL) - 수정 필요

---

## 1. DDL(Data Definition Language)

### 대표적인 데이터 형
- CHAR(L) : 
  - 고정길이 문자열, 
  - 고정길이 이므로 저장되는 값의 길이가 L보다 작을 경우 공백으로 채움
- VARCHAR2(L) : 
  - 가변길이 문자열이다. 
  - L 만큼의 최대 길이를 가진다.    
  - L 보다 작을경우 입력하는 값 만큼 공간을 차지한다.
- NUMBER(L,D) :
  - 정수 및 실수를 저장한다.
  - L값은 전체 자리수, D 값은 소수점 자리수 이다.
  - 만약 NUMBER(12, 2)이면 9999999999.99까지 저장 가능

### CREATE TABLE

- CREATE 구조
```sql
CREATE TABLE TB_RN_TMP( -- 도로명 임시
    RN_CD VARCHAR2(12) NOT NULL -- 도로명 코드
    , RN VARCHAR2(150) NOT NULL -- 도로명
)
```

- 부모 테이블(제약조건 추가)

```sql
CREATE TABLE TB_SUBWAY_STATN_TMP(       --지하철역 임시
    SUBWAY_STATN_NO CHAR(6) NOT NULL    --지하철역번호
    , LN_NM VARCHAR2(50) NOT NULL       --노선명
    , STATN_NM VARCHAR(50) NOT NULL     --역명
    -- 지하철 역 번호를 pk로 지정
    , CONSTRAINT PK_TB_SUBWAY_STATN_TMP PRIMARY KEY(SUBWAY_STATN_NO)
);
```

- 자식 테이블

```sql
CREATE TABLE TB_SUBWAY_STATN_TK_GFF_TMP --지하철역승하차임시
(
  SUBWAY_STATN_NO CHAR(6) NOT NULL --지하철역번호
, STD_YM CHAR(6) NOT NULL --기준년월
, BEGIN_TIME CHAR(4) NOT NULL --시작시간
, END_TIME CHAR(4) NOT NULL --종료시간
, TK_GFF_SE_CD VARCHAR2(6) NOT NULL --승하차구분코드
, TK_GFF_CNT NUMBER(15) NOT NULL --승하차횟수
-- 복합 pk 구성
, CONSTRAINT PK_TB_SUBWAY_STATN_TK_GFF_TMP PRIMARY KEY (SUBWAY_STATN_NO, STD_YM, BEGIN_TIME, END_TIME, TK_GFF_SE_CD)
);
```

- 외래키 생성

```sql
ALTER TABLE TB_SUBWAY_STATN_TK_GFF_TMP --지하철역승하차임시 테이블에
ADD CONSTRAINT FK_TB_SUBWAY_STATN_TK_GFF_TMP1 --참조 무결성 제약조건을 생성
FOREIGN KEY (SUBWAY_STATN_NO) --지하철역승하차임시 테이블의 지하철역번호 칼럼은
--지하철역임시 테이블의 지하철역번호를 참조
REFERENCES TB_SUBWAY_STATN_TMP (SUBWAY_STATN_NO);
```

### ALTER TABLE

칼럼 및 제약조건을 추가/수정/제거 하는데 이용한다. 

- 칼럼 추가
  
```sql
ALTER TABLE TB_SUBWAY_STAIN_TMP ADD (OPER_YN CHAR(1)); -- 운영여부 컬럼 추가
```

- 칼럼 삭제

```sql
ALTER TABLE TB_SUBWAY_STATN_TMP DROP COLUMN OPER_YN;
```

- 데이터 형 제약 조건 변경

```sql
--운영여부 칼럼 추가생성 (NULL 허용)
ALTER TABLE TB_SUBWAY_STATN_TMP ADD (OPER_YN CHAR(1) NULL);
--운영여부 칼럼 변경
ALTER TABLE TB_SUBWAY_STATN_TMP MODIFY(OPER_YN NUMBER(1) DEFAULT 0 NOT NULL NOVALIDATE);
```

- 칼럼명 변경

```sql
ALTER TABLE TB_SUBWAY_STATN_TMP RENAME COLUMN OPER_YN TO OPERATION_YN;
```

- 외래키 제거

```sql
ALTER TABLE TB_SUBWAY_STATN_TK_GFF_TMP
DROP CONSTRAINT FK_TB_SUBWAY_STATN_TK_GFF_TMP1;
```

- 외래키 생성

```sql
ALTER TABLE TB_SUBWAY_STATN_TK_GFF_TMP --지하철역승하차임시 테이블에
ADD CONSTRAINT FK_TB_SUBWAY_STATN_TK_GFF_TMP1 --참조 무결성 제약조건을 생성
FOREIGN KEY ( SUBWAY_STATN_NO ) --지하철역승하차임시 테이블의 지하철역번호 칼럼은
--지하철역임시 테이블의 지하철역번호를 참조
REFERENCES TB_SUBWAY_STATN_TMP (SUBWAY_STATN_NO );
```

- 테이블 명 변셩

```sql
RENAME TB_SUBWAY_STATN_TK_GFF_TMP TO TB_SUBWAY_STATN_TK_GFF_TMP_2;
```

- 테이블 내 데이터 제거(TRUNCATE는 테이블 객체는 그대로 내부 데이터만 영구 삭제)

```sql
TRUNCATE TABLE TB_SUBWAY_STATN_TK_GFF_TMP_2;
```

- 테이블 제거

```sql
DROP TABLE TB_SUBWAY_STATN_TK_GFF_TMP_2;
DROP TABLE TB_SUBWAY_STATN_TMP;
DROP TABLE TB_RN_TMP;
```