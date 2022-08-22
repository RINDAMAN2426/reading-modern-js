# Chapter23.  Execution Context

## 소스코드의 타입

`ECMAScript`에서 정의하는 `ECMAScript code` 타입은 총 4가지. 4가지의 소스코드들은 실행 컨텍스트를 생성함

**4가지 타입의 소스코드**
1. Global code - 전역에 존재하는 소스코드. 전역에 정의된 함수, 클래스 바디 내의 코드는 포함하지 않음
2. Function code - 함수 바디에 존재하는 소스코드. 중첩 함수,클래스 내의 코드는 포함하지 않음
3. Eval code - `built-in` 전역 함수인 `eval` 함수에 인수로 전달되어 실행되는 소스코드
4. Mode code - 모듈 내부에 존재하는 소스코드. 모듈 내부에 존재하는 함수, 클래스 내부 바디의 소스코드는 포함하지 않음

**각 타입의 실행 컨텍스트**
1. Global code - 전역 변수를 관리하기 위한 전역 스코프를 생성.  `var` 키워드로 선언된 전역 변수, 함수 선언문으로 정의된 전역 함수를 전역 객체의 프로퍼티 및 메소드로 바인딩, 참조하기 위해 전역 객체와 연결되어야 함. 전역 코드가 평가되면 전역 실행 컨텍스트가 생성됨
2. Function code - 지역 스코프를 생성하여 지역 변수, 매개변수, `arguments` 객체 관리. 생성한 지역 스코프를 `scope chain`의 일원으로 연결해야함. 함수 코드가 평가되면 함수 실행 컨텍스트가 생성됨
3. Eval code - `strict mode`에서 자신만의 독자적인 스코프 생성. `eval` 코드가 평가되면 `eval` 실행 컨텍스트가 생성됨
4. Module code - 모듈 별 독자적 스코프 생성. 모듈 코드가 평가되면 모듈 실행 컨텍스트가 생성됨

## 소스코드의 평가와 실행

모든 소스코드의 실행에 앞서 평가 과정을 거침. 평가 과정에서 실행 컨텍스트의 생성, 선언문의 실행을 통한 변수, 식별자를 실행 컨텍스트가 관리하는 스코프 내 등록.

평가가 끝나면 실행되기 시작(런타임). 실행에 필요한 참조는 실행 컨텍스트가 관리하는 스코프에서 검색하여취득

var = foo;
foo = 'bar';

위 코드가 실행 된다면

1. `var foo`의 실행을 통한 식별자 `foo`를 실행 컨텍스트가 관리하는 스코프 내 등록 및 `undefined` 초기화
2. 런타임에서 첫 줄의 선언문은 이미 실행이 됐기 때문에, 할당문 `foo = 'bar'`가 실행. 따라서 `foo`의 선언 여부를 체크
3. 실행 컨텍스트 내 해당 식별자가 존재한다면 해당 변수에 값을 할당(`bar`) 그 후 다시 결과를 실행 컨텍스트에 등록하여 관리

## 실행 컨텍스트의 역할

// global variable
const x = 1;
const y = 2;

function foo(a) {
    // local variable
    const x = 10;
    const y = 20;

    // call method
    console.log(a + x + y);
}

// call function
foo(100);

// call method
console.log(x + y);

위 코드의 타입별 평가, 실행 과정은 아래와 같다.

1. Evaluation global code
	1. 변수, 함수선언문의 실행
	2. 선언문 실행 결과를 전역 스코프에 등록. `var`  키워드로 선언된 변수와 함수 선언문으로 정의된 전역 함수는 전역 객체의 프로퍼티와 메소드가 됨
2. Execution global code
	1. 전역 변수에 값이 할당
	2. 함수 호출
	3. 전역 코드 실행 중단 및 함수 내부로 진입
3. Evaluation function code
	1. 실행 중단 후 평가 실시
	2. 매개변수, 지역 변수 선언문의 실행
	3. 결과를 지역 스코프에 등록
	4. `arguments` 객체 생성 및 스코프 등록
	5. `this binding`
4. Execution function code
	1. 지역 변수, 매개변수 값의 할당
	2. `method` 호출. 식별자를 `scope chain`에서 검색
	3. `method property`를 객체의 `prototype chain`에서 검색
	4. 메소드의 인수 값(`a+x+y`) 평가. 해당 식별자들은 다시 `scope chain`에서 검색
	5. 메소드 실행 후 함수 호출 이전으로 돌아가 전역 코드 실행을 계속

