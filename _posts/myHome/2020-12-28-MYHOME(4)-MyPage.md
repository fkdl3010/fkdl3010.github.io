---
title: (MyBatis, MVC) -4- MyPage기능구현
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
    img {
        height: 30em;
    }

    h2{
        color: coral;
    }
    h3{
        color: #ffccbc;
    }
</style>

## MyPage 기능구현

> MVC패턴으로 맞춰서 진행  
> index.jsp에서부터 시작합니다.  
> 기능구현 및 페이지 이동의 경로는 .member의 suffix값을 가집니다.  
> 세션에 로그인 정보를 가진 채 시작합니다.

<br>

_목차_

- [MyPage기능](#mypage기능)
  - [- index의 마이페이지 이동 버튼](#--index의-마이페이지-이동-버튼)
  - [- /myPage.member 경로 Controller](#--mypagemember-경로-controller)
  - [- myPage.jsp로 이동](#--mypagejsp로-이동)

## MyPage기능

### - index의 마이페이지 이동 버튼

> 실제로는 `header.jsp`에 위치하고있다.

```html
<input type="button" value="마이페이지" onclick="location.href='/MyHome/myPage.member'"/>
```

### - /myPage.member 경로 Controller

> myPage.member의 요청 처리 (단순 이동)

```java
case "/myPage.member":
    pathNRedirect = new PathNRedirect();
    pathNRedirect.setPath("member/myPage.jsp");
    pathNRedirect.setRedirect(false);
    break;
```

### - myPage.jsp로 이동

> 로그인 시 세션에 저장해둔 회원의 정보를 사용합니다.
>> document.referrer: 링크를 통해 이동했을때 어떤 페이지의 링크를 통해 왔는지 레퍼러가 기록되는 공간이다. 따라서 직접 특정 페이지로 들어갈 경우 이 referrer가 존재하지 않게 되므로 그게 첫 페이지임을 알 수 있다.

```javascript
<form id="f">
    아이디<br>
    ${loginDto.mId }<br><br> <!--MemberLoginCommand에서 session에 저장하였다. -->

    현재 비밀번호<br>
    <input type="password" name="mPw0" id="mPw0"/><br><br>

    비밀번호 변경<br>
    <input type="password" name="mPw" id="mPw"/><br><br>

    비밀번호 확인<br>
    <input type="password" name="mPw2" id="mPw2"/>
    <input type="button" value="비밀번호 변경" id="updatePwBtn"/><br>
    <span id="idResult"></span><br>

    성명<br>
    <input type="text" name="mName" id="mName" value="${loginDto.mName }"  /><br><br>

    이메일<br>
    <input type="text" name="mEmail" id="mEmail" value="${loginDto.mEmail }" /><br><br>

    전화번호<br>
    <input type="text" name="mPhone" id="mPhone" value="${loginDto.mPhone }" /><br><br>

    주소<br>
    <input type="text" name="mAddress" id="mAddress" value="${loginDto.mAddress }" /><br><br>

    가입일<br>
    ${loginDto.mRegdate }<br><br>

    <!-- hidden -->
    <input type="hidden" name="mNo" value="${loginDto.mNo }" />
    <input type="hidden" name="mId" value="${loginDto.mId }" />
    <input type="hidden" name="mRegdate" value="${loginDto.mRegdate }" />

    <input type="button" value="정보 수정하기" id="updateBtn" />
    <input type="button" value="되돌아가기" onclick="location.href=document.referrer" />
    <!-- document.referrer: 링크를 통해 이동했을때 어떤 페이지의 링크를 통해 왔는지 레퍼러가 기록되는 공간이다. 따라서 직접 특정 페이지로 들어갈 경우 이 referrer가 존재하지 않게 되므로 그게 첫 페이지임을 알 수 있다. -->
</form>
​
```

![MyPage](/assets/img/study/MyPage.png) *MyPage*

- 비밀번호 변경 기능

    > 세션에 저장된 회원정보: ${loginDto}
    > ajax로 처리합니다.
    > ajax처리를 위한 커맨드는 따로 작성합니다.(MVC x)

    ```javascript
    $(function(){
        $('#updatePwBtn').click(fn_updatePw);
        $('#updateBtn').click(fn_update);
        
    });

    function fn_updatePw(){
        if('${loginDto.mPw}' != $('#mPw0').val()){
            alert('비밀번호를 확인하세요.');
            return;
        }
        if('${loginDto.mPw}' == $('#mPw').val()){
            alert('같은 비밀번호로 변경할 수 없습니다.');
            return;
        }
        if($('#mPw').val() == ''){
            alert('비밀번호를 입력하세요.');
            $('#mPw').focus();
            return;
        }else if($('#mPw').val() != $('#mPw2').val()){
            alert('비밀번호를 확인하세요.');
            return
        }
        
        $.ajax({
            url: '/MyHome/MemberChangePw',
            type: 'post',
            data: $('#f').serialize(),
            dataType:'text',
            success: function(responseText){
                if(responseText == 'no'){
                    alert('비밀번호가 변경되지 않았습니다.');
                    $('#idResult').text('비밀번호가 변경되지 않았습니다.').css('color', 'red');
                    
                }else{
                    alert('비밀번호가 변경되었습니다.');
                    $('#idResult').text('비밀번호가 변경되었습니다.').css('color', 'green');
                    $('#mPw0').val('');
                    $('#mPw').val('');
                    $('#mPw2').val('');
                    location.reload();
                    //location.href = '/MyHome/myPage.member';
                }
                
            },
            error: function(){alert('에러');}
            
        });
        
    }
    ```

  - MemberChangePw

    ```java
    @WebServlet("/MemberChangePw")
    public class MemberChangePw extends HttpServlet {
        private static final long serialVersionUID = 1L;
        public MemberChangePw() {
            super();
        }
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            
            request.setCharacterEncoding("UTF-8");
            String mPw = request.getParameter("mPw");
            String mNo = request.getParameter("mNo");
            
            MemberDto memberDto = new MemberDto();
            memberDto.setmPw(mPw);
            memberDto.setmNo(Integer.parseInt(mNo));
            
            int result = MemberDao.getInstance().updatemPw(memberDto);
            
            String responseText = null;
            if (result > 0) {
                HttpSession session = request.getSession();
                if (session.getAttribute("loginDto") != null) {
                    /* 1. loginDto를 수정해서 다시 session에 올리는 방법 */
                    MemberDto loginDto = (MemberDto)session.getAttribute("loginDto");
                    loginDto.setmPw(mPw); // session의 loginDto는 참조 값이므로 적용이됨.

                    /* 2. 변경된 정보를 DB에서 다시 가져와서 session에 올리는 방법 */
                    /*
                    session.removeAttribute("loginDto");
                    MemberDto loginDto = MemberDao.getInstance().selectBymNo(mNo);
                    session.setAttribute("loginDto", loginDto);
                    */
                }
                responseText = "yes";
            } else {
                responseText = "no";
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

  - updatemPw    DAO와 mapper

    > MemberDAO

    ```java
    public int updatemPw(MemberDto memberDto) {
        SqlSession ss = factory.openSession(false);
        int result = ss.update("mybatis.mapper.member.updatemPw", memberDto);
        if (result > 0) {
            ss.commit();
        }
        ss.close();
        return result;
    }
    ```

    > mapper

    ```xml
    <update id="updatemPw" parameterType="dto.MemberDto">
        UPDATE MEMBER
            SET MPW = #{mPw}
            WHERE MNO = #{mNo}
    </update>
    ```

- 정보 수정 기능

    > 세션에 저장된 회원정보: ${loginDto}
    > ajax로 처리합니다.
    > ajax처리를 위한 커맨드는 따로 작성합니다.(MVC x)

    ```javascript
    function fn_update() {
        if ('${loginDto.mName}' == $('#mName').val() &&
            '${loginDto.mEmail}' == $('#mEmail').val() &&
            '${loginDto.mPhone}' == $('#mPhone').val() &&
            '${loginDto.mAddress}' == $('#mAddress').val()) {
            alert('변경할 회원 정보가 없습니다.');
            return;
        }
        if ($('#mEmail').val() == '') {
            alert('이메일은 필수입니다.');
            return;
        }
        $.ajax({
            url: '/MyHome/MemberUpdate',
            type: 'post',
            data: $('#f').serialize(),
            dataType: 'json',
            success: function(responseObj) {
                if (responseObj.result) {
                    alert('회원 정보가 수정되었습니다.');
                    location.href = '/MyHome/myPage.member';
                } else {
                    alert('회원 정보가 수정되지 않았습니다.');
                }
            },
            error: function(){ alert('실패'); }
        });
    }
    ```

  - MemberUpdate

    ```java
    @WebServlet("/MemberUpdate")
    public class MemberUpdate extends HttpServlet {
    private static final long serialVersionUID = 1L;
    public MemberUpdate() {
        super();
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        String mNo = request.getParameter("mNo");
        String mName = request.getParameter("mName");
        String mEmail = request.getParameter("mEmail");
        String mPhone = request.getParameter("mPhone");
        String mAddress = request.getParameter("mAddress");
        
        MemberDto memberDto = new MemberDto();
        memberDto.setmNo(Integer.parseInt(mNo));
        memberDto.setmName(mName);
        memberDto.setmEmail(mEmail);
        memberDto.setmPhone(mPhone);
        memberDto.setmAddress(mAddress);
                
        int result = MemberDao.getInstance().update(memberDto);
        
        // 결과 JSON
        // {"result": true}
        // {"result": false}
        JSONObject responseObj = new JSONObject();
        if (result > 0) {
            // session에 올라간 loginDto를 제거하고 새 loginDto를 올린다.
            HttpSession session = request.getSession();
            if (session.getAttribute("loginDto") != null) {
                session.removeAttribute("loginDto");
                MemberDto loginDto = MemberDao.getInstance().selectBymEmail(mEmail);
                // 이메일로 정보를 가져오는 selectByEmail 메소드 mNo를 이용해도 상관없다. 둘다 unique임
                session.setAttribute("loginDto", loginDto);
            }
            responseObj.put("result", true);
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

  - update의 DAO와 mapper

    > MemberDAO

    ```java
    public int update(MemberDto memberDto) {
        SqlSession ss = factory.openSession(false);
        int result = ss.update("mybatis.mapper.member.update", memberDto);
        if (result > 0) {
            ss.commit();
        }
        ss.close();
        return result;
    }
    ```

    > mapper

    ```xml
    <update id="update" parameterType="dto.MemberDto">
        UPDATE MEMBER
            SET MNAME = #{mName},
                MEMAIL = #{mEmail},
                MPHONE = #{mPhone},
                MADDRESS = #{mAddress}
            WHERE MNO = #{mNo}
    </update>
    ```

> 정보 업데이트 뒤 이동은 `'/MyHome/myPage.member'` 입니다.