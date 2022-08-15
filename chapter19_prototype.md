# Chapter 19. Prototype

`javascript`는 `imperative`, `functional`, `prototype-based`, `OOP`를 지원하는 멀티 패러다임 언어

#### `Class`
ES6에서 `Class`가 도입됨. 새로운 객체지향 모델은 아니며, 함수이고 `prototype` 패턴의 `syntatic sugar`
`Class`와 `constructor` 모두 인스턴스를 생성하지만 정확하게 동일하진 않음. `Class`가 보다 엄격하며, 추가적인 기능 제공도 있음
단순 `syntatic sugar`보다는 새로운 매커니즘으로 생각하는 것이 좀 더 합당

`javascript`의 거의 모든 것이 객체이며, `primitive type`을 제외한 나머지 데이터들은 모두 객체

## OOP

실세계의 특징을 나타내는 `attribute/property`를 통해 인식, 구별하는 것. 다양한 `property` 중에서 필요한 것만 간추려 표현하는 것을 `abstraction`이라 함

```js
const person = {
  name: 'Hyeongwuk',
  address: 'Ilsan',
};

console.log(person); // { name: 'Hyeongwuk', address: 'Ilsan' }
```

위와 같이  `property`를 통해 여러 값을 하나의 단위로 구성하는 자료구조가 객체

```js
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },

  getPerimeter() {
    return 2 * Math.PI * this.radius;
  },

  getArea() {
    return Math.PI * this.radius ** 2;
  },
};

console.log(circle); // {
  // radius: 5,
  // getDiameter: [Function: getDiameter],
  // getPerimeter: [Function: getPerimeter],
  // getArea: [Function: getArea]
  // }
console.log(circle.getDiameter()); // 10
console.log(circle.getPerimeter()); // 31.41592653589793
console.log(circle.getArea()); // 78.53981633974483
```

위와 같이 `OOP`는 객체의 `state`를 나타내는 값과 `state`를 조작할 수 있는 `behavior`를 하나의 논리 단위로 묶어 생각함. `state`는 `property`이며 `behavior`는 `method`라 칭함.

각 객체는 독립적이면서도 다른 객체와 `relationship`을 가질 수 있음

## Inheritance & Prototype

```js
function Circle(r) {
  this.r = r;
  this.getArea = function () {
    return Math.pi * this.r ** 2;
  };
}

const circle1 = new Circle(1);

const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea); // false
```

위 예시는 문제가 있음. `Circle constructor`는 모든 인스턴스를 생성할 때 마다 중복 생성하게 되고, 인스턴스들도 중복 소유하게 됨. 이는 메모리 낭비로 이어지게 됨

**prototype을 이용한 문제 해결**

```js
function Circle(r) {
  this.r = r;
}

Circle.prototype.getArea = function () {
  return Math.PI * this.r ** 2;
};

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea); // true
```

`Circle constructor`로 생성한 모든 인스턴스는 `Circle.prototype`의 모든 `property`와 `method`를 상속 받음. `getArea`는 하나만 생성되어 할당이 되어있기 때문에 생성되는 인스턴스들은 해당 `method`를 상속받아 사용 가능함.

## Prototype Object

`Prototype`은 객체간 `inheritance`의 구현을 위해 사용됨. 모든 `Object`는 `[[Prototype]]`이라는 `internal slot`을 갖고 있으며, 이 값은 `prototype`의 참조 값. 객체 생성 방식에 의해 결정된 후 저장됨

리터럴에 의해 생성된 `prototype`은 `Object.prototype`이며, `constructor`에 의해 생성된 `prototype`은 `constructor`의 `prototype property`에 바인딩 되어있는 `Object`

모든 `Object`는 하나의 `prototype`을 가지며, 모든 `prototype`은 `constructor`와 연결되어 있음

`[[Prototype]] internal slot`에는 직접 접근은 불가능하고, `__proto__ accessor property`를 통해 간접적으로 접근이 가능함. `prototype`은 `constructor property`를 통해 `constructor`에 접근할 수 있으며, `constructor`는 자신의 `prototype property`를 통해 `prototype`에 접근할 수 있음

