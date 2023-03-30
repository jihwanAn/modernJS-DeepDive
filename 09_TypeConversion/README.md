# 9장 타입 변환과 단축 평가

<br>
<br>

## 9.1 타입 변환이란?

- **명시적 타입 변환** : 개발자가 의도적으로 타입을 변환하는 것

```js
var x = 10;

// 명시적 타입 변환
// 숫자를 문자열로 타입 캐스팅 한다.

var str = x.toString();
console.log(typeof str, str); // string 10

// x 변수의 값이 변경된 것은 아니다.
console.log(typeof x, x); // number 10
```

- **암묵적 타입 변환** : 개발자가 의도 X, 표현식 평가 도중 자바스크립트 엔진에 의해서 암묵적으로 자동 변환되기도 함

```js
var x = 10;

// 암묵적 타입 변환
// 문자열 연결 연산자는 숫자 타입 x의 값을 바탕으로 새로운 문자열을 생성

var str = x + "";
console.log(typeof str, str); // string 10

// x 변수의 값이 변경된 것은 아니다.
console.log(typeof x, x); // number 10
```

💡 때로는 명시적 타입 변환보다 암묵적 타입 변환이 가독성 측면에서 더 좋을 수도 있다. 타입 변환을 정확히 이해하고 사용하자 !

ex. 문법을 잘 이해하고 있는 개발자에게는 (10).toString() 보다 10 + "" 이 더욱 간결

<br>
<br>

## 9.2 암묵적 타입 변환

개발자의 의도와는 상관 없이 코드의 문맥을 고려해 암묵적으로 데이터 타입을 강제 변환할 때가 있다.

- **문자열 타입으로 변환**

```js
// 숫자 타입
0 + ""; // "0"
NaN + ""; // "NaN"

// 불리언 타입
true + ""; // "true"

// null 타입
null + ""; // "null"

// 객체 타입
({} + ""); // "[object Object]"

Math + ""; // "[object Math]"

[10, 20] + ""; // "10,20"

(function () {} + ""); // "function () {}"
```

- **숫자열 타입으로 변환**

```jsx
// 문자열 타입
+""; // 0
+"0"; // 0
+"String"; // NaN

// 불리언 타입
+ture; // 1

// null 타입
+null; // 0

// undefined 타입
+undefined; // NaN

// 객체 타입
+{}; // NaN
+[]; // 0
+[10, 20]; // NaN
```

- **불리언 타입으로 변환**

> false로 평가되는 값들

- false
- undefined
- null
- 0, -0
- NaN
- '' (빈 문자열), 빈문자열이 아닌 문자열은 Truthy한 값

<br>
<br>

## 9.3 명시적 타입 변환

개발자의 의도에 따라 명시적으로 타입을 변경하는 방법은 다양함.

📄 **표준 빌트인 생성자 함수**와 **표준 빌트인 메서드**는 자바스크립트에서 기본 제공하는 함수

- **표준 빌트인 생성자 함수** : 객체를 생성하기 위한 함수, new 연산자와 함께 호출
- **표준 빌트인 메서드** : 자바스크립트에서 기본 제공하는 빌트인 객체 메서드

<br>

### 9.3.1 문자열 타입으로 변환

1. String 생성자 함수를 new 연산자 없이 호출하는 방법
2. Object.prototype.toString 메서드 사용하는 방법
3. 문자열 연결 연산자를 이용하는 방법

<br>

### 9.3.2 숫자 타입으로 변환

1. Number 생성자 함수를 new 연산자 없이 호출
2. parseInt,parseFloat 함수를 사용하는 방법(문자열만 숫자 타입으로 변환 가능)
3. \+ 단항 산술 연산자 이용
4. \* 산술 연산자를 이용

<br>

### 9.3.3 불리언 타입으로 변환

1. Boolean 생성자 함수를 new 연산자 없이 호출하는 방법
2. ! 부정 논리 연산자를 두 번 사용하는 방법

<br>
<br>

## 9.4 단축 평가

### 9.4.1 논리 연산자를 사용한 단축 평가

```
단축평가란? 논리 연산의 결과를 결정하는 피연산자를 타입 변환하지 않고 그대로 반환하는 것
```

논리합(||) 또는 논리곱(&&) 연산자 표현식은 언제나 2개의 피연산자 중 어느 한쪽으로 평가하며, 좌항 → 우항으로 평가 진행

💡 단축 평가의 유용한 패턴

- 객체를 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때

```js
var elem = null;
var value = elem.value; // TypeError

// 이때 단축평가 사용하면 에러 발생 X

var elem = null;
// elem 이 null 이나 undefined 같은 Falsy 값이면 elem으로 평가
// Truthy 값이면 elem.value로 평가
var value = elem && elem.value; // null
```

- 함수 매개변수에 기본값을 설정할 때

```js
// 단축 평가를 사용한 매개변수의 기본값 설정
function getStringLength(str) {
  str = str || "";
  return str.length;
}

getStringLength(); // 0
getStringLength("hi"); // 2

// ES6의 매개변수의 기본값 설정
function getStringLength(str = "") {
  return str.length;
}

getStringLength(); // 0
getStringLength("hi"); // 2
```

<br>

### 9.4.2 옵셔널 체이닝 연산자 (?.)

```
ES11에서 도입,
좌항 피연산자가 null 또는 undefined → undefined 반환, 아닐 경우 우항의 프로퍼티 참조를 이어감
```

```js
var elem = null;

var value = elem?.value;
console.log(value); // undefined
```

객체를 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때 유용

```js
// 연산자가 Falsy 값이라도 null 또는 undefined가 아니면 우항의 프로퍼티 참조 이어감

var str = "";

var length = str?.length;
console.log(length); // 0
```

<br>

### 9.4.3 null 병합 연산자 (??)

```
ES11에서 도입,
좌항의 피연산자가 null 또는 undefined인 경우 우항의 피연산자를 반환, 아닐 경우 좌항의 피연산자 반환
```

```js
// 변수에 기본값 설정 시 유용

var foo = null ?? "default string";
console.log(foo); // "default string"
```

<br>
