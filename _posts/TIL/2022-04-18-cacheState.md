---
title: "refetchQueries 대신 cacheState를 직접 수정하기"
excerpt: "굳이 전체 데이터를 다 리페치할 필요가 있을까?"

categories: react
tags: [react, cacheState]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

기존에 refetchQueries를 사용해서 변경된 데이터를 다시 페치해왔었다. 하지만 게시물이나 상품 목록과 같은 많은 데이터를 담은 부분에 수정이나 삭제가 발생하여 refetch를 요청했을때 수정/삭제가 발생한 부분만이 아니라 **전체 데이터를 refetch하기 때문에 불필요한 네트워크 비용을 소모한다는 문제**가 있었다. <br>

이 문제를 Apollo의 데이터가 저장되는 **cacheState를 직접 수정**함으로써 좀 더 효율적인 방향으로 해결할 수 있다.

```js
import { gql, useMutation, useQuery } from "@apollo/client";

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

const DELETE_BOARD = gql`
  mutation deleteBoard($boardId: ID!) {
    deleteBoard(boardId: $boardId)
  }
`;

const CREATE_BOARD = gql`
  mutation createBoard($createBoardInput: CreateBoardInput!) {
    createBoard(createBoardInput: $createBoardInput) {
      _id
      writer
      title
      contents
    }
  }
`;

export default function ApolloCacheStatePage() {
  const [deleteBoard] = useMutation(DELETE_BOARD);
  const [createBoard] = useMutation(CREATE_BOARD);
  const { data } = useQuery(FETCH_BOARDS);

  const onClickDelete = (boardId: string) => async () => {
    await deleteBoard({
      variables: { boardId },
      update(cache, { data }) {
        // 리페치쿼리 안 쓰기: delete 후 useQuery의 결과 캐시에서 꺼내오기 (삭제된 게시글의 id)
        const deletedId = data.deleteBoard;
        cache.modify({
          fields: {
            // 수정할 필드
            fetchBoards: (prev, { readField }) => {
              const filteredPrev = prev.filter(
                (el) => readField("_id", el) !== deletedId
              );
              // 이  라이브러리에서 el._id가 안 되므로 readField에서 꺼내줘야 함
              return [...filteredPrev]; // 리턴한 내용으로 업데이트 됨
            },
          },
        });
      },
    });
  };

  const onClickSubmit = async () => {
    // 등록하기 로직
    await createBoard({
      variables: {
        createBoardInput: {
          writer: "영희",
          password: "1234",
          title: "제목입니다~",
          contents: "내용입니다@@@",
        },
      },
      update(cache, { data }) {
        // 리페치쿼리 안 쓰기: create 후 useQuery의 결과 캐시에서 꺼내오기 (인풋 객체)
        cache.modify({
          fields: {
            // 수정할 필드
            fetchBoards: (prev) => {
              return [data.createBoard, ...prev]; // 리턴한 내용으로 업데이트 됨: [지금 등록한 글, ...이전 글들(가장 최신글, 그 다음글, ...)]
            },
          },
        });
      },
    });
  };

  return (
    <div>
      {data?.fetchBoards.map((el) => (
        <div key={el._id}>
          <span>{el.writer}</span>
          <span>{el.title}</span>
          <span>{el.contents}</span>
          <button onClick={onClickDelete(el._id)}>삭제하기</button>
        </div>
      ))}
      <button onClick={onClickSubmit}>등록하기</button>
    </div>
  );
}
```

- 우선 `update(cache,{data}){}`를 통해 기존 데이터가 저장되어있는 cache와 쿼리 실행 이후 받은 데이터가 담겨있는 data를 합쳐준다.

- `cache.modify({})`의 안에는 수정할 fields를 작성해주고, 그 안에 우리가 요청하는 쿼리의 이름을 key로 담아준다. (cacheState에 field라는 형태로 저장되어 각각 fetchBoards와 같은 키를 갖기 때문이다.)

- 값으로는 실행할 내용을 작성해주는데, 그 함수에서 리턴한 내용으로 필드가 업데이트된다. 즉, 작성해준 field를 우리가 직접 새로운 데이터와 기존 데이터를 함께 수정해 최신화 시켜 보여주는 것이다.

- 이때 주의할 점은, filter()를 사용할 때 el.\_id를 사용할 수 없었다는 것이다. 따라서 el에 대한 값을 읽어주기 위해 readField를 사용해주어야 한다. `readField("_id", el)`는 readField로 el에서 \_id를 꺼내라는 뜻이다.

- 네트워크 탭을 함께 열어놓고 보면, 추가적인 refetch api 요청이 백엔드로 가지 않는 것을 확인할 수 있다!

<br>

이 방법은 refetchQueries보다 효율적이지만, 페이지네이션으로 목록을 보여줘야 할 때는 어쨌든 정해진 개수만큼 보여줘야 하기 때문에 리페치를 하는 것이 좋다. 캐시를 직접 수정하는 위의 방법은 **무한스크롤**로 페이지 처리를 할 때 사용하면 좋다.
