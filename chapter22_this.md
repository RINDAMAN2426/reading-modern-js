# Chapter 22. This

## `this` keyword

`object`는 `state`와 `behavior`를 하나의 논리 단위로 묶은 자료구조
이 때 `method(behavior)`가 자신이 속한 객체의 `property(state)`를 참조하기 위해선 자기 자신을 가리키는 식별자를 참조할 수 있어야 함

객체 리터럴의 경우에는 아래 코드처럼 재귀적으로 참조가 가능함

const circle = {
    radius: 5,
    getDiameter() {
        return 2 * circle.radius;
    },
};

console.log(circle.getDiameter()); // 10

`getDiameter` 메소드 내의 식별자 `circle`의 평가 시점은 호출이 되고 함수 바디가 실행이 되는 시점. 즉 `circle` 변수에 위 리터럴 표현식ㅛ이 평가되어 할당이 되고 난 이후이기 때문에 메소드 내부에서 참조가 가능함. 다만 이런 재귀 방식은 일반적이지도 않고 바람직하지도 않음

`Constructor Function`의 경우에는 함수 정의 이후, 인스턴스 생성을 위한 호출이 필요. 즉 인스턴스가 존재하기 위해선 `Constructor Function`이 먼저 존재해야 함.

`Contructor Function`의 정의 시점에서는 인스턴스를 가리킬 수 없기 때문에 특수한 식별자가 필요, 그로 인해 `this`라는 식별자를 제공함

`this` 식별자는 `self-referencing variable`. `this`를 통해 자신이 속한 객체, 인스턴스의 `property`나 `method`를 참조할 수 있음. 함수를 호출 시 `arguments`와 `this`가 암묵적으로 함수 바디에 전달됨. `this binding`은 함수 호출 방식에 의해 동적으로 결정 됨

const circle = {
    radius: 5,
    getDiameter() {
        return 2 * this.radius;
    },
};

console.log(circle.getDiameter()); // 10

위 코드의 `this`는 `this`를 호출한 객체를 가리킴

function Circle(r) {
    this.r = r;
}

Circle.prototype.getDiameter = function () {
    return 2 * this.r;
};

const circle = new Circle(5);
console.log(circle.getDiameter()); // 10

위 코드의 `this`는 생성된 인스턴스 객체를 가리킴

`Java`, `C++`과 같은 `class` 기반 언어는 언제나 생성된 인스턴스를 가리킴. `javascript`의 경우에는 `this`가 해당 함수가 호출 되는 방식에 따라 `this binding`이 동적으로 결정되며 `this`는 코드 어디에서나 참조가 가능함

console.log(this); // window

function square(n) {
    console.log(this); // window
    return n * n;
}

square(2); // 4

const person = {
    name: 'Hyeongwuk',
    getName() {
        console.log(this); // person
        return this.name;
    },
};

console.log(person.getName()); // Hyeongwuk

function Person(name) {
    this.name = name;
    console.log(this); // Person
}

const hyeongwuk = new Person('Hyeongwuk');

`strict mode`의 경우 일반 함수 바디의 `this`는 사용성이 없는 식별자이기 때문에 `undefined`가 할당됨

## 함수 호출 방식과 `this` 바인딩

**`lexical scope`와 `this binding`의 결정 시기는 다르다**
>함수의 상위 스코프를 결정하는 `lexical scope`는 함수 정의가 `evaluate` 되고 함수 객체가 생성되는 시점에 상위 스코프를 결정함. `this`는 함수 호출 시점에 결정됨

함수 호출 방식은 아래와 같다.
1. 일반 함수 호출
2. 메소드 호출
3. 생성자 함수 호출
4. `Function.prototype.apply/call/bind` 메소드에 의한 간접 호출

const foo = function () {
    console.dir(this);
};

// 일반 함수 호출
foo(); // window

const obj = { foo };

// 메소드 호출
obj.foo(); // obj

// 생성자 함수 호출
new foo(); // foo {}

// Function.prototype
const bar = { name: 'bar' };

foo.call(bar); // bar
foo.apply(bar); // bar
foo.bind(bar); // bar

