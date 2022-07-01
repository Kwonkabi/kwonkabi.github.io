---
title: "구조분해할당 (destructuring assignment)"
excerpt: "구조분해할당이 뭔지, 어떻게 쓰는지 알아보자!"

categories: coding
tags: [javascript, destructuring assignment]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 구조분해할당이란?
구조분해할당 구문은 배열이나 객체의 속성을 분해한 다음, 그 값을 변수에 담을 수 있도록 하는 표현식이다.

```javascript
let [x, y] = [1, 2];

console.log(x); // 1
console.log(y); // 2
```
기본 구조는 위와 같다. x에 1이 들어가고, y에는 2가 들어간다.

#### 1.1. 배열 구조 분해

```javascript
let friends = ['소현', '서형', '동현'];
let [friend1, friend2, friend3] = friends;

console.log(friend1) // '소현'
console.log(friend2) // '서형'
console.log(friend3) // '동현'
```

friends라는 배열이 있괴, friend1,2,3라는 변수를 배열로 만들어 선언하고 friends 변수를 할당했다. 이때, 

```javascript
let [friend1, friend2, friend3] = friends;
```
의 의미는

```javascript
let friend1 = friends[0];
let friend2 = friends[1];
let friend3 = friends[2];
```
와 같다.

split을 이용할 수도 있다.

```javascript
let fruits = "apple-banana-grape";
let [fruit1, fruit2, fruit3] = fruits.split('-');

console.log(fruit1); // 'apple'
console.log(fruit2); // 'banana'
console.log(fruit3); // 'grape'
```
위의 경우처럼 '-' 기준으로 나누면 fruits.split('-')은 ['apple', 'banana', 'grape']라는 결과가 나오기 때문이다.

#### 1.2. 기본값
그런데, 만약 해당하는 값이 없는 경우는 어떨까?

```javascript
let [a, b, c] = [1, 2];
```
a에는 1이, b에는 2가 들어갈 텐데, c에는 뭐가 들어가게 될까? 바로 undefined가 들어간다. 이럴 때 다음과 같이 기본값을 지정해주면 에러를 방지할 수 있다.

```javascript
let [a=3, b=4, c=5] = [1, 2];

console.log(a); // 1
console.log(b); // 2
console.log(c); // 3
```
이 경우 a와 b는 배열에서 얻어온 값이 되었고, c는 기본값인 3이 된다.

#### 1.3. 일부 반환값 무시

공백과 쉼표를 이용해서 일부 반환값을 무시할 수 있다.

```javascript
let [fruit1, , fruit2] = ['apple', 'banana', 'grape', 'strawberry'];

console.log(fruit1); // 'apple'
console.log(fruit2); // 'grape'
```
이 경우 두번째가 비어 있기 때문에 'banana'는 건너뛰고 grape를 반환한다. 또한 네 번째 strawberry도 사용된 곳이 없어서 무시된다.

#### 1.4. 바꿔치기
이미 할당된 변수를 바꾸는 방법도 있다. 배열을 만들어서 뒤집어주면 끝!

```javascript
let a = 1;
let b = 2;

[a, b] = [b, a];

console.log(a) // 2
console.log(b) // 1
```