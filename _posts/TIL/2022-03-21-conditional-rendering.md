---
title: "비동기 통신과 조건부렌더링"
excerpt: "원하는 조건에 맞게 내용을 보여주자!"

categories: javascript
tags: [javascript, conditional rendering]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### 1. 조건부 렌더링

자바스크립트는 작성된 코드가 상단에서부터 순차적으로 실행되기 때문에, 데이터를 요청하고 응답을 받아오는 동안 화면에 그려질 데이터의 내용이 undefined이다. 따라서 이 첫 화면이 그려지는 시기에 데이터를 불러오면서 에러가 발생한다. 이 부분을 효율적으로 실행하기 위해 화면을 미리 그려놓고 데이터를 넣어주기 위해 **조건부 렌더링**을 사용한다. 조건부 렌더링에는 세 가지가 있다: &&연산자, 삼항연산자, 그리고 옵셔널 체이닝이 그것이다.

<br>

#### 1.1. 삼항연산자

가장 처음에는 **삼항 연산자**를 사용했다. data는 동기적으로 받아와야 하는 데이터인데, 데이터가 오기 전에 이미 return 부분에서 렌더링을 해주고 있기 대문에 삼항 연산자를 써서 데이터가 있을 때와 없을 때를 모두 적어주어야 했다.

```javascript
data ? data.fetchProfile : undefined;
```

해석해보면, ?의 앞쪽은 '데이터가 있을 때', ?와 : 사이 내용은 '프로필을 가져와라'
: 뒷쪽은 '데이터가 없으면 undefined를 반환하라'를 의미한다.

<br>

#### 1.2. &&연산자

그 이후에는 **&&연산자**를 사용했다. &&연산자는 데이터가 없을 경우 자동으로 undefined를 반환해주기 때문에 '삼항'을 쓸 필요가 없다.

```javascript
data && data.fetchProfile;
```

**※ Nullish Coalescing**
&&연산자는 앞의 값이 참일 경우에만 뒤의 값을 보여주었는데, 이와 반대로 앞의 값이 거짓일 때 뒤의 값을 보여주는 경우를 nullish coalescing 연산자라 부른다. ??연산자는 앞의 값이 빈 값이면 뒤의 값을 보여주며, ||연산자는 앞의 값이 거짓일 경우 뒤의 값을 보여준다.

```javascript
data ?? data.fetchProfile; // 앞의 값이 빈 값일 때 (nullish = 'null 같은 애들!')
data || data.fetchProfile; // 앞의 값이 거짓일 때
```

‣ 이때 **거짓값(falsy value)**이란!?? : 0, '', false, null, undefined, NaN을 말한다.

‣ null과 undefined의 차이
둘 다 비어 있는 값이지만, 의도적으로 비워놨을 때 이렇게 명시적으로 작성해두는 게 국룰이라고 한다.

<&&연산자와 Nullish Coalescing 예시>

```javascript
// &&의 앞이 true일 때 '안녕하세요'
"사과" && "안녕하세요"; // true이기 대문에 '안녕하세요' 출력

// ||의 앞이 false 때 '반갑습니다'
"사과" || "반갑습니다"; // true이기 때문에 '반갑습니다' 대신 그대로 '사과' 출력

//??의 앞이 빈 값일 때 'nullish'
null ?? "nullish"; //  null은 빈 값으로 취급되기 때문에 'nullish' 출력
undefined ?? "nullish"; // undefined도 빈 값으로 취급되기 때문에 'nullish' 출력

// ??의 앞이 빈 값일 때 'nullish'
0 ?? "nullish"; // 숫자 0은 빈 값이 아니기 때문에 0 출력
NaN ?? "nullish"; //
false ?? "nullish"; //
```

<br>

#### 1.3. 옵셔널 체이닝

optional-chaining이란, &&연산자를 스면서 길어졌던 코드를 간결하게 사용하는 연산자이다. (ES2020 최신-문법!) 다음과 같이 사용한다.

```javascript
// data && data.fetchProfile (&&연산자 사용)
data?.fetchProfile;
```

옵셔널 체이닝은 ?연산자 앞 객체의 참조가 undefined 혹은 null일 때 undefined를 리턴한다.
조건부 렌더링 세 가지를 함께 보면 다음과 같다. (최신 문법인 옵셔널 체이닝을 사용하면 좋은 이유!)
![optional_chaining](\assets/images/router/optional_chaining.png)
