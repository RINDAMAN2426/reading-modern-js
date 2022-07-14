# Chapter 13. Scope

```js
function add(x, y) {
  console.log(x, y);
  return x + y;
}

add(1, 2);

console.log(x, y); // ReferenceError: x is not defined
```

파라미터 `x`, `y`의 스코프는 함수 바디 내부. 따라서 외부에서 참조할 경우 `ReferenceError` 발생

```js
// 선언 위치에 따른 스코프의 차이
var v1 = 1;

if (true) {
  var v2 = 2;
  if (true) {
    var v3 = 3;
  }
}

function fn1() {
  var v4 = 4;

  function fn2() {
    var v5 = 5;
  }
}

console.log(v1); // 1
console.log(v2); // 2
console.log(v3); // 3
console.log(v4); // ReferenceError: v4 is not defined
console.log(v5); // ReferenceError: v4 is not defined
```

#### Quiz

```js
var foo = "foo";

function fn() {
  var foo = "bar";
  console.log(foo); // ?
}

fn();

console.log(foo); // ?
```

`console.log`에서 `foo`를 참조할 때 결정하는 것을 `identifier resolution`이라 함. 코드의 context를 고려 하여 코드를 실행함

#### 코드의 문맥과 환경

> 코드의 실행 위치, 주변 코드를 `lexical environment`라고 함. 즉 `context`는 `lexical environment`로 이루어짐. 이를 구현한 것을 `execution context`라고 하며, 모든 코드는 `execution context`에서 평가되고 실행됨

스코프를 통해 식별자인 변수 이름의 충돌을 방지, 같은 이름 또한 사용할 수 있게 됨. 즉 스코프는 `namespace`

#### var 키워드의 변수 중복 선언

> var 키워드의 경우 같은 스코프내에서 중복 선언이 허용 가능함. 하지만 이로 인한 변수 값의 재할당이 가능해져 side effect가 발생할 수 있음

## 스코프의 종류

스코프는 `global`과 `local`로 구분. `global`의 경우 가장 바깥 영역, `local`의 경우는 함수 바디 내부

### `global`과 `global scope`

전역에 선언한 변수는 `global variable`. 어디서든 참조할 수 있음

### `local`과 `local scope`

`local scope`에서 선언한 변수는 자신과 하위 `local`에서만 참조가 가능함.
`local`내에 `global variable`과 같은 이름이 존재한다면 자신의 스코프 내에 있는 변수를 참조함. 이는 `scope chain`을 통한 `identifier resolution`을 수행하기 때문

## 스코프 체인

함수 바디 내부에 함수가 정의되는 것을 함수의 중첩이라 표현. 내부에서 정의된 함수는 `nested function`, 포함하는 함수는 `outer function`이라고 함
함수의 중첩은 즉 스코프의 중첩. 스코프의 계층적 구조를 `scope chain`이라고 표현함.
자바스크립트 엔진은 `local scope`에서 시작하여 상위 `scope`로 이동하며 `identifier resolution`을 수행함

`scope chain`은 물리적으로 실존함. 자바스크립트 엔진은 실행 이전 `lexical environment`를 실제로 생성하여, 변수 선언이 실행되면 `key`로 등록, 할당이 이루어지면 해당 값을 변경하게됨

#### Lexical Environment

> `scope chain`은 `execution context`의 `lexical environment`를 단방향으로 연결하는 것. `global lexical`은 코드가 로드 되는 즉시, `local lexical`은 함수가 호출되는 즉시 생성됨

### 스코프 체인에 의한 변수 검색

상위 스코프의 변수를 하위 스코프가 참조할 수 있지만, 반대의 경우는 불가능. 이러한 계층적 구조는 `inheritance`와 유사함

### 스코프 체인에 의한 함수 검색

```js
function g() {
  console.log("global");
}

function outer() {
  function nested() {
    console.log("local");
  }
  nested();
}
outer();
```

위와 같이 함수 선언문으로 함수를 정의하면 런타임 이전에 함수 객체를 먼저 생성. `nested()`로 호출하는 순간 `nested`를 검색. 따라서 스코프는 `식별자를 검색하는 규칙`이라는 표현이 적합

## 함수 레벨 스코프

대부분의 프로그래밍 언어는 모든 code block이 `local scope`를 생성함 (`block level cope`. 하지만 `var`키워드의 변수는 오로지 함수 바디 만을 `local scope`로 인식함 (`function level scope`)

## 렉시컬 스코프

#### Quiz

```js
var x = 1;

function foo() {
  var x = 2;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```

### 동적 스코프 (dynamic scope)

함수를 어디서 호출했는 지에 따라서 동적으로 상위 스코프를 결정

### 렉시컬 스코프 (lexical scope)

함수 정의가 평가되는 시점에 상위 스코프를 결정. 대부분의 프로그래밍 언어가 따름.

자바스크립트는 `lexical scope`를 따르기 때문에 함수가 어디서 호출됐는지가 아닌 어디서 정의했는지로 상위 스코프를 결정함. 함수 정의가 실행이 되어 생성된 함수 객체는 이 결정된 상위 스코프를 기억함. (호출 시 마다 해당 상위 스코프를 참조함)
렉시컬 스코프의 확장은 Closure로 이어짐
