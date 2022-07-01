---
title: "flatten과 unflatten"
excerpt: "flatten과 unflatten 이해하기!"

categories: coding
tags: [flatten, unflatten, flat()]

toc: false
toc_sticky: true

author_profile: true
sidebar: false
---

자바스크립트에서 flatten은 말 그대로, 중첩 배열이나 객체 등 깊이가 있는 녀석들을 깊이가 없도록(줄도록) 평평한 형태로 보여주는 기능이다. 메서드나 라이브러리를 사용할 수 있다.


flat() 메서드는 중첩배열의 모든 하위 배열 요소를 지정한 깊이까지 (지정한 횟수만큼) 이어 붙인 새로운 배열을 리턴해준다. 뿐만 아니라 배열 내에 빈 요소가 있을 경우 삭제해준다. 기본 문법은 다음과 같다.

```javascript
const newArr = arr.flat([depth])
```

```javascript
// depth 디폴트는 1이다
const arr1 = [1, 2, [3, 4]];
arr1.flat();
// [1, 2, 3, 4]

// depth 디폴트는 1이다
const arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();
// [1, 2, 3, 4, [5, 6]]

// flatten 두 번!
const arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]

// flatten 무한으로 즐기기~
const arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]];
arr4.flat(Infinity);
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// 빈 요소 제거
const arr5 = [1, 2, , 4, 5];
arr5.flat();
// [1, 2, 4, 5]
```

arr-flatten-unflatten 라이브러리를 사용하면 flatten뿐만 아니라 unflatten도 사용할 수 있다. unflatten이란, flatten된 객체나 배열을 다시 깊이 있게 바꿔주는 것이다.

[arr-flatten-unflatten](https://www.npmjs.com/package/arr-flatten-unflatten)

```javascript
const { flatten, unflatten } = require("arr-flatten-unflatten");

let flat = flatten([2, 4, [8, [2, [32, 64]], 7], 5]);
/**
 * => {
 * "[0]": 2,
 * "[1]": 4,
 * "[2][0]": 8,
 * "[2][1][0]": 2,
 * "[2][1][1][0]": 32,
 * "[2][1][1][1]": 64,
 * "[2][2]": 7,
 * "[3]": 5
 * }
 * */

unflatten(flat);
// => [2, 4, [8, [2, [32, 64]], 7], 5]
```

