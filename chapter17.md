# Chapter 17. 생성자 함수에 의한 객체 생성

## Object 생성자 함수

```js
const person = new Object();

person.name = 'Hyeongwuk';
person.sayHello = function () {
    console.log('Hello ' + this.name);
};

console.log(person); // { name: 'Hyeongwuk', sayHello: [Function (anonymous)] }
person.sayHello(); // Hello Hyeongwuk
```

`constructor`를 통해 생성한 객체를 `instance`라고 지칭
`Object` 외에도 `String`,`Number`,`Boolean`,`Function`,`Array`,`Date`,`RegExp`,`Promise`와 같은 built-in 생성자 함수들이 있음

객체 생성은 반드시 `contructor`를 통해 생성할 필요는 없으며, 리터럴이 더 간편함

## 생성자 함수

### 객체 리터럴에 의한 생성 방식의 문제점

리터럴은 직관적이며 간편함. 단, 하나의 객체만을 생성하기 때문에 동일한 property를 갖는 객체를 여러개 생성할 경우 같은 property를 기술해야함

객체는 property를 통해 고유의 state를 표현하며, 메소드를 통해 behavior를 표현함. property가 다르더라도 behavior가 같은 경우가 많은데 이 경우 리터럴 생성 방식은 같은 property를 기술해야함

### 생성자 함수에 의한 생성 방식의 장점

위 서술 내용과 같이 `constructor`를 활용하면 간편하게 생성할 수 있음

```js
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getDiameter()); // 2
console.log(circle2.getDiameter()); // 4
```

#### this
this는 객체 자신의 property 혹은 method를 참조하기 위한 self-referencing variable. `this` 바인딩은 함수 호출 방식에 따라 동적으로 결정 됨

1. 일반 함수로서 호출 - 전역 객체
2. method로서 호출 - method를 호출한 객체(마침표 앞의 객체)
3. 생성자 함수로서 호출 - 생성자 함수가 생성할 인스턴스

```js
// 일반함수
function foo() {
    console.log(this);
}
foo(); // window

// method
const obj = { foo };
obj.foo(); // { foo: f}

// constructor
const inst = new foo(); // foo {}
```

`new`와 함께 함수를 호출하면 `constructor`로 동작함. 그렇지 않으면 일반 함수로 동작함

### 생성자 함수의 인스턴스 생성 과정

`constructor`의 역할은 instance 생성을 위한 template(class) 로써 동작하여 instance 생성 및 생성된 instance를 초기화 수행. 생성은 필수며, 초기화는 옵션임

```js
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

const circle1 = new Circle(5);
```

instance를 생성하고 반환하는 코드는 보이지 않는데, 해당 수행은 엔진에서 암묵적인 처리를 통해 생성 및 반환함. `new`와 함께 호출하면, 인스턴스 생성 및 초기화를 한 후 반환을 수행함

**인스턴스 생성 및 this 바인딩**

암묵적으로 빈 객체를 생성(constructor가 생성한 instance). 이 빈 객체는 this에 바인딩이 됨. 따라서 constructor 내부 this가 해당 instance를 참조하게 되는 것. 이 수행 과정은 런타임 이전에 수행됨

`바인딩이란 해당 식별자와 값을 연결하는 과정. 즉 this에 instance를 바인딩한다는 뜻은 this가 가리키는 객체가 instance가 된다는 것`

```js
function Circle(radius) {
    console.log(this); // Circle {}

    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

const circle1 = new Circle(5);
```

**instance 초기화**

`constructor` 내부에 기술된 코드가 한 줄씩 실행되며, this에 바인딩되어있는 instance를 초기화함. 이 과정은 개발자가 기술한 대로 수행하게 됨

```js
function Circle(radius) {
    console.log(this); // 위 예시처럼 이 시점에 this에는 Circle이 바인딩되어 있음

    // 아래 코드부터 this에 바인딩된 instance(Circle)에 대한 초기화를 수행함
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}
```

**instance 반환**

```js
function Circle(radius) {
    // binding & initializing
    // ...

    // 이 시점에서 바인딩 및 초기화가 끝난 this를 암묵적으로 반환함
}
```

