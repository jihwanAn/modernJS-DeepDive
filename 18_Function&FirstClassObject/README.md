# 18장 함수와 일급 객체

<br>

## 18.1 일급 객체

1. 무명의 리터럴로 생성 가능 (즉, 런타임에서 생성이 가능)
2. 변수나 자료구조(객체, 배열 등)에 저장 가능
3. 함수의 매개변수에 전달 가능
4. 함수의 반환값으로 사용 가능

```js
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
// 런타임(할당 단계)에 함수 리터럴이 평가되어 함수 객체가 생성되고 변수에 할당된다.
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

// 2. 함수는 객체에 저장 가능
const auxs = { increase, decrease };

// 3. 함수의 매개변수에게 전달 가능
// 4. 함수의 반환값으로 사용 가능
function makeCounter(aux) {
  let num = 0;

  return function () {
    num = aux(num);
    return num;
  };
}

// 3. 함수는 매개변수에게 함수를 전달 가능
const increaser = makeCounter(auxs.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// 3. 함수는 매개변수에게 함수를 전달 가능
const decreaser = makeCounter(auxs.decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

반면, 일반 객체는 호출할 수 없지만, 함수 객체는 호출할 수 있다.

<br>

## 18.2 함수 객체의 프로퍼티

함수도 프로퍼티를 가질 수 있고, `console.dir` 로 함수 내부를 볼 수 있음

<br>

`Object.getOwnPropertyDescriptors` 메서드로 square 함수의 모든 프로퍼티의 프로퍼티 어트리뷰트를 확인해보자 ~

```js
function square(number) {
  return number * number;
}

console.log(Object.getOwnPropertyDescriptors(square));
/*
{
  length: {value: 1, writable: false, enumerable: false, configurable: true},
  name: {value: "square", writable: false, enumerable: false, configurable: true},
  arguments: {value: null, writable: false, enumerable: false, configurable: false},
  caller: {value: null, writable: false, enumerable: false, configurable: false},
  prototype: {value: {...}, writable: true, enumerable: false, configurable: false}
}
*/

// __proto__는 square 함수의 프로퍼티가 아님
console.log(Object.getOwnPropertyDescriptor(square, "__proto__")); // undefined

// __proto__는 Object.prototype 객체의 접근자 프로퍼티
// square 함수는 Object.prototype 객체로부터 __proto__ 접근자 프로퍼티를 상속받는다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__"));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}
```

`__proto__` 는 접근자 프로퍼티이며, 함수 객체 고유의 프로퍼티가 아니라 `Object.prototype` 객체의 프로퍼티를 상속받은 것이다.

<br>

### 18.2.1 arguments 프로퍼티

함수 객체의 arguments 프로퍼티 값은 arguments 객체이며, arguments 객체는 함수 호출 시 전달된 **_인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체_** 이며, 함수 내부에서 지역 변수처럼 사용됨(함수 외부 참조 X)

arguments 프로퍼티는 ES3부터 **폐지** 되어 일부 브라우저에서만 지원하기때문에 **_`Function.arguments` 와 같은 사용법은 권장지 않음._**

```js
function multiply(x, y) {
  console.log(arguments);
  return x * y;
}

console.log(multiply()); // NaN
console.log(multiply(1)); // NaN
console.log(multiply(1, 2)); // 2
console.log(multiply(1, 2, 3)); // 2
```

함수를 정의할 때 선언한 매개변수는 함수 몸체 내부에서 변수와 동일하게 취급

선언된 매개변수 보다 인수 적게 전달 하면, 인수가 전달되지 않은 매개변수는 undefined가 유지되고 초과된 인수는 무시 (암묵적으로 arguments 객체의 프로퍼티로 보관 됨)

<br>

📄 arguments 객체의 `Symbol(Symbol.iterator)` 프로퍼티는 arguments 객체를 **_순회 가능한 자료구조_** **iterable** 로 만들어 주는 프로퍼티다.

```js
function multiply(x, y) {
  // 이터레이터
  const iterator = arguments[Symbol.iterator]();

  // 이터레이터의 next 메서드를 호출하여 이터러블 객체 arguments를 순회
  console.log(iterator.next()); // {value: 1, done: false}
  console.log(iterator.next()); // {value: 2, done: false}
  console.log(iterator.next()); // {value: 3, done: false}
  console.log(iterator.next()); // {value: undefined, done: true}

  return x * y;
}

