---
title: "react-hook-form과 yup"
excerpt: "react-hook-form과 yup을 사용해서 폼을 관리하고 유효성 검사까지 끝내버리자!"

categories: react
tags: [react, react-hook-form, yup]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

input태그를 사용하여 간단한 입력창 세 개와 등록 버튼을 만들어 실제로 등록까지 하는 기능을 다음과 같이 구현할 수 있다.

### input 태그와 onClick

```js
import { useState } from "react";
import { useMutation, gql } from "@apollo/client";
import { useRouter } from "next/router";

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
  const router = useRouter();

  const [writer, setWriter] = useState("");
  const [title, setTitle] = useState("");
  const [contents, setContents] = useState("");

  const [createBoard] = useMutation(CREATE_BOARD);

  const onClickSubmit = async () => {
    try {
      const result = await createBoard({
        variables: { writer, title, contents },
      });
      console.log(result.data.createBoard.number);
      alert("게시글 등록에 성공했어요!");
      alert("상세페이지로 이동해 볼까요?");
      router.push(
        `/05-08-dynamic-routed-input/${result.data.createBoard.number}`
      );
    } catch (error) {
      alert(error.message);
    }
  };

  const onChangeWriter = (event) => {
    setWriter(event.target.value);
  };

  const onChangeTitle = (event) => {
    setTitle(event.target.value);
  };

  const onChangeContents = (event) => {
    setContents(event.target.value);
  };

  return (
    <div>
      작성자: <input type="text" onChange={onChangeWriter}></input>
      <br />
      제목: <input type="text" onChange={onChangeTitle}></input>
      <br />
      내용: <input type="text" onChange={onChangeContents}></input>
      <br />
      <button onClick={onClickSubmit}>등록하기</button>
    </div>
  );
}
```

<br>

### 스프레드 연산자를 활용하여 리팩토링

중복되는 내용을 스프레드 연산자를 활용해서 줄여보았다. input의 종류가 많아질수록 리팩토링의 효과가 커진다.

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

  const [createBoard] = useMutation(CREATE_BOARD);

  const onClickSubmit = async () => {
    const result = await createBoard({
      variables: { ...inputs },
    });
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
      <button onClick={onClickSubmit}>등록하기</button>
    </div>
  );
}
```

<br>

### react-hook-form 사용하기

그동안 폼태그와 onChange함수, state를 활용해서 폼을 관리했던 것들을 미리 만들어놓고 라이브러리 형태로 제공해주는 폼 라이브러리가 많이 쓰인다. react-form, redux-form, formik 등이 대표적인 폼 라이브러리이다. 클래스 컴포넌트를 사용하던 시절에는 formik이 대세였으나, 최근 함수형 컴포넌트에서는 react-hook-form이 가장 많이 쓰인다. <br>

react-hook-form은 비제어 컴포넌트 방식으로, 변경 사항에 대해 100% 정확도를 보장할 수는 없다. 왜냐하면 제어 컴포넌트는 변경될 때마다 다시 렌더링 되기 때문에 속도가 느리면서 정확도가 높은 반면, 비제어 컴포넌트 방식은 입력할 때마다 다시 렌더되지 않기 때문이다. 그대신 속도가 빠르다는 장점이 있다. <br>

react-hook-form을 사용한 작성 방법은 이렇다:

- 입력값을 받을 Input태그들을 form이라는 태그로 감싸준다.
- useForm()으로 state를 등록하는데 필요한 모든 기능(onChange,onClick, onError 등)이 담겨있는 register를 가져와 input에 스프레드 시켜준다.
- 그냥 스프레드만 시켜주는 것이 아니고 register(“”)안에 input의 이름(구분)도 넣어준다.
- 이렇게 담긴 값들 혹은 Error등 은 formState에 담긴다.
- form태그에는 onSubmit()이라는 함수를 연결 시켜주고, 이렇게 감싸준 form 태그 내부에 button을 넣어준다!

```js
import { useState } from "react";
import { useMutation, gql } from "@apollo/client";
import { useForm } from "react-hook-form";

const CREATE_BOARD = gql`
  mutation createBoard($writer: String, $title: String, $contents: String) {
    createBoard(writer: $writer, title: $title, contents: $contents) {
      _id
      number
      message
    }
  }
`;

export default function ReactHookFormPage() {
  const { register, handleSubmit, formState } = useForm();

  // 버튼 중복 클릭 방지 (formState.isSubmitting이 true면 버튼이 disabled되도록)
  formState.isSubmitting;

  const [createBoard] = useMutation(CREATE_BOARD);

  const onClickSubmit = async (data) => {
    await createBoard({
      variables: { ...data }
    })
  };

  return (
    <form>
      작성자: <input type="text" form onSubmit={handleSubmit(onClickSubmit)}>
      {...register("writer")} />
      제목: <input type="text" {...register("title")} />
      내용: <input type="text" {...register("contents")} />
      <button disabled={formState.isSubmitting}>등록하기</button>
    </form>
  );
}
```

<br>

### yup과 함께 사용하기

yup과 함께 사용함으로써 정규표현식을 사용한 유효성 검사를 생략할 수 있다.

```js
import { useMutation, gql } from "@apollo/client";
import { useForm } from "react-hook-form";
import { yupResolver } from "@hookform/resolvers/yup";
import * as yup from "yup";
import styled from "@emotion/styled";

const CREATE_BOARD = gql`
  mutation createBoard($writer: String, $title: String, $contents: String) {
    createBoard(writer: $writer, title: $title, contents: $contents) {
      _id
      number
      message
    }
  }
`;

const Error = styled.div`
  color: red;
  font-size: 15px;
`;

const LoginButton = styled.button`
  background-color: ${(props) => (props.isActive ? "yellow" : "")};
`;

const schema = yup.object({
  email: yup
    .string()
    .email("이메일 형식이 적합하지 않습니다.")
    .required("이메일은 필수 입력 사항입니다."),
  password: yup
    .string()
    .min(4, "비밀번호는 최소 4자리 이상 입력해 주세요.")
    .max(15, "비밀번호는 최대 15자리로 입력해 주세요.")
    .required("비밀번호는 필수 입력 사항입니다."),
});

export default function ReactHookFormPage() {
  const { register, handleSubmit, formState } = useForm({
    resolver: yupResolver(schema), // '만든 스키마를 검증하겠다'
    mode: "onChange", // 변경되자마자 검증 시작 (페이지에서 일단 로그인버튼 눌러야만 검증 시작되는 문제 해결)
  });

  const [createBoard] = useMutation(CREATE_BOARD);

  const onClickSubmit = async (data) => {
    await createBoard({
      variables: { ...data },
    });
  };

  return (
    <form onSubmit={handleSubmit(onClickSubmit)}>
      이메일: <input type="text" {...register("email")} />
      <Error>{formState.errors.email?.message}</Error>
      비밀번호: <input type="text" {...register("password")} />
      <Error>{formState.errors.password?.message}</Error>
      <LoginButton isActive={formState.isValid}>로그인</LoginButton>
    </form>
  );
}
```
