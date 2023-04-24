# 17장 생성자 함수에 의한 객체 생성

객체 리터럴 이외에도 다양한 방법으로 생성 가능

<br>

## 17.1 Object 생성자 함수

new 연산자와 함께 Object 생성자 함수를 호출하면 빈 객체 생성, 이후 프로퍼티 또는 메서드 추가하여 객체를 완성할 수 있다.

Object 생성자 함수 이외에도 `String`, `Number`, `Boolean`, `Function`, `Array`, `Date`, `RegExp`, `Promise` 등의 빌드인 생성자 함수 제공

<br>
<br>

## 17.2 생성자 함수

### 17.2.1 객체 리터럴에 의한 객체 생성 방식의 문제점

자바스크립트에서 객체를 생성하는 방법 중, 객체 리터럴에 의한 객체 생성 방식은 직관적이고 간편 (단, 객체 리터럴에 의한 객체 생성 방식은 **_단 하나의 객체만 생성_** )

```js
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle2.getDiameter()); // 20
```

프로퍼티를 통해 → 객체 고유의 상태(state) 표현
메서드를 통해 프로퍼티를 참조하고 조작하는 동작(behavior) 표현

메서드(behavior)의 경우는 내용이 동일한 경우가 일반적인데, 객체 리터럴로 객체를 표현하는 경우 프로퍼티의 구조가 동일함에도 매번 같은 프로퍼티와 메서드를 기술해야하는 문제

<br>

### 17.2.2 생성자 함수에 의한 객체 생성 방식의 장점

객체(인스턴스)를 생성하기 위한 **템플릿(클래스) 처럼** 생성자 함수를 사용해서 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성할 수 있음

```js
// 생성자 함수
function circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킴
  this.radius = radius;
  this.getDiameter() = function(){
    return 2 * this.radius;
  }
}

// 인스턴스의 생성
const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

<br>

> 📄 this는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 **_자기 참조 변수_** , this 바인딩은 **_함수 호출 방식_** 에 따라 동적으로 결정됨 → 22장 "this"에서 자세히

| 함수 호출 방식       | this가 가리키는 값(this 바인딩)        |
| -------------------- | -------------------------------------- |
| 일반 함수로서 호출   | 전역 객체                              |
| 메서드로서 호출      | 메서드를 호출한 객체(마침표 앞의 객체) |
| 생성자 함수로서 호출 | 생성자 함수가 (미래에) 생성할 인스턴스 |

```js
function foo() {
  console.log(this);
}

// 일반적인 함수로서 호출
// 전역 객체는 브라우저 환경에서는 window, Node.js 환경에서는 global
foo(); // window

const obj = { foo }; // ES6 프로퍼티 축약 표현

// 메서드로서 호출
obj.foo(); // obj

// 생성자 함수로서 호출
const inst = new foo(); // inst
```

```js
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않음
// 즉, 일반 함수로서 호출
const circle3 = Circle(15);

// 일반 함수로서 호출된 Circle은 반환문이 없으므로 암묵적으로 undefined 반환

// 일반 함수로서 호출된 Circle 내의 this는 전역 객체를 가리킴
console.log(radius); // 15
```

<br>

### 17.2.3 생성자 함수의 인스턴스 생성 과정

생성자 함수의 몸체에서 수행하는 과정은 다음과 같다.

- 인스턴스를 생성 → 필수 작업

- 생성된 인스턴스를 초기화 (인스턴스 프로퍼티 추가 및 초기값 할당) → 옵션 작업

**new 연산자** 와 함께 **생성자 함수를 호출** 하면, 자바스크립트 엔진은 다음과 같은 과정을 거쳐 암묵적으로 인스턴스를 생성하고 인스턴스를 초기화한 후 암묵적으로 인스턴스를 반환한다.

**1. 인스턴스 생성과 this 바인딩**

- 암묵적으로 빈 객체가 생성
- 이 빈 객체가 아직은 미완성된 생성자 함수가 생성한 인스턴스
- 이 빈 객체(인스턴스)는 this에 바인딩된다.
  - 바인딩(binding) : 식별자(identifier) 와 값(value) 을 연결하는 과정
  - this 바인딩 은 this 와 this가 가리킬 객체를 바인딩하는 것

> 위에 처리과정은 함수 몸체의 코드가 한 줄씩 실행되는 **_런타임 이전_** 에 실행

```js
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩
  console.log(this); // Circle {}

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

