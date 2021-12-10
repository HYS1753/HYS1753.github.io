---
author_profile: true
date: 2021-08-20
title: "Web Development 2 - HTML"
categories: 
    - web
tag: 
    - HTML

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# HTML

---

## 1. HTML이란

다양한 인터넷 정보를 웹 브라우저에 보여줄 때 사용하는 언어로 웹 문서를 만드는 언어이다.

## 2. HTML 기본 구조

```
<!DOCTYPE html>
<html lang="ko">
    <head>
        <meta charset="UTF-8">
        <title>HTML 기본 문서</title>
    </head>
    <body>
        <h1>프런트엔드 웹 개발</h1>
        <hr>
        <p>HTML</p>
        <p>CSS</p>
        <p>JavaScript</p>
    </body>
</html>
```
- `<!DOCTYPE html>` : 현재 문서가 HTML5 언어로 작성한 웹 문서임을 뜻함
- `<html> ~ </html>` : 웹문서의 시작과 끝을 의미 하며 웹브라우저는 이 태그 안의 내용을 화면에 표시한다.
- `<head> ~ </head>` : 웹브라우저가 웹 문서를 해석하는데 필요한 정보 입력하는 부분
  - `<meta>` : 웹 문서와 관련한 정보 표시, 특히 문자 인코딩 방식 표시시 사용
    - `<meta charset="UTF-8">`
    - `<meta name="keywords" content="hello">` 
    - `<meta name="descroption" content="hello">` 
    - `<meta name="author" content="hello">` 
  - `<title>` : 웹 문서의 재목 표시
- `<body> ~ </body>` : 실제로 웹 브라우저 화면이 나타나는 부분

## 3. 시맨틱 태그

시멘틱 태그를 사용하면 HTML소스코드만 보고도 어느부분이 제목이고 메뉴이고 본문인지 쉽게 알 수 있다.

- `<header>` : 검색창, 사이트 메뉴 등
- `<nav>` : 같은 웹문서 안의 다른 위치로 연결할 때 사용하며, 헤더, 푸터 사이드바 등 다양한 곳에서 사용이 가능하다.
- `<main>` : 메인 컨텐츠가 들어가는 부분으로 웹문서에서 단 한번만 사용 가능하다.
- `<article>` : 잡지의 기사 처럼 실제로 보여주고 싶은 내용을 넣는다.
- `<section>` : 웹 문서의 컨텐츠 영역을 나타내며 section은 여러개 컨텐츠 묶을 때 사용, article은 단독으로 사용 시 사용한다. 
- `<aside>` : 본문 내용외에 오른쪽 또는 아래쪽에 사이드 바 만들 떄 사용.
- `<footer>` : 웹문서의 가장 아래쪽에 있는 푸터 영역을 만든다. 
- `<div>` : 
  - division의 줄임말으로 `<header>`, `<section>` 과 같은 시맨틱 태그가 나오기 전에 헤너 내비게이션 푸터 등을 구분할 떄 사용했던 태그이다.
  - `<div>` 는 `<div id="header">`, `<div class="detail">`과 같이 id나 class 속성을 사용해서 문서구조를 만들거나 스타일을 적용할 때도 사용한다.

## 4. 텍스트 입력하기

- `<Hn>` 태그
  - heading의 줄임말으로 n의 자리에는 1~6의 숫자가 들어가며 1부터 가장 큰 제목이다. 
- `<p>` 태그
  - 텍스트 단락을 만들 때 사용하는 태그로, 해당 태그 사이에 텍스트를 입력하면 앞뒤로 빈 줄이 생기면서 텍스트 단락이 생긴다. 주의할 점은 태그 안에서 줄을 바꿔도 웹브라우저에서는 한줄로 표시된다.
- `<br>` 태그
  - `<p>` 텍스트 단락 태그를 사용할 때 줄바꿈을 해도 웹상에서는 한줄로 표시되는데 이럴 때 원하는 위치에서 줄을 바꾸기 위해 사용하는 태그
  - 해당 태그는 단독으로 사용하는 태그로 `</br>` 쓸 필요 없음
- `<blockquote>` 태그
  - 인용문 사용할 때 사용하는 태그로 해당 태그로 감싸주면 들여쓰기 되어 표시된다.
- `<strong>`, `<b>` 태그
  - 텍스트를 굵게 표시할 때 사용하는 태그로 strong은 내용 강조 용, 단순 굵은 글자는 b 사용.
