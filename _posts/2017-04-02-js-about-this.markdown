---
layout: post
title:  "Javascript에서의 this정리"
date:   2017-04-02 10:20:17 +0900
categories: jekyll update
---


* 목차
{:toc}


## Javascript에서의 this
Javascript에서 **this**는 참 햇갈리는 녀석이다. 다른 프로그래밍 언어의 this와는 개념이 달라서 당황할 때가 생각보다 많은데
이번 post에서는 이런 this에 대해서 정리해보고자 한다.

## 기본적인 this의 5가지 해석
this는 함수를 어떻게 호출하는지에 따라서 다르게 결정된다.

### Global Scope
아래의 예제 처럼 Global Scope에서 사용되는 this는 Global 객체를 가리킨다.

``` javascript
// 예제 1 - Global Scope에서의 this
console.log(this);      // Global(브라우저에서 Window 객체)
```

### 함수 호출 패턴 - 함수를 호출할 때
아래의 예제 2를 보자. foo()를 호출하면, 그 때의 this도 Global 객체를 가리킨다.
``` javascript
// 예제 2 - 함수를 호출할 때의 this
var foo = function () {
    console.log(this);  // Global(브라우저에서 Window 객체)
};

foo();
```

### 메소드 호출 패턴 - 메소드로 호출할 때

어떤 객체가 자신의 메소드를 호출하게 되면, 그 때의 this는 해당 객체가 된다.

아래의 예제 3을 보자.
person객체가 person객체의 method인 hello()를 호출했으므로 이 때의 this는 person이 된다.
단순히 hello라는 함수가 person 객체의 method이니까 this가 person객체가 되는 것이 아니다!
**누가 호출**하는지에 따라서, this가 바뀌는 것이다.

hello2 변수를 보자. hello2는 person.hello를 할당했으므로, 그 자체적으로 함수이다. 이를 실행할 때의 this는 Global 객체를 가리킨다.

다음은 tempObj를 보자. tempObj의 hello method에 person.hello를 할당했다. 그리고, tempObj.hello()를 호출하면, 호출하는 주체가 tempObj이므로 this는 tempObj 객체가 되고, 따라서 이 때, this.name은 Tom이 아니라 Temp가 된다.

``` javascript
// 예제 3 - 메소드로 호출되는 경우의 this
var person = {
    name : 'Tom',
    hello : function () {
        console.log(this);
        console.log(this.name);
    }
};

person.hello();     // this는 person 객체,  this.name은 Tom

var hello2 = person.hello;
hello2();           // this는 Global, this.name은 undefined

var tempObj = {
    name : 'Temp',
    hello : person.hello
};
tempObj.hello();    // this는 tempObj 객체,    this.name은 Temp

```

### 생성자 호출 패턴 - 생성자를 호출할 때
new 키워드로 생성자를 실행시키는 경우에, 이 생성자 안에서의 this는 새로 만들어진 객체를 가리킨다.
아래의 예제를 보자. Person은 생성자 함수이다.
tom을 new를 이용해서 생성자를 호출하면, 생성자 안에서의 this는 tom 객체를 가리킨다.(새로 만들어진 객체)

``` javascript
// 예제 4 - 생성자를 호출할 때의 this
var Person = function (str) {
    this.name = str;
    console.log(this);          // this는 tom 객체
}

Person.prototype.getName = function () {
    return this.name;
}

var tom = new Person("Tom");
console.log(tom.getName());     // Tom
```


### apply 호출 패턴 - this가 가리키는 객체 정해준 경우(call, apply, ...)
apply 메소드는 함수를 호출할 때, 사용할 인수들의 배열을 받아들인다. 또한, this의 값을 선택할 수 있게 해준다.
첫번째 인자가 this에 binding될 값이고, 두번째 인자는 매개변수로 전달할 배열이다.
call 메소드는 apply와 같지만, 전달해줄 매개변수가 하나의 배열이 아니라 여러개로 늘어뜨려서 보내준다.

아래의 예제를 보면, add함수를 부르는데 call이나 apply를 통해서 부르게 되면,
그 첫번째 인자인 temp를 this로 binding한다. 그 뒤의 매개변수는 함수 내에서 사용할 매개변수들이다.

``` javascript
// 예제 5 - apply 호출 패턴에서의 this
var add = function (c, d) {
    return this.num + c + d;
}

var temp = {
    num : 3
};

add.call(temp, 4, 5);       // 3 + 4 + 5 = 12
add.apply(temp, [4, 5]);    // 3 + 4 + 5 = 12
```


## this를 고정 - innerFunc의 경우 자주 사용
아래의 예제 6을 보자.
person의 sayAge method안에 innerSay함수를 이중으로 불러주고 있다.
person.sayAge()를 하면, person의 this는 person 객체가 되고, 이 때의 age는 25가 맞는데
innerSay()를 부르게 되면, 이 때의 this는 person 객체가 아니라 전역 객체가 되어서, age는 30이 된다.
이 때, innerSay에서의 this를 person 객체로 고정하고 싶을 때의 두가지 방법을 살펴보겠다.

``` javascript
// 예제 6 - innerFunc에서의 this
var age = 30;
var person = {
    age : 25,
    sayAge : function () {
        console.log('this.age : ', this.age);           // 25

        var innerSay = function () {
            console.log('inner this.age : ', this.age); // 30
        };

        innerSay();
    }
}

person.sayAge();
```

첫번째 방법은 아래와 같다.
sayAge안에서 변수 self에 this를 담아두고, 내부 함수에서 이 변수를 사용하는 것이다.


``` javascript
// 예제 6-1 - innerFunc에서의 this
var age = 30;
var person = {
    age : 25,
    sayAge : function () {
        var self = this;
        console.log('this.age : ', this.age);       // 25

        var innerSay = function () {
            console.log('this.age : ', this.age);   // 30
            console.log('self.age : ', self.age);   // 25
        }

        innerSay();
    }
}

person.sayAge();
```

두번째 방법은 Function.prototype.bind()를 이용하는 것이다.
`f.bind(obj)` 이렇게 하면 f 함수의 this를 obj로 binding 시켜준다.


``` javascript
// 예제 6-2 - innerFunc에서의 this
var age = 30;
var person = {
    age : 25,
    sayAge : function () {
        console.log('this.age : ', this.age);       // 25

        var innerSay = function () {
            console.log('this.age : ', this.age);   // 25
        }.bind(this);

        innerSay();
    }
}

person.sayAge();
```

물론 위와 같이 self를 할당하거나, this를 binding시켜주더라도 애초에 이 함수를 부를 때 `person.sayAge()`로 부르는 것이 아니라
``` javascript
var say = person.sayAge;
say();
```
이렇게 부르면...this는 그냥 Global 객체이니 유의하자.


## 최종 정리
- javascript에서의 this는 사용하는 패턴에 따라, 다양하게 binding된다.
    - global, 함수, 메소드, 생성자, apply
- 자신이 원하는 객체값을 사용하고자 할 때, .bind나 var self=this를 사용할 수 있다.



## Reference

- [더글라스 크락포드의 자바스크립트 핵심 가이드](http://book.naver.com/bookdb/book_detail.nhn?bid=4774270)
- <http://bonsaiden.github.io/JavaScript-Garden/ko/#function.this>
- <https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this>
