---
title: "커스텀 훅(Custom hooks)"
excerpt: "을 만들어보자!"

categories: react
tags: [react, custom hooks, useAuth, useMoveToPage]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

커스텀 훅은 우리가 함수의 재사용성을 위해 자주 쓰는 함수를 import/export해서 쓸 수 있도록 만든 것이다. 다른 함수와는 달리 함수 이름 앞에 use를 붙여서 만드는데, 그 이유는 react hooks를 활용해서 만들었기 때문이다.

#### useAuth 만들어 사용하기

물론 로컬 스토리지에 로그인 정보를 저장해서는 안 되지만.. 간단한 예시를 위해 로컬 스토리지에 accessToken을 넣어둔 상황이라고 치자.

```js
// useAuth를 만들어서 export하기

import { useRouter } from "next/router";
import { useEffect } from "react";

export function useAuth() {
  const router = useRouter();

  // 권한분기 로직 추가하기
  useEffect(() => {
    if (!localStorage.getItem("accessToken")) {
      // 로그인이 안 된 상황
      alert("로그인 후 이용 가능합니다.");
      router.push("/24-01-login-use-apollo-client");
    }
  }, []);
}
```

렌더 시에 로컬 스토리지에 accessToken이 없는 경우 alert창을 띄우고, 원하는 페이지로 이동시켜주는 커스텀 훅을 만들었다.

<br>

```js
// useAuth를 import해서 사용하기

import { useAuth } from "../../src/components/commons/hooks/useAuth";

function CustomHooksUseAuthPage() {
  useAuth();

  return <div>철수의 프로필 페이지입니다!!!</div>;
}

export default CustomHooksUseAuthPage;
```

권한분기가 필요한 컴포넌트에서 import해서 사용해주면 된다.

<br>

#### useMoveToPage 만들기

페이지를 이동시킬 때마다 onClick함수를 만들어서 useRouter를 import하고 router.push를 사용하는 것은 귀찮은 일이다.

```js
// useMoveToPage를 만들어서 export하기

import { useRouter } from "next/router";
import { useRecoilState } from "recoil";
import { visitedPageState } from "../../../commons/store";

export function useMoveToPage() {
  const router = useRouter();
  const [visitedPage, setVisitedPage] = useRecoilState(visitedPageState);

  const onClickMoveToPage = (path) => () => {
    // 이동하기 전에 path를 저장해놓고 이동하기!!
    setVisitedPage(path);
    router.push(path);
  };

  // 이름 바뀌는 걸 원하지 않기 때문에 객체로 리턴
  return {
    visitedPage,
    onClickMoveToPage,
  };
}
```

리코일을 사용해 방문한 페이지를 글로벌 스테이트로 만들어 저장해준 다음, 원하는 경로를 path라는 매개변수 자리에 넣어줄 수 있도록 만들어준다. visitedPage와 onClickMoveToPage를 리턴해줌으로써, useMoveToPage 커스텀 훅을 사용하여 방문한 페이지 스테이트와 원하는 경로로 보내주는 함수를 가져다 쓸 수 있게 된다.

```js
// useMoveToPage를 import해서 사용하기

import { useMoveToPage } from "../../src/components/commons/hooks/useMoveToPage";

export default function CustomHooksUseMoveToPage() {
  const { onClickMoveToPage } = useMoveToPage();

  return (
    <div>
      <button onClick={onClickMoveToPage("/board")}>게시판으로 이동</button>
      <button onClick={onClickMoveToPage("/market")}>마켓으로 이동</button>
      <button onClick={onClickMoveToPage("/mypage")}>마이페이지로 이동</button>
    </div>
  );
}
```

리턴 받은 visitedPage와 onClickMoveToPage 중에서 구조분해할당을 통해 onClickMoveToPage만 사용한 예시이다. 재사용성이 좋아졌고, 코드도 간결해졌다.
