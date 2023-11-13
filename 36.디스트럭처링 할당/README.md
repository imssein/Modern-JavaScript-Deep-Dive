# 36장 디스트럭처링 할당

- `구조 분해 할당`: 구조화된 배열과 같은 이터러블 또는 객체를 destructuring(비구조화)하여 1개 이상의 변수에 개별적으로 할당하는 것
  - 필요한 값만 추출하여 변수에 할당할 때 유용

## 36.1 배열 디스트럭처링 할당

---

```jsx
// ES5
const arr = [1, 2, 3];

const one = arr[0];
const two = arr[1];
const three = arr[2];

console.log(one, two, three);

// ES6
const [one, two, three] = arr;
console.log(one, two, three);
```

- 배열 디스트럭처링 할당의 대상(우변)은 **이터러블**
- 할당 기준은 배열의 인덱스
  - 순서대로 할당
- 변수의 개수와 이터러블의 요소 개수가 반드시 일치할 필요 x

```jsx
const [a, b] = [1, 2];
console.log(a, b); // 1 2

const [c, d] = [1];
console.log(c, d); // 1 undefined

const [e, f] = [1, 2, 3];
console.log(e, f); // 1 2
```

- 기본값 설정 가능

```jsx
// 기본값
const [a, b, c = 3] = [1, 2];
console.log(a, b, c); // 1 2 3

// 💡 기본값보다 할당된 값이 우선한다!
const [e, f = 10, g = 3] = [1, 2];
console.log(e, f, g);
```

- URL을 파싱하여 protocol, host, path 프로퍼티를 갖는 객체를 생성해 반환

```jsx
function parseURL(url = "") {
  const parsedURL = url.match(/^(\w+):\/\/([^/]+)\/(.*)$/);
  console.log(parsedURL);

  if (!parsedURL) return {};

  const [, protocol, host, path] = parsedURL;
  return { protocol, host, path };
}

const parsedURL = parseURL(
  "https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function"
);
```

- 변수에 Rest 요소 사용 가능

```jsx
const [x, ...y] = [1, 2, 3];
console.log(x, y); // 1 [2, 3]
```

## 36.2 객체 디스트럭처링 할당

---

```jsx
// ES5 - 프로퍼티 키 사용
const user = { firstName: "Sein", lastName: "Hyun" };

const firstName = user.firstName;
const lastName = user.secondName;

console.log(firstName, lastName);

// ES6
// 순서는 의미 없다.
const { lastName, firstName } = user;

// 객체의 프로퍼티 키와 다른 변수 이름으로 프로퍼티 값을 할당받으려면 다음과 같이 변수 선언해야함
const { lastName: ln, firstName: fn } = user;
console.log(fn, ln); // Sein Hyun
```

- 기본값 설정 가능

```jsx
const { lastName, firstName = "Sein" } = { lastName: "H" };

const { lastName: ln, firstName: fn = "Sein" } = { lastName: "H" };
```

- 프로퍼티 값만 추출하여 변수에 할당 할 때 유용

```jsx
const str = "Hello";

const { length } = str;
console.log(length); // 5

const todo = { id: 1, content: "HTML", completed: true };
const { id } = todo;
console.log(id);
```

- 객체를 인수로 전달받는 함수의 매개변수에도 사용 가능

```jsx
function printTodo(todo) {
  console.log(
    `할일 ${todo.content}은 ${todo.completed ? "완료" : "미완료"} 상태입니다.`
  );
}

printTodo({ id: 1, content: "HTML", completed: true });

// 디스트럭처링 할당 사용
function printTodo({ content, completed }) {
  console.log(`할일 ${content}은 ${completed ? "완료" : "미완료"} 상태입니다.`);
}
```

- 배열 요소가 객체인 경우 배열 디스트럭처링 할당과 객체 디스트럭처링 할당 혼용 가능

```jsx
const todos = [
  { id: 1, content: "HTML", completed: true },
  { id: 2, content: "CSS", completed: false },
  { id: 3, content: "JS", completed: false },
];

// 배열의 두번째 요소인 객체로부터 id 프로퍼티만 추출
const [, id] = todos; // {id: 2, content: 'CSS', completed: false}
console.log(id);
```

- 중첩 객체의 경우

```jsx
const user = {
  name: "H",
  addr: {
    zipCode: "12345",
    city: "Seoul",
  },
};

const {
  addr: { city },
} = user;
console.log(city); // 'Seoul'
```

- 객체 디스트럭처링 할당을 위한 변수에 Rest 프로퍼티 사용 가능
