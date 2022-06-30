# Chatper 11. 원시 값과 객체 비교

- `primitive type`은 immutable value. `object/reference type`은 mutable value
- `primitive value`을 변수에 할당하면 변수(확보된 메모리)에는 실제 값이 저장. `object`를 변수에 할당하면 참조 값이 저장
- `primitive value`를 갖는 변수를 다른 변수에 할당 시 값이 복사되어 전달`pass by value`. `object`를 가리키는 변수를 다른 변수에 할당하면 참조 값이 복사되어 전달`pass by reference`

## Primitive value

### immutable value

- 한번 생성된 `primitive value`는 read only 이기 때문에 변경할 수 없음
- `primitive type`의 변수에 새로운 `primitive type`을 재할당 하면, 메모리 공간에 저장되어 있는 `primitive value`를 변경을 하는 것이 아닌 새로운 메모리 공간을 확보하여, 재할당한 `primitive value`를 저장하는 것. 이유는 `primitive value`가 immutable 하기 때문임

### 문자열과 불변성

- `primitive value`를 저장하기 위해선 확보해야하는 메모리의 크기를 결정해야함. `ECMAScript`사양에서 `string(2bytes)`, `number(8bytes)`이외의 `primitive type`은 명확하게 규정하지 않기 때문에 브라우저 제조사 구현 마다 다를 수 있음
- `string`은 0개 이상의 `character` 집합. 1개의 `character`는 2 bytes의 메모리 공간에 저장. 따라서 몇 개의 문자로 이뤄졌느냐에 따라 필요한 메모리 공간의 크기가 결정됨. `C`와 같은 언어에서는 따라서 `char`만 존재하고, `string` 타입은 `char`의 배열로 처리, 자바에서는 `String`객체로 처리
- `string`은 유사 배열 객체이면서 `iterable`이므로 배열과 유사하게 문자에 접근 가능함

#### array-like object

```js
var str = "string";

// 유사 배열이기 때문에 index를 이용한 접근이 가능함
console.log(str[0]); // s

// primitive value이지만 object처럼 동작함
console.log(str.length); // 6
console.log(str.toUpperCase()); // STRING
```

- `primitive value`를 객체처럼 사용하면 `primitive value`를 감싸는 `wrapper object`로 자동 변환

```js
// primitive value이기 때문에 변경은 불가능함
str[0] = "S";
console.log(str); // string;

// 변수에 새 문자열을 할당하는 것은 가능
str = "new_string";
console.log(str); // new_string;
```

### 값에 의한 전달

```js
var score = 80;
var copy = score;

console.log(score); // 80
console.log(copy); // 80

score = 100;

console.log(score); // 100
console.log(copy); // ??
```

- **변수에 변수를 할당하였을 때 무엇이 어떻게 전달되는가?**
- `copy = score`구문에서 새로운 숫자 80이 생성되어 `copy`에 할당 됨. `primitive value`를 갖는 변수를 할당하면, 할당 받는 변수에는 `primitive value`가 복사가 되어 전달됨 `call by value`

```js
var score = 80;

// copy
var copy = score;

console.log(score, copy); // 80 80
console.log(score === copy); // true
```

- `score`와 `copy`의 값 80은 서로 같지만 다른 메모리 공간에 저장된 별개의 값. 따라서 이전 코드에서 `score`를 변경해도 `copy`와는 다른 공간에 저장된 값의 변경이므로 어떠한 영향도 주지 않음 (실제 메모리 핸들링이 이렇게 된다는 보장은 없음 브라우저 별로 다를 수 있음)
- 이 방식과는 다르게 같은 메모리 공간 값을 참조하다가, 재할당이 일어나게 되면 그 때 재할당된 값을 저장하도록 동작할 수도 있음`python`
- `call by value`라는 표현은 `ECMAScript`에는 등장하지 않음. 엄격하게 따지면 값이 아닌 메모리 주소가 전달되기 때문
- `var copy = score`에서 `score`는 80으로 평가되는데 이 때 두 가지 평가 방식이 가능함
  - 새로운 80을 생성 후, 그 80의 메모리 주소를 전달. 이 방식은 두 변수가 기억하는 메모리 주소가 다름
  - score가 참조하는 80의 메모리 주소를 그대로 전달. 이 방식은 두 변수가 기억하는 메모리 주소가 같음
