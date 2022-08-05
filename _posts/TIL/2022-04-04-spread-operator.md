---
title: "스프레드 연산자로 리팩토링하기!!!"
excerpt: "스프레드 연산자를 활용해서 코드량을 줄여보자~"

categories: javascript
tags: [javascript, spread-operator, refactoring]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 기존 코드

```js
import { useState } from "react";
import { useMutation, gql } from "@apollo/client";

const CREATE_BOARD = gql`
  mutation createBoard($writer: String, $title: String, $contents: String) {
    createBoard(writer: $writer, title: $title, contents: $contents) {
      _id
      number
      message
    }
  }
`;

export default function GraphqlMutationPage() {
  const [myWriter, setMyWriter] = useState("");
  const [myTitle, setMyTitle] = useState("");
  const [myContents, setMyContents] = useState("");

  const [callApi] = useMutation(CREATE_BOARD);

  const callGraphqlApi = async () => {
    const result = await callApi({
      variables: { writer: myWriter, title: myTitle, contents: myContents },
    });
    console.log(result);
    console.log(result.data.createBoard.message);
  };

  const onChangeWriter = (event) => {
    setMyWriter(event.target.value);
  };

  const onChangeTitle = (event) => {
    setMyTitle(event.target.value);
  };

  const onChangeContents = (event) => {
    setMyContents(event.target.value);
  };

  return (
    <div>
      작성자: <input type="text" onChange={onChangeWriter}></input>
      <br />
      제목: <input type="text" onChange={onChangeTitle}></input>
      <br />
      내용: <input type="text" onChange={onChangeContents}></input>
      <br />
      <button onClick={callGraphqlApi}>GRAPHQL-API 요청하기!</button>
    </div>
  );
}
```

<br>

---

<br>

### 2. 여러 개의 스테이트를 객체로 묶어주기

```js
import { useState } from "react";
import { useMutation, gql } from "@apollo/client";

const CREATE_BOARD = gql`
  mutation createBoard($writer: String, $title: String, $contents: String) {
    createBoard(writer: $writer, title: $title, contents: $contents) {
      _id
      number
      message
    }
  }
`;

export default function GraphqlMutationPage() {
  const [inputs, setInputs] = useState({
    writer: "",
    title: "",
    contents: "",
  });

  const [callApi] = useMutation(CREATE_BOARD);

  const callGraphqlApi = async () => {
    const result = await callApi({
      variables: {
        // writer: inputs.writer,
        // title: inputs.title,
        // contents: inputs.contents,
        ...inputs,
      },
    });
    console.log(result);
    console.log(result.data.createBoard.message);
  };

  const onChangeWriter = (event) => {
    setInputs({
      writer: event.target.value,
      title: inputs.title,
      contents: inputs.contents,
    });
  };

  const onChangeTitle = (event) => {
    setInputs({
      writer: inputs.writer,
      title: event.target.value,
      contents: inputs.contents,
    });
  };

  const onChangeContents = (event) => {
    setInputs({
      writer: inputs.writer,
      title: inputs.title,
      contents: event.target.value,
    });
  };

  return (
    <div>
      작성자: <input type="text" onChange={onChangeWriter}></input>
      <br />
      제목: <input type="text" onChange={onChangeTitle}></input>
      <br />
      내용: <input type="text" onChange={onChangeContents}></input>
      <br />
      <button onClick={callGraphqlApi}>GRAPHQL-API 요청하기!</button>
    </div>
  );
}
```

그런데 오히려 코드가 더 길어졌다!!??

<br>

---

<br>

### 3. 객체에서 동일한 키에 값이 중복으로 할당되면 최근 값이 적용되는(덮어쓰는) 원리 활용

```js
import { useState } from "react";
import { useMutation, gql } from "@apollo/client";

const CREATE_BOARD = gql`
  mutation createBoard($writer: String, $title: String, $contents: String) {
    createBoard(writer: $writer, title: $title, contents: $contents) {
      _id
      number
      message
    }
  }
