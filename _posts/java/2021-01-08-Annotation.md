---
title: (JAVA)Annotation
date: 2020-12-16 22:00:00 +0900
categories: [JAVA]
tags: [Annotation, JAVA]
---

## Annotation

### > 어노테이션(Annotation)이란?

---

>@를 이용한 주석, 자바코드에 주석을 달아 특별한 의미를 부여한 것  
(참고로 클래스, 메소드, 변수 등 모든 요소에 선언이 가능)

어노테이션(Annotaion)은 메타데이터(metadata) 라고 볼 수 있다. 메타데이터란 애플리케이션이 처리해야 할 데이터가 아니라,  
컴파일 과정과 실행 과정에서 코드를 어떻게 컴파일하고 처리할 것인지를 알려주는 정보이다. 어노테이션은 다음과 같은 형태로 작성된다.

#### 용도

- 컴파일러에게 코드 문법 에러를 체크하도록 정보를 제공
- 소프트웨어 개발 툴이 빌드나 배치 시 코드를 자동으로 생성할 수 있도록 정보를 제공
- 실행 시(런타임 시) 특정 기능을 실행하도록 정보를 제공

```java
@Entity
@ < -- 기본적으로 컴파일러에게 어노테이션이라고 알린다.

```

#### 예시 어노테이션

@Override
이 어노테이션은 슈퍼클래스에 대해 오버라이드 되었다는걸 알려준다 따라서 만약 메서드가 
슈퍼클래스와 정확히 매칭되지 않았으면 에러를 날려주는 역할을 한다 (컴파일러 만드는 사람들이 에러를 감지하기 편리함)  

>아래 예를 보자

```java
public class MySuperClass {

    public void doTheThing() {
        System.out.println("Do the thing");
    }
}


public class MySubClass extends MySuperClass{

    @Override
    public void doTheThing() {
        System.out.println("Do it differently");
    }
}
```

물론 이 어노테이션을 사용하는게 강제는 아니지만 , 사용하는게 좋다. 만약 슈퍼클래스가 변경되었는데 그걸 모르고 자식클래스에서 오버라이딩한 클래스를 그대로 냅두면, 의도가 깨져버린다. 만약 @Override 를 선언해뒀다면 바로 에러로 알려준다.


### 어노테이션 타입 정의와 적용

---

>개인적으로 사용할 어노테이션을 만드는 것이 가능하며 어노테이션은 클래스나 인터페이스처럼 자신의 파일에 정의된다.

어노테이션 타입을 정의하는 방법은 인터페이스를 정의하는 것과 유사하다. 다음과 같이@interface를 사용해서 어노테이션을 정의하며, 그 뒤에 사용할 어노테이션 이름이 온다.

```java
@interface MyAnnotation {

    String       value();
    String       name();
    int            age();
    String[]    newNames();

}
```

위의 어노테이션은 4개의 요소를 가지고있고 이름은 MyAnnotation  이다.

@interface 키워드를 주목하라  이것은 자바컴파일러에게 어노테이션 정의라는걸 알려준다.

요소들은 인터페이스에서의 메서드 정의와 유사하다.  자바 기본요소들을 모두 사용할수있고 , 배열도 사용가능하다. (복잡객체사용은 불가능)  
  
    

 위에서 정의한 어노테이션을 사용해 보자.

```java
@MyAnnotation(
    value="123",
    name="Jakob",
    age=37,
    newNames={"Jenkov", "Peterson"}
)
public class MyClass {


}
```

위에 보다시피 각각의 요소에 나만의 값을 지정했다.  
후에 프레임워크에서 이 값을 파싱해서 필요한 정보를 알아낼것이다.

#### 요소(Element)디폴트 값

요소에 기본값을 설정할수 있다. 아래 예를 보자.

```java
@interface MyAnnotation {

    String   value() default "";

    String   name();
    int      age();
    String[] newNames();

}
디폴트값을 설정해두면 아래와 같이 사용할때 값을 넣지 않아도 된다. (아예 보이지도 않음) 

@MyAnnotation(
    name="Jakob",
    age=37,
    newNames={"Jenkov", "Peterson"}
)
public class MyClass {


}
```

참조: <https://hamait.tistory.com/314?category=79137> [HAMA 블로그]
