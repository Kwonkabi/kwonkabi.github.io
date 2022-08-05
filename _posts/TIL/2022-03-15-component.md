---
title: "React의 핵심, Component"
excerpt: "컴포넌트에 대해 알아보자!"

categories: react
tags: [component, react, class component, functional component]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 컴포넌트

**컴포넌트**란, **ui** 또는 **기능**을 **부품화**하여 **재사용**을 가능하게 한 것이다. 예를 들어, 바닐라자바스크립트나 제이쿼리가 도화지를 사서 하나하나 그리는 것이라면, 리액트, 뷰, 앵귤러는 도화지에 그림 부품을 사서 조립해 넣는 것이라고 생각하면 된다. 리액트나 뷰, 앵귤러를 사용해서 개발하는 방식을 따라서 '컴포넌트 기반의 개발방식'이라 한다.

(그중에서도 리액트가 웹, 모바일, PC앱 상관없이 활용도가 높고, 커뮤니티가 활성화되어 있기 때문에 가장 인기가 많다.)

<br>

---

<br>

### 2. 컴포넌트 import와 복사&붙여넣기의 차이

컴포넌트는 코드를 복사, 붙여넣기하는 것과는 엄연한 차이가 있다. 복붙한 코드는 수정사항을 하나하나 찾아서 적용해야 하는 반면, 컴포넌트는 원본 컴포넌트 하나를 만들어 원본과 연결된 컴포넌트들을 뿌리는 개념이기 때문에, 원본만 변경하면 모두 적용된다. (처음 class와 id를 통해 css를 제어할 때 느끼는 기쁨과 비슷한 감정을 느낄 수 있었다.) 형식 뿐만 아니라 데이터도 컴포넌트에 맞게 변경하여 사용 가능하다.

<br>

---

<br>

### 3. 컴포넌트의 종류

컴포넌트 작성 방법은 두 가지가 있다. 첫 번째가 클래스형 컴포넌트이고, 두 번째가 함수형 컴포넌트이다. 클래스형 컴포넌트는 사용 빈도가 줄어드는 추세이고, 대세는 함수형 컴포넌트이다. 클래스형이 더 어렵고 복잡하기 때문에 많은 기업들이 함수형 컴포넌트를 주로 사용한다고 한다. (기존의 서비스들은 클래스형으로 되어 있는 경우도 많아서 둘 다 알아두는 것이 좋다.)

<br>

#### 3.1. 클래스형 컴포넌트 vs. 함수형 컴포넌트

클래스형 컴포넌트는 함수형 컴포넌트보다 메모리 자원을 더 많이 사용한다. 무엇보다도, 아래를 보면 클래스형보다 함수형이 확실히 단순해 보인다.

##### 클래스형 컴포넌트

```javascript
import { Component } from "react";

class New extends Component {
  render() {
    return <div>이것은 클래스형 컴포넌트입니다.</div>;
  }
}
export default New;
```

##### 함수형 컴포넌트

```javascript
function New() {
  return <div>이것은 함수형 컴포넌트입니다.</div>;
}
export default New;
```

함수형 컴포넌트는 함수선언식, 표현식, 화살표함수 모두 사용 가능하다.

<br>

#### 3.2. 클래스형 컴포넌트 실습

```js
// 카운터 컴포넌트 실습!!!

import { Component } from "react";

interface IState {
  count: number;
}

// 객체지향 프로그래밍에서 사용하는 단순한 클래스를 '컴포넌트 기능으로 확장'하여 사용하기: state나 render 같은 것들은 class가 아니라 리액트에 있는 기능이다! -> 컴포넌트 기능을 가진 클래스가 됨
export default class CounterPage extends Component {
  state = {
    // state라는 이름은 못 바꿈!
    count: 99,
  };

  // 화살표 함수로 바꿔서 동적 -> 정적으로 바꿔주는 방법
  // onClickCounter = () => {
  //   console.log(this);
  //   // console.log("카운터 클릭!!!");
  //   console.log(this.state.count);
  //   this.setState((prev: IState) => ({ // state 만들면 컴포넌트 안에 setState 내장되어 있음
  //     count: prev.count + 1,
  //   }));
  // }

  // 화살표 함수로 바꾸기 싫을 때:
  onClickCounter() {
    console.log(this);
    // console.log("카운터 클릭!!!");
    console.log(this.state.count);
    this.setState((prev: IState) => ({
      // state 만들면 컴포넌트 안에 setState 내장되어 있음
      count: prev.count + 1,
    }));
  }

  render() {
    // render라는 함수가 하나 꼭 필요함! state처럼 이름 못 바꿈!
    return (
      <div>
        <div>현재카운트: {this.state.count}</div>{" "}
        {/* 객체 안에 있기 때문에 state.count가 되고, 자동으로 this가 붙는다. */}
        <button onClick={this.onClickCounter}>카운트 올리기!!!</button>
        {/* 화살표 함수로 바꾸기 싫을 때:
        <button onClick={this.onClickCounter.bind(this)}>카운트 올리기!!!</button>  */}
      </div>
    );
  }
}
```

- this가 들어가면 내가 속한 곳 전체를 의미한다. 그러나 실행 주체에 따라 매번 달라진다. 따라서 '동적 스코프'라 부른다.

- 그런데 화살표 함수로 바꾸면 this가 누구를 가리키고 있는지 정적으로 변한다. 이때의 this를 lexical this라 부른다.

- onClickCounter(){}를 onClickCounter = () => {}로 바꿔주기: 기본적으로 최신 자바스크립트는 use strict 모드로 작동한다. use strict 모드에서는 this가 실행되는 환경이 변경될 때, window를 나타내지 않고 undefined를 가리키게 된다. 우리가 마우스로 클릭했을 때, 키보드를 쳤을 때 등등 this를 실행하는 녀석은 window(기본)이다. this가 실행되는 환경이 다르기 때문에 클래스에서는 화살표함수를 사용하거나 bind 명령으로 this를 통일시켜 주어야 한다.
