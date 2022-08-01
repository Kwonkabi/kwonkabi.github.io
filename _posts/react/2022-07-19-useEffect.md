---
title: "useEffect"
excerpt: "덕분에 클래스 컴포넌트에서 함수형 컴포넌트로 간다~"

categories: react
tags:
  [
    react,
    react-hooks,
    useEffect(),
    dependency-array,
    clean-up,
    functional-components,
  ]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. useEffect()

> 어떤 컴포넌트가 마운트 되었을 때(첫 렌더링), 업데이트 되었을 때(다시 렌더링), 언마운트 되었을 때(화면에서 사라질 때) 특정 작업을 처리할 코드를 실행시켜주고 싶을 때 사용하는 리액트 훅이다.

<br>

---

<br>

### 2. useEffect() 사용 방법

useEffect 함수는 인자로 콜백함수를 받는다. (콜백 함수란, 다른 함수의 인자로 전달된 함수를 말한다.) 그리고 콜백함수 내부에 우리가 원하는 작업을 처리해줄 코드를 넣어준다. 다음과 같이 생겼다.

(useEffect() 내부의 콜백은 화면이 렌더링된 직후에 호출된다.)

```js
useEffect(() => {
  // 원하는 작업
});
```

위와 같이 써주는 방식이 있고, useEffect() 함수의 인자로 콜백함수와 배열을 넣어주는 방식도 있다.

```js
useEffect(() => {
  // 원하는 작업
}, [value]);
```

두 번째 인자로 들어가는 배열을 의존성 배열, dependency array라고 부른다.

<br>

**1) 렌더링 될 때마다 실행**

```js
useEffect(() => {
  // 원하는 작업
});
```

<br>

**2-1) 첫 렌더링 시에만 실행**

```js
useEffect(() => {
  // 원하는 작업
}, []);
```

<br>

**2-2) 배열 안의 값이 바뀔 때마다 실행**

```js
useEffect(() => {
  // 원하는 작업
}, [value]);
```

<br>

만약 우리가 useEffect()에서 어떤 함수를 넣었다면, 이후에 그 함수를 멈추는 작업을 하고 싶을 수 있다. 이걸 clean-up이라고 한다. 이러한 정리 작업을 해주려면, useEffect() 함수의 리턴 값으로 함수를 넣어주면 된다.

```js
useEffect(() => {
  // 구독 ... (원하는 작업)
  return () => {
    // 구독 해지 ... (정리 작업)
  };
}, []);
```

이렇게 함수를 리턴해주면, 해당 컴포넌트가 언마운트 될 때 혹은 다음 렌더링 시 불릴 useEffect가 실행되기 이전에 리턴되는 함수가 실행된다.

<br>

---

<br>

### 3. 예제 1

```js
import { useState } from "react";

function App() {
  const [count, setCount] = useState(1);

  const handleCountUpdate = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <button onClick={handleCountUpdate}>Update</button>
      <div>count: {count}</div>
    </div>
  );
}

export default App;
```

업데이트 버튼이 눌리면 handleCountUpdate 함수가 실행되어, count state의 값이 1씩 증가한다.

useEffect를 import 해준 후 사용해보자.

```js
import { useState, useEffect } from "react";

function App() {
  const [count, setCount] = useState(1);
  const [name, setName] = useState("");

  const handleCountUpdate = () => {
    setCount(count + 1);
    setName("");
  };

  const handleInputChange = (e) => {
    setName(e.target.value);
  };

  // 렌더링 될 때마다 매번 실행됨
  useEffect(() => {
    console.log("렌더링!");
  });

  return (
    <div>
      <button onClick={handleCountUpdate}>Update</button>
      <div>count: {count}</div>
      <input type="text" value={name} onChange={handleInputChange} />
      <div>이름: {name}</div>
    </div>
  );
}

export default App;
```

input 창에 새로운 글자가 입력될 때마다 handleInputChange 함수가 실행되고, name state 값이 변경된다. 그때마다 새롭게 렌더되고, 콘솔 창에는 `렌더링!`이 그때마다 출력된다!

```js
import { useState, useEffect } from "react";

function App() {
  const [count, setCount] = useState(1);
  const [name, setName] = useState("");

  const handleCountUpdate = () => {
    setCount(count + 1);
    setName("");
  };

  const handleInputChange = (e) => {
    setName(e.target.value);
  };

  // 첫 렌더링, count state가 변경될 때마다 실행됨
  useEffect(() => {
    console.log("count 변화!");
  }, [count]);

  return (
    <div>
      <button onClick={handleCountUpdate}>Update</button>
      <div>count: {count}</div>
      <input type="text" value={name} onChange={handleInputChange} />
      <div>이름: {name}</div>
    </div>
  );
}

export default App;
```

의존성 배열 내에 count state를 넣어주면, count가 변경될 때에만 렌더된다. 즉, Update 버튼이 눌려 handleCountUpdate 함수가 실행될 때에만 렌더된다. 빈 배열로 둔다면 처음 렌더 될 때에만 실행된다.

<br>

---

<br>

### 4. 예제 2

Toggle Timer 버튼을 누르면 화면에 `타이머를 시작합니다. 콘솔을 보세요.`라는 메시지가 보이고, 콘솔에는 1초마다 타이머가 찍히는 코드를 작성해보자.

**Timer 컴포넌트**

```js
import { useEffect } from "react";

const Timer = () => {
  useEffect(() => {
    const timer = setInterval(() => {
      console.log("타이머 돌아가는 중");
    }, 1000);
  }, []);

  return (
    <div>
      <span>타이머를 시작합니다. 콘솔을 보세요.</span>
    </div>
  );
};

export default Timer;
```

<br>

**App 컴포넌트**

```js
import { useState, useEffect } from "react";
import Timer from "./Timer";

function App() {
  const [showTimer, setShowTimer] = useState(false);

  return (
    <div>
      {showTimer && <Timer />}
      <button onClick={() => setShowTimer(!showTimer)}>Toggle Timer</button>
    </div>
  );
}

export default App;
```

Toggle Timer 버튼을 누르면 showTimer state가 false에서 true로 바뀌면서 Timer 컴포넌트가 마운트 된다.

첫 렌더 시 Timer 컴포넌트의 useEffect() 내부의 setInterval 함수가 실행되어 1초마다 콘솔에 '타이머 돌아가는 중'이 찍히게 된다.

그런데 다시 Toggle Timer 버튼을 눌러줘도 타이머가 멈추지 않는다. 영원히 돌아가는 타이머,,,

> 그 이유는 우리가 setInterval로 만든 함수를 정리(clean-up) 해주지 않아서다!!!

Timer 컴포넌트 내의 useEffect() 함수 내부를 다음과 같이 바꿔주면 된다.

```js
import { useEffect } from "react";

const Timer = () => {
  useEffect(() => {
    const timer = setInterval(() => {
      console.log("타이머 돌아가는 중");
    }, 1000);

    return () => {
      clearInterval(timer);
      console.log("타이머가 종료되었습니다.");
    };
  }, []);

  return (
    <div>
      <span>타이머를 시작합니다. 콘솔을 보세요.</span>
    </div>
  );
};

export default Timer;
```

그럼 Toggle Timer 버튼을 다시 눌러 showTimer state 값이 false로 변경되면 Timer 컴포넌트가 언마운트되고, 그때 Timer 컴포넌트의 useEffect 내부에서 clearInterval 함수가 실행되어 타이머가 종료된다!!
