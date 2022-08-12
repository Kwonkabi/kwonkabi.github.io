---
title: "글로벌 스테이트(Global State)"
excerpt: "props 없이 전역에서 상태관리를 해주자"

categories: react
tags:
  [react, global state, apollo cache state, fetch policy, context API, recoil]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 글로벌 스테이트

로그인이 되어 있는지 확인이 필요한 페이지(결제, 마이페이지 등)들이 있을때, 로그인 검증을 모든 컴포넌트에 넣어주는 것은 비효율적이다. 이를 해결하기 위한 글로벌 스테이트(Global State)라는 개념이 있다. 글로벌 스테잍 를 활용하면 프롭스 드릴링을 피하면서 전역에서 스테이트를 사용할 수 있다. <br>

Context API라는 것을 활용할 수도 있고, Redux 또는 Apollo Client의 Apollo Cache State를 활용할 수도 있다. 글로벌 스테이트는 보통 API와 관련된 스테이트인지 일반적인 스테이트인지에 따라 따로 관리된다.

<br>

#### 1.1. API 글로벌 스테이트

우선, rest API는 react-query를 graphQL을 사용했으므로 apollo client를 사용한다는 것을 알아두자. <br>

##### Fetch Policy

Apollo Client는 Apollo Cache라는 state를 가지고 있고 여기에는 컴포넌트에서 호출하는 API의 결과값 데이터가 담기게 된다. 만약 다른 컴포넌트에서 같은 요청을 했을때 Apollo Cache State에 담겨있는지를 확인하고 없으면 호출하게 되어 네트워크 요청 효율성을 높이게 된다.(처음에는 data가 undefined인 이유) 이러한 요청 방식은 **fetch policy**의 _cache-first_ 방식(디폴트)이였고, 이를 rest처럼 요청마다 새로 요청하도록 사용하고 싶다면 _network-only_ 방식을 선택하여 사용하는 것이다. 이렇게 fetchPolicy로 API 관련 스테이트를 효율적으로 관리할 수 있기 때문에 일반 글로벌 스테이트와 따로 관리한다.<br>

fetchPolicy를 설정해주는 부분은 주석을 확인하자.

```js
import { useQuery, gql } from "@apollo/client";
import { useEffect, useState } from "react";
import FetchPolicyTest from "../../src/components/units/board/21-fetch-policy";

const FETCH_BOARDS = gql`
  query fetchBoards {
    fetchBoards {
      _id
      writer
      title
      contents
    }
  }
`;

export default function GlobalStatePage() {
  const { data } = useQuery(FETCH_BOARDS, {
    fetchPolicy: "cache-first", // 디폴트, "network-only" 등 선택 가능
  });
  const [aaa, setAaa] = useState(false);

  const onClickAaa = () => {
    setAaa(true);
  };

  return (
    <div>
      <button onClick={onClickAaa}>
        클릭하면 새로운 컴포넌트가 나타납니다!!
      </button>
      {aaa && <FetchPolicyTest />}
    </div>
  );
}
```

<br>

```js
import { gql, useQuery } from "@apollo/client";

const FETCH_BOARDS = gql`
  query fetchBoards {
    fetchBoards {
      _id
      writer
      title
      contents
    }
  }
`;

export default function FetchPolicyTest() {
  const { data } = useQuery(FETCH_BOARDS);

  return (
    <div>
      {data?.fetchBoards?.map((el) => (
        <div key={el._id}>{el.title}</div>
      ))}
    </div>
  );
}
```

fetchPolicy가 cache-first이기 때문에 버튼을 눌러 페치를 다시 해보자. 아예 동일한 데이터를 요청하려는 것이기 때문에 즉, 이미 apollo cache state에 있는 걸 가져오기 때문에, 네트워크 탭을 확인해봐도 새로운 요청이 날아가지 않는 것을 확인할 수 있다.

<br>

#### 1.2. 일반적인 글로벌 스테이트

우리가 만드는 일반적인 글로벌 스테이트는 Context API, 혹은 recoil을 사용해서 관리한다. Context API와 recoil은 우리가 어떤 컴포넌트들을 묶어줄지 지정할 수 있다. 또한 여러 그룹으로 만들어 줄 수 있고 그룹끼리 묶어줄 수 있다. <br>

