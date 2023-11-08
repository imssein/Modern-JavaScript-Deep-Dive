# 46장 제너레이터와 async/await

## 제너레이터란?

---

ES6에서 도입된 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수이다.

## 제너레이터 함수의 정의

---

제너레이터 함수는 `function*` 키워드 사용하고, 하나 이상의 `yield` 표현식을 포함한다.

```jsx
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  *genObjMethod() {
    yield 1;
  },
};

// 제너레이터 클래스 메서드
class MyClass {
  *genClsMethod() {
    yield 1;
  }
}
```

> 💡 (`*`) 위치는 function 키워드와 함수 이름 사이라면 어디든 상관없다.
> 하지만 일관성을 유지하기 위해 [ `function*` ]을 권장한다!

제너레이터 함수는 화살표 함수로 정의할 수 없고, 생성자 함수로 호출 할 수 없다.

## 제너레이터 객체

---

| 제너레이터 함수 호출                           | 일반 함수 호출                  |
| ---------------------------------------------- | ------------------------------- |
| 제너레이터 객체를 생성 → 반환                  | 함수 코드 블록 실행             |
| 함수 호출자에게 함수 실행의 제어권 양도(yield) | 함수의 제어권을 함수가 독점     |
| yield 표현식까지만 실행                        | 코드 블록의 모든 코드 일괄 생성 |

**제너레이터 객체**

- 이터러블

  ```jsx
  // for...of 사용 가능
  function* generateSequence() {
    yield 1;
    yield 2;
    yield 3;
  }

  let generator = generateSequence();

  for (let value of generator) {
    alert(value); // 1, 2, 3
  }

  // spread 사용 가능
  let sequence = [0, ...generateSequence()];
  alert(sequence); // 0, 1, 2, 3
  ```

- 이터레이터

  - `next` 메서드와 이터레이터에 없는 `return`, `throw` 메서드를 갖는다.

    ```jsx
    function* generatorSequence() {
      yield 1;
      yield 2;
      return 3;
    }

    let generator = generatorSequence();
    console.log(generator.next());
    // {"value":1,"done":false}

    console.log(generator.return("End!"));
    // {"value": "End!", "done": true}
    ```

  1. `next` 메서드
     - 호출시 `yield 표현식`까지 코드 블록 실행한다.
     - 반환 객체: `{ value: yield된 값, done: false }`
       - done이 fase이면 아직 제너레이터가 완료되지 않았음을 의미한다.
  2. `return` 메서드
     - 호출시 제너레이터 함수의 실행이 종료된다.
     - 반환 객체: `{ value: 인수로 전달받은 값, done: true }`
  3. `throw` 메서드
     - 호출시 예외를 발생시킨다.
     - 반환 객체: `{ value: undefined, done: true}`

  ```jsx
  function* genFunc() {
    try {
      yield 1;
      yield 2;
      yield 3;
    } catch (e) {
      console.error(e);
    }
  }

  const generator = genFunc();

  console.log(generator.next());
  // {value: 1, done: false}
  console.log(generator.throw("Error!"));
  // {value: undefined, done: true}
  ```

## 제너레이터의 일시 중지와 재개

---

yield 키워드는 제너레이터 함수의 실행을 `일시 중지`시키거나 `yield` 키워드 뒤에 오는 `표현식의 평가 결과`를 제너레이터 함수 호출자에게 반환한다.

```jsx
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}

console.log(generator.next()); // {value: 2, done: false}

console.log(generator.next()); // {value: 3, done: false}

console.log(generator.next()); // {value: undefined, done: true}
```

`next` 메서드 호출

1. `yield` 표현식 실행 → `일시 중지`(suspend)

   → **함수의 제어권이 호출자로 양도(yield)**

2. 이터레이터 리절트 객체 (`{value, done}`) 반환

⇒ next 메서드를 반복 호출하다, 제너레이터 함수가 끝나면 done 프로퍼티에 true를 할당한다.

## 제너레이터의 활용

---

### 이터러블의 구현

---

**무한 이터러블을 생성하는 함수**

```jsx
const infiniteFibonacci = (function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre + cur];
      // 무한 이터러블이므로 done 프로퍼티를 생략한다.
      return { value: cur };
    },
  };
})();

// infiniteFibonacci는 무한 이터러블이다.
for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...2584 4181 6765
}
```

**무한 이터러블을 생성하는 제너레이터 함수**

```jsx
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
})();

// infiniteFibonacci는 무한 이터러블이다.
for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...2584 4181 6765
}
```

### 비동기 처리

---

`next`메서드와 `yield` 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있다.

이러한 특성을 활용하면 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있다.

프로미스의 후속 처리 메서드 `then`/`catch`/`finally` 없이 비동기 처리 결과를 구현할 수 있다.

> `async/await`를 사용하면 제너레이터 실행기를 사용할 필요가 없지만, 제너레이터가 필요하다면 `co 라이브러리`를 사용하는 것을 추천한다.

</aside>

## async / await

---

ES8에서는 비동기 처리를 `동기 처리`처럼 `동작`하도록 구현할 수 있는 `async/await`도입되었다. 이는 `프로미스` 기반으로 동작하며, 프로미스의 후속 메서드(`then`/`catch`/`finally`)없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있게 되었다.

### async 함수

---