### `__proto__` accessor property

```js
const obj = { foo: 'bar' }; // undefined
console.log(obj);
// Object { foo: "bar" }
// foo: "bar"
// <prototype>: Object { … }
//   __defineGetter__: function __defineGetter__()
// 	 __defineSetter__: function __defineSetter__()
//   __lookupGetter__: function __lookupGetter__()
//   __lookupSetter__: function __lookupSetter__()
//   __proto__:
// 		constructor: function Object()
// 		hasOwnProperty: function hasOwnProperty()
// 		isPrototypeOf: function isPrototypeOf()
// 		propertyIsEnumerable: function propertyIsEnumerable()
// 		toLocaleString: function toLocaleString()
// 		toString: function toString()
// 		valueOf: function valueOf()
// 		<get __proto__()>: function __proto__()
// 		<set __proto__()>: function __proto__()
```

`__proto__`	하위 값들이 `person` 객체의 `prototype`. 즉 `[[Prototype]] internal slot`이 가르키고 있는 `Object.prototype` 값

**`__proto__`는 `accessor property`**

`internal slot`은 `property`가 아님. 원칙상 직접 접근, 호출이 불가능함. `accessor property`는 자체적인 `[[Value]]`를 갖지 않고, 다른 `data property`를 읽거나 저장할 때 사용하는 `accessor function`, 즉 `[[Get]], [[Set]] property attribute`로 구성된 `property`

`__proto__`는 `getter/setter`라고 부르는 `accessor function`을 통해 `internal slot`인 `prototype`의 값을 취득하거나 할당함.

```js
const obj = {};
const parent = { x: 1 };

// getter
obj.__proto__;
// settter
obj.__proto__ = parent;

console.log(obj.x); // 1
```

**`__proto__ accessor property`는 상속을 통해 사용됨**

`__proto__`는 객체가 직접 소유하는 `property`가 아닌 `Object.prototype`의 `property`임. 모든 객체는 inheritance를 통해 이 `accessor property`를 사용할 수 있음

```js
const person = { name: 'Hyeongwuk' };

console.log(person.hasOwnProperty('__proto__')); // false

console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
/** {
  get: [Function: get __proto__],
  set: [Function: set __proto__],
  enumerable: false,
  configurable: true
} */

console.log({}.__proto__ === Object.prototype); // true
```

#### Object.prototype
모든 객체는 `prototype`의 계층 구조인 `prototype chain`에 묶여 있음. `javascript` 엔진은 객체의 `property`에 접근하려 할 때 해당 `property`가 존재하지 않을 경우 `__proto__`가 가리키는 참조를 따라 부모 역할을 하는 `prototype`을 순차 검색 실시함. 즉 최상위 객체인 `Object.prototype`의 `property, method`는 모든 객체에 상속됨

**`__proto__`를 통해 `prototype`에 접근하는 이유 **

이 `accessor property`를 통해 접근하는 이유는 상호 참조에 의한 `prototype chain`이 생성되는 것을 방지하기 위함

```js
const parent = {};
const child = {};

child.__proto__ = parent;
parent.__proto__ = child; // TypeError: Cyclic __proto__ value
```

`prototype chain`은 단방향 링크드 리스트로 구현되어야 함. `circular reference`가 만들어 지면, 이 체인의 종점이 없어지기 때문에 무한루프에 빠지게 됨. 따라서 이 체크를 위해서 `__proto__`를 통해 `prototype`에 접근하고 교체하도록 구현됨

**`__proto__`를 코드내 사용하는 것은 권장되지 않음**

`__proto__`는 `ES5`까지는 비표준 사양. 일부 브라우저에서 지원을 하기 때문에 `ES6`에서 표준으로 채택하게 됨. 다만 이를 코드 내에서 직접 사용하는 것은 권장치 않음. 모든 객체가 `__proto__`를 사용할 수는 없기 때문

```js
const obj = Object.create(null);
const parent = { x: 1 };

// 생성된 obj는 체인의 종점이기 때문에, __proto__가 undefined
// 따라서 getPrototypeOf, setPrototypeOf 사용이 적절
console.log(obj.__proto__); // undefined
console.log(Object.getPrototypeOf(obj)); // null
Object.setPrototypeOf(obj, parent);
console.log(obj.x); // 1
```