위의 과정을 수행하기 위해서 아래와 같은 관리 포인트가 필요함
	1. 선언에 의해 생성된 모든 식별자를 스코프에 구분하여 등록, 상태 변화를 지속적으로 관리 가능해야함
	2. 스코프는 중첩 관계에 의해 `scope chain`을 혈성해야 함. 또한 이를 통한 상위 스코프 식별자 검색이 가능해야함
	3. 실행 중인 코드의 실행 순서를 변경할 수 있어야 함

위 3가지 관리포인트를 수행하는 것이 실행 컨텍스트. 식별자와 스코프는 **렉시컬 환경**으로 관리하며 코드 실행 순서는 **실행 컨텍스트 스택**으로 관리함

## 실행 컨텍스트 스택

const x = 1;

function foo() {
    const y = 2;

    function bar() {
        const z = 3;
        console.log(x + y + z);
    }
    bar();
}

foo();

위의 코드는 전역 코드와 함수 코드로 구성된 코드. 엔진은 전역 코드를 평가하여 실행 컨텍스트를 생성. 그 후 함수 호출시 함수 코드를 평가하여 함수 실행 컨텍스트를 생성함.
생성된 실행 컨텍스트는 스택으로 관리되는데 이를 **실행 컨텍스트 스택**이라 함. 스택에 쌓이는 과정은 아래와 같음

const executionStack = [];

executionStack.push('global execution context');
executionStack.push('foo function execution context');
executionStack.push('bar function execution context');
executionStack.pop();
executionStack.pop();
executionStack.pop();

1. 전역 코드의 평가, 실행
	- 전역 변수 `x`, 전역 함수 `foo`가 전역 실행 컨텍스트에 등록, 전역 실행 컨텍스트를 실행 컨텍스트에 푸시함. 그 후 코드가 실행 되어 `x`의 값이 할당되고 `foo`의 호출이 발생
2. `foo` 함수 코드의 평가, 실행
	- `foo`의 호출 이후 실행되는 과정. 위와 같은 과정 실시
3. `bar` 함수 코드의 평가, 실행
	- 위와 같은 과정 실시. 단, `console.log`의 메소드 또한 함수이기 때문에 이것도 실행 컨텍스트를 생성 및 푸시 한다.
4. `foo`로 복귀
5. 전역 코드로 복귀

위 과정에서 최상위의 실행 컨텍스트를 `running execution context`라고 부름


## Lexical Environment

렉시컬 환경은 식별자, 식별자에 바인딩 된 값, 상위 스코프에 대한 참조를 기록함.

키와 값을 갖는 객체 형태의 스코프를 생성, 식별자가 키가 되고 바인딩된 값을 관리함. 즉 `lexical scope`의 실체

실행 컨텍스트는 `LexicalEnvironment` 컴포넌트와 `VariableEnvironment` 컴포넌트로 구성됨.

생성 초기의 `LexicalEnvironment` 컴포넌트와 `VariableEnvironment` 컴포넌트는 하나의 동일한 렉시컬 환경을 참조함. 이후 특정 상황들을 통해 `VariableEnvironment` 컴포넌트를 위한 새로운 렉시컬 환경이 생성되면서 두 컴포넌트간의 내용이 달라지는 경우가 있음

렉시컬 환경은 또 아래의 두 개의 컴포넌트로 구성됨

1. Environment Record
	- 스코프에 포함된 식별자를 등록, 식별자에 바인딩 된 값을 관리하는 저장소
2. Outer Lexical Environment Reference
	- 상위 스코프를 가리킴. 상위 스코프란 즉 실행 컨텍스틀 생성한 소스코드를 포함하는 상위코드의 렉시컬 환경. 외부 렉시컬 환경에 대한 참조를 통해 단방향 연결 리스트인 `scope chain`를 구현함

## 실행 컨텍스트의 생성과 식별자 검색 과정

var x = 1;
const y = 2;

function foo(a) {
    var x = 3;
    const y = 4;

    function bar(b) {
        const z = 5;
        console.log(a + b + x + y + z);
    }
    bar(10);
}

foo(20);

위 코드의 과정은 아래와 같음

### 전역 객체 생성

전역 객체는 코드 평가 이전에 생성됨. 여기 `built-in` 전역 프로퍼티와 함수, 표준 `built-in` 객체가 추가되며, 동작 환경에 따라 호스트 객체도 포함함. `Object.prototype`을 상속받는데 즉, 전역 객체 또한 `prototype chain`의 일원임

### 전역 코드 평가

전역 코드의 평가 과정은 아래와 같음
1. `Global Execution Context` 생성
2. `Global Lexical Environment` 생성
	1. `Global Environment Record` 생성
		1. `Object Environment Record` 생성
		2. `Declarative Environment Record` 생성
	2. `this` 바인딩
	3. 외부 렉시컬 환경에 대한 참조 결정

