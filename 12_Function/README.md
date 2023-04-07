# 12장 함수

<br>
<br>

## 12.1 함수란?

```
일련의 과정을 문(Statement)으로 구현하고 코드 블록으로 감싸서 하나의 실행 단위로 정의한 것
```

- 매개 변수 (parameter) : 함수 내부로 입력을 전달받는 변수
- 인수 (argument) : 입력
- 반환값 (return value) : 출력

```js
function add(x, y) {
  return x + y;
}

var result = add(2, 5);

// 인수를 매개변수를 통해 함수에 전달하면서 함수의 실행을 명시적으로 지시해야 한다 → 함수 호출 이라고 함

console.log(result); // 7
```

<br>
<br>

## 12.2 함수를 사용하는 이유

함수는 여러번 호출 가능, 재사용할 수 있으므로 **_코드의 재사용_** 측면에서 유용하며 **_유지보수의 편의성_** 을 높이고 실수를 줄임

<br>
<br>

## 12.3 함수 리터럴

자바스크립트 함수는 객체 타입의 값 → 함수 리터럴(function 키워드, 함수 이름, 매개 변수 목록, 함수 몸체)로 생성할 수 있다.

- 함수 이름
  - 식별자이므로 식별자 네이밍 규칙 준수
  - 함수 몸체 내에서만 참조 가능
  - 생략 가능
- 매개변수 목록
  - 0개 이상의 매개변수를 소괄호로 감싸고 쉼표로 구분
  - 함수를 호출할 때 지정한 인수가 순서대로 할당
  - 매개변수는 함수 몸체 내에서 변수와 동일하게 취급
- 함수 몸체
  - 함수가 호출되었을 때 일괄적으로 실행될 문들을 하나의 실행 단위로 정의한 코드 블록
  - 함수 호출에 의해 실행됨

💡 함수는 객체지만 일반 객체와 달리 **_호출이 가능하고, 함수 객체만의 고유한 프로퍼티를 갖음_**

<br>
<br>

## 12.4 함수 정의

**: 호출하기 이전에 인수를 전달받을 매개변수와 실행할 문들, 반환할 값을 지정하는 것**, 자바스크립트 엔진에 의해 평가되어 함수 객체가 됨

📄 함수 선언문 평가 → 식별자가 암묵적으로 생성, 함수 객체가 할당되기 때문에 ECMAScript 사양에서도 **변수**에는 **_선언(declaration)_** **함수**에는 **_정의(definition)_** 라고 표현한다.

<br>

### 12.4.1 함수 선언문

함수 선언문은 함수 리터럴과 형태가 동일하고 **_함수 이름을 생략할 수 없다._**

함수 선언문은 표현식이 아닌 문이다. (콘솔에서 실행하면 생성된 함수가 출력되는 것이 아닌, 완료 값 undefined가 출력)

```js
// 함수 선언문은 표현식이 아닌 문이므로 변수에 할당할 수 없다
// 하지만 함수 선언문이 변수에 할당되는 것처럼 보임

var add = function add(x, y) {
  return x + y;
};

// 함수 호출
console.log(add(2, 5)); // 7
```

JS 엔진은 {}이 단독으로 존재하면 코드 블록, 값으로 평가되어야 할 문맥에서는 객체 리터럴로 해석

<br>

```js
// 기명 함수 리터럴을 단독으로 사용하면 함수 선언문으로 해석
// 함수 선언문에서는 함수 이름을 생략할 수 없다.
function foo() {
  console.log("foo");
}

foo(); // foo

// 함수 리터럴을 피연산자로 사용하면 함수 선언문이 아니라 함수 리터럴 표현식으로 해석
// 함수 리터럴에서는 함수 이름을 생략 가능
(function bar() {
  console.log("bar");
});

bar(); // ReferenceError
```

그룹 연산자 내에 있는 함수 리터럴(bar)은 값이 아닌, **_함수 리터럴 표현식_** 으로 해석됨 → 피연산자로 사용 부적합