### 함수 객체의 `prototype`

함수 객체만이 소유하는 `prototype property`는 `constructor`가 생성할 인스턴스의 `prototype`을 가리킴

```js
console.log(function () {}.hasOwnProperty('prototype')); // true

// 일반 객체는 이 property가 존재하지 않음
console.log({}.hasOwnProperty('prototype')); // false
```

또한 `non-constructor`인 `arrow function`, `method shorthand`로 정의한 메소드는 `prototype property`를 소유하지 않으며 `prototype` 또한 존재 하지 않음

```js
const Person = (name) => {
  this.name = name;
};

console.log(Person.hasOwnProperty('prototype')); // false

console.log(Person.prototype); // undefined

const obj = {
  foo() {},
};

console.log(obj.foo.hasOwnProperty('prototype')); // false

console.log(obj.foo.prototype); // undefined
```

인스턴스를 생성하지 않는 일반 함수의 `prototype` 또한 아무런 의미가 없음

모든 객체가 갖고 있는 `__proto__`와 함수 객체만이 갖는 `prototype property`는 결국 동일한 `prototype`을 가리키고 있음. 단 사용하는 주체가 다름

1. `__proto__ accessor property`
	1. 모든 객체가 소유
	2. 프로토타입 참조
	3. 모든 객체가 사용 주체
	4. 객체가 자신의 프로토타입의 접근, 교체를 위해 사용
2. `prototype property`
	1. `constructor`가 소유
	2. 프로토타입 참조
	3. `constructor`가 사용 주체
	4. `constructor`가 생성할 인스턴스의 `prototype`을 할당하기 위해 사용

### `prototype`의 `constructor property`와 `constructor function`

```js
function Person(name) {
  this.name = name;
}

const person = new Person('Hyeongwuk');

console.log(person.constructor === Person); // true
```

생성된 인스턴스에는 `constructor`가 존재하지 않지만, `Person`을 상속받아 해당 `property`를 사용할 수 있음

## 리터럴 객체의 `constructor function`과 `prototype`

인스턴스는 `prototype`의 `constructor property`에 의해 `constructor function`과 연결됨

```js
const obj = new Object();
console.log(obj.constructor === Object); // true

const add = new Function('a', 'b', 'return a + b');
console.log(add.constructor === Function); // true

function Person(name) {
  this.name = name;
}

const person = new Person('Hyeongwuk');
console.log(person.constructor === Person); // true
```

위와 달리 리터럴로 생성한 객체는 `prototype`이 존재하지만, `constructor`가 가리키는 생성자 함수가 반드시 객체를 생성한 생성자 함수라고 단정지을 수는 없음

```js
const obj = {};
console.log(obj.constructor === Object); // true
```

위와 같이 리터럴로 생성했으나, `constructor function`은 `Object`임
`ECMAScript`에 의하면 `Object`에 인수 전달이 없거나 `undefined`,`null`을 전달하면 내부적으로 추상 연산 `OrdinaryObjectCreate`를 호출하여, `Object.prototype`을 `prototype`으로 갖는 객체를 생성함

#### 추상연산(`abstract operation`)
추상 연산은 내부 동작의 구현알고리즘을 표현. `ECMAScript`에서 설명을 위한 의사코드라고 이해

```js
// 인수 미전달 시 OrdinaryObjectCreate를 호출하여 빈 객체 생성
let obj = new Object();
console.log(obj); // {}

// new.target이 undefined이거나 Object가 아닌 경우
// instance -> Foo.prototype -> Object.prototype 순으로 되어있는 체인 생성
class Foo extends Object {}
new Foo();

// 인수 전달 시 인수를 객체로 생성
obj = new Object(123);
console.log(obj); // [Number: 123]

obj = new Object('123');
console.log(obj); // [String: '123']
```

