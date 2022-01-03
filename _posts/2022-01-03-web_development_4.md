---
author_profile: true
date: 2022-01-03
title: "Web Development 4 - CSS 활용"
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

# CSS 활용

---

## 1. 텍스트 표현 스타일

### 1.1 글꼴

- font-family 속성
  - 웹문서에서 사용할 글꼴은 font-family 속성으로 지정한다. 
  - 이 속성은 `<body>` 태그를 비롯한 다양한 텍스트를 사용하는 요소에서 사용된다.
  - `font-family:<글꼴이름> | [<글꼴이름>. <글꼴 이름>]`
  - 웹문서의 텍스트는 사용자 시스템의 글꼴을 이용해 웹 브라우저 화면에 표시 된다.
  - 만약 웹 문서에서 지정한 글꼴이 사용자 시스템에 설치되어 있지 않으면 웹 문서에서 의도한 글꼴이 아닌 글꼴이 표시되게 된다. 따라서 웹 문서에 글꼴을 지정할 때는 한가지 글꼴만 선택하기 보다 여러개 글꼴을 대안으로 사용하는 것이 좋다.
  - `body { font-family: "맑은 고딕", 돋음, 굴림}` 
  - font-family 속성은 `<body>` 태그 스타일에서 일단 한번 정의되면 문서 전체에 적용되고 문서 안에서의 모든 자식 요소에 같은 글꼴을 사용한다.

- font-size 속성
  - 글자 크기는 font-size 속성을 사용하여 조절할 수 있다.
  - 글자의 크기 단위는 px 이나 pt 등으로 지정할 수 있고 백분율을 사용할 수도 있다.
  - `기본형 font-size: <절대크기> | <상대크기> | <크기> | <백분율>`
    - 절대크기 : 브라우저에서 지정한 글자 크기
    - 상대크기 : 부모 요소의 글자 크기를 기준으로 상대적인 글자 크기를 지정
    - 크기 : 브라우저와 상관없이 글자 크기를 직접 지정
    - 백분율 : 부모 요소의 글자 크기를 기준으로 백분율로 표시
  - 키워드를 사용해 글자 크기를 지정할 수 있다.
    - xx-small < x-small < small < medium < large < x-large < xx-large
  - 단위를 사용ㅎ아여 글자 크기 지정할 수 있다.
    - em : 부모 요소에서 지정한 글꼴의 대문자 M의 너비 기준(1em)으로 한후 비율값을 지정.
    - rem : 문서 시작 부분(root)에서 지정한 크기를 기준(1rem)으로 한 후 비율 값을 지정.
    - ex : 해당 글꼴의 소문자 x의 높이를 기준(1ex)로 한후 비율 값을 지정
    - px : 모니터의 1픽셀을 기준으로 한 후 비율 값 지정
    - pt : 포인트라 하여 일반 문서에서 많이 사용
    - 과거에는 px, pt 절대단위를 많이 사용했지만, 모바일 기기까지 고려한 최근에는 상대 크기 단위인 em, rem을 많이 사용한다.(1em = 16px = 12pt)

- font-style 속성
  - `기본형 font-style: normal | italic | oblique`
  - 이텔릭체로 바꾸는 속성은 italic, oblique가 있지만 italic은 처음부터 디자인 되어 있고 oblique는 원래 글꼴에서 기울리는 효과를 주기 때문에 대부분의 웹에서 italic 사용한다.

- font-weight 속성
  - 글자의 굵기를 지정하는 속성
  - `기본형 font-weight: nomal | bold | bolder | ligher | 100 | 200 | _ | 800 | 900`

### 1.2 웹 폰트 

- 웹 문서의 텍스트는 사용자 시스템에 설치된 글꼴을 사용해 화면에 표시하기 때문에 사용자가 없는 글꼴을 그대로 보여주기 어려웠다
- 이를 해결하는 방법이 웹 폰트 이며 개발자가 웹 폰트를 사용하는 페이지 를 서버에 올릴 때 웹 폰트 파일도 함께 업로드 하여 사용자가 페이지에 접속할 떄 다운받아 페이지를 표시하기 때문에 제작자가 의도한대로 텍스트를 보여줄 수 있다.