multiply(1, 2, 3);
```

arguments 객체는 매개변수 개수를 확정할 수 없는 **_가변 인자 함수_** 를 구현할 때 유용

```js
function sum() {
  let res = 0;

  // arguments 객체는 length 프로퍼티가 있는 유사 배열 객체이므로 for 문으로 순회 가능
  for (let i = 0; i < arguments.length; i++) {
    res += arguments[i];
  }

  return res;
}

console.log(sum()); // 0
console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3)); // 6
```

```
📄 이터러블의 개념이 없었던 ES6에서 arguments 객체는 유사 배열 객체로 구분되었는데, ES6에 도입된 이터레이션 프로토콜을 준수하면 순회 가능한 자료구조인 이터러블이 된다.

ES6부터 arguments 객체는 유사 배열 객체이면서 동시에 iterable
iterable 객체는 for...in, for...of문을 사용할 수 있다.
```

유사 배열 객체는 배열이 아니므로 배열 메서드를 사용하기 위해 `Function.prototype.call`, `Function.prototype.apply` 를 사용해 간접 호출해야 한다.

```js
function sum() {
  // arguments 객체를 배열로 변환
  const array = Array.prototype.slice.call(arguments);
  // 배열로 만들었기 떄문에 reduce 메서드를 사용가능
  return array.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3, 4, 5)); // 15
```

번거로움을 해결하기 위해 ES6에서 **_Rest 파라미터_** 도입

```js
// ES6 Rest parameter
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3, 4, 5)); // 15
```

Rest 파라미터 도입으로 모던 자바스크립트에서는 aruments 객체의 중요성이 이전같지 않지만, ES6만 사용하지는 않을 수 있어 알아둘 필요는 있음 - 26.4 "Rest 파라미터"에서 자세히

<br>

### 18.2.2 caller 프로퍼티

ECMAScript 사양에 포함되지 않은 비표준 프로퍼티. 참고로 알아두기만 하자 ~

함수 객체의 caller 프로퍼티는 함수 자신을 호출한 함수를 가리킨다.

<br>

### 18.2.3 length 프로퍼티

함수를 정의할 때 선언한 매개변수의 개수를 가리킴

arguments 객체의 length 프로퍼티 **(인자의 개수를 가리킴)** 와 함수객체의 length 프로퍼티 **(매개변수의 개수를 가리킴)** 값은 다를 수 있음

<br>

### 18.2.4 name 프로퍼티

```js
// 기명 함수 표현식
var namedFunc = function foo() {};
console.log(namedFunc.name); // foo

// 익명 함수 표현식
var anonymousFunc = function () {};
// ES5: name 프로퍼티는 빈 문자열을 값으로 갖는다.
// ES6: name 프로퍼티는 함수 객체를 가리키는 변수 이름을 값으로 갖는다.
console.log(anonymousFunc.name); // anonymousFunc

// 함수 선언문(Function declaration)
function bar() {}
console.log(bar.name); // bar
```

"함수 선언문"에서 살펴 봤듯이 **_함수 이름_** 과 **_함수 객체_** 를 가리키는 식별자는 의미가 다르다는 것을 잊지 말자

<br>

### 18.2.5 \_\_proto\_\_ 접근자 프로퍼티

모든 객체는 [[Prototype]]이라는 객체지향 프로그래밍의 상속을 구현하는 프로토타입 객체인 내부 슬롯을 갖는다.

\_\_proto\_\_ 프로퍼티는 [[Prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 **_간접적으로 접근_** (내부 슬롯에는 직접접근할 수 없음)하기 위해 사용하는 접근자 프로퍼티

```js
const obj = { a: 1 };

// 객체 리터럴 방식으로 생성한 객체의 프로토타입 객체는 Object.prototype
console.log(obj.__proto__ === Object.prototype); // true

// 객체 리터럴 방식으로 생성한 객체는 프로토타입 객체인 Object.prototype의 프로퍼티를 상속받는다.
// hasOwnProperty 메서드는 Object.prototype의 메서드다.
console.log(obj.hasOwnProperty("a")); // true
console.log(obj.hasOwnProperty("__proto__")); // false
```

<br>

### 18.2.6 prototype 프로퍼티

prototype 프로퍼티는 생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor 만이 소유하는 프로퍼티다.

일반 객체와 생성자 함수로 호출할 수 없는 non-constructor에는 prototype 프로퍼티가 없다.

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}.hasOwnProperty("prototype")); // → true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}.hasOwnProperty("prototype")); // → false
```

prototype 프로퍼티는 생성자 함수로 호출될 때 생성자 함수가 생성할 인스턴스의 프로토타입 객체를 가리킨다.

<br>
