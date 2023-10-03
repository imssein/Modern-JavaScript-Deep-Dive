# 8장 제어문

## 8.2 조건문

---

### 8.2.1 if…else 문

---

`if…else 문`

- 표현식이 아닌 문
- 값처럼 사용할 수 없기 때문에 변수에 할당할 수 없음

<aside>
💡 **if..else문 vs 삼항 조건 연산자**

조건에 따라 단순히 값을 결정하여 변수에 할당하는 경우 `if…else문`보다 `삼항 조건 연산자`를 사용하는 편이 가독성에 좋다. 하지만 조건에 따라 실행해야 할 내용이 복잡하여 여러 줄의 문이 필요하다면 `if…else문`을 사용하는 편이 가독성이 좋다.

</aside>

### 8.2.2 switch 문

---

- `if…else 문`은 **논리적 참, 거짓**으로 실행할 코드 블록 결정
- `switch 문`은 논리적 참, 거짓보다는 **다양한 상황(case)**에 따라 실행할 코드 블록을 결정
- 폴스루(fall through): switch 문에서 case 내에서 의도적으로 break문을 생략하여 다음 case로 이동 시키는 방법

<aside>
💡 **if…else문 vs switch문**

`swithch문`보다는 i`f…else문`을 사용하는 편이 좋다.
조건이 너무 많아 `switch문`을 사용했을 때 가독성이 더 좋다면 `switch문` 사용

</aside>

## 8.3 반복문

---

<aside>
💡 **반복문을 대체할 수 있는 다양한 기능**

- `forEach` 메서드: 배열 순회
- `for…in 문`: 객체의 프로퍼티 열거
- `for…of 문`: ES6에서 도입된 이터러블을 순회

</aside>

### 8.3.2 while문

---

<aside>
💡 **for문 vs while문**

`for문`은 반복 횟수가 명확할 때 주로 사용하고 `while문`은 반복 횟수가 불명확할 때 주로 사용

</aside>

- 조건식의 평가 결과가 참이면 코드 블록을 계속해서 반복 실행

### 8.3.3 do…while문

---

- 코드 블록을 먼저 실행하고 조건식을 평가

## 8.4 break 문

---

- 레이블 문: 식별자가 붙은 문
  ```jsx
  foo: console.log("foo");
  ```
  ```jsx
  foo: {
    console.log(1);
    break foo;
    console.log(2);
  }

  console.log("Done!");
  ```
  ```jsx
  outer: for (var i = 0; i < 3; i++) {
    for (var j = 0; j < 3; j++) {
      // i + j === 3이면 outer라는 식별자가 붙은 레이블 for 문을 탈출
      if (i + j === 3) break outer;
      console.log(`inner [${i}, ${j}`);
    }
  }

  console.log("Done!");
  ```
  → 레이블 문을 사용하면 프로그램의 흐름이 복잡해져서 가독성이 나빠지고 오류를 발생시킬 가능성이 높아진다!

## 8.5 continue 문

---

- 문자열에서 특정 문자의 개수 세기
  ```jsx
  var string = "Hello World.";
  var search = "l";
  var count = 0;

  // continue 사용
  for (var i = 0; i < string.length; i++) {
    if (string[i] !== search) continue;
    count++;
  }

  // if문 내에서 실행해야 할 코드가 한 줄이라면 간편하고 가독성있게 할 수 있음
  for (var i = 0; i < string.length; i++) {
    if (string[i] === search) count++;
  }

  // String.prototype.match 메서드 이용
  const regexp = new RegExp(search, "g");
  console.log(string.match(regexp).length);
  ```
    <aside>
    💡 **continue 사용 vs if문 사용**
    
    위와 같이 if 문 내에서 실행해야 할 코드가 한 줄이라면 continue 문을 사용했을 때보다 간편하고 가독성이 좋다. 하지만 ****`if 문` 내에서 실행해야 할 코드가 길다면 들여쓰기가 한 단계 더 깊어지므로 `continue 문`을 사용하는 편이 가독성이 더 좋다!
    
    </aside>