```
<style>
    @font-face {
        font-family: 'nanumgodic';
        src: local('nanumgodic'), 
             url('fonts/nanumgodic-bold.woff') format('woff'),
             url('fonts/nanumgodic-bold.ttf') format('ttf'),
             url('fonts/nanumgodic-bold.svg') format('svg');
    }
    .wfont {
        font-family: 'nanumgodic', sans-serif' /*웹 폰트 지정 */
    }
```

  -  src 속성에는 사용할 글꼴 파일의 경로를 지정한다. local을 통해 있는지 확인한 후 없으면 뒤에 나오는 url을 통해 다운받게 된다. 
  - font.google.com 을 통해 웹 폰트를 받을 수 있다.

```
<style>
    @import url('https://fonts.googleapis.com/css2?family=Nanum+Pen+Script&display=swap');
    h1 {
        font-size: 60px;
        font-weight: blod;
        font-family: 'Nanum Pen Script', cursive;
    }
</style>
```

### 1.3 문자 색상

- 웹 문서에서 문단이나 제목 등으 ㅣ텍스트에서 글자색을 바꿀 때는 color 속성을 사용한다. 
- color를 사용할 수 있는 속성 값으로는 16진수, rgb, rbga, hsl, hsla, 색상 이름이 있다.
- `기본형 color: <색상>`
- 16진수 : #RRGGBB -> #000000(검은색) , #ffffff(흰색), #00f(두자리씩 중복되면 줄여서 표기 가능)
- hsl, hsla : hue(색상), saturation(채도), lightness(명도), alpha(불투명도)의 약자로 각각퍼센트로 표시 -> 반 투명한 빨간색(0, 100%, 50%, 0.5)
- 색상 명 : red, yellow, black, blue 처럼 잘 알려진 색상은 이름으로 표기 가능
- rgb, rgba : red, green, blue, alpha의 줄임말으로 0~255로 표현 -> rgba(0, 255, 255, 0.5)

### 1.4 텍스트 정렬

- text-align
  - 텍스트 위치 정렬
  - `기본형 text-align: start| end| left| right| centor| justify| match-parent`
  - start : 현재 텍스트 줄의 시작 위치에 맞춰 문잔을 정렬
  - end : 현재 텍스트 줄의 끝 위치에 맞춰 문잔을 정렬
  - left : 왼쪽에 맞춰 문단을 정렬한다.
  - right : 오른쪽에 맞퉈 문단을 정렬한다.
  - centor : 가운데에 맞퉈 문단을 정렬한다.
  - justify : 양쪽에 맞퉈 문단을 정렬한다.
  - match-parent : 부모 요소를 따라 문단을 정렬한다.

- line-height
  - 줄간격을 조절하는 속성
  - `p {font-size: 12px; line-height: 24px;} | 2.0 | 200%`

- text-decoration
  - 텍스트의 줄을 표시 하거나 없애주는 속성
  - 예를 들어 하이퍼 링크 시 자동 생성되는 밑줄을 삭제 할 수 있따.
  - `<p style="text-decoration:none|underline|overline|line-through">text</p>`

- text-shadow
  - 텍스트에 그림자 효과를 추가하는 속성
  - `기본형 text-shadow: none|<가로거리>|<세로거리>|<번짐정도>|<색상>`
  - 가로거리 : 텍스트부터 그림자까지의 가로거리로 필수 속성이다. 양수는 글자의 오른쪽, 음수는 글자의 왼쪽에 그림자를 만든다.
  - 세로거리 : 텍스트부터 그림자까지의 세로거리로 필수 속성이다. 양숫값을 글자의 아래쪽, 음숫값은 글자의 왼쪽에 그림자를 만든다.
  - 번짐정도 : 그림자가 번지는 정도이다. 양수값을 사용하면 그림자가 모든 방향으로 퍼져 나가므로 그림자가 크게 표시된다. 반대로 음숫값은 그림자가 모든 방향으로 축소되어 보인다. 기본값은 0
  - 색상 : 그림자 색상을 지정한다. 한가지만 지정할 수도 공백으로 구분해 여러 색상 구별도 가능하다. 기본값은 현재 글자 색이다.
  - `.shadow { color: red; text-shadow: 1px 1px black}`