하지만 context-api도 불필요한 렌더링이 일어난다는 문제점이 있다. 이를 보완하기 위해 나온 것이 recoil이다. 환경 설정은 context-api와 동일하게 app.tsx에서 진행한다. RecoilRoot로 감싸줘야 한다.

```js
import "antd/dist/antd.css"; // or 'antd/dist/antd.less'
import "../styles/globals.css";

import { AppProps } from "next/app";
import { globalStyles } from "../src/commons/styles/globalStyles";
import Layout from "../src/components/commons/layout";
import { Global } from "@emotion/react";

import { RecoilRoot } from "recoil";
import ApolloSetting from "../src/components/commons/apollo";

function MyApp({ Component, pageProps }: AppProps) {
  return (
    <div>
      <RecoilRoot>
        <ApolloSetting>
          <Global styles={globalStyles} />
          <Layout>
            <Component {...pageProps} />
          </Layout>
        </ApolloSetting>
      </RecoilRoot>
    </div>
  );
}

export default MyApp;
```

<br>

그 후에 필요한 atom을 만들어준 후 참조해주면 되는데 이때 참조하고 있는 atom에 변화가 일어난다면 해당 atom을 참조하는 모든 컴포넌트에서 재렌더링이 일어난다. 실제로 사용할 때는 useRecoilState로 useState처럼 사용하면 된다. <br>

아래 이미지는 페이스북에서 atom을 시각화해서 설명하는 영상을 캡처한 것이다. atom을 비눗방울로 추상화 할 수 있다. 즉, 우리가 만드는 Web Application을 구조화 한다면 그 구조의 상단에 atom이 비눗방울 처럼 둥둥 떠다니고 있다고 추상화 할 수 있다. 만약 개발을 하다가 어떤 비눗방울(상태)이 필요하다면 해당하는 비눗방울만 쏙 빼서 쉽게 사용할 수 있다.

![atom](/assets/images/recoil/atom-fb.png)

<br>

```js
// store 파일

import { atom } from "recoil";

export const isEditState = atom({
  key: "isEditState",
  default: false,
});
```

```js
// 페이지 컴포넌트

import { useEffect } from "react";
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

<br>

```js
// 컨테이너 컴포넌트 (글로벌 스테이트 사용되지 않음)

import GlobalStatePresenter from "./BoardWrite.presenter";

export default function GlobalStateContainer() {
  return <GlobalStatePresenter />;
}
```

<br>

```js
// 프레젠터 컴포넌트

import { useRecoilState } from "recoil";
import { isEditState } from "../../../../commons/store";

export default function GlobalStatePresenter() {
  const [isEdit, setIsEdit] = useRecoilState(isEditState);

  return <div>{isEdit ? "수정하기" : "등록하기"}</div>;
}
```

이를 활용하면 몇번의 단계를 통해 내려서 내려서 전달시켜주던 props 없이(props drilling 없이) useRecoilState로 필요한 곳에서 바로 손쉽게 사용할 수 있다. <br>

리코일 스테이트에는 atom만 사용할 수 있는 것은 아니다. selector를 사용하면 글로벌 스테이트에 함수도 담을 수 있다. default 대신 get을 써주고 프로퍼티 값으로 함수를 넣어주면 atom처럼 다른 컴포넌트에서 사용할 수 있다.

```js
// store 파일

import { selector } from "recoil";
import { getAccessToken } from "../libraries/getAccessToken";

export const restoreAccessTokenLoadable = selector({
  key: "restoreAccessTokenLoadable",
  get: async () => {
    const newAccessToken = await getAccessToken();
    return newAccessToken;
  },
});
```

<br>

📌 참고:

- [Recoil 공식 문서](https://recoiljs.org/ko/docs/introduction/installation)
- ['Recoil, 리액트의 상태관리 라이브러리'](https://tech.osci.kr/2022/06/16/recoil-state-management-of-react/)

<br>

만약 contextAPI를 사용한다면, 감싸줄 부분에서 createContext로 만들어, 사용할 곳에서 useContext로 받아주면 되고, .Provider로 감싸주어 value를 통해 전달하려는 내용을 객체로 전달시켜주면 된다.

📌 나의 관련 포스팅: [useContext](https://kwonkabi.github.io/react/useContext/)
