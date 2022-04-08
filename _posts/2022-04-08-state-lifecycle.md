---
title: "클래스형/함수형 컴포넌트 생명주기(lifecycle)"
excerpt: "생명주기 메서드와 useEffect에 대해 알아보잣~!"

categories: coding
tags: [react, typescript, component, lifecycle, componentDidMount, componentDidUpdate, componentWillUnmount, useEffect, dependency array]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 컴포넌트 생명주기(lifecycle)

예전 포스팅 [**React의 핵심, Component**](https://kwonkabi.github.io/coding/component/)에서, 클래스형 컴포넌트와 함수형 컴포넌트의 차이를 간단히 알아보았다. 그때 사실 "함수형 컴포넌트는 state나 **lifecycle** 관련 기능을 사용할 수 없었는데 Hooks를 통해 해결할 수 있었다"고 쓰면서도 그게 뭔지 잘 몰랐다. 단지, react-hooks가 생기면서 간편해졌다는 정도?! 오늘은 그래서 그 lifecycle, 즉 생명주기에 대해 써보려고 한다.

컴포넌트의 생명주기란, 컴포넌트가 브라우저에 나타나고, 업데이트되고, 사라지게 될 때 호출되는 메서드이다. (메서드란, 클래스에서 만들어진 함수를 말한다.) 즉, 특정한 시점에 코드가 실행되도록 설정할 수 있다는 말이다. react-hooks가 나타나기 이전에는 클래스형 컴포넌트에서만 이러한 기능을 사용할 수 있었다고 하니, 클래스형 먼저 살펴보자.


#### 1.1. 클래스형 컴포넌트 생명주기

 그리기 → **render**
 ① 그리고 난 뒤 → **componentDidMount**
 ② 그리고 난 뒤 변경 → **componentDidUpdate**
 ③ 그리고 난 뒤 사라짐 → **componentWillUnmount**


```typescript
import { Component, createRef } from "react";
import Router from "next/router";

interface IState {
  count: number;
}
export default class CounterPage extends Component {
  inputRef = createRef<HTMLInputElement>();
  state = {
    count: 99,
  };

  componentDidMount() { //  ① 그리고 난 뒤
    console.log("마운트됨!!!");
    this.inputRef.current?.focus();
    // 포커스 깜빡깜빡
  }

  componentDidUpdate() { // ② 그리고 난 뒤 변경
    console.log("수정되고 다시 그려짐!!!");
  }

  componentWillUnmount() { // ③ 그리고 난 뒤 사라짐
    console.log("컴포넌트 사라짐!!!");
    // 채팅방 나가기
    // api 요청!!!
  }

  onClickCounter = () => {
    console.log(this);
    // console.log("카운터 클릭!!!");
    console.log(this.state.count);
    this.setState((prev: IState) => ({
      count: prev.count + 1,
    }));
  };

  onClickMove() {
    Router.push("/");
  }

  render() { // 그리기
    return (
      <div>
        {/* ref는 createRef와 짝꿍 */}
        <input type="text" ref={this.inputRef} />
        <div>현재카운트: {this.state.count}</div>
        <button onClick={this.onClickCounter}>카운트 올리기!!!</button>
        <button onClick={this.onClickMove}>나가기!!!</button>
      </div>
    );
  }
}
```

위와 같은 코드에서, **render**를 통해 html부분이 화면에 그려지고, 페이지가 그려진 후 ① **componentDidMount** 메서드를 통해 인풋창의 커서가 깜빡이는 것과 콘솔창에서 "마운트됨!!!"을 볼 수 있을 것이다. 만약 '카운트 올리기' 버튼을 눌렀다면, ② **componentDidUpdate** 메서드를 통해 콘솔창에 "수정되고 다시 그려짐!!!"이 뜨는 것을 볼 수 있을 것이다. 그리고 '나가기!!!' 버튼을 클릭한다면 ③ **componentWillUnmount** 메서드를 통해 페이지가 이동되고, 콘솔창에서 "컴포넌트 사라짐!!!"을 발견할 수 있게 된다.


#### 1.2. 함수형 컴포넌트 생명주기

클래스형 컴포넌트에는 componentDidMount 같은 생명주기 메서드들이 있다. 함수형 컴포넌트에서의 생명주기 관련 훅은 바로 **useEffect**이다. 위에서 만든 카운터를 함수형 컴포넌트로 작성하면 다음과 같다.

```typescript
import { useEffect, useRef, useState } from "react";
import { useRouter } from "next/router";

export default function CounterPage() {
  const router = useRouter();
  const inputRef = useRef<HTMLInputElement>(null);
  const [count, setCount] = useState(99);

  // ① componentDidMount
  useEffect(() => {
    console.log("마운트됨!!!");
    inputRef.current?.focus();

  // ② componentDidUpdate
  useEffect(() => {
    console.log("수정되고 다시그려짐!!!");
  }, [count]);

    // ③ componentWillUnmount
    return () => {
      console.log("컴포넌트 사라짐!!!");
    };
  }, []);

  const onClickCounter = () => {
    setCount((prev) => prev + 1);
  };

  const onClickMove = () => {
    router.push("/");
  };

  console.log("나는 언제 실행되게?!!");

  return (
    <div>
      <input type="text" ref={inputRef} />
      <div>현재카운트: {count}</div>
      <button onClick={onClickCounter}>카운트 올리기!!!</button>
      <button onClick={onClickMove}>나가기!!!</button>
    </div>
  );
}
```

##### 1.2.1. useEffect와 의존성 배열(Dependency Array)
클래스형 컴포넌트의 생명주기 메서드와는 달리 하나만 있는데 어떻게 특정 시점을 알 수 있냐면, 바로 코드에 나타난 대괄호들, **의존성 배열** 덕분에 가능하다. 의존성 배열은 이 함수가 실행될 지 안 될 지를 결정한다. 클래스형 컴포넌트에서는 메서드에 따라 다르게 실행되었다면, 함수형 컴포넌트에서는 일단 마운트 되자마자 한 번은 실행된다.
- 만약 대괄호 안이 비어 있다면, 처음에 한 번만 실행되고 끝!!
- 그런데 대괄호 자체가 없다면, 컴포넌트 자체에서 뭐 하나라도 바뀌면 실행!!
- 대괄호 안에 예를 들어 count가 들어 있다면, count가 바뀔 때마다 실행된다.

※ 클래스형 컴포넌트의 생명주기 메서드와 함수형 컴포넌트의 훅인 useEffect 모두 **렌더 이후에 실행**된다!! 따라서, 저기 뜬금 없는 **console.log("나는 언제 실행되게?!!");**는 제일 먼저 실행된다. 유일하게 렌더 전에 실행되는 뇨속이기 때문.


##### 1.2.2. useEffect 사용 시 주의사항!
useEffect 내에서 setState를 사용하는 것은 지양하는 게 가장 좋다. 컴포넌트가 마운트된 이후에 setState를 적용하게 되면 1. state가 변경되고, 2. 변경된 state로 컴포넌트가 다시 그려지게 된다(리렌더). 다시 말해, useEffect 내에서 setState를 사용하게 되면 불필요한 **추가 렌더링**이나 **무한루프**를 일으키게 되어 성능면에서 비효율적이다.

```javascript
  useEffect(() => {
    setCount((prev) => prev + 1);
  }, [count]);
```
↑ 이렇게 사용하면 안 된다..