- text-transform
  - 영문자 표기시 텍스트이 대소문자를 원하는대로 바꾸는 속성
  - `기본형 text-transform: none | capitalize | uppercase | lowercase | full-width`
  - capitalize : 첫번째 글자를 대문자로 변환
  - uppercase : 모든 글자를 대문자로 변환
  - lowercase : 모든 글자를 소문자로 변환
  - full-width : 가능한 모든 문자를 전각문자(가로세로 길이 같은 글자)로 변환

- letter-spacing, word-spacing
  - letter-spacing은 글자와 글자 사이의 간격을 조절
  - word-spacing은 단어와 단어 사이의 간격을 조절한다. 
  - 이 두가지의 속성은 px, em과 같은 단위나 %를 사용해 조절한다. 
  - `.spacing { letter-spacing: 0.2em;}`

### 1.5 목록

- list-style-type
  - 불릿 모양 또는 번호 스타일을 지정하는 속성
  - 목록 스타일의 속성값
    
    |종류|설명|예시|
    |-|-|-|
    |disc|속이 찬 원모양|●|
    |circle|속이 빈 원모양|○|
    |square|속이 찬 사각형|■|
    |decimal|1부터 시작하는 10진수|1,2,3|
    |decimal-leading-zero|0이 붙어 있는 10진수|01, 02, 03|
    |lower-roman|로마 숫자 소문자|ⅰ, ⅱ, ⅲ|
    |upper-roman|로마 숫자 대문자|Ⅰ, Ⅱ, Ⅲ|
    |lower-alpha, lower-latin|알파벳 소문자|a, b, c|
    |upeer-alpha, upper-latin|알파벳 대문자|A, B, C|
    |none| 불릿이나 숫자 제거| |

    ```
    <style>
    .book {
        list-style-type: none;
    }
    </style>
    <ul class="book">
        <li>목록1</li>
        <li>목록2</li>
        <li>목록3</li>
    </ul>
    ```

- list-style-image
  - 단조로운 불릿유형 대신 이미지를 이요하는 속성
  - `기본형 list-style-image: <url(이미지 경로)>|none`

- list-style-position
  - 불릿이나 번호의 위치를 들어쓸때 사용하는 속성
  - `기본형 list-style-position: inside | outside;`

- list-style
  - 모든 목록의 속성을 한번에 표시하는 속성
  - `list-style: lower-alpha inside...`

### 1.6 표

- caption-side 속성
  - 표의 제목 위치를 정해주는 속성
  - `기본형 caption-side: top | bottom`

- border
  - 표의 테두리에 대한 속성
    ```
    <style>
    table {
        caption-side: bottom;
        border: 1px solid black;
    }
    td, th {
        border: 1px dotted black;
        padding: 10px;
        text-align: center;
    }
    </style>
    ```
    
    - 외부 테이블 은 실선 내부 셀들은 실선으로 표시하는 css

- border-spacing
  - 셀과 셀 사이의 여백을 조절하는 속성
  - `기본형 border-spacing: 수평거리 수직거리`

- border-collapse
  - 표와 셀 테두리를 합쳐주는 속성
  - `기본형 border-collapse: collapse | separate`
  - collapse: 표와 셀 테두리를 합쳐 하나로 표시한다.
  - separate: 표와 셀 테두리를 따로 표시한다. 기본값.


## 2. CSS 박스모델 스타일

- CSS 박스 모델이란 웹 문서의 내용을 박스 형태로 정의하는 방법이다. 
- 이 박스 모델이 모여 웹 문서를 이루게 된다.

### 2.1 블록 레벨 요소와 인라인 레벨 요소

- 박스 모델은 블록레벨 요소인지 인라인 레벨 요소인지에 따라 나열 방법이 달라진다.
- **블록레벨요소**
  - 태그를 사용해 요소를 삽입했을 때 혼자 한줄을 차지하는 것을 가리킨다.
  - 즉, 해당 요소의 너비가 100% 라는의미이다.
  - 따라서, 블록레벨 요소의 왼쪽이나 오른쪽에 다른 요소가 나올 수 없다.
  - 블록레벨 요소를 만드는 대표적인 태그로는 `<h1>`, ,`<div>`, `<p>` 태그 등이 있다.
