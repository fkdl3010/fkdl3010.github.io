---
title: (jQuery)ForEach
date: 2020-12-23 22:00:00 +0900
categories: [WEB, JQUERY]
tags: [JQUERY, JAVASCRIPT]
---

## each()메소드

```javascript
    // jQuery 유틸리티 메서드
    $.each(object, function(index, item){ }); 
     // jQuery 일반 메서드
    $(selector).each(function(index, item){ })

```

### $.each()

$.each() 메서드는 object 와 배열 모두에서 사용할 수 있는 일반적인 반복 함수입니다.
다시 말해, 배열과 length 속성을 갖는 배열과 유사 배열 객체들을 index를 기준으로 반복할 수 있습니다.
첫 번째 매개변수로 배열이나 객체를 받습니다.
그리고 두번째 매개변수로 콜백함수를 받으며 콜백함수의 인자로는 인덱스와 값을 인자로 갖습니다.
다음의 예제를 통해 살펴보도록 합니다.

> 배열

```javascript
// 객체을 선언 
var arr= [ 
    {title : '다음', url : 'http://daum.net'}, 
    {title : '네이버', url : 'http://naver.com'} 
];
// $.each() 메서드의 첫번째 매겨변수로 위에서 선언한 배열은 전달
$.each(arr, function (index, item) {
    // 두 번째 매개변수로는 콜백함수인데 콜백함수의 매개변수 중 
    // 첫 번째 index는 배열의 인덱스 또는 객체의 키를 의미하고 
    // 두 번째 매개 변수 item은 해당 인덱스나 키가 가진 값을 의미합니다. 
    var result = '';
    result += index +' : ' + item.title + ', ' + item.url;
    console.log(result); 
    // 0 : 다음, http://daum.net 
    // 1 : 네이버, http://naver.com 
});



```

> 객체

```javascript
// 객체를 선언
 var obj = {
    daum: 'http://daum.net', 
    naver: 'http://naver.com' 
};


  // $.each() 메서드의 첫번째 매겨변수로 위에서 선언한 객체를 전달 
$.each(obj, function (index, item) { 
    // 객체를 전달받으면 index는 객체의 key(property)를 가리키고 
    // item은 키의 값을 가져옵니다. 
    var result = ''; 

    result += index + ' : ' + item; 

    console.log(result); 

    // daum : http://daum.net 
    // naver : http://naver.com 
})

```

### $().each()

> 예제

```javascript
<ul class="list"> 
    <li>Lorem ipsum dolor sit amet.</li> 
    <li>Lorem ior sit amet.</li> 
    <li>Lorem ipsum </li> 
</ul>

```

> $().each() 사용

```javascript
$('.list li').each(function (index, item) { 

    // 인덱스는 말 그대로 인덱스 
    // item 은 해당 선택자인 객체를 나타냅니다. 

    $(item).addClass('li_0' + index); 

    // item 과 this는 같아서 일반적으로 this를 많이 사용합니다. 
    // $(this).addClass('li_0' + index); });
    
위 코드를 실행하면 li 의 클래스에 li_00, li_01, li_02 가 추가되어 있을 것입니다.

제이쿼리에 선택자를 넘기면 해당 선택자를 자바스크립트의 반복문과 같이 사용된다고 보면 됩니다.

```

__정리__
each문에서 배열은 index의 값으로 배열의 인덱스를 의미하고 item은 해당 인덱스의 값을 의미  
each문에서 객체는 index의 값으로 객체의 키를 의미하고 item은 해당 키가 가진 값을 의미

출처: [Webclub](https://webclub.tistory.com/455)