이를 기반으로 객체 리터럴이 평가될 때는 `OrdinaryObjectCreate`를 이용하여 빈 객체 생성 및 `property`를 추가. 즉, `Object constructor function`과 리터럴의 평가는 `OrdinaryObjectCreate`를 이용한 점은 동일하나 `new.target` 확인, `property` 추가 등 세부 내용이 다름. 따라서 **리터럴로 생성한 객체는 `Object constructor function`로 생성한 객체가 아님**

함수 객체의 경우 `Function constructor function`으로 생성한 함수는 `lexical scope`를 만들지 않고 전역 함수인 것처럼 스코프가 생성되며, `Closure`또한 생성하지 않음. 즉, `expression`,`declaration`을 평가하여 함수 객체를 생성한 것은 `Function constructor function`이 아님. 하지만 `constructor property`를 통해 확인해보면 해당 함수의 `constructor function`은 `Function constructor function`임

```js
function foo() {}

console.log(foo.constructor === Function); // true
```

리터럴로 생성한 객체 또한 상속을 위한 `prototype`이 필요함. 그 이유로 가상의 `constructor function`을 갖게 됨. `prototype`은 `constructor function`과 더불어 생성되며, `prototype.constructor`와 연결됨. 즉, **`prototype`과 `constructor function`은 단독 존재가 불가능하며, 언제나 쌍으로 존재함**

리터럴에 의해 생성된 객체는 `constructor function`으로 생성된 객체는 아니지만, 본질적인 면에서 큰 차이는 없음. 따라서 `prototype`의 `constructor property`를 통해 연결되어 있는 `constructor function`을 리터럴로 생성한 객체를 생성한 `constructor function`으로 생각해도 됨

1. 객체 리터럴 - `Object`, `Object.prototype`
2. 함수 리터럴 - `Function`, `Function.prototype`
3. 배열 리터럴 - `Array`, `Array.prototype`
4. 정규 표현식 리터럴 - `RegExp`, `RegExp.prototype`

## Prototype 생성 시점

리터럴 객체 또한 `constructor function`과 연결됨을 확인했듯, 모든 객체는 결국 `constructor function`과 연결됨

#### `Object.create` 메소드와 클래스에 의한 객체 생성
위 두가지 방법 또한 `constructor function`과 연결되어 있음

`prototype`은 `constructor function`이 생성되는 시점에 더불어 생성됨. `constructor function`은 사용자 정의 함수와, 빌트인 함수로 구분할 수 있음

### 사용자 정의 함수와 `prototype` 생성 시점

```js
console.log(Person1.prototype); // { constructor: function Person1(name) }

function Person1(name) {
  this.name = name;
}

const Person2 = (name) => {
  this.name = name;
};

console.log(Person2.prototype); // undefined
```

`declaration`은 런타임 이전 실행되므로, 해당 `constructor function`은 먼저 평가되어 함수 객체가 됨. 이 때 `prototype`도 같이 생성됨. 생성된 `prototype`은 `Person constructor function`의 `prototype property`에 바인딩 됨.

즉 사용자 정의 함수는 자신이 평가되어 함수 객체가 생성되는 시점에 `prototype`도 생성되며 그 `prototype`의 `prototype`은 `Object.prototype`

### 빌트인 함수와 `prototype` 생성 시점

모든 빌트인 함수는 전역 객체가 생성되는 시점에 생성됨.

위 두 가지에서 본 것 처럼 객체가 생성되기 이전 `constructor function`과 `prototype`은 이미 객체화가 되어 존재함. 이후 객체가 생성되면 `prototype`은 생성된 객체의 `[[Prototype]] internal slot`에 할당되게 됨. 이를 통해 생성된 객체는 `prototype`을 상속 받음

## 객체 생성 방식과 `prototype`의 결정

각 생성 방식 별 차이는 있으나 추상연산 `OrdinaryObjectCreate`에 의해 생성된다는 공통점이 있음. 이 연산은 필수적으로 생성할 객체의 `prototype`을 인수로 전달 받음. 또한 생성할 객체에 추가할 `property` 목록을 옵션으로 전달 가능함. 빈 객체를 먼저 생성한 후 `property` 목록이 있는 경우 이를 객체에 추가함. 그 후 전달받은 `prototype`을 `[[Prototype]] internal slot`에 할당 후, 생성한 객체를 반환함. 즉 `prototype`은 이 추상연산에 전달되는 인수에 의해 결정 되며, 이 인수는 객체가 생성되는 시점의 생성 방식에 의해 결정 됨

