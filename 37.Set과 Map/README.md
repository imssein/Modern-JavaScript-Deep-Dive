# 37장 Set과 Map

## Set

---

- Set: 중복되지 않는 유일한 값들의 집합

| 구분               | 배열 | Set 객체 |
| ------------------ | ---- | -------- |
| 중복값 허용        | o    | x        |
| 순서 의미          | o    | x        |
| 인덱스로 요소 접근 | o    | x        |

### Set 객체의 생성

---

```jsx
const set = new Set();
console.log(set); // Set(0) {}
```

- 이터러블을 인수로 전달
- 이터러블의 중복된 값은 저장 x

```jsx
const set1 = new Set([1, 2, 3, 4]);
console.log(set1); // Set(4) {1, 2, 3, 4}

const set2 = new Set("hello");
console.log(set2); // Set(4) {'h', 'e', 'l', 'o'}
```

- 배열의 중복 요소 제거

```jsx
// 배열 이용
const uniq = (array) => array.filter((v, i, self) => self.indexOf(v) === i);
console.log(uniq([2, 1, 2, 3, 4, 3, 4]));

// Set 이용
const uniq = (array) => [...new Set(array)];
console.log(uniq([2, 1, 2, 3, 4, 3, 4]));
```

### 요소 개수 확인

---

- `Set.prototype.size` 프로퍼티 사용

```jsx
const { size } = new Set([1, 2, 3, 3]);
console.log(size);
```

- 요소 개수 변경 불가능

### 요소 추가

---

- `Set.prototype.add` 메서드 사용

```jsx
const set = new Set();
console.log(set);

set.add(1);
console.log(set);
```

- `연속적`으로 add 메서드 사용 가능

```jsx
set.add(1).(2)
```

- 중복된 요소 추가 할 경우 → 에러없이 무시
- 일치 비교 연산자: NaN과 ≠ NaN, +0 === -0
- Set 객체: NaN === NaN, +0 === -0

- 자바스크립트의 모든 값을 요소로 저장 가능

### 요소 존재 여부 확인

---

- `Set.prototype.has` 메서드 사용
- 존재 여부를 나타내는 불리언 값을 반환

```jsx
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(4)); // false
```

### 요소 삭제

---

- `Set.prototype.delete` 메서드 사용
- 삭제 성공 여부를 나타내는 불리언 값을 반환
- 삭제하는 인덱스가 아닌 요소값을 인수로 전달해야한다. (순서 의미x, 인덱스x)

```jsx
const set = new Set([1, 2, 3]);

set.delete(3);
```

- 존재하지 않는 요소 삭제 → 에러없이 무시
- 연속적으로 호출 불가능

### 요소 일괄 삭제

---

- `Set.prototype.clear` 메서드 사용
- undefined 반환

```jsx
const set = new Set([1, 2, 3]);

set.clear();
console.log(set);
```

### 요소 순회

---

- `Set.prototype.forEach` 메서드 사용
- 콜백 함수는 3개의 인수 전달 받음 (Array.forEach메서드와 인터페이스 통일하기 위함, 의미 없음)
  - 첫 번째 인수: 현재 순회 중인 요소값
  - 두 번째 인수: 현재 순회 중인 요소값
  - 세 번째 인수: 현재 순회 중인 Set 객체 자체

```jsx
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));
```

- Set 객체는 `이터러블`
  - `for…of` 문으로 순회 가능
  - `스프레드 문법`, `배열 디스트럭처링의 대상`

```jsx
const set = new Set([1, 2, 3]);

for (const value of set) {
  console.log(value);
}

console.log([...set]);

const [a, ...rest] = set;
console.log(a, rest);
```

### 집합 연산

---

교집합, 합집합, 차집합 구현 가능

**교집합**

```jsx
Set.prototype.intersection = function (set) {
  const result = new Set();

  for (const value of set) {
    if (this.has(value)) result.add(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.intersection(setB)); // Set(2) {2, 4}
console.log(setB.intersection(setA)); // Set(2) {2, 4}
```

```jsx
Set.prototype.intersection = function (set) {
  return new Set([...this].filter((v) => set.has(v)));
};
```

**합집합**

```jsx
Set.prototype.union = function (set) {
  const result = new Set(this);

  for (const value of set) {
    result.add(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
```

```jsx
Set.prototype.union = function (set) {
  return new Set([...this, ...set]);
};
```

**차집합**

```jsx
Set.prototype.difference = function (set) {
  const result = new Set(this);

  for (const value of set) {
    result.delete(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.difference(setB)); // Set(2) {1, 3}
console.log(setB.difference(setA)); // Set(0) {size: 0}
```

```jsx
Set.prototype.difference = function (set) {
  return new Set([...this].filter((v) => !set.has(v)));
};
```

