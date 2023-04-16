# 16장 프로퍼티 어트리뷰트

<br>

## 16.1 내부 슬롯과 내부 메서드

내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECAMAScript 사양에서 사용하는 **_의사 프로퍼티_** 와 **_의사 메서드_**

개발자가 직접 접근할 수 있도록 외부로 공개된 객체의 프로퍼티는 아님

단, **_일부에 한하여 간접적으로 접근_** 할 수 있는 수단 제공

```js
const o = {};

// 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 직접 접근할 수 없다.
o.[[Prototype]] // → Uncaught SyntaxError

// 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 함
o.__proto__ // Object.prototype
```

<br>
<br>

## 16.2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

**자바스크립트 엔진은 프로퍼티를 생성할 떄 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의**

|              |                     |
| ------------ | ------------------- |
| Value        | 프로퍼티의 값       |
| Writable     | 값의 갱신 가능 여부 |
| Enumerable   | 열거 가능 여부      |
| Configurable | 재정의 가능 여부    |

<br>

```js
const person = {
  name: "Lee",
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환
console.log(Object.getOwnPropertyDescriptor(person, "name"));

// {value: 'Lee', writable: true, enumerable: true, configurable: true}
```

프로퍼티 어트리뷰트에 직접 접근할 수 없지만,
`Object.getOwnPropertyDescriptor()` 를 사용해 간접적으로 확인 가능 (첫 번째 매개변수 : 객체의 참조, 두 번째 매개변수 : 프로퍼티 키)

프로퍼티 어트리뷰트 정보를 제공하는 **_프로퍼티 디스크립터 객체_** 를 반환

존재하지 않는 프로퍼티나 상속받은 프로퍼티에 대한 프로퍼티 디스크립터를 요구하면 undefined 반환

ES8 도입된 `Object.getOwnPropertyDescriptors` 는 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들 반환

```js
const person = {
  name: "Lee",
};

// 프로퍼티 동적 생성
person.age = 20;

// 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환
console.log(Object.getOwnPropertyDescriptors(person));

/*
{
  age: {value: 20, writable: true, enumerable: true, configurable: true}
  name: {value: 'Lee', writable: true, enumerable: true, configurable: true}
}
*/
```

<br>
<br>

## 16.3 데이터 프로퍼티와 접근자 프로퍼티

```
데이터 프로퍼티 : 키와 값으로 구성된 일반적인 프로퍼티

접근자 프로퍼티 : 자체적으로는 값을 갖지 않고, 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티
```

### 16.3.1 데이터 프로퍼티

- [[Value]], value

  - 프로퍼티 키를 통해 프로퍼티 값에 접근하면 반환되는 값
  - 프로퍼티 키를 통해 프로퍼티 값을 변경하면 [[Value]]에 값을 재할당하고, 이때 프로퍼티 없으면 동적으로 생성하고 생성된 [[Value]]에 값을 저장

- [[Writable]], writable

  - 프로퍼티 값의 변경 기능 여부를 나타내며 불리언 값
  - [[Writable]]의 값이 false 인 경우 해당 프로퍼티의 [[Value]]의 값을 변경할 수 없음

- [[Enumerable]], enumerable

  - 프로퍼티의 열거 가능 여부를 나타내며 불리언 값
  - [[Enumerable]] 의 값이 false 인 경우 해당 프로퍼티는 for...in 문이나 Object.keys 메서드 등 열거할 수 없음

- [[Configurable]], configurable
  - 프로퍼티의 재정의 가능 여부를 나타내며 불리언값
  - [[Configurable]]의 값이 false인 경우 해당 프로퍼티의 삭제, 프로퍼티 어트리뷰트 값의 변경이 금지됨 ([[Writable]] 값이 true 인경우 [[Value]]의 변경과 [[Writable]]을 false로 변경하는 것은 허용)

```js
const person = {
  name: "Lee",
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 취득
console.log(Object.getOwnPropertyDescriptor(person, "name"));
// {value: 'Lee', writable: true, enumerable: true, configurable: true}
```

프로퍼티 생성될 때 [[value]] 의 값은 프로퍼티 값으로 초기화되며 [[Writable]], [[Enumerable]], [[Configurable]] 값은 true로 초기화

