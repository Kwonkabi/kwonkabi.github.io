---
title: "디바운스(debounce)와 스로틀(throttle)"
excerpt: "디바운스와 스로틀 이해하기"

categories: javascript
tags: [javascript, debounce, debouncing, throttle, throttling]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

scroll, resize, input, mousemove와 같은 이벤트는 짧은 시간 간격으로 연속해 발생한다. 이러한 이벤트에 바인딩한 이벤트 핸들러는 과도하게 호출되어 성능에 문제를 일으킬 수 있다.

> 디바운스와 스로틀은 '짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화해서 과도한 이벤트 핸들러의 호출을 방지하는 프로그래밍 기법'이다.

디바운스와 스로틀의 구현에는 타이머 함수가 사용된다.

<br>

### 디바운스(debounce)

디바운스는 짧은 시간 간격으로 이벤트가 연속해서 발생하면 이벤트 핸들러를 호출하지 않다가 일정 시간이 경과한 이후에 이벤트 핸들러가 한 번만 호출되도록 한다.

> 디바운스는 짧은 시간 간격으로 발생하는 이벤트를 그룹화해서 마지막에 한 번만 이벤트 핸들러가 호출되도록 한다.

이벤트가 발생하면 타이머를 실행시킨다. 타이머가 종료되기 전에 이벤트가 다시 발생하면 타이머를 초기화시킨다. 타이머가 종료되면 마지막으로 발생한 이벤트를 최종적으로 발생시킨다.

대표적인 예시가 **검색 기능**이다. 우리가 검색을 할 때 엔터를 치지 않더라도, 사용자가 입력을 멈추고 일정 시간이 지나면 자동으로 함수를 실행시켜 검색 결과를 보여주는 것이다.

사용자가 입력을 완료했는지 여부는 정확히 알 수 없으므로 일정 시간동안 텍스트 입력 필드에 값을 입력하지 않으면 입력이 완료된 것으로 간주된다.

<br>

디바운싱 방법을 사용하지 않고 input에 onChange를 이용해 console.log()를 확인해 보면 하나하나 입력할 때마다 onChange가 실행되는 것을 볼 수 있다.

```js
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

예를 들어, 인풋창에 '디바운싱'을 검색했다면 ['ㄷ', '디', '딥', '디바', '디방', '디바우', '디바운', '디바운ㅅ', '디바운시', '디바운싱']을 콘솔에서 볼 수 있을 것이다.

<br>

#### Lodash 디바운싱 구현

Lodash는 자바스크립트의 유틸리티 라이브러리다. 내장되어 있는 유용한 함수가 많기 때문에 자주 사용된다. Lodash의 많은 기능 중 디바운싱을 사용해보려 한다. 먼저 Lodash를 사용하기 위해서는 설치를 해야 한다.

```js
yarn add lodash
yarn add -D @types/lodash
```

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

<br>

---

<br>

### 스로틀(throttle)

자바스크립트에서의 스로틀링은 짧은 시간 내에 과도한 이벤트 실행으로 인해 발생하는 성능 저하를 막기 위해 일정한 time slice를 주기로 이벤트가 한 번만 실행되도록 제한한다.

예를 들어 1초에 100번의 업데이트가 일어나는 DOM 요소에 이벤트 핸들러를 추가한다면, 이벤트는 1초에 100번이나 실행되므로 성능에 무리를 줄 수 있다.

스로틀링을 적용하게 되면 불필요한 이벤트 실행 횟수를 줄일 수 있어 성능을 향상시킬 수 있다. 스로틀링을 적용하면 좋은 예시로는 scroll, resize, drag, mouse 이벤트, 애니메이션 등이 있다.

<br>

---

<br>

### 시각적으로 디바운스 스로틀 이해하기

![System Design Basics: Debounce Vs Throttle](/assets/images/js/debounce_throttle.gif)

[출처: 'System Design Basics: Debounce Vs Throttle'](https://www.youtube.com/watch?v=TBIEArmPywU)
