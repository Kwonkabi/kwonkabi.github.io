---
title: "함수와 일급객체"
excerpt: "함수가 객체라면.. 무슨 프로퍼티를 가졌을까?"

categories: javascript
tags: [javascript, function, first-class-object]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 일급 객체

다음과 같은 조건을 만족하는 객체를 일급객체라 한다.

- **무명의 리터럴**로 생성할 수 있다. 즉, **런타임에 생성**이 가능하다.
- **변수나 자료구조(객체, 배열 등)에 저장**할 수 있다.
- 함수의 **매개변수에 전달**할 수 있다.
- 함수의 **반환값으로 사용**할 수 있다.

자바스크립트의 함수는 위의 조건을 만족하므로 `일급 객체`이다.

<br>

```js
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
// 런타임(할당 단계)에 함수 리터럴이 평가되어 함수 객체가 생성되고 변수에 할당된다.
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

// 2. 함수는 객체에 저장할 수 있다.
const auxs = { increase, decrease };

// 3. 함수의 매개변수에게 전달할 수 있다.
// 4. 함수의 반환값으로 사용할 수 있다.
function makeCounter(aux) {
  let num = 0;

  return function () {
    num = aux(num);
    return num;
  };
}

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const increaser = makeCounter(auxs.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const decreaser = makeCounter(auxs.decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

- 함수가 일급 객체라는 것은 함수를 객체와 동일하게 사용할 수 있다는 의미다. 객체는 값이므로 함수는 값과 동일하게 취급할 수 있다.

- 따라서 함수는 값을 사용할 수 있는 곳(변수 할당문, 객체의 프로퍼티 값, 배열의 요소, 함수 호출의 인수, 함수 반환문)이라면 어디서든지 리터럴로 정의할 수 있으며 런타임에 함수 객체로 평가된다.

- 일급 객체로서 함수가 가지는 가장 큰 특징은 일반 객체와 같이 함수의 매개변수에 전달할 수 있으며, 함수의 반환값으로도 사용할 수도 있다.

- 일반 객체는 호출할 수 없지만 함수 객체는 호출할 수 있다.

<br>

---

### 2. 함수 객체의 프로퍼티

함수는 객체다. 따라서 함수도 프로퍼티를 가질 수 있다.

```js
function square(number) {
  return number * number;
}

console.dir(square);
```

![](/assets/images/js/func_property.png)

square 함수의 모든 프로퍼티의 프로퍼티 어트리뷰트를 `Object.getOwnPropertyDescriptors` 메서드로 확인해 보면 다음과 같다.

![](/assets/images/js/func_getOwnPropertyDescriptors.png)

이처럼 `arguments`, `caller`, `length`, `name`, `prototype` 프로퍼티는 모두 함수 객체의 데이터 프로퍼티이고 일반 객체에는 없는 **함수 객체 고유의 프로퍼티**다.

<br>

#### arguments 프로퍼티

- 함수 객체의 `arguments` 프로퍼티 값은 `arguments` 객체다. `arguments` 객체는 함수 호출 시 전달된 인수(argument)들의 정보를 담고 있는 순회 가능한 유사 배열 객체이며, 함수 내부에서 지역 변수처럼 사용된다. 즉, 함수 외부에서는 참조할 수 없다.

- 선언된 매개변수의 개수보다 인수를 적게 전달했을 경우 인수가 전달되지 않은 매개변수는 undefined로 초기화된 상태를 유지한다. 매개변수의 개수보다 인수를 더 많이 전달한 경우 초과된 인수는 무시된다.

- 그렇다고 초과된 인수가 그냥 버려지는 것은 아니다. 모든 인수는 암묵적으로 `arguments` 객체의 프로퍼티로 보관된다.

- 인수를 프로퍼티 값으로 소유하며 프로퍼티 키는 인수의 순서를 나타낸다.

- arguments 객체는 매개변수 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용하다.

<br>

#### caller 프로퍼티

caller 프로퍼티는 비표준 프로퍼티이며, 이후 표존화될 예정도 없기 때문에 사용하지 말고 참고로만 알아두자. 함수 객체의 caller 프로퍼티는 함수 자신을 호출한 함수를 가리킨다.

<br>

#### length 프로퍼티

함수 객체의 length 프로퍼티는 함수를 정의할 때 선언한 매개변수의 개수를 가리킨다.

```js
function foo() {}
console.log(foo.length); // 0

function bar(x) {
  return x;
}
console.log(bar.length); // 1

function baz(x, y) {
  return x * y;
}
console.log(baz.length); // 2
```

- 함수 객체의 `length` 프로퍼티는 함수를 정의할 때 **선언한 매개변수의 개수**를 가리킨다.
- 단, `arguments` 객체의 length 프로퍼티는 **인자(argument)의 개수**를 나타낸다.

<br>

#### name 프로퍼티

함수 객체의 `name` 프로퍼티는 **함수 이름**을 나타낸다.

```js
// 기명 함수 표현식
var namedFunc = function foo() {};
console.log(namedFunc.name); // foo

// 익명 함수 표현식
var anonymousFunc = function () {};
// ES5: name 프로퍼티는 빈 문자열을 값으로 갖는다.
// ES6: name 프로퍼티는 함수 객체를 가리키는 변수 이름을 값으로 갖는다.
console.log(anonymousFunc.name); // anonymousFunc

// 함수 선언문(Function declaration)
function bar() {}
console.log(bar.name); // bar
```

함수를 호출할 때는 함수 이름이 아닌 함수 객체를 가리키는 식별자로 호출한다.

<br>

#### proto 접근자 프로퍼티

모든 객체는 [[Prototype]]이라는 내부 슬롯을 갖는다. [[Prototype]] 내부 슬롯은 객체지향 프로그래밍의 상속을 구현하는 프로토타입 객체를 가리킨다.

**proto** 프로퍼티는 [[Prototpye]] 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티다. (내부슬롯에는 직접 접근할 수 없기 때문)

<br>

#### prototype 프로퍼티

prototype 프로퍼티는 생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor만이 소유하는 프로퍼티다.

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}.hasOwnProperty("prototype")); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}.hasOwnProperty("prototype")); // -> false
```

함수가 객체를 생성하는 생성자 함수로 호출될 때 생성자 함수가 생성할 인스턴스의 프로토타입 객체를 가리킨다.
