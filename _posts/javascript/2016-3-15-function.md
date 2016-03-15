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

