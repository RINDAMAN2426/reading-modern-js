# Chapter 14. 전역 변수의 문제점

## Life Cycle

### Local variable’s life cycle

변수는 자신이 선언된 위치에서 생성 및 소멸 됨
전역 변수의 life cycle은 application의 life cycle과 동일
함수 내부에서 선언된 변수는 함수 호출 시 생성, 함수 종료 시 소멸함

변수 선언은 선언문이 어디 있든 런타임 이전에 먼저 실행 되는데 엄밀하게 이는 전역 변수에만 해당함. 함수 내부에서 선언된 변수는 함수가 호출 되고 난 후, 함수 바디의 코드가 실행되기 전 먼저 실행됨

```js
function fn() {
  var local = "local"; // 변수 생성 및 undefined 초기화, 이후 함수 실행을 통한 'local' 문자열 할당
  console.log(local);
  return local; // 이후 함수 종료 후 변수 소멸
}

fn();
console.log(local); // local은 이미 소멸된 상태
```

Life cycle은 즉 allocate 시점부터 release되어 memory pool에 반환되는 시점까지

메모리 공간은 누구도 참조하지 않을 때 GC에 의해 해제되어 memory pool에 반환되는데, 누군가가 참조중이라면 release되지 않음. 스코프도 이와 마찬가지

#### Quiz

```js
var global = "global";

function fn() {
  console.log(global); // ?
  var global = "local";
}

fn();
console.log(global); // ?
```

\* 호이스팅은 스코프 단위로 동작

### 전역 변수의 life cycle

전역 코드는 엔트리 포인트 없이 로드 되자마자 해석되고 실행됨. 반환문이 없기 때문에 마지막 문이 실행되어 더 이상 실행할 것이 없을 때 종료됨.

`var` 키워드로 선언한 변수는 전역 객체의 property가 됨. 즉 `전역 변수의 life cycle = 전역 객체의 life cycle`

#### global object

> global object는 코드 실행 이전 단계에서 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체. browser에서는 window, 서버사이드에서는 global 객체. 환경 마다 전역 객체를 지칭하는 다양한 식별자가 존재했으나 ES11에서 `globalThis`로 통일
> global object는 표준 빌트인 객체 + 호스트 객체, 그리고 var 키워드로 선언한 전역 변수 및 전역 함수를 property로 가짐

## 전역 변수의 문제점

**암묵적 결합 implicit coupling\*\*** 모든 코드가 전역 변수를 참조 및 변경할 수 있음. 따라서 위험성도 높아짐

**긴 life cycle**
전역 변수는 life cycle이 길기 때문에 메모리 리소스도 오랜 기간 소비하게 됨

**스코프 체인 상에서 종점에 존재**
전역 변수는 변수 검색 시 가장 마지막에 검색. 차이가 크진 않지만 엄연히 존재함

**네임스페이스 오염**
파일이 분리되어 있어도 하나의 전역 스코프를 공유함. 따라서 파일 내 동일한 이름으로 명명된 전역 변수, 전역 함수가 같은 스코프내 존재한다면 예상치 못한 결과가 발생함

## 전역 변수 사용을 억제

변수의 스코프는 좁을수록 좋음

### 즉시 실행 함수

즉시 실행 함수는 정의와 동시에 호출되기 때문에 스코프가 지역으로 설정됨

### 네임스페이스 객체

전역에 `namespace`를 담당할 객체를 생성하고 전역 변수처럼 쓰고 싶은 변수를 프로퍼티로 추가. 식별자 충돌 방지 효과는 있으나 `namespace`객체가 결국 전역 변수에 할당 되므로 유용하진 않은 패턴

### 모듈 패턴

모듈 패턴의 기반은 클로저. 전역 변수 억제는 물론 캡슐화까지 가능함
자바스크립트는 `public`,`private`,`protected`등의 접근 제한자를 제공하진 않음
정보 은닉 구현을 하기 위해 사용함

```js
var Mod = (function () {
  var num = 0; // private

  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
})();

console.log(Mod.num); // undefined
console.log(Mod.increase()); // 1
console.log(Mod.decrease()); // 0
```

### ES6 Module

ES6 Module을 사용하면 전역 변수 사용이 불가능. 파일 자체의 독자적인 모듈 스코프를 제공함. 모듈 내 `var`로 선언한 변수는 전역 변수가 아니게 됨
