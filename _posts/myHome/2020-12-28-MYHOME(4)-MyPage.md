---
title: (MyBatis, MVC) -4- login기능구현
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

</form>
​
```

![MyPage](/assets/img/study/MyPage.png) *MyPage*

- 비밀번호 변경 기능

    > 세션에 저장된 회원정보: ${loginDto}
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