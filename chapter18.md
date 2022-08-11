# Chapter 18. 함수와 일급 객체

## 일급 객체

아래 조건을 만족하는 객체

1. 무명의 리터럴로 생성이 가능. 즉 런타임 생성 가능
2. 변수나 자료구조에 저장 가능
3. 함수의 매개변수에 전달 가능
4. 함수의 반환값으로 사용 가능

자바스크립트의 함수는 위의 조건을 모두 만족 가능하기 때문에 일급 객체. 즉 객체와 동일하게 사용이 가능함.
함수의 반환값으로 사용 가능하기 때문에 함수형 프로그래밍을 가능하게 할 수 있는 부분. 일반 객체와의 차이점은 함수는 호출이 가능하고 앞 챕터 내용처럼 고유의 프로퍼티를 소유하고 있음

## 함수 객체의 프로퍼티

```js
// browser

function square(number) {
  return number * number;
}

console.dir(square);

// ƒ square(number)
// arguments: null
// caller: null
// length: 1
// name: "square"
// prototype: {constructor: ƒ}
// [[FunctionLocation]]: VM2151:1
// [[Prototype]]: ƒ ()
// [[Scopes]]: Scopes[1]
```

해당 함수의 property attributes를 확인해보면 아래와 같음

```js
{length: {…}, name: {…}, arguments: {…}, caller: {…}, prototype: {…}}
arguments: {value: null, writable: false, enumerable: false, configurable: false}
caller: {value: null, writable: false, enumerable: false, configurable: false}
length: {value: 1, writable: false, enumerable: false, configurable: true}
name: {value: "square", writable: false, enumerable: false, configurable: true}
prototype: {value: {…}, writable: true, enumerable: false, configurable: false}
[[ProtoType]]: Object
```

일반 객체에는 없는 `arguments`, `caller`, `length`, `name`, `prototype` 등을 확인할 수 있으며, `__proto__`accessor property를 Object.prototype 객체로부터 상속받음.

### arguments

`arguments` 프로퍼티의 값은 `arguments` 객체. 이는 전달된 argument들에 대한 정보를 담고 있는 iterable한 유사 배열 객체이고, 함수 내부 지역 변수로 사용됨.

함수의 매개변수들은 함수 내부 변수와 동일하게 취급되기 때문에, 함수가 호출되면 함수 내부에서 매개변수가 선언 및 `undefined`로 초기화, 이후 인수가 할당 됨. 따라서 매개변수 개수보다 인수가 적을 경우에는 `undefined`로 초기화가 되있는 것. 단, 인수가 더 많은 경우는 그냥 무시됨. 하지만 이는 `arguments` 객체에는 남아 있음

`arguments` 객체는 인수를 property 값으로 소유, 키는 인수 순서. `callee` property는 함수 자신을 가리키며 (`arguments`를 생성한 함수), `length`는 인수의 개수를 가리킴

#### `arguments` 객체에서 `Symbol(Symbol.iterator)` property

> `Symbol(Symbol.iterator)` property는 해당 객체를 순회 가능한 자료구조인 `iterable`로 만들기 위함. 이 property를 키로 사용한 method를 구현하는 것에 의해서 `iterable`이 됨

```js
function multiply(x, y) {
  const iterator = arguments[Symbol.iterator]();

  console.log(iterator.next()); // { value: 1, done: false }
  console.log(iterator.next()); // { value: 2, done: false }
  console.log(iterator.next()); // { value: 3, done: false }
  console.log(iterator.next()); // { value: 4, done: false }
  console.log(iterator.next()); // { value: 5, done: false }
  console.log(iterator.next()); // { value: undefined, done: true }

  return x * y;
}

multiply(1, 2, 3, 4, 5);
```

위와 같은 `arguments`의 특성을 이용하면 가변 인자 함수를 구현할 때 유용함. `arguments` 객체는 `array-like object` 임. 즉 `length` property를 가진 객체로 `for`문으로 순회가 가능함

#### array-like object & iterable

> ES6의 iteration protocol을 준수하면 순회 가능한 자료구조인 iterable이 됨. 이 개념이 없던 ES5에서 `arguments`는 유사 배열 객체로 구분. ES6부터는 유사 배열 객체이자 `iterable`

`array-like object`는 엄연히 배열은 아니므로 배열 메소드 사용시에는 에러가 발생함. 따라서 `Function.prototype.call` 등을 사용하여 간접호출을 해야함. 이 번거로움 해결을 위해서 ES6 `rest parameter`가 도입됨

```js
function es5_sum() {
  const array = Array.prototype.slice.call(arguments);
  return array.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(es5_sum(1, 2, 3, 4, 5)); // 15

function es6_sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(es6_sum(1, 2, 3, 4, 5)); // 15
```

이러한 이유로 ES6 이후로는 `arguments`의 유용성이 많이 떨어짐

### caller property

`caller` property는 ECMAScript 사양에 포함이 안된 비 표준 property. 이후 표준화 예정도 없음. 그냥 함수 자신을 호출한 함수를 가르킴

### length property

`length`는 함수를 정의할 때 선언한 매개변수의 개수 (`arguments`의 length와는 다름)

### name property

`name`은 `ES6` 이전까진 비표준, 이후로는 정식 표준이 됨. 따라서 `ES5`와 `ES6`의 동작이 다름

익명 함수 표현식의 경우 `ES5`의 `name`은 빈 문자열. `ES6`에서는 이 함수를 가르키는 식별자를 값으로 갖게 됨

```js
var foo = function () {};
// es5 : ''
// es6 : foo
```

### `__proto__` accessor property

모든 객체는 `[[Prototype]]`이라는 internal slot을 갖고 있음. 이는 OOP의 상속을 구현하는 프로토타입 객체를 가리킴

`__proto__`는 이 `[[Prototype]]` 슬롯이 가리키는 프로토 타입 객체에 접근하기 위한 property.

### `prototype` property

`prototype` property는 `constructor`만이 소유하고 있음. 이 property가 가리키는 것은 생성할 `instance`의 프로토타입 객체를 가리킴
