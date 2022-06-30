# Chapter 12. 함수

## 함수란

```js
function add(x, y) {
  return x + y;
}

add(2, 5); // 7;
```

- `x,y`는 parameter, `2,5`는 arguments, `x+y`는 return value
- `function add()`는 definition, `add(2,5)`는 function call/invoke

## 함수 사용의 이유

코드 재사용, 이는 곧 유지보수의 편의성과 코드의 신뢰성을 높이는 효과
적절한 함수명은 코드의 가독성도 향상

## 함수 리터럴

javascript의 함수는 객체 타입의 값. 따라서 함수 리터럴로 생성이 가능함

```js
var f = function add(x, y) {
  return x + y;
};

f(2, 5); // 7
```

- 함수이름
  - 식별자 네이밍 규칙 준수
  - 몸체 내에서만 참조하는 식별자
  - 생략 가능. 이름이 있으면 `named function`, 없으면 `anonymous function`
- 매개변수
  - 0개이상의 매개변수, 소괄호로 감싸며 쉼표로 구분
  - 함수 호출 시 순서대로 할당, 순서에 의미가 존재
  - 몸체내에서 변수와 동일하게 취급. 따라서 네이밍 규칙 준수
- 몸체 - 함수 호출 시 일괄적으로 실행될 하나의 실행 단위 - 호출에 의해 실행됨
  함수는 객체이지만 일반 객체와는 다르게 호출이 가능함. 또한 함수 객체만의 고유한 property가 존재함

## 함수 정의

- 함수 선언문: `function add(x,y){...}`
- 함수 표현식: `var add = function(x,y){...}`
- Function 생성자 함수: `var add = new Function('x','y','return x+y')`
- 화살표 함수(ES6): `var add = (x,y) => x+y`

#### 변수 선언과 함수 정의

변수는 선언, 함수는 정의라고 표현. 함수 선언문이 평가되면 식별자가 암묵적으로 생성되고 함수 객체가 항당됨.

### 함수 선언문

```js
function add(x, y) {
  return x + y;
}

// 함수 참조
// console.dir는 함수 객체의 프로퍼티까지 출력
// 단 node.js에서는 일반 console.log와 동일하게 출력
console.dir(add); //function add(x, y)
                    ​// arguments: null
                    // caller: null
                    ​// length: 2
                    ​// name: "add"
                    ​// prototype: Object { … }
                    ​// <prototype>: function ()
// 함수 호출
console.log(add(2, 5)); // 7
```

- 함수 선언문은 함수 리터럴과 형태가 동일. 함수 리터럴은 이름 생략이 가능하나 선언문은 이름 생략 불가능
- 함수 선언문은 표현식이 아닌 문. 따라서 변수에 할당을 할 수 없음

```js
var add = function add(x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

- 위의 코드가 동작할 수 있는 이유는 javascript 엔진이 문맥에 따라서 리터럴로 해석하는 경우와 함수 선언문으로 해석하는 경우가 있기 때문

함수 리터럴 표현식으로 생성된 함수이름은 호출 할 수 없음
Javascript engine은 함수 선언문을 해석해서 함수 객체를 생성. 이 때 함수 이름은 몸체 내부에서만 유효한 식별자이므로 이름과 별도로 생성된 함수 객체를 가리키는 식별자가 필요함. 함수 객체를 참조하기 위해서 엔진은 함수 이름과 동일한 식별자를 암묵적으로 생성하여 거기에 객체를 할당함
즉 Javascript engine은 함수 선언문을 함수 표현식으로 변환해 객체를 생성한다고 생각할 수 있음. 단, 정확하게 동일하게 동작하지는 않음

### 함수 표현식

함수는 객체 타입의 값. 따라서 변수에 할당도 가능하고, property에 할당도 가능하며 배열의 요소도 될 수 있음. 이러한 성질의 객체를 **일급 객체**라고 표현함
함수 객체를 변수에 할당하는 정의 방식을 함수 표현식`function expression`이라고 함

```js
var add = function (x, y) {
  return x + y;
};
```

함수 리터럴의 함수는 이름이 생략 가능하며 일반적으로 이름을 생략함. 함수 이름은 함수 몸체 내부에서만 유효하며 외부에서는 해당 이름으로 호출이 불가능함
함수 선언문은 `표현식이 아닌 문`이며, 함수 표현식은 `표현식인 문`. 미묘하지만 중요한 차이임

### 함수 생성 시점과 함수 호이스팅

```js
// reference
console.dir(add);
console.dir(sub);

// call
console.log(add(2, 5));
console.log(sub(2, 5));

// declaration
function add(x, y) {
  return x + y;
}

