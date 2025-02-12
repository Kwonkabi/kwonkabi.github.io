---
title: "prototype"
excerpt: "프로토타입은 유전자다!"

categories: javascript
tags: [javascript, prototype]

toc: true
toc_sticky: true

author_profile: true
sidebar: false
---

자바스크립트는 `클래스 기반` 객체지향 프로그래밍 언어보다 효율적이며 강력한 객체지향 프로그래밍 능력을 지니고 있는 `프로토타입 기반`의 `객체지향` 프로그래밍 언어다.

> 자바스크립트는 객체 기반의 프로그래밍 언어이며 자바스크립트를 이루고 있는 거의 '모든 것'이 객체다.

<br>

---

### 1. 객체지향 프로그래밍

> 객체지향 프로그래밍은 실세계의 실체(사물이나 개념)를 인식하는 철학적 사고를 프로그래밍에 접목하려는 시도에서 시작한다.

`속성`을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 `객체`라하며, `객체 지향 프로그래밍`은 독립적인 객체의 `집합`으로 프로그램을 표현하려는 `프로그래밍 패러다임`이다.

<br>

#### 원이라는 개념을 객체로 만들어보자.

- 원에는 반지름이라는 속성이 있다. 이 반지름을 가지고 원의 지름, 둘레, 넓이를 구할 수 있다.

- 반지름은 원의 `상태를 나타내는 데이터`이며 원의 지름, 둘레, 넓이를 구하는 것은 `동작`이다.

> 객체지향 프로그래밍은 객체의 `상태`를 나타내는 데이터와 상테 데이터를 조작할 수 있는 `동작`을 하나의 논리적인 단위로 묶어 생각한다.

- 즉 객체는 `상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조`이다.

- 여기서 객체의 상태 데이터를 `프로퍼티`, 동작을 `메서드`라 부른다.

<br>

---

### 2. 상속과 프로토타입

> `상속`은 객체지향 프로그래밍의 핵심 개념으로, 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.

<br>

---

### 3. 프로토타입

> `프로토타입 객체(또는 줄여서 프로토타입)`란 객체지향 프로그래밍의 근간을 이루는 객체 간 상속을 구현하기 위해 사용된다.

- 프로토타입은 어떤 객체의 상위(부모) 객체의 역할을 하는 객체로서 다른 객체에 `공유 프로퍼티(메서드 포함)`을 제공한다.

- 프로토타입을 상속받은 하위(자식) 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 자유롭게 사용할 수 있다.

<br>

#### \_\_proto\_\_ 접근자 프로퍼티

모든 객체는 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 [[Prototype]] 내부 슬롯에 간접적으로 접근할 수 있다.

![](/assets/images/js/proto.png)

<br>

► `__proto__` 는 접근자 프로퍼티다. <br>

► `__proto__` 접근자 프로퍼티는 상속을 통해 사용된다. <br>

► `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유

- [[Prototype]] 내부 슬롯의 값, 즉 포로토타입에 접근하기 위해 접근자 프로퍼티를 사용하는 이유는 상호 참조에 의해 `프로토타입 체인`이 생성되는 것을 `방지`하기 위해서다.

- 프로토타입 체인은 `단방향 링크드 리스트`로 구현되어야한다.

- 다시 말해 순환참조하는 프로토타입 체인이 만들어지면 프로토타입 체인 종점이 존재하지 않기 때문에 프로토타입 체인에서 프로퍼티를 검색할 때 `무한루프`에 빠진다. <br>

► `__proto__` 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않는다.

- `__proto__` 접근자 프로퍼티 대신 프로토타입의 참조를 취득하고 싶은 겨우에는 `Object.getPrototypeOf` 메서드를 사용한다.

- 프로토타입을 `교체`하고 싶은 경우에는 `Object.setPrototypeOf` 메서드를 사용할 것을 권장한다.

<br>

#### 함수 객체의 prototype 프로퍼티

- 함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.

- prototype 프로퍼티는 생성자 함수가 생성할 객체(인스턴스)의 프로토타입을 가리킨다.

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}.hasOwnProperty("prototype")); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}.hasOwnProperty("prototype")); // -> false
```

<br>

- 따라서 생성자 함수로 호출할 수 없는 화살표 함수와 ES6 메서드 축약 표현으로 정의한 메서드는 prototype 프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않는다.

```js
// 화살표 함수는 non-constructor다.
const Person = (name) => {
  this.name = name;
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(Person.hasOwnProperty("prototype")); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(Person.prototype); // undefined

// ES6의 메서드 축약 표현으로 정의한 메서드는 non-constructor다.
const obj = {
  foo() {},
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(obj.foo.hasOwnProperty("prototype")); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(obj.foo.prototype); // undefined
```

