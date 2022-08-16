# Chapter 20. Strict Mode

## Strict Mode

```js
function foo() {
  x = 1;
}

foo();

console.log(x); // ?
```

위의 코드 진행은 다음과 같다

1. 선언되지 않는 x값에 1을 할당해야함
2. 할당을 위해 `scope chain` 검색
3. `foo` 함수 스코프 내에서는 x의 선언문이 존재하지 않아 상위 scope로 이동하여 검색
4. 상위 scope(전역)에 도달함에도 선언을 찾을 수 없음. 이 때 엔진은 `ReferenceError`가 아닌 암묵적으로 전역 객체에 x property를 동적 생성함. 이를 `implicit global`이라고 함

`implicit global`은 예기치 못한 오류 발생으로 이어질 수 있음. 따라서 `ES5`부터 `strict mode`를 지원하도록 함. 유사한 효과로는 `ESLint`같은 도구도 존재함. 린트는 `static analysis`를 통해 실행 전 코드 스캔을 통한 문법 오류, 잠재적 오류를 찾아내고 리포팅을 해주는 도구

## Strict Mode 적용

적용 방법은 전역 선두 혹은 함수 바디 선두에 `'use strict';`를 추가하면 적용됨

```js
"use strict";

function foo() {
  x = 1; // ReferenceError: x is not defined
}

foo();
```

## 전역 `strict mode`는 지양하는 것이 좋음

전역에 적용한 `strict mode`는 스크립트 단위로 적용됨

```js
<!DOCTYPE html>
<html>
    <body>
        <script>
            'use strict';
        </script>
        <script>
            x = 1; // 에러 발생하지 않음
        </script>
    </body>
</html>
```

위와 같이 `strict mode`가 적용된 스크립트와 적용하지 않은 스크립트의 혼용은 오류로 이어질 수 있음. 특히 서드파티 라이브러리의 경우 적용하지 않을 수도 있음.

## 함수 단위의 `strict mode`도 지양하는 것이 좋음

스크립트와 마찬가지로 함수도 적용이 된 함수와 적용하지 않은 함수를 혼용하는 것은 바람직하지 않으며, 모든 함수를 일일이 적용하는 것은 번거로움. 따라서 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직 함

## `strict mode`가 발생시키는 에러

대표적인 에러 케이스들은 아래와 같음

### implicit global

```js
(function () {
  "use strict";

  x = 1; // ReferenceError: x is not defined
  console.log(x);
})();
```

### 변수, 함수, 매개변수 삭제

```js
(function () {
  "use strict";

  var x = 1;
  delete x; // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a; // SyntaxError: Delete of an unqualified identifier in strict mode.
  }

  delete foo; // SyntaxError: Delete of an unqualified identifier in strict mode.
})();
```

### parameter 이름 중복

```js
(function () {
  "use strict";

  function foo(x, x) {
    return x + x; // SyntaxError: Duplicate parameter name not allowed in this context
  }
  console.log(foo(1, 2));
})();
```

### with 문 사용

`with statement`의 역할은 전달받은 객체를 `scope chain`에 추가함. 동일한 객체의 프로퍼티를 반복 사용 시 코드가 간단해질 수는 있으나, 성능 및 가독성이 나빠지는 문제가 있음

```js
(function () {
  "use strict";

  with ({ x: 1 }) {
    // SyntaxError: Strict mode code may not include a with statement
    console.log(x);
  }
})();
```

## `strict mode`사용을 통한 변화

### 일반 함수의 `this`

`strict mode`에서 함수를 일반 함수로 호출하면 `this`에는 `undefined`가 바인딩 됨. `Constructor Function`이 아닌 일반 함수의 경우 `this`를 사용할 이유가 없기 때문. 에러는 발생하지 않음

### `arguments` 객체

`parameter`에 전달된 `argument`를 재할당하여 변경해도 `arguments` 객체에는 반영되지 않음

```js
(function (a) {
  "use strict";

  a = 2;

  console.log(arguments); // [Arguments] {}
})(1);
```
