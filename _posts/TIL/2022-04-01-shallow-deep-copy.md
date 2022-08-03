---
title: "얕은 복사(shallow copy)와 깊은 복사(deep copy)"
excerpt: "다양한 방법으로 깊은 복사를 시도해보자!"

categories: javascript
tags:
  [
    javascript,
    shallow copy,
    deep copy,
    Object.assign(),
    spread-operator,
    JSON.parse(),
    JSON.stringify(),
    lodash,
  ]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 얕은 복사

> 얕은 복사는 참조(주소)값의 복사를 의미한다.

```js
const obj = { vaule: 1 };
const newObj = obj;

newObj.vaule = 2;

console.log(obj.vaule); // 2
console.log(obj === newObj); // true
```

obj 객체를 새로운 newObj 객체에 할당하였다. 이를 **참조 할당**이라 부른다. 복사 후 newObj 객체의 value값을 변경하면 기존의 obj.value값도 같이 변경된다. 두 객체를 비교해도 true로 나온다. <br>

이렇게 자바스크립트의 참조 타입은 얕은 복사가 된다. 즉, 데이터가 그대로 생성되는 것이 아니라 해당 데이터의 참조 값(메모리 주소)를 전달하여 결국 한 데이터를 공유하는 것을 의미한다. 진정한 의미의 복사라고 볼 수 없다. <br>

다음의 두 가지 방법은 1차원 객체에서는 복사본을 만들어내지만, 2차원 객체에서는 깊은 복사가 이루어지지 않는다.

<br>

#### 1.1. Object.assign()

> Object.assign() 메서드의 첫 번째 인수로 빈 객체를 넣어주고, 두 번째 인수로 할당할 객체를 넣어준다.

```js
const obj = { a: 1 };
const newObj = Object.assign({}, obj);

newObj.a = 2;

console.log(obj); // { a: 1 }
console.log(obj === newObj); // false
```

새로운 newObj 객체를 Object.assign() 메소드를 사용하여 생성하였다. newObj.a 값을 변경하여도 기존의 obj의 값은 변하지 않으며, 두 객체를 비교해도 false가 뜬다. 즉, 서로 참조값이 다르다는 것을 알 수 있다.

<br>

**하지만 Object.assign()는 2차원 객체의 깊은 복사가 이루어지지 않는다. Object.assign()를 활용한 복사는 완벽한 깊은 복사가 아니다.**

```js
const obj = {
  a: 1,
  b: {
    c: 2,
  },
};

const newObj = Object.assign({}, obj);

newObj.b.c = 3;

console.log(obj); // { a: 1, b: { c: 3 } }
console.log(obj.b.c === newObj.b.c); // true
```

obj 객체처럼 프로퍼티 값으로 객체를 갖고 있는 2차원 객체일 경우, Object.assign() 메서드를 사용해 newObj 객체를 생성해도 깊은 복사가 이루어지지 않는다. newObj.b.c의 값을 변경한 후 기존 obj 객체를 출력해보면 obj.b.c의 값도 3으로 변경된 것을 볼 수 있다. 그 이유는, 복사된 하위 객체 `{c: 2}`도 결국 객체이기 때문이다. <br>

스프레드 연산자를 사용하여 객체를 복사해도 같은 문제가 있다.

<br>

#### 1.2. 스프레드 연산자

```js
const obj = { a: 1 };
const newObj = { ...obj };

console.log(obj === newObj); // false

newObj.a = 2;

console.log(newObj); // {a: 2}
console.log(obj); // {a: 1}
```

스프레드 연산자를 사용해 1차원 객체를 복사했을 때는 복사본이 생성되었다. 비교했을 때 서로 같다고 출력되지도 않으며, newObj의 프로퍼티 값을 변경해주어도 obj에는 영향이 없다. <br>

하지만 Object.assign() 메서드와 마찬가지로 2차원 객체의 경우, 깊은 복사가 이루어지지 않는다.

```js
const obj = {
  a: 1,
  b: {
    c: 2,
  },
};

const newObj = { ...obj };

newObj.b.c = 3;

console.log(obj); // { a: 1, b: { c: 3 } }
console.log(obj.b.c === newObj.b.c); // true
```

<br>

---

<br>

### 2. 깊은 복사

깊은 복사를 할 수 있는 방법이 세 가지 있다.

<br>

#### 2.1. JSON.stringify()와 JSON.parse()

객체의 깊은 복사를 위해 JSON 객체의 stringify(), parse() 메서드를 사용할 수 있다. 객체를 문자열로 바꾼 다음 다시 객체로 바꿔주는 것이다.

```js
const obj = {
  a: 1,
  b: {
    c: 2,
  },
};

const newObj = JSON.parse(JSON.stringify(obj));

newObj.b.c = 3;

console.log(obj); // { a: 1, b: { c: 2 } }
console.log(obj.b.c === newObj.b.c); // false
```

이 방법을 사용하면 2차원 객체도 깊은 복사가 이루어진 것을 알 수 있다. 하지만 이 방법에는 두 가지 문제점이 있다.

1. 속도가 느리다(성능이 좋지 않다).
2. 프로퍼티 값으로 함수를 만나면 undefined를 반환한다.

```js
const obj = {
  a: 1,
  b: {
    c: 2,
  },
  func: function () {
    return this.a;
  },
};

const newObj = JSON.parse(JSON.stringify(obj));

console.log(newObj.func); // undefined
```

<br>

#### 2.2. lodash의 cloneDeep()

lodash 모듈의 cloneDeep() 메소드를 이용하여 객체의 깊은 복사가 가능하다. `npm i lodash`로 모듈을 설치해준 뒤 코드를 실행해보자.

```js
const lodash = require("lodash");

const obj = {
  a: 1,
  b: {
    c: 2,
  },
  func: function () {
    return this.a;
  },
};

const newObj = lodash.cloneDeep(obj);

newObj.b.c = 3;
console.log(obj); // { a: 1, b: { c: 2 }, func: [Function: func] }
console.log(obj.b.c === newObj.b.c); // false
```

매우 간단하게 깊은 복사를 구현할 수 있다.