// expression
var sub = function (x, y) {
  return x - y;
};
```

`declaration`을 통해 정의한 함수는 그 이전에 호출이 가능, `expression`은 이전에 호출이 불가능함. 두 함수 간에는 생성 시점이 다름
다른 선언문과 마찬가지로 `runtime`이전에 엔진에 의해 먼저 실행 됨. 즉 `declaration`을 이용한 함수 정의는 먼저 함수 객체가 생성됨. 그 후 엔진은 함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고 함수 객체를 할당함. 다시말해, `declaration`을 통한 함수 정의는 `runtime`이전에 함수 객체 생성, 함수 이름과 동일한 식별자에 해당 객체를 할당까지 완료한 상태. 이를 `function hoisting`이라함

`expression`은 변수에 할당되는 값이 함수 리터럴인 문. 따라서 기본적인 변수 선언, 변수 할당문을 한번에 기술한 축약 표현과 동일함. 즉 변수 호이스팅과 동일하게 동작하며 실제 함수 객체가 되는 시점은 `runtime`에 평가되는 시점임

함수 호이스팅과 같은 문제 때문에 함수 표현식을 사용할 것을 권장함

#### variable hoisting vs function hoisting

변수 호이스팅은 `var`키워드로 선언된 변수는 `undefined`로 초기화하는 것. 함수 호이스팅은 암묵적으로 생성된 식별자를 함수 객체로 초기화 하는 것. 따라서 함수 객체는 호출이 가능함

### Function 생성자 함수

기본 제공되는 빌트인 함수는 `Function` 생성자 함수에 매개변수 목록과 몸체를 문자열로 전달하여 생성하는 방식

```js
var add = new Function(`x`, `y`, `return x + y`);

console.log(add(2, 5)); // 7
```

일반적이지도 않고 바람직 하지도 않음. 위 생성자로 생성한 함수는 `closure`를 생성하지도 않으며, 선언문이나 표현식으로 생성한 함수와 다르게 동작하기도 함

### 화살표 함수

ES6에서 도입된 선언 방식. 항상 익명 함수로 정의

```js
const add = (x, y) => x + y;
console.log(add(2, 5));
```

이 방식은 표현 뿐 아니라 내부 동작도 간략화 되어 있음. 기존과는 `this` 바인딩 방식이 다르며, `prototype property`도 없고, `arguments`객체를 생성하지도 않음

## 함수 호출

### 매개변수와 인수

외부에서 내부로 값을 전달할 경우 `parameter`를 통해 `argument`를 전달함. `argument`는 값으로 평가될 수 있는 표현식이어야 함

`parameter`는 함수 몸체 내부에서 변수와 동일하게 취급됨. 즉 함수 호출 시 함수 몸체 내에서 암묵적으로 매개변수가 생성되고, `undefined`초기화가 된 이후 인수가 할당됨.

`parameter`의 스코프는 함수 내부

`paramter`와 `argument`의 개수 일치는 별도로 체크하지 않음. `parameter`보다 `argument`가 부족하다면 `undefined`로 값이 할당되며, `argument`가 더 많다면 초과된 `argument`들은 무시됨. 모든 인수들은 `arguments`객체의 `property`로 보관되기에 가변 인자 함수를 구현할 때 사용 가능함

### 인수 확인

별도의 체크가 없기 때문에 함수를 정의할 때 적절한 인수가 잘 전달되었는지 확인할 필요가 있음

```js
function add(x, y) {
  if (typeof x !== "number" || typeof y !== "number") {
    throw new TypeError("인수는 모두 숫자 값이어야 한다.");
  }
  return x + y;
}

console.log(add(2)); // TypeError: 인수는 모두 숫자 값이어야 한다.
console.log(add("a", "b")); // TypeError: 인수는 모두 숫자 값이어야 한다.
```

하지만 이런 방식도 부적절한 호출을 사전에 방지할 수 없고 `runtime`에서 에러가 발생하기 때문에 `typescript`와 같은 상위 확장을 도입하여 컴파일 시점에 방지하는 것도 하나의 방법

또한 `arguments` 객체를 통해 인수 개수를 확인할 수도 있으며, 전달되지 않은 경우 단축 평가를 사용하여 `parameter`에 기본값을 할당하는 방법도 있음

```js
function add(a, b, c) {
  a = a || 0;
  b = b || 0;
  c = c || 0;

  return a + b + c;
}

// ES6
function add(a = 0, b = 0, c = 0) {
  return a + b + c;
}
```

ES6의 `paramter default value`를 사용하여 인수 체크 및 초기화를 간소화 할 수도 있음

### 매개변수의 최대 개수

ECMAScript 사양에서 명시적인 제한은 없음. 물론 물리적 한계가 있기 때문에 엔진 별 최대 개수 제한은 존재

이상적인 매개 변수는 0개 , 적을 수록 좋음. 함수는 한가지만을 하며 가급적 작게 만들어야 함.
3개 이상의 객체 하나를 매개 변수로 선언하는 것이 좋음

객체를 활용하면 매개 변수의 순서로부터 자유로움. 다만 argument를 함수 내부에서 변경하게 된다면 외부 객체 또한 변경되는 side effect 발현

### 반환문

함수는 `return` 키워드를 활용하여 모든 값을 반환할 수 있음. 함수 호출 표현식은 `return`의 반환 값으로 평가됨

반환문은 아래와 같은 2가지 역할을 수행

- 함수의 실행을 중단하며 함수 몸체를 빠져나감. 하위 문은 실행되지 않음
- 키워드 뒤에오는 표현식을 평가하여 반환함. 지정하지 않을 시 `undefined`가 반환
  - 반환문을 생략해도 `undefined`가 반환

키워드와 반환값 사이 줄바꿈이 있으면 `ASI`에 의해 세미콜론이 자동 삽입되어 반환값이 무시되게 됨

`node.js`에서는 모듈 시스템에 의해 파일이 독립적인 `scope`를 갖기 때문에 가장 바깥 영역에서 반환문을 사용해도 `SyntaxError`에 해당되지 않음

## 참조에 의한 전달과 외부 상태의 변경
