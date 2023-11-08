# 45장 프로미스

- 전통적인 콜백 패턴
  - 콜백 헬 → 가독성이 나쁘고 비동기 처리 중 발생한 에러의 처리가 곤란
  - 여러 개의 비동기 처리를 한번에 처리하는 데도 한계
- `프로미스`
  - `ES6`에서는 `비동기 처리`를 위한 또 다른 패턴
  - 전통적인 콜백 패턴이 가진 `단점을 보완`
  - 비동기 처리 `시점`을 명확하게 표현할 수 있음

## 비동기 처리를 위한 콜백 패턴의 단점

---

### 콜백 헬

```jsx
// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콘솔에 출력한다.
      console.log(JSON.parse(xhr.response));
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

get("https://~");
```

**[비동기 함수 호출]**

- 비동기 함수 내부의 비동기로 동작하는 코드는 완료되지 않아도 즉시 종료된다.
- 비동기 함수 내부의 비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료된다.
- 따라서 비동기 함수 내부의 비동기 코드에서 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않는다.

```jsx
let g = 0;

// 비동기 함수인 setTimeout 함수는 콜백 함수의 처리 결과를 외부로 반환하거나
// 상위 스코프의 변수에 할당하지 못한다.
setTimeout(() => {
  g = 100;
}, 0);
console.log(g); // 0
```

**[setTimeout 함수 호출]**

- **`setTimeout`** 함수는 콜백 함수를 호출 스케줄링한 다음, 타이머 ID를 반환하고 즉시 종료된다.
- 콜백 함수는 **`setTimeout`** 함수가 종료된 이후에 실행된다.
- 따라서 **`setTimeout`** 함수를 사용하여 콜백 함수의 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당할 수 없다.

```jsx
// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // ① 서버의 응답을 반환한다.
      return JSON.parse(xhr.response);
    }
    console.error(`${xhr.status} ${xhr.statusText}`);
  };
};

// ② id가 1인 post를 취득
const response = get("https://jsonplaceholder.typicode.com/posts/1");
console.log(response); // undefined
```

**[XMLHttpRequest와 비동기 함수 호출]**

- XMLHttpRequest 객체를 사용하여 GET 요청을 보내는 경우, 서버 응답은 **`xhr.onload`** 이벤트 핸들러 내에서 처리됩니다.
- 이 이벤트 핸들러는 비동기로 동작하며, **`console.log`**가 호출되기 직전에 실행되지 않습니다.
- 결과적으로, **`xhr.onload`** 이벤트 핸들러가 완료된 후에 서버 응답 데이터를 얻을 수 있습니다.

```jsx
let todos;

// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // ① 서버의 응답을 상위 스코프의 변수에 할당한다.
      todos = JSON.parse(xhr.response);
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

// id가 1인 post를 취득
get("https://jsonplaceholder.typicode.com/posts/1");
console.log(todos); // ② undefined
```

**[서버의 응답을 상위 스코프의 변수에 할당]**

- 이벤트 핸들러 프로퍼티에 바인딩한 이벤트 핸들러는 ②의 console.log가 종료한 이후에 호출한다.
  - todos가 출력될 때는 결과가 할당되기 이전이기때문에 undefined가 출력된다.

**[비동기 함수의 동작과 이벤트 핸들러 처리 과정]**

1. 비동기 함수 `get` 호출
   - 함수 코드 평가 과정
   - `get` 함수의 실행 컨텍스트가 생성되고 실행 컨텍스트 스택(콜 스택)에 푸시된다.
2. 이벤트 핸들러 바인딩
   - `xhr.onload` 이벤트 핸들러 프로퍼티에 이벤트 핸들러가 바인딩된다.
3. 비동기 함수 `get` 종료
   - `get` 함수가 종료되면 해당 실행 컨텍스트가 실행 컨텍스트 콜 스택에서 제거된다.
4. `console.log` 호출
   - `console.log`의 실행 컨텍스트가 생성되고 실행 컨텍스트 스택에 푸시된다.
   - `console.log`가 호출되기 직전에 `load` 이벤트가 발생했더라도 **xhr.onload 이벤트 핸들러 프로퍼티에 바인딩한 이벤트 핸들러는 console.log보다 먼저 실행되지 않는다.**
