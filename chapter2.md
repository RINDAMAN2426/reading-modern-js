# 자바스크립트란?

## 자바스크립트의 탄생

- 1995년, 웹 브라우저를 지배하던 `Netscape communications`에서 브라우저에서 동작할 경량 프로그래밍 언어 도입을 결정. 그 후 탄생한 `Brendan Eich`의 `javascript`

- 1996년 3월, `Netscape communications`의 `Netscape Navigator 2` 브라우저에 탑재된 `Mocha`로 명명이 시작. 그 후 `LiveScript`로 이름이 바뀌었다가 `Javascript`로 최종 변경

## 자바스크립트의 표준화

- MS에서는 javascript의 파생버전인 `JScript`를 `IE 3.0`에 탑재. `JScript`와 `javascript` 간에는 표준화가 되지 못한체 적당히 호환. 즉 두 회사간에 서로의 브라우저에서만 동작하는 기능을 추가하기 시작. 이는 `cross browsing` 이슈의 시초.

- `Netscape communications`에서 `ECMA International`에 `javascript` 의 표준화를 요청

- 1997/7, `ECMA-262` 라는 표준 초반`(ECMAScript 1)`이 완성. 상표 문제로 `ECMAScript`로 명명 됨.

- 2009 `ECMAScript 5`는 `HTML5`와 함께 출현

## 자바스크립트 성장의 역사

- 초창기는 주요 로직은 웹 서버에서 실행, 브라우저는 단순한 `HTML`, `CSS`를 렌더링하는 역할

### Ajax (1999)

- 1999, 서버와 브라우저간 `asynchronous` 방식으로 통신하는 `Asynchronous Javascript And XML` 이 `XMLHttpRequest` 라는 이름으로 등장

- 이는 전체 `HTML`코드를 다시 서버로 받아올 필요가 없이 필요한 데이터에 대한 통신 후 변경하는 렌더링 방식이 가능토록 함

### jQuery (2006)

- 2006, `jQuery`의 등장으로 `DOM`이 더욱 쉽게 제어

### V8 Javascript Engine (2008, Google)

- `Google Maps`에서 성공을 거두면서 2008, `Google` 에서는 `V8 Javascript Engine`이 등장. 이 때부터 대다수의 로직들이 클라이언트 쪽으로 이동하기 시작, 프론트엔드가 주목받기 시작함

### Node JS (2009, Ryan Dahl)

- `V8` 엔진을 통한 javascript runtime environment.

- 브라우저에서만 동작하던 javascript를 그 외의 환경에서도 동작할 수 있도록 독립시킨 실행 환경.

- 프론트와 백에서 같은 언어를 사용하는 `isomorphic`의 이점이 생김

- `asynchronous I/O`, `single thread event loop` 기반.

- 최근엔 `cross platform` 언어의 역할로 주목. `PhoneGap, Ionic, NodeJS, Electorn, TensorFlow.js, Jonny-Five`

### SPA Framework

- `CBD(Component based development)` 방법론이 기반인 `SPA(Single Page Application)` 이 대중화.

- `Angular`,`React`,`Vue`,`Svelte`의 등장.

## Javascript와 ECMAScript

- `ECMAScript`라 함은, `ECMA-262`를 지칭. 값, 타입, 객체와 프로퍼티 등 핵심 문법을 규정. 각 브라우저 제조사는 `ECMAScript`를 준수하여 엔진을 구현하여야함.

- `Javascript`는 `ECMAScript`와 더불어 브라우저가 지원하는 `Web API`를 포함하는 개념

## Javascript의 특징

- 브라우저에서 동작하는 유일한 언어

- `Self`에서는 `prototype`기반 상속을, `Scheme`에서는 일급 함수의 개념을 차용

- 개발자가 별도로 컴파일 작업을 하지 않는 `interpreter language`. 모던 엔진들은 `interpreter`와 `compiler`의 장점들을 결합해 `interpreter`의 단점을 해결하려 노력.

- `imperative`, `functional`, `prototype-based 객체지향`을 지원하는 멀티 패러다임.

## ES6 Browser

- `IE`를 제외한 대부분의 모던 브라우저는 `ES6`를 지원. `NodeJS`는 `v4`부터 지원. 구형 브라우저 지원을 위해서는 `transfiler`를 이용한 `downgrade`가 필요함.