<br>

### 16.3.2 접근자 프로퍼티

자체적으로는 값을 갖지 않고, **_다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수_** 로 구성된 프로퍼티

- [[Get]], get

  - 접근자 프로퍼티를 통해 **_데이터 프로퍼티의 값을 읽을 때_** 호출되는 접근자 함수

- [[Set]], set

  - 접근자 프로퍼티를 통해 데이터 **_프로퍼티의 값을 저장할 때_** 호출되는 접근자 함수

- [[Enumerable]], enumerable

  - 데이터 프로퍼티의 [[Enumerable]]과 같다

- [[Configurable]], configurable
  - 데이터 프로퍼티의 [[Configurable]]과 같다

접근자 함수는 getter/setter 함수라고도 부름

```js
const person = {
  // 데이터 프로퍼티
  firstName: "Ungmo",
  lastName: "Lee",

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티
  // getter 함수

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },

  // setter 함수
  set fullName(name) {
    // 배열 디스트럭처링 할당: "31.1 배열 디스트럭처링 할당" 참고
    [this.firstName, this.lastName] = name.split(" ");
  },
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조
console.log(person.firstName + " " + person.lastName); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter함수가 호출
person.fullName = "Heegun Lee";
console.log(person); // {firsrName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출
console.log(person.fullName); // Heegun Lee

// firstName 은 데이터 프로퍼티
// 데이터 프로퍼티는 [[Value]] [[Writable]] [[Enumerable]] [[Configurable]]
// 프로퍼티 어트리뷰트를 갖음

let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log(descriptor);
// {value: 'Heegun', writable: true, enumerable: true, configurable: true}

// fullName은 접근자 프로퍼티
// 접근자 프로퍼티는 [[Get]] [[Set]] [[Enumerable]] [[Configurable]]
// 프로퍼티 어트리뷰트를 갖음
descript = Object.getOwnPropertyDescriptor(person, "fullName");
console.log(descript);
// {get: f, set: f, enumerable: true, configurable: true}
```

<br>

```
프로토타입은 어떤 객체의 상위 객체의 역할을 하는 객체
하위 객체에게 자신의 프로퍼티와 메서드를 상속하고, 상속 받은 하위 객체는 자신의 프로퍼티 또는 메서드처럼 자유롭게 사용 가능

프로토타입 체인은 프로토타입이 단방향 링크드 리스트 형태로 연결되어 있는 상속 구조 - 19장 "프로토타입"에서 자세히
```

<br>

접근자 프로퍼티와 데이터 프로퍼티 구별

```js
// 일반 객체의 __proto__는 접근자 프로퍼티
Object.getOwnPropertyDescriptor(Object.prototype, "__proto__");
// {get: f, set: f, enumerable: false, configurable: true}

// 함수 객체의 __proto__는 데이터 프로퍼티
Object.getOwnPropertyDescriptor(function () {}, "prototype");
// {value: {...}, writable:true , enumerable: false, configurable: true}
```

접근자 프로퍼티와 데이터 프로퍼티의 프로퍼티 디스크립터 객체의 프로퍼티가 다름

<br>
<br>

## 16.4 프로퍼티 정의

새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의 하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의 하는 것을 의미

```js
const person = {};

// Object.denfineProperty 메서드 사용하여 프로퍼티 어트리뷰트 정의할 수 있음

// 데이터 프로퍼티 정의
Object.denfineProperty(person, "firstName", {
  valu: "Ungmo",
  writable: true,
  enumerable: true,
  configurable: true,
});

Object.denfineProperty(person, "lastName", {
  value: "Lee",
});

// 접근자 프로퍼티 정의
Object.denfineProperty(person, "fullName", {
  get() {
    return `${this.firstName} ${this.lastName}`;
  },

  set(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
  enumerable: true,
  configurable: true,
});
```