### 리터럴에 의해 생성된 `prototype`

리터럴로 생성할 때 넘어가는 `prototype` 인수는 `Object.prototype`

### `Object constructor function`에 의해 생성된 `prototype`

이 케이스에도 인수는 `Object.prototype`

### 사용자 정의 함수에 의해 생성된 `prototype`

이 때 인수는 사용자 정의 생성함수의 `prototype property`에 바인딩 되어 있는 객체

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hello, I'm ${this.name}`);
};

const person = new Person('Hyeongwuk');
person.sayHello(); // Hello, I'm Hyeongwuk
```

위의 경우 즉, `Person.prototype`에 바인딩 되어 있는 객체가 인수로 전달됨. 따라서 `Person.prototype`이 갖고 있는 메소드를 생성된 객체들이 사용할 수 있게 됨

## Prototype Chain

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hello, I'm ${this.name}`);
};

const person = new Person('Hyeongwuk');
console.log(person.hasOwnProperty('name')); // true
```

`hasOwnProperty`는 `Object.prototype`의 메소드이다. 이를 사용할 수 있다는 것은 해당 프로토타입도 상속 받았음을 의미한다. `prototype`의 `prototype`은 언제나 `Object.prototype`이다. 또한 정의되지 않는 프로퍼티에 접근할 경우 상위 객체로 접근하는 것을 `prototype chain`이라 함. 그 과정은 아래와 같다

1. `hasOwnProperty`를 호출한 `person`객체의 메소드를 검색함. 존재하지 않을 경우 `[[Prototype]] internal slot`에 바인딩되어있는 `prototype`으로 이동하여 검색함. 이 경우 `Person.prototype`을 검색
2. `Person.prototype`에도 해당 메소드가 존재하지 않기 때문에 다시 `[[Prototype]] internal slot`에 바인딩되어있는 `prototype`으로 이동하여 검색함. 이는 `Object.prototype`
3. `Object.prototype`의 `hasOwnProperty`를 호출하게 됨. 이 때 `this`에는 `person` 객체가 바인딩 됨
	1. `Object.prototype.hasOwnProperty.call(me, 'name')`

이 체인 과정을 통해서도 확인이 되지 않는 `property`라면 `undefined`을 반환하며, 별도의 에러는 발생하지 않음. 식별자는 `scope chain`에서 검색을 실시함.

```js
person.hasOwnProperty('name');
```

위 코드의 경우 일단은 먼저 `scope chain`에서 `person` 식별자를 검색. 그 후 `prototype chain`에서 해당 메소드를 검색하게 됨.

## Override & Property Shadowing

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype.sayHello = function () {
    console.log(`Hello, I'm ${this.name}`);
  };

  return Person;
})();

const person = new Person('Hyeongwuk');

person.sayHello = function () {
  console.log('Method override');
};

person.sayHello(); // Method override
```

`prototype property`와 같은 이름을 인스턴스에 추가하게 되면 `prototype property`를 덮어 씌우는게 아닌 `instance property`로 추가하게 됨. 즉 `person.sayHello`는 오버라이딩을 했고 이로 인해 `Person.sayHello`는 가려지게 됨(`property shadowing`)

#### Overloading
함수 이름은 동일하지만 parameter의 타입 혹은 개수를 달리하여, parameter에 의한 메소드 구별 방식. `javascript`가 지원하진 않지만 `arguments` 객체를 이용해 구현 가능함

```js
delete person.sayHello;

person.sayHello(); // Hello, I'm Hyeongwuk
```

인스턴스 메소드를 삭제하게 되면, `prototype method`가 호출되게 됨. `prototype chain`을 이용해 삭제하려 할 시 삭제, 변경 불가능함. 즉, 하위 객체는 상위 객체에 `set`를 허용하지는 않음.

변경,삭제를 하려면 `prototype chain`를 통하지 않고 직접 `prototype`에 접근해야 함

```js
delete Person.prototype.sayHello;

