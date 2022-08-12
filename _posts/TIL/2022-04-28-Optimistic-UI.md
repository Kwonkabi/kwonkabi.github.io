---
title: "Optimistic-UI로 좋아요 카운트 올리기!"
excerpt: "눈속임을 해보자ㅋㅋ"

categories: react
tags: [react, optimistic-UI, optimisticResponse]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

> Optimistic-UI는 실제로 성능을 높여주는 것이 아니라 성능이 높아 빠른 처리를 하는 것처럼 보이도록 눈속임을 하는 것이다.

Optimistic-UI는 이름처럼 낙관적 UI다. 특정 API 요청을 보내기 전에 요청 결과가 실패하지 않을 것이라고 가정하고 요청이 성공한 것처럼 화면을 그려준 후 API 요청을 보내준 뒤, 결과가 돌아온 후 비교해서 다시 화면을 그려주는 것이다. <br>

Optimistic-UI는 ‘좋아요’ 기능과 같이 요청이 실패해도 크게 치명적이지 않고, 애초에 요청이 실패할 확률 자체가 적은 상황에서 사용하는 것이 좋다. 결제와 같이 안정성이 필요한 중요한 부분에는 사용하지 말아야 한다. <br>

비교적 느린 refetchQueries가 아닌 optimisticResponse를 사용해 미리 보여줄 데이터 값을 가지고 update()를 통해, cache state를 직접 수정하고, 요청이 완료되기 전 화면을 처리해줘 빠르게 눈속임 해주면 된다. <br>

```js
import { useMutation, useQuery, gql } from "@apollo/client";

const FETCH_BOARD = gql`
  query fetchBoard($boardId: ID!) {
    fetchBoard(boardId: $boardId) {
      _id
      likeCount
    }
  }
`;

const LIKE_BOARD = gql`
  mutation likeBoard($boardId: ID!) {
    likeBoard(boardId: $boardId)
  }
`;

export default function OptimisticUIPage() {
  const { data } = useQuery(FETCH_BOARD, {
    variables: { boardId: "6269ece4a8255b002988d633" },
  });

  const [likeBoard] = useMutation(LIKE_BOARD);

  const onClickOptimisticUI = () => {
    // 버튼 누르면 뮤테이션 실행
    likeBoard({
      variables: { boardId: "6269ece4a8255b002988d633" },

      // 방법 1. state 활용

      // 방법 2. 쿼리 두 번
      // refetchQueries: [
      //   {
      //     query: FETCH_BOARD,
      //     variables: { boardId: "6269ece4a8255b002988d633" }
      //   }
      // ]

      optimisticResponse: {
        // 실제 값이 들어오기 전에 낙관적으로 받겠다는 값
        likeBoard: (data?.fetchBoard.likeCount || 0) + 1, // 일단 data.likeBoard에는 이 값부터 들어가게 됨, 'undefined면 0 + 1  해줘~!'
      },
      // 3. 캐시(글로벌 스테이트)를 직접 수정 -> optimisticResponse와 같이 사용 가능
      // 백엔드랑 상관 없이 조작해주는 과정
      update(cache, { data }) {
        cache.writeQuery({
          // 기존에 FETCH_BOARD로 받아온 data를 직접 바꿔치기 하기
          query: FETCH_BOARD,
          variables: { boardId: "6269ece4a8255b002988d633" },
          data: {
            fetchBoard: {
              // 필수 입력 두 가지(_id, __typename): 글로벌 스테이트에 저장해둔 걸 구분하기 위한 용도. 나머지는 옵션.
              _id: "6269ece4a8255b002988d633",
              __typename: "Board",
              likeCount: data.likeBoard,
            },
          },
        });
      },
    });
  };

  return (
    <>
      <h1>Optimistic-UI</h1>
      <div>현재 좋아요 카운트: {data?.fetchBoard.likeCount}</div>
      <button onClick={onClickOptimisticUI}>좋아요 + 1</button>
    </>
  );
}
```
