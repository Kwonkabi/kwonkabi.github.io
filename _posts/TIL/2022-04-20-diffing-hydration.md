---
title: "하이드레이션과 스타일 이슈"
excerpt: "Next.js에선 스타일을 주의하자"

categories: next
tags: [next, next.js, hydration, SSR]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 하이드레이션

> Hydrate(Hydration, 이하 하이드레이션)는 서버 사이드 단에서 렌더링 된 정적 페이지와 번들링된 JS 파일을 클라이언트에게 보낸 뒤, 클라이언트 단에서 HTML 코드와 React인 JS 코드를 서로 매칭 시키는 과정을 말한다.

하이드레이션에 대해 이해하기 위해서는 일단 리액트와 넥스트가 웹 페이지를 구성하는 원리에 대해 이해해야 한다.

---

<br>

#### React의 웹 페이지 구성 원리

리액트는 자바스크립트 파일만을 이용하여 웹 화면을 구성한다. 그래서 실제 HTML 코드는 비어 있는 상태이며, 이것은 CSR(Client Side Rendering)이 SEO(Search Engine Optimization, 검색 엔진 최적화)에 적합하지 않은 이유가 된다. <br>

```js
// public/index.html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

위와 같이 단순 뼈대만 있는 HTML 문서와 자바스크립트 파일들을 클라이언트로 모두 보낸 뒤, 클라이언트 단에서 자바스크립트 코드들을 통해 웹화면을 렌더링한다. <br>

웹 페이지 렌더링 후에도, 페이지 내에서 동작하는 모든 이벤트 또한 자바스크립트로 일어난다.

```js
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import App from "./src/App";

ReactDOM.render(<App />, document.getElementById("root"));
```

public/index.html에는 아무 내용 없는 기본 뼈대만 있고, 나머지는 src/index.js의 자바스크립트 코드에서 모든 화면을 렌더링 한 뒤 HTML DOM 요소 중 root라는 아이디를 가진 엘리먼트를 찾아서 하위로 주입한다.

<br>

#### Next.js의 웹 페이지 구성 원리

Next.js는 클라이언트에게 웹 페이지를 보내기 전에 Server Side 단에서 미리 웹 페이지를 Pre-Rendering 한다. 그리고 Pre-Redering으로 인해 생성된 HTML 문서를 클라이언트에게 전송한다. <br>

현재 클라이언트가 받은 웹 페이지는 단순히 웹 화면만 보여주는 HTML일 뿐이고, 자바스크립트 요소들이 하나도 없는 상태이다. 즉, 웹 화면을 보여주고 있지만, 특정 JS 모듈 뿐 아니라 단순 클릭과 같은 이벤트 리스너들이 각 웹 페이지의 DOM 요소에 하나도 적용되어 있지 않은 상태다. <br>

Next.js Server에서는 Pre-Rendering된 웹 페이지를 클라이언트에게 보내고 나서, 바로 리액트가 번들링 된 자바스크립트 코드들을 클라이언트에게 전송한다. 이 자바스크립트 코드들이 이전에 보내진 HTML DOM 요소 위에서 한번 더 렌더링을 하면서, 각자 자기 자리를 찾아가며 매칭된다. <br>

바로 이 과정을 Hydrate라 부른다. 자바스크립트 코드들이 DOM 요소를 촉촉하게 채운다는 의미에서 Hydrate라는 용어를 사용한 것이다. <br>

HTML DOM 요소에 뒤늦게 자바스크립트가 동작하고 Hydration되기 때문에 Next.js에서는 새롭게 페이지를 로딩할 때마다 약간 뒤늦게 스타일이 적용되는 것 같은 잠깐의 스타일 깜빡임을 흔하게 볼 수 있다.

---

### 하이드레이션 이슈

요약하면 하이드레이션 단계에서 1) 렌더링한 결과물이 어떤 컴포넌트인지 확인하고, 2) 각 컴포넌트에 걸린 이벤트 들을 실제 DOM에 걸어주는 동작을 한다. 하이드레이션이 잘못되었을 때, 우리가 마주하는 문제들은 거의 1번 과정에서 발생한 것이다. <br>

다음은 에디터를 사용해 작성한 내용을 불러와 보여주기 위해 작성한 코드이다.

```js
import { gql, useQuery } from "@apollo/client";
import { useRouter } from "next/router";
import Dompurify from "dompurify";

const FETCH_BOARD = gql`
  query fetchBoard($boardId: ID!) {
    fetchBoard(boardId: $boardId) {
      _id
      writer
      title
      contents
    }
  }
`;

export default function WebEditorDetailPage() {
  const router = useRouter();

  const { data } = useQuery(FETCH_BOARD, {
    variables: { boardId: router.query.id },
  });

  return (
    <div>
      <div style={{ color: "red" }}>작성자: {data?.fetchBoard.writer}</div>
      <div style={{ color: "green" }}>제목: {data?.fetchBoard.title}</div>
      {/* 작성된 태그가 문자열이 아니라 태그로 인식되도록 하기 위해 */}
      {typeof window !== "undefined" && (
        <div
          style={{ color: "blue" }}
          dangerouslySetInnerHTML={{
            __html: Dompurify.sanitize(data?.fetchBoard.contents),
          }}
        ></div>
      )}
      <div style={{ color: "orange" }}>상품가격: </div>
    </div>
  );
}
```

브라우저 DOM에서 innerHTML을 사용하기 위한 React의 대체 방법으로써 dangerouslySetInnerHTML 태그를 사용했다. 직접적으로 HTML을 삽입할 수 있습니다. 그래야 에디터에 작성된 내용을 불러올 수 있기 때문이다. <br>

그런데 일반적으로 코드에서 HTML를 설정하는 것은 XSS 공격에 쉽게 노출될 수 있기 때문에 위험하다. <br>

따라서 React에서 직접 HTML을 설정할 수는 있지만, 위험하다는 것을 상기시키기 위해 dangerouslySetInnerHTML을 작성하고 \_\_html 키로 객체를 전달해야 한다.

문제는, 프리렌더링 시에는 돔 조작을 할 수 없으므로 window의 타입이 undefined가 아닐 때에만 contents가 들어 있는 태그를 보여줘야 한다. 즉, 프리 렌더링 시점에서의 태그의 개수와 브라우저 렌더링 시점에서의 태그의 개수에 차이가 난다. 따라서 red-green-orange 순으로 태그의 색상을 알고 있는데, 막상 렌더될 때는 세번째 태그가 blue임에도 orange로 보이게 된다.

<br>

```js
// ...

export default function WebEditorDetailPage() {
  // ...

  return (
    <div>
      <div style={{ color: "red" }}>작성자: {data?.fetchBoard.writer}</div>
      <div style={{ color: "green" }}>제목: {data?.fetchBoard.title}</div>
      {typeof window !== "undefined" ? (
        <div
          dangerouslySetInnerHTML={{
            __html: Dompurify.sanitize(data?.fetchBoard.contents),
          }}
        ></div>
      ) : (
        // 하이드레이션 이슈 해결 방법
        <div style={{ color: "blue" }}></div>
      )}
      <div style={{ color: "orange" }}>상품가격: </div>
    </div>
  );
}
```

위의 문제는 프리 렌더링 시에, 브라우저 렌더링과 같은 css를 가진 태그를 보여주는(태그 개수를 맞춰주는) 방식으로 해결할 수 있다.