- `async함수`는 명시적으로 지정하지않아도 `암묵적`으로 `resolve하는 프로미스 반환`

```jsx
// async 함수 선언문
async function foo(n) {
  return n;
}
foo(1).then((v) => console.log(v)); // 1

// async 함수 표현식
const bar = async function (n) {
  return n;
};
bar(2).then((v) => console.log(v)); // 2

// async 화살표 함수
const baz = async (n) => n;
baz(3).then((v) => console.log(v)); // 3

// async 메서드
const obj = {
  async foo(n) {
    return n;
  },
};
obj.foo(4).then((v) => console.log(v)); // 4

// async 클래스 메서드
class MyClass {
  async bar(n) {
    return n;
  }
}
const myClass = new MyClass();
myClass.bar(5).then((v) => console.log(v)); // 5
```

- 클래스의 constructor 메서드는 인스턴스를 반환하기때문에 async 메서드가 될 수 없다.

```jsx
class MyClass {
	async constructor() { }
}

const myClass = new MyClass();
```

> 💡 async 클래스 메서드
>
> ```jsx
> class Waiter {
>   async wait() {
>     return await Promsie.resolve(1);
>   }
> }
>
> new Waiter().wait().then(alert);
> ```

</aside>

### await 키워드

---

**await 키워드의 동작**

1. 대기: `await`키워드는 프로미스가 `settled` 상태(비동기 처리가 수행된 상태)가 될 때까지 `대기`한다.
2. 프로미스 처리 결과: `settled` 상태가 되면 프로미스가 `resolve`한 `결과를 반환`한다.
3. 일시 중지와 재개: 다음 실행을 `일시 중지`하고, 프로미스가 settled 상태가 되면 실행을 `재개`한다.

```jsx
const getGithubUserName = async (id) => {
  const res = await fetch(`https://api.github.com/users/${id}`);
  const { name } = await res.json();
  console.log(name); // 세인
};

getGithubUserName(`imssein`);
```

```jsx
async function foo() {
  const a = await new Promise((resolve) => setTimeout(() => resolve(1), 3000));
  const b = await new Promise((resolve) => setTimeout(() => resolve(2), 2000));
  const c = await new Promise((resolve) => setTimeout(() => resolve(3), 1000));

  console.log([a, b, c]); // [1, 2, 3]
}

foo(); // 약 6초 소요된다.
```

### 에러 처리

---

에러 처리는 비동기 코드에서 매우 중요하다. 비동기 처리를 위해 콜백 패턴을 사용할 때 에러 처리가 어려울 수 있으며, 에러가 호출자 방향으로 전파되는 한계가 있다.

**콜백 패턴에서의 에러 처리**

- 비동기 함수는 콜백 함수를 호출하고 나중에 실행한다. 이때 발생하는 에러는 `호출자 방향으로 전파`된다. 따라서 호출자가 에러를 적절히 처리하기 어렵다.
- 콜백 함수를 인수로 전달받는 비동기 함수는 비동기 함수의 실행 컨텍스트와 분리되어 있기 때문에 `try…catch문`을 사용하여 에러를 캐치할 수 없다.
- `setTimeout`의 콜백 함수 안에서 발생한 에러는 `try…catch문`으로 잡을 수 없다.

```jsx
try {
  setTimeout(() => {
    throw new Error("Error!");
  }, 1000);
} catch (e) {
  // 에러를 캐치하지 못한다.
  console.error("캐치한 에러", e);
}
```

**async/await에서의 에러 처리**

- 비동기 함수 내에서 발생한 에러는 해당 함수 내에서 캐치할 수 있다.
- `async`함수는 `프로미스를 반환`하며, `에러`가 발생하면 해당 프로미스는 `reject상태`로 전환된다. 따라서 `try…catch문`을 사용하여 에러를 처리 할 수 있다.

```jsx
const foo = async () => {
  try {
    const wrongUrl = "https://wrong.url";

    const response = await fetch(wrongUrl);
    const data = await response.json();
    console.log(data);
  } catch (err) {
    console.error(err); // TypeError: Failed to fetch
  }
};

foo();
```

- `모든 종류의 에러 처리`: async 함수 내에서 발생한 모든 종류의 에러, 네트워크 에러와 일반적인 에러를 모두 캐치할 수 있다.
- `에러를 reject하는 프로미스 반환`: catch문을 사용하여 에러를 처리하지 않으면, 해당 에러를 reject하는 프로미스를 반환한다. 이 프로미스는 에러 정보를 담고 있어, `후속 처리 메서드(then, catch)`를 사용하여 에러를 처리할 수 있다.

```jsx
const foo = async () => {
  const wrongUrl = "https://wrong.url";

  const response = await fetch(wrongUrl);
  const data = await response.json();
  return data;
};

foo().then(console.log).catch(console.error); // TypeError: Failed to fetch
```

> 💡 `await`는 최상위 레벨 코드에서 작동하지 않는다.
>
> ```jsx
> // 최상위 레벨 코드에서는 문법 에러가 발생한다.
> const res = await fetch(`https://api.github.com/users/${id}>`);
> const { name } = await res.json();
> ```
>
> 익명 async 함수로 코드를 감싸면 사용 가능하다.
>
> ```jsx
> (async () => {
>   const res = await fetch(`https://api.github.com/users/${id}>`);
>   const { name } = await res.json();
> })();
> ```
