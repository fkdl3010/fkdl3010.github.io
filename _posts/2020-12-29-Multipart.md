---
title: (File)MultipartRequest
date: 2020-12-29 02:00:00 +0900
categories: [JAVA-WEB, File]
tags: [MultipartRequest, File]
---

## MultipartRequest

파일 업로드 컴포넌트 중 현재 가장 인정받는 cos패키지의 MultipartRequest를 사용하여 파일 업로드 기능을 구현한다.

## 파일 업로드

1. cor.jar 라이브러리를 추가한다. (servlets.com)  
    1) MultipartRequest 클래스를 이용한다.
    2) 기존의 request를 이용해서 MultipartRequest 클래스 객체를 만든다.

2. `<form method="post"
            enctype="multipart/form-data">`

3. `<input type="file" name="" />`

4. 업로드 할 디렉토리(폴더)를 생성해 둔다.  
    -> WebContent 디렉토리 아래에 임의의 디렉토리를 만든다.