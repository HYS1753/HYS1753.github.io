---
author_profile: true
date: 2021-12-31
title: "Web Development 3 - CSS 기본"
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

# CSS 기본

---

## 1. CSS 란

- 웹 문서의 내용과 상관 없이 디자인만 바꿀 수 있다.
  - HTML로는 웹 사이트의 내용을 나열하고 CSS로는 웹 문서의 디자인을 구성하는 것이 웹 표준의 시작
- 다양한 기기에 맞게 탄력적으로 바뀌는 문서 생성 가능
  - 최근에는 PC 뿐만 아니라 모바일 환경에서도 많이 웹을 사용하기 때문에 CSS를 활용하면 HTML로 작성한 내용은 그대로 두고 CSS만 변경하면 같은 내용을 여러 기기에서 볼 수 있으며 이를 반응형 웹 디자인이라 한다.


## 2. 스타일

- CSS소스에서 한 줄이 하나의 스타일에 해당한다.
- 맨 앞의 선택자는 스타일을 어느 태그에 적용할 것인지 알려주는 것이고, 
- 중괄호({})사이에는 스타일에 대한 정보를 넣는다.
- 해당 스타일 정보는 속성: 값이 하나의 쌍으로 이루어져 있으며 이를 *스타일 규칙*이라 한다.
- 그리고 이러한 *스타일 규칙*은 세미콜론을 통해 여러개를 지정할 수 있다.
  
  ```
  p(선택자) {
      text-align: center; /*텍스트 정렬 속성: 값*/
      color: blue;        /*글자색 속성: 값*/
  }
  ```

### 스타일 시트

