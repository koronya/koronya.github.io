---
layout: post
title:  "Javascript Closure 정리"
date:   2017-03-02 14:10:17 +0900
categories: javascript
tags: [javascript, 자바스크립트, closure, 클로저]
---


# Javascript 에서의 Closure

## 자바스크립트에서의 클로저 정리

클로저를 만들면 클로저 스코프 안에서 클로저를 만든 외부 스코프에 항상 접근할 수 있다.
이것이 무슨 말인지 아래의 예를 통해서 살펴보자.


``` javascript
// 예제 1
var name = 'global var';

function makeFnc() {
  var name = 'function var';
  function displayName() {
    console.log('name : ', name);
  }
  return displayName;
}

var myFnc = makeFnc();
console.log('myFnc : ', myFnc);

myFnc();
// 이 때의 closure에 name이 'function var'가 저장되어 있음
// 즉, makeFunc()의 환경이 저장되어 있음
```

위 소스에서 myFnc에 MakeFnc()를 할당했다.
`makeFnc`를 할당한 것이 아니라 `makeFnc()`를 할당한 것이 특이하다!
`makeFnc()`는 해당 함수를 실행까지 한 후에 최종적으로 displayName을 return한다.
그래서 이 때, myFnc를 console.log()로 출력해보면 아래와 같이 나온다.

```
myFnc : function displayName() {
    console.log('name : ', name);
  }
```

그런데 분명, 이 안에는 name이라는 변수가 저장되어 있지 않다.
그런데 실제로, myFnc()를 통해서 해당 함수를 실행해보면
`name : global var`가 아닌 `name : function var`가 출력된다.

이게 바로 클로저 때문이다.

chrome developer tool을 켜서 debugging을 해보자.


아래 그림은 myFnc()를 호출하기 직전의 상황이다.

![myFnc() 호출 직전 상황]({{ site.url }}/images/2017-02-25_koro_2.png)

아래 그림은 myFnc()를 호출했을 때의 상황이다.
Closure라는 곳에 보면.
name이 있는 것을 확인할 수 있다.

![myFnc()를 호출했을 때의 상황]({{ site.url }}/images/2017-02-25_koro_3.png)

**이렇게 클로저는 함수가 만들어질 때, 자신 주위의 변수 등을 저장해서 나중에 사용할 수 있게 한다.**



## myFnc의 다른 표현법

위에서 언급했던 `myFnc = makeFnc()`와 아래의 두 코드도 같은 것을 나타낸다.
단순히, 함수를 나타내는 여러 가지 방법이지만, 햇갈릴 수 있으니 여기서 한번 더 작성했다.

아래의 myFnc2는 myFnc와 별로 다를게 없으니, 그냥 살펴보고 넘어가자.

``` javascript
// 예제 2
var makeFnc2 = function () {
  var name = 'function var2';
  function displayName() {
    console.log('name : ', name);
  }
  return displayName;
};

var myFnc2 = makeFnc2();
myFnc2();
```

아래의 myFnc3는 익명함수를 만들어서 바로 실행시켜서 리턴한 결과를 할당했다.

``` javascript
// 예제 3
var myFnc3 = (function () {
  var name = 'function var3';
  function displayName() {
    console.log('name : ', name);
  }
  return displayName;
})();

myFnc3();
```

myFnc, myFnc2, myFnc3 등등의 형태 중에서 원하는 형태로 사용하면 되겠다.

## Closure 다른 예제

### Closure를 통해서 private 변수, private 함수를 접근하기

이번에는 Closure를 통해서 private변수와 private함수를 접근할 수 있게 해주는 예제를 보자.

private 변수와 private 함수는 외부에서 접근할 수 없고,
public 함수에서 private 변수와 private 함수를 사용한다.
외부에서는 이 public 함수를 이용해서 원하는 동작을 구현한다.

아래 코드에서 privateCounter는 private 변수이고
changeBy()는 private 함수이다.
increment(), decrement(), value()는 public 함수로, 외부에서 접근 가능하다.


``` javascript
// 예제 4
var counterFnc = function() {
  var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  };
};

var counter = counterFnc();
console.log(counter.value());   // 0
counter.increment();
console.log(counter.value());   // 1
counter.increment();
console.log(counter.value());   // 2

counter.decrement();
console.log(counter.value());   // 1

var counter2 = counterFnc();    // 새로운 counter 변수
console.log(counter2.value());  // 0
counter2.increment();
console.log(counter2.value());  // 1
```