<br>

- 생성자 함수로 객체를 생성한 후 `__proto__` 접근자 프로퍼티와 `prototype 프로퍼티`로 프로토타입 객체에 접근한 예시

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}
// Person 이라는 생성자 함수를 사용해서 me 인스턴스 생성
const me = new Person("Lee");

// 결국 Person.prototype과 me.__proto__는 결국 동일한 프로토타입을 가리킨다.
console.log(Person.prototype === me.__proto__); // true
console.log(me.constructor === Person);
```

`__proto__` 로 접근했을때 그리고 `getPrototypeof(me)` 로 접근했을때 Person.protype이 같다.

<br>

#### 프로토타입의 constructor 프로퍼티와 생성자 함수

- 즉 me 객체에는 `constuctor` 프로퍼티가 없지만 me 객체의 프로토타입인 `Person.prototype`에는 `constructor` 프로퍼티가 있다. 따라서 me 객체는 프로토타입인 `Person.prototype`의 `constructor` 프로퍼티를 상속받아 사용할 수 있다.

<br>

---

### 4. 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

- `생성자 함수`에 의해 생성된 `인스턴스`는 프로토타입의 `constructor` 프로퍼티에 의해 `생성자 함수`와 연결된다.

- 이때 `constructor` 프로퍼티가 가리키는 `생성자 함수`는 `인스턴스`를 생성한 `생성자 함수`다.

```js
// obj 객체를 생성한 생성자 함수는 Object다.
const obj = new Object();
console.log(obj.constructor === Object); // true

// add 함수 객체를 생성한 생성자 함수는 Function이다.
const add = new Function("a", "b", "return a + b");
console.log(add.constructor === Function); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// me 객체를 생성한 생성자 함수는 Person이다.
const me = new Person("Lee");
console.log(me.constructor === Person); // true
```

<br>

- 하지만 `리터럴 표기법`에 의한 객체 생성 방식과 같이 new 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하지 않는 객체 생성 방식도 있다.

```js
// 객체 리터럴
const obj = {};

// 함수 리터럴
const add = function (a, b) {
  return a + b;
};

// 배열 리터럴
const arr = [1, 2, 3];

// 정규표현식 리터럴
const regexp = /is/gi;
```

<br>

- 리터럴 표기법에 의해 생성된 객체도 물론 프로토타입이 존재한다.

- 하지만 리터럴 표기법에 의해 생성된 객체의 경우 프로토타입 constructor 프로퍼티가 가리키는 생성자 함수가 반드시 객체를 생성한 생성자 함수라고 단정할 수는 없다.

```js
// obj 객체는 Object 생성자 함수로 생성한 객체가 아니라 객체 리터럴로 생성했다.
const obj = {};

// 하지만 obj 객체의 생성자 함수는 Object 생성자 함수다.
console.log(obj.constructor === Object); // true
```

<br>

- object 생성자 함수에 인수를 전달하지 않거나 undefined 또는 null을 인수로 전달하면서 호출하면 내부적으로 Object.prototype을 프로토타입으로 갖는 빈 객체를 생성한다.

```js
// 2. Object 생성자 함수에 의한 객체 생성
// Object 생성자 함수는 new 연산자와 함께 호출하지 않아도 new 연산자와 함께 호출한 것과 동일하게 동작한다.
// 인수가 전달되지 않았을 때 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 생성한다.
let obj = new Object();
console.log(obj); // {}
```

```js
// 1. new.target이 undefined나 Object가 아닌 경우
// 인스턴스 -> Foo.prototype -> Object.prototype 순으로 프로토타입 체인이 생성된다.
class Foo extends Object {}
new Foo(); // Foo {}
```

```js
// 3. 인수가 전달된 경우에는 인수를 객체로 변환한다.
// Number 객체 생성
obj = new Object(123);
console.log(obj); // Number {123}

// String  객체 생성
obj = new Object("123");
console.log(obj); // String {"123"}
```

<br>

- 함수 객체의 경우 차이가 더 명확하다.

```js
// foo 함수는 Function 생성자 함수로 생성한 함수 객체가 아니라 함수 선언문으로 생성했다.
function foo() {}

// 하지만 constructor 프로퍼티를 통해 확인해보면 함수 foo의 생성자 함수는 Function 생성자 함수다.
console.log(foo.constructor === Function); // true
```

- 리터럴 표기법에 의해 생성된 객체도 상속을 위해 프로토타입이 필요하다.
- 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다.

<br>

---

### 5. 프로토타입의 생성 시점

> 프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.

<br>

#### 사용자 정의 생성자 함수와 프로토타입 생성 시점

- 생성자 함수로서 호출할 수 있는 함수, 즉 constructor는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.

```js
// 함수 정의(constructor)가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
console.log(Person.prototype); // {constructor: ƒ}