**2. 인스턴스 초기화**

생성자 함수 몸체에 기술되어 있는 코드가 한 줄씩 실행되면서 **_this에 바인딩되어 있는 인스턴스를 초기화_**

인스턴스에 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 초기화하거나 고정값을 할당

```js
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩
  // 2. this에 바인딩되어 있는 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

**3. 인스턴스 반환**

생성자 함수 몸체의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this (생성자 함수에 의해 생성된 인스턴스)가 암묵적으로 반환

```js
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩
  // 2. this에 바인딩되어 있는 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환
}

// 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: f}
```

여기서 바인딩 된 this를 암묵적으로 반환하는 것 대신 다른 객체를 명시적으로 반환할 경우, return 문에 명시한 객체가 반환됨

또 명시적으로 원시값을 반환할 경우, 원시 값은 무시되고 암묵적으로 this 가 반환

> 💡 생성자 함수 내부에서 명시적으로 this 가 아닌 다른 값을 반환 하는 것은 생성자 함수의 기본 동작을 훼손하는 것 → **_반드시 생성자 함수 내부에서는 return 문을 생략할 것_**

<br>

### 17.2.4 내부 메서드 [[Call]]과 [[Construct]]

자바스크립트에서 함수는 객체이지만 일반 객체와는 다름

```
일반 객체는 호출 X, 함수는 호출 O

이와 같은 차이점은
함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드는 물론 함수 객체만을 위한 [[Environment]], [[FormalParameters]] 등의 내부 슬롯과 [[Call]], [[Construct]] 같은 내부 메서드를 추가로 가지고 있기 때문
```

함수가 일반 함수로서 호출 → 함수 객체 내부 메서드 [[Call]] 이 호출

함수가 생성자 함수로서 호출 → 함수 객체 내부 메서드 [[Construct]] 이 호출

```
내부 메서드 [[Call]] 을 갖는 함수 객체 → callable
내부 메서드 [[Construct]] 을 갖는 함수 객체 → constructor
내부 메서드 [[Construct]] 을 갖지 않는 함수 객체 → non-constructor
```

💡 결론적으로 함수 객체는 callable이면서 constructor이거나, callable이면서 non-constructor 이다. (모든 함수 객체를 생성자 함수로서 호출할 수 있는건 아님)

<br>

### 17.2.5 constructor 와 non-constructor의 구분

non-constructor는 [[Construct]] 내부 메서드가 없는 함수 객체 (new 연산자와 함께 생성자 함수로 호출할 수 없는 함수 객체)

| 구분            |                                                 |
| --------------- | ----------------------------------------------- |
| constructor     | 함수 선언문, 함수 표현식, 클래스(클래스도 함수) |
| non-constructor | 메서드(ES6 메서드 축약 표현), 화살표 함수       |

<br>

```js
// 일반 함수 정의: 함수 선언문, 함수 표현식
function foo() {}
const bar = function () {};
// 프로퍼티 x의 값으로 할당된 것은 일반 함수로 정의된 함수다. (이는 메서드로 인정하지 않음)
const baz = {
  x: function () {},
};

// 일반 함수로 정의된 함수만이 constructor
new foo(); // -> foo {}
new bar(); // -> bar {}
new baz.x(); // -> x {}

// 화살표 함수 정의
const arrow = () => {};

new arrow(); // TypeError: arrow is not a constructor

// 메서드 정의: ES6의 메서드 축약 표현만을 메서드로 인정
const obj = {
  x() {},
};

new obj.x(); // TypeError: obj.x is not a constructor
```

<br>

### 17.2.6 new 연산자

**_new 연산자와 함께 함수를 호출_** → 해당 함수는 생성자 함수로 동작

함수 객체의 내부 메서드 [[Call]]이 호출되는 것이 아니라 [[Construct]]가 호출된다. 단 함수는 constructor이어야함 ( **_non-constructor는 안됨_** )

```js
// 생성자 함수로서 정의하지 않은 일반 함수
function add(x, y) {
  return x + y;
}

// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
let inst = new add();

// 함수가 객체를 반환하지 않았으므로 반환문이 무시되고 빈 객체가 생성되어 반환
console.log(inst); // {}

// 객체를 반환하는 일반 함수
function createUser(name, role) {
  return { name, role };
}

// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
inst = new createUser("Lee", "admin");
// 함수가 생성한 객체를 반환
console.log(inst); // {name: "Lee", role: "admin"}
```

반대로 new 연산자 없이 호출하면 일반 함수로 호출됨

```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수 호출하면 일반 함수로서 호출
const circle = Circle(5);
console.log(circle); // undefined

// 일반 함수 내부의 this는 전역 객체 window를 가리킴
console.log(radius); // 5
console.log(getDiameter()); // 10

circle.getDiameter();
// TypeError: Cannot read property 'getDiameter' of undefined
```

Circle 함수를 new 연산자와 함께 생성자 함수로서 호출 → 함수 내부의 this는 Circle 인스턴스를 가리킴

Circle 함수를 일반 함수로 호출 → 함수 내부의 this는 window를 가리킴

<br>

### 17.2.7 new.target

ES6에서는 new.target을 지원

new.target 연산자는 this와 유사하게 constructor인 모든 함수 내부에서 암묵적인 지역변수와 같이 사용되며 메타 프로퍼티라고 부름

new 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 new.target은 함수 자신을 가리킴(new연산자 없이 일반 함수로 호출되면 undefined를 반환)

```js
// 생성자 함수
function Circle(radius) {
  // 이 함수가 new 연산자와 함께 호출되지 않았다면 new.target은 undefined
  if (!new.target) {
    // new 연산자와 함께 생성자 함수를 재귀 호출하여 생성된 인스턴스를 반환
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수를 호출하여도 new.target을 통해 생성자 함수로서 호출됨
const circle = Circle(5);
console.log(circle.getDiameter());
```

<br>

📄 스코프 세이프 생성자 패턴 : new.target을 사용할 수 없는 상황이라면 스코프 세이프 생성자 패턴을 사용할 수 있다. instanceof 연산자를 활용

```js
// Scope-Safe Constructor Pattern

function Circle(radius) {
  // 생성자 함수가 new 연산자와 함께 호출되면 함수의 선두에서 빈 객체를 생성하고
  // this에 바인딩한다. 이때 this와 Circle은 프로토타입에 의해 연결

  // 이 함수가 new 연산자와 함께 호출되지 않았다면 이 시점의 this는 전역 객체 window를 가리킴
  // 즉, this와 Circle은 프로토타입에 의해 연결되지 않음
  if (!(this instanceof Circle)) {
    // new 연산자와 함께 호출하여 생성된 인스턴스를 반환
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수를 호출하여도 생성자 함수로서 호출
const circle = Circle(5);
console.log(circle.getDiameter()); // 10
```

<br>

참고로 대부분의 빌트인 생성자 함수(`Object`, `String`, `Number`, `Boolean`, `Function`, `Array`, `Date`, `RegExp`, `Promise`등)는 new 연산자와 함께 호출되었는지를 확인한 후 적절한 값을 반환

- `Object`, `Function` 함수는 new 연산자 없이 호출해도 new 연산자와 함께 호출한 경우와 동일하게 동작

```js
let obj = new Object();
console.log(obj); // {}

obj = Object();
console.log(obj); // {}

let f = new Function("x", "return x ** x");
console.log(f); // ƒ anonymous(x) { return x ** x }

f = Function("x", "return x ** x");
console.log(f); // ƒ anonymous(x) { return x ** x }
```

- `String`, `Number`, `Boolean` 은 new 연산자와 함께 호출했을 때 객체를 생성하여 반환하지만, new연산자 없이 호출하면 문자열, 숫자, 불리언 원시 값을 반환

```js
const str = String(123);
console.log(str, typeof str); // 123 string

const num = Number("123");
console.log(num, typeof num); // 123 number

const bool = Boolean("true");
console.log(bool, typeof bool); // true boolean
```

<br>
