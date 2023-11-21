# 34장 이터러블

## 34.1 이터레이션 프로토콜

순회 가능한(iterable) 데이터 컬렉션을 만들기 위해 미리 약속한 규칙

for문, for…in문, forEach 메서드

**이터러블 프로토콜**

- for…of문, 스프레드 문법, 배열 디스트럭처링 할당대상으로 사용 가능

**이터레이터 프로토콜**

- next메서드 소유
  - value와 done 프로퍼티를 갖는 이터레이터 리절트 객체 반환
- 이터러블의 요소를 탐색하기 위한 포인터 역할

### 34.1.1 이터러블

```jsx
const isIterable = (v) =>
  v !== null && typeof v[Symbol.iterator] === "function";

// 배열, 문자열, Map, Set 등은 이터러블이다.
isIterable([]); // -> true
isIterable(""); // -> true
isIterable(new Map()); // -> true
isIterable(new Set()); // -> true
isIterable({}); // -> false
```

- `for…of` 문으로 순회 가능
- `스프레드 문법`과 `배열 디스트럭처링 할당의 대상`으로 사용 가능
- 일반 객체는 이터러블 x

```jsx
const obj = { a: 1, b: 2 };

// 스프레드 프로퍼티 제안(Stage 4)은 객체 리터럴 내부에서 스프레드 문법의 사용을 허용한다.
console.log({ ...obj }); // { a: 1, b: 2 }
```

### 34.1.2 이터레이터

- next 메서드 소유
  - 이터러블의 각 요소를 순회하기 위한 포인터 역할
  - 이터레이터 리절트 객체 반환
  ```jsx
  // 배열은 이터러블 프로토콜을 준수한 이터러블이다.
  const array = [1, 2, 3];

  // Symbol.iterator 메서드는 이터레이터를 반환한다. 이터레이터는 next 메서드를 갖는다.
  const iterator = array[Symbol.iterator]();

  // next 메서드를 호출하면 이터러블을 순회하며 순회 결과를 나타내는 이터레이터 리절트 객체를 반환한다.
  // 이터레이터 리절트 객체는 value와 done 프로퍼티를 갖는 객체다.
  console.log(iterator.next()); // { value: 1, done: false }
  console.log(iterator.next()); // { value: 2, done: false }
  console.log(iterator.next()); // { value: 3, done: false }
  console.log(iterator.next()); // { value: undefined, done: true }
  ```

## 34.4 유사 배열 객체

- 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고 length 프로퍼티를 갖는 객체를 뜻함
- 이터러블이 아닌 일반 객체!
- `Array.from()` : 유사 배열 객체를 배열로 변환

```jsx
// 유사 배열 객체
const arrayLike = {
  0: 1,
  1: 2,
  2: 3,
  length: 3,
};

// Array.from은 유사 배열 객체 또는 이터러블을 배열로 변환한다
const arr = Array.from(arrayLike);
console.log(arr); // [1, 2, 3]
```

## 34.5 이터레이션 프로토콜의 필요성

순회 가능한 데이터 컬렉션을 이터레이션 프로토콜을 준수하는 이터러블로 통일하여 `for…of문`, `스프레드 문법`, `배열 디스트럭처링 할당의 대상`으로 사용할 수 있도록 일원화했다.

`데이터 소비자`(for…of, 스프레드 문법, 배열 디스트럭처링 할당, Map/Set 생성자)가 효율적으로 다양한 `데이터 공급자`(Array, String, Map/Set, DOM 컬렉션)를 사용 → `인터페이스 역할`

## 34.6 사용자 정의 이터러블

### 34.6.1 사용자 정의 이터러블 구현

```jsx
// 피보나치 수열을 구현한 사용자 정의 이터러블
const fibonacci = {
  [Symbol.iterator]() {
    let [pre, cur] = [0, 1];
    const max = 10; // 수열의 최대값
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { value: cur, done: cur >= max };
      },
    };
  },
};

for (const num of fibonacci) {
  console.log(num);
}
```

### 34.6.4 무한 이터러블과 지연 평가

```jsx
// 무한 이터러블을 생성하는 함수
const fibonacciFunc = function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre + cur];
      // 무한을 구현해야 하므로 done 프로퍼티를 생략한다.
      return { value: cur };
    },
  };
};

// fibonacciFunc 함수는 무한 이터러블을 생성한다.
for (const num of fibonacciFunc()) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...4181 6765
}

// 배열 디스트럭처링 할당을 통해 무한 이터러블에서 3개의 요소만 취득한다.
const [f1, f2, f3] = fibonacciFunc();
console.log(f1, f2, f3); // 1 2 3
```

무한 이터러블과 지연 평가를 활용하면 필요한 시점에 데이터를 생성하여 빠른 실행 속도와 불필요한 메모리 소비를 최소화 가능
