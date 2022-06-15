Chapter 8. Control flow statement

## Block Statement

- 0개 이상의 statement를 brackets로 묶은 것
- block statement는 하나의 실행 단위
- 단독 사용 가능하나, 제어문 혹은 함수를 정의할 때 사용하는 것이 일반적
- 자체적인 종결성을 갖기 때문에 semi colon 불필요

## Conditional Statement

- conditional expression의 평가 결과에 따라 `code block`의 실행을 결정

### if else

- if문의 conditional expression은 `boolean`으로 평가 되어야함. 아닌경우 암묵적 타입 변환
- ternary operator로 바꿔 쓸 수 있음. 조건에 따른 실행문이 복잡하다면 `if else`이 가독성에 좋음

### switch

- `if else`와는 다르게 expression이 `string`이나 `number`인 경우가 많음

## loop

- conditional expression이 거짓일 때까지 계속 반복함

## Break

- `label statement`, `loop`,`switch`의 `code block`을 탈출. 그외 사용 시 `SyntaxError`

### label statement

```js
foo: {
  console.log(1);
  break foo;
  console.log(2);
}

console.log("Done");

//
// 1
// Done
```

## Continue

- 반복문의 실행을 해당 지점에서 중단하고 증감식으로 이동