- `<em>`, `<i>` 태그
  - 텍스트를 기울여 표시할 떄 사용하는 태그
  - em 은 emphasis의 줄임말으로 강조할 때 사용, i는 italic의 줄임말으로 이텔릭체를 의미한다.
- `<abbr>` 태그
  - 줄임말을 표시하고 title 속성을 함께 사용할 수있다.
  - `<abbr title="Internet of Things">IoT</abbr>`
  - IoT 가 웹상에 표시되고 마우스 커서를 올리면 title의 내용이 보인다.
- `<cite>` 태그
  - 웹 문서나 포스트에서 참고 내용을 표시한다.
  - 기우림 표시
- `<code>` 태그
  - 컴퓨터 인식을 위한 소스코드
- `<small>` 태그
  - 부가정보 처럼 작게 표시해도 되는 텍스트
- `<sub>` 태그
  - 아래 첨자 표시
- `<sup>` 태그
  - 위 첨자 표시
- `<s>` 태그
  - 취소선 표시
- `<u>` 태그
  - 밑 줄 표시
- `<ins>` 태그
  - 공동 작업 문서에서 새로운 내용을 삽입한다,.
- `<del>` 태그
  - 공동 작업 문서에서 기존 내용을 삭제한다.

## 5. 목록 만들기

- `<ol>`, `<li>` 태그
  - 순서 있는 몰고을 만드는 태그로 각각 ordered list와 list의 약자이다.
  
  ```
  <ol>
    <li>항목 1</li>               1. 항목 1
    <li>항목 2</li>       →       2. 항목 2
    <li>항목 3</li>               3. 항목 3
  </ol>
  ```

  - `<ol>` 태그의 type, start 속성
  
    |종류|설명|
    |-|-|
    |type = "1"|숫자(기본값)|
    |type = "a"|영문 소문자|
    |type = "A"|영문 대문자|
    |type = "i"|로마 숫자 소문자|
    |type - "I"|로마 숫자 대문자|
  - 사용 방법 `<ol type="a" start="3">` -> c. 부터 시작


- `<ul>`, `<li>` 태그
  - 순서 없는 목옥을 만들 떄 사용하는 태그
  - 각각의 태그의 약자는 unordered list 와 list이다.
  
  ```
  <ul>
    <li>항목 1</li>               ● 항목 1
    <li>항목 2</li>       →       ● 항목 2
    <li>항목 3</li>               ● 항목 3
  </ul>
  ```

- `<dl>`, `<dt>`, `<dd>` 태그
  - 설명 목록을 만들 때 사용하는 태그
  - description list의 약자로 이름과 값의 형태로 된 목록을 말한다.
  
  ```
  <dl>
    <dt>이름</dt>                 이름
    <dd>값 1</dd>                   값 1
    <dd>값 2</dd>        →          값 2
    <dd>값 3</dd>                   값 3
  </dl>
  ```

## 6. 표 만들기

- `<table>`, `<caption>`, `<tr>`, `<td>`, `<th>` 태그
  - 표의 시작과 끝을 알려주는 table 태그
  - 표의 제목을 표시하는 captipn 태그
  - 표의 행을 만드는 tr 태그
  - 표의 열을 만드는 hd 태그
  - 표의 제목 행에 셀을 만들 떄 td 대신 th를 사용한다. 
  
  ```
  <table>
    <caption>테이블 테스트</caption>
    <tr>
      <th>1행 1열</th>
      <th>1행 1열</th>
    </tr>
    <tr>
      <td>2행 1열</td>
      <td>2행 1열</td>
    </tr>
  </table>
  ```

  |1행 1열|1행 2열|
  |-|-|
  |2행 1열|2행 2열|

- `<thead>`, `<tbody>`, `<tfoot>` 태그
  - table에 head, body, foot을 붙인 약자로 제목, 본문, 요약이 있는부분으로 표의 구조를 나누어주는 용도의 태그
  - 해당 태그로 구분을 하면 CSS를 사용해 표의 제목 본문 요약에 각각 다른 스타일을 적용할 수 있으며, 내용이 많을 경우 javascript를 사용해 head, foot만 고정시키고 body 태그만 스크롤 할 수 있도록 한다.
  
  ```
  <table>
    <caption>테이블 테스트</caption>
    <thead>
      <tr>
        <th>1행 1열</th>
        <th>1행 1열</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>2행 1열</td>
        <td>2행 1열</td>
      </tr>
    </tbody>
  </table>
  ```

