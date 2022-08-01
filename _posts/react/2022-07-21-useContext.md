---
title: "useContext"
excerpt: "전역에서 필요한 데이터를 props 없이 주고 받는 방법"

categories: react
tags: [react, react-hooks, props, context, useContext(), functional-components]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. context란

리액트로 만든 앱은 여러 개의 컴포넌트들로 이루어져 있다. 최상위 앱 컴포넌트에서 시작해서 트리 형태로 뻗어나간다. **일반적인 데이터 흐름은 위에서 아래로, 부모 컴포넌트에서 자식 컴포넌트로 프롭스(props)를 통해 넘겨주는 단방향**이다. 프롭스를 사용해서 데이터를 전달할 때는 부모 컴포넌트가 자식 컴포넌트 태그에 일일이 프롭스를 넣어서 단계 별로 전달해주어야 한다.

<br>

보통 유저 정보, 테마, 언어 등과 같이 전역적으로 사용하는 데이터가 있다. 매우매우 크고 트리도 깊은 리액트 앱이 있으면, 최상단에서 최하단까지 전역적인 데이터를 프롭스를 통해 매 단계 별로 하나하나 넘겨줘야 한다. 뭔가 바뀌면 중간 컴포넌트를 다 찾아 수정해야 할 수도 있기 때문에 번거로울 것이다.

<br>

리액트는 이러한 문제를 해결해주는 context api를 제공해준다. context는 앱 안에서 전역적으로 사용되는 데이터들을 여러 컴포넌트들끼리 쉽게 공유할 수 있는 방법을 제공해준다.

> context를 사용하면 props로 일일이 데이터를 전달해주지 않아도 해당 데이터를 갖고 있는 상위 컴포넌트가 '이 데이터 필요한 컴포넌트~~?'하고 하위 컴포넌트들에게 방송을 해줄 수 있다.

> 하위 컴포넌트들은 트리 안에서 어디에 위치해 있든 '나 그 데이터 쓸래!'라고만 해주면 해당 값에 접근할 수 있다.

=> 그렇기 때문에 사용자 정보, 테마, 언어와 같은 수많은 컴포넌트들이 필요로 하는 전역적인 데이터를 전달하기에 편리하다!!

<br>

---

<br>

### 2. Props Drilling

최상위 컴포넌트인 앱 컴포넌트가 어떤 데이터를 state로 갖고 있다고 생각해보자. 이 데이터를 최하위 컴포넌트에게 전달하려면 어떻게 해야 할까? <br>

당연히 props를 통해 모든 중간단계(컴포넌트들)를 거쳐서 전달해야 할 것이다. 이러한 과정을 **prop drilling**이라고 한다.

이렇게 prop drilling하게 되면 어떤 문제가 있을까? <br>

최하위 컴포넌트만 그 데이터가 필요함에도 불구하고, 중간에 존재하는 모든 컴포넌트들을 거쳐야 하기 때문에 컴포넌트들이 받는 props도 많아지고, 코드도 더러워진다. 그것보다 더 문제인 것은 그 과정에서 데이터가 훼손될 확률이 있다는 것이다. 그럼 부모 컴포넌트들을 찾아다니면서 에러를 해결해야 된다. 만약 앱의 규모가 크고, 컴포넌트의 개수가 수백개가 넘어간다면 문제는 더 심각해질 것이다.

<br>

props 대신 context를 사용해서 데이터를 공유하면 한 곳에 있는 데이터를 모든 자녀 컴포넌트들에게 '데이터 필요한 사람~~!'하고 방송할 수 있고, 데이터가 필요한 컴포넌트는 useContext를 사용해서 받아오기만 하면 된다~!

> 즉, useContext는 context로 공유한 데이터를 쉽게 받아올 수 있도록 도와주는 역할을 한다.

<br>

그러면, context가 그렇게 좋으면 props를 왜 쓰나?? 그냥 다 context 쓰면 되지!?

그러나 context는 꼭 필요할 때만 사용해야 한다. 1) context를 사용하면 컴포넌트를 재사용하기 어려워질 수 있다. context의 목적은 다양한 레벨의 많은 컴포넌트들에게 전역적인 데이터를 전달하기 위한 것이다. 2) 단순히 props drilling을 피하기 위한 목적이라면 컴포넌트 합성(component composition)을 고려해보자.

