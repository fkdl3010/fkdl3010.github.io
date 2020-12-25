---
title: (MyBatis, MVC) -2- 회원가입 기능구현
date: 2020-12-26 03:00:00 +0900
categories: [JAVA-WEB, MyBatis]
tags: [MyBatis, MVC, AJAX]
---
<style>

    h2{
        color: coral;
    }
    h3{
        color: #ffccbc;
    }
</style>
## SignUp&Out 기능구현

> MVC패턴으로 맞춰서 진행
> index.jsp에서부터 시작합니다. 
> 기능구현 및 페이지 이동의 경로는 .member의 suffix값을 가집니다.

---

## SignUp 기능

### - index의 회원가입페이지 이동 버튼

> 실제로는 `header.jsp`에 위치하고있다.

```html
<input type="button" value="회원가입" onclick="location.href='/MyHome/signUpPage.member'"/>
```

### - /signUpPage.member경로 Controller

> signUpPage.member의 요청 처리

```java
case "/signUpPage.member":
    pathNRedirect = new PathNRedirect();
    pathNRedirect.setPath("member/signUpPage.jsp");
    break;
```

### - signUpPage

>가입하기 버튼 ajax로 구현합니다.

```javascript
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
<jsp:include page="../template/header.jsp">
    <jsp:param value="회원가입" name="title"/>
</jsp:include>

<form id="f" method="post">
	
    <label for="mId">아이디</label> <br/>
    <input type="text" id="mId" name="mId" /> <br>
    <span id="idCheckResult"></span> <br/>

    <label for="mPw">비밀번호</label> <br>
    <input type="password" id="mPw" name="mPw" /> <br>
    <span id="PwCheckResult"></span> <br>

    <label for="mPw2">비밀번호확인</label> <br>
    <input type="password" id="mPw2" name="mPw2" /> <br>
    <span id="pwConfirmResult"></span> <br>

    <label for="mName">성명</label> <br>
    <input type="text" id="mName" name="mName" /> <br> <br>

    <label for="mEmail">이메일</label> <br>
    <input type="text" id="mEmail" name="mEmail" /> <br>
    <span id="emailCheckResult"></span> <br>

    <label for="mPhone">전화번호</label> <br>
    <input type="text" id="mPhone" name="mPhone" /> <br> <br>

    <label for="mAddresss">주소</label> <br>
    <input type="text" id="mAddresss" name="mAddress" /> <br> <br>

    <input type="button" value="가입하기" id="signUpBtn" />
    <input type="button" value="입력취소" id="clearBtn" />

</form>

<script>
function fn_signUp(){
    $('#signUpBtn').click(function(){
        $.ajax({
            url: '/MyHome/SignUp', // 요청을 보낼 주소
            type: 'post',          // 데이터 보내는 방식 
            data: $('#f').serialize(), // 아이디 f 폼의 정보를 모두 데이터로 보냄
            success: function(responseObj){ // response정보를 responseObj로 받는다.
                if(responseObj.result){
                    alert('회원가입에 성공하였습니다.');
                    location.href='/MyHome/loginPage.member';
                }else{
                    alert('실패하였습니다.');
                }
            },
            error: function(){ alert('실패'); }
        });
    });
}
</script>
<%@ include file="../template/footer.jsp"%>
```

### - /SignUp 요청을 받을 Servlet

> ajax처리는 Servlet으로 진행합니다.
> doGet() 메소드만 작성합니다.

```java

@WebServlet("/SignUp")
public class SignUp extends HttpServlet {
	private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        String mId = request.getParameter("mId");
        String mPw = request.getParameter("mPw");
        String mName = request.getParameter("mName");
        String mEmail = request.getParameter("mEmail");
        String mPhone = request.getParameter("mPhone");
        String mAddress = request.getParameter("mAddress");
        
        MemberDto memberDto = new MemberDto();
        memberDto.setmId(mId);
        memberDto.setmPw(mPw);
        memberDto.setmName(mName);
        memberDto.setmEmail(mEmail);
        memberDto.setmPhone(mPhone);
        memberDto.setmAddress(mAddress);
        
        int result = MemberDao.getInstance().insert(memberDto);
        
        JSONObject responseObj = new JSONObject();
        
        if(result > 0) {
            responseObj.put("result", true);
        }else {
            responseObj.put("result", false);
        }
        
        response.setContentType("application/json; charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println(responseObj);
        out.close();
    }

    }

}
```

- insert DAO

    ```java
    public int insert(MemberDto memberDto) {
        SqlSession ss = factory.openSession(false); // false: 수동커밋으로 전환
        int result = ss.insert("mybatis.mapper.member.insert", memberDto);
        if(result >0) {
            ss.commit();
        }
        ss.close();
        return result;
    }
    ```

- insert 매퍼 (mybatis)

    ```xml
    <insert id="insert" parameterType="dto.MemberDto">
        insert into member
        values(member_seq.nextval, #{mId}, #{mPw}, #{mName}, #{mEmail}, #{mPhone}, #{mAddress}, sysdate)
    </insert>
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
        f.action = '/MyHome/logout.member';
        f.submit();
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