- rowspan, colspan 속성
  - 표는 `<tr>`, `<td>`, `<th>` 태그를 통해 여러 셀로 구성되는데 이때 여러 셀을 합치거나 나누어서 다양한 형태로 바꿀 때 사용하는 속성이다.
  
  ``` 
  <td rowspan="합칠 셀의 개수">셀의 내용</td>
  <td colspan="합칠 셀의 개수'>셀의 내용</td>
  ```

- `<col>`, `<colgroup>` 태그
  - 열을 묶어주는 역할의 태그
  - 해당 두 태그는 반드시 `<caption>` 태그 다음에 써야 한다. 즉, 표의 내용이 시작되기 전에 열의 상태를 알려 주어야 한다.
  - `<col>` 태그를 사용할 떄는 `<colgroup>` 태그 안에 `<col>` 태그를 포함해 표 전체의 열의 개수 만큼 `<col>` 태그를 넣어야 한다.
  
  ```
  <table>
    <caption>col group test</caption>
    <colgroup>
      <col style="background-color:#eee;">
      <col sytle="width:100px;">
      <col>
    </colgroup>
    <thead>
    ...
  ```

## 7. 이미지, 오디오, 동영상 삽입하기

- `<img>` 태그
  - 웹 문서에 이미지를 삽입할 때 사용하는 태그
  - GIF, JPG/JPEG, PNG 파일등을 삽입한다.
  
  ```
  <img src="이미지 파일 경로" alt="대체용 텍스트">
  ```

  - width, height 속성
    - 삽입할 이미지의 크기를 지정하는 속성으로 두개 모두 사용해도 되고, 만약 한개만 정의되어 있을 겨웅 자동으로 비율을 계산 해 표시하게 된다.
    - 이미지 크기를 표현하는 단위

    |종류|설명|예시|
    |-|-|-|
    |%|이미지 크기의 값을 %로 지정하면 현재 웹 브라우저 창 너비와 높이를 기준으로 크기 결정|width="50%"|
    |px|이미지 크기의 값을 픽셀로 지정하면 이미지의 너비나 높이를 해당 픽셀 크기만 큼 표시한다.|width="100"|

- `<object>`, `<audio>`, `<video>`태그
  - `<object>` 태그킄 오디오, 비디오, 자바 애플릿, PDF 등 다양한 파일을 삽입할 때 사용한다.
  
  ```
  <object width="너비" height="높이" data="파일"></object>
  ```

  - 멀티 미디어 관련 파일 테그가 HTML5 웹 표준이 정해지면서 가장 많이 변한 부분이다.
  - 기존 HTML4 까지는 웹 브라우저에서 파일을 삽입한 후 재생하기 위해선 플러그인 프로그램이 필요했지만 HTML5 부터 바로 태그로 재생 할 수 있다.
  
  ```
  <audio src="오디오 파일 경로"></audio>
  <video src="비디오 파일 경로"></video>
  <video src="media/aa.mp4" controls witdh="100"></video>
  ```
  - 해당 태그의 속성
  
  |종류|설명|
  |-|-|
  |contorls|플레이어 화면에 컨츠롤 바를 표시한다.|
  |autoplay|자동 실행|
  |loop|반복 재생|
  |muted|음소거|
  |preload|페이지를 불러올 떄 파일을 어떻게 불러올 지 지정한다. auto, metadata, none의 값을 넣을 수 있으며, 기본 값은 preload="auto"이다.|
  |width, height|높낮이 조절|
  |poster="파일 이름|비디오 태그에서 사용하는 속성으로 비디오 재생 전 까지 화면에 표시할 이미지|

- `<embed>` 태그
  - `<embed>` 태그는 오브젝트와 같이 다양한 멀티미디어 파일을 삽입할 때 사용하다, HTML의 `<audio>`, `<video>`, `<object>` 와 같은 태그를 지원하지 않는 웹브라우저를 고려해야 할 떄 사용할 수 있는 태그 이다.
  
  ```
  <embed src="파일 경로" width="너비" height="높이">
  ```

## 8.하이퍼 링크 삽입하기

- `<a>`, href 속성
  - 웹사이트에서 연결할(링크 할) 수 있는 기능
  ```
  <a href="링크할 주소">텍스트 또는 이미지</a>
  ```
  - 링크를 새 탭에서 열어주는 `target`속성
  - `<p><a herf = "https://www.naver.com" target="_black">NAVER</a></p>`
  - _black 를 주어 링크 시 새로운 탭에서 열리도록 함