만일 `return {}`과 같이 내부 코드에서 반환값이 지정되어 있다면 this 반환은 무시하게 됨. 단 primitive value를 반환하게 되면 해당 반환이 무시되고 this를 반환하게 됨

### 내부 method `[[Call]]`과 `[[Consturct]]`

함수 선언문, 표현식으로 정의한 함수는 일반적인 함수로서의 호출 및 constructor로서 호출이 가능.
함수는 객체이므로 `ordinary object`와 동일하게 동작 가능. `ordinary object`가 가진 `internal slot, method`를 모두 가지고 있음

```js
function foo() {}

foo.prop = 1;

foo.method = function () {
    console.log(foo.prop);
};

foo.method(); // 1
```

`ordinary object`와 다른 점은 함수는 호출이 가능함. 함수로서 동작을 위한 `[[Environment]]`, `[[FormalParameters]]` 와 같은 internal slot과 `[[Call]]`, `[[Construct]]` 같은 internal method도 가지고 있음

일반함수로서 호출은 `[[Call]]`이 호출, constructor로서 호출은 `[[Construct]]`가 호출 됨

`[[Call]]` method를 갖는 함수는 `callable`이라 지칭, `[[Construct]]`를 갖게되면 `constructor`, 갖지 않는다면 `non-constructor`라고 부름.

즉, 모든 함수 객체는 `callable`이지만 `constructor`는 아님

### `constructor` vs `non-constructor`

엔진의 평가 기준은 아래와 같음
- **`constructor`**: declaration, expression, class
- **`non-constructor`**: method(ES6 method shorthand), arrow function

**첨언**
> 위와 같은 스펙으로 인해 arrow function의 this binding이 일반함수와는 다른 이유가 되는 것 같음

주의할 점은 ECMAScript의 method 인정 범위는 일반적인 의미의 method보다 좁음

```js
// declaration
function foo() {}

// expression
const bar = function () {};

// 일반 함수로 정의된 x의 값은 method로 인정되지 않음
const baz = {
    x: function () {},
};

// 일반 함수로 정의된 함수는 constructor
new foo();
new bar();
new baz.x();

const arrow = () => {}; // TypeError: arrow is not a constructor

new arrow();

// method: ES6의 method shorthand만 method로 인정함
// 따라서 아래 x는 method, non-constructor
const obj = {
    x() {},
};

new obj.x(); // TypeError: obj.x is not a constructor
```

일반적인 method란, 함수를 property의 값으로 사용했을 경우를 통칭함. ECMAScript에서의 method란 ES6 method shorthand 만을 허용함. 즉 함수 정의 방식에 따라서 `constructor`와 `non-constructor`가 구분됨

### new 연산자

`new`와 함께 호출하게되면 `[[Construct]]`가 호출됨. 따라서 `non-constructor`는 해당 내부 메소드가 없으므로 에러가 발생하게 되는 것

반대로 `new` 연산자 없이 `constructor`를 호출하게 되면 일반 함수로 호출, 반환값이 `undefined`가 됨

#### Quiz
```js
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

const circle = Circle(5);

console.log(circle); // ?
console.log(radius); // ?
console.log(getDiameter()); // ?

circle.getDiameter(); // ?

```

### new.target

`new` 생성자 없이 호출되는 것을 방지하기 위해 ES6에서는 `new.target`을 지원함 (IE 제외)

해당 변수는 함수가 `new`와 함께 constructor로서 호출되었는지를 확인이 가능함. 일반함수의 경우 `undefined` 값임. 따라서 해당 케이스에 대한 fallback 처리가 가능해짐

IE 혹은 ES6 이전이라면 `scope-safe cunstructor pattern`을 활용하면 가능함. 함수 코드 내 `this`가 해당 instance인지 체크하면 가능함

대부분의 built-in 생성자 함수들은 `new`와 호출되었는지를 체크하는 로직이 반영되어 있음. 단 `String`, `Number`, `Boolean` 세 가지는 `new`와 함께 생성하면 객체를 생성하지만 그렇지 않으면 primitive value를 반환함(데이터 타입 변환에 활용 가능)