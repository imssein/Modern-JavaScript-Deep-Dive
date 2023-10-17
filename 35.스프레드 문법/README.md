# 35장 스프레트 문법

- ES6에서 도입
- 하나로 뭉쳐 있는 여러 값들의 집합을 펼쳐서 개별적인 값들의 목록으로 만듬
- Array, String, Map, Set, Dom 컬렉션(NodeList, HTMLCollection), arguments와 같이 for…of 문으로 순회할 수 있는 이터러블에 한정
- 스프레드 문법의 결과는 변수에 할당 불가능

## 함수 호출문의 인수 목록에서 사용하는 경우

---

- `Rest 파라미터`: 함수에 전달된 `인수들의 목록`을 `배열로 전달`받기 위해 `매개변수` `이름 앞`에 `…`을 붙이는 것
- `스프레드 문법`: 여러 개의 값이 하나로 뭉쳐 있는 `배열`과 같은 이터러블을 펼쳐서 `개별적인 값`들의 목록을 만드는 것

```jsx
const arr = [1, 2, 3];

const max = Math.max(...arr); // 3
```

## 배열 리터럴 내부에서 사용하는 경우

---

### concat

---

2개의 배열을 1개의 배열로 결합하고 싶은 경우 사용

```jsx
// ES5
var arr = [1, 2].concat([3, 4]);

// ES6
const arr = [...[1, 2], ...[3, 4]];
console.log(arr); // [1, 2, 3, 4]
```

### splice

---

배열의 중간에 다른 배열의 요소들을 추가하거나 제거할 경우 사용

```jsx
// ES5
var arr1 = [1, 4];
var arr2 = [2, 3];

arr1.splice(1, 0, arr2);
console.log(arr1); // 1, [2, 3], 4

// ES6
arr1.splice(1, 0, ...arr2);
console.log(arr1);
```

### 배열 복사

---

```jsx
// ES5
var origin = [1, 2];
var copy = origin.slice();

// ES6
const copy = [...origin];

console.log(copy); // [1, 2]
console.log(copy === origin); // false
```

### 이터러블을 배열로 변환

---

이터러블을 배열로 변환하려면 `Function.prototype.apply` 또는 `Function.prototype.call` 메서드를 사용하여 slice 메서드 호출

```jsx
// ES5
function sum() {
  var args = Array.prototype.slice.call(arguments);

  return args.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 2, 3)); // 6
```

```jsx
// 이터러블이 아닌 유사 배열 객체
const arrayLike = {
  0: 1,
  1: 2,
  2: 3,
  length: 3,
};

const arr = Array.prototype.slice.call(arrayLike); // [1, 2, 3]
console.log(arr);
console.log(Array.isArray(arr)); // true
```

`스프레드 문법`을 사용하면 좀 더 간편하게 `이터러블`을 `배열`로 변환할 수 있다. arguments 객체는 이터러블이면서 유사 배열 객체다.

```jsx
function sum() {
  // 이터러블이면서 유사 배열 객체인 arguments를 배열로 변환
  return [...arguments].reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2, 3)); // 6
```

더 좋은 방법은 `Rest 파라미터` 사용하는 것이다.

```jsx
// Rest 파라미터 args는 함수에 전달된 인수들의 목록을 배열로 전달받는다.
const sum = (...args) => args.reduce((pre, cur) => pre + cur, 0);

console.log(sum(1, 2, 3)); // 6
```

이터러블이 아닌 유사 배열 객체는 스프레드 문법의 대상이 될 수 없다.

```jsx
const arrayLike = {
  0: 1,
  1: 2,
  2: 3,
  length: 3,
};

const arr = [...arrayLike];
console.log(arr); // TypeError: arrayLike is not iterable
```

이터러블이 아닌 `유사 배열 객체`를 `배열`로 변경하려면 `Array.from` 메서드를 사용

```jsx
Array.from(arrayLike); // [1, 2, 3]
```

## 객체 리터럴 내부에서 사용하는 경우

---

스프레드 문법 대상은 이터러블이어야 하지만 스프레드 프로퍼티 제안은 일반 `객체` 대상으로도 문법의 사용을 허용한다.

```jsx
// 스프레드 프로퍼티
// 객체 복사(얕은 복사)
const obj = { x: 1, y: 2 };
const copy = { ...obj };
console.log(copy); // {x: 1, y: 2}
console.log(obj === copy); // false

// 객체 병합
const merged = { x: 1, y: 2, ...{ a: 3, b: 4 } };
console.log(merged); // {x: 1, y: 2, a: 3, b: 4}
```

스프레드 프로퍼티가 도입되기 전에는 ES6에서 도입된 `Object.assign` 메서드를 사용하여 여러 개의 `객체`를 `병합`하거나 `특정 프로퍼티`를 `변경` 또는 `추가`했다.

```jsx
//  프로퍼티가 중복되는 경우 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = Object.assign({}, { x: 1, y: 2 }, { y: 10, z: 3 });
console.log(merged); // {x: 1, y: 10, z: 3}

// 특정 프로퍼티 변경
const changed = Object.assign({}, { x: 1, y: 2 }, { y: 100 });
console.log(changed); // {x: 1, y: 100}

// 프로퍼티 추가
const added = Object.assign({}, { x: 1, y: 2 }, { z: 0 });
console.log(added); // {x: 1, y: 2, z: 0}
```

```jsx
//  객체 병합, 프로퍼티가 중복되는 경우 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = { ...{ x: 1, y: 2 }, ...{ y: 10, z: 3 } };
console.log(merged);

// 특정 프로퍼티 변경
const changed = { ...{ x: 1, y: 2 }, y: 100 };
console.log(changed);

// 프로퍼티 추가
const added = { ...{ x: 1, y: 2 }, z: 0 };
console.log(added);
```