**1. `Global Execution Context` 생성**

비어 있는 `Global Execution Context`를 생성하여 실행 컨텍스트 스택에 푸시함. 전역 실행 컨텍스트는 최상위 실행 컨텍스트 (`running execution context`)가 됨.

**2. `Global Lexical Environment` 생성**

`Global Lexical Environment`를 생성하여 `Global Execution Context`의 `LexicalEnvironment`에 바인딩함

**2-1. `Global Environment Record` 생성**

`Global Environment Record`는 전역 변수를 관리하는 전역 스코프, 전역 객체의 빌트인 전역 프로퍼티와 빌트인 전역 함수, 표준 빌트인 객체를 제공.

`ES6`이전엔 전역 객체가 `Global Environment Record`의 역할을 수행했으나, `let`과 `const` 변수는 전역 객체의 프로퍼티가 되지 않고 개념적인 블록 내에 존재하게 됨. 이와 같은 구분을 위해서 `Global Environment Record`는 `Object Environment Record`와 `Declarative Environment Record`로 구성됨.

`Object Environment Record`는 기존 전역 객체가 관리하던 것들, `Declarative Environment Record`는 `let`,`const` 키워드로 선언한 전역 변수 관리

**2-1-1.** `Object Environment Record` 생성

`Object Environment Record`는 `BindingObject`라고 부르는 객체와 연결이 됨.이 `BindingObject`는 `Global Object` 생성에서 생성된 전역 객체.

전역 코드 평가 과정에서 `var` 키워드 선언 변수와 함수 선언문으로 정의된 함수는 `Object Environment Record`에 연결된 `BindingObject`를 통해 전역 객체의 프로퍼티와 메소드가 되는 것. 이를 통해 전역 객체 식별자 없이 전역 객체의 프로퍼티를 참조할 수 있게 됨.

예제 코드를 보면 변수 `x`와 `foo` 함수가 `BindingObject`에 바인딩 된 전역 객체의 프로퍼티와 메소드가 된다.

변수 `x`는 `var` 키워드로 선언했기 때문에 선언과 초기화가 동시에 진행되기 때문에 바로 `undefined`가 바인딩됨. 따라서 이 평가 단계가 끝나고 실행단계에서 변수 `x`는 그 어디에서도 참조가 가능해지기 때문에 호이스팅이 발생하는 것이다.

함수 `foo`는 생성된 함수 객체를 즉시 할당하게 되고 이게 변수 호이스팅과 함수 호이스팅의 차이이다.

**2-1-2. `Declarative Environment Record` 생성**

`let`, `const` 키워드로 선언한 변수를 등록 및 관리하는 레코드. 즉 이 레코드에서 관리되는 변수들은 `Object Environment Recodrd`에 연결된 `BindingObject`를 통해 연결된 전역 객체와는 다른 곳에 저장되기 때문에 전역 객체의 프로퍼티로써 참조가 불가능함. 또한 `const` 키워드의 경우 선언과 초기화 단계가 분리되어 진행되는데 이로 인해 런타임의 변수 선언문에 도달하기 전까지 `Temporal Dead Zone; TDZ`에 빠지게 됨

let foo = 1;

{
    // let, const 또한 호이스팅은 발생함
    // 따라서 foo는 스코프내의 foo를 참조하게 되는데 여기서 ReferenceError가 발생함
    console.log(foo);
    let foo = 2;
}

**2-2. `this` 바인딩 **

`GlobalEnvironmentRecord`의 `[[GlobalThisValue]]` 내부 슬롯에 `this`가 바인딩됨. 여기서는 전역 객체가 바인딩이 됨.

`Object Environment Record`와 `Declarative Environment Record`에는 `this` 바인딩이 없다. `this` 바인딩은 오로지 `GlobalEnvironmentRecord`와 `FunctionEnvironmentRecord`에만 존재한다.

**2-3. 외부 렉시컬 환경에 대한 참조 결정**

이는 현재 평가중인 소스코드를 포함하는 외부 소스코드의 렉시컬 환경을 가리킴. 예제 케이스의 경우 최상위이기 때문에 참조에는 `null`이 할당됨

### 전역 코드 실행

