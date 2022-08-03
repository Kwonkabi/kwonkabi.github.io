---
title: "스테이트 끌어올리기"
excerpt: "리액트는 하향식 단방향 데이터 흐름 원칙!!"

categories: react
tags: [react, state, lifting-state-up]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

React의 데이터 흐름은 상위 컴포넌트에서 하위 컴포넌트로 전달하는 **하향식(top-down)이자 단방향 데이터 흐름(one-way data flow)**이다. 하지만 하위 컴포넌트에서 이벤트가 일어나 상위 컴포넌트의 상태를 갱신하는 경우가 존재하며, 이는 마치 역방향 데이터 흐름처럼 보인다.<br>

이는 state 끌어올리기를 했기 때문이다. 종종 동일한 데이터에 대한 변경사항을 여러 컴포넌트에 반영해야 할 필요가 있다. 이때 가장 가까운 공통의 조상 즉 상위 컴포넌트에 정의된 state와 state를 변경하는 함수(handler) 자체를 끌어올려준 다음 props로 하위 컴포넌트에 그것들을 전달하고, 하위 컴포넌트에서 그 함수를 실행하는 것이다. <br>

이것을 state 끌어올리기(Lifting state up)라고 하며, 공유될 상태를 소유한 컴포넌트가 진리의 원천(source of truth)이 된다. 이것이 단반향 데이터 흐름의 원칙을 지킬 수 있도록 React에서 제시한 방법이다.

<br>
다음은 스테이트 끌어올리기를 사용한 예제이다. Child1에 있던 함수를 Child2에서도 사용하고, 똑같이 동시에 작동하도록 하고 싶은 상황이다.

<br>

**진리의 원천(공통의 조상) 컴포넌트**

```js
// 자식1에서 끌올함
import { useState } from "react";

import Child1 from "../../src/components/units/board/14-lifting-state-up/Child1";
import Child2 from "../../src/components/units/board/14-lifting-state-up/Child2";

export default function LiftingStateUpPage() {
  // 자식1에서 끌올함
  const [count, setCount] = useState(0);
  // 자식1에서 끌올함
  const onClickCountUp = () => {
    setCount((prev) => prev + 1);
  };

  return (
    <div>
      {/* 방법1: 자식1에게 적용 */}
      <Child1 count={count} setCount={setCount} />
      <div>====================================</div>
      {/* 방법2: 자식2에게 적용 */}
      <Child2 count={count} onClickCountUp={onClickCountUp} />
    </div>
  );
}
```

<br>

**Child1 컴포넌트**

```js
// import { useState } from "react";

export default function Child1(props) {
  // const [count, setCount] = useState(0);

  // onClickCountUp = () => {
  //   setCount((prev) => prev + 1);
  // }

  const aaa = () => {
    props.setCount((prev) => prev + 1);
  };

  return (
    <div>
      <div>자식1의 카운트: {props.count}</div>
      {/* <button onClick={onClickCountUp}>카운트 올리기!</button> */}
      <button onClick={aaa}>카운트 올리기!</button>
    </div>
  );
}
```

<br>

**Child2 컴포넌트**

```js
export default function Child2(props) {
  return (
    <div>
      <div>자식2의 카운트: {props.count}</div>
      <button onClick={props.onClickCountUp}>카운트 올리기!</button>
    </div>
  );
}
```

<br>

Child1에서 주석으로 처리해준 부분은 공통의 조상 컴포넌트로 끌어올려졌다. 끌어올려진 스테이트를 사용하는 방법은 두 가지가 있다.

<br>

- 방법 1: setState 함수를 prop으로 받아와서 그것을 사용해 자식 컴포넌트에서 새로운 함수를 생성하는 방법

- 방법 2: 끌어올려진 onClick 함수를 prop으로 받아와서 그대로 사용하는 방법

어떤 방법을 사용해도 두 자식 컴포넌트가 하나처럼 동작하게 된다.
