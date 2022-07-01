---
title: "권한 분기"
excerpt: "인증과 인가의 차이점과 권한분기에 대해 알아보자!"

categories: coding
tags: [authorization]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

**권한분기**란 말 그대로, 권한에 따라 화면을 렌더링 해주는 페이지를 나눠준다는 의미이다. 그 전에 인증(Authentication)과 인가/권한부여(Authorization)의 개념을 각각 이해할 필요가 있다.

**인증(Authentication)**이란, 한 단어로 표현하면 '**로그인**'이다. **인가(Authorization)**는 한 단어로 표현하면 '**권한**'이다. 액세스를 제어하여 사용자가 CRUD 하는 것을 허용하는지 여부를 확인하는 것이다. 인증은 로그인 자격 증명을 확인해서 로그인 한 사용자를 인식하는 것이고, 인가는 사용자의 신원이 성공적으로 인증된 후에 발생하며, 사용자의 권한에 따라 누가 무엇을 할 수 있는지를 결정한다. 이때 사용자에 따라 렌더링되는 페이지를 달리 해주는 것을 **권한분기**라 한다.

크게는 관리자/사용자, 회원/비회원으로 나뉠 수 있고, 작게는 (배민)사장님/손님, (카카오택시)기사님/손님과 같이 여러 유형의 회원마다 볼 수 있는 페이지의 권한이 모두 달라야 한다.

이 과정에서 주로 HOC(고차 컴포넌트)와 useEffect를 사용한다. 해당 유저가 어떤 정보를 갖고 있는지 확인하기 위한 토큰 값을 이용해서 권한분기를 할 수 있다. 다음은 withAuth라는 HOC를 사용하여 함수 안쪽에서 조건문에 따라 라우터로 이동시켜주는 경로를 다르게 설정해준 예시이다.

```javascript
const withAuth = (Component) => (props) => {
  const router = useRouter();
  const { accessToken } = useRecoilState(accessTokenState);
  useEffect(() => {
    if (!accessToken) router.push("/login");
  }, []);
  if (!accessToken) return <></>;
  return <Component {...props} />;
};

export default withAuth;
```