counter 변수에 counterFnc()의 호출 결과를 할당하였으므로
increment(), decrement(), value()가 담긴 객체가 리턴된다.
이 함수들은 모두 counterFnc() 함수 안에서 만들어졌으며,
만들어진 환경이 같기에 모두 같은 클로저를 가지게 된다.
privateCounter와, changeBy()가 그 것이다.

counter2 변수는 새로운 counterFnc() 호출 결과를 할당하였으므로
새로운 Closure 환경이 만들어진다.
closure2가 바라보게 되는 privateCounter는 0으로 초기화가 되서,
원래의 closure가 바라보게 되는 privateCounter와는 별도로 동작하게 된다.


### Closure 생성 시 안에 담기는 변수 살펴보기 위한 다른 예제

아래 예제는 클로저에 대해서 보다 자세히 알아보고자 만든 예제이다.

``` javascript
// 예제 5
function closureTest() {
  var name = 'fnc name 1';
  var name2 = 'fnc name 2';
  var name3 = 'fnc name 3';

  function print() {
    var name4 = 'fnc name 4';
    console.log(name);
    console.log(name2);
  }

  print();
}

closureTest();
```

print()함수가 호출될 때, print() 함수는 name, name2에 접근을 하려고 하고 있다.
이 때, Closure가 만들어지게 되는 것이다.
단순히, 함수가 만들어질 때, 자신의 context에 있는 모든 변수를 Closure에 담는 것이 아니라
**함수 안에서 사용하는 변수인데, 함수 안이 아닌, 외부에 있으면, 그 변수를 Closure에 담아서 사용할 수 있게 하는 것이다.**
그래서 name, name2는 Closure에 담겨있지만, name3은 없다.

![alt text]({{ site.url }}/images/2017-02-26_koro_1.png)

위 그림은 closureTest()가 호출되었을 때, 당시의 모습이다.

closureTest()함수에서 사용하는 local 변수들과 함수들이 나와있다.
전역 객체인 Window 객체도 보인다.

![alt text]({{ site.url }}/images/2017-02-26_koro_2.png)

위 그림은 print()가 호출되었을 때의 모습이다.

print() 함수 안에서 사용하는 name4가 local로 저장되어 있고,
Closure에 name, name2가 담겨있는 것을 볼 수 있다.
또한, name3은 없는 것을 볼 수 있다.

# Closure 사용시 조심해야할 점


## loop 문에서의 클로저

loop문에서 클로저 때문에 생각보다 다르게 동작하는 경우가 많이 생긴다.
아래의 예제를 보자.

``` javascript
// 예제 6 - 오동작
function loopTest() {
  for(var i = 0; i < 5; i++) {
      setTimeout(function() {
        console.log(i);         // 여기서의 Closure가 같은 i를 참고함 -> 5
    }, 1000);
  }
}

loopTest();
```

예제 6은 단순히, loop를 5번 돌면서, 1초 후에 console.log(i)를 출력하는 함수이다.
0, 1, 2, 3, 4가 출력되기를 기대했을 테지만,
정작 출력 결과는 5, 5, 5, 5, 5 이다.

왜 그런지 살펴보자.
setTimeout()의 첫번째 인자로, 익명함수를 전달하였는데,
해당 익명함수가 생성될 때, 클로저가 생성된다.
해당 익명함수가 생성될 때의 환경을 보면
loopTest 함수 안이고
i값이 익명함수안에 없으므로, loopTest 함수의 i값을 클로저에 저장한다.
그런데 5개의 익명함수 모두, 하나의 i를 클로저에 저장한다.
그래서, 정작 loop가 다 끝나고 1초가 지나게 되면
i는 5가 되고, 다섯가지의 익명함수 모두 5를 출력하게 되는 것이다.

예제 6의 변형으로 예제 7을 보자.
예제 7은 예제 6의 익명함수를 기명함수로 바꿔준 것이다.

``` javascript
// 예제 7 - 오동작
function loopTest() {
  for(var i = 0; i < 5; i++) {
      setTimeout(show, 1000);
  }

  function show() {
    console.log(i);
  }
}
```

예제 7 역시, 예제 6 처럼
출력 결과는 5, 5, 5, 5, 5로 원하는 결과가 나오지 않는다.

이제 예제 7이 제대로 동작하게 수정해보자.


``` javascript
// 예제 7-1
function loopTest() {
  for(var i = 0; i < 5; i++) {
      setTimeout(show(i), 1000);
  }

  function show(e) {
    return function() {
      console.log(e);
    }
  }
}
```

