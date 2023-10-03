# 9장 타입 변환과 단축 평가

## 타입 변환

---

- `명시적 타입 변환`(explicit coercion) 또는 `타입 캐스팅`(type casting)은 개발자가 `의도적`으로 값의 `타입을 변환`하는 것
- `암묵적 타입 변환`(implicit coercion) or `타입 강제 변환`(type coercion)은 개발자의 의도와는 상관없이 표현식을 평가하는 도중에 자바스크립트 엔진에 의해 `암묵적`으로 `타입이 변환` 되는 것
- 명시적 타입 변환이나 암묵적 타입 변환이 `원시 값`(primitive value)을 직접 변경하는 것은 아니다. ⇒ `변경 불가능 한 값` (immutable value)
- `기존 원시 값`을 사용해 다른 타입의 `새로운 원시 값`을 생성하는 것

<aside>
💡 `명시적 타입 변환`은 코드에 개발자의 `의도`가 드러나지만, `암묵적 타입 변환`은 개발자의 의도가 드러나지않고 `타입이 자동 변환`한다.

**명시적 타입 변환만 사용하는건?**
`(10).toString()`보다 `10 + ‘’` 이 더 간결하기에 암묵적 타입 변환이 `가독성` 측면에서 더 좋을 수 있다.

**따라서, 코드에서 타입 변환이 어떻게 동작하는지 정확하게 이해하고 예측하고 사용해야한다.**

</aside>

## 암묵적 타입 변환

---

- 자바스크립트 엔진이 표현식을 평가할 때 `개발자의 의도와는 상관없이` 코드의 문맥을 고려해 `암묵적`으로 데이터 `타입을 강제 변환`(암묵적 타입 변환)하는 것

```jsx
// 피연산자가 모두 문자열 타입이어야 하는 문맥
"10" + 2; // '102'

// 피연산자가 모두 숫자 타입이어야 하는 문맥
5 * "10"; // 50

// 피연산자 또는 표현식이 불리언 타입이어야 하는 문맥
!0; // true
if (1) {
}
```

<aside>
💡 코드의 문맥에 부합하지 않는 상황에서 자바스크립트는 가급적 에러를 발생시키지 않도록 암묵적 타입 변환을 통해 표현식을 평가한다.

</aside>

### 문자열 타입으로 변환

---

```jsx
// 숫자 타입
0 + ''  // "0"
-0 + ''  // "0"
1 + ''  // "1"
-1 + ''  // "-1"
NaN + '' // "NaN"

// 불리언 타입
true + '' // "true"
false + '' // "false"

// null 타입
null + '' // "null"

// undefined 타입
undefined + '' // "undefined"

// 심벌 타입
(Symbol()) + ''  // TypeError: Cannot convert a Symbol value to a string

// 객체 타입
({}) + ''   // "[Object Object"]
Math + ''   // "[Object Math]"
[] + ''     // ""
[10, 20] + ''  // "10, 20"
(function(){}) + '' // "function(){}"
Array + ''    // "function Array() { [native code] }"
```

### 숫자 타입으로 변환

---

```jsx
1 - "1"; // 0
1 * "10"; // 10
1 / "one"; // NaN (피연산자를 숫자 타입으로 변환할 수 없는 경우는 NaN이 된다)

"1" > 0; // true
```

`+` 단항 연산자는 피연산자가 숫자 타입의 값이 아니면 `숫자 타입의 값으로 암묵적 타입 변환`을 수행 한다.

```jsx
// 문자열 타입
+''  // 0
+'0' // 0
+'1' // 1
+'string' // NaN

// 불리언 타입
+true  // 1
+false // 0

// null 타입
+null. // 0

// undefined 타입
+undefined // NaN

// 심벌 타입
+Symbol()  // TypeError: Cannot convert a Symbol value to a number

// 객체 타입
+{}  // NaN
+[]  // 0
+[10, 20]  // NaN
+(function(){}) // NaN
```

- `빈 문자열 (’’)`, `빈 배열([])`, `null`, `false` ⇒ 0
- `true` ⇒ 1
- `객체`, `값이 있는 배열`, `undefined` ⇒ NaN

### 불리언 타입으로 변환

---

- 자바스크립트 엔진은 불리언 타입이 아닌 값을 `True 값` 또는 `False 값`으로 구분한다.
- 자바스크립트 엔진이 false 값으로 판단하는 값: `false`, `undefined`, `null`, `0`, `-0`, `NaN`, `‘’(빈 문자열)`

## 명시적 타입 변환

---