person.sayHello(); // TypeError: person.sayHello is not a function
```

## Replace Prototype

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype = {
    sayHello() {
      console.log(`Hello, I'm ${this.name}`);
    },
  };

  return Person;
})();

const person = new Person('Hyeongwuk');

console.log(person.constructor === Person); // false
console.log(person.constructor === Object); // true
```

위 코드와 같이 `prototype`을 교체하게 되면 `constructor property`와 `constructor function`의 연결이 파괴됨.

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype = {
    constructor: Person,
    sayHello() {
      console.log(`Hello, I'm ${this.name}`);
    },
  };

  return Person;
})();

const person = new Person('Hyeongwuk');

console.log(person.constructor === Person); // true
console.log(person.constructor === Object); // false
```

따라서 위와 같이 `prototype`에 할당될 객체 리터럴에 `constructor`를 해당 `constructor function`을 할당하면 다시 연결됨

### 인스턴스에 의한 `prototype` 교체

인스턴스의 `__proto__ accessor property`, `Object.getPrototypeOf` 등을 통하면 `prototype`에 접근 가능함. 즉, 인스턴스도 `prototype`교체가 가능함

```js
function Person(name) {
  this.name = name;
}

const person = new Person('Hyeongwuk');

const parent = {
  sayHello() {
    console.log(`Hello, I'm ${this.name}`);
  },
};

Object.setPrototypeOf(person, parent);
// person.__proto__ = parent;

person.sayHello(); // Hello, I'm Hyeongwuk
```

위의 경우도 `constructor` 연결이 파괴됨

## `instanceof` 연산자

좌변은 객체 식별자, 우변은 `constructor function`을 받음. 위 조건이 아닌 경우 `TypeError` 발생

우변의 `prototype`에 바인딩된 객체가 좌변의 `prototype chain`에 존재하면 `true`, 아니면 `false`

```js
function Person(name) {
  this.name = name;
}

const person = new Person('foo');

console.log(person instanceof Person); // true

console.log(person instanceof Object); // true
```

앞서본 프로토타입 교체를 실시하면 아래와 같이 동작

```js
function Person(name) {
  this.name = name;
}

const person = new Person('foo');

const parent = {};

Object.setPrototypeOf(person, parent);

console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false
console.log(person instanceof Person); // false
console.log(person instanceof Object); // true
```

`person.constructor`와 `Person`간의 연결이 해제되면서 `person`의 `prototype chain`에는 `Person`이 존재하지 않게 됨. 따라서 해당 평가 값은 `false`

```js
function Person(name) {
  this.name = name;
}

const person = new Person('foo');

const parent = {};

Object.setPrototypeOf(person, parent);

console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

Person.prototype = parent;

console.log(person instanceof Person); // true
console.log(person instanceof Object); // true
```

`Person.prototype`을 `parent`로 교체하면 결과값이 바뀌게 됨.

즉 `instanceof`는 `constructor property`가 가리키는 `Constructor Function`이 아닌, `Constructor Function`의 `prototype`에 바인딩 된 객체가 `prototype chain`에 존재하는지를 확인하는 것

`instanceof`를 함수로 표현하면 아래와 같음

```js
function isInstanceOf(instance, constructor) {
  const prototype = Object.getPrototypeOf(instance);

  if (prototype === null) return false;

  return (
    prototype === constructor.prototype || isInstanceOf(prototype, constructor)
  );
}
```

즉 `contructor property`와 `Constructor Function`의 연결이 파괴되어도, `ConstructorFunction.prototype`과 `prototype`간의 연결이 파괴되지 않기 때문에 영향을 받지 않을 수 있음

## 직접 상속

### `Object.create`에 의한 직접 상속

`Object.create`는 명시적인 `prototype`을 지정하여 새로운 객체 생성

첫 번째 parameter는 `prototype`으로 지정할 객체, 두 번째 `parameter`는 `property key`, `property descriptor`로 이루어진 객체를 전달.

```js
// obj -> null
// Object.prototype을 상속받지 못함
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null);
console.log(obj.toString()); // TypeError: obj.toString is not a function