예제 7-1은 show() 함수를 수정해서 그 자체에서 함수를 또 return하게 수정해주었다.
show함수 안에서 리턴해주고 있는 함수는
생성될 때, 역시 클로저를 생성한다.

setTimeout에 show(i)를 설정하여, i값을 전달해주는데,
show함수 안에서 리턴해주고 있는 함수는
이 i값을 클로저에 저장하게 된다.(e라는 값으로 저장)
이 저장된 값은, 각각의 loop마다 다른 값을 전달해주므로(0, 1, 2, 3, 4)
최종적으로, 4, 4, 4, 4, 4가 출력되어 원하는 결과를 볼 수 있다.


예제 7-1 말고도, 다른 식으로 해결할 수도 있다.

``` javascript
// 예제 7-2 정상 동작
function loopTest() {
  for (var i = 0; i < 5; i++) {
    (function(e){
      setTimeout(show, 1000);

      function show() {
        console.log(e);
      }
    })(i);
  }
}
loopTest();
```

위의 예제 7-2는 for문을 돌면서, 즉시 실행함수를 실행시켜주어
그 안에서 i값이 show 함수의 클로저에 저장되게 하는 것이다.
여기서 주의해야 할 점은 show()함수의 위치이다.
show()함수가 만들어지는 곳을 기준으로 클로저가 생성되므로
위치를 조심히 해야 한다.

``` javascript
// 예제 7-2x 안되는 예제
function loopTest() {
  for (var i = 0; i < 5; i++) {
    (function(e){
      setTimeout(show, 1000);
    })(i);
  }
  function show() {
    console.log(e);
  }
}
loopTest();
```

예제 7-2x는 위에서 언급한 대로 show()함수의 위치가 잘못된 경우다.
show 함수가 loopTest() 함수 밑에 만들어졌으므로, e라는 값을 클로저에 가지지 못하고
아예 동작하지 않는다.


이 예제의 경우는 setTimeout() 함수를 사용했으므로,
클로저 대신, 단순히 매개변수 전달로 문제를 해결할 수도 있다.
아래의 예제를 보자.

``` javascript
// 예제 7-3 되는 예제
function loopTest() {
  for(var i = 0; i < 5; i++) {
      setTimeout(show, 1000, i);
  }

  function show(e) {
    console.log(e);
  }
}
loopTest();
```

위의 예제 7-3을 보면
setTimeout()의 세번째 인자에 i를 전달해주고 있다.
이렇게 하면, show 함수에서 클로저가 아닌, 넘어온 값을 기준으로
정상적인 값을 출력해주게 된다.


예제 6이 오동작했으므로
예제 6이 제대로 동작하게도 수정해보자.
예제 7의 수정이랑 비슷하므로
취향에 따라 쓰면 되겠다.

``` javascript
// 예제 6-1 되는 예제 - 클로저 이용
// 클로저에서 서로 다른 i를 가짐
function loopTest() {
  for(var i = 0; i < 5; i++) {
      setTimeout(function(e) {
        return function() {
          console.log(e);
        }
    }(i), 1000);
  }
}
loopTest();
```

``` javascript
// 예제 6-2 되는 예제 - 익명함수로 wrapping 하기
// loop 돌 때마다 익명함수 생성해서 그 안에 i 전달
function loopTest() {
  for(var i = 0; i < 5; i++) {
    (function(e){
      setTimeout(function() {
        console.log(e);
      }, 1000);
    })(i);
  }
}
loopTest();
```

``` javascript
// 예제 6-3 : 되는 예제
// setTimeout에 매개변수 전달
function loopTest() {
  for(var i = 0; i < 5; i++) {
      setTimeout(function(e) {
        console.log(e);
    }, 1000, i);
  }
}
loopTest();
```

## loop 문을 돌면서 이벤트 등록 시 이슈

이번에는 loop문을 돌면서 이벤트를 등록할 때, 생길 수 있는 이슈에 대해서 살펴보자.

``` javascript
// 예제 8 : li 태그에 클릭 이벤트 걸기 - 오동작
function addEventTest() {
    var textArr = ['1st...', '2nd...', '3rd...'];
    var liNodes = document.getElementsByTagName('li');
    var liNodesLen = liNodes.length;
    for (var i = 0 ; i < liNodesLen ; i++) {
        liNodes[i].addEventListener('click', function(){
            console.log(i);             // 3, 3, 3이 출력됨
            console.log(textArr[i]);
        });
    }
}
addEventTest();
```