foo는 함수 몸체 내부에서만 유효 식별자인 함수 이름이므로 foo로 함수를 호출할 수 없어야 하지만, JS엔진이 암묵적으로 생성한 식별자이기 때문에 이름으로 호출 가능하다.

💡 JS엔진은 함수 선언문을 해석해 함수 객체를 생성. 생성된 함수를 호출하기 위해 **_함수 이름과 동일한 이름의 식별자를 암묵적으로 생성_** 하고, 거기에 함수 객체를 할당.

```js
// 함수는 함수 이름이 아닌, 암묵적으로 생성 된 함수 객체를 가리키는 식별자로 호출하는 것
var add = function add(x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

결론적으로 JS엔진은 함수 선언문을 함수 표현식으로 변환해 함수 객체를 생성. 단, 함수 선언문과 함수 표현식이 정확히 동일하게 동작하는 것은 아님

<br>

### 12.4.2 함수 표현식

자바스크립트의 함수는 값의 성질을 갖는 객체인 **_일급 객체_**

```js
// 함수 리터럴로 생성한 함수 객체를 변수에 할당한 기명 함수 표현식

var add = function foo(x, y) {
  return x + y;
};

// 함수 객체를 가리키는 식별자로 호출
// 이름으로 호출하면 함수 내부에서만 유효하기 때문에 ReferenceError가 발생함 !
console.log(add(2, 5)); // 7
```

<br>

### 12.4.3 함수 생성 시점과 함수 호이스팅

함수 선언문으로 정의한 함수와 함수 표현식으로 정의한 함수의 생성 시점이 달라서 함수 **표현식으로 정의한 함수는 표현식 이전에 호출할 수 없음**

```js
// 함수 참조
console.dir(add); // ƒ add(x, y)
console.dir(sub); // undefined

// 함수 호출
console.log(add(2, 5)); // 7
console.log(sub(2, 5)); // TypeError

// 함수 선언문
function add(x, y) {
  return x + y;
}

// 함수 표현식
var sub = function (x, y) {
  return x - y;
};
```

모든 선언문과 마찬가지로 함수 선언문도 런타임 이전에 먼저 실행되기 때문에 선언문 이전에 참조, 호출이 가능하다. (런타임에는 이미 함수 객체가 생성되어 있고 함수 이름과 동일한 식별자에 할당까지 완료 상태)

> 💡 이처럼 함수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작하는 특징을 **_함수 호이스팅_** 이라고 한다. (변수 호이스팅은 `var`로 선언된 변수는 undefined로 초기화되고, 함수 선언문을 통해 암묵적으로 생성된 식별자는 함수 객체로 초기화되는 미묘한 차이가 있음)

<br>

### 12.4.4 Function 생성자 함수

자바스크립트가 기본 제공하는 빌트인 함수인 Function 생성자 함수에 매개변수 목록과 함수 몸체를 문자열로 전달하면서 new 연산자와 함께 호출하면 함수 객체를 생성해서 반환한다.

```js
var add = new Function("x", "y", "return x + y");

console.log(add(2, 5)); // 7
```

Function 생성자 함수로 함수를 생성하는 방식은 생성한 함수가 클로저를 생성하지 않는 등 일반적이지 않고 다르게 동작

### 12.4.5 화살표 함수

화살표 함수는 기존의 함수보다 표현, 내부동작 또한 간략화

- 화살표함수는 생성자 함수로 사용할 수 없음
- 기존 함수와 this 바인딩 방식이 다름
- prototype 프로퍼티 없음
- arguments 객체 생성하지 않음

```js
const add = (x, y) => x + y;
console.log(add(2, 5)); // 7
```

26.3절 "화살표 함수"에서 자세히

<br>
<br>

## 12.5 함수 호출

### 12.5.1 매개변수와 인수

**_매개변수 parameter_** 를 통해 **_인수 argument_** 를 전달하고, 인수는 값으로 평가될 수 있는 표현식으로 함수를 호출할 때 지정(개수, 타입 제한 없음)

매개변수 수 보다 초과된 인수는 무시된다.

```js
function add(x, y) {
  console.log(x, y); // 2 5
  return x + y;
}

add(2, 5);

