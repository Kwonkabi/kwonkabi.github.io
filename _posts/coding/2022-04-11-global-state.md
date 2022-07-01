---
title: "글로벌 스테이트(Global State)와 Recoil 사용법"
excerpt: "스테이트 관리 라이브러리 사용 흐름을 알아보고, Recoil로 실습해보기!"

categories: coding
tags: [global state, context-api, redux, mobx, swr]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---


### 1. 글로벌 스테이트(Global State)
부모 컴포넌트가 가지고 있는 state 데이터를 최하위에 위치한 자식 컴포넌트에게 넘겨주려면 끝없이.. 모든 자식들을 타고 props가 마지막 자식 컴포넌트까지 내려가야 한다. 이걸 **props-drilling**이라고 하는데, 매우 귀찮고 번거롭다. 그렇다면 '다이렉트로 한방에 줄 수 있는 스테이트'는 없을까? 있다. 이걸 **글로벌 스테이트**라 부른다.

#### 1.1. Redux, mobX, swr...
그래서 리액트 사용 초창기에는 Redux를 설치해서, 그 안에 저장된 스테이트를 뽑아서 쓸 수 있었다. (Redux 사용법은 영상으로 대체하도록 하겠다:[React 입문자들이 알아야할 Redux 쉽게설명 (8분컷)](https://www.youtube.com/watch?v=QZcYz2NrDIs)) 그런데 Redx는 세팅도 어렵고, 이해의 영역이 아닌 복붙코드도 많고 복잡했다고 한다. 그 외에도 mobX나 swr과 같은 state 관리 라이브러리들이 사용되었다. 그러다가 최근에는 용도에 따라 **api용 글로벌 스테이트**는 *react-query(rest-API 용)*와 *apollo-client(grqphQL)*, **일반적인** 글로벌 스테이트는 *context-API*를 사용하게 되었다.

#### 1.2. Context-API와 Recoil
context-API는 그런데 불필요한 렌더링이 일어난다는 단점이 있었는데, 이걸 보완해서 나온 게 Recoil이다. Recoil은 업데이트된 state 부분만 리렌더 해준다. Recoil도 context-API를 기반으로 하고 있는 라이브러리이다.


### 2. Recoil 사용법
※ [**Recoil 시작하기**](https://recoiljs.org/ko/docs/introduction/getting-started/)

#### 2.1. Recoil 설치
npm 사용자는 `npm install recoil`을, yarn 사용자는 `yarn add recoil`을 입력한다.

#### 2.2. Recoil 세팅
app.tsx 파일에서 `import { RecoilRoot } from 'recoil'` import 해주고, `<Component />`를 `<RecoilRoot></RecoilRoot>`로 감싼다.
![recoil](\assets/images/recoil/app.png)
(레이아웃이나 글로벌스타일스를 포함해도 상관없다.)

#### 2.3. Recoil 사용
Recoildptjsms Atom으로 state의 일부를 보여준다. 그리고 컴포넌트들은 자신이 필요한 Atom을 참조한다. 따라서 자신이 참조하고 있는 Ato에 변화가 있으면 해당 Atom을 참조하는 모든 컴포넌트에서 리렌더링이 일어난다.
![atom](\assets/images/recoil/atom.png)
(Atom을 store라는 폴더에 따로 저장했다.)

Recoil을 사용해서 isEdit스테이트를 프레젠터까지 내려준 사용 예시이다.
![recoil_usage](\assets/images/recoil/recoil_usage.png)

- Atom

```javascript
import { atom } from "recoil";

export const isEditState = atom({
  key: "isEditState", // state의 이름; 글로벌스테이트는 하나라서 키로 구분함
  default: false, // useState의 초깃값이었던 것
});

```

- Index

```javascript
import { useEffect, useState } from "react";
import { useRecoilState } from "recoil";
import { isEditState } from "../../src/commons/store";
import GlobalStateContainer from "../../src/components/units/board/21-global-state/BoardWrite.container";

export default function GlobalStatePage() {
  const [isEdit, setIsEdit] = useRecoilState(isEditState);

  useEffect(() => {
    setIsEdit(true);
  }, []);

  return <GlobalStateContainer />;
}
```

- Container

```javascript
import GlobalStatePresenter from "./BoardWrite.presenter";

export default function GlobalStateContainer() {
  return <GlobalStatePresenter />;
}
```

- Presenter

```javascript
import { useState } from "react";
import { useRecoilState } from "recoil";
import { isEditState } from "../../../../commons/store";

export default function GlobalStatePresenter() {
  const [isEdit, setIsEdit] = useRecoilState(isEditState);

  return <div>{isEdit ? "수정하기" : "등록하기"}</div>;
}
```