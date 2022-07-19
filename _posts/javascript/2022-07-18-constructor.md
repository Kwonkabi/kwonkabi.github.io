---
title: "constructor"
excerpt: ""

categories: javascript
tags: [javascript, constructor]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

constructor는 object 복사 기계

object를 마구 찍어내고 싶을 때 사용한다
비슷한 object 여러 개 만들 때

```js
function 기계() {
  // 생성 기계(constructor) 이름은 보통 대문자로 시작함
  this.name = "Kim"; // this는 이 기계로부터 새로 생성되는 object를 뜻함; '인스턴스'라고 부른다!
  this.age = 15;
}

var 학생1 = new 기계();

console.log(학생1); // 기계 {name: 'Kim', age: 15}
```

```js
function 기계(이름, 나이) {
  this.name = 이름;
  this.age = 나이;
}

var 학생1 = new 기계("Lee", 18);
var 학생2 = new 기계("Kwon", 27);

console.log(학생1); // 기계 {name: 'Lee', age: 18}
console.log(학생2); // 기계 {name: 'Kwon', age: 27}
```

```js
function 기계(이름, 나이) {
  this.name = 이름;
  this.age = 나이;
  this.sayHi = function () {
    console.log(`안녕하세요 저는 ${this.name}입니다.`);
  };
  this.myAge = function () {
    console.log(`제 나이는 ${this.age}입니다.`);
  };
}

var 학생1 = new 기계("Lee", 18);
var 학생2 = new 기계("Kwon", 27);

console.log(학생1.sayHi()); // 안녕하세요 저는 Lee입니다.
console.log(학생2.sayHi()); // 안녕하세요 저는 Kwon입니다.

console.log(학생1.myAge()); // 제 나이는 18입니다.
console.log(학생2.myAge()); // 제 나이는 27입니다.
```

**간단 연습문제**

쇼핑몰에 쓸 상품데이터를 오브젝트로 여러개 만들고 싶습니다.

그래서 하드코딩해봤는데

```js
var product1 = { name: "shirts", price: 50000 };
var product2 = { name: "pants", price: 60000 };
```

앞으로 몇십개를 더 만들어야해서 하드코딩은 그만두고 constructor를 만들어서 오브젝트를 뽑아내려고 합니다.

> Q1. 위처럼 생긴 상품오브젝트들을 뽑아낼 수 있는 constructor를 제작해보세요.

그리고 실제 상품 두개를 뽑아보십시오.

```js
function Product(품목, 가격) {
  this.name = 품목;
  this.price = 가격;
}

var product1 = new Product("shirts", 50000);
var product2 = new Product("pants", 60000);

console.log(product1); // Product {name: 'shirts', price: 50000}
console.log(product2); // Product {name: 'pants', price: 60000}
```

> Q2. 상품마다 vat() 라는 내부 함수를 실행하면 콘솔창에 상품가격 \* 10% 만큼의 부가세금액이 출력되도록 하고 싶으면 constructor를 어떻게 수정해야할까요? <br>
> 예를 들면 product1.vat() 이렇게 쓰면 콘솔창에 5000이 출력되어야합니다

```js
function Product(품목, 가격) {
  this.name = 품목;
  this.price = 가격;
  this.vat = function () {
    return this.price * 0.1;
  };
}

var product1 = new Product("shirts", 50000);
var product2 = new Product("pants", 60000);

console.log(product1.vat()); // 5000
console.log(product2.vat()); // 6000
```
