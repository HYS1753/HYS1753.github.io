---
author_profile: true
date: 2021-08-20
title: "SQLD 1 - ORACLE 실습환경 구축"
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

# SQLD - ORACLE 실습 환경 구축

---

## 1. Oracle Database 11g 설치

- setup.exe 파일 관리자 권한 실행
- 설치 대상 폴더 확인
- SYS 및 SYSTEM 계정 비밀 번호 설정(ex. 1234)
- 완료

---

## 2. SQL*PLUS를 이용한 Oracle 접속

- CMD 관리자 권한 실행
- sqlplus/nolog
- conn sys as SYSDBA
- 1234
- 접속 완료

---

## 3. 문자 집합 UTF-8 to MS949

- 2번의 SYSDBA 접속 상태에서 아래의 SQL스크립트 실행
```SQL
UPDATE SYS.PROPS$
SET VALUE$='KO16MSWIN949'
WHERE NAME='NLS_CHARACTERSET';

UPDATE SYS.PROPS$
SET VALUE$='KO16MSWIN949'
WHERE NAME='NLS_NCHAR_CHARACTERSET';

UPDATE SYS.PROPS$
SET VALUE$='KOREAN_KOREA.KO16MSWIN949'
WHERE NAME='NLS_LANGUAGE';

COMMIT;

SHUTDOWN IMMEDIATE;

STARTUP MOUNT; --마운트 모드 시작

ALTER SYSTEM ENABLE RESTRICTED SESSION;
ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0;
ALTER SYSTEM SET AQ_TM_PROCESSES=0;
ALTER DATABASE OPEN;
ALTER DATABASE CHARACTER SET KO16MSWIN949;

SHUTDONW IMMEDIATE;

STARTUP;

```

- 환경 변수 설정 
  - 변수 : NLS_LANG
  - 값 : KOREAN_KOREA.KO16MSWIN949
  

---
## 4. 사용자 계정 및 테이블 스페이스 생성

```sql
-- 사용자 계정 미치 테이블 스페이스 생성

CREATE USER SQLD IDENTIFIED BY 1234;  -- 사용자 계정 생성

ALTER USER SQLD ACCOUNT UNLOCK;  -- 생성한 사용자 계정의 잠금 해제

GRANT RESOURCE, DBA, CONNECT TO SQLD; -- 생성한 사용자 계정에게 권한 부여

-- 관리자 권한 계정인 SYSTEM 계쩡으로 접속하여 사용자 계정인 SQLD 계정을 생성하고 비밀번호는 1234로 설정합니다. 
-- 또한, 해당 계정의 잠금을 해제 하고 이 계정에게 'RESOURCE', 'DBA', 'CONNECT' 권한을 준다. 
-- 이 권한을 줌으로써 SQLD는 DBA의 권한을 가지게 된다.


-- 테이블 스페이스 및 임시 테이블 스페이스 생성
CREATE TABLESPACE SQLD_DATA
DATAFILE 'D:\OracleDBMS\Oracle_Database_11g\app\oracle\oradata\XE\SQLD_DATA.DBF' SIZE 4G
AUTOEXTEND ON NEXT 512M MAXSIZE UNLIMITED
LOGGING
ONLINE
PERMANENT
EXTENT MANAGEMENT LOCAL AUTOALLOCATE
BLOCKSIZE 8K
SEGMENT SPACE MANAGEMENT AUTO
FLASHBACK ON;

CREATE TEMPORARY TABLESPACE SQLD_TEMP
TEMPFILE 'D:\OracleDBMS\Oracle_Database_11g\app\oracle\oradata\XE\SQLD_TEMP.DBF' SIZE 1G
AUTOEXTEND ON NEXT 100M MAXSIZE UNLIMITED;


-- 생성한 테이블 스페이스를 SQLD 계정의 디폴트 테이블 스페이스로 지정한다.
ALTER USER SQLD DEFAULT TABLESPACE SQLD_DATA;
ALTER USER SQLD TEMPORARY TABLESPACE SQLD_TEMP;

-- 위의 명령어를 통해 SQLD 계정의 디폴트 테이브르 스페이스를 SQLD_DATA 와 SQLD_TEMP로 지정한다. 
-- 위의 설정을 하면 SQLD계정으로 접속하여 생성하는 테이블은 지정한 DEFAULT 테이블 스페이스인 SQLD_DATA내에 생성되고
-- SQLD계쩡이 SQL 작정을 할 때는 지정한 임시 테이블인 SQLD_TEMP의 저장공간을 이용하게 된다.
```
