## 호출 스케줄링 (scheduling a call)

---

- 함수를 명시적으로 호출하지 않고 일정 시간이 경과된 이후에 호출되도록 함수 호출을 예약하려면 타이머 함수를 사용
- 타이머 생성: setTimeout, setInterval
- 타이머 제거: clearTimeout, clearInterval
- 자바스크립트 엔진은 싱글 스레드로 동작하여, 타이머 함수는 비동기 처리 방식으로 동작

## 타이머 함수

---

### setTimeout / clearTimeout

---

- setTimeout
  - 시간(ms, 1/1000초)
  - 두 번째 인수로 전달받은 시간으로 단 한 번 동작하는 타이머를 생성
  - 타이머가 만료되면 첫 번째 인수로 전달받은 콜백 함수 호출
  ```jsx
  setTimeout(() => console.log("hi~"), 3000);
  setTimeout((name) => console.log(`Hi! ${name}`), 2000, "Lee");
  ```
  - delay 생략하면 기본값 0이 지정
- clearTimeout: 호출 스케줄링 취소
  ```jsx
  const timerId = setTimeout(() => console.log("hi~"), 3000);
  clearTimeout(timerId);
  ```

### setInterval / clearInterval

---

- setInterval
  - 타이머가 만료될 때마다 첫 번째 인수로 전달받은 콜백 함수가 반복 호출
  - 타이머가 취소될 때까지 계속됨
    ```jsx
    const timeoutId = setInterval(() => {
      console.log(count);

      if (count++ === 5) clearInterval(timeoutId);
    }, 1000);
    ```

## 디바운스와 스로틀

---

짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화해서 과도한 이벤트 핸들러의 호출을 방지하는 프로그래밍 기법

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <button>Click Me</button>
    <pre>일반 클릭 이벤트 카운터 <span class="normal-msg">0</span></pre>
    <pre>디바운스 클릭 이벤트 카운터 <span class="debounce-msg">0</span></pre>
    <pre>스로틀 클릭 이벤트 카운터 <span class="throttle-msg">0</span></pre>

    <script>
      const $button = document.querySelector("button");
      const $normalMsg = document.querySelector(".normal-msg");
      const $debounceMsg = document.querySelector(".debounce-msg");
      const $throttleMsg = document.querySelector(".throttle-msg");

      const debounce = (callback, delay) => {
        let timerId;
        return (...args) => {
          if (timerId) clearTimeout(timerId);
          timerId = setTimeout(callback, delay, ...args);
        };
      };

      const throttle = (callback, delay) => {
        let timerId;
        return (...args) => {
          if (timerId) return;
          timerId = setTimeout(() => {
            callback(...args);
            timerId = null;
          }, delay);
        };
      };

      $button.addEventListener("click", () => {
        $normalMsg.textContent = +$normalMsg.textContent + 1;
      });

      $button.addEventListener(
        "click",
        debounce(() => {
          $debounceMsg.textContent = +$debounceMsg.textContent + 1;
        }, 500)
      );

      $button.addEventListener(
        "click",
        throttle(() => {
          $throttleMsg.textContent = +$throttleMsg.textContent + 1;
        }, 500)
      );
    </script>
  </body>
</html>
```

## 디바운스

---

- debounce
  - 짧은 시간 간격으로 이벤트가 연속해서 발생하면 이벤트 핸들러를 호출하지 않다가 `일정 시간이 경과한 이후`에 `이벤트 핸들러가 한 번만 호출`된다.
  - 짧은 시간 간격으로 발생하는 이벤트를 `그룹화`해서 `마지막에 한 번만` 이벤트 핸들러가 호출되도록 한다.
  - 사용 예) input 이벤트를 사용할 때 사용자가 입력할 때마다 서버에 전송이 되는 것은 효율적이지 못하다. 일정 시간 동안 텍스트 입력 필드에 값이 들어오지않으면 완료된 것으로 간주한다. delay보다 짧은 간격으로 이벤트가 발생하면 이전 타이머를 취소하고 새로운 타이머 재설정한다.
  - resize 이벤트 처리, input 요소에 입력된 값으로 자동완성 UI 구현, 버튼 중복 클릭 방지
  - 실무: `Underscore`의 `debounce` 함수나 `Loadash`의 `debounce` 함수 사용

### 스로틀

---

- throttle
  - 짧은 시간 간격으로 이벤트가 연속해서 발생하더라도 `일정 시간 간격`으로 `이벤트 핸들러가 최대 한 번만 호출`되도록 한다.
  - 짧은 시간 간격으로 연속해서 발생하는 이벤트를 `그룹화`해서 일정 시간 단위로 이벤트 핸들러가 호출되도록 호출 주기를 만든다.
  - delay가 경과하기 이전에 이벤트가 발생하면 아무것도 하지 않다가 delay시간이 경과했을 때 `이벤트`가 `발생`하면 콜백 함수를 호출하고 `새로운 타이머 재설정`
  - scroll 이벤트 처리, 무한 스크롤
  - 실무: `Underscore`의 `throttle` 함수나 `Loadash`의 `throttle` 함수를 사용하는 것 권장
    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
        <style>
          .container {
            width: 300px;
            height: 300px;
            background-color: rebeccapurple;
            overflow: scroll;
          }
          .content {
            width: 300px;
            height: 1000vh;
          }
        </style>
      </head>
      <body>
        <div class="container">
          <div class="content"></div>
        </div>
        <div>
          일반 이벤트 핸들러: scroll 이벤트 처리 횟수:
          <span class="normal-count">0</span>
        </div>
        <div>
          스로틀 이벤트 핸들러: scroll 이벤트 처리 횟수:
          <span class="throttle-count">0</span>
        </div>

        <script>
          const $container = document.querySelector(".container");
          const $normalCount = document.querySelector(".normal-count");
          const $throttleCount = document.querySelector(".throttle-count");

          const throttle = (callback, delay) => {
            let timerId;
            return (...args) => {
              if (timerId) return;
              timerId = setTimeout(() => {
                callback(...args);
                timerId = null;
              }, delay);
            };
          };

          let normalCount = 0;
          $container.addEventListener("scroll", () => {
            $normalCount.textContent = ++normalCount;
          });

          let throttleCount = 0;
          $container.addEventListener(
            "scroll",
            throttle(() => {
              $throttleCount.textContent = ++throttleCount;
            }, 100)
          );
        </script>
      </body>
    </html>
    ```
