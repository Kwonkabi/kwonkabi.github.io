---
title: "Promise와 Promise.all 차이 비교하기"
excerpt: ""

categories: coding
tags: [Promise, Promise.all]

toc: false
toc_sticky: true

author_profile: true
sidebar: false
---


```javascript

export default function PromisePage() {
  const onClickPromise = async () => {
    console.time("Promise 시작");
    const result1 = await new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("https://dog1.jpg");
      }, 3000);
    });
    console.log(result1);

    const result2 = await new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("https://dog2.jpg");
      }, 1000);
    });
    console.log(result2);

    const result3 = await new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("https://dog3.jpg");
      }, 2000);
    });
    console.log(result3);
    console.timeEnd("Promise 시작");
  };

  return (
    <>
      <button onClick={onClickPromise}>Promise 연습</button>
    </>
  );
}

```

![promise01](\assets/images/promise/promise01.png)

Promise를 사용하면 총 6초의 시간이 걸리는 걸 볼 수 있다.


```javascript

export default function PromiseAllPage1() {
  const onClickPromiseAll = async () => {
    console.time("PromiseAll 시작");

    const result = await Promise.all([
      new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve("https://dog1.jpg");
        }, 3000);
      }),

      new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve("https://dog2.jpg");
        }, 1000);
      }),

      new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve("https://dog3.jpg");
        }, 2000);
      }),
    ]);
    console.log(result);
    console.timeEnd("PromiseAll 시작");
  };

  return (
    <>
      <button onClick={onClickPromiseAll}>Promise.all 연습하기</button>
    </>
  );
}

```

![promise.all01](\assets/images/promise/promise01.png)

Promise.all을 사용하면 시간이 6초에서 3초로 줄어든다.


```javascript

export default function PromiseAllPage2() {
  const onClickPromiseAll = async () => {
    console.time("PromiseAll 시작");

    const result = await Promise.all(
      ["https://dog1.jpg", "https://dog2.jpg", "https://dog3.jpg"].map(
        (el) =>
          new Promise((resolve, reject) => {
            setTimeout(() => {
              resolve(el);
            }, 3000);
          })
      )
    );
    console.log(result);
    console.timeEnd("PromiseAll 시작");
  };

  return (
    <>
      <button onClick={onClickPromiseAll}>Promise.all 연습하기</button>
    </>
  );
}

```

![promise.all02](\assets/images/promise/promise.all02.png)

Promise 세 개의 시간을 동일하게 설정한다면, 배열과 map()을 사용해서 코드 길이를 줄여줄 수도 있다.