- **인라인 레벨 요소**
  - 블록레벨 요소와 달리 한줄을 차지하지 않는다. 
  - 콘텐츠 영역만큼만 영역을 차지하고 나머지 공간에는 다른 요소가 나올 수 있다.
  - 대표적인 인라인 레벨 요소에는 `<span>`, `<img>`, `<strong>` 등이 있다. 

### 2.2 박스 모델의 기본 구성 

- 웹 문서의 블록 레벨 요소는 모두 박스 형태 이다. 스타일시트에서는 이렇게 박스 형태인 요소들을 박스 모델 요소라고 한다. 
- 박스모델은 콘텐츠영역, 박스와 콘텐츠영역 사이의 여백인 패딩, 박스 테두리, 여러 박스 모델 사이의 여백인 마진, 등의 요소로 구성된다. 

![CSS 박스모델 기본 구성](/assets/images/2022-01-03/css1.PNG)

- 박스 모델 기본
  - **width** 와 **height**
    - 컨텐츠 영역의 크기 지정
    - `.box{ width: 400px; height: 100px}`    - 고정 너비, 높이
    - `.box{ width: 50%; height: 100px}`      - 가변 너비, 고정 높이

      |종류|설명|
      |-|-|
      |`<크기>`|너비나 높이의 값을 px, em 단위로 지정한다.|
      |`<백분율>`| 박스 모델을 포함하는 부모 요소를 기준으로 너빗값이나 높잇값을 백분율(%)로 지정한다.- ex. 브라우저 창|
      |auto| 박스모델의 너빗값과 높잇값이 콘텐츠 양에 따라 자동으로 결정된다. 기본값.|

  - **box-sizing**
    - 박스 모델의 크기를 계산하는 속성
    - width, heigh 속성은 박스 모델에서 콘텐츠 주변의 여백이나 테두리를 뺀 컨텐츠 영역의 크기만 가리킨다.
    - 하지만, 실제로 배치할떄 박스 모델이 차지하는 크기는 컨텐츠 영역 뿐만 아니라 테두리 사이의 여백, 테두리 두께 등까지 계산해야 한다.
    - 따라서 CSS에서 박스모델을 사용할 떄 요소의 크기를 쉽게 계산하기 위해서는 box-sizing을 border-box로 지정해 두는 것이 좋다.
    - box-sizing 속성 종류
      
      |종류|설명|
      |-|-|
      |border-box| 테두리까지 포함해서 너빗값을 지정한다.|
      |content-box| 컨텐츠 영역만 너빗값을 지정한다. 기본값.|

  - **box-shadow**
    - 박스 모델에 그림자 효과 주기
    - `기본형 box-shadow: <수평거리> <수직거리> <흐림정도> <번짐 정도> <색상> inset`
    - ex. `.box { box-shadow: 2px -2px 5px 0px;}`

- 테두리 스타일
  - **border-style**
    - 테두리의 모양을 결정해 준다.
    - border-style 속성값.

        |종류|설명|
        |-|-|
        |none|테두리가 없는 기본값|
        |hidden|테두리를 감춘다. |
        |solid|테두리를 실선으로 바꾼다.|
        |dotted|테두리를 점선으로 바꾼다.|
        |dashed|테두리를 짧은 직선으로 바꾼다.|
        |douoble|테두리를 이중선으로 펴시한다. 두 선 사이의 간격이 border-width가 된다.|
        |groove|테두리를 창에 조각한것 처럼 표시. 홈에 파인듯 입체 느낌 제공|
        |inset|표에서 border-collapse: separate의 경우 전체 박스 테두리가 창에 박혀 있는모양으로 구현되고, 표에서 border-collapse: collapse의 경우 groove와 똑같이 설정된다.|
        |outset|표에서 border-collapse: separate의 경우 전체 박스 테두리가 창에서 튀어나오는 것처럼 구현되고, 표에서 border-collapse: collapse의 경우 ridge와 똑같이 설정된다.|
        |ridge| 테두리를 창에서 튀오나온 것처럼 표시한다.|

    - `#box { border-style: solid; }`
  - **border-width**
    - 테두리 두께를 지정하는 속성
    - `기본형 border-width: <크기> | thin | medium | thick `
    - `#box { border-width: 10px 5px 5px 10px}`
      - 하나만 변수를 설정하면 전체가 설정되고 
      - 두개를 입력하면 (top, bottom), (left, right) 가 설정되고 
      - 세개를 입력하면 top -> right -> bottom 순서로 적용되며 left는 right와 같은 사이즈로 적용된다.
      - 4개를 입력하면 top -> right -> bottom -> left 순서로 적용된다.

  - **boder-color**
    - 테두리의 색상을 지정한다.
  - **border**
    - 테두리 스타일을 묶어서 지정한다.
    - `border: 3px dotted blue;`
  - **border-radius**
    - 테두리가 둥근 박스 모델을 만든다.
    - `기본형 border-radius: <크기> | <백분율>`
    - 한꼭지점 씩 설정하는 방법
      - `border-top-left-radius: 20px` 과 같이 설정한다.