// add 함수의 매개변수 x, y의 스코프(유효 범위)는 함수 내부다!
console.log(x, y); // ReferenceError
```

arguments 객체는 함수를 정의할 때 매개변수 개수를 확정할 수 없는 **_가변 인자 함수를 구현_** 할 때 유용하게 사용 18.2.1 "arguments 프로퍼티"에서 자세히

```js
function add(x, y) {
  console.log(arguments);
  // Arguments(3) [2, 5, 10, callee: ƒ, Symbol(Symbol.iterator): ƒ]

  return x + y;
}

add(2, 5, 10);
```

<br>

### 12.5.2 인수 확인

```
1. 자바스크립트 함수는 매개변수와 인수의 개수가 일치하는지 확인하지 않음
2. 자바스크립트는 동적 타입 언어이기때문에 매개변수의 타입을 사전에 지정할 수 없음
```

따라서 타입스크립트와 같은 정적 타입을 선언할 수 있는 JS 상위 확장을 도입해서 컴파일 시점에 부적절한 호출을 방지할 수 있게 만드는 것도 하나의 방법이다.

ES6에 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화 가능

<br>

### 12.5.3 매개변수의 최대 개수

```
ECMAScript 사양에서는 매개변수의 최대 개수에 대해 명시적으로 제한하고 있지 않지만 물리적 한계는 있음
```

> 💡 이상적인 함수는 한 가지 일만 해야 하며 가급적 작게 만들어야 한다.

매개변수는 최대 3개 이상을 넘지 않는 것을 권장. 만약 그 이상 필요하다면, 하나의 매개변수를 선언하고 객체를 인수로 전달하는 것이 유리. 하지만 주의할 것은 함수 외부에서 함수 내부로 전달한 객체를 함수 내부에서 변경 → 함수 외부의 객체가 변경됨 (side effect)

<br>

### 12.5.4 반환문

함수는 return 키워드와 표현식으로 이뤄진 반환문을 사용해 실행 결과를 함수 외부로 반환

```js
function multiply(x, y) {
  return x * y; // 반환문

  // 함수 실행을 중단하고 빠져나감(이후 다른 문이 존재하면 그 문은 실행되지 않고 무시)
  // return 키워드 뒤에 오는 표현식을 평가해 반환, 반환문 생략하면 암묵적으로 undefined 반환

  console.log("실행 안된다 ~ ");
}

// 함수 호출은 반환값으로 평가
var result = multiply(3, 5);
console.log(result); // 15
```

<br>
<br>

## 12.6 참조에 의한 전달과 외부 상태의 변경

**_원시값은 값에 의한 전달_** , **_객체는 참조에 의한 전달_** 방식으로 동작 (함수의 매개변수도 동일)

```js
// 매개변수 primitive는 원시값을 전달받고, 매개변수 obj는 객체를 전달받는다.
function changeVal(primitive, obj) {
  primitive += 100;
  obj.name = "Kim";
}

// 외부 상태
var num = 100;
var person = { name: "Lee" };

console.log(num); // 100
console.log(person); // {name: "Lee"}

// 원시값은 값 자체가 복사되어 전달되고 객체는 참조값이 복사되어 전달된다.
changeVal(num, person);

// 원시값은 원본이 훼손되지 않는다.
console.log(num); // 100

// 객체는 원본이 훼손된다.
console.log(person); // {name: "Kim"}

// person객체를 매개변수로 전달하면 함수 내부에서 이를 수정할 경우 원본 객체가 변경됨 (side effect)
// 원시값은 값을 복사해 전달하기 때문에 영향 없음
```

```
📄 순수 함수란?
외부의 상태를 변경 X
외부 상태에 의존 X
함수가 외부 상태를 변경하게 되면 상태 변화를 추적하기 어려워짐
→ 코드의 복잡성 증가, 가독성을 해치는 원인

