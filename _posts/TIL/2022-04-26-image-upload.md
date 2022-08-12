---
title: "fileReader를 사용해서 스토리지 없이 이미지 미리보기 구현하기"
excerpt: "스토리지 찌꺼기를 만들지 말자!"

categories: react
tags: [react, fileReader, image upload, storage]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 스토리지 없이 이미지 미리보기 구현하기

Browser에서 이미지를 등록하면 백엔드에서 storage에 이미지를 올려 url을 받아 그 url을 Browser에게 반환해주는 방식으로 미리보기를 구현할 수 있다. 하지만 이 방식은 Storage 용량 낭비 문제가 있다. 사용자가 이 이미지를 실제로 등록하지 않고 이탈해도 스토리지에 찌꺼기가 쌓인다. 이를 해결할 수 있는 방법이 있다. <br>

> FileReader()를 활용해 아직 파일을 업로드(uploadFile)하지 않은 상태로 임시주소를 만들어서 이미지를 보여주는 것이다.

readAsDataURL()을 사용하면 그 컴퓨터에서만 사용 가능한 가짜 URL을 만들어 낼 수 있다. 뮤테이션 없이 미리보기를 구현할 수 있다.

```js
import { ChangeEvent, useState } from "react";

export default function ImageUploadPreviewPage() {
  const [imageUrl, setImageUrl] = useState("");

  const onChangeFile = (event: ChangeEvent<HTMLInputElement>) => {
    const file = event.target.files?.[0];
    if (!file) {
      alert("파일이 없습니다!");
      return;
    }

    // 미리보기로 빠르게 보여주기 (스토리지를 차지하지 않음)
    const fileReader = new FileReader();
    fileReader.readAsDataURL(file); // blob(binary large object) 파일을 읽어서 임시 url 형태로 만든다
    fileReader.onload = (data) => {
      if (typeof data.target?.result === "string") {
        console.log(data.target?.result);
        setImageUrl(data.target?.result);
      }
    };
  };

  return (
    <div>
      <input type="file" onChange={onChangeFile} />
      <img src={imageUrl} />
    </div>
  );
}
```

<br>

하지만 실제로 게시물을 등록할때는 가짜 URL을 등록하면 안 된다! 게시물 등록 버튼을 클릭하면, 파일을 먼저 모두 업로드(uploadFile)한 후, 결과로 받아온 url을 배열로 묶어 게시물 등록(createBoard)을 요청하면 된다.

```js
import { gql, useMutation } from "@apollo/client";

import { ChangeEvent, useState } from "react";
import {
  IMutation,
  IMutationUploadFileArgs,
} from "../../src/commons/types/generated/types";

const UPLOAD_FILE = gql`
  mutation uploadFile($file: Upload!) {
    uploadFile(file: $file) {
      url
    }
  }
`;

export const CREATE_BOARD = gql`
  mutation createBoard($createBoardInput: CreateBoardInput!) {
    createBoard(createBoardInput: $createBoardInput) {
      _id
    }
  }
`;

export default function ImageUploadPreviewPage() {
  const [file1, setFile1] = useState<File>();

  const [imageUrl, setImageUrl] = useState("");

  const [uploadFile] = useMutation<
    Pick<IMutation, "uploadFile">,
    IMutationUploadFileArgs
  >(UPLOAD_FILE);
  const [createBoard] = useMutation(CREATE_BOARD);

  // 미리보기용 임시 이미지 URL 만들기
  const onChangeFile = (event: ChangeEvent<HTMLInputElement>) => {
    const file = event.target.files?.[0];
    if (!file) {
      alert("파일이 없습니다!");
      return;
    }

    const fileReader = new FileReader();
    fileReader.readAsDataURL(file); // blob(binary large object) 파일을 읽어서 임시 url 형태로 만든다
    fileReader.onload = (data) => {
      if (typeof data.target?.result === "string") {
        console.log(data.target?.result);
        setImageUrl(data.target?.result);
        setFile1(file);
      }
    };
  };

  const onClickSumbit = async () => {
    // 1. 파일 업로드
    const result1 = await uploadFile({ variables: { file: file1 } });
    const imageUrl = result1.data?.uploadFile.url;

    // 2. 위에서 받아온 URL 가지고 뮤테이션 날리기
    const result2 = await createBoard({
      variables: {
        createBoardInput: {
          writer: "코알라",
          password: "1234",
          title: "안녕하세요",
          contents: "20시간을 자야 합니다",
          images: [imageUrl], // 임시 url(blob)을 넣어도 작동은 되지만 용량이 커서 좋은 방법이 아님. 백엔드 들렀다 와서 짧아진 url을 사용!
        },
      },
    });
    console.log(result2.data.createBoard._id);
  };

  return (
    <div>
      <input type="file" onChange={onChangeFile} />
      <img src={imageUrl} />
      <button onClick={onClickSumbit}>게시글 등록하기</button>
    </div>
  );
}
```

