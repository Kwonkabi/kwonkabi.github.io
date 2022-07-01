---
title: "페이지네이션 vs. 무한 스크롤"
excerpt: "페이지네이션과 무한 스크롤의 차이를 알아보고, 라이브러리도 사용해보자!"

categories: coding
tags: [react, pagination, infinite-scroller, ]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 일반적인 방식의 페이지 처리

페이지 처리를 하는 방법에는 일반적인 방식과, 페이스북이나 인스타그램, 유튜브처럼 끝없이 내려서 보여주는 무한 스크롤 방식, 두 가지 방법이 있다. 일반적인 페이지 방식을 코드로 보면 다음과 같다.

```javascript
// ...

// 시작 페이지값을 알려줄 state
const [startPage, setStartpage] = useState(1); // 초기 값(1 페이지)

// 페이지 네비게이션을 눌렀을 때 화면을 해당 페이지 값으로 다시 구현해줄 함수
const onClickPage = (event) => {
  if (event.target instanceof Element){
    refetch({ page: Number(event.target.id) });
  }
};

// 페이지 내비게이션 이전 목록
const onClickPrevPage = () => {
  // 0페이지 이하로 내려가지 않도록
  if (startPage === 1) return;
  setStartPage((prev) => prev - 10);
};

// 페이지 내비게이션 다음 목록
const onClickNextPage = () => {
  // ex) 1-10 페이지 목록 이후 11-20 페이지 목록 나오게, 마지막 페이지 이후로는 계속 넘어가지 않도록
  if (startPage + 10 > lastPage) return;
  setStartPage((prev) => prev  + 10);
};
```

```javascript
// ...

// map을 사용해 데이터를 뿌려줄 리스트 생성
<div>
  {data?.fetchBoards.map((e) => (
    <div key={e._id}>
      {e.title} {e.writer}
    </div>
  ))}
</div>

// 페이지 이동을 위한 내비게이션 생성
<span onClick={onClickPrevPage}>{`<`}</span>
  {new Array(10).fill(1).map((_, i) => startPage + i <= lastPage && (
    <span
      key={startPage + i}
      onClick={onClickPage}
      id={String(startPage + i)}
      style={{ margin: "10px", cursor: "pointer" }}
    >
      {startPage + i}
    </span>
  ))}
<span onClick={onClickNextPage}>{`>`}</span>
```


### 2. 무한 스크롤 방식 페이지 처리

무한 스크롤 방식은 아래로 스크롤할 때, 계속해서 페이지가 추가되어 보여주는 방식의 페이지 처리 방법이다. 무한 스크롤 라이브러리를 nmpjs.com에 검색해보면, **React Inifinte Scroller**와 **react-infinite-scroll-component** 이 두 가지가 가장 인기가 많다. (나는 전자를 사용했당!)

[npmjs.com: **React Inifinte Scroller**](https://www.npmjs.com/package/react-infinite-scroller)
[npmjs.com: **react-infinite-scroll-component**](https://www.npmjs.com/package/react-infinite-scroll-component)

```javascript
function onLoadMore(){
  if (!data) return;

  fetchMore({
    variable: { page: Math.ceil(data?.fetchBoards.length / 10) + 1 },
    updateQuery: (prev, { fetchMoreResult }) => {
      if (!fetchMoreResult?.fetchBoards)
        return { fetchBoards: [...prev.fetchBoards] };

      return {
        fetchBoards: [...prev.fetchBoards, ...fetchMoreResult?.fetchBoards],
      }
    };
  };)
}
```

```javascript
<InfiniteScroll
  pageStart={0}
  loadMore={onLoadMore}
  hasMore={true || false}
  loader={<div className="loader" key={0}>Loading ...</div>}
>
{data?.fetchBoards.map((el) => (
  <div>
    <span>{el.writer}</span>
    <span>{el.title}</span>
    <span>{el.contents}</span>
  </div>
))}
</InfiniteScroll>
```


InfiniteScroll 태그를 사용해서 감싸주어야 사용이 가능하다. 스크롤이 일정 하단 부분에 닿았을 때 실행되는 기능인 onLoadMore를 loadMore에 지정해준 코드이다.