- 두 방식 모두 어찌됐건 `primitive value`에 대하여 재할당을 통한 값의 변경은 다른 변수에 대해서는 간섭할 수 없음

## Object

- `property`는 동적으로 추가되고 삭제할 수도 있음. 값에 대한 제한마저도 없기 때문에 메모리 공간의 크기를 사전에 정해둘 수 없음. 관리 방식도 복잡하고 구현방식도 제조사마다 다를 수 있음

#### javascript 객체의 관리 방식

- `property key`를 인덱스로 사용하는 hash table로 생각할 수 있음. 유사하지만 높은 성능을 위해서 보다 나은 방법으로 객체를 구현함
- `java`,`c++` 같은 클래스 기반 객체 지향 언어에서는 생성 이후 `property`의 추가 삭제가 불가능. `javascript`는 동적으로 `proprety`와 `method`생성이 가능함. 사용하기는 편리하나 이론적으론 생성과 접근 비용이 더 많이 드는 방식이긴 함. 따라서 V8 엔진에서는 `dynamic lookup` 대신 `hidden class`라는 방식을 사용
- [https://v8.dev/blog/fast-properties](https://v8.dev/blog/fast-properties), [https://engineering.linecorp.com/ko/blog/v8-hidden-class](https://engineering.linecorp.com/ko/blog/v8-hidden-class), [https://meetup.toast.com/posts/78](https://meetup.toast.com/posts/78), [https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e), [https://www.youtube.com/watch?v=UJPdhx5zTaw](https://www.youtube.com/watch?v=UJPdhx5zTaw)

### 변경 가능한 값

- 객체는 `mutable value`
- 객체를 할당한 변수가 기억하는 메모리 주소를 통해 접근 하면 `reference value`에 접근 할 수 있음. 그 값은 객체가 저장된 메모리 공간의 주소
- `mutable value`이기 때문에 가장 큰 부작용은 여러 개의 식별자가 하나의 객체를 공유할 수 있음

#### shallow copy & deep copy

```js
const o = { x: { y: 1 } };

// shallow copy
const c1 = { ...o };
console.log(c1 === 0); // false
console.log(c1.x === o.x); // true

// deep copy
const _ = require("lodash");

const c2 = _.cloneDeep(o);
console.log(c2 === 0); // false
console.log(c2.x === o.x); // false
```

- `shallow`, `deep` 두가지 다 원본과는 다른 객체를 생성. 다만 `shallow copy`는 중첩된 객체가 있을 경우 참조 값을 복사, `deep copy`는 모두 원시값처럼 완전한 복사본을 만들어 냄.
- 아래와 같이 `primitive value`를 다른 변수에 할당하는 것을 `deep copy`, `object`를 다른 변수에 할당하는 것을 `shallow copy`라고 하는 경우도 있음

```js
const v = 1;

const c1 = v;
console.log(c1 === v); // true

const o = { x: 1 };
const c2 = o;
console.log(c2 === 0); // true
```

### 참조에 의한 전달

```js
var person = {
  name: "Lee",
};

// shallow copy
var copy = person;
```

- `copy`에는 `person`에 참조값이 복사되어 전달되기 때문에 참조에 의한 전달이라 함
- `person`과 `copy`의 메모리 주소는 다르지만 동일한 참조 값을 가지고 있음. 따라서 객체의 변화는 두 변수 모두에게 영향을 줌

```js
var person = {
  name: "Lee",
};

var copy = person;

console.log(copy === person); // true

copy.name = "Kim";

person.address = "Seoul";

console.log(person); // { name: 'Kim', address: 'Seoul' }
console.log(copy); // { name: 'Kim', address: 'Seoul' }
```

- `call by value`, `call by reference`는 결국 식별자가 기억하는 메모리 공간에 저장되어 있는 값을 복사 전달 한다는 면에서 동일. 그 값이 `primitive`냐 `reference`냐의 차이만 있을 뿐. 따라서 엄격하게는 `call by value`만이 존재한다고 할 수 있음

### Quiz

```js
var person1 = {
  name: "Lee",
};

var person2 = {
  name: "Lee",
};

console.log(person1 === person2);
console.log(person1.name === person2.name);
```
