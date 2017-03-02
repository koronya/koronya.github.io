---
layout: post
comments: true
title:  "각종 markdown 문법"
date:   2017-02-27 19:34:17 +0900
categories: jekyll update
---


* 목차
{:toc}



## 블럭 인용 관련

------------------------------------------------

----

아래는 블럭 인용  (  " > " 를 이용)
> 블럭 인용 되어서 오른쪽으로 들여쓰기 된 텍스트
> 블럭 인용 되어서 오른쪽으로 들여쓰기 된 텍스트 2

블럭안에 블럭도 된다! ( " > " 를 계속 이용)
> ㄴㅁㅇㄹㅁㄴㄹㅁ
> ㅁㄴㅇㄹㅁㄴㅇㄹㅁㄴㄹ
> >ㅁㅇㄹㅁㄴㅇㄹㅁㄴㅁㅇ
> >adfasdf
> >>adfadsfadsf
> >>adfasf
>

## 코드 block 관련

------------------------------------------------

### 한줄 혹은 일부 영역 code block

한줄 코드는 역따옴표로 감싸서 삽입 ( ` )

`code insert`

라인 도중에 `console.log();` 코드를 넣을 수도 있다.

### 여러 줄 code block

#### 방법 1

4개 이상의 공백이나 탭으로 code block 생성

    code block
    asdfdsjfadsf


#### 방법2

` 를 세개 써서 감싸주기

```
여러 줄의 코드 블럭
입니다.
```



#### 방법2에서 뒤에 언어 이름 적으면, 해당 언어 syntax highlighting 된다.

\``` javascript 이렇게 하고 코드 블럭을 감싸주면 된다.

``` javascript
var name = 'korea';

function hello() {
    console.log('name : ', name);
}
```

#### 방법 3 : github에서 제공하는 Rouge?

아래와 같이 할 수도 있다.

linenos를 넣으면 line number도 출력된다.

\{\% highlight javascript linenos \%\}  
code~~~  
\{\% endhighlight \%\}

{% highlight javascript linenos %}
var name = 'korea';

function hello() {
    console.log('name : ', name);
}
{% endhighlight %}

## escape 문자

------------------------------------------------

마크다운 문법이 아닌 그냥 문자를 쓰고 싶을 때, \로 escape!


## 수평선 넣는 방법

------------------------------------------------

\-\-\-\- 4개 이상 써서 수평선  
여기 아래에 수평선 삽입!

----

수평선을 넣으려면, 넣으려는 줄 위에가 비어있어야 한다!



## 하나의 문단안에서 한줄 띄어쓰기(softBreak?)

------------------------------------------------

해당 줄의 끝에서 공백을 두칸 입력하면  
띄어쓰기가 된다.


## 기본 서식들

### italic

------------------------------------------------

italic은 \*로 감[^koro]싸거나, \_로 감싸기


*single asterisks*  
_single underscore_

### bold

------------------------------------------------

bold는 \*\*로 감싸거나, \_\_로 감싸기

**double asterisks**  
__double underscore__



### 그림 삽입

------------------------------------------------


그림을 넣기 위해서는...

`![Alt text](/path~~~/.jpg)`

![alt text]({{ site.url }}/images/2017-02-25_koro_1.png)


## 하이퍼링크 삽입

------------------------------------------------

### 방법1

\[link 이름\]\(링크주소\)

ex > \[github 사이트\]\(http://github.io\)

[github 사이트](http://github.io)


### 방법2

하이퍼링크는 아래와 같이 하면 알아서 만들어준다.

\<http://github.io\>

<http://github.io>

### 방법3 - custom 변수 사용

`[naver_url]: http://www.naver.com`

위와 같이 하면 naver_url을 변수처럼 불러올 수 있다.

ex> `[네이버 연결][naver_url]`

결과> [네이버 연결][naver_url]



[naver_url]: http://www.naver.com



## 리스트 삽입

------------------------------------------------

1. list


2. asdfadf

    1. asdfadsf


- sdafsadfj
- sdafasdf

3. dfasdf

## 각주 삽입

------------------------------------------------

각주를 삽입하기 위해서는 삽입하고자 하는 곳에 `[^1]`을 입력하고

문서의 마지막에 `[^1]: 내용`을 써주면 연결된다.

숫자가 아니라 문자도 되는듯. `[^koro]` 처럼...

ex> 네이버[^1]와 다음[^2]은 어쩌구저쩌구...


## etc tips

### 뭔가를 쓰고 아래에 \-\-\- 입력하면 제목 처럼 됨.

제목
---



[^1]: [naver 사이트](http://www.naver.com) http://www.naver.com 네이버 입니다.
[^2]: http://www.daum.net 다음 입니다.


{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://koroghost.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>

{% endif %}
