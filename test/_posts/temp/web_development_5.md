---
author_profile: true
date: 2021-08-20
title: "Web Development 5 - CSS 활용2"
categories: 
    - web
tag: 
    - CSS

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# CSS 활용 2

---

## 1. css 상에서 외부 폰트 추가 방법

- Black Han Sans 폰트 추가 
  - @import url('https://fonts.googleapis.com/css2?family=Black+Han+Sans&display=swap');

## 2. 변수 설정

- root로 만들어 두면 필요한 곳에서 사용이 가능하다 
    
    ```
    :root {
        --text-color: #eee5e9;
        --background-color: #debcdb;
        --accent-color: #8f148f52;
    }
    ```

## 3. Flex 컨테이너

- display: flex; 
  - FLEX 아이템들은 가로방향으로 배치되고, 
  - 자신이 가진 내용물의 width 만큼만 차지한다. height는 컨테이너의 높이만큼 늘어난다. 
  - 즉, 플렉스 컨테이너를 오뎅 꼬치로 생각하고 막대가 메인 축, 꼬치에 flex 아이템이 끼워진다 생각하면 편하다.

- flex-direction: row | column | row-reverse | column-reverse;
  - flex 컨테이너를 오뎅꼬치로 보았을때 flex 아이템을 어떻게 꽂을 것인지에 대한 설정
  - row는 기본값 가로로 왼쪽부터, row-reverse 가로로 오른쪽부터
  - column은 세로로 위에서 부터 , column-reverse 세로로 아래에서 부터 꽂는다.

- flex-wrap: nowrap | warp | wrap-reverse;
  - 컨테이너가 더 이상 아이템들을 한 줄에 담을 여유 공간이 없을 때 아이템 줄바꿈을 어떻게 할지 결정하는 속성
  - nowrap : 뚫고 나감
  - wrap : 넘어가는 부분 아래로 배치
  - wrap-reverse : 넘어가는 부분 위로 배치

- flex-flow: row warp;
  - flex-direction, flex-wrap 을 한번에 지정할 수 있는 속성

- justify
  - 메인축(오뎅꼬치) 방향으로 정렬, align은 수직축(오뎅을 뜯는)방향으로 정렬
  - justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
    - 메인축 방향 정렬 하는 속성. 위의 justify가 나옴
    - flex-start :  아이템들을 시작점으로 정렬한다.
    - flex-end : 아이템들을 끝점으로 정렬한다.
    - center : 아이템들을 가운데로 정렬한다.
    - space-between : 아이템간의 간격을 균일하게 설정한다. `|■↔■↔■|`
    - space-around : 아이템 들의 둘레를 균일하게 설정한다. `|↔■↔↔■↔↔■↔|`
    - space-evenly : 아이템들의 사이와 양끝에 간격을 일정하게 한다. `|↔■↔■↔■↔|` 

- align-items: stretch | flex-start | flex-end | center | baseline;
  - 수직 축 방향으로 아이템들을 정렬하는 속성이다.
  - stretch : 아이템들이 수직 방향으로 끝까지 늘어난다.
  - flex-start : 아이템들을 시작점으로 정렬한다.
  - flex-end : 아이템들을 끝으로 정렬한다.
  - center : 아이템들을 가운데로 정렬한다.
  - baseline : 아이템들을 텍스트 베이스라인 기준으로 정렬한다. 

- align-content: stretch | flex-start | flex-end | center | space-between | space-around | space-evenly;
  - flex-wrap 이 설정되어 있을떄 아이템들이 행이 2줄 이상 되었을 떄 수직 축 방향을 정렬하는 속성 


## 4. Grid 컨테이너

- grid 컨테이너를 위한 기본적인 html 구조는 다음과 같다.
    
    ```
    <div class="container">
        <div class="item">A</div>
        <div class="item">B</div>
    </div>
    ```

  - 부모요소인 div.container를 그리드 컨테이너라 하고 
  - 자식요소인 div.itme을 그리드 아이템이라 부른다.
  - flex와 마찬가지로 grid는 컨테이너에 display: grid를 설정하는 것으로 시작한다.

- .container { display: grid;}
  - 그리드 컨테이너: display: grid를 적용하는 Grid의 전체 영역이다. 
  - 그리드 아이템 : Grid 컨테이너의 자식요소들이다. 이러한 아이템들이 Grid 규칙에 의해 배치된다.
  - 그리드 트랙 : 그리드의 행 또는 열을 가리키는 말
  - 그리드 셀 : 그리드의 한 칸을 가리키는 말 <div>와 같은 html 요소는 그리드 아이템이고, 이런 grid 아이템 하나가 들어가는 가상의 칸 이라 생각하면 된다.
  - 그리드 라인 : 그리드 셀을 구분하는 선이다
  - 그리드 번호 : 그리드 라인의 각 번호이다.
  - 그리드 갭 : 그리드 셀 사이의 간격이다.
  - 그리드 영역 : 그리드 라인으로 둘러싸인 사각형 영역으로 그리드 셀의 집합.