- 박스모델 여백 설정
  - **margin**
    - 요소 주변의 여백을 의미한다.
    - 테두리 바깥 여백
    - margin 다음에 하이픈(-) 이후 예약어인 top, left, right, bottom으로 각각 설정 가능
    - `기본형 margin: <크기> | <백분율> | auto`
    - border width와 같이 top=>right->bottom->left 순서로 지정해 줄 수 있다.
  - **padding**
    - 컨텐츠 영역과 테두리 사이의 여백을 의미한다. 
    - margin과 마찬가지로 4개 방향을 설정할 수 있다.
    - padding 다음에 하이픈(-) 이후 예약어인 top, left, right, bottom으로 각각 설정 가능

- 웹 문서의 레이아웃
  - **display**
    - 블록레벨 요소와 인라인 레벨 요소를 서로 바꿔서 사용할 수 있다.
    - display 속성은 주로 웹 문서의 내비게이션을 만들면서 메뉴 항목을 가로로 배치할 때 많이 사용된다.
    - display 속성 값

        |종류|설명|
        |-|-|
        |block|인라인 레벨 요소를 블록레벨 요소로 만든다.|
        |inline|블록레벨 요소를 인라인 레벨 요소로 만든다.|
        |inline-block|인라인레벨 요소와 블록레벨 요소의 속성을 모두 가지고 있으며 마진과 패딩을 지정할 수 있다.|
        |none|해당 요소를 화면에 표시하지 않는다.
    
    - 수평 네비게이션 만들기 예시

        ```
        <style>
        * { box-sizing: border-box;}    /* 모든 요소에 테두리까지 포함한 너빗값 지정 */
        nav ul {list-style: none;}      /* 블릿이나 숫자 번호 없앰 */
        nav ul li {
            display: inline-block;
            padding: 20px;
            margin: 0 20px;
            border: 1px solid #222;
        }
        <nav>
            <ul>
                <li> 메뉴 1 </li>
                <li> 메뉴 2 </li>
                <li> 메뉴 3 </li>
                <li> 메뉴 4 </li>
            </ul>
        </nav>
        ```
  - **float**
    - 웹 요소를 문서 위에 떠있게 만든다. 즉, 왼쪽구석 또는 오른쪽 구석에 배치되는것을 말한다.
    - left, right, none의 옵션이 있다.

  - **clear**
    - float 설정된 값을 해제 하는 옵션

- 2단 레이아웃 만들기
  - html

    ```
    <!DOCTYPE html>
    <html lang="ko">
    <head>
    <meta charset="UTF-8">
    <title>CSS 레이아웃 - 2단 레이아웃</title>
    <link rel="stylesheet" href="css/2column.css">
    </head>
    <body>
    <div id="container">
        <header id="header">
        <h1>사이트 제목</h1>
        </header>
        <aside id="sidebar">
        <h2>사이드바</h2>				
        </aside>
        <section id="contents">
        <h2>본문</h2>
        </section>
        <footer id="footer">
        <h2>푸터</h2>
        <footer>
    </div>
    </body>
    </html>
    ```

  - css

    ```
    * {  
        margin:0;  /* 브라우저 기본 마진 리셋 */
        padding:0;  /* 브라우저 기본 패딩 리셋 */
        box-sizing: border-box;  /* 테두리까지 포함해서 박스 모델 너비로 계산 */
    }
    #container {
        width:1200px;   /* 내용 전체의 너비 */
        margin:20px auto;  /* 내용을 화면 가운데 배치하도록 좌우 마진을 auto로 */
    }
    #header{
        width:100%;  /* 부모 요소의 너비와 똑같게 */
        height:120px;  /* 헤더의 높이 */
        background-color:#acacac;
    }
    #sidebar{
        width:300px;  /* 사이드바의 너비 */
        height:600px;  /* 사이드바의 높이 */
        background-color:#e9e9e9;
        float:left;  /* 왼쪽으로 플로팅 */
    }
    #contents {
        width:900px;  /* 본문의 너비 */
        height:600px;  /* 본문의 높이 */
        background-color:#f7f7f7;
        float:left;  /* 왼쪽으로 플로팅 */
    }
    #footer {
        width:100%;  /* 부모 요소의 너비와 똑같게  */
        height:100px;  /* 푸터의 높이 */
        background-color:#888888;
        clear:left;  /* 플로팅 해제 */
    }
    ```

  - 결과

    ![2단 레이아웃 결과](/assets/images/2022-01-03/css2.PNG)