// obj -> Object.prototype -> null
// obj = {};
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj -> Object.prototype -> null
// obj = Object.create(Object.prototype); obj.x = 1;
// obj = { x: 1 };
obj = Object.create(Object.prototype, {
    x: { value: 1, writable: true, enumerable: true, configurable: true },
});
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj -> customProto -> Object.prototype
const customProto = { x: 10 };
obj = Object.create(customProto);
console.log(obj.x);
console.log(Object.getPrototypeOf(obj) === customProto); // true

// Constructor Function
// obj -> Person.prototype -> Object.prototype
function Person(name) {
    this.name = name;
}
obj = Object.create(Person.prototype);
obj.name = 'Hyeongwuk';
console.log(obj.name); // Hyeongwuk
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

`Object.create`의 장점은 아래와 같음
1. `new` 연산자가 없이도 객체 생성 가능
2. `prototype`을 지정하면서 객체 생성 가능
3. 객체 리터럴에 의해 생성된 객체 상속도 가능

`ESlint`에서는 `Object.prototype`의 `built-in` 메소드 사용을 직접 호출하는 것을 권장하지 않음. `Object.create`를 통해 체인의 종점에 위치하는 객체 생성이 가능하기 때문임. 따라서 사용이 필요한 경우 간접호출하는 것이 좋음

```js
const obj = Object.create(null);
obj.a = 1;

console.log(Object.prototype.hasOwnProperty.call(obj, 'a')); // true
```

### 리터럴 내부에서 `__proto__`를 이용한 직접 상속

```js
const customProto = { x: 10 };

const obj = {
    y: 20,
    __proto__: customProto,
};

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === customProto); // true
```

## Static Property/Method

`static property/method`란 인스턴스 생성 없이 호출할 수 있는 데이터를 의미

```js
// constructor function
function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function () {
    console.log(`Hello, I'm ${this.name}`);
};

Person.staticProp = 'static prop';

Person.staticMethod = function () {
    console.log('staticMethod');
};

const person = new Person('hyeongwuk');

Person.staticMethod(); // staticMethod

person.staticMethod(); // TypeError: person.staticMethod is not a function
```

이는 생성된 인스턴스로는 참조/호출이 불가능함. `Constructor Function`으로 생성한 인스턴스는 자신의 `prototype chain`에 속한 객체의 `property/method` 접근이 가능함. 하지만 `static property/method`는 해당 객체의 `property/method`가 아니기 때문에 접근이 불가능함

앞 서 살펴본 `Object.create` 또한 `Object.prototype`의 메소드가 아닌 `Object`의 `static method`이기 때문에 인스턴스에서는 호출할 수 없는 것

일부 표기법에서는 `prototype`을 `#`으로 표기하는 경우가 존재함. `Object#isPrototypeOf`로 표기하는 것과 같은 경우

## Property 존재 확인

### `in` 연산자

`key in object`와 같은 형태로 사용
`in` 연산자가 `object`객체가 속한 `prototype chain`상에 존재하는 모든 `prototype`에서 검색함. 대체재로는 `ES6`에서 도입된 `Reflect.has` 메소드가 존재함

### `Object.prototype.hasOwnProperty`

인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키일 경우만 `true`를 반환함

## Property 열거

### `for ... in`

`for (변수 선언문 in 객체) {...}`와 같은 형태로 사용함. 객체의 모든 `property`를 순회하여 열거함. 또한 상속받은 `prototype`의 `property`들 또한 열거함.
하지만 `toString`과 같은 `Object.prototype`의 `property`들은 열거되지 않는데 이 이유가 전에 서술했던 것과 같이 `[[Enumarable]]`이 `false`이기 때문임

**정리하면 상속받는 `prototype`을 포함 모든 property중 `Enumerable`의 값이 `true`인 `property`들을 열거함**

한 가지 더 예외 케이스는 키 값이 `Symbol`인 경우도 열거하지 않음

### `Object.keys/values/entries`

객체 고유의 프로퍼티만을 열거하기 위해선 위 메소드들을 사용하는 것이 권장됨