<br>

---

<br>

### 3. useContext() 실습

토글 버튼을 눌러 다크모드와 라이트모드를 전환하는 앱을 만들어보자. App 컴포넌트가 최상위 컴포넌트이며, 그 아래에는 Page 컴포넌트가 있고, 그 아래에는 Header, Content, Footer 형제 컴포넌트들이 있다.

<br>

#### 3.1. Props Drilling의 경우

<br>

**App 컴포넌트**

```js
import { useState } from "react";
import Page from "./Page";

const App = () => {
  const [isDark, setIsDark] = useState(false);

  return <Page isDark={isDark} setIsDark={setIsDark} />;
};

export default App;
```

<br>

**Page 컴포넌트**

```js
import Content from "./Content";
import Footer from "./Footer";
import Header from "./Header";

const Page = ({ isDark, setIsDark }) => {
  return (
    <div>
      <Header isDark={isDark} />
      <Content isDark={isDark} />
      <Footer isDark={isDark} setIsDark={setIsDark} />
    </div>
  );
};

export default Page;
```

Page 컴포넌트에서는 isdark 스테이트와 setIsDark 함수를 사용하지 않지만, 자식 컴포넌트인 Header, Content, Footer 컴포넌트에게 전달해주기 위해 프롭스를 받아서 건네주는 역할이다.

<br>

**Page 컴포넌트의 자식 컴포넌트 1: Header 컴포넌트**

```js
const Header = ({ isDark }) => {
  return (
    <header
      style={{
        backgroundColor: isDark ? "black" : "lightgray",
        color: isDark ? "white" : "black",
      }}
    >
      <h1>Welcome 홍길동</h1>
    </header>
  );
};

export default Header;
```

props로 받아온 isDark가 true일 때 배경색은 검정, 글자색은 흰색, false일 때는 배경색은 회색, 글자색은 검정색으로 바뀌게 만들었다.

<br>

**Page 컴포넌트의 자식 컴포넌트2: Content 컴포넌트**

```js
const Content = ({ isDark }) => {
  return (
    <div
      style={{
        backgroundColor: isDark ? "black" : "white",
        color: isDark ? "white" : "black",
      }}
    >
      <p>홍길동님 좋은 하루!</p>
    </div>
  );
};

export default Content;
```

props로 받아온 isDark가 true일 때 배경색은 검정, 글자색은 흰색, false일 때는 배경색은 흰색, 글자색은 검정색으로 바뀌게 만들었다.

<br>

**Page 컴포넌트의 자식 컴포넌트3: Footer 컴포넌트**

```js
const Footer = ({ isDark, setIsDark }) => {
  const onToggleThmeme = () => {
    setIsDark(!isDark);
  };
  return (
    <footer
      style={{
        backgroundColor: isDark ? "black" : "lightgray",
      }}
    >
      <button onClick={onToggleThmeme}>
        {isDark ? `Light Mode` : `Dark Mode`}
      </button>
    </footer>
  );
};

export default Footer;
```

props로 받아온 isDark가 true면 배경색은 검정, 버튼 텍스트는 'Light Mode'로, isDark가 false면 배경색은 회색, 버튼 텍스트는 'Dark Mode'로 만들어줬다. 그리고 버튼을 누르면 onToggleTheme 함수가 실행되면서 props로 받아온 setIsDark가 isDark의 상태를 반대로 바꿔주게 만들었다. <br>

이렇게 하면 잘 작동은 하지만, 위에서 보았듯 Page 컴포넌트가 불필요하게 지저분해진다. 만약 Page 컴포넌트에서 다른 데이터를 다루어야 하는 복잡한 작업이 있었다면, 혹은 Page 컴포넌트와 같은 중간 컴포넌트가 수십개라면 그 과정에서 props가 가진 데이터가 훼손될 위험이 있다. <br>

중간 컴포넌트를 거쳐 props를 사용하지 않고, context를 사용하여 데이터를 최하위 컴포넌트에게 넘겨줘보자. 이때 Page 컴포넌트는 isDark, setIsDark에 대해 전혀 몰라도 된다. <br>

