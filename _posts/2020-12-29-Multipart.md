---
title: (File, Upload)MultipartRequest
date: 2020-12-29 02:00:00 +0900
categories: [JAVA-WEB, File]
tags: [MultipartRequest, File]
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
    td, th{
        border: 1px solid rgba(255, 255, 255, 0.2);
    }
</style>

## MultipartRequest

파일 업로드 컴포넌트 중 현재 가장 인정받는 cos패키지의 MultipartRequest를 사용하여 파일 업로드 기능을 구현한다.

## MultipartRequest 개요

> uploadPage

1. cor.jar 라이브러리를 추가한다. (servlets.com)  
    1) MultipartRequest 클래스를 이용한다.  
    2) 기존의 request를 이용해서 MultipartRequest 클래스 객체를 만든다.

2. `<form method="post"  enctype="multipart/form-data">`

3. `<input type="file" name="" />`

4. 업로드 할 디렉토리(폴더)를 생성해 둔다.  
    -> WebContent 디렉토리 아래에 임의의 디렉토리를 만든다.

```javascript
<h3>파일 업로드 폼</h3>
    <form action="upload.jsp"
            method="post"
            enctype="multipart/form-data">
            
            업로더 <input type="text" name="uploader" /><br/><br/>
            첨부 <input type="file" name="filename" /><br/><br/>
            <button>올리기</button>
    </form>
```

### > POST 방식으로 보내는 이유

GET 방식으로 보낼 수 있는 데이터는 한계가 있기 때문에 (1024byte정도? )  
일정 수치 이상의 데이터를 보내게 되면 깨지게 된다. (게시물 작성 등..)  
따라서 넘기는 데이터가 많을 경우 POST를 쓰거나, 또는 로그인 폼 등의 보안상의 문제가 있는 경우에는 POST를 쓰게 된다.  

> 보통의 경우 DB에서 데이터를 불러오는 등의 작업을 하는 JSP페이지로 보낼 때는 GET방식으로 처리하고, 그 외의 경우엔 POST로 처리한다.

### > POST 방식의 getParameter()

POST 방식에서 request.getParameter()메서드를 WAS에서 알아서 처리할 수 있도록 되어있는 이유는 form에서 method가 `POST방식일 때는 디폴트값으로 enctype="application/x-www-form-urlencoded" 옵션이 설정 되어있기 때문에 이를 WAS에서 인식하고 알아서 in/output방식으로 데이터를 처리`하기 때문이다.

- 따라서, `POST방식에서는 enctype이 "application/x-www-form-urlencoded"방식이 아닌 경우, getParameter()로 데이터를 불러올 수 없다.`
- request.getHeader(“Content-Type”) 또는 request.getContentType() 를 통해서 enctype을 확인할 수 있다. 또한 이 값은 get방식의 경우는 null을 반환하게 된다. ( get방식에는 enctype이 없다. )

### > 파일전송

- form에서 파일 또는 이미지를 전송하기 위해서는 enctype이 “multipart/form-data”로 설정되어 있어야 한다. 따라서, request.getParameter()로 데이터를 불러올 수 없게 된다.

- 또한 enctype은 post방식에서만 존재하기 때문에 GET방식에서는 파일전송이 불가능하다.

## MultipartRequest 객체를 이용한 업로드

```java
<%@page import="java.text.SimpleDateFormat"%>
<%@page import="java.io.File"%>
<%@page import="com.oreilly.servlet.multipart.DefaultFileRenamePolicy"%>
<%@page import="com.oreilly.servlet.MultipartRequest"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

<%-- 업로드가 진행되는 곳 --%>
<%
    // request의 인코딩을 할 필요가 없다.
    // MultipartRequest 객체를 만들 때 인코딩을 한다.

    // 디렉토리명을 변수로 저장한다.
    String directory = "archive";
    // 디렉토리의 실제 경로를 알아낸다.
    String realPath = request.getServletContext().getRealPath(directory);

    // MultipartRequest 객체를 만든다. (이 때 업로드가 진행된다.)
    MultipartRequest multipart = new MultipartRequest(
            request,
            realPath,
            1024 * 1024 * 10,  // 업로드 크기 (10MB)
            "UTF-8",
            new DefaultFileRenamePolicy()  // 동일한 파일이 업로드되면 기존 파일명을 수정하는 방법이다.(원래 파일명에 숫자 붙이기)
            );
%>
```

### > MultipartRequest  

- MultipartRequest객체의 생성자의 매개인자로는 2개짜리부터 5개짜리까지 있다.
  - 1번째 인자로는 무조건 request객체를 받는다.
  - 2번째 인자는 세이브디렉토리의 경로.
  - 3번째 인자는 제한용량설정(int 형 (1024*1024*100 =>100mb )
  - 4번째 인자는 문자인코딩 방식
  - 5번째 인자는 리네임정책

> MultipartRequest객체의 기본 문자인코딩 방식으로는 한글파일명이 처리가 불가능하기 때문에 4번째 인코딩 방식을 “utf-8”로 설정해준다.  
> => 5번째 인자인 리네임정책은 중복파일명을 어떻게 처리할 것인지에 대해 설정하는 것

```javascript
    <h3>업로드 결과</h3>
    <ul>
        <li>경로: <%=realPath%></li>
        <li>업로더: <%=request.getParameter("uploader")%></li>
        <li>업로더: <%=multipart.getParameter("uploader")%></li>
        <li>올릴 때 파일명: <%=multipart.getOriginalFileName("filename")%></li>
        <li>저장된 파일명: <%=multipart.getFilesystemName("filename")%></li>
        
            // multipart 객체에 저장된 파일을 가져오는 메소드: getFile()
            File file = multipart.getFile("filename");
            // file을 통해서 필요한 정보를 얻어낸다.
            String filename = file.getName();	  // 파일명
            long filesize = file.length() / 1024; // file.length()는 바이트이므로 / 1024를 통해서 KB로 변환
            pageContext.setAttribute("filesize", filesize);
            String lastModifiedDate = new SimpleDateFormat("yyyy-MM-dd a h:mm").format(file.lastModified());  // 최종 수정일
        
        <li>저장된 파일명: <%=filename%></li>
        <li>파일크기: <fmt:formatNumber value="${filesize}" pattern="#,##0" />KB</li>
        <li>최종수정일: <%=lastModifiedDate%></li>
    </ul>
    <br/><br/>
    <a href="download.jsp?directory=<%=directory%>&filename=<%=filename%>">다운로드</a>
</body>
</html>
```

### > MultipartRequest 메소드

| 반환타입 |                 메소드명                 |                                              설명                                              |
| -------- | :--------------------------------------: | :--------------------------------------------------------------------------------------------: |
| String   | getFileOriginalFileName(String filename) |                                       최초 올릴때 파일명                                       |
| String   |    getFilesystemName(String filename)    |                                       실제 저장된 파일명                                       |
| String   |     getContentType(String filename)      |                    업로드 된 파일의 컨텐트(enctype) 타입을 얻어올 수 있다.                     |
| File     |         getFile(String fileName)         | 업로드 된 파일의 File객체를 얻는다.우리는 이 객체로부터 파일사이즈 등의 정보를 얻어낼 수 있다. |

<br><br>

[출처](https://gunbin91.github.io/jsp/2019/05/28/jsp_11_file.html)
*[gunbin91 Blog]*