5. xhr.onload 이벤트 핸들러 실행
   - load 이벤트 발생하고 해당 이벤트는 태스크 큐에 저장되어 대기한다.
   - 콜 스택이 비어 있는 상태에서 이벤트 루프에 의해 태스크 큐의 이벤트가 콜 스택으로 푸시되어 실행된다.

<aside>
💡 xhr.onload 이벤트 핸들러가 실행되는 시점에 콜 스택이 빈 상태여야 하므로 console.log는 이미 종료된 이후이다.

</aside>

```jsx
// GET 요청을 위한 비동기 함수
const get = (url, successCallback, failureCallback) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콜백 함수에 인수로 전달하면서 호출하여 응답에 대한 후속 처리를 한다.
      successCallback(JSON.parse(xhr.response));
    } else {
      // 에러 정보를 콜백 함수에 인수로 전달하면서 호출하여 에러 처리를 한다.
      failureCallback(xhr.status);
    }
  };
};

// id가 1인 post를 취득
// 서버의 응답에 대한 후속 처리를 위한 콜백 함수를 비동기 함수인 get에 전달해야 한다.
get("https://jsonplaceholder.typicode.com/posts/1", console.log, console.error);
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere ...",
  "body": "quia et suscipit ..."
}
*/
```

**[콜백 함수를 사용한 비동기 처리]**

- 비동기 처리에서 콜백 함수를 사용하여 비동기 작업의 결과를 처리하거나 에러를 처리한다.
- 비동기 함수는 콜백 함수를 인수로 받아서 비동기 작업이 성공하거나 실패할 때 해당 콜백 함수를 호출한다.
- 콜백 함수를 사용하면 비동기 처리 결과를 처리하는 데 유용하지만, 중첩된 콜백 함수로 인해 코드가 복잡해질 수 있고 "콜백 헬" 문제가 발생할 수 있다.

```jsx
get("/step1", (a) => {
  get(`/step2/${a}`, (b) => {
    get(`/step3/${b}`, (c) => {
      get(`/step4/${c}`, (d) => {
        console.log(d);
      });
    });
  });
});
```

### 에러 처리의 한계

---

```jsx
try {
  setTimeout(() => {
    throw new Error("Error!");
  }, 1000);
} catch (e) {
  // 에러를 캐치하지 못한다
  console.error("캐치한 에러", e);
}
```

1. `setTimeout`과 비동기 동작
   - `setTimeout` 함수는 비동기 함수로, 콜백 함수의 실행을 기다리지 않고 즉시 반화된다.
   - `setTimeout` 콜백 함수는 `setTimeout` 함수가 종료된 이후에 실행된다.
