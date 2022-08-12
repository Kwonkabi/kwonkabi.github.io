---
title: "JWT을 활용한 로그인 프로세스"
excerpt: "로그인 프로세스의 변천사에 대해 알아보고, JWT로 로그인 기능을 구현해보자!"

categories: react
tags: [react, login, JWT, accessToken, refreshToken]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 로그인 프로세스 변천사

Browser에서 로그인 확인이 필요한 데이터 요청을 Backend로 보낼 경우, 예전에는 백엔드의 메모리세션에 로그인 정보들을 기록했다. 하지만 이 방식은 메모리가 다 차게 될 경우 더 이상 정보 저장을 할 수 없다는 문제가 있었다. 이 문제를 해결 하기 위해 같은 정보를 가진 백엔드 서버를 여러개 만들어 처리했는데, 이 방법을 스케일 아웃(scale-out)이라 한다. <br>

그런데 이 스케일 아웃 방법으로는, 정보는 같지만 처리는 다르기 때문에 특정 로그인 기록이 남아있는 백엔드 서버가 아니라면 특정 유저의 로그인 유무를 인지하지 못하는 문제가 있었다. <br>

이에 대한 대안으로 로그인 정보를 DB에 넣는 방식을 사용하게 되었다. 하지만 이 방식도 문제점을 전부 해결하진 못했다. 데이터베이스에서 병목현상이 일어나게 되었기 때문이다. 백엔드 서버처럼 DB를 여러개 복사하기는 쉽지 않았다. 그래서 나온 방법이 인증 테이블 파티셔닝이었다. 예를 들어, 1번부터 3000번까지는 1번 DB에 저장하는 식으로 정보를 나눠 보관하는 방식을 사용했다. <br>

위의 문제점들을 해결한 방식이였지만, 위 방식도 결국 DB에 접근해서 데이터를 꺼내와야하는 번거로움이 있었다. <br>

> 앞서 알아본 흐름들과 대안들의 최종 대안으로 나온 것이 JWT다.

(JWT 말고도 메모리 대신 캐시를 사용하는 memCached나 Redis라는 대안도 있다.) <br>

JWT는 토큰 자체에 로그인 정보가 들어 있기 때문에 백엔드에서 DB로 갈 필요가 없어졌다. 하지만 JWT도 백엔드에 들어가 **접속의 유무만 구분**해주기 때문에 중간에 탈취가 되어도 이 정보가 맞는지 DB검증이 불가능하다는 한계가 있었다. 그래서 만료시간을 주어 시간이 지나면 재발급(refreshToken)시키게 되었다. <br>

<br>

#### JWT를 활용한 로그인 프로세스

첫 로그인을 진행했을때, 즉 인증(authentication) 과정에서, 2개의 결과물(accessToken과 refreshToken)을 보내준다. 받아온 accessToken을 state에 담아주고 refreshToken은 cookie에 담아준다. <br>

이후 로그인 유무가 필요한 특정 API를 요청한다고 가정해보자. accessToken을 함께 넘겨 주어 인가(authorization)를 받게 된다. 만약 accessToken이 만료되지 않았다면 잘 작동할 것이다. <br>

하지만 토큰이 만료가 된 경우, 만료된 토큰을 가지고 요청을 보냈을 경우에는 Browser에게 UNAUTHENTED 에러를 반환한다. 이 에러를 받게 되면 Browser 단에서 app.tsx부분에서 받은 에러가 어떤 에러인지 체크한 후 인가 관련 에러일 경우 refreshToken을 가지고 accessToken 재발급 요청을 한다. <br>

그렇게 새로 발급받은 accessToken을 state에 다시 담아준 후 기존에 요청에 실패했던 API를 새 accessToken을 가지고 재요청하는 것이다. <br>

우리 눈에는 보이지 않지만, 토큰이 만료되어도 에러를 주고 받고 새롭게 요청을 보내는 일련의 과정을 통해 로그인 상태가 지속되는 것이었다. 이러한 과정을 수행하기 위해서 우선 app.tsx에 몇 가지 설정을 해주어야 한다. <br>

