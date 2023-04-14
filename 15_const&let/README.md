# 15장 let, const 키워드와 블록 레벨 스코프

<br>

## 15.1 var 키워드로 선언한 변수의 문제점

### 15.1.1 변수 중복 선언 허용

```js
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용
// 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작

var x = 100;
// 초기화문이 없는 변수 선언문은 무시됨
var y;

console.log(x); // 100
console.log(y); // 1
```

<br>

### 15.1.2 함수 레벨 스코프

```js
var x = 1;

if (true) {
  // x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로, x 변수는 중복 선언됨
  // 이는 의도치 않게 변수값이 변경되는 부작용을 발생시킴
  var x = 10;
}

console.log(x); // 10
```

```js
var i = 10;

// for문에서 선언한 i는 전역 변수다. 이미 선언된 전역 변수 i가 있으므로 중복 선언됨
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 i 변수의 값이 변경됨
console.log(i); // 5
```

<br>

### 15.1.3 변수 호이스팅

`var` 키워드로 변수를 선언하면 **_변수 호이스팅_** 에 의해 변수 선언문이 스코프의 **_선두_** 로 끌어 올려진 것처럼 동작하여 변수 선언문 이전에 참조 가능 (선언문 이전에 참조하는 경우는 undefined를 반환)

```js
// 이 시점에는 변수 호이스팅에 의해 이미 foo 변수가 선언(1. 선언 단계)
// 변수 foo는 undefined로 초기화 (2. 초기화 단계)
console.log(foo); // undefined

// 변수에 값을 할당(3. 할당 단계)
foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행
var foo;
```

**_위와 같은 예제는 프로그램의 흐름상 맞지도 않고 가독성도 떨어뜨린다 !_**

<br>
<br>

## 15.2 let 키워드

앞서 살펴본 var 키워드의 단점을 보완하기 위해 ES6에서는 새로운 변수 선언 키워드인 **`let`** 과 **`const`** 도입

### 15.2.1 변수 중복 선언 금지

**`let`** 키워드는 중복 선언을 하면 문법에러 SyntaxError 가 발생

```js
var foo = 123;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작
var foo = 456;

let bar = 123;
// let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다 !!
let bar = 456; // SyntaxError
```

<br>

### 15.2.2 블록 레벨 스코프

var 키워드는 함수 레벨 스코프를 따르지만 **`let`** 키워드는 모든 코드 블록(함수,if문, for문, while문, try/catch문 등)을 지역 스코프로 인정하는 **_블록 레벨 스코프_** 를 따름

```js
let foo = 1; // 전역 변수

{
  let foo = 2; // 지역 변수
  let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError
```

💡 전역에 선언된 foo 변수와 코드 블록 내에서 선언된 foo 변수는 다른 별개의 변수 !!

<br>

### 15.2.3 변수 호이스팅

var 와 달리 **`let`** 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작한다.
하지만 동일하게 변수 호이스팅이 발생한다.

```js
console.log(foo); // ReferenceError
let foo;
```

암묵적으로 선언 단계와 초기화 단계가 한번에 진행되는 var 로 선언한 변수와 달리,
**`let`** 키워드로 선언한 변수는 **_선언 단계와 초기화 단계가 분리되어 진행_**

- **`var`** 키워드로 선언한 변수

```js
// var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행
// 따라서 변수 선언문 이전에 변수를 참조 가능
console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행
console.log(foo); // 1
```

<br>

- **`let`** 키워드로 선언한 변수

```js
// 런타임 이전에 선언 단계가 실행 아직 변수가 초기화되지 않음
// 초기화 이전의 일시적 사각 지대에서는 변수를 참조할 수 없음
console.log(foo); // ReferenceError

let foo; // 변수 선언문에서 초기화 단계가 실행
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행
console.log(foo); // 1
```

**`let`** 키워드로 선언한 변수는 **_일시적 사각지대 (Temporal Dead Zone)_** 가 발생

변수가 호이스팅된 스코프의 시작점부터 **`let`** 키워드로 선언된 선언문을 만나기 전 까지의 구간을 일시적 사각지대 (TDZ)라고 부름 (이 지점에서 변수를 참조하려고 하면 참조에러 발생)

```js
let foo = 1; // 전역 변수

{
  console.log(foo); // ReferenceError
  let foo = 2; // 지역 변수
}
```

만약 변수 호이스팅이 되지 않는다면 위 예제에서 전역 변수 foo 값인 1을 출력해야 하지만 `let` 키워드로 선언한 변수도 호이스팅이 발생하기 때문에 참조에러가 발생함

자바스크립트는 ES6에서 도입된 `let`, `const`를 포함해서 모든 선언(var, let, const, function, class 등)을 호이스팅한다.

