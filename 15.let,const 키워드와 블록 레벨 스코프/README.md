# 15장 let, const 키워드와 블록 레벨 스코프

## var 키워드로 선언한 변수의 문제점

---

### 변수 중복 선언 허용

---

```jsx
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용
// 초기화문이 있는 변수 선언문은 JS 엔진에 의해 var 키워드가 없는 것처럼 동작
var x = 100;
// 초기화문이 없는 변수 선언문은 무시
var y;

console.log(x); // 100
console.log(y); // 1
```

### 함수 레벨 스코프

---

`var` 키워드로 선언한 변수는 오로지 `함수의 코드 블록`만을 `지역 스코프`로 인정

`함수 외부`에서 `var` 키워드로 선언한 변수는 `코드 블록 내`에서 선언해도 모두 `전역 변수`가 된다.

```jsx
var x = 1;

if (true) {
  // x는 전역 변수다. 중복 선언된다.
  // 의도치 않게 변수값이 변경
  var x = 10;
}

console.log(x); // 10
```

```jsx
var i = 10;

for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

console.log(i); // 5
```

### 변수 호이스팅

---

- 변수 호이스팅
  - 변수 선언문이 `스코프의 선두로 끌어 올려진 것`처럼 동작한다.
  - `변수 선언문 이전`에 `참조`할 수 있다.
  - 할당문 이전에 변수를 참조하면 `undefined`가 반환한다.

```jsx
console.log(foo); // undefined

foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행
var foo;
```

> 🍏 `변수 선언문 이전`에 `변수를 참조`하는 것은 `변수 호이스팅`에 의해 `에러를 발생시키지는 않지만` 프로그램의 흐름상 맞지 않을뿐더러 `가독성`을 떨어뜨리고 `오류`를 발생시킬 여지를 남긴다

## let 키워드

---

### 변수 중복 선언 금지

---

`let` 키워드로 이름이 같은 변수를 `중복 선언`하면 문법 `에러`가 발생한다.

```jsx
var foo = 123;
var foo = 456;

let bar = 123;
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

### 블록 레벨 스코프

---

```jsx
let foo = 1;
{
  let foo = 2;
  let bar = 3;
}
console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

전역에서는 bar 변수를 참조할 수 없다.

### 변수 호이스팅

---

- `let`키워드로 선언한 변수는 `변수 호이스팅`이 발생하지 않는 것처럼 동작
- 변수 선언문 이전에 `참조`하면 `ReferenceError`가 발생

```jsx
console.log(foo); // SyntaxError: Identifier 'foo' has already been declared
let foo;
```

> 🍏 `let` 키워드로 선언한 변수는 `“선언 단계”`와 `“초기화 단계”`가 분리되어 진행된다.

- 일시적 사각지대: 스코프의 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간
- `let`키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작하지만, 그렇지 않다!

```jsx
let foo = 1;
{
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  let foo = 2;
}
```

변수 호이스팅이 발생하지 않는다면 위 예제는 전역 변수 foo의 값을 출력해야한다. 하지만 `let`키워드로 선언한 변수도 여전히 호이스팅이 발생하기 때문에 `ReferenceError`가 발생한다.

> 🍏 `var`, `let`, `const`, `function`, `function*`, `class`는 `호이스팅` 된다.
> `let`, `const`, `class`를 사용한 선언문은 호이스팅이 `발생하지 않는 것`처럼 동작한다.

### 전역 객체와 let

---

`var`키워드로 선언한 변수, 전역 함수, 암묵적 전역은 전역 객체 `window의 프로퍼티`가 된다.

```jsx
// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

console.log(window.x); // 1
console.log(window.y); // 2
console.log(window.foo); // f foo() {}
```

`let` 키워드로 선언한 전역 변수는 `전역 객체의 프로퍼티`가 아니며 window.foo와 같이 `접근 할 수 없다`.

```jsx
let x = 1;

console.log(window.x); // undefined
```

## const 키워드

---

- `상수`를 선언하기 위해 사용

### 선언과 초기화

---

- `const` 키워드로 선언한 변수는 반드시 `선언`과 `동시에` `초기화`해야 한다.
- `블록 레벨 스코프`를 가지며, `변수 호이스팅이 발생하지 않는 것`처럼 동작한다.

```jsx
{
  // 변수 호이스팅이 발생하지 않는 것처럼 동작한다.
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  const foo = 1;
  console.log(foo); // 1
}
// 블록 레벨 스코프
console.log(foo); // ReferenceError: foo is not defined
```

### 재할당 금지

---

`const` 키워드로 선언한 변수는 `재할당이 금지`된다.

### 상수

---

`const` 키워드로 선언한 변수에 `원시 값`을 할당한 경우 `변수 값을 변경할 수 없다`.

- 상수
  - `재할당이 금지`된 변수
  - `상태 유지`와 `가독성`, `유지보수의 편의`를 위해 적극적으로 사용해야한다.

```jsx
// 세전 가격
let preTaxPrice = 100;

// 세후 가격
// 0.1의 의미를 명확히 알기 어렵기 때문에 가독성이 좋지 않다.
let afterTaxPrice = preTaxPrice + preTaxPrice * 0.1;

console.log(afterTaxPrice);
```

→ 0.1의 의미를 명확히 알 수 없기때문에 고정된 값을 사용하는 것이 좋다.

`const` 키워드로 선언된 변수에 `원시 값`을 할당한 경우 원시 값은 `변경할 수 없는 값`(immutable value)이고 const 키워드에 의해 `재할당이 금지`되므로 할당된 값을 변경할 수 있는 방법은 없다.

상수의 이름은 `대문자`로 선언해 상수임을 명확히 나타낸다. 여러 단어로 이뤄진 경우에는 `언더스코어로(_)`로 구분해서 스네이크 케이스로 표현하는 것이 일반적

```jsx
const TAX_RATE = 0.1;
```

### const 키워드와 객체

---

`const` 키워드로 선언된 변수에 `객체`를 할당한 경우 `값을 변경 가능`하다.

`원시 값`은 `재할당` 없이 `변경`할 수 있는 방법은 없지만 `변경 가능한 값`인 `객체`는 재할당 없이도 직접 `변경 가능`하다.

```jsx
const person = {
  name: "Lee",
};

person.name = "Kim";

console.log(person); // {name: 'Kim'}
```

`**const` 키워드는 재할당을 금지할 뿐 “불변”을 의미하지 않는다.\*\*

### var vs let vs const

---

`변수 선언`에는 기본적으로 `const`를 사용하고 `let`은 `재할당`이 필요한 경우에 한정해 사용하는 것이 좋다. `const`키워드를 사용하면 의도치 않은 `재할당을 방지`하기 때문에 좀 더 안전하다.

- `ES6`를 사용한다면 `var` 키워드로 `사용하지 않는다`.
- `재할당`이 필요한 경우에 한정해 `let` 키워드를 사용한다. 변수의 `스코프`는 최대한 `좁게` 만든다.
- 변경이 발생하지 않고 `읽기 전용`으로 사용하는(재할당이 필요 없는 상수) `원시 값`과 `객체`에는 `const` 키워드를 사용한다. `const` 키워드는 `재할당을 금지`하므로 `var`, `let` 키워드보다 안전하다.

> 🍏 객체는 의외로 재할당하는 경우가 드물다.
> `변수`를 선언할 때 먼저 `const`키워드로 이용하고, `재할당`이 필요하다면 `let` 키워드로 변경하자!
