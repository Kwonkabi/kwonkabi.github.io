---
title: "useCallback"
excerpt: "메모이제이션을 통해 컴포넌트 최적화를 해보자!(2)"

categories: react
tags: [react, react-hooks, memoization, useCallback(), functional-components]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. useCallback()

useCallback 또한 useMemo와 마찬가지로 **메모이제이션을 통해 컴포넌트의 성능을 최적화**시켜주는 도구이다.

> 메모이제이션은 어떤 자주 사용되는 값을 받아오기 위해 반복적으로 계산해야 한다면, 이미 계산해둔 값을 캐싱해두고 해당 값이 또 필요할 때마다 반복적인 계산 없이 메모리에서 꺼내서 재사용하는 최적화 기법을 말한다.

useCallback도 useMemo와 거의 같다.

> useMemo가 첫 번째 인자인 콜백함수가 리턴하는 값을 메모아이즈 해두었다면, useCallback은 첫 번째 인자인 콜백함수 자체를 메모아이즈 해둔다.

```js
const calculate = useCallback(
  (num) => {
    return num + 1;
  },
  [item]
);
```

<br>

자바스크립트의 함수는 사실 객체의 한 종류라는 걸 기억해둬야 한다. calculate라는 변수에 함수 객체가 할당되는 것이다.

```js
const calculate = (num) => {
  return num + 1;
};
```

<br>

만약 다음과 같은 컴포넌트가 있다면,

```js
function Component() {
  const calculate = (num) => {
    return num + 1;
  };

  return <div>{value}</div>;
}
```

이 컴포넌트는 calculate라는 함수를 가지고 있고, calculate라는 함수는 사실 calculate라는 변수에 함수객체가 할당되는 것이다.

컴포넌트가 할당될 때마다 calculate 변수는 다시 초기화되기 때문에, 리렌더되면 새로 만들어진 함수 객체를 다시 할당받게 되는 것이다. (useMemo 포스팅 참고!)

<br>

만약 useCallback을 사용하면, 컴포넌트가 다시 렌더링되더라도 calculate가 초기화되는 걸 막을 수 있다.

```js
function Component() {
  const calculate = useCallback(
    (num) => {
      return num + 1;
    },
    [item]
  );
  return <div>{value}</div>;
}
```

즉, 컴포넌트가 맨 처음 렌더링될 때만 함수 객체를 만들어서 calculate라는 변수에 할당하고, 이후에 렌더링이 될 때는 calculate 변수가 새로운 함수객체를 할당 받는 게 아니라, 이전에 이미 할당 받은 함수 객체를 계속 갖고 있으면서 재사용할 수 있게 되는 것이다.

<br>

---

<br>

### 2. useCallback의 구조

> useCallback의 첫 번째 인자로는 메모이제이션 해줄 **콜백함수**, 두 번째 인자로는 의존성 배열을 받는다. 함수를 useCallback으로 감싸주면, calculate라는 변수는 메모이제이션 된 함수를 갖고 있게 된다. 메모이제이션 된 calculate 함수는 의존성 배열 내부의 값이 변경되지 않는 이상 초기화되지 않는다.

<br>

---

<br>

### 3. 예제 1

```js
import { useState, useEffect } from "react";

const App = () => {
  const [number, setNumber] = useState(0);

  const someFunction = () => {
    console.log(`someFunc: number: ${number}`);
    return;
  };

  useEffect(() => {
    console.log("someFunction이 변경되었습니다.");
  }, [someFunction]);

  return (
    <div>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(e.target.value)}
      />
      <br />
      <button onClick={someFunction}>someFunc 호출</button>
    </div>
  );
};

export default App;
```

useEffect의 의존성 배열에 someFunction이라는 함수(객체)가 들어 있다. 숫자를 올리거나 내려서 number 스테이트가 변경될 때마다 App 컴포넌트가 다시 렌더된다.

