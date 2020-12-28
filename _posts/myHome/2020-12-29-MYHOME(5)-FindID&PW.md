---
title: (MyBatis, MVC) -5- Find ID&PW
date: 2020-12-28 22:00:00 +0900
categories: [JAVA-WEB, MyBatis]
tags: [MyBatis, MVC, AJAX]
---
<style>
    img + em {
        display: block;
        text-align: center;
        font-size: .8rem;
        color: $grey-color-light;
    }
    img{
        height: 10em;
    }
    h2{
        color: coral;
    }
    h3{
        color: #ffccbc;
    }
</style>

## Find ID&PW 기능 구현 (아아디, 비밀번호 찾기)

> MVC패턴으로 맞춰서 진행  
> index.jsp에서부터 시작합니다.  
> 기능구현 및 페이지 이동의 경로는 .member의 suffix값을 가집니다.

<br>

_목차_

---

## Find ID기능

> 실제로는 `header.jsp`에 위치하고있다.

```html
<input type="button" value="MyHome 로그인" onclick="location.href='/MyHome/loginPage.member'" />
```

### - /loginPage.member경로 Controller

> loginPage.member의 요청 처리

```java
case "/loginPage.member":
    pathNRedirect = new PathNRedirect();
    pathNRedirect.setPath("member/loginPage.jsp");
    pathNRedirect.setRedirect(false);
    break;
```

### - loginPage

> 아이디 찾기 버튼  

```javascript
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<jsp:include page="../template/header.jsp">
    <jsp:param value="로그인페이지" name="title"/>
</jsp:include>

<script type="text/javascript">
    function fn_login(f){
        if(f.mId.value == '' || f.mPw.value == ''){
            alert('아이디와 비밀번호를 모두 입력하세요');
            return;
        }
        f.action = '/MyHome/login.member';
        f.submit();
    }
</script>	
    <div class="login-box">
        <form method="post">
            <label for="mId">아이디</label><br>
            <input type="text" name="mId" id="mId" autofocus="autofocus"/><br><br>
            <label for="mPw">비밀번호</label><br>
            <input type="password" name="mPw" id="mPw"/><br><br>
            <input type="button" value="로그인" onclick="fn_login(this.form)"/>
            <a href="/MyHome/findIdPage.member">아이디 찾기</a>/&nbsp;
            <a href="/MyHome/findPwPage.member">비밀번호 찾기</a>
            
        </form>
    </div>

<%@ include file="../template/footer.jsp" %>
```

- /findIdPage.member경로 Controller

    ```java
    case "/findIdPage.member":
        pathNRedirect = new PathNRedirect();
        pathNRedirect.setPath("member/findIdPage.jsp");
        break;
    ```

![FindPage](/assets/img/study/FindId.png) *FindIdPage*

### - /findIdPage.jsp 이동

> ajax로 처리합니다.  
> ajax처리를 위한 커맨드는 따로 작성합니다.(MVC x)

```javascript
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<jsp:include page="../template/header.jsp">
	<jsp:param value="아이디찾기" name="title" />
</jsp:include>

<script>

    $(document).ready(function(){
        $('#findIdBtn').click(fn_findId);
    });

    function fn_findId() {
        $.ajax({
            url: '/MyHome/MemberFindId',  // 매핑(/MemberFindId)을 가진 별도의 Servlet으로 간다.
            type: 'post',
            data: 'mEmail=' + $('#mEmail').val(),  // url로 보내는 파라미터
            dataType: 'text',  // 받아 오는 데이터의 타입
            success: function(responseText) {  // responseText: 받아 오는 데이터
                if (responseText.trim() == 'no') {
                    $('#findIdResult').text('해당하는 회원 정보가 없습니다.');
                    $('#findIdResult').css('color', 'red');
                } else {
                    $('#findIdResult').text('회원님의 아이디는 "' + responseText + '"입니다.');
                    $('#findIdResult').css('color', 'green');
                }
            },
            error: function(){ alert('실패'); }
        });
    }

</script>

<form>
    가입 당시 이메일을 입력하세요.<br/><br/>
    <input type="text" name="mEmail" id="mEmail" />
    <input type="button" value="아이디 찾기" id="findIdBtn" />
    <input type="button" value="로그인 하러 가기" onclick="location.href='/MyHome/loginPage.member'" />
</form>
<br/><br/>

<%-- 아이디 찾기 결과가 나타날 위치 --%>
<div id="findIdResult"></div>

<%@ include file="../template/footer.jsp" %>
```