## 9. 폼 삽입하기 

- `<form>` 태그 
  - 폼을 만드는 가장 기본적인 태그로 `<form [속성="속성값"]>여러 폼 요소</form>` 의 형태로 지정한다.
  - `<form>` 태그의 속성
    
    |종류|설명|
    |-|-|
    |method|사용자가 입력한 내용을 서버 쪽 프로그램으로 어떻게 넘겨줄 것인지 정함. method에서 사용할 수 있는 속성 값은 get과 post 방식이 있다.|
    |name|자바스크립트로 폼을 제어할 때 사용할 폼의 이름|
    |action|`<form>`태그 안에 내용을 처리해 줄 서버 프로그램을 지정한다.|
    |target|action 속성에서 지정한 스크립트 파일을 현재 창이 아닌 다른 위치에서 열도록 함|
    |autocomplete|자동완성 기능 기본은 "on" 상태이므로 비밀번호 같은경우 "off"로 지정해 준다.|

  - 예를 들어 폼에 내용을 입력하고 서버로 전송 했을 떄 서버에 있는 .php 를 실행한다면 다음과 같이 작성한다.
    ```
    <form action="temp.php">
    <!--폼 요소 -->
    </form>
    ```

- `<fieldset>`, `<legend>` 태그
  - 하나의 폼 안에서 여러 구역을 나누어 표시할 떄 `<fieldset>` 태그를 사용한다.
  - 기본형 : `<fieldset [속성="속성값"]></fieldset>`
  - '<legend>`는 fieldset으로 묶은 그룹에 제목을 부여해 준다.

- `<label>` 태그
  - 라벨 테그는 input 태그와 같은 폼 요소에 레이블을 붙일 때 사용한다.
  - `<label>아이디<input type="text"></label>`
  - `<label for="user-id">아이디</label>`
    `<input type="text" id="user-id">` 와 같이도 사용 가능
  
- `<textarea>` 태그
  - 텍스트를 여러줄 입력하는 영역을 만드는 태그 
  - `<textarea>내용</textarea>`
  - `<textarea>` 태그 속성
    - cols : 텍스트 영역의 가로 너비를 문자 단위로 지정
    - rows : 텍스트 영역의 세로 길이를 줄 단위로 지정, 지정한 숫자보다 줄 개수가 많아지면 스크롤 막대 생성됨.

- `<select>`, `<option>` 태그
  - 사용자가 내용을 직접 입력하지 않고 여러 옵션 중에서 선택하게 하려면 드롭다운 목록이나 데이터 목록을 사용한다. 
  - select 태그는 드롭다운 목록의 시작과 끝을 표시하고 그 안의 option 태그를 이용해 원하는 항목을 추가한다.
  - `<select>` 속성
    - size : 화면에 표시할 드롭다운 항목의 개수
    - multiple : 드롭다운 목록에서 둘 이상의 항목을 선택 할 때 사용.
  - `<option>` 속성
    - value : 해당 항목을 선택할 때 서버로 넘겨줄 값을 지정한다.
    - selectted : 드롭다운 메뉴를 삽입할 때 기본적으로 선택해서 보여줄 항목을 지정.
  
  ```
  <select>
    <option value="값1">값1</option>
    <option value="값2">값2</option>
    <option value="값3">값3</option>
  </select>
  ```

- `<datalist>`, `<option>` 태그
  - 데이터 목록을 만드는 태그로 select와 달리 서버로 전송되는 값도 사용자에게 표시되는 목록이다.
  ```
  <input type="text" list="데이터 목록 id">
  <datalist id="데이터 목록 id">
    <option value-"서버로 넘길 값1>옵션1</option>
    <option value-"서버로 넘길 값2>옵션2</option>
    <option value-"서버로 넘길 값3>옵션3</option>
  </datalist>
  ```

- `<button>` 태크
  - input 태그를 사용해 버튼을 삽입할 수 있지만, button태그를 이용해 폼을 전송하거나 리셋하는 버튼을 삽입할 수 있다.
  - `<button type="submit">내용</button>`
  - `<button type="reset">내용</button>`
  - `<button type="button">내용</button>`



## 10. 사용자 입력

