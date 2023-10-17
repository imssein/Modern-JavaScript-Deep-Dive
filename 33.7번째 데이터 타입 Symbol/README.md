# 33장 7번째 데이터 타입 Symbol

- 변경 불가능한 원시 타입의 값
- 다른 값과 중복되지 않는 유일무이한 값 → 유일한 프로퍼티 키를 만들기 위해 사용

## 심벌 값의 생성

---

### Symbol 함수

---

- 심벌 값
  - `Symbol 함수`를 `호출`하여 생성
  - `외부로 노출되지 않아 확인 x`
  - 다른 값과 절대 중복되지 않는 `유일무이한 값`

```jsx
const mySymbol = Symbol();
console.log(typeof mySymbol); // symbol
// 외부로 노출되지 않아 확인 불가능
console.log(mySymbol); // Symbol();
```

- new 연산자와 함께 호출하지 않음
- 문자열을 인수로 전달 가능
  - 심벌 값에 대한 설명으로 디버깅 용도로만 사용
  - 심벌 값에 대한 설명이 같더라도 생성된 심벌 값은 유일무이한 값

```jsx
const mySymbol1 = Symbol("mySymbol");
const mySymbol2 = Symbol("mySymbol");

cosole.log(mySymbol1 === mySymbol2); // false
```

- 래퍼 객체(원시값을 객체처럼 다룰 수 있도록 하는 객체)를 생성
- `Symbol.prototype`의 프로퍼티(`description 프로퍼티`, `toString 메서드`)

```jsx
console.log(mySymbol.description); // mySymbol
console.log(mySymbol.toString()); // Symbol(mySymbol)
```

심벌 값은 `암묵적`으로 `문자열`이나 `숫자 타입`으로 `변환되지 않는다.`

```jsx
console.log(mySymbol + "");
console.log(+mySymbol);

console.log(!!mySymbol); // true
```

`불리언 타입`으로는 `암묵적`으로 타입 `변환`된다.

### Symbol.for / Symbol.keyFor 메서드

---

`Symbol.for` 메서드: 인수로 전달받은 `문자열`을 `키`로 사용하여 키와 심벌 값의 쌍들이 저장되어 있는 전역 심벌 레지스트리에서 해당 키와 일치하는 심벌 값을 검색

- 검색에 성공 → 새로운 심벌 값을 생성 x, 검색된 심벌 값 반환
- 실패 → 새로운 심벌 값 생성 → `Symbol.for` 메서드의 인수로 전달된 키로 전역 심벌 레지스트리에 저장한 후, 생성된 심벌 값을 반환

`Symbol.for` 메서드를 사용 → 어플리케이션 전역에서 중복되지 않는 유일무이한 상수인 심벌 값을 단 하나만 생성하여 전역 심벌 레지스트리를 통해 공유할 수 있다.

```jsx
const s1 = Symbol.for("mySymbol");
const s2 = Symbol.for("mySymbol");
console.log(s1 === s2); // true
```

`Symbol.keyFor` : 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출 할 수 있다.

```jsx
Symbol.keyFor(s1); // mySymbol

// Symbol 함수를 호출하여 생성한 심벌 값은 전역 심벌 레지스트리에 등록되어 관리 되지 않는다.
const s2 = Symbol("foo");
Symbol.keyFor(s2); // undefined
```

## 심벌과 상수

---

`상수 이름`에만 `의미`가 있고, 상수 값은 변경될 수 있고, 중복될 수도 있다.

변경/중복될 가능성이 있는 무의미한 상수 대신 중복된 가능성이 없는 `유일무이한 심벌 값`을 사용할 수 있다.

> 💡 `enum`
>
> - 명명된 숫자 상수의 집합으로 `열거형`이라고 부른다.
> - enum과 같이 사용하려면 객체의 변경을 방지하기 위해 객체를 동결하는 `Object.freeze` >메서드와 `심벌 값`을 사용한다.

```jsx
// 불변 객체이며 프로퍼티 값은 유일무이한 값
const Direction = {
  UP: Symbol("up"),
  DOWN: Symbol("down"),
  LEFT: Symbol("left"),
  RIGHT: Symbol("right"),
};
```

</aside>

## 심벌과 프로퍼티 키

---

객체의 `프로퍼티 키`는 빈 문자열을 포함하는 `모든 문자열` 또는 `심벌 값`으로 만들 수 있으며, `동적`으로 `생성`할 수 있다. 심벌 값을 사용할 때는 `대괄호`를 이용해야 한다.

```jsx
const obj = {
  [Symbol.for("mySymbol")]: 1,
};

obj[Symbol.for("mySymbol")]; // 1
```

심벌 값은 `유일무이한 값`이므로 심벌 값으로 프로퍼티 키를 만들면 `다른 프로퍼티 키`와 절대 `충돌하지 않는다.`

## 심벌과 프로퍼티 은닉

---

심벌 값은 프로퍼티 키로 사용하여 생성한 프로퍼티는 `for…in` 문이나 `Object.keys`, `Object.getOwnPropertyNames` 메서드로 찾을 수 없다.

```jsx
for (const key in obj) {
  console.log(key);
}

console.log(Object.keys(obj)); // []
console.log(Object.getOwnPropertyNames(obj)); // []
```

ES6에서 도입된 `Object.getOwnPropertySymbols` 메서드를 사용하면 심벌 값을 프로퍼티 키로 사용하여 생성한 프로퍼티를 찾을 수 있다.

```jsx
// 인수로 전달한 객체의 심벌 프로퍼티 키를 배열로 반환
console.log(Object.getOwnPropertySymbols(obj)); // [Symbol(mySymbol)]

// 심벌 값도 찾을 수 있음
const symbolkey1 = Object.getOwnPropertySymbols(obj)[0];
console.log(obj[symbolkey1]); // 1
```

## 심벌과 표준 빌트인 객체 확장

---

표준 빌트인 객체에 사용자 정의 메서드를 직접 추가하여 확장하는 것은 권장하지 않는다. 이유는 미래에 표준 사양에 추가될 메서드와 이름이 중복될 수 있기 때문이다.

대신 중복될 가능성이 없는 `심벌 값`으로 `프로퍼티 키`를 생성하여 표준 빌트인 객체를 `확장`하면 어떤 프로퍼티 키와도 `충돌할 위험이 없어` 안전하게 빌트인 객체를 확장할 수 있다.

```jsx
Array.prototype[Symbol.for("sum")] = function () {
  return this.reduce((acc, cur) => acc + cur, 0);
};

[1, 2][Symbol.for("sum")](); // 3
```