### 일반 함수 호출

`this`에는 `global object`가 바인딩 됨

중첩 함수를 일반 함수로 호출해도 함수 바디 내부의 `this`에는 전역 객체가 바인딩 됨. 일반 함수는 인스턴스를 생성하지 않기 때문에 `this`의 의미가 없음

또한 메소드 내에서 정의한 중첩함수도 일반 함수로 호출되면 전역 객체가 바인딩 됨

// global object property
var value = 1;

const obj = {
    value: 100,
    foo() {
        console.log("foo's this: ", this);
        console.log("foo's this.value: ", this.value);

        // nested function
        function bar() {
            console.log("bar's this: ", this);
            console.log("bar's this.value: ", this.value);
        }

        bar();
        // bar's this: Object[global]
        // bar's this.value: undefined
    },
};

obj.foo();
// foo's this: obj
// foo's this.value: 100

콜백 함수가 일반 함수로 호출 된다면 콜백 함수 내부 `this`에도 전역 객체가 바인딩 됨.
**어떤 함수라도 일반 함수로 호출되면 `this`에는 전역 객체가 바인딩 됨**

다만 콜백함수, 중첩함수와 외부함수의 `this`가 일치하지 않으면 동작 예측에 있어서 어려움이 존재함. 따라서 중첩 함수, 콜백함수의 `this` 바인딩을 외부함수와 일치시키기 위한 방법은 아래와 같음

// global object property
var value = 1;

const obj = {
    value: 100,
    foo() {
        const _this = this;
        setTimeout(function () {
            console.log('callback this.value: ', _this.value);
        }, 100);
    },
};

obj.foo(); // callback this.value:  100

이 외에도 `Function.prototype.apply,call,bind` 메소드를 활용할 수도 있음.

좀 더 간편한 방법은 `ES6`의 `arrow function` 활용

// global object property
var value = 1;

const obj = {
    value: 100,
    foo() {
        setTimeout(() => {
            console.log('callback this.value: ', this.value);
        }, 100);
    },
};

obj.foo(); // callback this.value:  100

### 메소드 호출

메소드 내부의 `this`는 메소드를 호출한 객체가 `this`에 바인딩 됨. 메소드를 소유한 객체가 아닌, 메소드를 호출한 객체라는 것에 주의

const obj = {
    value: 'foo',
    getValue() {
        return this.value;
    },
};

console.log(obj.getValue()); // foo

`getValue`는 `obj`의 메소드. 메소드는 프로퍼티에 바인딩 된 함수이다. 따라서 `getValue` 프로퍼티가 가리키는 객체는 `obj` 객체에 포함된 것이 아니라독립적으로 존재하는데 `getValue` 프로퍼티가 `obj` 객체를 가리키고 있을 뿐.

const foo = {
    value: 'bar',
    getValue() {
        return this.value;
    },
};

const baz = {
    value: 'quz',
};

// getValue가 가리키는 객체 변경
baz.getValue = foo.getValue;
console.log(baz.getValue()); // 'quz'

// 일반 함수로써 호출
const getValue = foo.getValue;
console.log(getValue()); // ''

`prototype method` 내부에서 사용되는 `this`도 위와 마찬가지

### 생성자 함수 호출

function Circle(r) {
    this.r = r;
    this.getDiameter = function () {
        return 2 * this.r;
    };
}

const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20

생성자 함수 내부의 `this`는 생성할 인스턴스가 바인딩 됨

### `Function.prototype.apply/call/bind`

function getThisBinding() {
    return this;
}

const obj = { foo: 'bar' };

console.log(getThisBinding()); // Object [global]

console.log(getThisBinding.apply(obj)); // { foo: 'bar' }
console.log(getThisBinding.call(obj)); // { foo: 'bar' }

`bind` 메소드의 경우에는 함수 호출을 하지 않기 때문에 다시 호출해주어야 함. 중첩 함수 혹은 콜백 함수의 `this binding` 이슈 해결을 위해 유용하게 사용됨

getThisBinding.bind(obj)();
