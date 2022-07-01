---
title: "객체 메서드: Object.keys(), Object.values(), Object.entries(), Object.assign()"
excerpt: "객체 메서드를 예시를 통해 공부해보자!"

categories: coding
tags: [javascript, method, Object.keys(), Object.values(), Object.entries(), Object.assign()]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

[객체 메서드 공부하기 : **MDN - Object**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object#)


### 1. Object.keys()

`Object.keys()` 메서드는 객체의 키를 모아서 문자열 엘리먼트를 가진 배열로 반환한다. 배열도 일종의 객체이기 때문에 배열에도 적용할 수 있다. 그 배열의 엘리먼트 순서는 객체에서 그 키가 해당하는 속성 순서와 동일하다. 그런데 키가 순서 없이 나열된 경우엔 엘리먼트가 오름차순으로 배열 안에 나열되는 것을 볼 수 있다.

#### 1.1. 예시

```javascript
// 객체 1
const object1 = {
  a: 'somestring',
  b: 42,
  c: false
};
console.log(Object.keys(object1)); // expected output: Array ["a", "b", "c"]

// 객체 2
const obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.keys(obj)); // console: ['0', '1', '2']

// 순서 없이 나열된 키의 경우
// 숫자로 된 키가 있는 경우, 숫자 키의 오름차순 순서대로 해당하는 값이 배열 안에 나열된다.
const anObj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.keys(anObj)); // console: ['2', '7', '100']

// 배열
const arr = ['a', 'b', 'c'];
console.log(Object.keys(arr)); // console: ['0', '1', '2']
```

#### 1.2. 알고리즘에 적용해보기

```javascript
/*
  사용자의 입력을 받아 그 값을 저장하고 있는 객체 inputs가 있습니다.
  inputs 객체의 키로는 title, contents, price, writer만 들어갑니다.
  value로는 string, number 타입만 들어갑니다. 

  이때, inputs의 value들이 ("" 과 0)이 아니라면 true, 하나의 value라도 ("" 또는 0)이라면 false를 리턴해주세요.
  
  예시 )
  inputs = {title : "", contents : "내용"} 이라면 title의 value가 빈 문자열이기 때문에 false를 리턴합니다.
  inputs = {title : "제목", writer : "작성자", price : 1500} 이라면 빈 문자열이거나 0인 값이 없기 때문에, true를 리턴해주세요.
*/

function validateInputs(inputs) {
  let keys = Object.keys(inputs);
  for (let i = 0; i < keys.length; i++) {
    return inputs[`${keys[i]}`] === "" || inputs[`${keys[i]}`] === 0
      ? false
      : true;
  }
}
```


### 2. Object.values()
`Object.keys()`가 키를 배열로 반환했다면, `Object.values()` 메서드는 값을 배열로 반환한다. 

#### 2.1. 예시
```javascript
// 객체 1
const object1 = {
  a: 'somestring',
  b: 42,
  c: false
};
console.log(Object.values(object1)); // expected output: Array ["somestring", 42, false]

// 객체 2
const obj = { foo: 'bar', baz: 42 };
console.log(Object.values(obj)); // ['bar', 42]

// 객체 3
const arrayLikeObj1 = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.values(arrayLikeObj1 )); // ['a', 'b', 'c']

// 순서 없이 나열된 키의 경우
// 숫자로 된 키가 있는 경우, 숫자 키의 오름차순 순서대로 해당하는 값이 배열 안에 나열된다.
const arrayLikeObj2 = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.values(arrayLikeObj2 )); // ['b', 'c', 'a']
```


### 3. Object.entries()
`Object.entries()`는 객체의 속성 하나 당 하나의 배열을 반환하는데, 배열 안에는 키와 값이 들어간다.

#### 3.1. 예시
```javascript
// 객체 1
const obj = { foo: 'bar', baz: 42 };
console.log(Object.entries(obj)); // [ ['foo', 'bar'], ['baz', 42] ]

// 객체 2
const obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.entries(obj)); // [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ]

// 순서 없이 나열된 키의 경우
// 숫자로 된 키가 있는 경우, 숫자 키의 오름차순 순서대로 해당하는 값이 배열 안에 나열된다.
const anObj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.entries(anObj)); // [ ['2', 'b'], ['7', 'c'], ['100', 'a'] ]
```

### 4. Object.assign()
`Object.assign()` 메서드는 주어진 객체의 모든 속성을 복사해서 객체로 반환한다. 사용법은 다음과 같다;

```javascript
Object.assign(target, ...sources)
```

target 객체는 source 객체를 적용할 타깃이 된다. 메서드를 사용하고 나면 원본이 수정된다. 만약 객체 원본이 수정되는 걸 원하지 않는다면 target 자리에 빈 객체 {}를 작성해주면 된다(마지막 예시).

#### 4.1. 예시

```javascript
// 객체 복제
const target = { a: 1, b: 2 };
const source = { b: 4, c: 5 };

const returnedTarget = Object.assign(target, source);

console.log(target); // expected output: Object { a: 1, b: 4, c: 5 }
console.log(returnedTarget); // expected output: Object { a: 1, b: 4, c: 5 }
console.log(source) // { b: 4, c: 5 }

// 객체 복제: target으로 빈 객체 사용
const obj = { a: 1 };
const copy = Object.assign({}, obj);
console.log(copy); // { a: 1 }
console.log(obj);  // { a: 1 }

// 객체 합치기
const o1 = { a: 1 };
const o2 = { b: 2 };
const o3 = { c: 3 };

const obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 }, target object
console.log(o2);  // { b: 2 }
console.log(o3);  // { c: 3 }

// 객체 합치기 : 키가 겹치는 경우, 더 나중에 오는 값이 덮어쓴다.
const o1 = { a: 1, b: 1, c: 1 };
const o2 = { b: 2, c: 2 };
const o3 = { c: 3 };

const obj = Object.assign({}, o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 1, c: 1 }
console.log(o2);  // { b: 2, c: 2 }
console.log(o3);  // { c: 3 }
```