---
title: "React의 핵심, Component"
excerpt: "컴포넌트에 대해 이해하고, state에 대해 배워보자!"

categories: coding
tags: [Component, 컴포넌트, React]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 컴포넌트

**컴포넌트**란, **ui** 또는 **기능**을 **부품화**하여 **재사용**을 가능하게 한 것이다. 예를 들어, 바닐라자바스크립트나 제이쿼리가 도화지를 사서 하나하나 그리는 것이라면, 리액트, 뷰, 앵귤러는 도화지에 그림 부품을 사서 조립해 넣는 것이라고 생각하면 된다. 리액트나 뷰, 앵귤러를 사용해서 개발하는 방식을 따라서 '컴포넌트 기반의 개발방식'이라 한다.

(그중에서도 리액트가 웹, 모바일, PC앱 상관없이 활용도가 높고, 커뮤니티가 활성화되어 있기 때문에 가장 인기가 많다.)

### 2. 컴포넌트 import와 복사&붙여넣기의 차이

컴포넌트는 코드를 복사, 붙여넣기하는 것과는 엄연한 차이가 있다. 복붙한 코드는 수정사항을 하나하나 찾아서 적용해야 하는 반면, 컴포넌트는 원본 컴포넌트 하나를 만들어 원본과 연결된 컴포넌트들을 뿌리는 개념이기 때문에, 원본만 변경하면 모두 적용된다. (처음 class와 id를 통해 css를 제어할 때 느끼는 기쁨과 비슷한 감정을 느낄 수 있었다.) 형식 뿐만 아니라 데이터도 컴포넌트에 맞게 변경하여 사용 가능하다.

### 3. 컴포넌트의 종류

컴포넌트 작성 방법은 두 가지가 있다. 첫 번째가 클래스형 컴포넌트이고, 두 번째가 함수형 컴포넌트이다. 클래스형 컴포넌트는 사용 빈도가 줄어드는 추세이고, 대세는 함수형 컴포넌트이다. 클래스형이 더 어렵고 복잡하기 때문에 많은 기업들이 함수형 컴포넌트를 주로 사용한다고 한다. (기존의 서비스들은 클래스형으로 되어 있는 경우도 많아서 둘 다 알아두는 것이 좋다.)

#### 3.1. 클래스형 컴포넌트 vs. 함수형 컴포넌트

클래스형 컴포넌트는 함수형 컴포넌트보다 메모리 자원을 더 많이 사용한다. 무엇보다도, 아래를 보면 클래스형보다 함수형이 확실히 단순해 보인다.

##### (클래스형 컴포넌트)

```javascript
import { Component } from "react";

class New extends Component {
  render() {
    return <div>이것은 클래스형 컴포넌트입니다.</div>;
  }
}
export default New;
```

##### (함수형 컴포넌트)

```javascript
function New() {
  return <div>이것은 함수형 컴포넌트입니다.</div>;
}
export default New;
```

함수형 컴포넌트는 함수선언식, 표현식, 화살표함수 모두 사용 가능하다.

### 4. React-hooks

클래스형 컴포넌트에서 함수형 컴포넌트로 넘어오는 추세는 React-hooks 덕분에 가능했다. 리액트는 함수형 컴포넌트가 클래스형 컴포넌트와 동일한 기능을 사용할 수 있도록 이 Hooks란 도구를 만들었다. (예를 들면, 함수형 컴포넌트는 state나 lifeCycle 관련 기능을 사용할 수 없었는데 Hooks를 통해 해결할 수 있었다.)

#### 4.1. React-hooks의 종류

React-Hooks의 종류에는 useEffect, useState, useMemo, useCallback, useRef, useContext 등이 있다. 그 중 대표적인 것이 useEffect와 useState이다. (이 포스팅에서는 다양한 hooks에 대해선 다루지 않을 것이고, hooks 덕분에 함수형 컴포넌트를 이용할 수 있어 편해졌다는 정도만 이해하고 넘어간다.)

#### 4.2. State

React-Hooks에 대해 알아보기 전에 알아야 할 개념이 하나 있다. 바로 'State'이다. **state는 컴포넌트에서 사용하는 변수**이다. (자바스크립트에서 데이터를 담기 위해 변수를 사용하는 것과 같다.) 그리고, **setState**는 컴포넌트에서 사용하는 **변수를 바꿔주는 기능**이며, **useState**는 컴포넌트에서 사용하는 **변수를 만들어주는 기능**이다.
\*state, setState, useState는 1)회원가입, 게시물 작성 내용 등을 서버 컴퓨터에 전송하기 위해 변수에 담아둘 때, 2)작성한 내용을 검증하고, 잘못된 부분을 빨간색으로 표기할 때 사용한다!(4.2.2.과 4.2.3.에서 실습할 것이다.)