- `개발자의 의도`에 따라 명시적으로 타입을 변경하는 대표적인 방법은 다음과 같다.
  - 표준 빌트인 생성자 함수(String, Number, Boolean)를 new 연산자 없이 호출하는 방법
  - 빌트인 메서드를 사용하는 방법
  - 암묵적 타입 변환

<aside>
💡 **표준 빌트인 생성자 함수와 빌트인 메서드 (21장)**
- 자바스크립트에서 `기본 제공`하는 함수
- `표준 빌트인 생성자 함수`는 `객체를 생성하기 위한 함수`이며 `new 연산자와 함께 호출`된다.
- `빌트인 메서드`는 자바스크립트에서 `기본 제공`하는 빌트인 객체 메서드다

</aside>

### 문자열 타입으로 변환

---

1. `String 생성자 함수`를 new 연산자 없이 호출하는 방법
2. `Object.prototype.toString 메서드`를 사용하는 방법
3. `문자열 연결 연산자`를 이용하는 방법

```jsx
// 1. String 생성자 함수를 new 연산자 없이 호출하는 방법
// 숫자 타입 => 문자 타입
String(1); // '1'
String(NaN); // 'NaN'
String(Infinity); // 'Infinity'

// 불리언 타입 => 문자열 타입
String(true); // 'true'
String(false); // 'fasle'

// 2. Object.prototype.toString 메서드를 사용하는 방법
(1).toString(); // '1'
NaN.toString(); // 'NaN'
true.toString(); //' true'

// 3. 문자열 연결 연산자를 이용하는 방법
1 + ""; // '1'
NaN + ""; // 'NaN'
```

### 숫자 타입으로 변환

---

1. `Number 생성자 함수`를 new 연산자 없이 호출하는 방법
2. `parseInt.parseFloat 함수`를 사용하는 방법(문자열만 숫자 타입을 변환 가능)
3. `+` 단항 산술 연산자를 이용하는 방법
4. `*` 산술 연산자를 이용하는 방법

```jsx
// 1. Number 생성자 함수를 new 연산자 없이 호출하는 방법
Number("0"); // 0
Number("-1"); // -1
Number(true); // 1

// 2. parseInt.parseFloat 함수를 사용하는 방법(문자열만 숫자 타입을 변환 가능)
parseInt("0"); // 0

// 3. + 단항 산술 연산자를 이용하는 방법
+"0"; // 0
+"-1"; // -1
+"10.53"; // 10.53
+true + // 1
  false; // 0

// 4. * 산술 연산자를 이용하는 방법
"0" * 1; // 0
"-1" * 1; // -1
true * 1; // 1
false * 1; // 0
```

### 불리언 타입으로 변환

---

1. `Boolean 생성자 함수`를 new 연산자 없이 호출하는 방법
2. `!` 부정 논리 연산자를 두 번 사용하는 방법

```jsx
// 1. Boolean 생성자 함수를 new 연산자 없이 호출하는 방법
Boolean("x"); // true
Boolean(""); // false
Boolean("false"); // true

Boolean(null); // false
Boolean(undefined); // false

Boolean({}); // true
Boolean([]); // true

// 2. ! 부정 논리 연산자를 두 번 사용하는 방법
!!"x"; // true
!!""; // false
!!"false"; // true

!!0; // false
!!1; // true
!!Infinity; // true
!!NaN; // false
```

## 단축 평가

---

- 논리 연산의 결과를 결정하는 피연산자를 `타입 변환하지않고 그대로 반환`
- 표현식을 평가하는 도중에 평가 결과가 확정된 경우 나머지 평가 과정을 생략

| 단축 평가 표현식  | 평가 결과 |
| ----------------- | --------- | -------- | -------- |
| true              |           | anything | true     |
| false             |           | anything | anything |
| true && anything  | anything  |
| false && anything | false     |

### 논리 연산자를 사용한 단축 평가

---

`논리합(||)` 또는 `논리곱(&&)` 연산자 표현식은 언제나 2개의 피연산자 중 어느 한쪽의 평가된다.

- `논리곱(&&) 연산자`
  - 두 개의 피연산자가 모두 true일 때 true로 반환
  - 좌항에서 우항으로 평가가 진행
  - 논리 연산의 결과를 결정하는 두번째 피연산자 반환
    ```jsx
    "Cat" && "Dog"; // 'Dog'
    ```
- `논리합(||) 연산자`

  - 두 개의 피연산자 중 하나만 true여도 true로 반환
  - 좌항에서 우항으로 평가가 진행
  - 논리 연산의 결과를 결정한 첫 번째 피연산자 반환
    ```jsx
    "Cat" || "Dog"; // 'Cat'
    ```

