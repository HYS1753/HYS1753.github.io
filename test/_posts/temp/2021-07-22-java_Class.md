---
author_profile: true
date: 2021-07-22
title: "Java Class정의 및 사용"
categories: 
    - Java
tag: 
    - Class
    - overloading

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Java Class 생성

## Car.java

- Field : Class 내에서 사용할 데이터 저장
- 생성자 : new 연산자로 호출되는 특별한 블록으로 객체 생성 시 초기화를 담당한다.
- 메소드 : 객체의 동작에 해댱하는 코드로 메소드 이름을 호출하면 블록안의 코드가 일괄적으로 실행됨.
- **메소드 오버로딩(Overloading)**
    - 생성자 또는 메소드에서 사용하며 매개 변수를 달리하는 메소드 여러개 선언하는 것
    - 즉, 클래스 내에 같은 이름의 메소드를 여러 개 선언하는 것이며 하나의 메소드 이름으로 다양한 기능을 담기 위해 만들어진 개념
    - 오버로딩할 메소드의 리턴타입은 무관하고, 메소드 이름은 같아야 하며, 매개변수의 타입과 개수 순서는 달라야 한다.

```java
public class Car {
    // Field (company, model, color, maxSpeed, minSpeed 가 field name)
    String company = "현대자동차";
    String model;
    String color;
    int maxSpeed;
    int minSpeed;
	
    // 생성자
    Car(){
        
    }

    Car(String model){
        this.model = model;
    }
	
    Car(String model, String color){
    	this.model = model;
    	this.color = color;
    }
	
    Car(String model, String color, int maxSpeed, int minSpeed){
    	this.model = model;
    	this.color = color;
    	this.maxSpeed = maxSpeed;
    	this.minSpeed = minSpeed;
    }
	
    // 메소드 선언
    // return 값이 없는 메소드
    void carPrint() {
    	System.out.println("company: " + this.company);
    	System.out.println("model: " + this.model);
    	System.out.println("color: "+ this.color);
    	System.out.println("maxSpeed: " + this.maxSpeed);
    	System.out.println("minSpeed: " + this.minSpeed);
	}
	
    // return 값이 있는 메소드
    int increase_maxSpeed(int x) {
    	this.maxSpeed = this.maxSpeed + x;
    	return this.maxSpeed;
    }
	
    int reduce_maxSpeed(int x) {
    	this.maxSpeed = this.maxSpeed - x;
    	return this.maxSpeed;
    }
	    
    // 메소드 오버로딩 Overloading
    // 만약 아래의 예처럼 change_Speed에 입력으로 1개 정수형 할 경우 maxSpeed만 변경
    // 하지만 2개의 입력이 있을시 max, min 모두 변경
    int change_Speed(int x) {
    	this.maxSpeed = x;
    	return this.maxSpeed;
    }
	
    void change_Speed(int x, int y) {
        this.maxSpeed = x;
    	this.minSpeed = y;
    }
	
}
```

## CarExample.java
```java
public class CarExample {
    public static void main(String[] args) {
    	// Car.java에서 정의한 Car Class를 통해 객체 생성
    	System.out.println("Car Class의 생성자를 사용해 객체 생성\n");
    	Car car1 = new Car();
    	System.out.println("\ncar1.company : " + car1.company);
    
    	Car car2 = new Car("자가용");
    	System.out.println("\ncar2.company : " + car2.company);
    	System.out.println("car2.model : " + car2.model);
    
    	Car car3 = new Car("자가용", "빨강");
    	System.out.println("\ncar3.company : " + car3.company);
    	System.out.println("car3.model : " + car3.model);
    	System.out.println("car3.color : " + car3.color);
	
    	Car car4 = new Car("자가용", "빨강", 200, 0);
    	System.out.println("\ncar4.company : " + car4.company);
    	System.out.println("car4.model : " + car4.model);
    	System.out.println("car4.color : " + car4.color);
       	System.out.println("car4.maxSpeed : " + car4.maxSpeed);
    	System.out.println("car4.minSpeed : " + car4.minSpeed);
		
    	// Car.java에서 정의한 메소드 호출
    	System.out.println("\n\nCar Class에 선언된 메소드 호출\n");
    	car4.carPrint();
		
    	System.out.println("\nmaxSpeed가 " + car4.increase_maxSpeed(10) + "로 증가");
    	car4.carPrint();
		
    	System.out.println("\nmaxSpeed가 " + car4.reduce_maxSpeed(10) + "로 감소");
    	car4.carPrint();
		
		
    	// 오버로딩 사용
    	System.out.println("\nmaxSpeed가 " + car4.change_Speed(150) + "으로 변경");
    	car4.carPrint();
		
    	System.out.println("\nmaxSpeed를 300으로 minSpeed를 100으로 변경");
    	car4.change_Speed(300, 100);
    	car4.carPrint();
    }
}

```

## 출력
```
Car Class의 생성자를 사용해 객체 생성


car1.company : 현대자동차

car2.company : 현대자동차
car2.model : 자가용

car3.company : 현대자동차
car3.model : 자가용
car3.color : 빨강

car4.company : 현대자동차
car4.model : 자가용
car4.color : 빨강
car4.maxSpeed : 200
car4.minSpeed : 0


Car Class에 선언된 메소드 호출

company: 현대자동차
model: 자가용
color: 빨강
maxSpeed: 200
minSpeed: 0

maxSpeed가 210로 증가
company: 현대자동차
model: 자가용
color: 빨강
maxSpeed: 210
minSpeed: 0

maxSpeed가 200로 감소
company: 현대자동차
model: 자가용
color: 빨강
maxSpeed: 200
minSpeed: 0

maxSpeed가 150으로 변경
company: 현대자동차
model: 자가용
color: 빨강
maxSpeed: 150
minSpeed: 0

maxSpeed를 300으로 minSpeed를 100으로 변경
company: 현대자동차
model: 자가용
color: 빨강
maxSpeed: 300
minSpeed: 100
```