순수 함수를 통해 부수효과를 최대한 억제하여 오류를 피하고 안정성을 높이는 패러다임을 함수형 프로그래밍이라 한다.
```

<br>
<br>

## 12.7 다양한 함수의 형태

### 12.7.1 즉시 실행 함수 (IIFE, Immediately Invoked Function Expression)

함수의 **_정의와 동시에 즉시 호출_** 되는 함수 (단 한 번만 호출됨!)

```js
// 익명 즉시 실행 함수
(function () {
  var a = 3;
  var b = 5;
  return a * b;
})();
```

익명함수를 사용하는 것이 일반적이지만 기명함수도 사용 가능
그룹연산자()내의 기명함수는 함수 몸체에서만 참조가능하여 호출할 수 없다.

```js
// 기명 즉시 실행 함수
// 즉시 실행 함수는 반드시 그룹 연산자로 감싸야 함
(function foo() {
  var a = 3;
  var b = 5;
  return a * b;
})();

foo(); // ReferenceError: foo is not defined
```

무명이든 기명이든 함수를 그룹 연산자로 감싸면 함수 리터럴로 평가되어 함수 객체가 됨

```js
console.log(typeof function f() {}); // function
console.log(typeof function () {}); // function
```

즉시 실행 함수도 일반 함수처럼 값을 반환하고 인수도 전달

```js
// 즉시 실행 함수도 일반 함수처럼 값을 반환
var res = (function () {
  var a = 3;
  var b = 5;
  return a * b;
})();

console.log(res); // 15

// 즉시 실행 함수에도 일반 함수처럼 인수를 전달
res = (function (a, b) {
  return a * b;
})(3, 5);

console.log(res); // 15
```

<br>

### 12.7.2 재귀 함수

함수 자기 자신을 호출하는 것을 재귀 호출(recursive call)이라고 한다.

```js
function countdown(n) {
  if (n < 0) return;
  console.log(n);
  countdown(n - 1); // 재귀 호출
}

countdown(10);
```

```js
// 함수 표현식
var factorial = function foo(n) {
  // 탈출 조건: n이 1 이하일 때 재귀 호출을 멈춤
  if (n <= 1) return 1;
  // 함수를 가리키는 식별자로 자기 자신을 재귀 호출
  return n * factorial(n - 1);

  // 함수 이름으로 자기 자신을 재귀 호출할 수도 있다.
  // console.log(factorial === foo); // true
  // return n * foo(n - 1);
};

console.log(factorial(5)); // 5! = 5 * 4 * 3 * 2 * 1 = 120
```

함수 식별자는 factorial 이지만 함수 내부에서는 함수 이름 foo 를 사용해 호출할 수 있다. 당연히 함수 식별자 factorial 로도 함수 내부에서 호출 가능

<br>

### 12.7.3 중첩 함수

중첩 함수(nested function) 또는 내부 함수(inner function)라고 하며 중첩 함수를 포함하는 함수는 외부 함수(outer function)라 한다.

```js
function outer() {
  var x = 1;

  // 중첩 함수
  function inner() {
    var y = 2;
    // 외부 함수의 변수를 참조 가능
    console.log(x + y); // 3
  }

  inner();
}

outer();
```

ES6부터는 함수 선언문을 if문이나 for문 등의 코드 블록 내에서도 정의할 수 있음

단, 호이스팅으로 인해 혼란이 발생할 수 있으므로 바람직하지 않음

중첩 함수는 클로저와 관련

<br>

### 12.7.4 콜백 함수 (callback function)

함수의 매개변수를 통해 다른 함수의 내부로 전달되는 함수

> 매개변수를 통해 함수의 외부에서 콜백 함수를 전달받은 함수를 고차 함수(Higher-Order Function, HOF)라고 한다.

```js
// n만큼 어떤 일을 반복
function repeat(n) {
  // i를 출력
  for (var i = 0; i < n; i++) console.log(i);
}

repeat(5); // 0 1 2 3 4
```

위 예제를 아래와 같이 변경하여 공통적으로 수행하는 일을 미리 정의해 재사용

```js
// 외부에서 전달받은 f를 n만큼 반복 호출
function repeat(n, f) {
  for (var i = 0; i < n; i++) {
    f(i); // i를 전달하면서 f를 호출
  }
}

var logAll = function (i) {
  console.log(i);
};

