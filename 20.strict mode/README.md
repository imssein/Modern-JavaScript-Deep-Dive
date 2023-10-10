# 20장 strict mode

## strict mode란?

---

```jsx
function foo() {
  x = 10;
}
foo();

console.log(x);
```

- 전역 스코프에 x 변수의 선언이 존재하지 않기 때문에 `ReferenceError`를 발생시킬 것 같지만 자바스크립트 엔진은 `암묵적`으로 전역 객체에 `x 프로퍼티`를 `동적 생성`한다.
- `암묵적 전역` (implicit global): `전역 객체의 x 프로퍼티`는 마치 `전역 변수처럼` 사용할 수 있음
  - 개발자 의도와 상관없이 발생한 `암묵적 전역`은 `오류`를 발생시킬 수 있음
  - `var`, `let`, `const` 키워드를 사용해 변수를 `선언`한 다음 사용해야함
- `strict mode`: 자바스크립트 언어의 문법을 좀 더 `엄격히 적용`하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 `명시적인 에러`를 발생시킴
- `ESLint`를 이용해 `strict mode`와 유사한 효과를 얻을 수 있음
  - 소스코드를 `실행하기 전`에 소스코드를 스캔하여 `문법적` 오류만이 아니라 `잠재적` 오류까지 찾아내고 오류의 원인을 리포팅해주는 유용한 도구

> 🍏 ES6에서 도입된 클래스와 모듈은 기본적으로 strict mode가 적용

## strict mode의 적용

---

전역의 선두 또는 몸체의 선두에 `‘use strict’;` 추가

## 전역에 strict mode를 적용하는 것은 피하자

---

전역에 적용한 strict mode는 스크립트 단위로 적용

```html
<!DOCTYPE html>
<html></html>
  <body>
    <script>
        'use strict';
    </script>
    <script>
        x = 1;      // 에러 발생 안함
        console.log(x);  // 1
    </script>
    <script>
        'use strict';
        y = 1;      // ReferenceError
    </script>
  </body>
</html>
```

strict mode는 해당 스크립트에 한정되어 적용

## 함수 단위로 strict mode를 적용하는 것도 피하자

## strict mode가 발생시키는 에러

---

### 암묵적 전역

---

선언하지 않은 변수를 참조하면 ReferenceError가 발생

```jsx
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError: x is not defined
})();
```

### 변수, 함수, 매개변수의 삭제

---

delete 연산자로 변수, 함수, 매개변수를 삭제하면 `SyntaxError`가 발생

```jsx
(function () {
  "use strict";

  var x = 1;
  delete x; // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a; // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo; // SyntaxError: Delete of an unqualified identifier in strict mode.
})();
```

### 매개변수 이름의 중복

---

중복된 매개변수 이름을 사용하면 `SyntaxError`가 발생

### with 문의 사용

---

with문을 사용하면 `SyntaxError` 발생

with문은 전달된 객체를 스코프 체인에 추가한다. with 문은 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠지는 문제가 있다. with문 사용하지 않는 것이 좋다.

## strict mode 적용에 의한 변화

---

### 일반 함수의 this

---

일반 함수 호출 → `this`에 `undefined`가 바인딩된다.

생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문에 에러는 발생하지 않는다.

```jsx
(function () {
  "use strict";

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

### arguments 객체

---

매개변수에 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```jsx
(function () {
  "use strict";

  a = 2;

  console.log(arguments); // ReferenceError: a is not defined
})(1);
```
