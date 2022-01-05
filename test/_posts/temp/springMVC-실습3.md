---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 3"
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

# Spring MVC + Gradle Project 3 (lombok 사용)

---

## 0. lombok이란

lombok이란 자동으로 기본적인 상용구를 생성해 줌으로써 코드량을 줄이는데 도움을 주는 Java어노테이션 라이브러리이다.</p>

예를들어 다음과 같이 어떠한 VO를 만들었다고 가정한다면

```
public calss Store extends Common{
    private String companyNmae;
    private String industryTypeCode;

    public String getCompanyName(){
        return companyName;
    }

    public void setCompanyName(String companyName){
        this.companyName = companyNmae;
    }

    public String getindustryTypeCode(){
        return industryTypeCode;
    }

    public void setindustryTypeCode(String industryTypeCode){
        this.industryTypeCode = industryTypeCode;
    }
}
```

위와 같은 코드가 다음과 같이 줄일 수 있다.

```
@Getter
@Setter
public calss Store extends Common{
    private String companyNmae;
    private String industryTypeCode;
}
```


- 즉, Lombok은 어노테이션 기반으로 코드를 자동완성해주는 라이브러리이며 Getter, Setter, Equals, ToString 등 다양한 코드를 자동완성시킬 수 있다.

### Lomkbok 기능

- **@Getter, @Setter**
  - Lombok에서 가장 자주 활용하는 어노테이션이며 해당 어노테이션을 클래스 이름 위에 적용시키면 모든 변수들에 적용가능하며 변수위에 적용하면 해당 변수만 적용이 가능하다.
- **@AllArgsConstructor**
  - 모든 변수를 사용하는 생성자를 자동 완성시켜주는 어노테이션이다.
  - 위의 예를 따르면 다음 생성자를 자동 생성시켜주는 역할을 한다.

    ```
    public Store(Stirng companyName, String industryTypeCode, ...){
        this.companyName = companyName;
        this.industryTypeCode = industryTypeCode;
        ...
    }
    ```

- **@NoArgsConstructor**
  -  어떠한 변수도 사용하지 않는 기본 생성자를 자동완성 시켜주는 어노테이션
  -  위의 예를 따르면 아므 생성자를 자동 생성시켜주는 역할을 한다.

    ```
    public Store(){

    }
    ```

- **@RequiredArgsConstructor**
  - 특정 변수만 활용하는 생성자를 자동완성시켜주는 어노테이션이다. 생성자의 인자로 추가할 변수에 @NonNull 어노테이션을 붙여서 해당 변수르르 생성자의 인자로 추가할 수 있다. 아니면 해당 변수를 final 로 선언해도 의존성을 주입할 수 있다.
  - 예로는 다음과 같다.

    ```
    @Getter
    @RequiredArgsConstructor
    public class Store extends Common{
        @NonNull
        private String companyNmae;
        private final String industryTypeCode;
        ...
    }
    
    --------------------------------------------
    자동 생성되는 생성자.
    public Store(String companyName, String industryTypeCode){
        this.companyName = companyName;
        this.industryTypeCode = industryTypeCode;
    }
    ```

- **@EqualsAndHashCode**
  - 클래스에 대한 equal 함수와 hashCode 함수를 자동으로 생성해 준다. 만약 서로 다른 두 객체에서 특정 변수의 이름이 똑같은 경우 같은 객체로 판단하고 싶다면 아래 


https://mangkyu.tistory.com/78