| 프로퍼티 디스크립터 객체의 프로퍼티 | 대응하는 프로퍼티 어트리뷰트 | 생략했을 때 기본값 |
| ----------------------------------- | ---------------------------- | ------------------ |
| value                               | [[Value]]                    | undefined          |
| get                                 | [[Value]]                    | undefined          |
| set                                 | [[Value]]                    | undefined          |
| writable                            | [[Value]]                    | false              |
| enumerable                          | [[Value]]                    | false              |
| configurable                        | [[Value]]                    | false              |

<br>

`Object.defineProperties`로 한번에 여러개 프로퍼티 정의 가능

```js
const person = {};

Object.defineProperties(person, {
  // 데이터 프로퍼티 정의
  firstName: {
    value: 값,
    writable: true,
    enumerable: true,
    configurable: true,
  },
  lastName: {
    value: 값,
    writable: true,
    enumerable: true,
    configurable: true,
  },
  // 접근자 프로퍼티 정의
  fullName: {
    get() { ... },
		set(name) { ... },
	  enumerable: boolean,
	  configurable: boolean
  }
});
```

<br>
<br>

## 16.5 객체 변경 방지

객체는 재할당 없이 직접 변경할 수 있기때문에 객체의 변경을 방지하는 다양한 메서드를 제공한다.

| 구분           | 메서드                   | 프로퍼티 추가 | 프로퍼티 삭제 | 프로퍼티 값 읽기 | 프로퍼티 값 쓰기 | 프로퍼티 어트리뷰트 재정의 |
| -------------- | ------------------------ | ------------- | ------------- | ---------------- | ---------------- | -------------------------- |
| 객체 확장 금지 | Object.preventExtensions | X             | O             | O                | O                | O                          |
| 객체 밀봉      | Object.isSealed          | X             | X             | O                | O                | X                          |
| 객체 동결      | Object.freeze            | X             | X             | O                | X                | X                          |

### 16.5.1 객체 확장 금지

```js
Object.preventExtensions(객체);

// 확장 가능한 객체인지 판단 여부 메서드 -> boolean
Object.isExtensible(객체);
```

<br>

### 16.5.2 객체 밀봉

프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지 (읽기, 쓰기만 가능)

```js
Object.seal(객체);

// 밀봉된 객체인지 판단 여부 메서드 -> boolean
Object.isSealed(객체);

// 프로퍼티 값 갱신은 가능
객체.name = "kim";
console.log(person); // {name: 'kim'}
```

<br>

### 16.5.3 객체 동결

동결된 객체는 **_읽기만 가능_**

```js
Object.freeze(객체);

// 동결된 객체인지 판단 여부 메서드 -> boolean
Object.isFrozen(객체);
```

<br>

### 16.5.4 불변 객체

앞서 확인한 객체 변경 방지 메서드들은 모두 **_얕은(Shallow) 변경 방지_** 로 직속 프로퍼티만 변경이 방지된다.

**중접 객체까지는 영향을 줄 수 없음**

> 이를 해결하기 위해서는 객체를 값으로 갖는 모든 프로퍼티에 재귀적으로 `Object.freeze` 메서드를 적용

```js
const person = {
  name: "Lee",
  address: {
    city: "Seoul",
  },
};

// 얕은 객체 동결
Object.freeze(person);

console.log(Object.isFrozen(person)); // true
console.log(Object.isFrozen(person.address)); // false, 중첩된 address 프로퍼티에 대해서는 freeze 되지 않음

person.address.city = "Busan";
console.log(person); // { name: 'Lee', address: { city: 'Busan' }} → 프로퍼티 값이 갱신되었다.

// 💡 깊은 객체 변경 방지(Deep)
function deepFreezen(target) {
  if (target && typeof target === "object" && !Object.isFrozen(target)) {
    Object.freeze(target);

    // 모든 프로퍼티 순회하며 재귀적으로 동결
    Object.keys(target).forEach((key) => deepFreezen(target[key]));
  }
  return target;
}

const person = {
  name: "Lee",
  address: {
    city: "Seoul",
  },
};

// 깊은 객체 동결
deepFreezen(person);

console.log(Object.isFrozen(person)); // true
console.log(Object.isFrozen(person.address)); // true

person.address.city = "Busan";
console.log(person); // 중첩 객체의 프로퍼티 값도 갱신되지 않음
```

<br>