그런데 이렇게 파일이 변경될 때마다 자동으로 uploadFile이 실행되도록 하면 시간이 비교적 오래 걸린다. <br>

따라서, 다음과 같이 시간 단축을 위해 동시에 보내는 방법으로 Promise.all()을 사용하여 효율성을 높일 수 있다.

```js
import { gql, useMutation } from "@apollo/client";

import { ChangeEvent, useState } from "react";
import {
  IMutation,
  IMutationUploadFileArgs,
} from "../../src/commons/types/generated/types";

const UPLOAD_FILE = gql`
  mutation uploadFile($file: Upload!) {
    uploadFile(file: $file) {
      url
    }
  }
`;

export const CREATE_BOARD = gql`
  mutation createBoard($createBoardInput: CreateBoardInput!) {
    createBoard(createBoardInput: $createBoardInput) {
      _id
    }
  }
`;

export default function ImageUploadPreviewPage() {
  const [files, setFiles] = useState<(File | undefined)[]>([
    // file 또는 undefined가 들어 있는 배열
    undefined,
    undefined,
    undefined,
  ]);

  const [imageUrls, setImageUrls] = useState(["", "", ""]);

  const [uploadFile] = useMutation<
    Pick<IMutation, "uploadFile">,
    IMutationUploadFileArgs
  >(UPLOAD_FILE);
  const [createBoard] = useMutation(CREATE_BOARD);

  const onChangeFile =
    (number: number) => (event: ChangeEvent<HTMLInputElement>) => {
      const file = event.target.files?.[0];
      if (!file) {
        alert("파일이 없습니다!");
        return;
      }

      const fileReader = new FileReader();
      fileReader.readAsDataURL(file); // blob(binary large object) 파일을 읽어서 임시 url 형태로 만든다
      fileReader.onload = (data) => {
        if (typeof data.target?.result === "string") {
          const tempUrls = [...imageUrls]; // 원본이 변경되면 원본과의 비교를 통한 리렌더가 발생하지 않으므로
          tempUrls[number] = data.target?.result;
          setImageUrls(tempUrls);

          const tempFiles = [...files];
          tempFiles[number] = file;
          setFiles(tempFiles);
        }
      };
    };

  const onClickSumbit = async () => {
    const results = await Promise.all(
      files.map((el) => el && uploadFile({ variables: { file: el } })) // 초깃값이 undefined니까 'el이 있으면 바꿔라!'
    );

    const resultUrls = results.map((el) =>
      el?.data ? el?.data.uploadFile.url : ""
    ); // 결과가 있으면, 결과 안의 url을 뽑고, 없으면 빈 문자열을 리턴해라

    const result2 = await createBoard({
      variables: {
        createBoardInput: {
          writer: "코알라",
          password: "1234",
          title: "안녕하세요",
          contents: "20시간을 자야 합니다",
          images: resultUrls,
        },
      },
    });
    console.log(result2.data.createBoard._id);
  };

  return (
    <div>
      <input type="file" onChange={onChangeFile(0)} />
      <input type="file" onChange={onChangeFile(1)} />
      <input type="file" onChange={onChangeFile(2)} />
      <img src={imageUrls[0]} />
      <img src={imageUrls[1]} />
      <img src={imageUrls[2]} />
      <button onClick={onClickSumbit}>게시글 등록하기</button>
    </div>
  );
}
```

게시글 등록하기 버튼을 눌렀을 때 한번에 모든 파일을 uploadFile을 통해 업로드하고, createBoard도 함께 요청 하도록 했다.
