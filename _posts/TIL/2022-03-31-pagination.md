---
title: "페이지네이션"
excerpt: "페이지네이션을 구현해보고, 스테이트 끌어올리기를 통해 공통 컴포넌트로 만들어보자!"

categories: react
tags: [react, pagination, state-lifting-up]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 페이지네이션

1. newArray()를 통해 페이지를 담을 배열을 새로 만든다. <br>

2. state와 setState를 활용해 이전 페이지 목록, 다음 페이지 목록을 만든다.

- 처음 페이지 처리 : 조건을 주어 1 아래로 내려가지 못하게 한다.
- 마지막 페이지 처리 : 게시글 총 개수에서 화면에 뿌려주는 게시글 개수를 나눠 반올림 처리 해주어 마지막 게시물 이후로 페이지가 더 생기지 않게 만든다. <br>

3. 데이터가 담기지 않는 페이지가 생기는 경우 조건부 렌더링을 사용한다.

<br>

```js
import { useQuery, gql } from "@apollo/client";
import styled from "@emotion/styled";
import { useState } from "react";

const FETCH_BOARDS = gql`
  query fetchBoards($page: Int) {
    fetchBoards(page: $page) {
      _id
      writer
      title
      contents
    }
  }
`;

const FETCH_BOARDS_COUNT = gql`
  query fetchBoardsCount {
    fetchBoardsCount
  }
`;

const Row = styled.div`
  display: flex;
  flex-direction: row;
`;

const Column = styled.div`
  width: 25%;
`;

export default function MapBoardPage() {
  const [startPage, setStartPage] = useState(1); // 기준이 되는 페이지
  const { data, refetch } = useQuery(FETCH_BOARDS);
  const { data: dataBoardsCount } = useQuery(FETCH_BOARDS_COUNT); // 위의 data와 이름이 같아서 바꿔준 거시다
  const lastPage = Math.ceil(dataBoardsCount?.fetchBoardsCount / 10);
  // fetchBoardsCount를 통해 알아낸 글의 총 개수를 10으로 나눠서 소숫점 첫째 자리를 올림한 것!!
  // 만약 글이 13개라면 2페이지가 필요하기 때문
  // (백엔드에서 받아오기 전까지 undefined이기 때문에 ?달아주기)

  const onClickPage = (event) => {
    refetch({ page: Number(event.target.id) });
  };

  const onClickPrevPage = () => {
    if (startPage === 1) return; // 현재 페이지 아니고 스타트 페이지인 것 주의!!
    setStartPage((prev) => prev - 10); // 페이지 숫자 바꾸기
    refetch({ page: startPage - 10 }); // 실제로 해당하는 페이지로 바꿔주기
  };

  const onClickNextPage = () => {
    if (startPage + 10 > lastPage) return; // startPage가 lastPage보다 크다면 다음 페이지 버튼을 사용할 수 없다
    // if (!(startPage + 10 <= lastPage)) return;
    setStartPage((prev) => prev + 10);
    refetch({ page: startPage + 10 }); // 실제로 해당하는 페이지로 바꿔주기
  };

  return (
    <>
      {data?.fetchBoards.map((el) => (
        <Row key={el._id}>
          <Column>{el.title}</Column>
          <Column>{el.writer}</Column>
        </Row>
      ))}
      <span onClick={onClickPrevPage}>이전페이지</span>
      {new Array(10).fill(1).map(
        // 엘리먼트가 10개인 임의의 배열 만들어주기
        (_, index) =>
          index + startPage <= lastPage && (
            // 현재 페이지가 마지막 페이지와 같거나 마지막 페이지보다 작을 때만 span 태그를 보여줘라
            // 함수는 10번 실행됨. 조건이 충족되지 않으면 undefined가 보여지는 것뿐.
            <span
              key={index + startPage}
              onClick={onClickPage}
              id={String(index + startPage)}
            >
              {` `} {index + startPage}
            </span>
          )
      )}
      <span onClick={onClickNextPage}>다음페이지</span>
    </>
  );
}
```

<br>

---

<br>

### 2. 스테이트 끌어올리기(state lifting up)를 통해 페이지네이션을 공통 컴포넌트로 만들기