### - MemberFindId Command

```java
@WebServlet("/MemberFindId")
public class MemberFindId extends HttpServlet {
    private static final long serialVersionUID = 1L;
    public MemberFindId() {
        super();
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        String mEmail = request.getParameter("mEmail");
        
        MemberDto memberDto = MemberDao.getInstance().selectBymEmail(mEmail);
        
        String responseText = null;
        if (memberDto == null) {
            responseText = "no";
        } else {
            responseText = memberDto.getmId();
        }
        
        response.setContentType("text/plain; charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println(responseText);
        out.close();
        
    }
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

- selectBymEmail DAO

    ```java
    public MemberDto selectBymEmail(String mEmail) {
        SqlSession ss = factory.openSession();
        MemberDto dto = ss.selectOne("mybatis.mapper.member.selectBymEmail", mEmail);
        ss.close();
        return dto;
    }
    ```

- selectBymEmail mapper

    ```xml
    <select id="selectBymEmail" parameterType="String" resultType="dto.MemberDto">
        SELECT *
            FROM MEMBER
            WHERE MEMAIL = #{mEmail}
    </select>
    ```

- 결과

![FindIdResult](/assets/img/study/FindIdResult.png)*FindIdResult*

---

## Find Pw 기능

[loginPage](#--loginpage)에서 시작합니다.
> 비밀번호 찾기 버튼 

- /findPwPage.member경로 Controller

    ```java
    case "/findIdPage.member":
        pathNRedirect = new PathNRedirect();
        pathNRedirect.setPath("member/findPwPage.jsp");
        break;
    ```

### - /findPwPage.jsp 이동

> ajax로 처리합니다.  
> ajax처리를 위한 커맨드는 따로 작성합니다.(MVC x)
> 비밀번호를 보여주지않고 비밀번호를 재설정 하도록 구현하였습니다.

```javascript
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<jsp:include page="../template/header.jsp">
	<jsp:param value="비밀번호찾기" name="title" />
</jsp:include>

<script>

    // 페이지 로드 이벤트 처리
    $(function(){
        $('#findPwBtn').click(fn_findPw);
    });

    function fn_findPw() {
        $.ajax({
            url: '/MyHome/MemberFindPw',
            type: 'post',
            data: 'mEmail=' + $('#mEmail').val(),
            dataType: 'text',
            success: function(responseText) { //json데이터를 텍스트로 응답받으면 .trim 필수 이다. 안하면 인식안됨
                if (responseText.trim() == 'no') {
                    alert('해당하는 회원 정보가 없습니다.');
                } else {
                    alert('회원 정보가 확인되었습니다. 새로운 비밀번호를 설정하세요.');
                    location.href = '/MyHome/changePwPage.member?mNo=' + responseText.trim();
                }
            },
            error: function(){ alert('실패'); }
        });
    }

</script>

    <h3>이메일 인증</h3>
    <%-- 스프링에서 구글 메일로 이메일을 보내 주는 라이브러리를 사용합니다. --%>
    <form>
        가입 당시 이메일을 입력하세요.<br/><br/>
        <input type="text" name="mEmail" id="mEmail" />
        <input type="button" value="비밀번호 찾기" id="findPwBtn" />
        <input type="button" value="로그인 하러 가기" onclick="location.href='/MyHome/loginPage.member'" />
    </form>

    <h3>전화번호 인증</h3>
    <%-- 문자(SMS)를 보내 주는 라이브러리 사용은 돈이 듭니다. --%>

<br/><br/>

<%-- 아이디 찾기 결과가 나타날 위치 --%>
<div id="findIdResult"></div>

<%@ include file="../template/footer.jsp" %>
```

### - MemberFindPw Command

```java
package command.member;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import dao.MemberDao;
import dto.MemberDto;


@WebServlet("/MemberFindPw")
public class MemberFindPw extends HttpServlet {
    private static final long serialVersionUID = 1L;
    public MemberFindPw() {
        super();
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        String mEmail = request.getParameter("mEmail");
        
        MemberDto memberDto = MemberDao.getInstance().selectBymEmail(mEmail);
        
        String responseText = null;
        if (memberDto == null) {
            responseText = "no";
        } else {
            responseText = memberDto.getmNo() + "";
        }
        
        response.setContentType("text/plain; charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println(responseText);
        out.close();
        
    }
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```