// 생성자 함수
function Person(name) {
  this.name = name;
}
```

<br>

> 생성자 함수로서 호출할 수 없는 함수는 프로토타입이 생성되지 않는다.

```js
// 화살표 함수는 non-constructor다.
const Person = (name) => {
  this.name = name;
};

// non-constructor는 프로토타입이 생성되지 않는다.
console.log(Person.prototype); // undefined
```

<br>

---

### 6. 객체 생성 방식과 프로토타입의 결정

- 다양한 방식으로 생성된 모든 객체는 각 방식마다 세부적인 객체 생성 방식의 차이는 있으나 추상 연산 OrdinaryObjectCreate 에 의해 생성된다는 공통점이 있다.

- 즉, 프로토타입은 OrdinaryObjectCreate에 전달되는 인수에 의해 결정된다.

- 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

<br>

#### 객체 리터럴에 의해 생성된 객체의 프로토타입

- 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 프로토타입으로 갖게 되며, 이로써 Object.prototype을 상속받는다.

- obj객체는 constructor 프로퍼티와 hasOwnProperty 메서드 등을 소유하지 않지만 프로토타입인 Object.prototype의 constructor 프로퍼티와 hasOwnProperty 메서드를 자유롭게 사용할 수 있다.

- 이는 obj 객체가 자신의 프로토타입인 Object.prototype 객체를 상속 받았기 때문이다.

```js
const obj = { x: 1 };

// 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

<br>

#### Object 생성자 함수에 의해 생성된 객체의 프로토타입

- Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 프로토타입으로 갖게 된다.

```js
const obj = new Object();
obj.x = 1;

// Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

<br>

#### 생성자 함수에 의해 생성된 객체의 프로토타입

- 생성자 함수에 의해 생성되는 객체의 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체다.

- `Person.prototype`에 프로퍼티를 추가하여 하위 객체가 상속받을 수 있도록 구현할 수도 있다.

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person("Lee");
const you = new Person("Kim");

me.sayHello(); // Hi! My name is Lee
you.sayHello(); // Hi! My name is Kim
```

- Person 생성자 함수를 통해 생성된 모든 객체는 프로토타입에 추가된 `sayHello` 메서드를 상속받아 자신의 메서드처럼 사용할 수 있다.

<br>

---

### 7. 프로토타입 체인

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person("Lee");

// hasOwnProperty는 Object.prototype의 메서드다.
console.log(me.hasOwnProperty("name")); // true
```

- Person 생성자 함수에 의해 생성된 me 객체는 `Object.prototype`의 메서드인 `hasOwnProperty`를 호출할 수 있다.

- 이것은 me 객체가 `Person.prototype` 뿐만 아니라 `Object.prototype`도 상속 받았다는 것을 의미한다.

- me 객체의 프로토타입은 `Person.prototype` 이다.

```js
Object.getPrototypeOf(me) === Person.prototype; // -> true
```

- `Person.prototype` 의 프로토타입은 `Object.prototype`이다. 프로토타입의 프로토타입은 언제나 `Object.prototype`이다.

```js
Object.getPrototypeOf(Person.prototype) === Object.prototype; // -> true
```

<br>

자바스크립트는 객체의 프로퍼티에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 [[Prototype]] 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다.

> 이를 `프로토타입 체인`이라고 한다. 프로토타입 체인은 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 매커니즘이다.

```js
// hasOwnProperty는 Object.prototype의 메서드다.
// me 객체는 프로토타입 체인을 따라 hasOwnProperty 메서드를 검색하여 사용한다.
me.hasOwnProperty("name"); // -> true
```

- 프로토타입 체인의 최상위에 위치하는 객체는 언제나 Object.prototype이다. 따라서 모든 객체는 Object.prototype을 상속받는다.

- Object.prototype을 `프로토타입 체인의 종점(end of prototype chain)`이라 한다.

- Object.prototype의 프로토타입, 즉 [[Prototype]] 내부 슬롯의 값은 null이다.

- 프로토타입 체인의 종점인 Object.prototpye에서도 프로퍼티를 검색할 수 없는 경우 undefined를 반환한다.

```js
console.log(me.foo); // undefined
```

- 프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘 이라고 할 수 있다.

- 프로퍼티가 아닌 식별자는 스코프 체인에서 검색한다. 즉 자바스크립트 엔진은 함수의 중첩 관계로 이루어진 스코프의 계층적 구조에서 식별자를 검색한다.

- 스코프 체인은 식별자 검색을 위한 메커니즘 이라 할 수 있다.

> 스코프 체인과 프로토타입 체인은 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는데 사용된다.

<br>

---

### 8. 오버라이딩과 프로퍼티 섀도잉

```js
const Person = (function () {
  // 생성자 함수 (📌 즉시실행함수로 만듦)
  function Person(name) {
    this.name = name;
  }

  // 📌 프로토타입 sayHello 메서드 만듦
  Person.prototype.sayHello = function () {
    console.log(`Hi! My name is ${this.name}`);
  };

  // 생성자 함수를 반환
  return Person;
})();
// 생성자 함수 Person 은 sayHello가 있는 상태로 만들어짐

