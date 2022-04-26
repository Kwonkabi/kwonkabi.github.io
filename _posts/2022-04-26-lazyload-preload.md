---
title: "레이지로드(Lazyload)와 프리로드(Preload)"
excerpt: "레이지로드와 프리로드 이해하기"

categories: coding
tags: [lazyload, preload]

toc: false
toc_sticky: true

author_profile: true
sidebar: false
---

레이지 로드(Lazy Load)는, 말 그대로 자바스크립트를 바로 로드해서 실행하는 것이 아니라 페이지 로드가 이뤄지고 나서 조금 늦게 로드해서 실행하는 방법이다. 페이지 전체 데이터(ex. 이미지)를 미리 다 받아두는 게 아니라, 스크롤을 내릴 때 그때그때 추가로 받아지는 것을 예로 들 수 있다.

크몽 사이트를 열고 네트워크 탭을 보면, 스크롤을 내릴 때마다 이미지가 새로 다운로드 되는 것을 볼 수 있다.

![lazyload](\assets/images/lazyload_preload/lazyload.png)

레이지 로드를 구현할 수 있는 라이브러리로는 [**React Lazy Load**](https://www.npmjs.com/package/react-lazy-load)가 있다.

레이지 로드와 달리, 프리로드(Preload)는 페이지를 전환하면 바로 데이터를 볼 수 있도록, 다른 메뉴에 있을 때 미리 다운로드를 받아놓고 대기하고 있는 것을 말한다.

```javascript
import { useEffect, useRef, useState } from "react";

export default function ImagePreloadPage() {
  const [imgTag, setImgTag] = useState<HTMLImageElement>();
  const divRef = useRef<HTMLDivElement>(null);
  const [isLoaded, setIsLoaded] = useState(false);

  useEffect(() => {
    const img = new Image();
    img.src =
      "https://pixabay.com/get/g38c8d00a4dcd6230c798d612f69eb660cf5f3d4ea97aa84137d9a0446293c973e533fd1dad078670288462eb268fb802.jpg";
    img.onload = () => {
      setImgTag(img);
    };
  }, []);

  const onClickPreload = () => {
    if (imgTag) divRef.current?.appendChild(imgTag);
  };

  const onClickLoad = () => {
    setIsLoaded(true);
  };

  return (
    <div>
      <div ref={divRef}></div>
      <button onClick={onClickPreload}>이미지 프리로드</button>
      ========================================
      {isLoaded && (
        <img src="https://pixabay.com/get/g38c8d00a4dcd6230c798d612f69eb660cf5f3d4ea97aa84137d9a0446293c973e533fd1dad078670288462eb268fb802.jpg" />
      )}
      <button onClick={onClickLoad}>이미지 일반로드</button>
    </div>
  );
}
```

useEffect 부분에서, 버튼 클릭 여부와 상관 없이 다운로드 시작, 스테이트에 저장된다. 그래서 같은 이미지라도 '이미지 일반로드' 버튼을 눌렀을 때보다 '이미지 프리로드' 버튼을 눌렀을 때 더 빨리 화면에 나타나게 된다. (확연한 차이를 보려면 네트워크에서 'No throttling'에서 'slow 3G'로 변경 후 시도해보면 된다.)