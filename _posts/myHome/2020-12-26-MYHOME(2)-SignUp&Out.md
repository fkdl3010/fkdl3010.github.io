---
title: (MyBatis, MVC) -2- 회원가입 및 탈퇴 기능구현
date: 2020-12-26 01:00:00 +0900
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

<br>

_목차_

- [SignUp 기능](#signup-기능)
  - [- index의 회원가입페이지 이동 버튼](#--index의-회원가입페이지-이동-버튼)
  - [- /signUpPage.member경로 Controller](#--signuppagemember경로-controller)
  - [- signUpPage](#--signuppage)
  - [- /SignUp 요청을 받을 Servlet](#--signup-요청을-받을-servlet)
- [SignOut 기능](#signout-기능)
  - [- index의 회원탈퇴 페이지 이동 버튼](#--index의-회원탈퇴-페이지-이동-버튼)
  - [- /signOutPage.member경로 Controller](#--signoutpagemember경로-controller)
  - [- signOutPage](#--signoutpage)
  - [- /SignOut 요청을 받을 Servlet](#--signout-요청을-받을-servlet)

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

---

## SignOut 기능

### - index의 회원탈퇴 페이지 이동 버튼

> 실제로는 `header.jsp`에 위치하고있다.

```javascript
<input type="button" value="회원탈퇴" onclick="fn_signOut()"/>

<script>
    function fn_signOut(){
        location.href= '/MyHome/signOutPage.member';
    }
</script>
```

### - /signOutPage.member경로 Controller

> signOutPage.member의 요청 처리

```java
case "/signOutPage.member":
    pathNRedirect = new PathNRedirect();
    pathNRedirect.setPath("member/signOutPage.jsp");
    break;
```

### - signOutPage

```javascript
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<jsp:include page="../template/header.jsp">
	<jsp:param value="마이페이지" name="title"/>
</jsp:include>

<form id="f">

    <h1>회원 정보를 확인하세요.</h1>

    아이디<br>
    ${loginDto.mId }<br><br> <!--MemberLoginCommand에서 session에 저장하였다. -->


    성명<br>
    ${loginDto.mName} <br><br>


    가입일<br>
    ${loginDto.mRegdate }<br><br>

    <!-- hidden -->
    <input type="hidden" name="mNo" value="${loginDto.mNo }" />

    <input type="button" value="회원 탈퇴하기" id="signOutBtn" />
    <input type="button" value="되돌아가기" onclick="location.href=document.referrer" />	

</form>
<script>
    $(function(){
        $('#signOutBtn').click(fn_signOut);
    });

    function fn_signOut(){
        if(confirm('정말 탈퇴하시겠습니까?')){
            
            $.ajax({
                url: '/MyHome/SignOut',
                type: 'post',
                data: $('#f').serialize(),
                dataType:'text',
                success: function(responseText){
                    if(responseText.trim() == 'yes'){
                        alert('탈퇴되었습니다. 이용해 주셔서 감사합니다.');
                        location.href= '/MyHome/index.member';
                    }else{
                        alert('탈퇴되지않았습니다 회원 정보를 확인해 주세요.');
                    }
                    
                },
                error: function(){alert('에러');}
                
            });
            
        }
        
    }
</script>
​
<%@ include file="../template/footer.jsp" %>
```

### - /SignOut 요청을 받을 Servlet

> ajax처리는 Servlet으로 진행합니다.
> doGet() 메소드만 작성합니다.

```java
package command.member;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.json.simple.JSONObject;

import dao.MemberDao;
import dto.MemberDto;

@WebServlet("/SignOut")
public class SignOut extends HttpServlet {
    private static final long serialVersionUID = 1L;
    public SignOut() {
        super();
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        String mNo = request.getParameter("mNo");
        
        int result = MemberDao.getInstance().delete(mNo);
        
        JSONObject responseObj = new JSONObject();
        
        if (result > 0) {
            responseObj.put("result", true);
            request.getSession().invalidate();  // 세션 초기화
        } else {
            responseObj.put("result", false);
        }
        
        response.setContentType("application/json; charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println(responseObj);
        out.close();

    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

- delete DAO

    ```java
    public int delete(String mNo) {
        SqlSession ss = factory.openSession(false);
        int result = ss.delete("mybatis.mapper.member.delete", mNo);
        if (result > 0) {
            ss.commit();
        }
        ss.close();
        return result;
    }
    ```

- delete 매퍼 (mybatis)

    ```xml
    <delete id="delete" parameterType="String">
        DELETE
        FROM MEMBER
        WHERE MNO = #{mNo}
    </delete>
    ```

> 탈퇴에 성공하게 되면 데이터베이스의 데이터는 삭제되며 아래의 스크립트가 실행됩니다.

```javascript
alert('탈퇴되었습니다. 이용해 주셔서 감사합니다.');
location.href= '/MyHome/index.member'; 
// 인덱스 페이지로 돌아가는 index.member 요청은 controller에서 처리합니다.
```