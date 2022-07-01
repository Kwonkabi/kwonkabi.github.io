---
title: "메모이제이션(memoization)"
excerpt: "리렌더 멈춰!!!"

categories: coding
tags: [memoization]

toc: false
toc_sticky: true

author_profile: true
sidebar: false
---

setState 함수와 이제 많이 친해져서 요긴하게 사용하고 있다. 그치만 setState 함수를 사용하게 되면, 화면 전체가 다시 렌더되면서 스테이트와 함수들을 다시 그리게 된다. 만약 input 태그에 onChange 이벤트로 setState 함수를 실행한다면, 문자 하나 변경될 때마다 컴포넌트가 다시 렌더되고, 스테이트와 함수들이 계속 재생성되는 것이다. 컴포넌트 크기가 크거나 함수들의 로직이 매우 길다면, 처음부터 다시 그려내는 과정이 불필요하게 느껴진다.

**메모이제이션(Memoization)**은 특정 연산이나 함수의 값을 기억해놓은 후, 스테이트의 변화로 컴포넌트를 리렌더하더라도 연산이나 함수를 재생성하지 않고 기존에 저장된 값을 그대로 사용할 수 있게 한다. 메모이제이션 기능을 사용해서 좀 더 빠른 컴포넌트 렌더링을 기대할 수 있다.

메모이제이션 예제로 let과 state를 사용해서 간단한 카운터를 만들어보자!

![1](\assets/images/memoization/1.png)

![2](\assets/images/memoization/2.png)

스테이트 누르고 (리렌더) 후 let 누르면 초기화. 1부터 다시 시작

![3](\assets/images/memoization/3.png)
![4](\assets/images/memoization/4.png)

부모 컴포넌트가 리렌더 되면 자식 컴포넌트도 다 다시 렌더됨.

크롬에서 react-dev-tools 설치 후 리액트 앱(사이트) 접속하면 이렇게 나옴
![5](\assets/images/memoization/5.png)
‘프로파일러’ 클릭

하이라이트 웅앵웅 선택
![6](\assets/images/memoization/6.png)

자식 리렌더 막는 법: memo
![7](\assets/images/memoization/7.png)
![8](\assets/images/memoization/8.png)

'프리젠터가 렌더링됩니다.'가 뜨지 않음

프로파일러로 확인해보면
프리젠터(자식 컴포넌트)가 'Did not render'라고 뜬다.
