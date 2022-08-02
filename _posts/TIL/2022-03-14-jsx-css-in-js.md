---
title: "리액트에선 html과 css를 어떻게 작성하나"
excerpt: "모든 파일은 다 자바스크립트로!"

categories: react
tags: [javascript, react, import, export, jsx, css-in-js]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

※ 리액트 공식 문서 : [**JSX 소개**](https://ko.reactjs.org/docs/introducing-jsx.html)

리액트를 배우기 전에는 html, css, js 파일을 모두 따로 만들어서 html파일의 head 태그 내에서 연결해주었다.

```html
<head>
  <title>코드캠프 회원가입</title>
  <link href="./m-final.css" rel="stylesheet" />
  <script src="./m-final.js"></script>
</head>
```

바닐라자바스크립트로 개발을 하게 되면, 겹치는 코드를 작성하려면 복붙해야 하기 때문에 코드량이 증가하고, 그 중 하나를 고쳐야 한다면 하나하나 찾아서 고쳐야 하는 번거로움이 있다.

번거로울 뿐만 아니라, 수작업으로 찾아 고치다보면 버그가 발생할 확률이 올라가게 되고 그때부터 지뢰밭이 되는 것이다.. 어니서 버그가 나타날지 모르는 지뢰밭!

그래서 리액트에서는 컴포넌트라는 부품을 만들어서 여기저기서 불러와서 사용하고, 수정이 필요할 때는 그 부품만 고치면 그 부품을 불러다 쓴 페이지에서 한꺼번에 수정할 수 있게 만들었다! 유지보수성이 너무 좋아진 것!!!

그럼 그 부품을 어떻게 가져다 쓰는지부터 알아보자.

<br>

---

<br>

### 1. 다른 폴더의 파일 불러오기 (export/import)

프론트엔드에서는 주로 html, css, Javascript로 소스코드를 작성하는데, 하나의 소스코드에 모든 내용을 작업하면 너무 복잡해진다. 그래서 리액트를 이용해서 html, css, javascript 소스코드를 각각 나누어 작성하고 필요에 따라 서로 불러와서 사용하게 된다. 이때 불려가는 파일은 **'export'**된다고 하고, 불러오는 파일은 **'import'**한다고 말한다. (모든 파일은 js로 작성되고, 확장자도 .js이다.)

예를 들어 다음과 같은 코드가 있다고 하자. (사실은 1억줄이라고 치자.)

```javascript
const aaa = "this is aaa";
const bbb = "this is bbb";
const ccc = "this is ccc";
const ddd = "this is ddd";
const eee = "this is eee";
const fff = "this is fff";

console.log(aaa);
console.log(bbb);
console.log(ccc);
console.log(ddd);
console.log(eee);
console.log(fff);
```

이러한 1억줄짜리 코드를 나누어서 작성해도 위와 똑같이 작동한다.

file1:

```javascript
import { aaa, bbb, ccc } from "./file2";

const ddd = "this is ddd";
const eee = "this is eee";
const fff = "this is fff";

console.log(aaa);
console.log(bbb);
console.log(ccc);
console.log(ddd);
console.log(eee);
console.log(fff);
```

file2:

```javascript
export const aaa = "this is aaa";
export const bbb = "this is bbb";
export const ccc = "this is ccc";
```

불러오는 파일 안에서는 **import**와 **{불러올 내용}** from **경로**를 작성해주고,

불려가는 파일에서는 **export**를 이용해서 변수를 내보내주면 된다.

<br>

---

<br>

### 2. JSX 사용법

React에서는 보통 HTML을 사용하기 위한 자바스크립트 확장문법인 JSX를 사용한다. 사용법은 기존 html 방식과 거의 비슷하다. 속성값을 사용할 때 이름이 조금 다르거나 캐멀케이스를 사용하는 정도의 차이가 있다.

- 기존 html

```javascript
<div>
  <div class="title">제목</div>
  <button onclick="alert()"/>
<div>
```

- js 파일 안에서 작성된 html (jsx)

```javascript
<div>
  <div className="title">제목</div>
  <button onClick="alert()"/>
<div>
```

자바스크립트 파일 안에 작성되지만, 최종적으로 소스코드가 실행될 때에는 JSX가 HTML로 자동변환되어 실행된다.
※ input이나 img처럼 닫는 태그가 없는 경우, />를 이용해 바로 닫아주어야 한다.

<br>

---

<br>

### 3. CSS-in-JS

React에서 사용하는 css도 기존과는 조금 다르다. css-in-js라고 하는데, css를 자바스크립트 상수에 저장해서 사용하는 방법이다. (그중에서도 emotion이라는 css-in-js의 종류 중 하나를 사용해보았다.) 기존 css파일에서 사용하던 방식과 비교하면 다음과 같다.

```html
<div class="title">Hello World</div>
```

이러한 html 코드가 있었을 때,

```css
.title {
  width: 996px;
  height: 52px;
}
```

이러한 방식으로 class 선택자를 사용해서 css 코드를 작성했다. css-in-js 방식에서는 다음과 같이 작성한다. jsx의 태그명도 함께 변경된다(대문자로 시작!).

```javascript
<Title>Hello World</Title>
```

```javascript
import styled from "@emotion/styled";

const Title = styled.div`
  width: 996px;
  height: 52px;
`;
```

이렇게 사용할 때의 장점은, **태그에 의미를 부여할 수 있기 때문에 1. 태그만 봐도 결과물이 예상이 간다**는 점이고, **2. class명을 입력할 필요가 없어 코드의 길이가 짧아져** 읽기 쉬운 코드가 된다는 것이다. 한눈에 보아도 훨씬 편하게 사용할 수 있다는 걸 알 수 있다! (기존 css vs. css-in-js)

![css-in-js](\assets/images/jsx/css-in-js.jpg)
