# Chapter 15. 키워드와 블록 레벨 스코프

## var 키워드 변수의 문제점

`es5`까지는 `var` 키워드로만 변수 선언이 가능

### 변수 중복 선언 허용

```js
var x = 1;
var y = 1;

// 동일 스코프 내에서 중복 선언이 허용

// 초기화 문이 있는 변수 선언문은 js 엔진에 의해 var 키워드가 없는 것 처럼 동작
var x = 100;

// 초기화 문이 없는 변수 선언문은 무시
var y;

console.log(x); // 100
console.log(y); // 1
```

### 함수 레벨 스코프

`var` 키워드의 변수는 함수의 코드블록 만을 지역 스코프로 인정함. 즉 외부에서 선언한 변수는 모두 전역 변수로 처리됨

```js
var x = 1;

if (true) {
  var x = 10; // 전역 변수 값을 변경하게 됨
}

console.log(x); // 10
```

위 예시와 마찬가지로 `for`문도 동일함

### 변수 호이스팅

`var` 키워드로 변수 선언 시 호이스팅으로 인해 선언문이 스코프의 선두로 끌어 올려진 것 처럼 동작. 할당문 이전 참조 시 `undefined` 처리

```js
console.log(foo); // undefined

foo = "hello";

console.log(foo); // hello

var foo;
```

가독성도 떨어질 뿐더러 오류가 발생될 여지를 남기게 됨

## let

### 변수 중복 선언 금지

`var`의 경우 중복 선언 및 할당 시에도 오류가 발생하지 않고 재할당이 되는 부작용 발생.
`let` 키워드의 경우 `SyntaxError` 발생

```js
let foo = 1;

let foo = 2; // SyntaxError: Identifier 'foo' has already been declared
```

### 블록 레벨 스코프

`var` 키워드의 경우 오로지 함수 코드 블록 만을 지역 스코프로 인정하는 함수 레벨 스코프.
`let` 키워드의 경우 모든 코드 블록을 지역 스코프로 인정하는 블록 레벨 스코프를 따름

```js
let foo = 1;

{
  let foo = 2;
  let bar = 3;
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

### 변수 호이스팅

`let` 키워드로 선언한 변수는 “**선언단계**” 와 “**초기화 단계**”가 분리됨. 런타임 이전 엔진에 의해 암묵적으로 선언 단계가 먼저 실행 되지만, 초기화 단계는 선언문에 도달했을 때 실행 됨. 초기화 이전 접근하려 했을 시 `ReferenceError` 발생.

스코프의 시작 지점부터 초기화 단계의 시작(선언문) 지점까지 변수를 참조할 수 없게 되는데, 이 구간을 `TemporalDeadZone; TDZ` 라고 부름

```js
console.log(foo); // ReferenceError: foo is not defined
// 여기까지 TDZ
let foo;
console.log(foo); // undefined

foo = 1;
console.log(foo); // 1
```

변수 호이스팅이 발생하지 않은 것 같지만 그렇지는 않음

#### Quiz

```js
let foo = 1; // global

{
  console.log(foo); // ?
  let foo = 2;
}
```

\* `let`,`const`를 포함한 모든 선언은 호이스팅이 동작 됨

### 전역 객체와 let

`var` 키워드로 선언한 전역 변수, 함수, 선언하지 않은 변수에 값을 할당한 암묵적 전역은 모두 전역 객체 `window`의 프로퍼티가 됨

```js
var x = 1;

y = 2;

function foo() {}

console.log(window.x); // 1

console.log(x); // window.x(1)

console.log(window.y); // 2

console.log(y); // window.y(2)

console.log(window.foo); // function foo()

console.log(foo); // window.foo
```

이와 달리 `let`으로 선언한 전역 변수는 전역 객체의 프로퍼티가 아님. `let` 전역 변수는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재하게 됨

## const

`const`는 상수 선언을 위함. 반드시 상수만을 위해 사용하진 않음

### 선언과 초기화

```js
const foo; // SyntaxError: Missing initializer in const declaration
```

`let` 과 마찬가지로 블록 레벨 스코프. 변수 호이스팅이 발생하지 않는 것 처럼 동작함.

### 재할당 금지

```js
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

### 상수

원시 값을 할당했을 경우 위와 같이 재할당이 불가능 함. 원시 값이 `immutable value` 이기 때문.

### const 키워드와 객체

객체를 할당했을 경우에는 값의 변경이 가능. 객체는 재할당 없이도 직접 변경이 가능하기 때문

`const`는 재할당을 금지할 뿐 `immutable`을 의미하는 것은 아님.

## `var` vs. `let` vs. `const`

변수 선언은 기본적으로 `const`. 재할당이 필요한 경우만 `let`을 한정해 사용함. `ES6` 이상 버전의 경우 `var`은 사용하지 않는 것이 좋음

`let` 변수는 최대한 스코프를 좁게 만드는 것이 좋음
