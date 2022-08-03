---
title: "이벤트 버블링"
excerpt: "target과 currentTarget의 차이를 알고 사용해보자!"

categories: javascript
tags: [javascript, event-bubbling, currentTarget]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 이벤트 버블링

> 이벤트 버블링은 특정 화면 요소에서 이벤트가 발생했을 때 해당 이벤트가 더 상위의 화면 요소들로 전달되어 가는 특성을 의미한다.

<br>

예를 들어, `FORM > DIV > P 형태로 중첩된 구조`를 살펴보자.

```html
<form onclick="alert('form')">
  FORM
  <div onclick="alert('div')">
    DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>
```

<br>

가장 안쪽의 `<p>`를 클릭하면 순서대로 다음과 같은 일이 벌어진다.<br>

1. `<p>`에 할당된 onclick 핸들러가 동작한다.
2. 바깥의 `<div>`에 할당된 핸들러가 동작한다.
3. 그 바깥의 `<form>`에 할당된 핸들러가 동작한다.
4. document 객체를 만날 때까지, 각 요소에 할당된 onclick 핸들러가 동작한다.<br>

=> 이러한 동작 방식 때문에 `<p>`요소를 클릭하면 `p → div → form` 순서로 3개의 얼럿 창이 뜨게 된다.<br>

이벤트가 제일 깊은 곳에 있는 요소에서 시작해 부모 요소를 거슬러 올라가며 발생하는 모양이 마치 물속 거품(bubble)과 닮았기 때문에 이러한 흐름을 **'이벤트 버블링’**이라 부른다.

<br>

#### target과 currentTarget

게시판 게시글 목록을 만들다가.. 글 제목 칸 어디를 눌러도 그 글의 상세페이지로 이동하게 하고 싶은데, 제목 텍스트를 요리조리 피해서 눌러야만 이동했다.

그 이유는 내가 이벤트 버블링도 모르고 currentTarget도 몰랐기 때문..! <br>

위와 비슷한 예시로 다시 알아보자. <br>

P 요소를 클릭했을 때, 가장 바깥 쪽의 form 태그의 id를 alert창으로 보여주고 싶다고 하자.

```html
<form id="form" onclick="alert(event.target.id)">
  FORM
  <div id="form">
    DIV
    <p id="form">P</p>
  </div>
</form>
```

<br>
이렇게 하면 되긴 된다. 그치만 아무리 봐도 옳게 된 코드가 아니다. id는 말 그대로 고유한 것인데 에러가 안 난다고 여러 개를 쓸 순 없다.. 그리고 복붙을 하고 있는 나를 발견하는 순간 그건 잘못된 거라는 시그널이다.

<br>

이럴 때는 **event.currentTarget**을 사용하면 된다. target과 currentTarget의 차이는 다음과 같다. <br>

> event.target은 실제 이벤트가 시작된 ‘타깃’ 요소다. 버블링이 진행되어도 변하지 않는다.
> event.currentTarget은 ‘현재’ 요소로, 현재 실행 중인 핸들러가 할당된 요소를 참조한다.

```html
<form id="form" onclick="alert(event.currentTarget.id)">
  FORM
  <div>
    DIV
    <p>P</p>
  </div>
</form>
```

P를 눌러도, DIV를 눌러도 alert창에는 form이 뜬다.

<br>

내 게시글 목록으로 돌아와보자.

**container 컴포넌트**

```js
const onClickMoveToBoardDetail = (event: MouseEvent<HTMLDivElement>) => {
  if (event.target instanceof Element)
    router.push(`/boards/${event.currentTarget.id}`);
};
```

<br>

**presenter 컴포넌트**

```js
{
  props.data?.fetchBoards.map((el) => (
    <S.Row key={el._id}>
      <S.ColumnBasic>{String(el._id).slice(-4).toUpperCase()}</S.ColumnBasic>
      <S.ColumnTitle id={el._id} onClick={props.onClickMoveToBoardDetail}>
        {el.title
          .replaceAll(props.keyword, `@#$%${props.keyword}@#$%`)
          .split("@#$%")
          .map((el) => (
            <S.TextToken key={uuidv4()} isMatched={props.keyword === el}>
              {el}
            </S.TextToken>
          ))}
      </S.ColumnTitle>
      <S.ColumnBasic>{el.writer}</S.ColumnBasic>
      <S.ColumnBasic>{getDate(el.createdAt)}</S.ColumnBasic>
    </S.Row>
  ));
}
```

<br>

이벤트 버블링 때문에 가장 바깥 div 태그인 `S.ColumnTitle`태그에만 onClick 함수 `onClickMoveToBoardDetail`를 달아주었다.

`S.ColumnTitle`태그 안쪽 어디가 클릭되더라도 `onClickMoveToBoardDetail` 함수가 실행되어 현재 실행 중인 핸들러가 할당된 요소를 참조한 currentTarget(S.ColumnTitle)의 id로 라우팅된다!!
