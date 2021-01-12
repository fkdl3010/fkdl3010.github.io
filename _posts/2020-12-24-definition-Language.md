---
title: 웹 도구들 정의 및 정리
date: 2020-12-24 00:00:00 +0900
categories: [Language]
tags: [Language ]
---
<style type="text/css">
    ul{
        padding-left: 10px;
    }
    li{
        color: lightskyblue;
    }
</style>

>시험을 보는 중 문득 내가 사용하고 있는 언어들의 개념을 확실히 잡고싶은 생각이 들었다.

## Language

- ### JSP

```s
1. Java Server Page
2. HTML 문서내에서 JAVA 코드를 사용할 수 있는 서블릿(Servlet) 기반의 서버측 스크립트 언어이다
    JSP 스크립트 요소
    1. <%@ 지시어 %> : 지시어 (directive)
    2. <%! 선언부 %> : 선언부 (declaration), 전역변수 선언, 메소드 정의
    3. <%= 표현식 %> : 표현식 (expression), 결과 출력(변수, 메소드 호출 결과, 식)
    4. <% 스크립트릿 %> : 스크립트릿 (scriptlet), Java 코드
JSP(JavaServer Pages)는 “HTML 정적 페이지에 자바 코드를 삽입하여 웹 서버를 동적으로 웹 브라우저에 보여주는 언어”이다.
```

- ### EL

```s
EL
1. Expression Language (표현언어)
2. JSP의 새로운 스크립트 언어이다.
3. 기존의 표현식(<%=표현식%>)을 대체하는 역할이다.
4. 대체방식
    <%=표현식%> -> ${표현언어}
5. 데이터를 저장할 수 있는 4개 영역에서 사용할 수 있다.
    1) pageContext
    2) request
    3) session
    4) application
6. 각 영역의 우선순위
    pageContext > request > session > application
7. 각 영역의 스코프 키워드
    1) pageContext: pageScope
    2) request: requestScope
    3) session: sessionScope
    4) application: applicationScope
8. 저장이 "속성"으로 된 경우 다음과 같이 사용한다.
    1) pageContext.setAttribute("name", "에밀리") -> ${name}
                                                    -> ${pageScope.name}
    2) request.setAttribute("age", 25) -> ${age}
                                        -> ${requestScope.age}
9. request에 파라미터로 저장된 경우 다음과 같이 사용한다.
    1) <input type="text" name="id" /> -> ${param.id}
    2) <input type="checkbox" name="hobbies" /> -> ${paramValues.hobbies[0]}
        <input type="checkbox" name="hobbies" /> -> ${paramValues.hobbies[1]}
10. EL 연산자
    1) +
    2) -
    3) *
    4) /, div : 나누기
    5) %, mod : 나머지
    6) >, gt  : 크다   {a gt 5}, {a > 5}
    7) >=, ge : 크거나 같다
    8) <, lt  : 작다
    9) <=, le : 작거나 같다
    10) ==, eq : 같다
    11) !=, ne : 같지 않다
    12) and    : 그리고
    13) or     : 또는
    14) not    : 부정
    15) empty  : 비어 있다

```

- ### JSTL

```s
JSTL(JavaServer Pages Standard Tag Library)
1. JSP 표준 태그 라이브러리(여러 프로그램이 공통으로 사용하는 코드를 모아놓은 집합)의 약어
2. 이미 만들어진 태그를 이용하여 JSP환경에서 보다 가독성 좋게 JAVA를 사용할 수 있다.
3. 기본적으로 제공하는 태그 외에도 자신만의 태그를 만들어서 사용할 수 있다.

taglib를 사용하려면 taglib 지시어를 작성해야 한다.
    1. 코어(core) 라이브러리: if, for문
        <%@ taglib uri="" prefix="c" %>
    2. 형식(fmt) 라이브러리: 숫자, 날짜 형식
        <%@ taglib uri="" prefix="fmt" %>
```

>지속적으로 추가할 예정

---

## 그 외

- ### JDBC

__자바에서 DB프로그래밍을 하기 위해 사용되는 API__

- ### DBCP

__데이터 베이스에 연결하여 사용하는 경우 데이터 베이스에 접속하기 위해 Connection 등의 객체를 생성해야 한다.이게 혼자서 쓸 때는 접속 할 때마다 객체를 생성해도 괜찮지만 사람들이 많이 접속하는 사이트에서는 사용자 한 명당 하나씩 계속 객체를 생성하게 되면 서버가 객체를 생성하는데 리소스를 많이 쓰게 된다.이러한 현상을 해결하고자 '커넥션 풀'이라는 공간을 만들어 커넥션 객체들을 담아놓고 차후 사용자가 데이터 베이스에 접속을 시도하면 커넥션 풀에 담겨있는 커넥션 객체를 하나하나 꺼내주는 방법을 적용시켰다.이것이 바로 DBCP(DataBase Connection Pool)이다.__