```js
// ...
import { getAccessToken } from "../../../commons/libraries/getAccessToken";

// ...

const errorLink = onError(({ graphQLErrors, operation, forward }) => {
  // 1-1. 에러를 캐치
  if (graphQLErrors) {
    for (const err of graphQLErrors) {
      // 1-2. 해당 에러가 토큰만료 에러인지 체크(UNAUTHENTICATED)
      if (err.extensions.code === "UNAUTHENTICATED") {
        // 2-1. refreshToken으로 accessToken을 재발급 받기
        getAccessToken().then((newAccessToken) => {
          // 2-2. 재발급 받은 accessToken 저장하기
          setAccessToken(newAccessToken);

          // 3-1. 재발급 받은 accessToken으로 방금 실패한 쿼리 재요청하기
          operation.setContext({
            headers: {
              ...operation.getContext().headers, // 기존 헤더를 스프레드 해서 가져오기
              Authorization: `Bearer ${newAccessToken}`, // accessToken만 새로운 토큰으로 바꿔치기(덮어쓰기)
            },
          });

          // 3-2. 변경된 operation 재요청하기!!
          return forward(operation);
        });
      }
    }
  }
});

const uploadLink = createUploadLink({
  uri: "https://backend06.codebootcamp.co.kr/graphql",
  headers: { Authorization: `Bearer ${accessToken}` },
  credentials: "include",
});

const client = new ApolloClient({
  link: ApolloLink.from([errorLink, uploadLink]),
  cache: new InMemoryCache(),
});

// ...
```

- uploadLink부분을 좀 수정해주어야 한다. 데이터 암호화를 사용하기 위해 기존의 http uri를 https로 변경해주고, 쿠키를 포함시켜 보내기 위해 credentials를 include로 변경시켜준다.

- onError()를 사용해 errorLink를 만들어준다. 이 onError()안에는 콜백함수가 들어간다. 콜백함수의 인자로는 graphQLErrors operation, forward가 들어간다.

**1-1.** 만약 gql에러가 있다면 <br>
**1-2.** 에러를 하나씩 뽑아 해당 에러가 토큰 만료 에러(UNAUTHENTICATED)인지 확인해주고 <br>

**2-1.** 토큰 만료 에러라면 refreshToken으로 accessToken을 재발급 시켜주어야 하는데, 이 부분에서 ApolloClient세팅이 끝나지 않은 시점이기에 restoreAccessToken(useMutation)요청이 불가능하다. 따라서, graphql-request 라이브러리를 사용하여 요청해주면 된다. <br>
**2.2.** 재발급 받은 accessToken을 setAccessToken()을 통해 저장시켜주고, <br>

**3.1.** 방금 실패한 쿼리의 정보가 담긴 operation의 설정을 operation.setContext({})를 활용해, <br>
**3.2.** accessToken만 변경하여 forword(operation)로 재요청해준다. <br>

위 에러링크를 client(ApolloClient)에 연결 시켜주면 끝!!

<br>

다음은 위에서 getAccessToken을 함수로 빼놓은 부분이다. errorLink가 들어가고 나서야 apolloSetting이 되는 거라서 그 전에는 graphql-request같은 것들을 사용해줘야 한다. axios나 graphQLClient를 사용하는 방법이 있는데, 최근엔 후자를 많이 사용한다.

```js
// 2-1. getAccessToken

import { gql, GraphQLClient } from "graphql-request";

const RESTORE_ACCESS_TOKEN = gql`
  mutation restoreAccessToken {
    restoreAccessToken {
      accessToken
    }
  }
`;

export async function getAccessToken() {
  try {
    const graphqlClient = new GraphQLClient(
      "https://backend06.codebootcamp.co.kr/graphql",
      { credentials: "include" }
    );

    // axios로 하는 방법
    // axios.post("http://backend06.codebootcamp.co.kr/graphql",{
    //     query:`
    //     mutation restoreAccessToken {
    //         restoreAccessToken {
    //           accessToken
    //         }
    //       }
    //     `
    // })

    const result = await graphqlClient.request(RESTORE_ACCESS_TOKEN);
    // result.data. 할 필요 없음!
    const newAccessToken = result.restoreAccessToken.accessToken;
    return newAccessToken;
  } catch (error) {
    console.log(error.message);
  }
}
```
