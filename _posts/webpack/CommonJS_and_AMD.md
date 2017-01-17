# CommonJS

CommonJS는 자바스크립트를 클라이언트 기반에서만 사용하는 것 뿐만 아니라 서버에서도 사용할 수 있도록 하기 위한 명세.

CommonJS에서 제기한 자바스크립트를 범용적으로 사용하기 위해서 부족한 점들

* 상호 호환되는 표준 라이브러리가 없다.
* DB에 연결할 수 있는 표준 라이브러리가 없다.
* 다른 모듈을 삽입하는 표준 방식이 없다.
* 코드를 패키징해서 배포/설치하는 방법이 없다.
* 모듈(라이브러리)간의 의존성 문제를 해결해주는 저장소가 없다.

위의 문제를 해결하는 표준을 만들자는 것이 CommonJS이다. 그리고 위의 문제들은 결국 모듈화에 대한 문제 제시이다.

따라서, **CommonJS** 는 **자바스크립트 모듈화에 대한 명세** 라고 생각하면 된다.
(실제 구현은 하지 않는다.)


## CommonJS의 모듈화

모듈은 다음 3가지 요소로 이루어진다.

* 스코프(Scope) - 모든 모듈은 자신만의 독립적인 실행 영역이 있어야 한다.
* 정의(Definition) - 모듈 정의는 exports 객체를 이용한다.
* 사용(Usage) - 모듈 사용은 require 함수를 이용한다.


    // 정의 예제 - hello.js
    var hello = '안녕';
    module.exports = hello;

    // 사용 예제 - world.js
    var hello = require('./hello')
    var world = hello + ' 세상';
    module.exports = world;

## CommonJS의 문제점

CommonJS의 모듈 명세는 서버사이드를 기본으로 하기 때문에 모든 파일이 로컬 디스크에 있어서 필요할 때 바로 불러올 수 있는 상황을 전제로 한다.

이러한 방식은 브라우저에서 필요한 모듈을 모두 내려받을 때 까지 아무것도 할 수 없게 되는 치명적인 단점이 존재한다. 또한, 자바스크립트가 브라우저에서 동작할 때는 파일 단위의 스코프가 없다.

이런 문제를 해결하기 위해서 서버 모듈을 비동기적으로 클라이언트에 전송할 수 있는 *모듈 전송 포맷* 을 추가로 정의해서 콜백 방식과 유사하게 서버 모듈을 비동기적으로 로드 할 수 있도록 했다.

# 동적 로딩(Dynamic Loading, Lazy Loading)?!

`<script>`태그는 페이징 랜더링을 방해한다. `<script>`태그를 처리(HTTP요청 -> 다운로드 -> 파싱 -> 실행)하는 동안 브라우저는 다른 동작을 하지 않는다. 브라우저 입장에서는 안전한 동작 방식이지만, 사용자 입장에서는 답답하다. 그래서 `<script>`태그를 `<body>`태그 마지막에 배치하는 식의 최적화 방법을 사용하기도 한다.

하지만, 웹앱의 규모가 커질 수록 페이지에 필요한 모든 자바스크립트를 로딩하는 것은 첫 화면의 랜더링과 사용자의 액션에 제약을 주게된다. 따라서 필요한 자바스크립트만 로딩하고 사용자의 반응에 따라서 점진적으로 로딩하는 방식이 필요해졌다.

필요에 따라서 파일을 로딩하는 방법 중에서 가장 안전하고 합리적인 방법은 `<script>`태그를 동적으로 삽입하는 방법이다.

    var scriptEl = document.createElement('script');
    scriptEl.type = 'text/javascript';
    scriptEl.src = 'example.js';
    scriptEl.onload = function () { ... 콜백 지정... };
    document.getElementByTagName('head')[0].appendChild(scriptEl);

하지만 이러한 방법도 각 파일의 삽입 순서를 지키기 위해서 콜백 지옥에 빠질 수 있다.

이러한 동적 로딩과 파일간의 의존성 문제는 AMD로 해결할 수 있다.

# AMD(Asynchronous Module Definition)

AMD는 CommonJS를 정의하는 워킹그룹에서 갈라져나온 자바스크립트 모듈화에 대한 명세이다. 위에 서술한 CommonJS의 비동기 처리에 대한 처리 방식을 두고 이견을 보이다가 합의점을 찾지 못해서 독립한 명세이다.

본래의 CommonJS가 자바스크립트를 범용적으로 사용하기 위해서 모듈화를 표준화 하고자 했다면, AMD는 브라우저 내에서의 실행에 중점을 두었다고 할 수 있다.

CommonJS와 AMD의 뿌리는 같기 때문에 사용 방식은 유사하지만, 서버 사이드(CommonJS)와 네트워크를 이용한 서버 모듈 사용(AMD)이라는 관점의 차이가 있기 때문에 각각의 환경에서 더 유연한 방법을 제공한다.


## AMD의 모듈화

CommonJS에서 갈라져 나온 명세이기 때문에 많은 부분이 닮았고, 호환할 수 있도록 정의 되어 있다. `require()` 함수로 다른 모듈을 삽입할 수 있고, `exports`형태로 모듈을 정의 할 수 있다.

그 외에 AMD만의 대표적인 기능으로 `define()` 함수가 있다. 브라우저 환경에서의 스코프(네임스페이스) 역할을 하여 모듈에서 사용하는 변수와 전역변수를 분리한다.

    // define 함수
    define(id?, dependencies?, factory);

>* id - 모듈의 식별자. 따로 지정하지 않으면 src값으로 설정된다.
>* dependencies - 정의하는 모듈의 의존성을 배열로 나타낸다. 반드시 먼저 로드 되어야 하는 모듈을 명시하면 된다. dependencies에서 지정된 모듈들은 factory에 인자로 넘어오게 된다.
>* factory - 모듈이나 객체를 인서턴스화 하는 실제 구현 팩토리. 함수라면 싱클톤으로 한번 만 실행 되고, 반환되는 값이 있으면 그값을 exports 객체의 송석값으로 할당한다.

    // 정의 예제 - /js/alpha.js
    define("alpha", ["require", "exports"], function (require, exports) {
        return {
            hello : function() {
                return "hello";
            }
        };
    });

    define("beta", ["require", "exports", "alpha"], function (require, exports, alpha) {
        return alpha.hello() + " world";
    });

AMD 모듈 명세는 CommonJS의 모듈 전송 포맷보다 간단하고, 서버사이드에서도 동일한 코드로 동작한다. 그리고 `deinfe()`함수(클로저 패턴)를 이용해 모듈을 구현하므로 전역변수 문제가 없다. 또한, 동적 로딩을 제공한다.


위에서 살펴본 CommonJS와 AMD의 명세를 구현한 로더들이 많이 존재한다. 그 중에서 **RequireJS** 가 가장 인지도가 높다.

# 마무리

Webpack은 CommonJS와 AMD의 명세를 모두 지원한다. 그리고 2 버전부터는 **ES2015** 의 `import`를 기본으로 지원하고 있다.

Webpack 2.x에서 모듈로 취급하는 것(?)들

>* ES2015의 `import`
>* CommonJS의 `require()`
>* AMD의 `define()`과 `require()`
>* CSS, SASS, LESS 파일의 `@import`
>* CSS와 HTML파일의 이미지 URL(`url(...)`, `<img src="..."/>`)
