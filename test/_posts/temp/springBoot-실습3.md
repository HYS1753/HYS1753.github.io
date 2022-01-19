---
author_profile: true
date: 2022-01-01
title: "SpringBoot 3 (Service 연동)"
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

# SpringBoot 3 (Service 연동)

---

## 1. 비지니스 로직을 담당하는 Service 생성

### DictionaryService 인터페이스 작성

- com/spring/service/DictionaryService.java 생성 및 다음과 같이 작성

```
package com.spring.service;

import java.util.List;

import com.spring.domain.DictionaryDTO;

public interface DictionaryService {
	
    // 삽입
	public boolean insertDictionary(DictionaryDTO params);
    // 삭제
	public boolean deleteDictionary(Long idx);
	// 조회
    public DictionaryDTO getDictionary(Long idx);
	// 전체 조회
    public List<DictionaryDTO> getDictionaryList();

}
```

### DictioanryServiceImpl 구현체 작성 

- com/spring/service/impl/DictionaryServiceImpl.java 생성 및 다음과 같이 작성

```
package com.spring.service.impl;

import java.util.ArrayList;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.spring.domain.DictionaryDTO;
import com.spring.mapper.DictionaryMapper;
import com.spring.service.DictionaryService;

@Service
public class DictionaryServiceImpl implements DictionaryService{
	
	@Autowired
	private DictionaryMapper dictionaryMapper;
	
	@Override
	public boolean insertDictionary(DictionaryDTO params) {
		int result = 0;
		// idx 가 null 인것은 새로 등록하는것이기 때문에 Insert 이외에는 update 수행
		if (params.getIdx() == null) {
			result = dictionaryMapper.insertDictionary(params);
		} else {
			result = dictionaryMapper.updateDictionary(params);
		}
		return (result == 1) ? true : false;
	}
	
	@Override
	public boolean deleteDictionary(Long idx) {
		int result = 0;
		DictionaryDTO dic = dictionaryMapper.selectDictionaryDetail(idx);
		if (dic != null && "N".equals(dic.getDeleteYN())) {
			result = dictionaryMapper.deleteDictionary(idx);
		}
		return (result == 1) ? true : false;
	}
	
	@Override
	public DictionaryDTO getDictionary(Long idx) {
		return dictionaryMapper.selectDictionaryDetail(idx);
	}
	
	@Override
	public List<DictionaryDTO> getDictionaryList() {
		List<DictionaryDTO> dicList = new ArrayList<DictionaryDTO>();
		int DictionaryTotalCount = dictionaryMapper.selectDictionaryCount();
		if (DictionaryTotalCount > 0) {
			dicList = dictionaryMapper.selectDictionaryList();
		}
		return dicList;
	}
}
	
```

- **@Service** : @Mapper와 유사하게 해당 클래스가 비지니스 로직을 담당하는 서비스 클래스 임을 명시
- **implements** : Service를 인터페이스와 구현체로 구분
- **dictionaryMapper** : @Autowired를 이용해서 DictionaryMapper 인터페이스 Bean을 주입한다.


## 2. Bootstrap 사용하기

