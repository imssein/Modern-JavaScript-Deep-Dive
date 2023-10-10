# 17장 생성자 함수에 의한 객체 생성

## Object 생성자 함수

---

- `new` 연산자와 함께 `Object 생성자 함수`를 `호출`하면 `빈 객체`를 `생성`하여 `반환`
- 빈 객체에 `프로퍼티` 또는 `메서드를` 추가하여 객체 완성

```jsx
const person = new Object();

person.name = "Lee";
person.sayHello = function () {
  console.log("Hi! My name is " + this.name);
};

console.log(person); // {name: 'Lee', sayHello: f}
person.sayHello(); // Hi! My name is Lee
```

- **생성자 함수(constructor)**: `new` 연산자와 함께 호출하여 `객체(인스턴스)를 생성`하는 함수
  - 빌트인 생성자 함수 제공(Object, String, Number, Boolean, Function, Array, Date, RegExp, Promise 등)

```jsx
const strObj = new String("Lee");
console.log(typeof strObj); // object
console.log(strObj); // String {'Lee'}

const func = new Function("x", "return x * x");
console.log(typeof func); // function
console.dir(func);

const date = new Date();
console.log(typeof date); // object
console.log(date); // Tue Oct 10 2023 14:43:43 GMT+0900 (한국 표준시)
```

→ 유용한 방법은 아님

## 생성자 함수

---

### 객체 리터럴에 의한 객체 생성 방식의 문제점

---

객체 리터럴에 의한 객체 생성 방식은 직관적이고 간편하지만, 단 하나의 객체만 생성한다. 따라서 동일한 프로퍼티를 갖는 객체를 여러 개 생성해야 하는 경우 비효율적이다.

```jsx
const circle1 = {
	radius: 5,
	getDiameter() {
		return 2 * this.radius;
	}
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
	radius: 10,
	getDiameter() {
		return 2 * this.radius'
	}

};

console.log(circle2.getDiameter()); // 20
```

→ 프로퍼티 구조가 동일함에도 불구하고 매번 같은 프로퍼티와 메서드를 기술해야 한다.

### 생성자 함수에 의한 객체 생성 방식의 장점

---

객체(인스턴스)를 생성하기 위한 `템플릿(클래스)처럼 생성자 함수를 사용`하여 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성할 수 있다.

```jsx
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스의 생성
const circle1 = new Circle(5);
const circle2 = new Circle(10);
```

> 🍏 **this**
>
> - `객체 자신의 프로퍼티`나 `메서드를 참조`하기 위한 `자기 참조 변수`
> - this 바인딩은 `함수 호출 방식`에 따라 `동적`으로 결정
>
> | 함수 호출 방식       | this가 가리키는 값(this 바인딩)        |
> | -------------------- | -------------------------------------- |
> | 일반 함수로서 호출   | 전역 객체                              |
> | 메서드로서 호출      | 메서드를 호출한 객체(마침표 앞의 객체) |
> | 생성자 함수로서 호출 | 생성자 함수가 (미래에) 생성할 인스턴스 |

- `new` 연산자와 `함께 호출`하면 해당 함수는 `생성자 함수`로 동작
- `new` 연산자 `없이 호출`하면 해당 함수는 `일반 함수`로 동작

```jsx
// new 연산자없이 사용
const circle3 = Circle(5);
// 반환문이 없어 암묵적으로 undefined를 반환
console.log(circle3); // undefined
// 전역 객체를 가리킴
console.log(radius); // 5
```

### 생성자 함수의 인스턴스 생성 과정

---

생성자 함수의 역할: `인스턴스를 생성`하는 것과 `생성된 인스턴스를 초기화(인스턴스 프로퍼티 추가 및 초기값 할당)`

```jsx
// 생성자 함수
function Circle(radius) {
  // 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
// 인스턴스 생성
const circle1 = new Circle(5);
```

→ 암묵적인 처리를 통해 인스턴스를 생성하고 반환한다.

**1. 인스턴스 생성과 this 바인딩**

- 암묵적으로 `빈 객체`가 생성된다. `인스턴스`는 `this에 바인딩`된다.
- 런타임 이전에 실행된다.

> 🍏 **바인딩**
>
> - `식별자`와 `값`을 `연결`하는 과정
> - ex) 변수 이름(식별자)과 확보된 메모리 공간의 주소를 바인딩하는 것
> - **this 바인딩**은 `this`(식별자 역할)와 `this가 가리킬 객체`를 `바인딩`하는 것

