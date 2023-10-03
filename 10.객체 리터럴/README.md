# 10장 객체 리터럴

## 객체

---

<aside>
💡 자바스크립트는 객체 기반의 언어, 자바스크립의 거의 모든 것이 객체

</aside>

- 원시 값을 제외한 `함수`, `배열`, `정규 표현식` 등은 모두 객체
- `원시 타입`은 `하나의 값`만 나타내지만 `객체 타입`은 `다양한 타입의 값`을 하나의 단위로 구성한 복합적인 자료구조
- `원시 타입의 값` ⇒ `변경 불가능 (immutable value)`
- `객체 타입의 값` ⇒ `변경 가능 (mutable value)`
- 객체는 0개 이상의 프로퍼티로 구성된 집합, 프로퍼티는 `key: value` 로 구성

```jsx
var person = {
  name: "Lee", // 프로퍼티
  age: 20, // 프로퍼티
};
// 프로퍼티 키: name, age
// 프로퍼티 값: 'Lee', 20
```

- 함수도 프로퍼티의 값으로 설정 가능 → 메서드

```jsx
var counter = {
  num: 0, // 프로퍼티
  increase: function () {
    // 메서드
    this.num++;
  },
};
```

<aside>
💡 `프로퍼티`: 객체의 `상태`를 나타내는 값

`메서드`: 프로퍼티(상태 데이터)를 `참조`하고 `조작`할 수 있는 `동작`

</aside>

## 객체 리터럴에 의한 객체 생성

---

- 자바스크립트에서 지원하는 객체 생성 방법
  - `객체 리터럴`, Object 생성자 함수, 생성자 함수, Object.create 메서드, 클래스(ES6)
- 객체 리터럴: 가장 일반적이고 간단한 방법
  - 리터럴: 사람이 이해할 수 있는 문자 또는 약속된 기호를 사용하여 값을 생성
  - 중괄호({…}) 내에 0개 이상의 프로퍼티를 정의
  - 변수에 할당되는 시점에 자바스크립트 엔진은 객체 리터럴을 해석해 객체를 생성
  - `값으로 평가되는 표현식`

```jsx
var counter = {
  num: 0, // 프로퍼티
  increase: function () {
    // 메서드
    this.num++;
  },
};

console.log(typeof counter); // object
console.log(counter); // {num: 0, increase: f}
```

- 중괄호 내에 프로퍼티를 정의하지 않으면 빈 객체가 생성

```jsx
var empty = {}; // 빈 객체
console.log(typeof empty); // object
```

## 프로퍼티

---

<aside>
💡 **객체는 프로퍼티의 집합이며, 프로퍼티는 키와 값으로 구성**

</aside>

```jsx
var person = {
  name: "Lee", // 프로퍼티
  age: 20, // 프로퍼티
};
// 프로퍼티 키: name, age
// 프로퍼티 값: 'Lee', 20
```

<aside>
💡 프로퍼티를 나열할 때 쉼표로 구분, 일반적으로 마지막 프로퍼티 뒤에는 쉼표를 사용하지 않으나 사용해도 상관 x

</aside>

- `프로퍼티 키`: 빈 문자열을 포함하는 모든 `문자열` 또는 `심벌` 값
- `프로퍼티 값`: 자바스크립트에서 `사용할 수 있는 모든 값`
- `식별자 네이밍 규칙을 따르지 않는` 이름에는 반드시 `따옴표`를 사용해야 함
- 사용 가능한 `유효한 이름`인 경우, 따옴표 `생략 가능`

<aside>
💡 **가급적 식별자 네이밍 규칙을 준수하는 프로퍼티 키를 사용!!**

</aside>

```jsx
var person = {
	firstName: 'Sein',   // 식별자 네이밍 규칙을 준수하는 프로퍼티 키
	'last-name': 'Kim',  // 식별자 네이밍 규칙 준수하지 않는 프로퍼티 키
	last-name: 'Kim'
   // 식별자 네이밍 규칙 준수 x (Uncaught SyntaxError: Unexpected token '-')
   // - 연산을 함

};
// 프로퍼티 키: name, age
// 프로퍼티 값: 'Lee', 20
```

```jsx
// 프로퍼티 키 동적 생성 가능
var obj = {};
var key = "hello";
obj[key] = "world";

// 빈 문자열을 프로퍼티 키로 사용 가능 (키로서의 의미 x, 권장 x)
var foo = {
  "": "",
};
// {"":""}

// 심벌 값 외의 값을 사용하면 암묵적 타입 변환을 통해 문자열이 됨
var foo = {
  0: 1,
  1: 2,
  2: 3,
};
// {0: 1, 1: 2, 2: 3}

// var, function과 가은 예약어 사용 가능 (권장x)
var foo = {
  var: "",
  function: "",
};
// {var: '', function: ''}

// 이미 존재하는 프로퍼티 키를 중복 선언 => 나중에 선언한 프로퍼티가 먼저 선언한 프로퍼티를 덮어씀
var foo = {
  name: "Lee",
  name: "Kim",
};
// {name: 'Kim'}
```

## 메서드

---

- `프로퍼티 값이 함수`일 경우 일반 함수와 구분하기 위해 `메서드`라 부름

```jsx
var circle = {
  radius: 5,

  getDiameter: function () {
    // 메서드
    return 2 * this.radius; // this키워드(22장)
  },
};
console.log(circle.getDiameter()); // 10
```

## 프로퍼티 접근

---

1. `마침표 표기법`: 마침표 프로퍼티 접근 연산자`(.)`를 사용
2. `대괄호 표기법`: 대괄호 프로퍼티 접근 연산자 `([…])`를 사용

```jsx
// 1. 마침표 표기법: 마침표 프로퍼티 접근 연산자(.)를 사용
console.log(person.name);

// 2. 대괄호 표기법: 대괄호 프로퍼티 접근 연산자 ([…])를 사용
console.log(person['name']); // 따옴표로 감싸야함
console.log(person[name]));  // Uncaught ReferenceError: name is not defined
```

- 객체에 `존재하지 않는` 프로퍼티에 접근하면 `undefined` 반환 (ReferenceError 발생 x)

### 프로퍼티 삭제

---

- delete 연산자: `delete person.age`

## ES6에서 추가된 객체 리터럴의 확장 기능

---

### 1. 프로퍼티 축약 표현

프로퍼티 값으로 변수를 사용하는 경우 `변수 이름`과 `프로퍼티 키가 동일`한 이름일 때 `프로퍼티 키 생략 가능` ⇒ 변수 이름 자동 생성

```jsx
let x = 1,
  y = 2;
// ES5
const obj = { x: x, y: y };
// ES6
const obj = { x, y };
// {x: 1, y: 2}
```

### 2. 계산된 프로퍼티 이름

```jsx

const prefix = 'prop';
let i = 0;
// ES5
const obj = {
	[prefix + '-' + ++i]: i,
	[prefix + '-' + ++i]: i,
	[prefix + '-' + ++i]: i
}
// ES6
const obj = {
	[`${prefix}-${++i}`]: i,
	[`${prefix}-${++i}`]: i,
	[`${prefix}-${++i}`]: i
}
console.log(obj}; // {prop-1: 1, prop-2: 2, prop-3; 3}
```

### 3. 메서드 축약 표현

```jsx
// ES5
const obj = {
	name: 'Lee',
	sayHi: function () {
		console.log('Hi'! + this.name);
	}
}

// ES6
const obj = {
	name: 'Lee',
	// 메서드 축약 표현
	sayHi() {
		console.log('Hi'! + this.name);
	}
}
```