// 반복 호출할 함수를 인수로 전달
repeat(5, logAll); // 0 1 2 3 4

var logOdds = function (i) {
  if (i % 2) console.log(i);
};

// 반복 호출할 함수를 인수로 전달
repeat(5, logOdds); // 1 3
```

📄 고차 함수

- 콜백 함수를 전달받은 함수
- 콜백 함수를 자신의 일부분으로 합성
- 전달받은 콜백함수의 호출 시점을 결정해서 호출
- 콜백 함수에 인수를 전달

> 익명함수 리터럴로 정의하면서 곧바로 고차 함수에 전달하는 것이 일반적

```js
// 익명 함수 리터럴을 콜백 함수로 고차 함수에 전달
// 익명 함수 리터럴은 repeat 함수를 호출할 때마다 평가되어 함수 객체를 생성
repeat(5, function (i) {
  if (i % 2) console.log(i);
}); // 1 3
```

💡 함수형 프로그래밍 패러다임뿐만 아니라 비동기 처리(이벤트, Ajax통신, 타이머함수 등)에 활용되는 중요한 패턴이다.

```js
// 콜백 함수를 사용한 이벤트 처리
// myButton 버튼을 클릭하면 콜백 함수를 실행
document.getElementById("myButton").addEventListener("click", function () {
  console.log("button clicked!");
});

// 콜백 함수를 사용한 비동기 처리
// 1초 후에 메시지를 출력
setTimeout(function () {
  console.log("1초 경과");
}, 1000);
```

모든 콜백 함수가 고차 함수에 의해 호출되는 것은 아니다. setTimeout 함수의 콜백 함수는 setTimeout 함수가 호출하지 않음

**📄 배열 고차 함수**

```js
// 콜백 함수를 사용하는 고차 함수 map
var res = [1, 2, 3].map(function (item) {
  return item * 2;
});

console.log(res); // [2, 4, 6]

// 콜백 함수를 사용하는 고차 함수 filter
res = [1, 2, 3].filter(function (item) {
  return item % 2;
});

console.log(res); // [1, 3]

// 콜백 함수를 사용하는 고차 함수 reduce
res = [1, 2, 3].reduce(function (acc, cur) {
  return acc + cur;
}, 0);

console.log(res); // 6
```

<br>

### 12.7.5 순수 함수와 비순수 함수

- 순수함수

  - 외부 상태에 의존하지 않고 변경하지도 않는, 부수효과가 없는 함수
  - 동일한 인수가 전달되면 언제나 동일한 값을 반환
  - 전달된 인수에게만 의존해 반환값을 만든다.

- 비순수 함수
  - 외부 상태에 의존하거나 외부 상태를 변경하는, 부수효과가 있는 함수
  - 외부상태에 따라 반환값이 달라진다.

<br>

> 순수 함수

```js
var count = 0; // 현재 카운트를 나타내는 상태

// 순수 함수 increase는 동일한 인수가 전달되면 언제나 동일한 값을 반환한다.
function increase(n) {
  return ++n;
}

// 순수 함수가 반환한 결과값을 변수에 재할당해서 상태를 변경
count = increase(count);
console.log(count); // 1

count = increase(count);
console.log(count); // 2
```

> 비순수 함수

```js
var count = 0; // 현재 카운트를 나타내는 상태: increase 함수에 의해 변화

// 비순수 함수
function increase() {
  return ++count; // 외부 상태에 의존하며 외부 상태를 변경
}

// 비순수 함수는 외부 상태(count)를 변경하므로 상태 변화를 추적하기 어려워짐
increase();
console.log(count); // 1

increase();
console.log(count); // 2
```

매개변수를 통해 객체를 전달받으면 비순수 함수가 된다.

순수 함수를 사용하는 것이 좋다.

함수형 프로그래밍은 외부 상태를 변경하는 부수효과를 최소화해서 불변성(immutable)을 지향하는 프로그래밍 패러다임이다.

함수형 프로그래밍은 순수 함수를 통해 부수 효과를 최대한 억제하여 오류를 피하고, 프로그램의 안정성을 높이는 노력의 일환이다.

<br>
