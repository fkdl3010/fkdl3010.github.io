---
title: (MyBatis, MVC) -3- login기능구현
date: 2020-12-26 02:00:00 +0900
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

    h2{
        color: coral;
    }
    h3{
        color: #ffccbc;
    }
</style>

## Login 기능구현

> MVC패턴으로 맞춰서 진행  
> index.jsp에서부터 시작합니다.  
> 기능구현 및 페이지 이동의 경로는 .member의 suffix값을 가집니다.

<br>

_목차_

- [login기능](#login기능)
  - [- index의 로그인페이지 이동 버튼](#--index의-로그인페이지-이동-버튼)
  - [- /loginPage.member경로 Controller](#--loginpagemember경로-controller)
  - [- loginPage](#--loginpage)
  - [- /login.member경로 Controller](#--loginmember경로-controller)
  - [- login의 Command](#--login의-command)
  - [- loginResult.jsp 로그인 결과](#--loginresultjsp-로그인-결과)
- [logOut기능](#logout기능)
  - [- index의 로그아웃페이지 이동 버튼](#--index의-로그아웃페이지-이동-버튼)
  - [- /logout.member경로 Controller](#--logoutmember경로-controller)
  - [- logout의 Command](#--logout의-command)

---

## login기능

### - index의 로그인페이지 이동 버튼

> 실제로는 `header.jsp`에 위치하고있다.

```html
<input type="button" value="MyHome로그인" onclick="location.href='/MyHome/loginPage.member'"/>
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

>로그인 버튼

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


### - /login.member경로 Controller

```java
case "/login.member":
    command = new MemberLoginCommand();
    pathNRedirect = command.execute(request, response);
    break;
```

### - login의 Command

>MemberloginCommand

```java
package command.member;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import common.PathNRedirect;
import dao.MemberDao;
import dto.MemberDto;

public class MemberLoginCommand implements MemberCommand{

    @Override
    public PathNRedirect execute(HttpServletRequest request, HttpServletResponse response) {
        
        String mId = request.getParameter("mId");
        String mPw = request.getParameter("mPw");
        
        MemberDto memberDto = new MemberDto();
        memberDto.setmId(mId);
        memberDto.setmPw(mPw);
        
        // 로그인 한 회원 정보는 session에 올린다.
        MemberDto loginDto = MemberDao.getInstance().selectBymIdmPw(memberDto);
        if(loginDto != null) {
            HttpSession session = request.getSession();
            session.setAttribute("loginDto", loginDto);
        }
        PathNRedirect pathNRedirect = new PathNRedirect();
        pathNRedirect.setPath("member/loginResult.jsp");
        pathNRedirect.setRedirect(false); // forward( mId, mPw를 보낼 수 있따).
        
        return pathNRedirect;
    }

}
```

- selectBymIdmPw DAO

    ```java
    public MemberDto selectBymIdmPw(MemberDto memberDto	) {
        SqlSession ss = factory.openSession();
        MemberDto dto = ss.selectOne("mybatis.mapper.member.selectBymIdmPw", memberDto);
        
        ss.close();
        return dto;
    }
    ```

- selectBymIdmPw 매퍼 (mybatis)

    ```xml
    <select id="selectBymIdmPw" parameterType="dto.MemberDto" resultType="dto.MemberDto">
        select *
        from member
        where mid = #{mId}
        and mpw = #{mPw}
    </select>
    ```

### - loginResult.jsp 로그인 결과

```javascript
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<script type="text/javascript">
    /* 로그인의 성공 실패 여부는 session의 loginDto 존재여부를 확인하면된다. */
    if( '${loginDto}' != ''){
        alert('${param.mId}' + '님 환영합니다.');
        location.href = '/MyHome/index.member';
    } else{
        alert('제출된 정보와 일치하는 회원이 없습니다.');
        location.href = '/MyHome/loginPage.member';
    }
</script>

```

---

## logOut기능

### - index의 로그아웃페이지 이동 버튼

> 실제로는 `header.jsp`에 위치하고있다.

```javascript
<input type="button" value="로그아웃" onclick="fn_logout(this.form)"/>
<script>
function fn_logout(f){
    if(confirm('로그아웃 하시겠습니까?')){
        location.href = '/MyHome/logout.member';
    }
}
</script>
```

### - /logout.member경로 Controller

> logout.member의 요청 처리

```java
case "/logout.member":
    command = new MemberLogoutCommand();
    pathNRedirect = command.execute(request, response);
    break;
```

### - logout의 Command

> MemberLogoutCommand

```java
package command.member;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import common.PathNRedirect;

public class MemberLogoutCommand implements MemberCommand{

    @Override
    public PathNRedirect execute(HttpServletRequest request, HttpServletResponse response) {

    //		로그아웃은 session을 비워주면 됩니다.
        HttpSession session = request.getSession();
        
        if(session.getAttribute("loginDto") != null) {
            session.invalidate();
        }
        PathNRedirect pathNRedirect = new PathNRedirect();
        pathNRedirect.setPath("index.jsp");
        pathNRedirect.setRedirect(true);
        
        return pathNRedirect;
    }

}
```

    session을 비운 후 index페이지로 이동합니다

