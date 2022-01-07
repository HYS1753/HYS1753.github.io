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

## 1. Lomkbok 기능

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
  -  위의 예를 따르면 다음 생성자를 자동 생성시켜주는 역할을 한다.

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
  - 클래스에 대한 equal 함수와 hashCode 함수를 자동으로 생성해 준다. 만약 서로 다른 두 객체에서 특정 변수의 이름이 똑같은 경우 같은 객체로 판단하고 싶다면 다음과 같이 표현한다.

    ```
    @RequiredArgsConstructor
    @EqualsAndHashCode(of={"companyName", "industryTypeCode}, callSuper = false)
    public class Store extends Common {
        @NonNull
        private String companyName;
        @NonNull
        private String industryTypeCode;
    }
    ```
    
  - 이는 @EqualsAndHashCode(of={"companyName", "industryTypeCode}, callSuper = false)로 설정해 companyName과 industryTypeCode가 동일하다면 같은 객체로 인식하도록 해준다.
  - 또한, Common을 상속하고 있는데 상위 클래스의 경우 적용시키지 않기 위해 callSuper = false로 한다.

    ```
    @RestController
    @RequsetMapping(value="/store")
    @Log4j2
    public class StoreController {
        @GetMapping(vaule = "/test")
        private ResponseEntity test() {
            Store store1 = new Store("이름", "코드1");
            Store store2 = new Store("이름", "코드1");
            Store store3 = new Store("이름", "코드2");

            log.debug(store1.equals(store2)); // True

            log.debug(store1.equals(store3)); // False

            return ResponseEntity.ok().build();
        }
    }


- **@ToString**
  - 해당 어노테이션은 클래스의 변수들을 기반으로 ToString 메소드를 자동으로 완성시켜 준다. 
  - 출력을 원하지 않는 변수에 @ToString.Exclude 어노테이션을 붙여주면 출력을 제외시킬 수 있다.
  - 또한, 상위 클래스에 대해서도 toString을 적용시키고자 한다면 상위 클래스에 @ToString(callSuper = true) 를 적용시켜 준다.

    ```
    @ToString
    @AllArgsConstructor
    public class Store extends Common {

        @ToString.Exclude
        private String companyName;
        private String industryTypeCode;
    }

    ------ 결과 ------
    StoreVO(industryTypeCode=000)
    ```

- **@Data**
  - @Data 어노테이션을 활용하면 @ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor 를 자동 완성시켜 준다. 실무에서는 너무 무겁고 객체 안정성을 지키기 때문에 @Data 사용을 지양한다.

- **@Delegate**
  - 해당 어노테이션은 한 객체의 메소드를 다른 객체로 위임시켜 준다. 만약 어떤 Store에 Review를 추가하는 상황이면 다음과 같이 작성 가능하다.

    ```
    @Getter
    @NoArgsConstructor
    public calss Store extends Common{
        private String companyName;
        private String industryTypeCode;
        @Delegate
        private List<Review> reviewList;
    }

    ------ @Delegate 없을 시 ------
    store.getReviewList().add(review); 
    ------ @Delegate 있을 시 
    store.add(review);
    ```

- **@Log**
  - @Log4j2와 같은 어노테이션을 활용하며 해당 클래스의 로그 클래스를 자동 완성시켜 준다. 


## 2. Lombok 사용 방법

- build.gradle의 depnedencies 부분에 lombok 추가 
    ```
    // lombok 설치 1.18.22
	compile "org.projectlombok:lombok:1.18.22"
    ```
- `build.gradle 우클릭 - Gradle - Refresh Gradle Project 클릭`
- `해당 프로젝트 우클릭 - Properites - java build path - libraries - classpath` 에 보면 다음과 같이 lombok이 설치된 것을 확인할 수 있다.

![lombok.jar 설치 확인](/assets/images/springmvc14.PNG){: .align-center}

- 또한, Controller, VO 등 java에서 lombok이 설치 되어있다면 lombok에서 제공하는 어노테이션을 사용할 수 있다.
  - @Da 까지 쓰고 `Ctrl + Space` 시 다음과 같이 lombok 어노테이션 사용가능한 것을 확인할 수 있다.

![lombok.jar 설치 확인](/assets/images/springmvc15.PNG){: .align-center}