**부분 집합과 상위 집합**

집합 A가 B에 포함되는 경우, A는 B의 부분 집합이며, 집합 B는 A의 상위 집합이다.

```jsx
Set.prototype.isSuperset = function (subset) {
  for (const value of subset) {
    if (!this.has(value)) return false;
  }

  return true;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA가 setB의 상위 집합인지 확인
console.log(setA.isSuperset(setB)); // true
console.log(setB.isSuperset(setA)); // false
```

```jsx
Set.prototype.isSuperset = function (subset) {
  const supersetArr = [...this];
  return [...subset].every((v) => supersetArr.includes(v));
};
```

## Map

---

- Map: 키와 값의 쌍으로 이루어진 컬렉션

| 구분                   | 객체                    | Map 객체              |
| ---------------------- | ----------------------- | --------------------- |
| 키로 사용할 수 있는 값 | 문자열 또는 심벌 값     | 객체를 포함한 모든 값 |
| 이터러블               | x                       | o                     |
| 요소 개수 확인         | Object.keys(obj).length | map.size              |

### Map 객체의 생성

---

```jsx
const map = new Map();
console.log(map); // Map(0) {size: 0}
```

- 이터러블을 인수로 전달받아 Map 객체 생성
- 키와 값의 쌍으로 이루어진 요소

```jsx
const map1 = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);
console.log(map1); // Map(2) {'key1' => 'value1', 'key2' => 'value2'}

const map2 = new Map([1, 2]); // TypeError: Iterator value 1 is not an entry object
```

- 중복된 키를 갖는 요소가 존재하면 값이 덮어써짐

### 요소 개수 확인

---

- `Map.prototype.size` 프로퍼티 사용

```jsx
const { size } = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);
console.log(size); // 2
```

- 요소 개수 변경 불가능

### 요소 추가

---

- `Map.prototype.set` 메서드

```jsx
const map = new Map();
console.log(map); // Map(0) {size: 0}

map.set("key1", "value1");
console.log(map); // Map(1) {'key1' => 'value1'}
```

- 연속적으로 호출 가능
- 중복된 키를 갖는 요소 존재 불가능, 추가 될 경우 값이 덮어써지고 에러 발생 x
- 일치 비교 연산자: NaN !== NaN, +0 === -0
- Map: NaN === NaN, +0 === -0
- `Map 객체의 키 타입에 제한이 없음 !!!`

### 요소 취득

---

- `Map.prototype.get` 메서드 사용
- 인수로 키를 전달하여 값을 반환
- 키를 갖는 요소가 없다면 undefined 반환

```jsx
const map = new Map();

const lee = { name: "Lee" };
const kim = { name: "kim" };

map.set(lee, "developer").set(kim, "designer");

console.log(map.get(lee)); // developer
console.log(map.get("key")); // undefined
```

### 요소 존재 여부 확인

---

- `Map.prototype.has` 메서드 사용
- 존재 여부를 불리언 값으로 반환

```jsx
console.log(map.has(lee)); // true
console.log(map.has("key")); // false
```

### 요소 삭제

---

- Map.prototype.delete 메서드 사용
- 삭제 성공 여부를 불리언 값으로 반환

```jsx
map.delete(kim);
```

- 존재하지 않는 키로 삭제할 경우 에러없이 무시
- 연속적으로 호출 불가능

### 요소 일괄 삭제

---

- Map.prototype.clear 메서드 사용
- undefined 반환

```jsx
map.clear();
```

### 요소 순회

---

- Map.prototype.forEach
- 콜백 함수는 3개의 인수 전달 받음
  - 첫 번째 인수: 현재 순회 중인 요소값
  - 두 번째 인수: 현재 순회 중인 요소값
  - 세 번째 인수: 현재 순회 중인 Map 객체 자체

```jsx
const map1 = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);

map1.forEach((v, k, map) => console.log(v, k, map));
```

- Map 객체는 `이터러블`
  - `for…of` 문으로 순회 가능
  - `스프레드 문법`, `배열 디스트럭처링의 대상`

```jsx
const map = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);

for (const entry of map) {
  console.log(entry);
}

console.log([...map]);

const [a, b] = map;
console.log(a, b);
```

- `Map.keys()`: 요소 키를 값으로 갖는 객체 반환
- `Map.values()`: 요소 값을 값으로 갖는 객체 반환
- `Map.entries()`: 요소 키와 요소 값을 값으로 갖는 객체 반환

```jsx
const map = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);

for (const key of map.keys()) {
  console.log(key);
}

for (const value of map.values()) {
  console.log(value);
}

for (const entry of map.entries()) {
  console.log(entry);
}
```
