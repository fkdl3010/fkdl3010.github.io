---
title: (게시판) -1- 게시판 구현
date: 2020-12-30 22:00:00 +0900
categories: [JAVA-WEB, MyBatis]
tags: [MyBatis, MVC, AJAX]
---

## 게시판 만들기

### DB

```sql
drop table gil;
drop sequence gil_seq;

create table gil(
    gil_no number primary key,
    gil_writer varchar2(100) not null,
    gil_title varchar2(1000) not null,
    gil_content varchar2(4000),
    gil_pw varchar2(20) not null,
    gil_hit number,
    gil_ip varchar2(20),
    gil_date date
);

create sequence gil_seq
start with 1
increment by 1
maxvalue 999999
nocycle
nocache;

insert into gil values (gil_seq.nextval, '가길동', '질문입니다1.', '왜이리시끄러운것이냐1', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '나길동', '질문입니다2.', '왜이리시끄러운것이냐2', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '다길동', '질문입니다3.', '왜이리시끄러운것이냐3', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '라길동', '질문입니다4.', '왜이리시끄러운것이냐4', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '마길동', '질문입니다5.', '왜이리시끄러운것이냐5', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '바길동', '질문입니다6.', '왜이리시끄러운것이냐6', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '사길동', '질문입니다7.', '왜이리시끄러운것이냐7', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '아길동', '질문입니다8.', '왜이리시끄러운것이냐8', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '자길동', '질문입니다9.', '왜이리시끄러운것이냐9', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '차길동', '질문입니다10.', '왜이리시끄러운것이냐10', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '카길동', '질문입니다11.', '왜이리시끄러운것이냐11', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '타길동', '질문입니다12.', '왜이리시끄러운것이냐12', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '파길동', '질문입니다13.', '왜이리시끄러운것이냐13', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '하길동', '질문입니다14.', '왜이리시끄러운것이냐14', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '홍길동', '질문입니다15.', '왜이리시끄러운것이냐15', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '김길동', '질문입니다16.', '왜이리시끄러운것이냐16', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '나길동', '질문입니다17.', '왜이리시끄러운것이냐17', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '박길동', '질문입니다18.', '왜이리시끄러운것이냐18', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '이길동', '질문입니다19.', '왜이리시끄러운것이냐19', '1111', 0, '127.0.0.1', sysdate);
insert into gil values (gil_seq.nextval, '최길동', '질문입니다20.', '왜이리시끄러운것이냐20', '1111', 0, '127.0.0.1', sysdate);
```

---

### DAO

```java
package dao;

import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;

import common.PageVo;
import dto.GilDto;
import gil.config.DBService;

public class GilDao {

    private SqlSessionFactory factory;

    private GilDao() {
        factory = DBService.getInstance().getFactory();
    }
    private static GilDao gilDao = new GilDao();
    public static GilDao getInstance() {
        
        return gilDao;
    }

    // 메소드

    // 한 페이지의 게시물만 가져오는 메소드
    public List<GilDto> gilBBSList(PageVo pageVo){
        SqlSession ss = factory.openSession();
        List<GilDto> list = ss.selectList("gil.mapper.gil.selectList", pageVo);
        
        ss.close();
        
        return list;
    }
```

---

###

> 게시판으로 이동하기


```javascript
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>

<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
    <a href="/WebExer/BBSPage.web">게시판으로 이동</a>
</body>
</html>
```
