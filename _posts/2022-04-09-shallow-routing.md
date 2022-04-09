---
title: "얕은 라우팅(Shallow Routing)"
excerpt: ""

categories: coding
tags: [react, typescript, component, ref, createRef, useRef]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 얕은 라우팅

**얕은 라우팅**은 'getServerSideProps', 'getStaticProps'나 'getInitialProps'와 같은 데이터 페칭 메서드를 사용하지 않고도 URL을 바꿔준다. 얕은 라우팅을 사용하면 'useRouter'나 'withRouter'를 사용해서 추가한 **라우터 객체**를 통해 스테이트를 잃지 않으면서, 업데이트된 경로명과 쿼리를 얻을 수 있다! (여러 개의 URL을 다루는 하나의 페이지 컴포넌트)

얕은 라우팅을 가능하게 하려면, **shallow 옵션을 true**로 설정해주면 된다! 다음은 [Next.js에서 제공하는 예시](https://nextjs.org/docs/routing/shallow-routing)이다.


```javascript
import { useEffect } from 'react'
import { useRouter } from 'next/router'

// Current URL is '/'
function Page() {
  const router = useRouter()

  useEffect(() => {
    // Always do navigations after the first render
    router.push('/?counter=10', undefined, { shallow: true })
  }, [])

  useEffect(() => {
    // The counter changed!
  }, [router.query.counter])
}

export default Page
```
**shallow: true**로 설정해주면 URl이 `/?counter=10`로 업데이트되지만 페이지는 바뀌지 않고 라우팅 상태만 변경된다.


#### 1.1. 얕은 라우팅 예시: Choose Your Own Adventure

이해가 잘 안 돼서 얕은 라우팅이 적용된 예시를 찾아봤다. ['Choose Your Own Adventure'](https://next-adventure.netlify.app/)이라는 앱이다. 얕은 라우팅을 사용해서, 매번 데이터베이스에 쿼리를 요청하지 않고도 이야기를 전개해나간다. 라우팅 관련 코드는 다음과 같다.

```javascript
useEffect(() => {
  let trailingPath = router.asPath.substring(
    router.asPath.length - state.event.type.length
  );

  if (trailingPath !== state.event.type) {
    router.push(`/s/start`, undefined, { shallow: true });
  }
}, []);
```

※ 출처 : [**Shallow Routing in Next.js**(https://dev.to/netlify/shallow-routing-in-next-js-1dp9)](https://dev.to/netlify/shallow-routing-in-next-js-1dp9)