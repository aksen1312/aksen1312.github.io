---
layout : post
title : "함수 Function"
published : true
category : javascript
tags : [function]
permalink : /javascript/function
---

자바스크립트에서 정의된 함수는 다음과 같이 3가지 역할을 한다.

#### 메서드 아닌 함수(일반적인 함수)
직접 호출할 수 있고 일반적인 함수로 동작한다.
일반적인 함수는 소문자로 시작하는 것이 관례이다.

```
id('hello');
```

#### 생성자
함수를 `new` 연산자로 호출하면, 객체 팩토리인 생성자가 된다.
생성자 이름은 대문자로 시작하는 것이 관례이다.

```
new Date();
```

#### 메서드
함수를 객체 프로퍼티에 저장할 수 있다. 이런 함수를 메서드라 부르며, 객체를 통해 호출한다.
메서드 이름은 소문자로 시작하는 것이 관례이다.

```
obj.method();
```

###### 기타. 'Parameter' vs 'Argument'

* Parameter는 함수를 정의할때 사용한다.(formal parameter, formal argument라고도 부른다)

```
function say(param1, param2){ ... }
```

* Argument는 함수를 호출할때 사용한다.(actual parameter, actual argument라고도 부른다)

```
say('hello', 'world');
```

.....


### 클로저

클로저는 만들어진 스코프(상위 스코프)와 연결된 채 존재하는 함수를 말한다. ('변수가 외부에서 왔다'라고도 말한다)

함수를 호출할 때마다 매개변수와 변수를 저장할 새 환경(저장소)을 만든다. 그리고 함수 실행을 마치면 보통 해당 공간을 회수한다.(재귀적 호출이 n번 일어나는 경우, n개의 환경이 만들어진다.)

함수는 선언될 때 자신이 생성된 스코프(상위 스코프)를 내부 프로퍼티[Scope]에 기록한다. 그리고 함수 호출시 생성되는 환경에 [Scope]를 통해서 outer라는 필드로 상위 스코프의 환경을 가리킨다.

createInc가 반환하는 함수는 startValue와 계속 연결되어 함수를 언제 호출하든 상태가 사라지지 않고 보존된다.

```
function createInc(startValue){
  return function(step){
    startValue += step;
    return startValue;
  };
}

> var inc = createInc(5);
> inc(1);
6
> inc(2);
8
```

##### inc가 클로저가 되는 순서는 다음과 같다.

1. 함수 호출 `createInc(5);`를 실행하는 동안 createInc를 위한 새로운 환경이 만들어 지고, outer는 전역 환경(createInc의 상위 스코프 환경)을 가리킨다. 해당 환경에는 매개변수 startValue가 존재한다.
2. 변수 inc에 함수 호출을 할당한 후 createInc를 위한 환경을 회수해야 하지만, inc의 [Scope]가 createInc의 환경을 가리키고 있기 때문에 회수하지 않는다.
3. `inc(1);`를 실행하는 동안 inc를 위한 새로운 환경이 만들어지고, outer는 createInc의 환경(inc의 상위 스코프 환경)을 가리킨다. outer를 통하여 startValue(createInc의 환경)에 접근한다.


### 함수와 메서드의 묵시적 매개변수 this

함수를 호출하면 항상 this가 (묵시적) 매개변수로 넘어간다.

- 일반적인 함수 : 일반적인 함수에서는 this를 쓸일이 없지만, 여전히 존재한다. 
  - 엉성한 모드 : 전역객체(window)
  - 엄격한 모드 : undefined
- 메서드 : this는 메서드를 호출한 객체이다. 메서드 호출의 수신자(receiver)라고도 부른다.

함수도 객체이므로 각 함수에는 기본적으로 상속되는 메서드들이 있다. 그러한 메서드들로 this를 설정하여 함수를 호출 할 수 있다. 

다음과 같은 객체 있다.
```javascript
var jane = {
  name : 'Jane',
  sayHello : function(otherName){
    'use strict';
    console.log(this.name + ' say hello to ' + otherName);
  }
};
```

#### Function.prototype.call(thisValue, arg1?, arg2?, ...)

첫 번째 매개변수는 호출한 함수 내부에서 this가 갖는 값이며, 나머지 매개변수는 호출한 함수에 전달된다.
다음 3가지 호출방법은 모두 동등하다. 

```javascript
jane.sayHello('Tarzan');              // 1

jane.sayHello.call(jane, 'Tarzan');   // 2

var func = jane.sayHello;             // 3
func.call(jane, 'Tarzan');
```

2번의 호출은 jane을 반복하는데, 왜냐하면 `call()`은 자신을 호출한 함수의 콘텍스트를 모르기 때문이다.

#### Function.prototype.apply(thisValue, argArray)

첫 번째 매개변수는 호출한 함수 내부에서 this가 갖는 값이며, 두 번째 매개변수는 호출한 함수에 전달될 배열이다.
다음 3가지 호출방법은 모두 동등하다. 

```javascript
jane.sayHello('Tarzan');              // 1

jane.sayHello.apply(jane, ['Tarzan']);   // 2

var func = jane.sayHello;             // 3
func.call(jane, ['Tarzan']);
```

2번의 호출은 jane을 반복하는데, 왜냐하면 `apply()`은 자신을 호출한 함수의 콘텍스트를 모르기 때문이다.

#### Function.prototype.bind(thisValue, arg1?, ..., argN?)

이 메서드는 부분 함수를 생성한다. `bind()`의 수신자를 호출하면서 this는 thisValue로 고정하고, arg1부터 argN까지 새 함수의 매개변수가 이어진다. 다시말해, 새 함수는 원래 함수를 호출할 때 자신이 받은 매개변수를 arg1~argN 다음에 이어서 붙인다.

```javascript
function fnc(){
  console.log('this: ' + this);
  console.log('arguments: ' + Array.prototype.slice.apply(arguments));
}
var bound = func.bind('abc', 1, 2);
```
bound는 새로운 함수로 매개변수 1,2를 기본적으로 전달하고 있다. 호출 결과는 다음과 같다.

```javascript
>bound(3);
this : abc
arguments : 1,2,3
```

다음 3가지 호출방법은 모두 동등하다. 

```javascript
jane.sayHello('Tarzan');                // 1

var func1 = jane.sayHello.bind(jane);   // 2
func1('Tarzan');

var funcc2 = jane.sayHello.bind(jane, 'Tarzan');     // 3
func2();
```

#### 함정 : 메서드를 추출할때 사라지는 this

객체에서 추출한 메서드는 하나의 함수로 돌아갑니다. 객체와의 연결은 파괴되고, 오작동 하는 경우가 많다.

```javascript
var counter = {
  count : 0,
  inc : function(){
    this.count++;
  }
};
```

inc를 추출해서 함수로 호출하면 제대로 동작하지 않는다. 함수로 호출되어 this가 전역객체를 가리키게 되어 window.count++를 시도한것과 다름 없다.

```javascript
> var func = counter.inc;
> func();         // counter객체에 효과 없음
> counter.count
0
```

> 추출된 함수가 엄숙한 모드라면 에러가 발생한다.

메서드를 제대로 추출하기 위해서는 `bind()`를 사용하여 `inc`와 `counter`의 연결이 끊어지지 않게 할 수 있다.

```javascript
> var func2 = counter.inc.bind(counter);
> func2();
> counter.count;
1
```

 
