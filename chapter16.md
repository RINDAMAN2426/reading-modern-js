# Chapter 16. Property and Attribute

## 내부 슬롯과 내부 메서드

`internal slot`과 `internal method`는 엔진 구현의 알고리즘을 설명하기 위해 `ECMAScript` 사양에서 사용하는 `pseudo property`와 `pseudo method`.

[ECMA spec](https://262.ecma-international.org/13.0/#sec-object-type "ECMA spec")에서 이중 대괄호로 감싼 이름들이 `internal slot`과 `internal method`

엔진에서 동작하며, 개발자가 직접 접근할 수는 없음. 일부 슬롯과 메소드만 간접적으로 접근할 수 있는 수단을 제공함. `[[Prototype]]`이라는 `internal slot`의 경우, `__proto__`를 통해 간접적으로 접근이 가능함

```js
const o = {};

console.log(o.[[Prototype]]); //SyntaxError: Unexpected token '['
console.log(o.__proto__); // [Object: null prototype] {}
```

## Property Attribute and Property Descriptor

자바스크립트 엔진은 프로퍼티 생성 시, 프로퍼티의 상태를 나타내는 `property attribute`를 기본값으로 자동 정의함.

프로퍼티 상태는 `value`, `writable`, `enumerable`, `configurable` 를 지칭함

`property attribute`는 엔진이 관리하는 `meta-perperty`인 `internal slot`인 `[[Value]]`, `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`이 있다. 직접 접근은 역시 불가능하며 `Object.getOwnPropertyDescriptor` 메소드를 통해 간접적인 확인은 가능함

```js
const person = {
  name: "brouk",
};

console.log(Object.getOwnPropertyDescriptor(person, "name")); // { value: 'brouk', writable: true, enumerable: true, configurable: true }
```

`Object.getOwnPropertyDescriptor`의 반환 값은 해당 attribute의 정보를 제공하는 `property descriptor`. 존재하지 않는 property 혹은 상속받은 property의 경우 `undefined`를 반환함

## Data Property and Accessor Property

**data property** : `key`, `value`로 구성된 일반적인 property
**accessor property**: 자체적인 값을 갖지 않고, 다른 data property의 값을 읽거나 저장할 때 호출되는 `accessor function`으로 구성된 property

### Data Property

위에 서술한 것과 동일한 attribute를 갖고 있음. 엔진이 property 생성시 기본 값으로 자동 정의 됨. 동적 생성도 마찬가지

1. `[[Value]]`
   - property 키를 통해 값에 접근하면 반환되는 데이터
2. `[[Writable]]`
   - 변경 가능 여부를 나타내는 `boolean` 데이터
   - `false`인 경우 읽기 전용 property가 됨
3. `[[Enumerable]]`
   - property 열거 가능 여부를 나타내는 `boolean` 데이터
   - `false`인 경우 `for ... in` 혹은 `Object.keys` 등으로 열거할 수 없음
4. `[[Configurable]]`
   - property 재정의 가능 여부를 나타내는 `boolean` 데이터
   - `false`인 경우 해당 property 삭제 혹은 데이터의 변경이 금지됨
   - `[[Writable]]`이 `true`라면, 데이터의 변경 혹은 `[[Writable]]`을 `false`로 변경하는 것은 가능함

### Accessor Property

`accessor property`는 자체적인 값을 갖지 않고 `accessor function`으로 구성된 property. 아래와 같은 `property attribute`를 갖고 있음

1. `[[Get]]`
   - `accessor property`를 통해 `data property`의 값을 읽을 때 호출되는 함수
2. `[[Set]]`
   - `accessor property`를 통해 `data property`의 값을 저장할 때 호출되는 함수
3. `[[Enumerable]]`
   - `data property`와 동일
4. `[[Configurable`
   - `data property`와 동일

```js
const person = {
  firstName: "Hyungwuk",
  lastName: "Choi",

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },

  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
};

console.log(`${person.firstName} ${person.lastName}`); // Hyungwuk Choi

// person의 fullname 변수에 값을 저장하면 setter 함수가 호출 됨
person.fullName = "Hyeongwuk Kang";

console.log(person); // { firstName: 'Hyeongwuk', lastName: 'Kang', fullName: [Getter/Setter] }
console.log(person.fullName); // hyeongwuk gang

// data property
let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log(descriptor); // { value: 'Hyeongwuk', writable: true, enumerable: true, configurable: true }

// accessor property
descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log(descriptor); // { get: [Function: get fullName], set: [Function: set fullName], enumerable: true, configurable: true }
```

`internal slot/method` 관점에서 설명하면 아래와 같음

`access property(fullName)`으로 property 값에 접근하면, 내부적으로 `[[Get]] internal method`가 호출되며 다음과 같이 동작

1. `property key` 가 유효한지 확인. 해당 키는 문자열 혹은 `Symbol`이어야 함. `'fullName`이라는 문자열이므로 유효.
2. `prototype chain`에서 해당 `property` 검색. `person` 객체에 해당 property 존재
3. `fullName` property가 어떤 property인지 확인. 여기선 `accessor property`
4. `access property`이므로 `[[Get]]`, getter 함수를 호출하며 결과를 반환함

#### prototype

prototype은 어떠한 객체의 상위 객체 역할을 하는 객체. 하위 객체에게 property와 method를 상속함. 하위 객체는 자신의 property 혹은 method처럼 사용이 가능함

`prototype chain`은 prototype이 단방향 Linked List 형태로 되어있는 상속 구조를 지칭. 어떠한 객체에 해당 property 혹은 method가 없다면, 체인을 따라 차례대로 검색함

## Property 정의

```js
const person = {};

Object.defineProperty(person, "firstName", {
  value: "Hyeongwuk",
  writable: true,
  enumerable: true,
  configurable: true,
});

// 누락되는 속성은 전부 false 처리
Object.defineProperty(person, "lastName", { value: "Choi" });

let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log("firstName", descriptor); // firstName { value: 'Hyeongwuk', writable: true, enumerable: true, configurable: true }

descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log("lastName", descriptor); // lastName { value: 'Choi', writable: false, enumerable: false, configurable: false }

// lastName은 enumerable이 false이기 때문에 열거가 불가능함
console.log(Object.keys(person)); // [ 'firstName' ]

// writable이 false이기 때문에 변경 불가능
// 에러는 발생하지 않고 그냥 무시됨
person.lastName = "Kang";

// configurable 값이 false이기 때문에 삭제 불가능
// 마찬가지로 에러는 발생하지 않고 무시됨
delete person.lastName;

// configurable 값이 false이기 때문에 재정의도 불가능함
// Object.defineProperty(person, 'lastName', { enumerable: true }); // TypeError: Cannot redefine property: lastName

descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log("lastName", descriptor); // lastName { value: 'Choi', writable: false, enumerable: false, configurable: false }

Object.defineProperty(person, "fullName", {
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  set(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
  enumerable: true,
  configurable: true,
});

descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log("fullName", descriptor); // fullName { get: [Function: get], set: [Function: set], enumerable: true, configurable: true }

person.fullName = "Hyeongwuk Kang";
console.log(person); // { firstName: 'Hyeongwuk', fullName: [Getter/Setter] }
```

## 객체 변경 방지

객체 데이터는 변경 가능한 값이므로 재할당 없이 직접 변경이 가능함. property의 추가, 삭제, 갱신이 가능하며 property attribute도 재정의할 수 있음

객체의 변경을 방지하기 위한 아래 메소드들이 존재함

1. `Object.preventExtensions` - 프로퍼티 추가 불가능. 나머지 삭제, 읽기, 쓰기, attribute 재정의 가능
2. `Object.seal` - 추가, 삭제, attribute 재정의 불가능. 읽기, 쓰기만 가능
3. `Object.freeze` - 읽기를 제외한 나머지 불가능

### 객체 확장 금지 (`Object.preventExtensions`)

동적 추가, `Object.defineProperty` 모두 불가능

```js
const person = { name: "brouk" };

console.log(Object.isExtensible(person)); // true

Object.preventExtensions(person);

console.log(Object.isExtensible(person)); // false

person.age = 30;
console.log(person); // ignore, strict mode에서는 Error

Object.defineProperty(person, "age", { value: 20 });
console.log(person); // TypeError: Cannot define property age, object is not extensible

delete person.name;
console.log(person); // {}
```

### 객체 밀봉 (`Object.seal`)

추가, 삭제, attribute 재정의 불가능. 읽기 쓰기만 가능함

```js
const person = { name: "brouk" };

console.log(Object.isSealed(person)); // false

Object.seal(person);

console.log(Object.isSealed(person)); // true

console.log(Object.getOwnPropertyDescriptor(person)); // { name: { value: 'brouk', writable: true, enumerable: true, configurable: false }}

person.age = 30; // ignore, strict mode에서는 Error
console.log(person); // { name: 'brouk' }

delete person.name; // ignore, strict mode 에서는 에러
console.log(person); // { name: 'brouk' }

person.name = "Hyeongwuk"; // // 쓰기는 가능함
console.log(person); // { name: 'Hyeongwuk' }

Object.defineProperty(person, "age", { value: 20 });
console.log(person); // TypeError: Cannot define property age, object is not extensible
```

### 객체 동결 (`Object.freeze`)

읽기만 가능하고 나머지는 모두 금지함

```js
const person = { name: "brouk" };

console.log(Object.isFrozen(person)); // false

Object.freeze(person);

console.log(Object.isFrozen(person)); // true

console.log(Object.getOwnPropertyDescriptor(person)); // { name: { value: 'brouk', writable: false, enumerable: false, configurable: false }}

person.age = 30; // ignore, strict mode에서는 Error
console.log(person); // { name: 'brouk' }

delete person.name; // ignore, strict mode 에서는 에러
console.log(person); // { name: 'brouk' }

person.name = console.log(person); // ignore, strict mode 에서는 에러 // { name: 'brouk' }

Object.defineProperty(person, "age", { value: 20 });
console.log(person); // TypeError: Cannot define property age, object is not extensible
```

### 불변 객체

위 변경 방지 메소드들은 `shallow only`로 만일 중첩객체가 있다면 해당 중첩 객체에는 영향을 주지 못함. 중첩 객체에도 영향을 주려면 결국엔 `Object.freeze`의 재귀호출이 필요

```js
function deepFreeze(target) {
  if (!target || typeof target !== "object" || Object.isFrozen(target)) return;
  Object.freeze(target);
  Object.keys(target).forEach((key) => deepFreeze(target[key]));
  return target;
}

const person = {
  name: "brouk",
  contract: {
    email: "brouk.develop@gmail.com",
  },
};

deepFreeze(person);

console.log(Object.isFrozen(person));

console.log(Object.isFrozen(person.contract));

person.contract.email = "hello world";
console.log(person);
```
