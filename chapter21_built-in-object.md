# Chapter 21. Built-in Object

## `javsacript`의 객체 분류

1. **standard build-in objects/native objects/global objects**
   - `ECMAScript`에 정의된 객체. 어플리케이션 전역의 공통 기능 제공
   - 실행환경(Browser/NodeJS)과 관련 없이 사용 가능
2. **host objects**
   - 실행환경에서 추가로 제공하는 객체
   - Browser의 경우 Web API, NodeJS의 경우는 고유 API
3. **user -defined objects**
   - 사용자가 직접 정의한 객체

## `Standard Built-in Object`

- `Object`, `String`, `Number`, `Boolean` 등
- `Math`, `Reflect`, `JSON`을 제외한 객체는 모두 인스턴스를 생성할 수 있는 `Constructor Function Object`
- `Constructor Function Object`는 `prototype method`, `static method`를 제공, 아닌 객체는 `static method`만을 제공함

`prototype method`와 `static method`의 예시는 아래와 같음

const numObj = new Number(1.5);

// prototype method
console.log(numObj.toFixed()); // 2
// static method
console.log(Number.isInteger(0.5)); // false

아래 코드와 같이 생성된 인스턴스의 `prototype`은 해당 `Constructor Function`의 `prototype`

const strObj = new String('Hello');

console.log(Object.getPrototypeOf(strObj) === String.prototype); // true

## Primitive value and wrapper object

const str = 'hello';

console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO

위 코드를 보면 원시 값임에도 `method`를 가진 객체처럼 동작함
이는 엔진이 마침표 표기법으로 접근 시 일시적으로 원시 값을 연관 객체로 변환해주기 때문
객체로 변환하여 `property` 혹은 `method` 호출 후 다시 원시값으로 되돌림
이 때 생성되는 객체를 `wrapper object`라고 함

`string` 타입에 대해 접근 시, `String`으로 인스턴스를 생성, 해당 문자열은 `wrapper object`의 `[[StringData]]`라는 `internal slot`에 할당됨. 그 후 관련된 연산을 수행 후 다시 `[[StringData]]`에 저장된 값으로 되돌린 후 `wrapper object`는 `garbage collection`의 대상이 됨

살짝 다르게 동작하는 경우는 `ES6`의 `Symbol`. 이 데이터 타입은 일반적으로는 리터럴 표기법으로 생성이 불가능함.

`null`과 `undefined`는 `wrapper object`를 생성하지 않음

## Global Object

코드가 실행되기 이전 엔진에 의해 어떠한 객체보다도 먼저 생성되는 객체. 최상위 객체
브라우저의 경우 `window`,`self`,`this`,`frames`, NodeJS의 경우 `global`이 전역 객체를 가리킴

#### `globalThis`

ES11에서 도입되었으며, 모든 실행 환경에서 전역 객체를 지칭하는 식별자를 통일한 식별자

전역 객체는 `Built-in Object`와 `Host Object`, 그리고 `var`로 선언한 전역 변수 및 함수를 `property`로 갖고 있음. 추가로 `implicit global` 변수도 여기 해당함.
최상위 객체이기 때문에 어떠한 객체의 `property`도 아니며 개발자가 생성할 수 있는 객체도 아님

### `built-in global property`

전역 객체의 `property`를 의미

1. `Infinity` - 무한대를 나타내는 숫자 값
2. `NaN` - `Not a Number`. `NaN`은 `Number.NaN`과 같음
3. `undefined` - 해당 값은 `undefined` 값을 가짐

### `built-in global function`

1. `eval` - 코드를 인수로 전달받음. 표현식이라면 런타임에서 평가하여 값을 생성, 아니면 런타임에 실행 됨
2. `isFinite` - 인수가 유한수라면 `true`, 아니라면 `false`. 수가 아니라면 숫자로 타입을 변환하여 검사 수행, `NaN`일 시 `false`. `null`이라면 0이되어 `true`
3. `isNaN` - 인수가 `NaN`인지 체크하여 결과를 `boolean`으로 반환. 숫자가 아닌 경우 숫자로 변환하여 검사 수행
4. `parseFloat` - 인수를 floating point number로 파싱하여 해석
5. `parseInt` - 인수를 interger로 파싱하여 해석
6. `encodeURI / decodeURI` - 완전한 `URI`를 문자열로 전달 받아 이스케이프 처리를 위한 인코딩 혹은 이스케이프 처리 이전으로 디코딩
   - 이스케이프 처리란, 어떤 시스템에서도 읽을 수 있도록 ASCII 문자셋으로 변환하는 것
7. `encodeURIComponent / decodeURIComponent` - `URI`의 구성요소를 전달 받아 인코딩 / 디코딩

### `implicit global`

var x = 10; // global variable

function foo() {
y = 20;
}
foo();

console.log(x + y); // 30

위 코드의 변수 y와 같이 선언되지 않았음에도 전역에서 참조할 수 있는 경우를 가리킴
선언하지 않은 식별자에 값을 할당하게 되면, 해당 식별자는 전역 객체의 `property`가 됨
하지만 y는 변수가 아닌 전역 객체의 `property` 이기 때문에 호이스팅은 발생하지 않음

var x = 10; // global variable

console.log(y); // ReferenceError: y is not defined

function foo() {
y = 20;
}
foo();

console.log(x + y); // 30

또한 변수가 아니기 때문에 `delete` 연산자로 삭제가 가능함. 전역 변수는 이와 달리 같은 전역 객체의 `property`이지만 삭제할 수는 없음
