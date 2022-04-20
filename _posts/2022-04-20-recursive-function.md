---
title: "재귀 함수(recursive function)"
excerpt: ""

categories: coding
tags: [recursive function]

toc: false
toc_sticky: true

author_profile: true
sidebar: false
---

### 재귀 함수

**재귀 함수**란, 자기 자신을 호출하는 함수를 말한다. 마트료시카 인형을 예시로 생각하면 이해하기 쉽다. 우리가 인형 안의 인형을 계속 꺼내면서 초콜릿을 찾고 있다고 생각해보자. 1. 만약 인형 안에서 초콜릿을 찾으면 "냠냠냠!"과 함께 함수가 종료된다. 2. 인형을 열었는데 초콜릿이 없다면 인형을 또 연다(함수 다시 실행). 그 사이에 새로운 조건을 추가할 수도 있다. 예를 들면, 3. 초콜릿을 아직 못 찾았는데 더 이상 열 수 있는 인형이 없다면 "여기는 초콜릿 없음!"과 함께 함수가 종료되는 식으로 말이다. pseudo code로 이해를 해보자면 다음과 같다.


```javascript
function processDoll(doll) {
    if ( 인형 안에서 초콜릿을 찾으면 ) return "냠냠냠!";
  // 3. 조건을 추가할 수도 있다
    else if (인형이 더 이상 없으면) return "여기는 초콜릿 없음!";
  // 2. 자기 자신을 부른다 (초콜릿이 없으면, 더 작은 인형을 연다)
    else processDoll()
}
```

#### 예제

팩토리얼 문제를 재귀함수를 이용해서 풀어보면 다음과 같다. 우선 팩토리얼(!)이란, 주어진 수부터 1씩 줄어들어 1까지 곱한 값을 구하는 것이다. 예를 들면, `4!`의 경우, `4 * 3 * 2 * 1 = 24`이다. (주의! 0!은 1이다.)이걸 재귀함수를 이용한 코드로 작성해보면 다음과 같다.

```javascript
function factorial(n) {
  if (n === 1 || n === 0) return 1;
  else return n * factorial(n-1);
}
```

n이 4일 때, 4는 1이나 0이 아니기 때문에 else로 내려가고, `4 * factorial(3)`이 된다. 그럼 3은 또 1이나 0이 아니기 때문에 `3 * factorial(2)`가 된다. 2는 1이나 0이 아니기 때문에 `2 * factorial(1)`이 된다. 결국 n이 1이 되기 때문에 1을 리턴하고 함수가 종료된다. 거꾸로 올라가보면, 결국 24가 나온다.

```javascript

`factorial(2)` === `2 * factorial(1)` === `2 * 1` => 2
`factorial(3)` === `3 * factorial(2)` === `3 * 2` => 6
`fatorial(4)` === `4 * factorial(3)` === `4 * 6` => 24

```

※ 참고 영상: [**Best Javascript Recursion Explanation on YouTube**](https://www.youtube.com/watch?v=LteNqj4DFD8&ab_channel=DevSage)