const me = new Person("Lee");

// 인스턴스 메서드
me.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};

// 인스턴스 메서드가 호출된다. 프로토타입 메서드는 인스턴스 메서드에 의해 가려진다.
me.sayHello(); // Hey! My name is Lee

// 📌 Hi 가 나오는 것이 아니라 Hey가 나옴 왜냐하면 가장 가까운 것부터 탐색을 하기 때문, 자기 자신한테 sayHello가 있으니까 상위로 안가고 멈춤.
```

`Person` 생성자 함수로 `객체(인스턴스)`를 생성한 다음, `인스턴스`에 메서드 `sayHello` 를 추가했다.

![](/assets/images/js/overriding_property_shadowing.png)

`프로토타입 프로퍼티` : 프로토타입이 소유한 프로퍼티(메서드 포함)
`인스턴스 프로퍼티` : 인스턴스가 소유한 프로퍼티

- 프로토타입 프로퍼티와 같은 이름의 프로퍼티를 인스턴스에 추가하면 프로토타입 체인을 따라 프로토타입 프로퍼티를 검색하여 프로토타입 프로퍼티를 덮어쓰는 것이 아니라 인스턴스 프로퍼티로 추가한다.

- 이때 인스턴스 메서드 sayHello는 프로토타입 메서드 sayHello 를 오버라이딩했고 프로토타입 메서드 sayHello는 는 가려진다.

> 이처럼 상속 관계에 의해 프로퍼티가 가려지는 현상을 `섀도잉`이라한다.

`오버라이딩`: 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

<br>

> 프로퍼티를 삭제하는 경우도 마찬가지다.

```js
// 인스턴스 메서드 sayHello를 삭제한다.
delete me.sayHello;
// 인스턴스에는 sayHello 메서드가 없으므로 프로토타입 메서드가 호출된다.
me.sayHello(); // Hi! My name is Lee
```

- 프로토타입 메서드가 아닌 `인스턴스 메서드 sayHello`가 삭제된다.

<br>

> 이번엔 다시 sayHello 메서드를 삭제하여 `프로토타입 메서드 sayHello` 를 삭제해보자.

```js
// 프로토타입 체인을 통해 프로토타입 메서드 sayHello 가 삭제되지 않는다.
delete me.sayHello;
// 프로토타입 메서드가 호출된다.
me.sayHello(); // Hi! My name is Lee
```

- 하위 객체를 통해 프로토타입의 프로퍼티를 변경 또는 삭제하는 것은 불가능하다.
- 즉 하위 객체를 통해 프로토타입에 `get 엑세스`는 허용되나 `set 엑세스`는 허용되지 않는다.

<br>

> 프로토타입 프로퍼티를 변경 또는 삭제하려면 하위 객체를 통해 프로토타입에 접근하는 것이 아니라 프로토타입에 직접 접근해야한다.

```js
// 프로토타입 메서드 변경
Person.prototype.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};
me.sayHello(); // Hey! My name is Lee

// 프로토타입 메서드 삭제
delete Person.prototype.sayHello;
me.sayHello(); // TypeError: me.sayHello is not a function
```

<br>

---

### 9. 프로토타입의 교체

> 프로토타입은 임의의 다른 객체로 변경할 수 있다. 이것은 부모 객체인 프로토타입을 `동적`으로 변경할 수 있다는 것을 의미한다.

`객체 간의 상속관계`를 `동적`으로 변경하며 `프로토타입`은 `생성자 함수` 또는 `인스턴스`에 의해 `교체`할 수 있다.

<br>

#### 생성자 함수에 의한 프로토타입의 교체

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // ① 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  // 📌 constructor 없는 이유: 별개의 객체로 덮어씌웠기 때문
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Lee");
```

①에서 Person.prototype에 객체 리터럴을 할당했다. 이는 Person 생성자 함수가 생성할 객체의 프로토타입을 객체 리터럴로 교체한 것이다.

![](/assets/images/js/prototype_change.png)

