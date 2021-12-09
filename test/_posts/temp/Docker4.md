---
author_profile: true
date: 2021-08-20
title: "Docker 4 활용"
categories: 
    - Docker
tag: 
    - Docker
    - DB
    - Python

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Docker 활용

---

## 1. Docker를 통해 Python3.9 실행

venv 와 같이 따로 설정할 필요 없이 도커 컨테이너 상에서 바로 python을 돌릴 수도 있다.

### 디렉터리 생성

- pyhton3.9
  - app(실행시킬 python 파일 저장) - 추후에 컨테이너 실행시 바인드 마운트로 볼륨으로 사용.
    - main.py
  - Dockerfile
  - requirements.txt  (pip으로 설치할 라이브러리 지정)

### Dockerfile 작성

Docker Container를 만들기 위한 설정 파일 작성

```
FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

CMD [ "python", "./main.py" ]

```

### requirement.txt 작성

해당 .py 를 실행시키기 위해 필요한 라이브러리 추가

```
pandas==1.3.4
```

### main.py 작성

Docker 상에서 실행시킬 Python 작성

```
import os


print("docker python start")
print(os.environ.get('HOME'))
```

### docker 실행 명령어

```
# Dockerfile build (이미지 생성)
docker build -t mypython ./
# 생성한 이미지를 통해 컨테이너 생성 및 실행
docker run -v $('pwd')/app:/usr/src/app -it --rm --name my_running_python mypython
```

### GIT 참조

[Docker-Python39](https://github.com/HYS1753/Docker-Python39)

---

## 2. Docker Compose를 사용해 PostgreSQL 접속

Docker Compose를 사용해 기존 Dockerfile만으로 복잡하게 만들었던 이미지를 간단히 생성

### docker-compose.yml 작성

```
version: "3.9"

services:
  postgreSQL:
    build: ./
    container_name: PostgreSQL
    env_file:
      - ./.env
    volumes:
      - ./app:/usr/src/app
    ports:
      - 5432:5432
```

### Dockerfile 작성

```
FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

CMD [ "python", "./main.py" ]
```

### .env 파일 작성

- docker-compose --env-file .env config 로 사용 또는
- yml 파일에 env_file로 명시
  
```
pgshost = 127.0.0.1
pgsdbname = 'test'
pgsdbuser = 'testuser'
pgspassword = 'testuser'
pgsport = '5432'
```

### docker-compose 실행

`docker-compose up`

### GIT 참조

[Docker-Compose-PostgreSQL](https://github.com/HYS1753/Docker-Compose-PostgreSQL)