- `단축 평가`(논리합(||), 논리곱(&&)) 를 사용하면 `if문`을 대체 가능

```jsx
var done = true;
var message = "";

if (done) message = "완료";
// done이 true라면 message에 '완료'를 할당
message = done && "완료";
console.log(message); // 완료
```

```jsx
var done = false;
var message = "";

if (!done) message = "미완료";
// done이 false라면 message에 '미완료'를 할당
message = done || "미완료";
console.log(message); // 미완료
```

- `삼항 조건 연산자`는 `if…else문` 대체 가능

```jsx
var done = true;
var message = "";

if (done) message = "완료";
else message = "미완료";

message = done ? "완료" : "미완료";
```

**단축 평가의 유용한 사용**

1. 객체를 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때

- 객체를 가리키기를 기대하는 변수가 null 또는 undefined인 경우 객체의 프로퍼티를 참조하면 타입 에러 발생 ⇒ 프로그램 종료

```jsx
var elem = null;
var value = elem.value; // Uncaught TypeError: Cannot read properties of null (reading 'value')
```

- 단축 평가 사용 ⇒ 에러 발생 x

```jsx
var elem = null;
// elem이 null이거나 undefined와 같은 Falsy값이면 elem으로 평가
// elem이 Truthy 값이면 elem.value로 평가
var value = elem && elem.value; // null
```

1. 함수 매개변수에 기본값을 설정할 때

- 함수를 호출할 때 인수를 전달하지 않으면 매개변수에는 undefined가 할당
- 단축 평가 사용 ⇒ 기본값을 설정 ⇒ undefined로 인해 발생할 수 있는 에러 방지 가능

```jsx
// 단축 평가를 사용한 매개변수의 기본값 설정
function getStringLength(str) {
	str = str || '';
	retur str.legnth;
}

getStringLength();  // 0
getStringLength('hi');  // 2

// ES6의 매개변수의 기본값 설정
function getStringLength(str = '') {
	retur str.legnth;
}

getStringLength();  // 0
getStringLength('hi');  // 2
```

### 옵셔널 체이닝 연산자

---

- `?.` : 옵셔널 체이닝 연산자
- `좌항`의 피연산자가 `null` 또는 `undefined` ⇒ `undefined`를 반환
- 그렇지 않은 경우 ⇒ `우항의 프로퍼티를 참조`

```jsx
var elem = null;

var value = elem?.value;
console.log(value); // undefined
```

- 객체를 가리키기를 기대하는 변수가 `null` 또는 `undefined`가 아닌지 확인하고 `프로퍼티를 참조할 때 유용`
- 옵셔널 체이닝 도입 이전에는 `논리곱을 사용한 단축 평가`를 통해 `null` 또는 `undefined`인지 확인

```jsx
var elem = null;
var value = elem && elem.value; // null
```

<aside>
💡 **논리 연산자 (&&) vs 옵셔널 체이닝 연산자**

- 논리 연산자는 `좌항 피연산자가 Falsy값`이면, 좌항 피연산자를 `그대로 반환` (0 또는 ‘’은 객체로 평가될 때도 있음 (21장) )

```jsx
var str = "";

var length = str && str.length;
// 문자열의 길이를 참조하지 못함
console.log(length); // ''
```

- 옵셔널 체이닝 연산자는 `좌항 피연산자가 Falsy값`이더라도 `null` 또는 `undefined`가 `아니면` `우항의 프로퍼티 참조`를 이어감

```jsx
var str = "";

var length = str?.length;
console.log(length); //
```

</aside>

### null 병합 연산자

---

- `??` : 병합 (nullish coalescing) 연산자
- 변수에 `기본값`을 설정할 때 유용
- `좌항`의 피연산자: `null` or `undefined` ⇒ `우항의 피연산자 반환`
- 그렇지 않은 경우 ⇒ `좌항의 피연산자 반환`

```jsx
var foo = null ?? "default string";
console.log(foo);
```

<aside>
💡 **논리 연산자 (||) vs null 병합 연산자**

- 논리 연산자는 `좌항 피연산자가 Falsy값`이면, `우항` 피연산자를 `반환` (0 또는 ‘’도 기본값으로 유효하다면 예기치 않은 동작이 발생)

```jsx
var foo = "" || "default string";
console.log(foo); // 'default string'
```

- null 병합 연산자는 `좌항 피연산자가 Falsy값`이더라도 `null` 또는 `undefined`가 `아니면` `좌항`의 피연산자를 그대로 `반환`
</aside>