- 프로토타입이 교체한 객체 리터럴에는 `constructor` 프로퍼티가 없다.
- constructor 프로퍼티는 자바스크립트 엔진이 프로토타입을 생성할 때 암묵적으로 추가한 프로퍼티다.
- 따라서 me 객체의 생성자 함수를 검색하면 Person이 아닌 `Object`가 나온다.

```js
// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

- 위의 예제처럼 프로토타입을 교체하면 constuctor 프로퍼티와 생성자 함수간의 연결이 파괴된다.

<br>

파괴된 constructor 프로퍼티와 생성자 함수 간의 연결을 되살리려면 프로토타입으로 교체한 객체 리터럴에 constructor 프로퍼티를 추가하여 프로토타입의 constructor 프로퍼티를 되살린다.

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    // constructor 프로퍼티와 생성자 함수 간의 연결을 설정
    // 📌 constructor 추가
    constructor: Person,
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Lee");

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false
```

<br>

#### 인스턴스에 의한 프로토타입의 교체

인스턴스의 `__proto__` 접근자 프로퍼티를 통해 프로토타입을 교체하는 것은 이미 생성된 객체의 프로토타입을 교체하는 것이다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  },
};

// ① me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee
```

![](/assets/images/js/prototype_change_instance.png)

- 프로토타입으로 교체한 객체에는 contructor 프로퍼티가 없으므로 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.

- 따라서 프로토타입의 contructor 프로퍼티로 me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나온다.

```js
// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

<br>

생성자 함수에 의한 프로토타입의 교체와 인스턴스에 의한 프로토타입 교체는 별다른 차이가 없지만 미묘한 차이가 존재한다. **그리고 프로토타입 교체를 통해 객체 간의 상속 관계를 동적으로 변경하는 것은 꽤나 번거롭기 때문에 프로토타입은 직접 교체하지 않는 것이 좋다.**

상속 관계를 인위적으로 설정하려면 `직접 상속`이 더 편리하고 안전하다. 또는, ES6에서 도입된 클래스를 사용하면 간편하고 직관적인 상속관계를 구현할 수 있다.

<br>

---

### 10. instanceof 연산자

> `instanceof` 연산자는 생성자의 `prototype 속성`이 객체의 프로토타입 체인 어딘가 존재하는지 판별한다.

```js
객체 instanceof 생성자 함수
```

- 좌변에 객체를 가리키는 식별자, 우변에 생성자 함수를 가리키는 식별자를 피연산자로 받는다.

- 우변의 피연산자가 함수가 아닌 경우, TypeError가 발생한다.

