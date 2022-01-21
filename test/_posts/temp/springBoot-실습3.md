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

	@RequestMapping(value="/dictionary/delete.do", method = RequestMethod.POST)
	public String deleteDictionary(@RequestParam(value = "idx", required = false) Long idx, Model model) {
		try {
			if (idx == null) {
				// TODO => idx 입력 오류(올바르지 않은 접근)
				return "redirect:/dictionary/list.do";
			} else {
				DictionaryDTO dic = dictionaryService.getDictionary(idx);
				if (dic == null || "Y".equals(dic.getDeleteYN())) {
					// TODO => 이미 삭제된 단어임을 알림
				} else {
					boolean result = dictionaryService.deleteDictionary(idx);
					if (result == true){
						// TODO => 삭제 성공
					} else {
						// TODO => 삭제 실패
					}
				}
			}			
		} catch (Exception e) {
			// TODO => 오류처리
		}
		return "redirect:/dictionary/list.do";
	}

	@PostMapping(value="/dictionary/register.do")
	public String registerDictionary(final DictionaryDTO params) {
		try {
			System.out.println(params);
			boolean isRegistered = dictionaryService.insertDictionary(params);
			if (isRegistered == false) {
				// TODO => 게시글 등록에 실패하였다는 메시지를 전달
			}
		} catch (DataAccessException e) {
			// TODO => 데이터베이스 처리 과정에 문제가 발생하였다는 메시지를 전달

		} catch (Exception e) {
			// TODO => 시스템에 문제가 발생하였다는 메시지를 전달
		}

		return "redirect:/dictionary/list.do";
	}
	
	@GetMapping(value="/dictionary/list.do")
	public String openDictionaryList(Model model) {
		List<DictionaryDTO> dicList = dictionaryService.getDictionaryList();
		model.addAttribute("dictionaryList", dicList);
		return "dictionary/list";
	}

	@GetMapping(value = "/dictionary/view.do")
	public String openDictionaryDetail(@RequestParam(value = "idx", required = false) Long idx, Model model) {
		if (idx == null) {
			// TODO => 올바르지 않은 접근이라는 메시지를 전달하고, 게시글 리스트로 리다이렉트
			return "redirect:/board/list.do";
		}

		DictionaryDTO dic = dictionaryService.getDictionary(idx);
		if (dic == null || "Y".equals(dic.getDeleteYN())) {
			// TODO => 없는 게시글이거나, 이미 삭제된 게시글이라는 메시지를 전달하고, 게시글 리스트로 리다이렉트
			return "redirect:/board/list.do";
		}
		model.addAttribute("dictionary", dic);

		return "dictionary/view";
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

### 입력 FORM

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
<html lang="ko" xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorate="dictionary/layout/basic">
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
						<input type="radio" name="deleteYN" th:value="Y" th:checked="*{#strings.equals( deleteYN, 'Y' )}"/>삭제
						<input type="radio" name="deleteYN" th:value="N" th:checked="*{#strings.equals( deleteYN, 'N' )}"/>저장
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
    - **변수식으로 사용하는 `${}`와 메세지방식 `#{}`, 객체변수식인 `*{}`, 링크방식 `@{}`**
- **th:checked** : 체크박스의 경우 th:checked 속성을 이용해서 조건이 true 에 해당하면 체크 속성을 적용한다. 여기서는 타임리프의 equals 함수를 사용하여 값을 비교한다.
- **layout:fragment="script"** : 자바스크립트도 페이지마다 로직이 다르기 때문에 layout:fragment를 사용한다.
- **th:inline="javascript"** : script 태그에 th:inline 속성을 javascript로 지정해야 자바 스크립트를 사용할 수 있다.
- **`<![CDATA[]]>`** : 타임리프는 `<`, `>` 과 같은 태그를 엄격하게 검사하기 떄문에 태그 사용시 주의해야 하며 자바스크립트는 반드시 CDATA로 묶어줘 특수문자를 문자열로 치환한다.
- **onsubmit="return registerDictionary(this)"** : 하단에 있는 registerBoard 함수를 실행시키게 되며 this에는 폼 객체가 들어가기 된다.
- **checkbox** : 원래는 밑에 있는 text 처럼 th:field로 하면 위에 적힌 것들이 자동으로 생성되나, deleteYN이 true나 false를 값으로 가지지 않기때문에 따로 처리해 줘야 한다.


### LIST FORM

- resources/templates/dictionary/list.html을 생성하고 다음과 같이 작성한다.

```
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorate="dictionary/layout/basic">
	<th:block layout:fragment="title">
		<title>This page is a list page</title>
	</th:block>

	<th:block layout:fragment="search">
		<form action="#" id="searchform-header" class="searchform js__toggle active pull-right">
			<input type="search" placeholder="Search..." class="input-search">
			<button class="mdi mdi-magnify button-search" type="submit">
				<i class="fa fa-search" aria-hidden="true"></i>
			</button>
		</form>
	</th:block>

	<th:block layout:fragment="content">
		<div class="table-responsive clearfix">
			<table class="table table-hover">
				<thead>
					<tr>
						<th>번호</th>
						<th>영어</th>
						<th>한글</th>
						<th>수정일</th>
					</tr>
				</thead>
				<tbody>
					<tr th:if="${not #lists.isEmpty( dictionaryList )}" th:each="row : ${dictionaryList}">
						<td scope="row" th:text="${row.idx}"></td>
						<td class="text-left">
							<a th:href="@{/dictionary/view.do( idx=${row.idx} )}" th:text="${row.english}"></a>
						</td>
						<td th:text="${row.korean}"></td>
						<td th:text="${#temporals.format( row.modifiedDate, 'yyyy-MM-dd' )}"></td>
						<!-- 다음과 같이 조건을 추가하여 list 요소를 추가할 수 있다. -->
						<!-- <td scope="row" th:text="${#string.equals( row.deleteYN, 'Y') ? '삭제됨' : row.idx}"></td> -->
					</tr>
					<tr th:unless="${not #lists.isEmpty( dictionaryList )}">
						<td colspan="4">조회된 결과가 없습니다.</td>
					</tr>
				</tbody>
			</table>

			<div class="btn_wrap text-right">
				<a th:href="@{/dictionary/write.do}" class="btn btn-primary waves-effect waves-light">Write</a>
			</div>

			<th:block layout:fragment="paging">
				<nav aria-label="Page navigation" class="text-center">
					<ul class="pagination">
						<li>
							<a href="#" aria-label="Previous">
								<span aria-hidden="true">&laquo;</span>
							</a>
						</li>
						<li><a href="#">1</a></li>
						<li><a href="#">2</a></li>
						<li><a href="#">3</a></li>
						<li><a href="#">4</a></li>
						<li><a href="#">5</a></li>
						<li>
							<a href="#" aria-label="Next">
								<span aria-hidden="true">&raquo;</span>
							</a>
						</li>
					</ul>
				</nav>
			</th:block>
		</div>
	</th:block>
</html>
```

- **layout:frangment="search"** : 사전 리스트에서 특저어 단어를 검색할 수 있는 영역
- **layout:fragment="content"** : 리스트 페이지의 실제 데이터가 들어가는 영역 리스트는 대부분 테이블로 만들어 처리한다.
- **th:if, th:unless** : if와 else 문과 같다. 단 unless에서는 일반적인 else와 달리 if에 들어가는 조건과 동일한 조건을 가지고 있어야 한다. 즉 if 문과 조건이 동일하지만 if 조건과 다른조건인 경우 unless 구문이 실행되게 되는것.
- **#list.isEmpty** : 해당 함수는 인자로 지정한 데이터가 비어있는지 확인하는 함수이다. 컨트롤러의 openDictionaryList 메소드에서 전달받은 dictionaryList가 비어있는지 확인하는것. isEmpty는 비어있으면 true를 반환하는데 함수 앞의 not은 부정을 의미해 위에 처럼 `not #lists.isEmpty( dictionaryList )` 또는`! #lists.isEmpty( dictionaryList )` 또는 `#lists.isEmpty( dictionaryList ) == false` 왁 타이 적용할 수 있다. not 뒤에 뛰어쓰기는 가독성을 위한 띄어쓰기로 있으나 없으나 상관 없다.
- **th:each**
  - JSTL의 `<c:forEach>` 또는 자바의 forEach와 유사한 기능으로 여기서는 row라는 이름으로 DictionaryList를 순환해 데이터를 출력한다.
- **td scope** : 해당 셀리 row 또는 Column 에대한 헤더 셀임을 명시한다.
- **a th:href** : 해당 영단어를 클릭하게 되면 연결된 href로 이동하게 되며 여기서는 해당 단어 상세 보기로 이동한다.
    - 보통 href 속성에 따라 파라미터로 포함시킬 때 첫번째 파라미터는 ? 로 연결하고 두번째 부터는 &로 연결한다.
    - 타임리프는 URI 뒤에 괄호를 열어 파라미터를 포함한다.
    - ex. 일반적인 GET : /dictinoary/view.do?idx=${idx}&page=${page}
    - ex. 타임리프 GET : /dictionary/view.do(idx=${idx},page=${page})
- **td colspan** : 열 합치기
- **th:herf="@{/dictionary/write.do}** : 단어 입력 페이지 이동


### view Form

- resources/templates/dictionary/view.html을 생성하고 다음과 같이 작성한다.

```
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorate="dictionary/layout/basic">
	<th:block layout:fragment="title">
		<title>this is view page</title>
	</th:block>

	<th:block layout:fragment="content">
		<div class="card-content">
			<form class="form-horizontal form-view" th:object="${dictionary}">
				<div class="form-group">
					<label for="inp-type-1" class="col-sm-2 control-label">인덱스</label>
					<div class="col-sm-10">
						<p class="form-control" th:text="*{idx}"></p>
					</div>
				</div>

				<div class="form-group">
					<label for="inp-type-2" class="col-sm-2 control-label">영어</label>
					<div class="col-sm-10">
						<p class="form-control" th:text="*{english}"></p>
					</div>
				</div>

				<div class="form-group">
					<label for="inp-type-5" class="col-sm-2 control-label">한국어</label>
					<div class="col-sm-10">
						<p class="form-control" th:text="*{korean}"></p>
					</div>
				</div>

				<div class="form-group">
					<label for="inp-type-5" class="col-sm-2 control-label">수정일</label>
					<div class="col-sm-10">
						<p class="form-control" th:text="*{#temporals.format( modifiedDate, 'yyyy-MM-dd' )}"></p>
					</div>
				</div>
			</form>

			<div class="btn_wrap text-center">
				<a th:href="@{/dictionary/list.do}" class="btn btn-default waves-effect waves-light">뒤로가기</a>
				<a th:href="@{/dictionary/write.do( idx=${dictionary.idx} )}" class="btn btn-primary waves-effect waves-light">수정</a>
				<button type="button" class="btn btn-danger waves-effect waves-light" th:onclick="deleteDictioanry([[ ${dictionary.idx} ]])">삭제</button>
			</div>
		</div>
		<!-- /.card-content -->
	</th:block>

	<th:block layout:fragment="add-content">
		<div class="box-content">
			<div class="card-content">
				<div class="clearfix">
					<h4 class="box-title pull-left">Comment</h4>
					<!-- /.box-title -->
				</div>

				<form class="form-horizontal form-view">
					<div class="input-group margin-bottom-20">
						<input type="email" class="form-control" placeholder="">
						<div class="input-group-btn"><button type="button" class="btn waves-effect waves-light"><i class="fa fa-commenting" aria-hidden="true"></i></button></div>
						<!-- /.input-group-btn -->
					</div>
					<ul class="notice-list">
						<!-- notice-content-list -->
					</ul>
				</form>
			</div>
			<!-- /.card-content -->
		</div>
		<!-- /.box-content -->
	</th:block>
	
	<th:block layout:fragment="script">
		<script th:inline="javascript">
			/*<![CDATA[*/

			function deleteDictioanry(idx) {

				if (confirm(idx + "번 게시글을 삭제할까요?")) {
					var uri = /*[[ @{/dictionary/delete.do} ]]*/ null ;
					var html = "";

					html += '<form name="dataForm" action="'+uri+'" method="post">';
					html += '<input type="hidden" name="idx" value="'+idx+'" />';
					$("body").append(html);
					document.dataForm.submit();
				}
			}
			/*[- end of function -]*/

			/*]]>*/
		</script>
	</th:block>
</html>
```

- **CDATA**
  - conform : 자바스크립트의ㅡalert 과 유사한 기능으로 해당 함수를 통해 다시한번 단어에 대한 삭제 여부를 확인한다. 취소 및 확인 버튼은 각각 false, true 반환
  - uri : Dictionary Controller에 선언한 삭제 메소드와 매핑된 URL
  - html : dataFomr이라는 폼 안에 함수의 파라미터로 전달 받은 idx를 hidden 타입으로 추가한다. 따로 HTML에 추가해도 되지만 단어 삭제 이벤트가 발생했을 때만 엘리먼트를 추가하기 위해 동적으로 폼을 생성하는 방식을 사용한다.
  - $("body").append(html) : HTML의 body 태그 안에 Html 변수에 담긴 폼을 추가 한다.
  - document.dataForm.submit() : body에 추가된 폼을 찾아 컨트롤러로 전송한다.

### alert

https://congsong.tistory.com/22?category=749196