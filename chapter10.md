# Chapter 10. Object Literal

## Object

- javascript는 `object`기반 프로그래밍 언어. 원시 값을 제외한 나머지 값은 모두 객체
- 하나의 값만 나타내는 원시 타입과는 다르게 `data structure`. 또한 원시 값은 immutable value, 객체는 mutable value
- 0개 이상의 `property`로 구성된 집합. `property`는 `key/value`
- 함수는 javascript의 1급 객체. 따라서 값으로 사용 가능함. 이 때 해당 함수는 `method`라고 칭함

#### object and function

- 함수로 객체를 생성하기도 하며, 함수 자체가 객체이기도 함. 분리할 수 없는 개념

## 객체 리터럴에 의한 객체 생성

- `cpp`,`java`와 같은 객체 지향 언어는 `class`를 사전 정의하고 `new` 연산자와 함께 `consturctor`를 통한 객체 생성
- javascript의 객체 생성 방법은 아래와 같이 여러 방법이 존재
  - 객체 리터럴
  - `Object` 생성자 함수
  - 생성자 함수
  - `Object.create`
  - `Class(ES6)`
- 객체 리터럴 방식은 아래 코드와 같음

  ```js
  var person = {
    name: "Lee",
    sayHello: function () {
      console.log(`Hello! My name is ${this.name}`);
    },
  };

  console.log(typeof person); // object
  console.log(person); // { name: "Lee", sayHello: [Function: sayHello]}

  var empty = {};
  console.log(typeof empty); // object
  ```

- 객체 리터럴의 중괄호는 `code block`이 아님. `code block`과는 다르게 `semi colon`을 붙이도록 함

## 프로퍼티

- `key`: 빈 문자열 포함, 모든 문자열 혹은 `Symbol` 값
- `value`: 모든 값
- `key`는 네이밍 규칙을 준수하면 별도의 `colon`이 필요 없음

  ```js
  var person = {
    firstName: "Hyeongwuk",
    "last-name": "Choi",
  };
  ```

- 네이밍 규칙을 준수하지 않게되면 해당 문자를 연산자로 해석하게됨. 문자열로 평가 될 수 있는 데이터의 경우 대괄호를 사용해서 표현식을 만들 수 있음
  ```js
  var obj = {};
  var key = "foo";
  obj[key] = "bar";
  ```
- 빈 문자열도 가능은 하나, 키의 의미를 갖지 못함
- `string` 혹은 `Symbol` 외의 값을 사용할 경우 `string` 타입으로 암묵적 타입 변환이 일어남. 다만 예상하지 못한 에러가 발생할 수 있음
- 중복 키 선언의 경우 후에 선언한 값으로 덮어 씌워짐

## 메소드

- 자바스크립트의 함수는 일급 객체. 따라서 `property`의 값으로 사용 가능함
- 메소드 내부에서 사용하는 `this`는 객체 자신을 가리키는 참조 변수

## 프로퍼티 접근

- 접근 방식은 두가지가 존재함
  - `dot notation`
  - `bracket notation`
- `bracket notation`의 경우 반드시 `colon`으로 감싼 문자열이어야 함. 객체에 존재하지 않는 `property`에 접근하게 되면 `ReferenceError`가 아닌 `undefined`이 발생하게됨

### Quiz

```js
var person = {
  name: 'Choi Hyeongwuk',
  'last-name': 'Choi',
  1: 10,
};

console.log(person[name])

console.log(person.age); // ?

console.log(person.age.months); // ?

console.log(person.'last-name') // ?

console.log(person.last-name) // ?
                              // hint: browser와 node.js에서 다름

console.log(person['last-name']); // ?

console.log(person.1); // ?

console.log(person.'1'); // ?

console.log(person[1]);

console.log(person['1']);

```

## 프로퍼티 값 갱신

- 기존의 `property`에 새 값을 할당

## 프로퍼티 동적 생성

- 존재하지 않는 `property`에 값을 할당하면 `property`가 동적으로 생성됨

## 프로퍼티 삭제

- `delete` 연산자를 통한 `property` 삭제. 존재하지 않는 `property`라면 아무 에러없이 그냥 무시됨

## ES6 \> 객체 리터럴의 확장 기능

- `key`와 변수이름이 동일 할때 `property shorthand` 가능
- 문자열 혹은 문자열 타입변환이 가능한 값으로 평가되는 표현식으로 `key`를 동적 생성 가능
- 메소드 정의의 경우 `function` 생략 가능

  ```js
  // property shorthand
  let x = 1,
    y = 2;

  let obj = { x, y };
  console.log(obj); // { x: 1, y: 2 }

  // dynamic key

  var prefix = "prop";
  let i = 0;

  obj = {
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
  };
  console.log(obj); // { 'prop-1': 1, 'prop-2': 2, 'prop-3': 3 }

  // 메소드 축약 표현

  obj = {
    name: "Hyeongwuk",
    sayHi() {
      console.log("Hi! " + this.name);
    },
  };
  obj.sayHi(); // Hi! Hyeongwuk
  ```