- 우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true, 아니면 false로 평가된다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 📌 Q. me 가 Person 의 인스턴스냐?
// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// 📌 Q. me 가 Object 의 인스턴스냐?
// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다. (Object.prototype 를 상속받음)
console.log(me instanceof Object); // true
```

<br>

- instanceof 연산자가 어떻게 동작하는지 이해하기 위해 프로토타입을 교체해보자.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 인스턴스 만들었음
const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {};

// parent 객체를 프로토타입의 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다.
// 📌 Person 은 처음의 프로토타입을 가지고 있는데 me의 프로토타입은 parent 객체로 변경되었기 때문에
console.log(Person.prototype === parent); // false

// 📌 parent의 constructor 가 Person 을 가리키고 있지 않다. parent은 빈객체니까.
console.log(parent.constructor === Person); // false

// 📌 원래 me 는 생성자함수 Person 의 인스턴스로 만든건데 프로토타입을 교체했더니 Person의 인스턴스가 아니게 된 것
// 📌 즉, Person.prototype이 me 객체의 프로토타입 체인 상에 존재하지 않기 때문에 false로 평가된다.
console.log(me instanceof Person); // false

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

- me 객체는 프로토타입이 교체되어 프로토타입과 생성자 함수간 연결이 파괴되었지만 Person 생성자 함수에 의해 생성된 인스턴스임에는 틀림이 없다.

- 그러나 me instanceof Person은 false로 평가된다.

- 이는 Person.prototype이 me 객체의 프로토타입 체인 상에 존재하지 않기 때문이다.

따라서 프로토타입으로 교체한 parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 `바인딩`하면 me instanceof Person은 true로 평가될 것이다.

<br>

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {};

// 프로토타입의 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다.
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 바인딩한다.
// 📌 Person의 prototype 을 parent 로 복구시킴
Person.prototype = parent;

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

위의 예시처럼, `instanceof` 연산자는 생성자 함수의 prototype에 `바인딩`된 객체가 프로토타입 체인 상에 존재하는 지 확인한다.(프로토타입의 contructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니다.)

- `me instanceof Person의` 경우, me 객체의 프로토타입 체인 상 Person.prototype에 `바인딩`된 객체가 존재하는지 확인한다.

- `me instanceof Person`의 경우, me 객체의 프로토타입 체인 상 Object.prototype에 `바인딩`된 객체가 존재하는지 확인한다.

<br>

---

### 11. 직접 상속

#### Object.create에 의한 직접 상속

```js
Object.create(proto[, propertiesObject])
```

`proto`: 새로 만든 객체의 프로토타입이어야 할 객체.

`propertiesObject`: 선택사항. 지정되고 undefined가 아니면, 자신의 속성(즉, 자체에 정의되어 그 프로토타입 체인에서 열거가능하지 않은 속성)이 열거가능한 객체는 해당 속성명으로 새로 만든 객체에 추가될 속성 설명자(descriptor)를 지정한다.

`Object.create`: 명시적으로 프로토타입을 지정하여 새로운 객체를 생성함. 다른 객체 방식과 마찬가지로 추상 연산 OrdinaryObjectCreate를 호출한다.

- `첫 번째 매개변수`: 생성할 객체의 프로토타입으로 지정할 객체를 전달한다.

- `두 번째 매개변수`: 생성할 객체의 프로퍼티 키와, 프로퍼티 디스크립터 객체로 이뤄진 객체를 전달. (Object.defineProperties메서드의 두번째 인수와 동일). 옵션이므로 생략가능하다.

- 첫 번째 매개변수에 전달한 객체의 프로토타입 체인에 속하는 객체를 생성한다.

- 즉, 객체를 생성하면서 직접적으로 상속을 구현하는 것이다.

```js
// 1)
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인의 종점에 위치한다.
// obj → null
// 📌 obj 객체는 프로토타입을 null 지정하면서 객체를 생성
let obj = Object.create(null) ;
console.dir(obj( // 📌 Object(No properties)
console.log(Object.getPrototypeOf(obj) === null); // true

// Object.prototype을 상속받지 못한다.
// 📌 상속받은 것이 없으니까 프로토타입 체인이 없는 상태 toString() 메서드도 없음
console.log(obj.toString()); // TypeError: obj.toString is not a function

// 2)
// 📌 create 메서드로 Object.prototype 를 상속받게 함 일반적인 빈 객체 리터럴 생성
// obj → Object.prototype → null
// obj = {};와 동일하다.
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true


// 3)
// 📌 Object.prototype 를 상속받게 하고 값을 지정함
// obj → Object.prototype → null
// obj = { x: 1 };와 동일하다.
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true }
});
obj // 📌 {x:1}
// 위 코드는 다음과 동일하다.
// obj = Object.create(Object.prototype);
// obj.x = 1;
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true


// 4)
// 📌 myProto 객체 생성
const myProto = { x: 10 };

// 📌  myProto를 obj에게 직접 상속을 함
// 📌 obj 를 만들고 프로토타입은 myProto로 해라
// obj → myProto → Object.prototype → null
obj = Object.create(myProto);

console.log(obj.x); // 📌 10 why? 위에서 myProto 를 상속받았으니까 즉 프로토타입 체이닝을 타고서 obj 의 값이 10
console.log(Object.getPrototypeOf(obj) === myProto); // true


// 5)
// 생성자 함수
function Person(name) {
  this.name = name;
}

// obj → Person.prototype → Object.prototype → null
// obj = new Person('Lee')와 동일하다.
// 📌 Person 생성자 함수의 prototype 이 obj에 [[prototype]] 을로 연결이 되어있는 상태 > Person의 인스턴스가 됨
obj = Object.create(Person.prototype);
obj.name = 'Lee';
console.log(obj.name); // Lee
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

<br>

#### Object.create 메서드의 장점

- new 연산자가 없이도 객체를 생성할 수 있다.

- 프로토타입을 지정하면서 객체를 생성할 수 있다.

- 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.

<br>

#### Object.prototype의 빌트인 메서드

`Object.prototype.hasOwnProperty`, `Object.prototype.isPrototypeOf`, `Object.prototype.propertyIsEnumerable`등은 **모든 객체의 프로토타입 체인의 종점**, 즉 `Object.prototype`의 메서드이므로 모든 객체가 상속받아 호출할 수 있다.

```js
const obj = { a: 1 };

obj.hasOwnProperty("a"); // -> true
obj.propertyIsEnumerable("a"); // -> true
```

그러나 `ESLint`에서는 `Object.prototype`의 빌트인 메서드를 객체가 **직접 호출하는 것을 권장하지 않는다.** `Object.create` 메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문이다. <br>