위의 예제 8을 보면, 문서에 있는 li 태그를 찾고,
그 수만큼 loop를 돌면서 클릭 이벤트 핸들러를 걸어주었다.
이 예제를 위해서 html에 li태그를 3개 생성해주었다.
개인적으로 익명함수보다는 기명함수가 마음에 들어서
예제 8을 변형한 예제 9를 보자.

``` javascript
// 예제 9 : li 태그에 클릭 이벤트 걸기 - 오동작
function addEventTest() {
    var textArr = ['1st...', '2nd...', '3rd...'];
    var liNodes = document.getElementsByTagName('li');
    var liNodesLen = liNodes.length;

    for (var i = 0 ; i < liNodesLen ; i++) {
        liNodes[i].addEventListener('click', show);
    }

    function show() {
        console.log(i);             // 3, 3, 3이 출력됨...
        console.log(textArr[i]);
    }
}
addEventTest();
```

예제 9에서 li 태그를 세개 돌면서 show() 함수를 클릭이벤트시에 동작하도록 걸어주었다.
예제 6, 예제 7에서와 같이 show()함수는 생성되면서
i를 클로저에 저장하는데, 그 i값을 공유하므로
막상 show() 함수에서의 i값은 0, 1, 2가 아닌 3, 3, 3이 출력된다.
textArr은 2번 index까지밖에 없으므로 마지막 `console.log(textArr[i]);`는 출력되지 않는다.

이제 예제 9를 정상적으로 수정해보자.

### ECMAScript6 이전에서의 처리

``` javascript
// 예제 9-1 : 정상적으로 동작
function addEventTest() {
    var textArr = ['1st...', '2nd...', '3rd...'];
    var liNodes = document.getElementsByTagName('li');
    var liNodesLen = liNodes.length;

    for (var i = 0 ; i < liNodesLen ; i++) {
        liNodes[i].addEventListener('click', show(i));
    }

    function show(e) {
        function showFnc() {
            console.log(e);             // 0, 1, 2가 출력
            console.log(textArr[e]);
        }

        return showFnc;
    }
}
addEventTest();
```

정상적으로 수정한 예제 9-1을 보자.
show함수 안에 showFnc()을 만들고, show함수를 수행하면, 이 showFnc()를 리턴하게 수정하였다.
또한, show()함수를 호출할 때는, i값을 전달해주었다.
showFnc()가 만들어질 때, show함수의 매개변수로 받은 e값을 클로저에 각각 저장하게 되고
최종적으로 각자 원하는 결과인 0, 1, 2와
그에 해당하는 text값이 나오게 된다.(1st... 2nd... 3rd...)

별의미 없을 수도 있지만...
예제 9-1을 조금 바꿔서 표현하면 예제 9-2가 된다.
단순히, show() 안의 return 부분을 익명함수로 바꾼 것 뿐이다.


``` javascript
// 예제 9-2 : 정상적으로 동작
function addEventTest() {
    var textArr = ['1st...', '2nd...', '3rd...'];
    var liNodes = document.getElementsByTagName('li');
    var liNodesLen = liNodes.length;

    for (var i = 0 ; i < liNodesLen ; i++) {
        liNodes[i].addEventListener('click', show(i));
    }

    function show(e) {
        return function () {
            console.log(e);
            console.log(textArr[e]);
        }
    }
}
addEventTest();
```

### ECMAScript6 에서의 let을 통한 처리
위의 예제 8은 ECMAScript6에서 var를 let으로 변경해주는 것으로 정상적인 수정을 쉽게 할 수 있다.

즉, 아래의 예제 9-3과 같이, for문안의 var를 let으로 변경해주면
원래의 의도대로 0, 1, 2가 출력되는 것을 볼 수 있다.
클릭에 대한 이벤트 리스너 함수가 익명으로 생성될 때의 scope가 for문의 scope이고
이 안에 i가 block scope를 가진 변수로 선언되어 있으므로
익명함수 안에서의 i는, 정확히 for문안의 i를 가리키게 되어
원하는 결과를 보여주게 된다.

``` javascript
// 예제 9-3 : let을 이용한 정상동작
function addEventTest() {
    var textArr = ['1st...', '2nd...', '3rd...'];
    var liNodes = document.getElementsByTagName('li');
    var liNodesLen = liNodes.length;
    for (let i = 0 ; i < liNodesLen ; i++) {
        liNodes[i].addEventListener('click', function(){
            console.log(i);             // 0, 1, 2가 출력
            console.log(textArr[i]);
        });
    }
}
addEventTest();
```