함수형 컴포넌트가 렌더된다는 것은 함수가 다시 호출되는 것이기 때문에 함수 내부의 변수들은 모두 초기화된다. 따라서 someFunction 변수에도 새롭게 생성된 함수 객체가 할당되기 때문에(원시값이 아니기 때문에 새로운 메모리공간 주소를 참조함) useEffect는 변화(메모리 공간 주소의 변화)를 감지하고 실행되게 된다.

input창의 숫자만 변경해도 콘솔에는 "someFunction이 변경되었습니다."라는 메시지가 찍히게 된다.

<br>

useCallback을 사용해서 컴포넌트 리렌더 시에도 someFunction이 바뀌지 않도록 만들어보자.

```js
import { useCallback, useEffect, useState } from "react";

const App = () => {
  const [number, setNumber] = useState(0);

  const someFunction = useCallback(() => {
    console.log(`someFunc: number: ${number}`);
    return;
  }, []);

  useEffect(() => {
    console.log("someFunction이 변경되었습니다.");
  }, [someFunction]);

  return (
    <div>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(e.target.value)}
      />
      <br />
      <button onClick={someFunction}>someFunc 호출</button>
    </div>
  );
};

export default App;
```

useCallback을 사용해서 someFunction에 할당되었던 함수객체를 첫 번째 인자인 콜백함수로 넣어주고, 빈 배열을 두 번째 인자로 넣어주면 number가 바뀌어도 useEffect가 호출되지 않는다. someFunction이 이전 값을 기억하고 있고 새로운 함수 객체를 생성해서 할당하지 않기 때문이다.

그런데 input창의 숫자를 변경하고 someFunc 호출이라는 버튼을 눌러도 콘솔에는 계속 number가 초기값 0이 찍힌다. 그 이유는 useCallback의 의존성 배열이 빈 배열이기 때문에 첫 렌더시에 저장된 값을 계속 기억하고 그 이후에는 갱신하지 않기 때문이다.

<br>

의존성 배열에 number를 넣어주면, number가 변경될 때마다 useCallback의 콜백함수의 리턴값을 갱신하게 된다.

```js
import { useCallback, useEffect, useState } from "react";

const App = () => {
  const [number, setNumber] = useState(0);

  const someFunction = useCallback(() => {
    console.log(`someFunc: number: ${number}`);
    return;
  }, [number]);

  useEffect(() => {
    console.log("someFunction이 변경되었습니다.");
  }, [someFunction]);

  return (
    <div>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(e.target.value)}
      />
      <br />
      <button onClick={someFunction}>someFunc 호출</button>
    </div>
  );
};

export default App;
```

숫자를 올릴 때마다 useCallback의 의존성 배열 내의 number 스테이트 변화를 감지해 someFunction에도 새로운 함수객체가 생성되어 할당되기 때문에 콘솔에는 갱신된 number가 찍혀 나오게 되고, useEffect의 의존성 배열에 들어 있는 someFunction의 변화를 감지하여 useEffect가 실행된다.

사실 여기까지는 맨 처음 상황과 똑같고.. 나는 괜히 코드만 길게 짠 사람이 된다.

<br>

다른 스테이트가 있어야 useCallback의 역할을 더 잘 알 수 있을 것 같다.

```js
import { useCallback, useEffect, useState } from "react";

const App = () => {
  const [number, setNumber] = useState(0);
  const [toggle, setToggle] = useState(true);

  const someFunction = useCallback(() => {
    console.log(`someFunc: number: ${number}`);
    return;
  }, [number]);

  useEffect(() => {
    console.log("someFunction이 변경되었습니다.");
  }, [someFunction]);

  return (
    <div>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(e.target.value)}
      />
      <button onClick={() => setToggle(!toggle)}>{toggle.toString()}</button>
      <br />
      <button onClick={someFunction}>someFunc 호출</button>
    </div>
  );
};

export default App;
```

toggle 스테이트를 아무리 변경해줘도 (number에만 반응하기 때문에) someFunction은 갱신되지 않고, someFunction에 변화가 없으면 useEffect도 실행되지 않기 때문에 콘솔은 조용하다.