- UI를 쉽고 빠르게 구성할 수 있도록 도와주는 HTML, CSS, JS 프레임워크가 있는데 이를 오픈소스로 쉽고 빠르며 동적페이지까지 지원하는 부트스트랩이 있다.
- 부트스트랩을 사용하기 위해서는 [부트스트랩](https://startbootstrap.com/) 에서 무로 템플릿을 다운 받은 후 Static 폴더에 있는 파일 전체를 Spring Boot의 Static디렉터리 아래 옮겨서 사용하면 된다.
- 가장 대표적인 [SB Admin2](https://startbootstrap.com/theme/sb-admin-2) 를 사용해 본다.

### 타임리프의 레이아웃 기능을 사용하기 위한 라이브러리 추가

- build.gradle 에 다음과 같이 추가한다.

```
implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
```

### header, body 작성

- resources/dictionary/fragments/header.html 을 생성하고 다음과 같이 작성한다.

```
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
	<head th:fragment="main-head">
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<meta http-equiv="X-UA-Compatible" content="IE=edge">
		<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
		<meta name="description" content="">
		<meta name="author" content="">

		<th:block layout:fragment="title"></th:block>

		<link rel="stylesheet" th:href="@{/css/style.css}" />
		<link rel="stylesheet" th:href="@{/plugin/mCustomScrollbar/jquery.mCustomScrollbar.min.css}" />

		<th:block layout:fragment="add-css"></th:block>
	</head>
</html>
```

- resources/dictionary/fragments/body.html 을 생성하고 다음과 같이 작성한다.

```
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
	<body th:fragment="main-body">
		<div class="fixed-navbar">
			<div class="pull-left">
				<h1 class="page-title">Board</h1>
			</div>
		</div>
		<!-- /.fixed-navbar -->

		<div id="wrapper">
			<div class="main-content">
				<div class="row row-inline-block small-spacing">
					<div class="col-xs-12">
						<div class="box-content">
							<div class="clearfix">
								<h4 class="box-title pull-left"></h4>
								<!-- /.box-title -->
	
								<th:block layout:fragment="search"></th:block>
	
							</div>
							<!-- //.clearfix -->
	
							<th:block layout:fragment="content"></th:block>
	
							<th:block layout:fragment="paging"></th:block>
						</div>
						<!-- /.box-content -->
						
						<th:block layout:fragment="add-content"></th:block>

					</div>
					<!-- /.col-xs-12 -->
				</div>
				<!-- /.row row-inline-block small-spacing -->
				<footer class="footer">
					<ul class="list-inline">
						<li>2016 © NinjaAdmin.</li>
					</ul>
				</footer>
			</div>
			<!-- /.main-content -->
		</div>
		<!-- /#wrapper -->

		<script th:src="@{/scripts/jquery.min.js}"></script>
		<script th:src="@{/plugin/bootstrap/js/bootstrap.min.js}"></script>
		<script th:src="@{/plugin/mCustomScrollbar/jquery.mCustomScrollbar.concat.min.js}"></script>

		<script th:src="@{/scripts/main.js}"></script>
		<script th:src="@{/scripts/common.js}"></script>

		<th:block layout:fragment="script"></th:block>
	</body>
</html>
```

- **th:fragment** : head 태그에 해당 속성을 사용해서 fragment의 이름을 지정한다. fragment는 다른 HTML에서 include 또는 replace 속성으르 사용해서 적용할 수 있다.
- **th:block** : layout:fragmnet 속성에 이름을 지정해서 실제 Content페이지의 내용을 채우는 기능이다. 해당 기능은 동적인 처리가 필요할 때 사용된다.
- **th:href** : a 태그의 href 속성과 동일하며 JSTL의 c:url 태그와 마찬가지로 웹 어플리케이션을 구분하는 context경로를 포함한다. 추가적으로 application.properties에서 context path를 원하는 경로로 변경 가능하다.
- **layout:fragment** 
  - **search** : 검색영역
  - **content** : Contents 영역
  - **paging** : 페이징 처리
  - **add-content** : contents 추가

### header와 body를 묶은 Layout 생성

- resources/templates/dictionary/layout/basic.html 을 생성하고 다음과 같이 작성

```
<!DOCTYPE html>
<html lang="ko" xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
	<head th:replace="dictionary/fragments/header :: main-head"> </head>
	<body th:replace="dictionary/fragments/body :: main-body"> </body>
</html>
```

- **th:replace** : JSP의 include 태그와 유사한 속성으로 template/dictionary 디렉터리에서 header.html의 main-head 프래그먼트와 body.html의 main-body 프래그먼트를 찾아 해당 코드로 치환한다.

## 3. 사용자의 요청과 응답을 처리하는 Controller

### Controller 작성

- com/spring/controller/DictionaryController.java 를 생성하고 다음과 같이 작성한다.

```
// 단순 표현식 사용시 다음과 같이 작성
package com.spring.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.spring.service.DictionaryService;

@Controller
public class DictionaryController {
	
	@Autowired
	private DictionaryService dictionaryService;
	
	@RequestMapping(value="/dictionary/write.do", method = RequestMethod.GET)
	public String openDictionaryWrite(Model model) {
		String title = "제목";
		String content = "내용";
		String writer = "가나다";
		model.addAttribute("t", title);
		model.addAttribute("c", content);
		model.addAttribute("w", writer);
		return "dictionary/write";
	}
	
}

// 타임리프 적용 시
package com.spring.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import com.spring.domain.DictionaryDTO;
import com.spring.service.DictionaryService;

@Controller
public class DictionaryController {
	
	@Autowired
	private DictionaryService dictionaryService;
	
	@RequestMapping(value="/dictionary/write.do", method = RequestMethod.GET)
	public String openDictionaryWrite(@RequestParam(value = "idx", required = false) Long idx, Model model) {
		if (idx == null) {
			model.addAttribute("dictionary", new DictionaryDTO());
		} else {
			DictionaryDTO dic = dictionaryService.getDictionary(idx);
			if(dic == null) {
				return "redirect:/dictionary/list.do";
			}
			model.addAttribute("dictionary", dic);
		}
		return "dictionary/write";
	}
}

```

- **@Controller**: 해당 클래스가 사용자의 요청과 응답을 처리하는 컨트롤러 클래스임을 명시
- **@RequestMapping** : vaule에는 URI, method에는 HTTP 요청 메서드를 지정하는 방식으로 컨트롤러에 URI와 HTTP 요청 메서드를 매핑한다. 스프링 4.3 이후 부터는 @GetMapping, @PostMapping 과 같이 별도의 어노테이션이 추가되었다.
- **return** : 컨트롤러 메소드의 리턴 타입은 void, String, ModelAndView, Map, List 등 여러가지 타입을 리턴 타입으로 가질 수 있다. 대표적으로 String, MddelAndView는 사용자에게 보여줄 화면 HTML 을 지정한다. 리턴 문에 지정된 HTML경로의 끝에는 접미사(Suffix)로 확장자(.html, .jsp 등)이 연결되기 때문에 생략 가능하다.
- **model**: 메서드의 파라미터로 지정된 model 인터페이스는 데이러를 뷰로 전달하는데 사용된다. 
- **addAttribute 메서드**: 해당 메서드를 통해 HTML로 데이터를 전달 할 수 있다. 메서드의 인자로는 이름(String), 값(Object) 형식으로 전달한다.HTML 에서는 ${}표현식을 사용해 전달받은 데이터에 접근할 수 있다.
- **@RequestParam** : 화면(view)에서 전달받은 파라미터를 처리하는데 사용한다. 예를 들어 사전 리스트 페이지에서 사전 등록 페이지로 이동하면 idx는 null로 전송, 하지만 사전 상세페이지에서 수정하기 페이지로 이동하면 컨트롤러로 idx가 파라미터로 전송되고 해당 idx를 getDictionary() 메서드 인자로 전달한다. 새로운 사전을 등록할 경우에는 idx가 필요하지 않기 떄문에 required 속성을 false로 지정한다. 필수 속성은 default가 true 이며 만약 false로 지정하지 않으면 idx null 시 오류가 발생한다.


## 4. 사용자가 보게 되는 View

- resources/templates/dictionary/write.html을 생성하고 다음과 같이 작성한다.

```
<!-- 단순 표현식 -->
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
	<head>
		<meta charset="UTF-8">
		<title>The page is a write page</title>
	</head>
	<body>
		<h2>Welcome Spring Boot!</h2>
		<span th:text="${t}">여기는 제목입니다.</span>
		<span th:text="${c}">여기는 내용입니다.</span>
		<span th:text="${w}">여기는 작성자입니다.</span>
	</body>
</html>

<!-- 타임리프 적용 -->
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorator="dictionary/layout/basic">
	<th:block layout:fragment="title">
		<title>thiThe page is a write page</title>
	</th:block>

	<th:block layout:fragment="content">
		<div class="card-content">
			<form class="form-horizontal" th:action="@{/dictionary/register.do}" th:object="${dictionary}" method="post" onsubmit="return registerDictionary(this)">
				<!--/* update의 경우 서버로 전달할 게시글 번호 (PK) */-->
				<!-- th:if의 조건에 따라 idx가 null이 아니고 0 보다 큰경우 숨겨진 타입으로 idx 가져옴 -->
				<input type="hidden" th:if="*{idx != null and idx > 0}" th:field="*{idx}" />

				<div class="form-group">
					<label for="deleteYN" class="col-sm-2 control-label">삭제 여부 설정</label>
					<div class="col-sm-10" style="margin-top: 10px;">
						<input type="checkbox" th:value="*{deleteYN}" th:checked="*{#strings.equals( deleteYN, 'Y' )}" />
					</div>
				</div>

				<div class="form-group">
					<label for="english" class="col-sm-2 control-label">영어</label>
					<div class="col-sm-10">
						<input type="text" th:field="*{english}" class="form-control" placeholder="영어 입력." />
					</div>
				</div>

				<div class="form-group">
					<label for="korean" class="col-sm-2 control-label">한국어</label>
					<div class="col-sm-10">
						<input type="text" th:field="*{korean}" class="form-control" placeholder="한국어 입력." />
					</div>
				</div>

				<div class="btn_wrap text-center">
					<a th:href="@{/dictionary/list.do}" class="btn btn-default waves-effect waves-light">뒤로가기</a>
					<button type="submit" class="btn btn-primary waves-effect waves-light">저장하기</button>
				</div>
			</form>
		</div>
		<!-- /.card-content -->
	</th:block>

	<th:block layout:fragment="script">
		<script th:inline="javascript">
			/*<![CDATA[*/

			function registerDictionary(form) {

				form.deleteYN.value = form.deleteYN.checked == false ? 'N' : 'Y';

				var result = (
						   isValid(form.english, "영어", null, null)
						&& isValid(form.korean, "한국어", null, null)
				);

				if ( result == false ) {
					return false;
				}
			}
			/*[- end of function -]*/

			/*]]>*/
		</script>
	</th:block>
</html>

```

- **localhost**: 자신의 PC를 의미하며 "127.0.0.1" 과 같은 의미이다.
- **8080 Port**: WAS에 해당하는 톰캣의 기본 포트 번호이다. application.properties에서 server.port 속성을 이용해 원하는 포트로 변경이 가능하다.
- **/dictionary/write.do** : controller에서 매핑된 URI 값이다. 만약 Controller에 없는 URI가 접속된다면 404 Error 발생
- **xmlns:th** : 타임리프의 th 속성을 사용하기 위해 선언된 namespace 순수 HTML일 경우에는 생략
- **th:text** : JSP의 EL 표현식인 ${}와 마찬가지로 타임리프도 ${} 표현식을 이용해서 컨트롤러에서 전달받은 데이터에 접근한다. 해당 속성은 일반적인 텍스트 형식으로 화면에 출력하며, JSTL의 c:forEach, c:set 등의 태그와 마찬가지로 여러가지 속성이 존재한다.
- **xmlns:layout** : 타임리프의 레이아웃 기능을 사용하기 위한 선언
- **xmlnslayout:decorate** : 레이아웃으로는 뒤에 지정되는 basic.html을 사용하겠다는 선언. Spring Boot 2.6 이하에서는 layout:decorator 사용해야 함.
- **th:blocklayout:fragment** : header.html 등에서 정의한 layout:fragment 속성에 이름을 지정해서 실제 Content를 채우는 역할을 한다. 해당 속성을 이용해서 타이틀을 동적으로 처리할 수 있다.
- **layout:fragment="content"** : 사전 등록 페이지에는 사전의 정보를 입력할 수 있는 폼이 필요하고, 사전 리스트 페이지에는 사전 정보를 보여주는 테이블이 필요하다. 즉, 타이틀 처럼 페이지마다 content영역의 형태가 다르기 때문에 해당 layout:fragmnet를 사용한다.
- **form 태그** : 
  - 폼 태그 안에 선언되어 있는 input, textarea 등 사용자 입력 필드의 name 속성 값을 기준으로 th:action 속성에 지정된 URI와 매핑된 컨트롤러의 메서드로 폼데이터를 전달한다. 
  - method 속성에는 http 요청 매소드를 지정한다.
      - http 요청 메소드는 대표적으로 GET(데이터 조회-select), POST(데이터 생성-insert, update) 이 있다.
  - onsubmit은 폼데이터를 컨트롤러로 전송하기 전에 폼데이터의 유효성을 검증하는 이벤트이다. 함수명 앞에 return이 있는 이유는 자바스크립트 이벤트가 기번적으로 return true의 성향을 가지고 있기 때문이다. return 되지 않으면 폼을 제출했을 때 함수를 실행하지 않고 바로 컨트롤러로 가는 문제가 생길 수 있다.
- **th:object** : 
    - form 태그의 th:object 속성의 ${dictionary}는 컨트롤러에서 뷰로 전달한 DictionaryDTO 클래스의 객체이다. 
    - 해당 속성을 이용하면 th:field를 이용해서 HTML 태그에 멤버 변수를 매핑할 수 있다.
    - th:filed 를 이용한 사용자 입력 필드(input, textarea 등)은 id, name, value 속성 값이 자동으로 매핑되기 때문에 따로 속성을 지정할 필요가 없으며 th:field는 ${} 표현식이 아닌 *{} 표현식을 사용한다. 
    - th:object 와 th:field 는 컨트롤러에서 특정 클래스의 객체를 전달받은 경우에만 사용할 수 있다.
    - 만약 checkbox 만들 시 제대로 연동이 안되는 문제가 있을 경우 각각의 id, name을 지정해 주어서 해결할 수 있다.
- **th:checked**
- https://congsong.tistory.com/16?category=749196