`;

export default function GraphqlMutationPage() {
  const [inputs, setInputs] = useState({
    writer: "",
    title: "",
    contents: "",
  });

  const [callApi] = useMutation(CREATE_BOARD);

  const callGraphqlApi = async () => {
    const result = await callApi({
      variables: {
        ...inputs,
      },
    });
    console.log(result);
    console.log(result.data.createBoard.message);
  };

  const onChangeWriter = (event) => {
    setInputs({
      ...inputs,
      writer: event.target.value,
    });
  };

  const onChangeTitle = (event) => {
    setInputs({
      ...inputs,
      title: event.target.value,
    });
  };

  const onChangeContents = (event) => {
    setInputs({
      ...inputs,
      contents: event.target.value,
    });
  };

  return (
    <div>
      작성자: <input type="text" onChange={onChangeWriter}></input>
      <br />
      제목: <input type="text" onChange={onChangeTitle}></input>
      <br />
      내용: <input type="text" onChange={onChangeContents}></input>
      <br />
      <button onClick={callGraphqlApi}>GRAPHQL-API 요청하기!</button>
    </div>
  );
}
```

그래도 여전히 처음보다 길다.

<br>

---

<br>

### 4. id 어트리뷰트를 활용해 onChange 함수들 내부를 동일하게 만들어주기

```js
import { useState } from "react";
import { useMutation, gql } from "@apollo/client";

const CREATE_BOARD = gql`
  mutation createBoard($writer: String, $title: String, $contents: String) {
    createBoard(writer: $writer, title: $title, contents: $contents) {
      _id
      number
      message
    }
  }
`;

export default function GraphqlMutationPage() {
  const [inputs, setInputs] = useState({
    writer: "",
    title: "",
    contents: "",
  });

  const [callApi] = useMutation(CREATE_BOARD);

  const callGraphqlApi = async () => {
    const result = await callApi({
      variables: {
        ...inputs,
      },
    });
    console.log(result);
    console.log(result.data.createBoard.message);
  };

  const onChangeWriter = (event) => {
    setInputs({
      ...inputs,
      [event.target.id]: event.target.value,
    });
  };

  const onChangeTitle = (event) => {
    setInputs({
      ...inputs,
      [event.target.id]: event.target.value,
    });
  };

  const onChangeContents = (event) => {
    setInputs({
      ...inputs,
      [event.target.id]: event.target.value,
    });
  };

  return (
    <div>
      작성자: <input type="text" id="writer" onChange={onChangeWriter}></input>
      <br />
      제목: <input type="text" id="title" onChange={onChangeTitle}></input>
      <br />
      내용: <input
        type="text"
        id="contents"
        onChange={onChangeContents}
      ></input>
      <br />
      <button onClick={callGraphqlApi}>GRAPHQL-API 요청하기!</button>
    </div>
  );
}
```

event.target.id는 문자열이 아니므로 그 상태로는 프로퍼티 키가 될 수 없다. [event.target.id]로 만들어준다.

<br>

---

<br>

### 5. onChange 함수들을 하나로 합쳐주기

```js
import { useState } from "react";
import { useMutation, gql } from "@apollo/client";

const CREATE_BOARD = gql`
  mutation createBoard($writer: String, $title: String, $contents: String) {
    createBoard(writer: $writer, title: $title, contents: $contents) {
      _id
      number
      message
    }
  }
`;

export default function GraphqlMutationPage() {
  const [inputs, setInputs] = useState({
    writer: "",
    title: "",
    contents: "",
  });

  const [callApi] = useMutation(CREATE_BOARD);

  const callGraphqlApi = async () => {
    const result = await callApi({
      variables: { ...inputs },
    });
    console.log(result);
    console.log(result.data.createBoard.message);
  };

  const onChangeInputs = (event) => {
    setInputs({
      ...inputs,
      [event.target.id]: event.target.value,
    });
  };

  return (
    <div>
      작성자: <input type="text" id="writer" onChange={onChangeInputs}></input>
      <br />
      제목: <input type="text" id="title" onChange={onChangeInputs}></input>
      <br />
      내용: <input type="text" id="contents" onChange={onChangeInputs}></input>
      <br />
      <button onClick={callGraphqlApi}>GRAPHQL-API 요청하기!</button>
    </div>
  );
}
```

input의 개수가 많아질수록 리팩토링의 효과는 커진다!!