- `<input>` 태그
  - 사용자가 입력한 정보를 받을 때 사용하는 태그
  - input 태그의 type 속성

    |종류|설명|
    |-|-|
    |text|한줄짜리 텍스트를 입력할 수 있는 텍스트 박스|
    |password|비밀번호를 입력할 수 있는 필드|
    |search|검색할 때 입력하는 필드|
    |url|URL 주소 입력하는 필드|
    |email|이메일 주소 입력하는 필드|
    |tel|전화번호 입력하는 필드|
    |checkbox|2개 이상 선택할 수 있는 체크박스|
    |radio|여러 항목 중 1개만 선택할 수 있는 라디오 버튼|
    |number|숫자를 조절 할 수 있는 스핀 박스|
    |range|숫자를 조절 할 수 있는 슬라이드 막대|
    |date|사용자 지역을 기준으로 날짜(연 월 일)를 넣음|
    |month|사용자 지역을 기준으로 날짜(연 월)를 넣음|
    |week|사용자 지역을 기준으로 날짜(연, 주)를 넣음|
    |time|사용자 지역을 기준으로 날짜(시 분 초 분할 초)를 넣음|
    |datetime|국제 표준시 UTC 기준으로 날짜(연 월 일 시 분 초 분할 초)를 넣음|
    |datetime-local|사용자 지역 기준 datatime|
    |submit|전송 버튼|
    |reset|리셋 버튼|
    |image|submit버튼 대신 사용할 이미지|
    |button|일반 버튼|
    |file|파일을 첨부할 수 있는 버튼|
    |hidden|사용자에게는 보이지 않지만 서버로 넘겨주는 값이 있는 필드|

    - input type 사용 방법
      - "text" or "password"의 속성
        - size : 해당 필드에 보여지게 될 글자 수
        - value : 텍스트 부분에 보여지게 될 내용
        - maxlength : 최대 문자 수
        - `<label>아이디<input type="text" id="user-id" size="10" value="ID"></label>`
      - "search" or "url" or "email" or "tel"
        - 컴퓨터 상으로 별 차이가 없어보이나
        - 핸드폰 같은경우 해당하는 필드에 따라 키보드 레이아웃이 변경된다.
      - "checkbox" or "radio" 의 속성
        - value : 선택한 버튼을 서버에 알려줄 떄 넘겨줄 값
        - checked : 처음에 기본선택 값.
        - `<label><input type="checkbox" value="check">ischeck</label>`
      - "number" or "range"
        - 숫자 입력 필드를 나타낼 때 사용하는 필드
        - min : 필드에 입력할 수 있는 최솟 값, 기본 최소 0
        - max : 필드에 입력할 수 있는 최댓 값, 기본 최대 100
        - step : 숫자 간격 기본은 1
        - value : 필드에 표시할 초기 값.
      - "date" or "month" or "week"
        - 날짜 입력을 나타내는 필드로 다음과 같이 나타낸다.
        - `<input type="date|month|week|">`
      - "time" or "datetime" or "datetime-local"
        - 시간 입력을 나타낼 때 사용하는 필드로 다음과 같이 나타낸다.
        - `<input type="time|datetime|datetime-local|">` 
      - "submit" or "reset"
        - 전송 리셋 버튼 생성
        - `<input type="submit|reset" value="버튼에 표시할 내용">
      - "image"
        - 이미지 버튼 사용시 사용
        - `<input type="image" src="이미지 경로" alt="대체 택스트">`
      - "button"
        - 기본 버튼을 나타낼 떄 사용
        - `<input type="button" value-"버튼에 표시할 내용">
      - "file"
        - 파일을 첨부하는 버튼 만들 떄 사용
        - `<input type="file">`
      - "hidden"
        - 히든 필드 생성
        - `<input type="hidden" name="이름" value="서버로 넘길 값">

  - Input 태그의 주요 속성
    - autofocus 속성
      - `<input type=텍스트-입력-필드 autofocus required>`
      - 페이지를 불러오자 마자 폼의 원하는 요소에 마우스 포인터 표시 가능
    - placeholder 속성
      - 해당 input 값에 힌트 추가
      - `<input type="text" id="user-id" placeholder="ID">`
    - readonly 속성
      - 사용자가 입력은 하지 못하고 읽게만 하는 읽기 전용 필드
      - `<input type="텍스트-입력-필드" readonly>`
    - required 속성
      - 사용자가 내용을 폼에 입력한 후 submit버튼을 누르면 폼을 서버로 전송하는데 이때 필수 필드가 채워 졌는지 확인하는 속성
      - `<input type="텍스트-입력-필드" required>`

