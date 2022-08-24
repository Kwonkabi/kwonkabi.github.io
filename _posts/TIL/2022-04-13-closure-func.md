---
title: "클로저(closure)"
excerpt: "클로저란 무엇일까,,"

categories: javascript
tags: [javascript, closure]

toc: false
toc_sticky: true

author_profile: true
sidebar: false
---

지금까지 클로저를 이해하기 어려웠던 이유는.. 클로저가 아닌 함수를 본 적이 없어서다. 자바스크립트는 기본적으로 모든 함수가 클로저 함수인 것이나 마찬가지이다. 다음의 예시를 보자.

```javascript
function add1(a, b) {
  return a + b;
}

let poison = 0;

function add2(a, b) {
  return a + b + poison;
}

add1(6, 11); // returns 17
add2(6, 11); // returns 17

poison = 3;
add1(6, 11); // returns 17
add2(6, 11); // returns 20
```

처음 add1함수와 add2함수를 실행 했을 때, 즉 poison 값이 0이었을 때는 두 함수 모두 17을 반환했다. 그런데 몰래 poison 변수에 3을 할당했다면, add2는 갑자기 20을 리턴하게 된다.

만약 여럿이서 함께 코드를 짜는데, 누군가 poison이라는 변수가 있는 줄 모르고 add2 함수를 잘 사용하고 있었다고 치자. 그러다가 poison 값이 바뀌게 되면 이상하게 작동하게 될 것이다.

함수가 선언된 모양만 보고서는 함수 밖에서 변수를 가져오는지(독을 품고 있는지) 안 가져오는지 알 방법이 없다. 위의 예시에서 외부 변수와 함수를 합친 요 부분을 바로 클로저(closure)라고 한다!

```javascript
let poison = 0;

function add2(a, b) {
  return a + b + poison;
}
```

다른 프로그래밍 언어에서는 외부 함수를 가져다 쓰지 않는다고 표현해주거나, 혹은 가져다 쓰겠다고 표현해주는 다른 문법이 있다고 한다. 그런데 자바스크립트에는 그런 게 따로 없기 때문에 이해하기가 어려웠던 것..!

<br>

📌 참고: [**자바스크립트 클로저, 쉽게 이해하기**](https://www.youtube.com/watch?v=KmpofpqkitA&ab_channel=Taehoon)