하지만, 예제 9처럼, 이벤트 리스너가 기명함수로 따로 나와있는 경우에는
for문안의 var를 let으로 수정해준다고 문제가 해결되지 않는다.

아래의 예제 9-4를 보자.
클릭에 대한 이벤트 리스너 함수인 show는 addEventTest함수의 scope에 존재한다.
show함수 안에 i가 없으므로, show함수는 생성되면서 자신 주위의 변수 등을 살펴보는데
이 때도 i가 없고, 매개변수로 전달해준 것도 없으므로 i는 undefined가 된다.
따라서, 이렇게 기명함수를 사용한 경우에는
위에 있는 예제 9-1이나 9-2처럼 해결하면 되겠다.

``` javascript
// 예제 9-4 : 예제 9에서 var -> let으로 변경 : 오동작!
function addEventTest() {
    var textArr = ['1st...', '2nd...', '3rd...'];
    var liNodes = document.getElementsByTagName('li');
    var liNodesLen = liNodes.length;
    console.log('liNodesLen : ', liNodesLen);

    for (let i = 0 ; i < liNodesLen ; i++) {
        liNodes[i].addEventListener('click', show);
    }

    function show() {
        console.log(i);             // undefined, undefined, undefined
        console.log(textArr[i]);
    }
}
addEventTest();
```


## 클로저에서 외부함수의 argument 참조

클로어제어 외부함수의 argument 객체는 직접적으로 참조할 수 없다.
매개변수 자체는 참조할 수 있지만,  외부함수의 argument 객체는 직접적으로 참조할 수 없으므로
굳이 참조해야 겠다고 한다면,
`var a = argument[0]` 과 같이 외부함수의 변수에 담아서 사용할 수는 있다.



# 성능 관련 고려 사항



## 불필요한 클로저 생성 방지(함수 내 함수 생성)

특별히 클로저가 필요하지 않은데도 불구하고, 불필요하게 클로저를 많이 만들게 되면
처리 속도, 메모리 소비 측면 모두에서 부정적인 영향을 끼칠 것이다.
즉, **함수 내에서 함수를 불필요하게 많이 만드는 것은 조심** 해야 한다.

``` javascript
// 예제 10 : PersonObject
function PersonObject(name, age) {
  this.name = name.toString();
  this.age = age;
  this.getName = function() {
    return this.name;
  };

  this.getAge = function() {
    return this.age;
  };
}
```

예를 들어서 위의 예제 10을 보자.
새로운 객체/클래스를 생성할 떄, 메서드는 일반적으로 객체 생성자에 정의되기 보다는
객체의 프로토타입에 연결되는 것이 좋다.
객체 생성자에 메서드가 정의되면, 생성자가 호출될 때마다
즉, 객체가 새로 생성될 때마다 메서드가 다시 할당되기 때문이다.

``` javascript
// 예제 10-1 : 예제 10의 보완 예제
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
}
MyObject.prototype.getName = function() {
  return this.name;
};
MyObject.prototype.getMessage = function() {
  return this.message;
};
```

그렇게 바뀐 예제가 예제 10-1 이다.
불필요한 내부함수를 제거하고, prototype에 메서드를 연결해주었다.


## 메모리 누수

javascript는 garbage collection을 이용해서 사용하지 않는 메모리를 반환한다.
그런데, 클로저를 사용하면서 아직 참조가 남아있어서 메모리가 반환되지 않고 누수 되는 경우가 생길 수 있다.
이때, 변수에 할당한 클로저를 더이상 사용하지 않으면 null로 할당해서 garbage collection에서
이 메모리를 반환할 수 있게 할 수 있다.

## 전역 scope까지의 탐색 비용 증가

클로저는 함수 객체가 생성될 때마다, 생성되므로 남용하면 메모리가 많이 사용되며
scope chain을 뒤지는 비용을 늘려줄 수도 있으니
남용하는 것은 좋지 않을 수 있다.


# 최종 정리

- Closure는 클로저 스코프 안에서 클로저를 만든 외부 스코프에 항상 접근할 수 있다.

- Closure scope에 들어가는 변수는 함수가 만들어질 때, 그 함수에서 접근하는 외부 변수이다.

# Reference


[^1][^2][^3][^4]

[^1]: [MDN - 클로저 설명](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)
[^2]: [JavaScript Garden - 클로저 설명](http://bonsaiden.github.io/JavaScript-Garden/ko/#function.closures)
[^3]: <http://mollangk.tistory.com/10>
[^4]: <http://blog.javarouka.me/2012/01/closure.html>
