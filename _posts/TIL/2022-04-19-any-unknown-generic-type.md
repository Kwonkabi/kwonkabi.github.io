---
title: "any, unknown, generic 타입"
excerpt: "다양한 타입에 대해 알아보자"

categories: typescript
tags: [typescript, any, unknown, generic]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

### any와 unknown, generic 타입 차이

```js
// 1. 문자 타입
const getString = (arg: string): string => {
  return arg;
};
const result1 = getString("철수");

// 2. 숫자 타입
const getNumber = (arg: number): number => {
  return arg;
};
const result2 = getNumber(7);

// 3. any 타입
const getAny2 = (arg: any): any => {
  return arg;
};
const result3_1 = getAny2("철수");
const result3_2 = getAny2(8);
const result3_3 = getAny2(true);

// 4. any 타입2
const getAnys = (arg1: any, arg2: any, arg3: any): [any, any, any] => {
  return [arg3, arg2, arg1];
};
const result4 = getAnys("철수", "다람쥐초등학교", 8);

// 5. generic 타입 (들어온 타입을 그대로 사용)
function getGeneric<MyType>(arg: MyType): MyType {
  return arg;
}
const aaa: string = "철수";
const bbb: number = 8;
const ccc: boolean = true;
const result5_1 = getGeneric(aaa);
const result5_2 = getGeneric(bbb);
const result5_3 = getGeneric(ccc);

// 6. generic 타입2
// prettier-ignore
function getGenerics<MyType1, MyType2, MyType3>(arg1: MyType1, arg2: MyType2, arg3: MyType3): [MyType3, MyType2, MyType1] {
  return [arg3, arg2, arg1];
}
const result6 = getGenerics("철수", "다람쥐초등학교", 8);

// 7. generic - 축약1
// prettier-ignore
function getGenericsT<T1, T2, T3>(arg1: T1, arg2: T2, arg3: T3): [T3, T2, T1] {
  return [arg3, arg2, arg1];
}
const result7 = getGenericsT("철수", "다람쥐초등학교", 8);

// 8. generic - 축약2
// prettier-ignore
function getGenericsTUV<T, U, V>(arg1: T, arg2: U, arg3: V): [V, U, T] {
  return [arg3, arg2, arg1];
}
const result8 = getGenericsTUV("철수", "다람쥐초등학교", 8);

// 9. useState에서의 generic!!!!
// const [school, setSchool] = useState<string>("다람쥐초등학교");
// const apple: number = 3;
// const result8 = getGenericsTUV<string, string, number>("철수", "다람쥐초등학교", 8);

// 10. 화살표 함수에서의 generic!!
// const getGenericsTUV = <T, U, V>(arg1: T, arg2: U, arg3: V): [V, U, T] => {
//   return [arg3, arg2, arg1];
// }
```

- any는 우리가 알지 못하는 타입을 표현할때 자주 사용한다. any의 경우 타입 검사를 하지 않게 되는데 타입의 일부만 알고 전체에 대한 타입을 알지 못할 때 유용하다.

- any와 unknown 모두 매개변수(arg)로 뭘 넣어주든 상관없지만, unknown은 개발자에게 조금 더 안전하게 타입을 지정할 수 있도록 경고해 줄 수 있다.

- any는 실제로 함수 반환 시 어떤 타입인지에 대한 정보는 잃게 되어 string을 넣어도 any타입이 반환되는 반면, generic은 입력 값에 따라 내부 타입이 정의된다. 즉, 단일 타입이 아닌 다양한 타입에서 작동하게 작성할 수 있다.

- generic은 useState,useMutation 등을 만들어 사용할 경우에 자주 쓰인다.

<br>

### 고차함수에서

```js
// 1. HOF - 일반 타입
function firstFunc1(arg1: string) {
  return function secondFunc1(arg2: number): [string, number] {
    return [arg1, arg2];
  };
}
const result1 = firstFunc1("영희")(8);

// 2. HOF - any 타입
function firstFunc2(arg1: any) {
  return function secondFunc2(arg2: any): [any, any] {
    return [arg1, arg2];
  };
}
const result2 = firstFunc2("영희")(8);

// 3. HOF - generic 타입
function firstFunc3<T>(arg1: T) {
  return function secondFunc3<U>(arg2: U): [T, U] {
    return [arg1, arg2];
  };
}
const result3 = firstFunc3("영희")(8);

// 4. HOF - generic 타입(화살표 함수)
// const firstFunc4 = <T>(arg1: T) => {
//   return const secondFunc4 = <U>(arg2: U): [T, U] => {
//     return [arg1, arg2];
//   };
// }
// prettier-ignore
const firstFunc4 = <T>(arg1: T) => <U>(arg2: U): [T, U] => {
  return [arg1, arg2];
}
const result4 = firstFunc4("영희")(8);

// 5. HOF - generic 타입(컴포넌트에 응용해보기 - HOC)
// prettier-ignore
const withAuth = <C>(component: C) => <P>(props: P): [C, P] => {
  return [component, props];
}
const result5 = withAuth("bbb")({ aaa: "철수" });
```

- `const aaa = <>()=><>():반환타입(return 있을경우)=>{}` 의 형태가 된다.

<br>

### 고차 컴포넌트에서

```js
import { useRouter } from "next/router";
import { ComponentType, useEffect } from "react";

export const withAuth = (Component: ComponentType) => <P extends {}>(props: P) => {
  const router = useRouter();

  useEffect(() => {
    if (!localStorage.getItem("accessToken")) {
      alert("로그인 후 이용 가능합니다.");
      router.push("/24-01-login-use-apollo-client");
    }
  }, []);

  return <Component {...props} />;
};
```

- HOC와 HOF의 가장 큰 차이는 HOC의 경우, 매개변수와 반환되는 형태가 컴포넌트라는 점이다! 따라서 Component부분은 ComponentType으로, props부분은 {}객체로 지정한다.

- return 타입은 `:` 을 통해 return type을 지정해 줄 수 있다. Ex) fuction aaa():string{return “망고”}