<br>

### 15.2.4 전역 객체와 let

var 키워드로 전역 변수와 전역 함수, 선언하지 않은 변수(암묵적 전역변수)는 모두 window 객체의 프로퍼티가 됨 (전역 객체의 프로퍼티를 참조할 때 window 생략 가능)

하지만 `let` 키워드로 선언된 변수 → **전역 객체의 프로퍼티 X**

<br>

- `var` 키워드

```js
// 이 예제는 브라우저 환경에서 실행해야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체 window의 프로퍼티다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 함수는 전역 객체 window의 프로퍼티다.
console.log(window.foo); // ƒ foo() {}
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // ƒ foo() {}
```

<br>

- `let` 키워드

```js
// 이 예제는 브라우저 환경에서 실행해야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

```js
// 이 예제는 브라우저 환경에서 실행해야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

<br>
<br>

## 15.3 const 키워드

`const` 키워드는 상수를 선언하기 위해 사용하지만 반드시 상수만을 위해 사용하지는 않는다. (`const` 키워드의 특징은 `let` 키워드와 대부분 동일)

다른점을 중심으로 살펴보자

### 15.3.1 선언과 초기화

`const` 키워드로 선언한 변수는 반드시 **_선언과 동시에 초기화_** 해야 한다. (그렇지 않으면 문법에러 발생)

```js
const foo = 1;
```

```js
const foo; // SyntaxError: Missing initializer in const declaration
```

`const` 키워드로 선언한 변수도 `let` 과 마찬가지로 **_ 블록 레벨 스코프_** 를 가지며, 변수 호이스팅이 발생하지만 발생하지 않는것 처럼 동작함

```js
{
  // 변수 호이스팅이 발생하지 않는 것처럼 동작한다
  console.log(foo); // ReferenceError
  const foo = 1;
  console.log(foo); // 1
}

// 블록 레벨 스코프를 갖는다.
console.log(foo); // ReferenceError
```

<br>

### 15.3.2 재할당 금지

`const` 키워드로 선언한 변수는 **_재할당이 금지_**

```js
const foo = 1;
foo = 2; // TypeError
```

### 15.3.3 상수

원시 값은 **_변경 불가능한 immutable value_** 이므로 재할당 없이 값을 변경할 수 있는 방법이 없기 때문에, `const` 키워드로 선언한 변수에 **_원시 값을 할당한 경우_** 변수 값을 변경할 수 없다. → 이러한 특징을 이용해 `const` 키워드를 **_상수_** 를 표현하는데 사용하기도 함

일반적으로 상수의 이름은 대문자로 선언해 상수임을 명확히 나타내고, 여러 단어로 이뤄진 경우 언더스코어\_로 구분해 스네이크 케이스로 표현하는 것이 일반적임

```js
// 세율을 의미하는 0.1은 변경할 수 없는 상수로서 사용될 값
// 변수 이름을 대문자로 선언해 상수임을 명확히 나타냄
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + preTaxPrice * TAX_RATE;

console.log(afterTaxPrice); // 110
```

<br>

### 15.3.4 const 키워드와 객체

`const` 키워드로 선언된 변수에 원시 값을 할당하면 변경할 수 없다.

하지만 **_객체_** 를 할당하는 경우, 객체는 변경 가능한 값 mutable value 이기 때문에 재할당 없이도 직접 변경이 가능하여 값을 변경할 수 있다.

즉, `const` 키워드는 재할당을 금지할 뿐 불변을 의미하지는 않는다.

```js
const person = {
  name: "Lee",
};

// 객체는 변경 가능한 값이다. 따라서 재할당없이 변경이 가능하다.
person.name = "Kim";

console.log(person); // {name: "Kim"}
```

<br>
<br>

## 15.4 **var** vs **let** vs **const**

변수 선언시 기본적으로 `const`를 사용하며, 재할당이 필요한 경우에 한정하여 `let`을 사용하는 것이 좋다.

> `var`, `let`, `const` 키워드 사용시 권장사항

- ES6를 사용한다면 var는 사용하지 않는다.
- 재할당이 필요한 경우에 한정해 `let` 키워드를 사용하고, **_스코프는 최대한 좁게_**
- 변경이 없고 읽기 전용으로 사용하는 원시 값과 객체에는 `const` 를 사용 (재할당이 안되므로 안전)

```
💡 변수를 선언하는 시점에는 재할당이 필요할지 모르는 경우가 많기때문에, 변수를 선언할 때 일단 const 키워드를 사용해 선언하자. 코드를 만들다가 재할당이 필요하면 그때 let 키워드로 변경해도 결코 늦지 않다!
```

<br>
