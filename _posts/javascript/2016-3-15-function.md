---
layout : post
title : "Function"
published : true
tags : [javascript, function]
---

# 함수 Function

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