<br>

---

<br>

### 4. 예제 2

이번엔 input창에 사이즈를 조절해서 화면의 박스 크기를 조절하는 컴포넌트를 만들어보자.

**Box 컴포넌트의 부모 컴포넌트인 App 컴포넌트**

```js
import { useState } from "react";
import Box from "./Box";

const App = () => {
  const [size, setSize] = useState(100);

  const createBoxStyle = () => {
    return {
      backgroundColor: "green",
      width: `${size}px`,
      height: `${size}px`,
    };
  };

  return (
    <div>
      <input
        type="number"
        value={size}
        onChange={(e) => setSize(e.target.value)}
      />
      <Box createBoxStyle={createBoxStyle} />
    </div>
  );
};

export default App;
```

<br>

**App 컴포넌트의 자식 컴포넌트인 Box 컴포넌트**

```js
import { useEffect, useState } from "react";

const Box = ({ createBoxStyle }) => {
  const [style, setStyle] = useState({});

  useEffect(() => {
    console.log("박스 키우기");
    setStyle(createBoxStyle);
  }, [createBoxStyle]);

  return <div style={style}></div>;
};

export default Box;
```

Box 컴포넌트에서 부모 컴포넌트로부터 프롭스를 잘 받아와서 작동되는 것을 볼 수 있다.

<br>

App 컴포넌트에 스테이트를 하나 더 만들어보자.

```js
import { useState } from "react";
import Box from "./Box";

const App = () => {
  const [size, setSize] = useState(100);
  const [isDark, setIsDark] = useState(false);

  const createBoxStyle = () => {
    return {
      backgroundColor: "green",
      width: `${size}px`,
      height: `${size}px`,
    };
  };

  return (
    <div
      style={{
        backgroundColor: isDark ? "black" : "white",
      }}
    >
      <input
        type="number"
        value={size}
        onChange={(e) => setSize(e.target.value)}
      />
      <button onClick={() => setIsDark(!isDark)}>
        {isDark ? "Light Mode" : "Dark Mode"}
      </button>
      <Box createBoxStyle={createBoxStyle} />
    </div>
  );
};

export default App;
```

버튼을 클릭해서 isDark 스테이트가 변경되면 분명 사이즈는 건드리지 않았지만 콘솔에는 '박스 크기 조절하기'가 찍힌다.

그 이유는, isDark 스테이트가 변경되면, App 컴포넌트가 새로 그려지게 되고, 그러면 createBoxStyle 변수도 초기화되어 새로운 함수 객체가 생성되어 할당되기 때문이다. Box 컴포넌트에서 props로 createBoxStyle을 받아왔는데 변화가 생겼고, useEffect 의존성 배열에 createBoxStyle이 들어 있으니 useEffect가 실행되어 콘솔에 '박스 크기 조절하기'가 찍히게 되는 것이다.

<br>

useCallback을 사용해서 createBoxStyle 함수가 size가 변경될 때에만 갱신되고, 그렇지 않으면 이전의 값을 기억하도록 만들어주면 아무리 버튼을 눌러도 Box 컴포넌트의 useEffect가 실행되지 않는다.

```js
import { useCallback, useState } from "react";
import Box from "./Box";

const App = () => {
  const [size, setSize] = useState(100);
  const [isDark, setIsDark] = useState(false);

  const createBoxStyle = useCallback(() => {
    return {
      backgroundColor: "green",
      width: `${size}px`,
      height: `${size}px`,
    };
  }, [size]);

  return (
    <div
      style={{
        backgroundColor: isDark ? "black" : "white",
      }}
    >
      <input
        type="number"
        value={size}
        onChange={(e) => setSize(e.target.value)}
      />
      <button onClick={() => setIsDark(!isDark)}>
        {isDark ? "Light Mode" : "Dark Mode"}
      </button>
      <Box createBoxStyle={createBoxStyle} />
    </div>
  );
};

export default App;
```