평가가 끝나면 이제 실행되기 시작함. 변수 할당문이 실행되어 `x`, `y` 값이 할당되고 그 후 `foo` 함수가 호출됨. 변수 할당문, 함수 호출문의 실행을 위해선 해당 식별자들이 선언된 식별자인지 확인해야 함.
식별자의 경우 다른 스코프라면 동일한 이름이 사용 가능한데 이를 결정하기 위해 `identifier resolution`이 있음. 식별자를 검색할 때는 실행 중인 실행 컨텍스트에서 검색하기 시작한다.
`x`,`y`,`foo`의 경우 전역 컨텍스트가 실행 컨텍스트 이므로 `Global Lexical Environment`에서  검색을 시작하며 존재하지 않을 시 상위 스코프로 이동하여 검색을 시작함. 지금은 최상위이기 때문에 여기 존재하지 않으면. `ReferenceError`가 발생함.

### 함수 코드 평가

`foo` 함수의 호출로 전역 코드 실행은 일시 중단되며 `foo` 함수 내부로 제어권이 이동한다. 그 후 함수 코드를 평가하기 시작하며 함수 코드 평가는 아래와 같이 실행 된다.

1. `Function Execution Context` 생성
2. `Function Lexical Environment` 생성
	1. `FunctionEnvironmentRecord` 생성
	2. `this` 바인딩
	3. 외부 렉시컬 환경에 대한 참조 결정


**1. `Function Execution Context` 생성**

`foo Execution Context`를 생성. 이는 `Lexical Environment` 를 구성 완료 한 다음 실행 컨텍스트 스택에 푸시됨. `foo Execution Context`가 푸시되면서 `runnin execution context`는 변경된다.

**2. `Function Lexical Environment` 생성**

`Function Lexical Environment`를 생성하여, `Funcion Execution Context`에 바인딩 한다.

**2-1. `Function Environment Record` 생성**

`Function Environment Record`는 매개변수, `arguments` 객체, 함수 내부에 선언된 변수 및 중첩함수를 등록 관리함.

**2-2. `this` 바인딩**

`Function Environment Record`의 `[[ThisValue]]` 슬롯에 `this`가 바인딩 되며, 바인딩될 객체는 함수 호출 방식에 따라 결정됨

**2-3. 외부 렉시컬 환경에 대한 참조 결정**

함수 정의가 평가된 시점에 실행중인 실행 컨텍스트의 렉시컬 환경의 참조가 할당됨. 즉 평가 중 `running execution context`는 `Global Execution Context`이므로 `Global Execution Context`의 참조(전역객체)가 할당됨. 렉시컬 스코프의 경우는 어디에서 정의했는지에 따라 상위 스코프를 결정함.

함수 정의를 평가하여 함수 객체를 생성할 때 `running execution context`의 렉시컬 환경을 함수 객체의 내부 슬롯인  `[[Environment]]`에 저장하게 되며, 이 `[[Environment]]` 슬롯에 저장된 값을 ** 외부 렉시컬 환경에 대한 참조 결정**에서 사용하게 된다.

### `foo` 함수 코드 실행

매개변수에 인수 값이 할당, 변수 할당문이 실행되며, `bar` 함수가 호출됨

### `bar` 함수 코드 평가

`foo` 함수 코드 평가 방법과 동일

### `bar` 함수 코드 실행

`console.log(a+b+x+y+z);`의 실행. 해당 식별자들을 `scope chain`을 통해서 검색 및 호출한다.

### `bar` 함수 코드 실행 종료

`console.log`의 실행 후 함수 코드 실행은 종료됨. 이 때 실행 컨텍스트 스택에서 `bar Execution Context`가 제거되고 다시 `foo`가 `running execution context`가 된다.

실행 컨텍스트에서 제거되었다고 `lexical environment`가 없어지진 않는다. 모든 곳에서 참조가 되지 않을 때 `GC`에 의해서 메모리가 해제되어 소멸한다.

### `foo` 함수 코드 실행 종료

위와 동일

### 전역 코드 실행 종료

위와 동일. 실행 컨텍스트 스택엔 이제 아무것도 남지 않게 됨

## 실행 컨텍스트와 블록 레벨 스코프

`var`와 달리 `let`과 `const`는 `block -level scope`를 따르고 있음. 블록 레벨 스코프 생성 시, `Declarative Environment Record`를 갖는 `BLOCK Lexical Environment`를 새롭게 생성하여 `Global Lexical Environment`을 교체시킨다. 이 때 `BLOCK Lexical Environment`의 외부 렉시컬 환경 참조는 `Global Lexical Environment` 이다.

블록 내용이 종료되면 다시 `Global Lexical Environment`로 되돌린다. 이는 모든 블록문에 해당됨.

만일 `for`문 내부의 함수실행이 있다면 해당 함수의 외부 렉시컬 환경 참조는 해당 `for`문의 `BLOCK Lexical Environment` 이다.