2. 에러 전파 방향
   - **에러는 호출자(caller)방향으로 전파**된다. 콜 스택의 아래 방향(실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파된다.
3. `setTimeout` 콜백 함수의 호출자
   - `setTimeout` 함수의 콜백 함수를 호출한 것은 `setTimeout` 함수가 아니다. (콜백 함수가 실행될 때 setTImeout함수는 이미 콜 스택에서 제거된 상태) `setTimeout` 함수의 콜백 함수가 발생시킨 에러는 `setTimeout` 함수의 콜 스택에 있지 않아 `try…catch` 블록에서 캐치되지 않는다.

이러한 문제를 극복하기 위해서 **Promise**와 **async/await**와 같은 비동기 처리 패턴이 도입되었다.

## 프로미스의 생성

---

- `Promise` 생성자 함수를 `new` 연산자와 함께 호출
- 비동기 처리를 수행할 `resolve`와 `reject` 콜백 함수를 인수로 전달받음

```jsx
const promise = new Promise((resolve, reject) => {
	// 프로미스 함수의 콜백 함수 내부에서 비동기 처리를 수행
	if (/* 비동기 처리 성공*/) resolve('result');
	// 비동기 처리 실패
	else reject('failure reason')
});
```

**비동기 함수 get을 프로미스를 사용**

```jsx
// GET 요청을 위한 비동기 함수
const promiseGet = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        // 성공적으로 응답을 전달받으면 resolve 함수를 호출한다.
        resolve(JSON.parse(xhr.response));
      } else {
        // 에러 처리를 위해 reject 함수를 호출한다.
        reject(`${xhr.status} ${xhr.statusText}`);
      }
    };
  });
};
// 프로미스를 반환한다.
promiseGet("https://~");
```

| 프로미스의 상태 정보 | 의미                                  | 상태 변경 조건                   |
| -------------------- | ------------------------------------- | -------------------------------- |
| pending              | 비동기 처리가 아직 수행되지 않은 상태 | 프로미스가 생성된 직후 기본 상태 |
| fulfilled            | 비동기 처리가 수행된 상태(성공)       | resolve 함수 호출                |
| rejected             | 비동기 처리가 수행된 상태(실패)       | reject 함수 호출                 |

생성된 직후의 프로미스는 기본적으로 `pending` 상태다. 이후 비동기 처리가 수행되면 비동기 처리 결과에 따라 다음과 같이 프로미스의 상태가 변경된다.

- 비동기 처리 `성공`: `resolve` 함수 호출 → `fulfilled` 상태로 변경
- 비동기 처리 `실패`: `reject` 함수 호출 → `rejected` 상태로 변경

### Promise.prototype.then

---

- 첫 번째 콜백 함수는 프로미스가 `fulfilled 상태`(`resolve 함수`가 호출된 상태)가 되면 호출된다. 콜백 함수는 프로미스의 `비동기 처리 결과`를 `인수`로 전달받는다.

```jsx
new Promise((resolve) => resolve("fulfilled")).then(
  (v) => console.log(v),
  (e) => console.eroor(e)
); // fulfilled
```

- 두 번째 콜백 함수는 프로미스가 `rejected 상태`(`reject 함수`가 호출된 상태)가 되면 호출된다. 콜백 함수는 프로미스의 `에러`를 `인수`로 전달받는다.

```jsx
new Promise((_, reject) => reject(new Error('rejected'))
	.then(v => console.log(v), e => console.eroor(e)); // Error: rejected
```

→ `프로미스를 반환`한다.

### Promise.prototype.catch

---

프로미스가 `rejected` 상태일 경우만 호출되며 1개의 콜백 함수를 인수로 전달받는다.

```jsx
new Promise((_, reject) => reject(new Error('rejected'))
	.catch(e => console.eroor(e)); // Error: rejected
```

→ `catch` 메서드는 `then`(undefined, onRejected)과 동일하게 동작하며 언제나 `프로미스를 반환`한다.

### Promise.prototype.finally

---

프로미스의 성공(`fulfilled`) or 실패(`rejected`)와 상관없이 `무조건 한 번 호출`되며 1개의 콜백 함수를 인수로 전달받는다. `filnally`는 프로미스 상태와 상관없이 `공통적`으로 수행해야 할 처리 내용이 있을 때 유용하다.

```jsx
new Promise(() => {}).finally(() => console.log("finally")); // finally
```

→ `then`/`catch` 메서드와 동일하게 언제나 `프로미스를 반환`한다.

```jsx
// GET 요청을 위한 비동기 함수
const promiseGet = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        // 성공적으로 응답을 전달받으면 resolve 함수를 호출한다.
        resolve(JSON.parse(xhr.response));
      } else {
        // 에러 처리를 위해 reject 함수를 호출한다.
        reject(`${xhr.status} ${xhr.statusText}`);
      }
    };
  });
};
// 프로미스를 반환한다.
promiseGet("https://~").
	.then(res => console.log(res))
	.catch(err => console.error(err))
	.finally(() => console.log('Bye!'));
```

## 프로미스의 에러 처리

---

비동기 처리 결과에 대한 후속 처리 메서드 `then`, `catch`, `finally`를 사용하여 수행한다.

- 비동기 처리에서 발생한 에러는 `then` 메서드의 `두 번째 콜백 함수`로 처리할 수 있다.

```jsx
const wrongUrl = "http://~~~";

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl).then(
  (res) => console.log(res),
  (err) => console.log(err)
); // Error: 404
```

- `catch`를 사용해 처리할 수 있다.

```jsx
const wrongUrl = "http://~~~";

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl)
  .then((res) => console.log(res))
  .catch((err) => console.error(err)); // Error: 404
```

`catch` 메서드를 호출하면 내부적으로 `then`을 호출하며 내부적으로는 다음과 같이 처리된다.

```jsx
const wrongUrl = "http://~~~";

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl)
  .then((res) => console.log(res))
  .then(undefined, (err) => console.error(err)); // Error: 404
```

`then` 메서드의 두번째 콜백 함수는 첫 번째 콜백 함수에서 발생한 `에러를 캐치하지 못하고` `코드가 복잡`해져서 가독성이 좋지 않다. 따라서 `catch` 메서드를 모든 then 메서드를 호출한 이후에 호출하면 비동기 처리에서 발생한 에러뿐만 아니라 then 메서드 내부에서 발생한 에러까지 `모두 캐치`할 수 있다.

## 프로미스 체이닝

---

프로미스는 `then`, `catch`, `finally` 후속 처리 메서드를 통해 콜백 헬을 해결한다.

```jsx
const url = "https://~~~";

// id가 1인 post의 userId를 취득
promiseGet(`${url}/posts/1`)
  // 취득한 post의 userId로 user 정볼르 취득
  .then(({ userId }) => promiseGet(`${url}/users/${userId}`))
  .then((userInfo) => console.log(userInfo))
  .catch((err) => console.error(err));
```

then → then → catch 순서로 호출하는 후속 처리 메서드는 언제나 `프로미스를 반환`하므로 `연속적`으로 호출할 수 있다. 이를 `프로미스 체이닝`이라 한다.

콜백 패턴은 가독성이 좋지 않아 ES8에서 도입된 `async/await`를 통해 해결할 수 있다. async/await를 사용하면 프로미스의 `후속 처리 메서드 없이` `마치 동기 처리처럼 프로미스가 처리 결과를 반환`하도록 구현할 수 있다.

```jsx
const url = 'https://~~~';

(async () => {
	// id가 1인 post의 userId를 취득
	const { userId } = awati promiseGet(`${url}/posts/1`);
	// 취득한 post의 userId로 user 정보를 취득
	const userInfo = awati promiseGet(`${url}/users/${userId}`);
	console.log(userInfo);
})();
```

## 프로미스의 정적 메서드

---

### Promise.resove / Promise.reject

---

이미 존재하는 값을 래핑하여 프로미스를 생성하기 위해 사용한다.

```jsx
// 배열을 resolve하는 프로미스를 생성
const resolvedPromise = Promise.resolve([1, 2, 3]);
resolvedPromise.then(console.log); // [1, 2, 3]
```

위 코드는 다음과 동일하게 동작한다.

```jsx
const resolvedPromise = new Promise((resove) => resolve([1, 2, 3]));
resolvedPromise.then(console.log); // [1, 2, 3]
```

```jsx
// 에러 객체를 reject하는 프로미스를 생성
const rejectedPromise = Promise.reject(new Error("Error!"));
rejectedPromise.catch(console.log); // Error: Error!
```

위 코드는 다음과 동일하게 동작한다.

```jsx
const rejectedPromise = new Promise((_, reject) => reject(new Error('Error!'));
rejectedPromise.catch(console.log);  // Error: Error!
```

### Promise.all

---

여러 개의 비동기 처리를 모두 `병렬 처리`할 때 사용한다.

```jsx
const requestData1 = () =>
  new Promise((resolve) => setTimeout(() => resolve(1), 3000));
const requestData2 = () =>
  new Promise((resolve) => setTimeout(() => resolve(2), 3000));
const requestData3 = () =>
  new Promise((resolve) => setTimeout(() => resolve(3), 3000));

const res = [];
requestData1()
  .then((data) => {
    res.push(data);
    return requestData2();
  })
  .then((data) => {
    res.push(data);
    return requestData3();
  })
  .then((data) => {
    res.push(data);
    console.log(res); // [1, 2, 3] => 약 6초 소요
  })
  .catch(console.error);
```

세 개의 비동기 처리를 순차적으로 처리한다. 그런데 세 개의 비동기 처리는 서로 의존하지 않고 개별적으로 수행되기에 순차적으로 처리할 필요가 없다.

`Promise.all` 메서드는 여러 개의 비동기 처리를 모두 `병렬` 처리할 때 사용한다.

```jsx
const requestData1 = () =>
  new Promise((resolve) => setTimeout(() => resolve(1), 3000));
const requestData2 = () =>
  new Promise((resolve) => setTimeout(() => resolve(2), 2000));
const requestData3 = () =>
  new Promise((resolve) => setTimeout(() => resolve(3), 1000));

Promise.all([requestData1(), requestData2(), requestData3()])
  .then(console.log) // [1, 2, 3] => 약 3초 소요
  .catch(console.error);
```

`모든` 프로미스가 `fullfilled` 상태가 되면 `resolve`된 처리 결과를 `모두 배열에 저장`해 새로운 프로미스를 반환한다. 첫 번째 프로미스가 가장 나중에 fulfilled 상태가 되어도 첫 번째 프로미스가 resolve한 처리 결과부터 `차례대로 배열에 저장`한다.

프로미스가 하나라도 `rejected` 상태가 되면 나머지 프로미스가 fullfilled 상태가 되는 것을 기다리지 않고 `즉시 종료`한다.

### Promise.race

---

`모든` 프로미스가 `fullfilled` 상태가 되는 것을 `기다리는 것이 아니라` `가장 먼저 fullfilled` 상태가 된 프로미스의 처리 결과를 `resolve`하는 새로운 프로미스를 반환한다.

```jsx
Promise.race([requestData1(), requestData2(), requestData3()])
  .then(console.log) // 3
  .catch(console.error);
```

`rejected` 상태가 되면 `에러를 reject`하는 새로운 프로미스를 `즉시 반환`한다.

### Promise.allSettled

---

프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받는다. 전달받은 프로미스가 모두 settled 상태(비동기 처리가 수행된 상태, fullfilled or rejected)가 되면 처리 결과를 배열로 반환한다.

```jsx
Promise.allSettled([
  new Promise((resolve) => setTimeout(() => resolve(1), 2000)),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error("Error!")), 1000)
  ),
]).then(console.log);

// {status: 'fulfilled', value: 1}
// {status: 'rejected', reason: Error: Error! at <anonymous>:3:53}
```

→ `fullfilled` 상태 → `value` 프로퍼티 / `rejected` 상태 → `reason` 프로퍼티

## 마이크로태스크 큐

---

```jsx
setTimeout(() => console.log(1), 0);

Promise.resolve()
	.then(() => console.log(2));
	.then(() => console.log(3));
```

위 예제는 1 → 2 → 3 순으로 출력될 것 같지만, 2 → 3 → 1 순으로 출력된다. 이유는 프로미스의 후속 처리 메서드의 콜백 함수는 태스크 큐가 아니라 `마이크로태스크 큐`에 저장되기 때문이다.

`마이크로태스크 큐`에는 프로미스의 후속 처리 메서드의 `콜백 함수가 일시 저장`된다. 그 외의 `비동기 함수의 콜백 함수`나 `이벤트 핸들러`는 `태스크 큐`에 일시 저장된다. `마이크로태스크 큐`는 태스크 큐보다 `우선순위가 높다.`

## fetch

---

```jsx
const promise = fetch(url [, options])
```

fetch 함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환한다.

```jsx
fetch("https:~").then((response) => console.log(response));
```

```jsx
fetch('https:~')
	// response는 HTTP 응답을 나타내는 Response 객체다.
	// json 메서드를 사용하여 Response 객체에서 HTTP 응답 몸체를 취득하여 역직렬화한다.
	.then(response => response.json()
	// json은 역직렬화된 HTTP 응답 몸체다.
	.then(json => console.log(json));
```

**[에러 처리 주의 할 점]**

```jsx
const wrongUrl = "https://~~";

fetch(wrongUrl)
  .then(() => console.log("ok"))
  .catch(() => console.log("error"));
```

부적절한 URL이 지정되었기 때문에 `404 Not Found 에러`가 발생하고 `‘ok’`가 출력된다.

`fetch` 함수가 반환하는 프로미스는 기본적으로 404 Not Found나 500 Internal Server Error와 같은 `HTTP 에러가 발생`해도 에러를 `reject`하지 않고 불리언 타입의 `ok 상태`를 `false`로 설정한 `Response 객체를 resolve`한다. 오프라인 등의 네트워크 장애나 CORS 에러에 의해 요청이 완료되지 못한 경우에만 프로미스를 reject한다.

```jsx
const wrongUrl = "https://~~";

fetch(wrongUrl)
  .then((response) => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then((todo) => console.log(todo))
  .catch((err) => console.error(err));
```

`axios`는 `모든 HTTP 에러`를 `reject`하는 프로미스를 반환한다. 따라서 모든 에러를 catch에서 처리 할 수 있어 편리하다. 또한 axios는 인터셉터, 요청 설정 등 fetch보다 다양한 기능을 지원한다.