##### 4.2.1. let? const? state!

- 리액트 컴포넌트에서 변수를 만들고 바꿔보자!

```javascript
// 자바스크립트에서 변수 만들기
변수만드는기능 변수명 = 담을내용           ==>    let classmate = "철수"


// 리액트 컴포넌트에서 변수 만들기
const [변수명] = 변수만드는기능(담을내용)  ==> const [classmate] = useState("철수")
```

```javascript
// 자바스크립트에서 변수 바꾸기
let classmate = "철수";
classmate = "영희"; // classmate 가 영희로 바뀝니다.

// 리액트 컴포넌트에서 변수 바꾸기
const [변수명, 변수바꾸는기능] = 변수만드는기능(담을내용);
const [classmate, setClassmate] = useState("철수");
setClassmate("영희"); // classmate 가 영희로 바뀝니다.
```

이것만 보면, 왜 굳이 간단한 let을 두고 state를 쓰나 싶다. 그러나 다음을 보면 state를 써야 하는 이유를 알 수 있다. 만약 우리가 좋아요 버튼을 누를 때마다 숫자가 올라간다고 해보자. **만약, 변수 let이나 상수 const를 사용하여 좋아요를 그렸다면 데이터는 변경되지만 화면에는 반영되지 않을 것이다.** (자바스크립트에서는 무려 document.getElementById("h1").innerText를 사용했을 것이다...)

```javascript
// let으로 count 예제 실험해보기
function New() {
  let count = 0; // let으로 자바스크립트 변수 만들기

  function handleClick() {
    count = count + 1; // 개수는 증가했지만, 화면에는 반영이 안됨
  }

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={handleClick}>let을 사용하여 count 증가</button>
    </div>
  );
}

export default New;
```

하지만, **컴포넌트 변수인 state를 사용해서 화면에 그리고 setState()를 사용해서 좋아요를 변경한다면, setState() 안에서 화면을 새롭게 그리라는 명령이 실행되고, 변경된 데이터가 화면에 그려진다.**

```javascript
// state로 count 예제 실험해보기
import { useState } from "react";

function New() {
  const [count, setCount] = useState(0); // state로 컴포넌트 변수 만들기

  function handleClick() {
    setCount(count + 1); // 갯수가 증가하면서, 화면에 정상적으로 반영됨
  }

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={handleClick}>state를 사용하여 count 증가</button>
    </div>
  );
}

export default New;
```

##### 4.2.2. useState 실습 1: 서버컴퓨터에 전송하기 위해 state에 담아두기

```javascript
import { useState } from "react";

function Login() {
  const [id, setId] = useState("");
  const [pw, setPw] = useState("");

  function handleChangeId(event) {
    const value = event.target.value;
    setId(value);
  }

  function handleChangePw(event) {
    const value = event.target.value;
    setPw(value);
  }

  return (
    <div>
      <h1>로그인</h1>
      <div>아이디 {id}</div>
      <input type="text" onChange={handleChangeId} />
      <div>비밀번호 {pw}</div>
      <input type="text" onChange={handleChangePw} />
    </div>
  );
}

export default Login;
```

##### 4.2.3. useState 실습 2: 작성한 내용을 검증하고 잘못된 부분을 빨간색으로 표기하기

```javascript
import { useState } from "react";

function Login() {
  const [id, setId] = useState("");
  const [pw, setPw] = useState("");
  const [errorId, setErrorId] = useState("");
  const [errorPw, setErrorPw] = useState("");

  function handleChangeId(event) {
    const value = event.target.value;
    setId(value);
  }

  function handleChangePw(event) {
    const value = event.target.value;
    setPw(value);
  }

  function handleClickLogin() {
    if (id === "") {
      setErrorId("! 아이디를 정확히 입력해 주세요.");
    }
    if (pw === "") {
      setErrorPw("! 비밀번호를 정확히 입력해 주세요.");
    }
    if (id !== "" && pw !== "") {
      alert("아이디와 비밀번호가 모두 입력되었습니다. 로그인을 시작합니다.");
    }
  }

  return (
    <div>
      <h1>로그인</h1>
      <div>아이디 {id}</div>
      <input type="text" onChange={handleChangeId} />
      <div style={{ color: "red" }}>{errorId}</div>
      <div>비밀번호 {pw}</div>
      <input type="text" onChange={handleChangePw} />
      <div style={{ color: "red" }}>{errorPw}</div>
      <div>
        <button onClick={handleClickLogin}>로그인</button>
      </div>
    </div>
  );
}

export default Login;
```
