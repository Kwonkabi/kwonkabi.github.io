---
title: "리페치쿼리 사용해보기!"
excerpt: "for문을 대체할 자 누구인가"

categories: react
tags: [react, graphQL, refetchQueries]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

앞서 공부한 map 메서드를 활용해서 화면에 게시글 목록을 뿌려주었다. 그런데 삭제 뮤테이션을 날려도 삭제 내용이 반영되지 않는다. 새로고침을 해야만 다시 페치된다.

<br>

이 문제를 해결하기 위해 다시 가져와달라는 요청인 `refetchQueries`를 사용하였다.

<br>

> `onClickDelete` 함수가 실행되고, 어떤 게시글을 삭제할지 `variables`에 담아 보낸 후, `refetchQueries`를 통해 갱신된 목록을 페치해온다.

```js
import { useQuery, gql, useMutation } from "@apollo/client";
import styled from "@emotion/styled";
import { Fragment } from "react";

const FETCH_BOARDS = gql`
  query fetchBoards {
    fetchBoards {
      number
      writer
      title
      contents
    }
  }
`;

const DELETE_BOARD = gql`
  mutation deleteBoard($number: Int) {
    deleteBoard(number: $number) {
      message
    }
  }
`;

const Row = styled.div`
  display: flex;
  flex-direction: row;
`;

const Column = styled.div`
  width: 20%;
`;

const MapBoardPage = () => {
  const [deleteBoard] = useMutation(DELETE_BOARD);
  const { data } = useQuery(FETCH_BOARDS);

  const onClickDelete = (event) => {
    deleteBoard({
      variables: { number: Number(event.target.id) }, // 어떤 게시글을 삭제할지
      refetchQueries: [{ query: FETCH_BOARDS }],
      // 새로고침 해야지만 다시 페치해주는 문제 해결하기 위해, 다시 가져와달라는 요청을 하는 것
    });
  };

  return (
    <Fragment>
      {data?.fetchBoards.map((el) => (
        <Row key={el.number}>
          {/* 인덱스가 키에 들어가면 안 됨!! 완전 고유한 내용을 넣어야 함. 인덱스는 고정이고 계속 새로운 내용이 들어오기 때문. */}
          <Column>
            <input type="checkbox" />
          </Column>
          <Column>{el.number}</Column>
          <Column>{el.title}</Column>
          <Column>{el.writer}</Column>
          <Column>
            <button id={el.number} onClick={onClickDelete}>
              삭제
            </button>
            {/* 이벤트핸들러 함수 */}
          </Column>
        </Row>
      ))}
    </Fragment>
  );
};

export default MapBoardPage;
```
