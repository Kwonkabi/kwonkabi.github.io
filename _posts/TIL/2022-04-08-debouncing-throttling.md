---
title: "쓰로틀링(throttling)과 디바운싱(debouncing)"
excerpt: "쓰로틀과 디바운스 이해하기"

categories: javascript
tags: [throttle, throttling, debounce, debouncing]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

쓰로틀링과 디바운싱 방법 모두 '요청이나 처리 빈도를 제한하거나 지연시키는 프로그래밍 기법'이다. 어떤 이벤트가 특정 시간 내에 너무 자주 발생되면 성능 문제를 야기할 수 있어서 적절히 제어하여 과도한 호출이 일어나지 않도록 해야 한다.

<br>

### 1. 쓰로틀링(throttling)

**쓰로틀링**이란, 일정 시간 내에 마지막으로 실행된 이벤트만 실행하는 방법이다. <br>

연이어 발생한 이벤트에 대해 일정한 `delay`를 포함 시켜, 연속적으로 발생하는 이벤트는 무시하는 방식으로 사용된다. 즉, 지정한 `delay`동안 호출된 함수는 무시한다. <br>

어떤 이벤트가 발생하면, 우선 이벤트의 발생을 막은 뒤에 타이머를 동작시키고, 타이머가 종료되기 전 마지막으로 발생한 이벤트만 최종적으로 발생시킨다. 대표적인 예시가 **스크롤 기능**이다.

![throttling](/assets/images/throttling_debouncing/throttle.png)

<br>

---

<br>

### 2. 디바운싱(debouncing)

**디바운싱**이란, 일정 시간 안에 해당 이벤트가 다시 발생하지 않으면 이벤트를 발생시키는 방법이다. <br>

연이어 발생한 이벤트를 하나의 그룹으로 묶어서 처리하는 방식인데, 그룹에서 마지막으로 처리된 함수를 처리하는 방식이다. <br>

쓰로틀링과 마찬가지로 이벤트가 발생하면 타이머를 실행시킨다. 타이머가 종료되기 전에 이벤트가 다시 발생하면 타이머를 초기화시킨다. 타이머가 종료되면 마지막으로 발생한 이벤트를 최종적으로 발생시킨다. <br>

대표적인 예시가 **검색 기능**이다. 우리가 검색을 할 때 엔터를 치지 않더라도, 사용자가 입력을 멈추고 일정 시간이 지나면 자동으로 함수를 실행시켜 검색 결과를 보여주는 것이다.<br>

![debouncing](/assets/images/throttling_debouncing/debounce.png)

<br>

#### Lodash 디바운싱 구현

Lodash는 자바스크립트의 유틸리티 라이브러리다. 내장되어 있는 유용한 함수가 많기 때문에 자주 사용된다. <br>

Lodash의 많은 기능 중 디바운싱을 사용해보려 한다. 먼저 Lodash를 사용하기 위해서는 설치를 해야 한다.

```js
yarn add lodash
yarn add -D @types/lodash
```

<br>

> 디바운스(debounce)는 반복적인 동작을 강제적으로 대기하는 것을 말한다.

<br>

예를 들어, 우리가 input에 onChange를 이용해 console.log()를 확인해 보면 우리가 하나하나 입력할 때마다 onChange가 실행되는 것을 볼 수 있다.

```js
import { debounce } from "lodash";

export default function DebounceTest() {
  const handleOnChange = (e) => {
    console.log(e.target.value);
  };

  return (
    <>
      <input onChange={handleOnChange}></input>
    </>
  );
}
```

디바운싱 방법을 사용하지 않고 인풋창에 '디바운싱'을 검색했다면 ['ㄷ', '디', '딥', '디바', '디방', '디바우', '디바운', '디바운ㅅ', '디바운시', '디바운싱']을 콘솔에서 볼 수 있을 것이다. 이럴 경우 일정 시간 동안 중간 과정을 없애고 결과만 한 번에 실행해주는 게 디바운싱이다.

<br>

```js
import { debounce } from "lodash";

export default function DebounceTest() {
  const handleOnChange = debounce((e) => {
    console.log(e.target.value);
  }, 500);

  return (
    <>
      <input onChange={handleOnChange}></input>
    </>
  );
}
```

**debounce**는 setTimeout과 사용방법이 똑같다. <br>

`debounce(콜백함수, 시간)` <br>

첫 번째 인자로는 **실행시키고 싶은 함수**가 들어가고, 두 번째 인자로는 **시간**이 들어간다. <br>

debounce는 우리가 **두 번째 인자로 넣어준 시간 동안 아무 일도 하지 않았을 때 콜백함수를 실행**시킨다. 즉, 우리가 **무언가를 계속 입력하고 있으면 함수를 실행시키지 않고, 우리가 입력을 끝내고 가만히 있으면 그때 함수 결과를 보여준다.**