> 프로토타입 체인의 종점에 위치하는 객체는 `Object.prototype`의 빌트인 메서드를 사용할 수 없다.

```js
// 프로토타입이 null인 객체, 즉 프로토타입 체인의 종점에 위치하는 객체를 생성한다.
// 📌 null 로 해서 만든 것은 프로토타입 체이닝이 없는 상태
const obj = Object.create(null);
obj.a = 1;

console.log(Object.getPrototypeOf(obj) === null); // true

// obj는 Object.prototype의 빌트인 메서드를 사용할 수 없다.
// 📌 hasOwnProperty 이 프로토타입 체인 상에 없으니까
console.log(obj.hasOwnProperty("a")); // TypeError: obj.hasOwnProperty is not a function
```

따라서 이 같은 에러를 발생시킬 위험을 없애기 위해 `Object.prototype`의 빌트인 메서드는 다음과 같이** 간접적**으로 호출하는 것이 좋다.

```js
// 프로토타입이 null인 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

// console.log(obj.hasOwnProperty('a')); // TypeError: obj.hasOwnProperty is not a function

// Object.prototype의 빌트인 메서드는 객체로 직접 호출하지 않는다.
// 📌 call 사용
console.log(Object.prototype.hasOwnProperty.call(obj, "a")); // true
// Fucntion.prototype.call 메서드는 22.2.4절에서 살펴본다. (Function.prototype.apply/call/bind)
```

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

<br>

#### 객체 리터럴 내부에서 \_**\_proto\_\_**에 의한 직접 상속

`ES6`에서는 객체 리터럴 내부에서 `__proto__`접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다.

```js
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서 프로토타입을 지정하여 직접 상속받을 수 있다.
const obj = {
  y: 20,
  // 객체를 직접 상속받는다.
  // obj → myProto → Object.prototype → null
  __proto__: myProto,
};
/* 위 코드는 아래와 동일하다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
});
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

<br>

---

### 12. 정적 프로퍼티/메서드

`정적 프로퍼티/메서드`는 생성자 함수로 인스턴스를 생성하지 않아도 `참조/호출`할 수 있는 `프로퍼티/메서드`를 말한다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 정적 프로퍼티 📌 static 한 것들은 프로토타입 체인을 타고서 쓸 수 있는 것이 아님
Person.staticProp = "static prop";

// 정적 메서드
Person.staticMethod = function () {
  console.log("staticMethod");
};

const me = new Person("Lee");

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.staticMethod(); // staticMethod

// 📌 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

- Person 생성자 함수는 객체이므로 자신의 프로퍼티/메서드를 소유할 수 있다.

- Person 생성자 함수 객체가 소유한 프로퍼티/메서드를 정적 프로퍼티/메서드라고 한다.

- 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.

- 생성자 함수가 생성한 인스턴스는 자신의 프로토타입 체인에 속한 객체의 프로퍼티/메서드에 접근이 가능하다.

- 정적 프로퍼티/메서드는 인스턴스의 프로토타입 체인에 속한 객체의 프로퍼티/메서드가 아니므로 인스턴스로 접근 불가하다.

<br>

---

### 13. 프로퍼티 존재 확인

#### in 연산자

`in` 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다.

```js
key in object;
// key: 프로퍼티 키를 나타내는 문자열
// object: 객체로 평가되는 표현식
```

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/in)

```js
속성 in 객체명;
// 속성: 속성의 이름이나 배열의 인덱스를 뜻하는 문자열 또는 수 값
// 객체명: 객체의 이름
```

<br>

```js
const person = {
  name: "Lee",
  address: "Seoul",
};

// person 객체에 name 프로퍼티가 존재한다.
console.log("name" in person); // true
// person 객체에 address 프로퍼티가 존재한다.
console.log("address" in person); // true
// person 객체에 age 프로퍼티가 존재하지 않는다.
console.log("age" in person); // false
```

- 확인 대상 객체의 프로퍼티와, 확인 대상 객체가 상속받는 모든 프로토타입의 프로퍼티를 확인한다.

- 아래 예시코드에서 `person`객체는 `toString`이라는 프로퍼티가 없지만 `'toString' in person` 은 `true`를 반환한다.

```js
const person = {
  name: "Lee",
  address: "Seoul",
};

console.log("toString" in person); //true
// 📌 toString 이 없는 왜 true 가 나오는가 ?

const person = { name: "Lee" };