페이지네이션 컴포넌트를 만들었는데 게시글 목록 페이지에서만 사용하는 것은 아깝다.

게시글 목록 페이지에서 목록을 보여주는 몸통 부분과 페이지네이션을 분리해서, 페이지네이션 부분을 다른 페이지에서도 재사용할 수 있게끔 만들어보자.

<br>

**페이지 컴포넌트**

```js
import { gql, useQuery } from "@apollo/client"; // Board와 Pagination에서 모두 쓰이기 때문에 끌어올려짐
import Board from "../../src/components/units/board/14-board-pagination/Board";
import Pagination from "../../src/components/units/board/14-board-pagination/Pagination";

// Board와 Pagination에서 모두 쓰이기 때문에 끌어올려짐
const FETCH_BOARDS = gql`
  query fetchBoards($page: Int) {
    fetchBoards(page: $page) {
      _id
      writer
      title
      contents
    }
  }
`;

// Pagination 컴포넌트 재사용이 목적이기 때문에 (Pagination 컴포넌트에 존재할 필요가 없어서) 끌어올려진 것뿐이지, 자식 컴포넌트들에서 공통으로 사용하기 위함이 아님!!
const FETCH_BOARDS_COUNT = gql`
  query fetchBoardsCount {
    fetchBoardsCount
  }
`;

export default function BoardListPage() {
  const { data, refetch } = useQuery(FETCH_BOARDS); // Board와 Pagination에서 모두 쓰이기 때문에 끌어올려짐

  // Pagination 컴포넌트에 존재할 필요가 없어서 끌어올려짐
  // 모든 페이지에서 공통으로 사용할 수 없는 내용은 끌어올려짐
  const { data: dataBoardsCount } = useQuery(FETCH_BOARDS_COUNT);
  const lastPage = Math.ceil(dataBoardsCount?.fetchBoardsCount / 10);

  return (
    <div>
      {/* 끌어올려준 뒤 props로 뿌려주기 */}
      <Board data={data} />
      <Pagination refetch={refetch} lastPage={lastPage} />
    </div>
  );
}
```

<br>

**Board 컴포넌트**

```js
import styled from "@emotion/styled";

const Row = styled.div`
  display: flex;
  flex-direction: row;
`;

const Column = styled.div`
  width: 25%;
`;

export default function Board(props) {
  return (
    <div>
      {/* data -> props.data 바뀜 */}
      {props.data?.fetchBoards.map((el) => (
        <Row key={el._id}>
          <Column>{el.title}</Column>
          <Column>{el.writer}</Column>
        </Row>
      ))};
    </div>
  );
}
```

<br>

**Pagination 컴포넌트**

```js
import { useState } from "react";

export default function Pagination(props) {
  const [startPage, setStartPage] = useState(1);

  const onClickPage = (event) => {
    props.refetch({ page: Number(event.target.id) }); // refetch -> props.refetch로 바뀜
  };

  const onClickPrevPage = () => {
    if (startPage === 1) return;
    setStartPage((prev) => prev - 10);
    props.refetch({ page: startPage - 10 }); // refetch -> props.refetch로 바뀜
  };

  const onClickNextPage = () => {
    if (startPage + 10 > props.lastPage) return; // lastPage -> props.lastPage로 바뀜
    // if (!(startPage + 10 <= lastPage)) return;
    setStartPage((prev) => prev + 10);
    props.refetch({ page: startPage + 10 }); // refetch -> props.refetch로 바뀜
  };

  return (
    <>
      <span onClick={onClickPrevPage}>이전페이지</span>
      {new Array(10).fill(1).map(
        (_, index) =>
          index + startPage <= props.lastPage && ( // lastPage -> props.lastPage로 바뀜
            <span
              key={index + startPage}
              onClick={onClickPage}
              id={String(index + startPage)}
            >
              {` `} {index + startPage}
            </span>
          )
      )}
      <span onClick={onClickNextPage}>다음페이지</span>
    </>
  );
}
```

<br>

---

<br>

### 3. 현재 페이지 숫자에 동적으로 스타일 입히기

![pagination](/assets/images/js/pagination.png)
