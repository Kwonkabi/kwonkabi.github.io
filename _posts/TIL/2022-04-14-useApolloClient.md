---
title: "useQuery 말고 useApolloClient"
excerpt: "useApolloClient를 사용해 원하는 시점에 쿼리 요청을 보내고 결과를 받아보자!"

categories: react
tags: [react, useApolloClient]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### useApolloClient

쿼리 방식에는 useQuery만 있는 건 아니다. 컴포넌트가 마운트되면 실행되는 useQuery와 달리 axios처럼 원하는 위치에서 받을 수 있게 도와주고 결과 값을 내가 원하는 상자에 담아줄 수 있는 useApolloClient가 있다.

(물론 유저 정보를 로컬 스토리지에 저장하면 안 되지만.. 원하는 시점에 query요청을 보낼 수 있는 useApolloClient의 특징을 알아보기 위한 예시로 보도록 하자!)

지금 우리는 로그인을 하고, 라우터로 다른 페이지로 이동하자마자 그 페이지에서 유저의 이름을 포함해서 '유저이름님 환영합니다!'라는 텍스트를 보여주고 싶다고 치자.

useQuery를 사용하면, 라우팅된 페이지에서 쿼리를 해야 유저의 이름을 볼 수 있을 것이다.

그치만 useApolloClient를 사용하면 원하는 시점에 쿼리를 할 수 있기 때문에, 라우팅되기 전에 쿼리를 해서 글로벌 스테이트에 유저 정보를 저장해두고 페이지가 이동하자마자 유저의 이름을 볼 수 있게 된다!!

```js
// routing page

import { gql, useApolloClient, useMutation } from "@apollo/client";
import { useRouter } from "next/router";
import { ChangeEvent, useState } from "react";
import { useRecoilState } from "recoil";
import { accessTokenState, userInfoState } from "../../src/commons/store";

const LOGIN_USER = gql`
  mutation loginUser($email: String!, $password: String!) {
    loginUser(email: $email, password: $password) {
      accessToken
    }
  }
`;

const FETCH_USER_LOGGED_IN = gql`
  query fetchUserLoggedIn {
    fetchUserLoggedIn {
      email
      name
    }
  }
`;

export default function LoginPage() {
  const [, setAccessToken] = useRecoilState(accessTokenState);
  const [, setUserInfo] = useRecoilState(userInfoState);
  const router = useRouter();
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [loginUser] = useMutation(LOGIN_USER);

  const client = useApolloClient();

  const onChangeEmail = (event: ChangeEvent<HTMLInputElement>) => {
    setEmail(event.target.value);
  };

  const onChangePassword = (event: ChangeEvent<HTMLInputElement>) => {
    setPassword(event.target.value);
  };

  const onClickLogin = async () => {
    // 1. 로그인하기
    const result = await loginUser({
      variables: {
        email,
        password,
      },
    });
    const accessToken = result.data.loginUser.accessToken;
    console.log(accessToken);

    // 2. 유저 정보 받아오기
    const resultUserInfo = await client.query({
      query: FETCH_USER_LOGGED_IN,
      // accessToken이 첨부돼 있어야 정상적으로 쿼리가 작동하는데 아직 저장 안 되어 있음, 아래처럼 써주기!
      context: {
        headers: {
          Authorization: `Bearer ${accessToken}`,
        },
      },
    });
    const userInfo = resultUserInfo.data.fetchUserLoggedIn;
    console.log(userInfo);

    // 3. 글로벌 스테이트에 유저 정보 저장하기
    setAccessToken(accessToken);
    setUserInfo(userInfo);
    localStorage.setItem("accessToken", accessToken);
    localStorage.setItem("userInfo", JSON.stringify(userInfo));
    // 객체는 로컬/세션 스토리지에 못 들어감 주의! 문자열로 바꿔서 넣어주자!

    // 4. 로그인 성공 페이지로 이동하기
    alert("로그인에 성공하였습니다!");
    router.push("/24-02-login-use-apollo-client-success");
  };

  return (
    <div>
      이메일: <input type="text" onChange={onChangeEmail} />
      <br />
      비밀번호: <input type="password" onChange={onChangePassword} />
      <br />
      <button onClick={onClickLogin}>로그인하기</button>
    </div>
  );
}
```

원하는 시점('로그인하기 버튼'이 눌린 시점)에 query 요청을 보낼 수 있도록, fetchUserLoggedIn 결과값(UserInfo)을 GlobalState에 담아주었다.

이를 위해 onClickLogin 안에서 client.query({})로 요청을 보냈다.

여기서 중요한 포인트가 있는데, 어느 정보를 불러올지 인증 정보를 함께 보내야 한다는 것이다.

따라서 client.query의 context의 headers 내부에 Authorization 정보를 함께 첨부해 보내주고, 그 결과값을 GlobalContext 안 setUserInfo에 담아주어야 한다!

<br>

```js
// routed page

import { useRecoilState } from "recoil";
import { userInfoState } from "../../src/commons/store";

function LoginSuccessPage() {
  const [userInfo] = useRecoilState(userInfoState);

  return <div>{userInfo.name}님 환영합니다!!!</div>;
}

export default LoginSuccessPage;
```

따로 useQuery를 사용해서 쿼리 요청을 하지 않아도 페이지가 이동되면 글로벌 스테이트에 저장된 userInfo.name을 화면에 렌더한다. 처음 렌더되어 쿼리 요청에 대한 응답을 받아 오기 전 undefined가 있을 틈이 없다.