```jsx
function Circle(radius) {
  // 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
  console.log(this); // Circle {}

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

**2. 인스턴스 초기화**

`this에 바인딩`되어 있는 `인스턴스`에 `프로퍼티나 메서드를 추가`하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 `초기화`하거나 `고정값을 할당`

```jsx
function Circle(radius) {
  // 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

**3. 인스턴스 반환**

생성자 함수 내부에서 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this를 암묵적으로 반환`한다.

```jsx
function Circle(radius) {
  // 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
  // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
}

// 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환한다.
const circle1 = new Circle(5);
```

this가 아닌 `다른 객체`를 `명시적으로 반환`하면 `명시한 객체가 반환`된다.

```jsx
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
  return {};
}

const circle = new Circle(5);
console.log(circle); // {}
```

`명시적으로 원시 값을 반환`하면 원시 값 반환은 무시되고 `암묵적으로 this가 반환`된다.

```jsx
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
  return 100;
}

const circle = new Circle(5);
console.log(circle); // Circle {radius: 5, getDiameter: ƒ}
```

**→ 생성자 함수 내부에서 return 문을 반드시 생략!!!**

### 내부 메서드 [[Call]] 과 [[Construct]]

---

함수 객체는 callble이지만 모든 함수 객체가 constructor인 것은 아니다.

### constructor와 non-construct의 구분

---

- 함수 정의 방식에 따라 함수를 constructor와 non-constructor로 구분한다.
  - constructor: 함수 선언문, 함수 표현식, 클래스
  - non-constructor: 메서드, 화살표 함수

```jsx
// 일반 함수 정의: 함수 선언문, 함수 표현식
function foo() {}
const bar = function () {};
// 프로퍼티 x의 값으로 할당된 것은 일반 함수로 정의된 함수다. 메서드로 인정 x
const baz = {
  x: function () {},
};

// 일반 함수 정의된 함수만이 constructor다.
new foo();
new bar();
new baz.x(); // -> x {}

// 화살표 정의 함수
const arrow = () => {};
new arrow(); // TypeError: arrow is not a constructor

// 메서드 정의
const obj = {
  x() {},
};

new obj.x(); // TypeError: obj.x is not a constructor
```

### new 연산자

---

```jsx
// 생성자 함수로 정의하지않은 일반 함수
function add(x, y) {
  return x + y;
}

// 생성자 함수로 정의하지 않은 함수를 new 연산자와 함께 호출
let inst = new add();

// 함수가 객체를 반환하지 않았으므로 반환문이 무시
console.log(inst); // {}

// 객체를 반환하는 일반 함수
function createUser(name, role) {
  return { name, role };
}

// 일반 함수를 new 연산자와 함께 호출
inst = new createUser("Lee", "admin");
console.log(inst); // {name: 'Lee', role: 'admin'}
```

- `new` 연산자 `없이` 생성자 함수를 `호출`하면 `일반 함수`로 호출

> 🍏 `생성자 함수`는 일반적으로 `첫 문자를 대문자로 기술`하는 `파스칼 케이스`로 명명하여 일반 함수와 구별

### new.target

---

생성자 함수가 new 연산자 없이 호출되는 것을 방지하기 위해 ES6에서 `new.target`을 지원한다.

`new` 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 `new.target`은 `함수 자신`을 가리킨다. new 연산자 `없이` `일반 함수`로서 호출된 함수 내부의 `new.target`은 `undefined`이다.

```jsx
function Circle(radius) {
  if (!new.target) {
    return new Circle(radius);
  }
  // ...
}
```

> 🍏 **스코프 세이프 생성자 패턴**
>
> - new.target은 ES6에서 도입된 최신 문법으로 IE에서 지원하지않아, 스코프 세이프 생성자 패턴 이용 가능하다.
>
> ```jsx
> function Circle(radius) {
>   if (!(this instanceof Circle)) {
>     return new Circle(raidus);
>   }
>   // ...
> }
> ```

- Object와 Function 생성자 함수는 new 연산자 없이 호출해도 동일하게 동작
- String, Number, Boolean은 new 연산자 없이 호출하면 문자열, 숫자, 불리언 값을 반환
