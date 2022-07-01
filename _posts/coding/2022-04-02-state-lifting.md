---
title: "스테이트 끌어올리기(Lifting State Up)"
excerpt: "리액트의 단방향 데이터 흐름과 스테이트 끌어올리기 이해"

categories: coding
tags: [react, state, props, lifting-state-up]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 스테이트 끌어올리기

리액트에서 데이터의 흐름은 상위 컴포넌트에서 하위 컴포넌트로 전달하는 하향식 & 단방향이다. 이러한 **단방향** 데이터 흐름의 장점은 기능 변경 사항에 대한 코드 수정이 적어진다는 것이다. (복붙을 했다면 여기저기 고쳐야 하기 때무네~) 그리고 코드가 덜 복잡핵서 코드 흐름을 알기 쉽다는 장점도 있다!

만약 단방향 데이터 흐름의 원칙을 지키면서, 하위 컴포넌트의 이벤트로 상위 컴포넌트의 상태를 바꾸어 주려면 어떻게 해야 할까? 상위 컴포넌트에 정의된 상태를 변경하는 함수(handler) 자체를 하위 컴포넌트에 props로 전달하고, 하위 컴포넌트에서 이 콜백 함수를 실행하는 것이다. 이렇게 하면 단방향 데이터 흐름의 원칙을 지킬 수 있다.


![lifting-state-up](\assets/images/lifting-state-up/lifting-state-up.jpeg)


- index.tsx

```javascript
import { useState } from "react";
import Child1 from "../../src/components/units/board/14-lifting-state-up/Child1";
import Child2 from "../../src/components/units/board/14-lifting-state-up/Child2";

export default function LiftingStateUpPage() {
  const [count, setCount] = useState(0);

  // 방법 - 1
  const onClickCountUp = () => {
    setCount((prev) => prev + 1);
  };

  return (
    <div>
      <Child1 count={count} setCount={setCount} />
      <div>=====================================</div>
      <Child2 count={count} onClickCountUp={onClickCountUp} />
    </div>
  );
}
```

- Child1.tsx

```javascript
import { useState } from "react";

export default function Child1(props) {
  // 방법 - 2
  const aaa = () => {
    props.setCount((prev) => prev + 1);
  };

  return (
    <div>
      <div>자식1의 카운트: {props.count}</div>
      <button onClick={aaa}>카운트 올리기!!!</button>
    </div>
  );
}
```

- Child2.tsx

```javascript
export default function Child2(props) {
  return (
    <div>
      <div>자식2의 카운트: {props.count}</div>
      <button onClick={props.onClickCountUp}>카운트 올리기!!!</button>
    </div>
  );
}
```

위의 설명처럼 state 끌어올리기를 사용한 '방법 - 1'도 있고, '방법 - 2'처럼 props로 받은 함수를 새로운 변수에 담아서 사용할 수도 있다.