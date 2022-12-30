---
author_profile: true
date: 2022-12-30
title: "Web Development 5 - Template Engine(Thymeleaf)"
categories: 
    - web
tag: 
    - Thymeleaf

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Thymeleaf Template Engine

---

## 1. 타임리프란

- 컨트롤러에서 전달하는 데이터를 사용하여 동적으로 화면을 구성하게 해 주는 뷰 템플릿(View Template)
- 타임리프는 html태그를 기반으로 하여 th: 속성을 이용하여 동적인 뷰를 구성한다.

- 단점
    - 타임리프는 컨트롤러에서 페이지를 처음 로딩(생성)할 때 각 객체에 데이터가 fix 되기 때문에 해당 페이지에서 변경된 값을 직접 불러오거나 js function을 사용할 때 직접 넘겨주지 못한다.
    - 예를 들어 select박스 부분에서 오름차순이 기본이고, 내림차순을 선택해 th:onchange() 를 넣게 되면 변경된 내림차순이 들어가는 것이 아닌 처음 셋팅된 오름차순이 들어가게 된다. 
    - 위와 같은 문제를 해결하기 위해서는 따로 js function을 추가해서 js 내에서 처리되도록 변경해야 한다는 단점이 있다.
    - 추가적으로 다른 뷰템플릿들과 문법적인 차이가 있어 주의가 필요하다. 

## 2. Thymeleaf 사용법

- 프로젝트내 디펜던시를 추가하여 사용한다.
- Gradle 

    ```json
    dependencies{
        ...
        implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    }
    ```
- Maven

    ```html
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>srping-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

## 3. Thymeleaf 사용 방법

- 타임리프는 기본적으로 html에 기본을 두고 있다.
- 변수식 사용(`${}`)
    - Context 안에 포함된 변수를 사용한다.
    - Java 컨트롤러 단에서 Model 객체에 데이터를 담으면 html 변수로 값을 전달 할 수 있다.
    - `model.addAttribute("key","value")` 와 같이 추가하면 된다.
    - 예시 Java Controller

        ```java
        @RequestMapping("/test")
        @Controller
        public class testController {
            @RequestMapping("/modelTest")
            public String modelTest(Model model) {
                model.addAttribute("key", "value");
                return "modelTest";
            }
        }
        ```
    - 예시 html

        ```html
        <!DOCTYPE html>
        <html lang="ko" xmlns:th="http://www.thymeleaf.org">
        <head>
        <meta charset="UTF-8">
        <title>Insert title here</title>
        </head>
        <body>
        <p th:text="${key}"></p>
        </body>
        </html>
        ```

- 메시지방식(`#{}`)
    - 미리 정의된 message properties 파일이 존재하고 thymeleaf engine에 등록되어 있다면 #표현식으로 사용이 가능하다

        ```
        test.message=테스트메시지입니다.
        ```

        ```html
        <p th:text="#{test.message}"></p>
        <!-- 출력 : 테스트메시지입니다. -->
        ```    

- 객체변수식(`*{}`)
    - `${}`와 유사하게 Context에 포함된 변수를 서용하지만 가까운 DOM 의 th:object가 존재하는 변수가 있다면 그 변수값에 포함된 값을 가져온다.

        ```html
        <div th:object="${session.user}">
            <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
            <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
            <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
        </div>
        ```

- 링크방식(`@{}`)
    - URL을 표현하는 방법

        ```html
        <!--'http://localhost:8080/testpage?key=value' -->
        <a href="testpage.html" th:href="@{http://localhost:8080/testpage(key=${key}})}">테스트페이지이동</a>
        <!-- '/testpage/details?key=value' -->
        <a href="testpage.html" th:href="@{/testpage(orderId=${o.id})}">테스트페이지이동</a>
        ```

## 4. Thymeleaf 문법

- 문자 더하기
    - `|`와 `|` 연산자 사이에 문자와 변수 표현식을 입력하면 static문자와 변수 문자가 이어져서 출력된다. 

        ```html
        <span th:text="|hello world, ${key}!|">
        ```

    - `+` 연산자를 이용해서도 합치기 
    
        ```html
        <span th:text="'hello world, ' + ${key} + '!'">
        ```

    - `|`, `+` 연산자 혼용도 가능

- if else 문

    ```html
    <!-- 단순 if 문 -->
    <div>
        <span th:if="${info.gender == 'F'}">여자</span>
        <span th:if="${info.gender == 'M'}">남자</span>
    </div>

    <!-- 단순 if else문 -->
    <p th:if="${key == 'value'}">일치</p>
    <p th:unless="${key == 'value'}">불일치</p>

    <!--th:block 을 사용해 div 영역 자체에 조건을 주기 -->
    <th:block th:if="${info.useYn == 'Y'}">
        <div>가능 합니다.</div>
    <th:block>

    <th:block th:if="${info.useYn == 'N'}">
        <div>불가능 합니다.</div>
    </th:block>
    ```

- block 태그 (th:block)
    - 타임리프에서 유일한 자체 태그로써 랜더링시 태그가 사라지고 html에서 빈영역으로 표현된다. 
    - 해당 block에서는 th:each, th:switch, th:if 등 여러 타임리프 자체 속성을 사용하여 반복문 또는 조건문을 사용할 수 있다.
    - th:each(for문)

        ```html
        <th:block th:each="item, status: ${userList}">
            <tr>
                <td th:text="${item.name}">이름</td>
                <td th:text="${item.userId}">아이디</td>
                <td th:text="${item.email}">이메일</th>
                <td th:text="${item.phone}">핸드폰</td>
                <td th:text="${item.createDt}">가입일자</td>
            </tr>    
        </th:block>
        ```

    - th:switch

        ```html
        <th:block th:switch="${user.gender}">
        <span th:case="M">남자<span>
            <span th:case="W">여자<span>
            <span th:case="Z">모름<span>
        </th:block>
        ```

- properties 값을 타임리프로 가져오기
    - 컨트롤러(java) 단에서 가져오기

        ```java
        @Value("${key.context}")
        private String key;

        @GetMapping("/test")
        public String view(Model model){
            model.addAttribute("key", key);
        }
        ```

        ```html
        <div>
            key : <span th:text="${key}"></span>
        </div>
        ```

    - 타임리프에서 다이렉트로 가져오기

        ```html
        <div>
            key : <span th:text="${@environment.getProperty('key.context')}"></span>
        </div>
        ```

- 기타 사용법 
    - Simple expressions:
        - Variable Expressions: ${...}
        - Selection Variable Expressions: *{...}
        - Message Expressions: #{...}
        - Link URL Expressions: @{...}
        - Fragment Expressions: ~{...}
    - Literals
        - Text literals: 'one text', 'Another one!',…
        - Number literals: 0, 34, 3.0, 12.3,…
        - Boolean literals: true, false
        - Null literal: null
        - Literal tokens: one, sometext, main,…
    - Text operations:
        - String concatenation: +
        - Literal substitutions: |The name is ${name}|
    - Arithmetic operations:
        - Binary operators: +, -, *, /, %
        - Minus sign (unary operator): -
    - Boolean operations:
        - Binary operators: and, or
        - Boolean negation (unary operator): !, not
    - Comparisons and equality:
        - Comparators: >, <, >=, <= (gt, lt, ge, le)
        - Equality operators: ==, != (eq, ne)
    - Conditional operators:
        - If-then: (if) ? (then)
        - If-then-else: (if) ? (then) : (else)
        - Default: (value) ?: (defaultvalue)