- 웹 문서 안에서는 스타일 규칙을 여러개 사용한다. 
- 이러한 스타일 규칙을 한 눈에 확인하고 필요할 때 마다 수정하기 쉽도록 한데 묶어 놓은 것을 스타일 시트라 한다.
- 스타일 시트는 크게 웹 브라우저에 기본으로 만들어져 있는 브라우저 기본 스타일과 사이트 제작자가 만드는 사용자 스타일로 나눌 수 있다.
  - **브라우저 기본 스타일**
    - CSS를 사용하지 않은 웹 문서라 하더라도 웹 브라우저에 표시할 때는 기본 스타일을 사용한다.
  - **인라인 사용자 스타일**
    - 간단한 스타일 정보라면 스타일 시트를 사용하지 않고 스타일을 적용할 대상에 직접 표시할 수 있다.
    - 스타일을 적용하고 싶은 부분이 있다면 해당 태그에 sytle속성을 사용해 `sytle="속성:값;" 의 형태로 스타일을 변경할 수 있다.
        ```
        <h1> 제목 </h1>
        <p style="color:blue;"> P 단락 테그 </p>
        ```
  - **내부 스타일 시트**
    - 스타일을 여러곳에 적용할 떄 사용하는 방식
    - 스타일 정보는 웹 문서를 브라우저 화면에 표시하기 전에 결정해야 하므로 모든 스타일 정보는 `<head>` 태그 안에서 정의하고 `<style>` 태그 사이에 작성한다.

        ```
        <!DOCTYPE html>
        <html lang="ko">
            <head>
                <meta charset="UTF-8">
                <title> 제목 </title>
                <style>
                    h1 {
                        padding: 10px;
                        background-color: #222;
                        color: #fff;
                    }
                </style>
            </head>
            <body>
                <h1> H1 CSS 추가 </h1>
                <p> 단락은 기본 브라우저 스타일 적용</p>
            </body>
        </html>
        ```
  - **외부 스타일 시트**
    - 스타일 정보를 따로 저장해 놓고 사용하는 방식
    - 웹 사이트를 만들 때 하나의 웹 문서로 끝나는 경우는 거의 없다.
    - 대부분의 디자인은 일관성있게 디자인 되는데 같은 스타일을 여러 웹 문서에 사용하는 것은 비효율적이다. 
    - 따라서 사이트를 제작할 때 여러 웹 문서에서 사용할 스타일을 별도로 파일로 저장하고 필요할 때 마다 불러오는 방식으로 사용한다.
    - 이렇게 따로 저장해 놓은 스타일 정보를 외부 스타일 시트라 하고 `*.css`라는 파일 확장자를 사용한다.
    - 외부 스타일 시트 파일에 스타일을 작성할 때는 `<style>` 태그를 사용하지 않는다. 
    - 또한 외부 스타일 시트는 웹 문서에 연결해야 스타일이 문서에 적용된다. 외부 스타일 시트를 연결할때는 `<link>` 태그를 사용하며 양식은 다음과 같으며 HTML의 헤더 부분에 해당 태그를 추가해 외부 스타일 시트를 사용한다.
    - `<link rel="stylesheet" href="외부 스타일 시트 파일 경로">`

        ```
        style.css

        h1 {
            padding: 10px;
            backgroud-color: #222;
            color: #fff;
        }
        ```
    - link 태그 속성
      - `<link rel="속성값">`
      - 속성 값
        - alternate: 프린트 페이지나 번역된 페이지와 같이 해당 문서의 대체 버전에 대한 링크를 제공함.
        - author: 해당 문서의 저자에 대한 링크를 제공함.
        - dns-prefetch: 브라우저가 대상 리소스의 원본에 대해 DNS 확인(DNS resolution) 작업을 미리 수행하도록 명시함.
        - help: 도움말 문서에 대한 링크를 제공함.
        - icon: 사용자 인터페이스에서 해당 문서를 나타낼 리소스(일반적으로 아이콘)를 불러옴.
        - license: 해당 문서의 저작권 정보에 대한 링크를 제공함.
        - next: 연관된 문서들의 모음 중 다음 문서에 대한 링크를 제공함.
        - pingback: 현재 문서에 대한 핑백(pingback)을 처리하는 핑백 서버의 주소를 제공함.
        - preconnect: 브라우저가 대상 리소스의 원본에 미리 연결하도록 명시함.
        - prefetch: 사용자가 요청할 가능성이 있으므로, 브라우저가 대상 리소스를 미리 가져와 캐시(cache)하도록 명시함.
        - preload: 브라우저가 as 속성과 해당 대상과 관련된 우선순위에 따라 현재 탐색에 사용할 대상 리소스를 미리 가져와 캐시하도록 명시함.
        - prev: 연관된 문서들의 모음 중 이전 문서에 대한 링크를 제공함.
        - search: 현재 문서 및 관련 페이지를 검색하는데 사용할 수 있는 리소스에 대한 링크를 제공함.
        - stylesheet: 스타일 시트(stylesheet)로 사용할 외부 리소스를 불러옴.


## 3. CSS 기본 선택자

- **전체 선택자**
  - 전체 선택자(universal selector)는 말 그대로 스타일 문서의 모든 요소에 적용할 떄 사용한다.
  - 또는, 웹 브라우저의 기본 스타일을 초기화 할 때 자주 사용한다.
  - 다음과 같으 `*`을 사용해 표현한다.
  - `*{속성: 값; ...}`
  
- **타입 선택자**
  - 타입 선택자(type selector)는 특정 태그를 사용한 모든 요소에 스타일을 적용한다.
  - 즉, `<p>` 요소의 스타일을 헤더에 한번만 정의하면 html문서 안의 모든 `<p>`요소는 해당 스타일이 적용되게 된다.
  - `태그명 {속성: 값; ...}`

- **클래스 선택자**
  - 클래스 선택자(class selector)는 앞서 본 전체를 설정하는 선택자와 달리 특정 부분만 선택해서 스타일을 적용할 때 사용한다.
  - 클래스 선택자는 클래스 이름을 사용해서 다른 선택자와 구별하는데, 이때 클래스 이름 앞에 .(마침표)를 반드시 붙여야 한다.
  - 또한 요소 하나에 클래스 스타일을 2개 이상 적용할 수도 있다.
  
    ```
    <!DOCTYPE html>
    <html lang="ko">
        <head>
            <meta charset="UTF-8">
            <title> 클래스 연산자 예시 </title>
            <style>
                p {
                    font-style: italic;     /* 이텔릭체 */
                }
                .accent {
                    border: 1px solid #000;     /* 테두리 */
                    padding: 5px;                 /* 테두리와 내용 사이의 여백 */
                }
                .bg {
                    background-color: #ddd;     /* 배경 색 */
                }
            </style>
        </head>
        <body>
            <div>
                <h1 class="accent bg"> assent, bg 클래스 사용</h1>
                <p>뒤에 나오는 특정 <span class="accent">부분</span>만 accent 클래스 적용</p>
            </div>
        </body>
    </html>
    ```

- **id 선택자**
  - id 선택자도 클래스 선택자와 마찮가지로 웹 문서의 특정 부분을 선택해서 스타일을 지정할 떄 사용한다.
  - 마침표 대신 `#`기호를 사용하고 웹 요소에 적용할 떄 class가 아닌 `id="아이디명"`으로 적용한다는 차이점 제외하면 정의 방법은 클래스 선택자와 같다.
  - 클래스 선택자와의 가장 큰 자이는 클래스 선택자가 문서에서 여러번 적용할 수 있는 반명, id 선택자는 문서에서 한번만 적용할 수 있다.
  - 이처럼 id 선택자는 중복해서 적용할 수 없으므로 주로 문서의 레이아웃과 관련된 스타일을 지정하거나 웹 요소에 자바스크립트 프로그램을 사용하면서 요소를 구별할 때 사용한다.
  - `#아이디명 { 스타일 규칙 }


- **그룹 선택자**
  - 선택자를 이용해 스타일을 저의하다 보면 여러 선택자에서 같은 스타일 규착을 적용하는 경우가 있다.
  - 이럴 경우 쉼표로 구분해 여러 선택자를 나열한 후 스타일 규칙을 한번만 정의하면 된다.
  - `선택자1, 선택자2 {스타일 규칙}`

## 4. 캐스케이딩 스타일 시트

- 캐스케이딩의 의미 
  - CSS는 Cascading Style Sheet의 줄임말으로 우선순위가 위에서 아래 즉, 계단식으로 적용된 스타일 시트를 의미한다. 
  - 따라서, CSS에서는 웹 요소에 둘 이상의 스타일을 적용할 때 우선순위에 따라 적용할 스타일을 결정한다.
  - 캐스케이딩은 스타일끼리 충돌하지 않도록 막아주는 중요한 개념이며 충돌 방지를 위한 방법은 다음과 같다
    - 스타일 우선순위 : 스타일 규칙의 중요도와 적용범위에 따라 우선순위가 결정되고, 그 우선순위에 따라 위에서 아래로 스타일을 적용한다.
    - 스타일 상속 : 태그에 포함 관계에 따라 부모 요소의 스타일을 자식 요소로, 위에서 아래로 전달한다.

### 스타일 우선순위
- 중요도에 따른 우선순위
  - 웹브라우저에 내용을 표시할 때에는 단순히 CSS 소스의 스타일만 적용되는 것이아니다.
  - 컴퓨터 사용자가 지정한 스타일, 웹문서를 제작한 제작자의 스타일, 그리고 웹 브라우저가 기본으로 정해 놓은 스타일 이렇게 3가지가 있다.
  - 이 3가지의 우선순위는 사용자 > 제작자 > 브라우저 스타일 순서이다.

- 적용범위에 따른 우선순위
  - 스타일의 적용범위가 좁을 수록 우선순위가 높아진다. 
  - 단 `!important` 를 붙이면 그 스타일은 다른 스타일보다 우선순위가 높아진다.
  - !important > 인라인 스타일 > id 스타일 > 클래스 스타일 > 타입 스타일

- 코드 작성 순서에 따른 우선순위
  - 스타일 시트에서 중요도와 적용범위가 같다면 그 다음은 스타일을 정의한 소스 순서로 우선순위가 정해 진다. 

### 스타일 상속
- 웹 문서에서 사용하는 여러 태그는 서로 포함관계가 있다. 
- 스타일 시트에서는 자식 요소에서 별도로 스타일을 지정하지 않으면 부모 요소의 스타일 속성들이 자식요소로 전달되는데 이를 스타일 상속이라 한다. 