## 3. 이미지

### 배경색과 배경 범위 지정

- **backgroud-color**
  - 16진수, RBB, 색상 이름을 통해 배경 색상을 지정한다. 
  - `backgroud-color: #000000`

- **background-clip**
  - 배경을 넣고 싶은 요소 마다 속서을 입력하면 되지만 박스 모델 관점에서 배경의 적용범위를 조절할 수 있다.
  - 속성값

    |종류|설명|
    |-|-|
    |border-box|박스 모델의 가장 외곽인 테두리까지 적용한다. 기본값|
    |padding-box|박스 모델에서 테두리를 뺀 패딩 범위까지 적용한다.|
    |content-box|박스 모델에서 내용 부분에만 적용된다.|

- **background-image**
  - 웹 요소에 배경이미지를 넣는 속성
  - `기본값 background-image: url('이미지 경로')`

- **background-repeat**
  - 배경 이미지르 ㄹ가로와 세로 중에서 어떤 방향으로 반복할지 지정하거나 반복하지 않고 한번만 나타나게끔 할 수 있다.
  - 속성 값
  
    |종류|설명|
    |-|-|
    |repeat|브라우저 화면에 가득 찰 때까지 가로와 세로를 반복한다. 기본|
    |repeat-x|브라우저 화면 너비에 가득 찰 때까지 가로로 반복한다.|
    |repeat-y|브라우저 화면 높이에 가득 찰 때까지 세로를 반복한다.|
    |no-repeat|한번만 표시하고 반복하지 않는다.|

- **background-position**
  - 배경 이미지의 수평 또는 수직 위치의 값을 지정할 수 있다.,
  - `기본값 background-position: <수평위치> <수직위치>;`
  - `수평 수직위치: left | center | right | <백분율> | <길이 값>

- **background-attachment**
  - 배경이미지가 있는 웹 문서를 웹 브라우저에서 열고 스크롤 막대를 위아래로 조절하면 문서 전체가 움직이므로 배경이미지도 같이 움직이게 된다. 
  - 이때 해당 옵션을 사용하면 배경이미지를 고정할 수 있다.
  - 속성

    |속성|설명|
    |-|-|
    |scroll|화면을 스크롤하면 배경이미지도 같이 스크롤 된다. 기본값|
    |fixed|화면을 스크롤하면 배경 이미지는 고정되고 내용만 스크롤 된다.|

- **background-size**
  - 배경 이미지 크기 조절하는 옵션
  - 속성 값이 하나라면 그 값은 너비로 인식하고 높이는 원래 이미지의 너비와 높이 비율에 따라 자동 계산한다.
  - 속성값

    |종류|설명|
    |-|-|
    |auto|원래 배경 이미지 크기만큼 표시한다.|
    |contain|요소 안에 배경이미지가 다 들어오도록 이미지를 확대 축소 한다.|
    |cover|배경 이미지로 요소를 모두 덮도록 이미지를 확대 축소한다.|
    |`<크기>`|이미지의 너비와 높이를 지정한다. 값이 하나만 주어질 경우 너빗값으로 인식하며, 이미지의 너비와 너빗값에 맞춘 높임값도 자동 계산된다.|
    |`<백분율>`|배경이미지가 들어갈 요소의 크기를 기준으로 값을 백분율로 지정하고 그 크기에 맞도록 배경이미지를 확대- 축소한다.|