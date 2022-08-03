---
title: "무한 스크롤"
excerpt: "페이지 처리의 또 다른 방식인 무한 스크롤을 사용해보자!"

categories: react
tags: [react, infinite-scroller]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 무한 스크롤

무한 스크롤 방식은 아래로 스크롤할 때, 계속해서 페이지가 추가되어 보여주는 방식의 페이지 처리 방법이다. 무한 스크롤 라이브러리를 nmpjs.com에 검색해보면, **React Inifinte Scroller**와 **react-infinite-scroll-component** 이 두 가지가 가장 인기가 많다. (나는 전자를 사용했다!)

<br>

[npmjs.com: **React Inifinte Scroller**](https://www.npmjs.com/package/react-infinite-scroller)
[npmjs.com: **react-infinite-scroll-component**](https://www.npmjs.com/package/react-infinite-scroll-component)

<br>

```javascript
import { useQuery, gql } from "@apollo/client";
import styled from "@emotion/styled";
import InfiniteScroll from "react-infinite-scroller";

const FETCH_BOARDS = gql`
  query fetchBoards($page: Int) {
    fetchBoards(page: $page) {
      # variables로 받은 페이지
      _id
      writer
      title
      contents
    }
  }
`;

const MyRow = styled.div`
  display: flex;
`;

const MyColumn = styled.div`
  width: 25%;
`;

export default function MapBoardPage() {
  const { data, fetchMore } = useQuery(FETCH_BOARDS);

  const onLoadMore = () => {
    if (!data) return;
    // 데이터가 없다면 스크롤이 실행될 필요 없어! 더 많이 요청할 필요도 없어!
    // (data가 undefined일 때도 <InfiniteScroll></InfiniteScroll>은 있기 때문에)

    fetchMore({
      // 기존에 10개가 있는데 추가로 페치해줌, 리페치 아님!
      variables: { page: Math.ceil(data.fetchBoards.length / 10) + 1 },
      // 어떤 페이지를 페치할 건데? ($page 내용) : 받아온 데이터.페치보드의 객체 개수를 10으로 나눠서 올려주기(현재 몇 페이지까지 받았는지?) + 1(그 다음페이지를 추가로 요청할 것이기 때무네~)
      updateQuery: (prev, { fetchMoreResult }) => {
        // 받아온 useQuery를 수정하는 부분 (useQuery로 요청한 거, {추가로 요청한 거})

        // 만약에 추가로 요청했는데 그 페이지가 0개일 경우! 그대로 받아온다!
        if (!fetchMoreResult.fetchBoards)
          return { fetchBoards: [...prev.fetchBoards] };

        return {
          fetchBoards: [...prev.fetchBoards, ...fetchMoreResult.fetchBoards],
        };
        // 리턴: "페치보드를 업데이트 해줘!"
        // 이전에 받은 10개랑 추가로 받은 개수 보여주면 됨
        // {기존에 fetchBoards한 거를 [...기존꺼 10*n개, ...추가 10개]로 바꿔줘}
      },
    });
  };

  return (
    // <div style={{ height: "700px", overflow: "auto" }}>
    // useWindow={false}일 때 (내부 스크롤 사용하고 싶을 때) 700px 넘어가면 스크롤이 생긴다는 뜻
    <InfiniteScroll
      pageStart={0} //
      loadMore={onLoadMore} // 더 내렸을 때 있으면 이 함수를 실행시켜 주겠다
      hasMore={true} // 더 있는지 없는지
      // loader를 추가하면 로딩되는 동안 보여줄 내용 넣을 수 있음
      // useWindow={false} -> true면 윈도우바디에 있는 스크롤을 사용하겠다는 뜻
    >
      {data?.fetchBoards.map((el) => (
        <MyRow key={el._id}>
          <MyColumn>{el._id}</MyColumn>
          <MyColumn>{el.writer}</MyColumn>
          <MyColumn>{el.title}</MyColumn>
        </MyRow>
      )) || <></>}
      {/* 자식이 없으면 콘솔창에 에러가 뜨기 때문에 넣어준 fragment */}
    </InfiniteScroll>
    // </div>
  );
}
```