- 그리드 형태 정의
  - grid-template-columns: 200px 200px 200px | 1fr 1fr 1fr | repeat(3, 1fr) | 200px 1fr | 100px 200px auto;
  - grid-template-rows: 200px 200px 200px | 1fr 1fr 1fr | repeat(3, 1fr) | 200px 1fr | 100px 200px auto;
    - 그리드의 각 행과 열을 배치하는 것으로 200px 200px 200px 은 각각을 해당하는 픽셀크기 만큼 지정하겠다는 의미
    - 1fr 1fr 1fr은 frame의 약자로 숫자 비율대로 트랙의 크기를 나눈다.
    - repeat(반복횟수, 반복 값) 함수 :  반복
    - minmax(최소, 최대)  함수 : 예를 들어 minmax(100px, auto)로 지정하면 최소 100px을 가져간다는 의미
    - auto-fill, auto-fit 함수
        - grid-template-columns: repeat(auto-fill, minmax(20%, auto)); 와 같이 사용한다.
        - auto-fill의 경우 % 크기에 따라 알아서 크기자 조절되고
        - auto-fit의 경우 아이템 개수가 부족하더라고 알아서 남는공간을 늘려서 채운다.

    - row-gap: 10px;  column-gap: 10px;  gap: 10px 10px;
    - 각 아이템 간의 간격을 정의한다.

- 그리드 각 셀의 영역 지정
  - 영역 이름으로 그리드 정의(grid-template-areas)
    
    ```
    .container {
        grid-template-areas:
            "header header header"
            "   a    main    b   "
            "   .     .      .   "
            "footer footer footer";
    }
    ```

  - 위와 같이 적용하고
  - 각각의 아이템은 다음과 같이 적용한다.
    - .header { grid-area: header; }
    - .sidebar-a { grid-area: a; }
    - .main-content { grid-area: main; }
    - .sidebar-b { grid-area: b; }
    - .footer { grid-area: footer; }


- 예시(다음과 같이 사용할 수 있다.)
  
    ```
    .container{
        display: grid;
        grid-template-areas: 
            "  .      .     tag  "
            "header header header"
            "  .      .      .   "
            " left   main   main "
            "  .      .      .   "
            "footer footer footer"
            ;
    }
    .tag{
        grid-area: tag;
    }
    .header{
        grid-area: header;
    }
    .sidebar-left{
        grid-area: left;
    }
    .main-content{
        grid-area: main;
    }
    .footer{
        grid-area: footer;
    }
    ```

## 5. HTML 속성 추가

```
<!DOCTYPE html>
<html lang="ko">
    <head>
        <!-- meta 테그는 meta data를 정의하기 위해 사용된다.-->
        <!-- 즉, 화면상에는 보이지 않지만 해당 문서에 대한 정보를 담고 있는 부분이다.-->
        <!-- 많은 검색 엔진이 메타 태그를 이용하기 때문에 검색엔진에서 수집할 수 있도록 -->
        <!-- 웹 문서의 정보를 포함해 준다.-->
        <!-- 웹 브라우저에 표시되는 과정에서 인코딩 할 때 지정된 문자코드로 인코딩 하라-->
        <meta charset="UTF-8">
        <!-- 제작 도구 표시-->
        <meta name="Generator" content="Visual Studio Code"/>
        <!-- 페이지 작성 제작자 명 표시-->
        <meta name="Author" content="">
        <!-- 검색엔진에 의해 검색되는 단어 지정-->
        <meta name="Keywords" content="">
        <!-- 검색 결과에 표시되는 문자 지정-->
        <meta name="Description" content="">
        <!-- Content-Script-Type 웹페이지에 쓰인 언어 -->
        <meta http-equiv="Content-Script-Type" content="Text/html" />
        <!-- 웹 페이지 제목 지정-->
        <title>테스트 페이지</title>
        <!-- css linking-->
        <!-- link 태그는 해당 문서와 외부 소스 사이의 관계를 정의할 떄 사용한다.-->
        <!-- link 요소는 빈 태그 이며, 속성만을 포함한다. --> 
        <!-- 또한, 이 요소는 head 내부에만 위치할 수 있으며 그 개수에는 제한이 없다.-->
        <!-- rel 속성: 필수 속성으로 외부 리소스 사이의 연관관계 명시 -->
        <!--          alternate, author, dns-prefetch, icon, licenese, stylesheet 등-->
        <link rel="stylesheet" href="css/2column.css">
    </head>
</html>
```