console.log(Reflect.has(person, "name")); // true
console.log(Reflect.has(person, "toString")); // true
```

`person` 객체가 속한 `프로토타입 체인` 상 존재하는 `모든 프로토타입`에서 `toString` 프로퍼티를 검색했기 때문. `toString`은 `Object.prototpye`의 메서드!

<br>

#### Object.prototype.hasOwnProperty 메서드

`Object.prototype.hasOwnProperty` 메서드를 사용해도 객체에 특정 프로퍼티가 존재하는지 확인할 수 있다.

- `인수`로 전달받은 프로퍼티 `키가 객체의 고유의 프로퍼티 키`인 경우에만 `true`를 반환하고 `상속받은 프로토타입의 프로퍼티 키`인 경우 `false`를 반환한다.

```js
console.log(person.hasOwnProperty("name")); // true
console.log(person.hasOwnProperty("age")); // false

// 상속받은 프로토타입의 프로퍼티 키 toString
console.log(person.hasOwnProperty("toString")); // false
```

<br>

---

### 14. 프로퍼티 열거

#### for...in 문

`for...in` 문 객체의 모든 프로퍼티를 열거할 때 사용한다.

```js
for(변수 선언문 in 객체) {...}
```

<br>

```js
const person = {
  name: "Lee",
  address: "Seoul",
};

// for...in 문의 변수 prop에 person 객체의 프로퍼티 키가 할당된다.
for (const key in person) {
  console.log(key + ": " + person[key]);
}
// name: Lee
// address: Seoul
```

- `for ... in` 문은 객체의 프로퍼티 개수만큼 순회한다.

- `for ... in` 문의 변수 선언문에서 선언한 변수 key 에 프로퍼티 키를 할당한다.

- `for ... in` 문은 프로퍼티가 심벌인 프로퍼티는 열거하지 않는다.

- 상속받은 프로퍼티는 제외하고 객체 자신의 프로퍼티만 열거하려면 `Object.prototype.hasOwnProperty` 메서드를 사용해야한다.

- 프로퍼티를 열거할 때 `순서`를 보장하지 않는다.

- `배열`에는 `for...in`문보다 일반 `for`문이나 `for...of`문 또는 `Array.prototype.forEach`메서드를 사용하길 권장한다.

<br>

`for ... in` 문은 상속받은 프로토타입의 프로퍼티까지 열거한다. 하지만 `toString`과 같은 `Object.prototype`의 프로퍼티가 열거되지 않는다.

```js
const person = {
  name: "Lee",
  address: "Seoul",
};

// in 연산자는 객체가 상속받은 모든 프로토타입의 프로퍼티를 확인한다.
console.log("toString" in person); // true

// for...in 문도 객체가 상속받은 모든 프로토타입의 프로퍼티를 열거한다.
// 하지만 toString과 같은 Object.prototype의 프로퍼티가 열거되지 않는다.
for (const key in person) {
  console.log(key + ": " + person[key]);
}

// name: Lee
// address: Seoul
```

이는 `toString` 메서드가 열거할 수 없도록 즉 `[[Enumerable]]`의 값이 `false`이기 때문이다.
`[[Enumerable]]` : 열거 가능 여부, `boolean` 값을 가짐

```js
// Object.getOwnPropertyDescriptor 메서드는 프로퍼티 디스크립터 객체를 반환한다.
// 프로퍼티 디스크립터 객체는 프로퍼티 어트리뷰트 정보를 담고 있는 객체다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "toString"));
// {value: ƒ, writable: true, enumerable: false 📌, configurable: true}
```

> `for ... in` 문은 객체의 `프로토타입 체인` 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 `[[Enumerable]]` 의 값이 `true`인 프로퍼티를 순회하며 열거한다.

<br>

#### Object.keys/values/entries 메서드

`for ... in` 문은 객체 자신의 고유 프로퍼티뿐 아니라 상속받은 프로퍼티도 열거한다.

**객체 자신의 고유 프로퍼티만** 열거하기 위해서는 `for ... in` 문을 사용하는 것보다. `Object.keys()`, `Object.values()`, `Object.entries()` 메서드를 사용하는 것을 권장한다.

<br>

##### object.key()

객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.

```js
let user = {
  name: "훈이",
  age: 20
  __proto__:{address:"jeju"} // 📌 열거대상에서 제외 for...in문으로는 열거 가능
};

console.log(Object.keys(user))
// ['name', 'age']

```

<br>

##### object.values()

ES8에서 도입된 메서드이며 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환한다.

```js
let user = {
  name: "훈이",
  age: 20,
};

console.log(Object.values(user));
// ['훈이', 20]
```

<br>

##### object.entries()

ES8에서 도입된 메서드이며 객체 자신의 열거 가능한 프로퍼티 [키, 값]의 쌍의 배열을 배열에 담아 반환한다.

```js
let user = {
  name: "훈이",
  age: 20,
};

console.log(Object.entries(user));
// ["name", "훈이"], ["age", 20]
```
