---
title: "Map과 Filter 메서드"
excerpt: "for문을 대체할 자 누구인가"

categories: javascript
tags: [map, filter, method]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. Map()

실무에서는 비슷한 동작을 반복할 때 for문보다 간편한 map과 filter 메서드를 더 많이 사용한다고 한다. Map은 배열 안의 모든 엘리먼트를 일괄적으로 원하는 대로 바꿔주는 역할을 한다. 기본 형식은 다음과 같다.

배열이름.map((각 요소를 의미하는 매개변수) => (매개변수를 어떻게 바꿔줄 것인지))

예시로 보면 더 쉽게 알 수 있다.

```javascript
const classmates = ["철수", "영희", "훈이"];
const classmatesNew = classmates.map((el) => el + " 어린이");

classmates; // [ '철수', '영희', '훈이' ] : 원본에는 영향 없음
classmatesNew; // [ '철수 어린이', '영희 어린이', '훈이 어린이' ]
```

매개변수의 이름은 대체로 element의 el을 사용한다. 그리고 map 메서드를 사용해주어도 원본에는 변화가 없다는 것을 알 수 있다.

엘리먼트는 꼭 문자열이 아니어도 된다. 객체도 가능하다. 객체 엘리먼트가 있는 배열과 map을 사용한 예시는 다음과 같다.

```javascript
const classmates2 = [{ name: "철수" }, { name: "영희" }, { name: "훈이" }];

classmates2.map((el) => ({ name: el.name + " 어린이" })); // 객체로 받을 거니까 ()안에 {}

// [
//   { name: '철수 어린이' },
//   { name: '영희 어린이' },
//   { name: '훈이 어린이' }
// ]
```

여기서 주목할 점은, 화살표 뒷부분의 소괄호 안에 객체를 의미하는 중괄호를 사용해주어야 한다는 것이다. 여러 키와 밸류를 가질 때도 위와 비슷하다.

```javascript
const classmates3 = [
  { name: "철수 어린이" },
  { name: "영희 어린이" },
  { name: "훈이 어린이" },
];

classmates3.map((el) => ({ name: el.name, school: "다람쥐초등학교" }));

// {name: '철수 어린이', school: '다람쥐초등학교'}
// {name: '영희 어린이', school: '다람쥐초등학교'}
// {name: '훈이 어린이', school: '다람쥐초등학교'}
```

```javascript
const classmates4 = [
  { name: "철수 어린이", age: 13 },
  { name: "영희 어린이", age: 10 },
  { name: "훈이 어린이", age: 11 },
];

classmates4.map((el) => ({
  name: el.name,
  age: el.age,
  school: "토끼초등학교",
}));

// {name: '철수 어린이', age: 13, school: '토끼초등학교'}
// {name: '영희 어린이', age: 10, school: '토끼초등학교'}
// {name: '훈이 어린이', age: 11, school: '토끼초등학교’}
```

map을 사용할 때 소괄호를 생략해도 되는 경우와 그렇지 않은 경우가 있다.

- **소괄호를 생략해도 되는 경우:**

1. 문자열 엘리먼트일 때

```javascript
const classmates5 = ["철수", "영희", "훈이"];
classmates5.map((el) => el + " 어린이");
// ['철수 어린이', '영희 어린이', '훈이 어린이']
```

```javascript
const classmates5 = ["철수", "영희", "훈이"];
classmates5.map((el) => el + " 어린이");
// ['철수 어린이', '영희 어린이', '훈이 어린이']
```

2. 화살표 함수에서 {return}을 생략했을 때

```javascript
const add = (aaa, bbb) => {
  return aaa + bbb;
};
add(1, 2);

const add2 = (aaa, bbb) => aaa + bbb;
add2(1, 2);
// 3

const add3 = (aaa, bbb) => aaa + bbb;
add3(1, 2);
// 3
```

- **소괄호를 생략하면 안 되는 경우:** 객체 엘리먼트를 사용할 때

```javascript
const classmates = [{ name: "철수" }, { name: "영희" }, { name: "훈이" }];

classmates.map((el) => {
  name: el.name + "어린이";
});
// [undefined, undefined, undefined]
```

<br>

---

<br>

### 2. Filter()

Map을 사용하면 원하는 엘리먼트만 가져오는 건 불가능하다. 조건에 맞는 것들만 가져오고 싶다면 filter 메서드를 사용해야 한다. 맵은 요소마다 동일한 로직을 적용하기 때문에 원본의 개수와 결과의 개수가 같다. 필터를 사용하면 결과의 개수가 원본의 개수와 같거나 적다.

filter메서드는 주어진 함수의 테스트를 통화하는 모든 요소를 모아서 새로운 배열로 반환한다. 사용 방법은 다음과 같다.

```javascript
const num = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

num.filter((item) => itmem <= 8);
// [1, 2, 3, 4, 5, 6, 7, 8]
```
