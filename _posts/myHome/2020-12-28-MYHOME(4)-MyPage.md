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

### - index의 