<br>

---

<br>

#### 3.2. context와 useContext()

우선 context를 만들어주자. 리액트 context를 만들기 위해서는 우선 `createContext를` import 해주어야 한다.

```js
import { createContext } from "react";

export const ThemeContext = createContext(null);
```

App 컴포넌트에 가서, 만들어둔 ThemeContext를 import 해주고, 자식 컴포넌트인 Page 컴포넌트를 `ThemeContext.Provider`로 감싸줘야 한다. 하위의 모든 컴포넌트들에게, '이 데이터 필요한 사람~!'하고 방송하는 파트이다. <br>

이 context의 provider는 value라는 prop을 받는다. 그리고 그 value 안에는 우리가 전달하고자 하는 데이터를 집어넣으면 된다. Page 컴포넌트가 갖고 있는 props는 이제 지워줘도 된다. <br>

이때 Provider로 감싸주지 않으면, ThemeContext에서 인자로 넘겨준 초기값이 반환된다. 그치만 우리는 value를 사용해서 값을 넘겨주기 때문에 초기값이 필요하지 않다. 그래서 null로 일단 지정해두었다. (비워두어도 똑같이 작동하는 것을 보니, 초기값이 사용되지 않는 것을 확인할 수 있었다.) <br>

```js
import { useState } from "react";
import Page from "./Page";
import { ThemeContext } from "./context/ThemeContext";

const App = () => {
  const [isDark, setIsDark] = useState(false);

  return;
  <ThemeContext.Provider value={{ isDark, setIsDark }}>
    <Page />;
  </ThemeContext.Provider>;
};

export default App;
```

<br>

Page 컴포넌트도 깨끗이 정리해준다.

```js
import Content from "./Content";
import Footer from "./Footer";
import Header from "./Header";

const Page = () => {
  return (
    <div>
      <Header />
      <Content />
      <Footer />
    </div>
  );
};

export default Page;
```

<br>

Header, Content, Footer 컴포넌트에서 props를 지우고 useContext와 ThemeContext를 import해서 사용해준다. (useContext를 사용해서, App 컴포넌트에게 '나 그 데이터 사용할래!'라며 반응하는 파트이다!)

```js
import { useContext } from "react";
import { ThemeContext } from "../00/context/ThemeContext";

const Header = () => {
  const { isDark } = useContext(ThemeContext);

  return (
    <div
      style={{
        color: isDark ? "white" : "black",
        backgroundColor: isDark ? "black" : "lightgray",
      }}
    >
      <h1>Welcome 홍길동</h1>
    </div>
  );
};

export default Header;
```

<br>

```js
import { useContext } from "react";
import { ThemeContext } from "./context/ThemeContext";

const Content = () => {
  const { isDark } = useContext(ThemeContext);

  return (
    <div
      style={{
        backgroundColor: isDark ? "black" : "white",
        color: isDark ? "white" : "black",
      }}
    >
      <p>홍길동님 좋은 하루!</p>
    </div>
  );
};

export default Content;
```

<br>

```js
import { useContext } from "react";
import { ThemeContext } from "./context/ThemeContext";

const Footer = () => {
  const { isDark, setIsDark } = useContext(ThemeContext);

  const onToggleThmeme = () => {
    setIsDark(!isDark);
  };
  return (
    <footer
      style={{
        backgroundColor: isDark ? "black" : "lightgray",
      }}
    >
      <button onClick={onToggleThmeme}>
        {isDark ? `Light Mode` : `Dark Mode`}
      </button>
    </footer>
  );
};

export default Footer;
```

props를 사용했을 때와 똑같이 작동하지만, Page 컴포넌트는 자식 컴포넌트인 Header, Content, Footer 컴포넌트가 Page 컴포넌트의 부모 컴포넌트인 App 컴포넌트로부터 데이터를 가져다가 쓴 사실을 모른다!

<br>

> context를 사용하면 위에서 아래로 데이터의 흐름인 리액트의 단방향 원칙에 위배되지도 않고, 중간 컴포넌트가 더럽혀지지 않으며, 중간에 데이터가 훼손될 위험이 없다.
