#### prototype 사용해서 상속기능 만들기

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

console.log(product1); // Product {name: 'shirts', price: 50000}
console.log(product2); // Product {name: 'pants', price: 60000}
console.log(product1.vat()); // 5000
console.log(product2.vat()); // 6000
```

Product라는 constructor가 가진 name, price 속성들을 그대로 물려 받아서 오브젝트를 만들어주는 게 부모가 자식에게 재산을 물려주는 것과 비슷하기 때문에 `상속`이라 부른다.

자바스크립트에는 constructor 말고도 상속 기능을 구현할 수 있는 장치가 하나 더 있다.

바로... prototype..

~~아직 모던 자바스크립트 딥다이브 1회독 중이기 때문에 읽었다는 것에 의의를 두는 수준이다~~

constructor를 만들면 prototype이라는 항목이 그 안에 몰래 생성된다!!

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

console.log(Product.prototype); // {constructor: ƒ}
```

콘솔에 찍어보면, 만든 적도 없는 prototype이라는 프로퍼티가 만들어져 있다.

요 프로토타입은 '부모의 **유전자** 역할', '자식들이 물려받을 수 있는 **유전자** 역할'을 해주는 비밀공간이다.

Product.prototype 에 뭔가 변수나 함수가 들어가있다면

Product로부터 생성되는 새로운 오브젝트들(자식들)은 전부 그걸 그대로 물려받아 쓸 수 있다.

```js
function Product(품목, 가격) {
  this.name = 품목;
  this.price = 가격;
  this.vat = function () {
    return this.price * 0.1;
  };
}

Product.prototype.weight = 100;
var product1 = new Product("shirts", 50000);
var product2 = new Product("pants", 60000);

console.log(product1.weight); // 100
console.log(product2.weight); // 100
```

같은 코드에 `Product.prototype.weight = 100;` 이 부분을 추가했는데,
Product의 유전자(프로토타입)에 `weight: 100`을 추가한 것이다.

그렇게 되면 product1, product2처럼 같은 Product로부터 생성된 모든 자식들은 weight라는 속성을 사용할 수 있게 되는 거시다~

#### 왜 자식은 부모 유전자에 등록된 값을 사용할 수 있나??

근데 대체 어떻게?? 왜때무네.. 가능한 건가?

`product1.weight`라고 사용하면 100이 출력된다. 그 이유는,

자바스크립트는 오브젝트에서 값을 출력할 때 이런 순서로 물어보기 때문이다.

1. product1에 weight가 있는지,

2. 없다면, 부모 유전자에 weight가 있는지,

3. 없다면, 부모의 부모 유전자에...

4. 없다면, 부모의 부모의 부모 유전자에... 있는가?

=> 해당 객체에 찾으려는 key가 있는지 확인하고, 찾을 때까지 더 상위 프로토타입으로 올라가는 요걸 `프로토타입 체인`이라 한다

#### 자바스크립트 내장함수를 사용할 수 있는 이유

자바스크립트 array, object들에는 붙일 수 있는 내장함수들이 많다. sort, push, toString, map, forEach 등 이런 것들을 array에 붙여서 사용한다.

메서드로 알고리즘 풀면서 어떤 원리로 사용할 수 있는지는 생각해보지 않았다.ㅎ

```js
var arr = [1, 2, 3];
console.log(arr.toString()); // 1,2,3
```

요렇게 내가 만든 배열에 `arr.toString()` 이렇게 붙일 수 있는 이유는, 내가 만든 배열의 부모 유전자가 toString()을 가지고 있기 때문이다.

그치만,, 나는 constructor로 생성하지 않았따..

그런데도 상속이 가능한 이유는 사실

```js
var arr = [1, 2, 3]; // 인간 방식
var arr = new Array(1, 2, 3); // 컴터 방식
```

위의 두 줄은 완전히 똑같은 의미를 가지기 때문이다. 우리는 위처럼 배열(객체)를 만들지만, 컴퓨터는 아래처럼 배열을 만든다.

`new Array()`는 생긴 거에서 알 수 있듯이 자식들을 만들어주는 생성자다.

```js
console.log(Array.prototype);
```

콘솔에 찍어보면 `sort, map, push, forEach` 같이 익숙한 뇨속들이 많이 보일 것이다.

Array의 자식들은 부모인 Array의 프로토타입(유전자)에 있던 함수들을 자유롭게 사용할 수 있는 것이다.
Object 자료형도 똑같이 new Object() 이런 식으로 만들어지기 때문에 부모의 prototype에 있던 함수들을 자유롭게 사용할 수 있는 것!

> prototype으로 상속시키는 것과 constructor로 상속시키는 것의 차이?

prototype에 추가된 데이터들은 자식들이 직접 가지는게 아니라 부모만 가지고 있다. 자식들이 값을 직접 소유하게 만들고 싶으면 constructor로 상속시키시면 되고, 부모만 가지고 있고 그걸 참조해서 쓰게 만들고 싶으면 prototype으로 상속시키면 된다! (그래서 상속할 수 있는 함수 같은 것들은 prototype으로 많이 만들어 놓는다고 한다..!)

#### 프로토타입의 특징

1. prototype은 constructor 함수에만 몰래 생성된다.

따라서 일반 object, array 이런 걸 만들어도 거기엔 prototype이 없다. (일반 객체 같은 뇨속들을 상속하고 싶다면 construcor 함수를 만들거나.. Object,create()나 class를 써야 한다.)

2. 부모의 prototype(유전자)를 찾고 싶다면 **proto**를 출력해보면 된다.

부모로부터 생성된 자식 object들은 **proto**라는 속성이 있다. 이걸 출력해보면 부모의 prototype이 출력된다.

그래서 **proto**는 부모의 prototype과 같은 의미다.

```js
function Product(품목, 가격) {
  this.name = 품목;
  this.price = 가격;
}

var product1 = new Product("shirts", 50000);

console.log(Product.prototype === product1.__proto__); // true
```

둘을 비교해보면 서로 같다고 출력된다!

3. **proto**를 직접 등록하면 object끼리 상속 기능을 구현할 수 있다.

```js
var 부모 = { name: "Kim" };
var 자식 = {};

자식.__proto__ = 부모;
console.log(자식.name);
```

**proto**가 부모의 유전자를 의미한다고 했다.

지금 부모와 자식 object를 하나씩 만들었다. 셋째 줄에서 자식의 **proto**에 부모를 집어넣었다.

그럼 자식의 부모 유전자는 { name : 'Kim' } 이라는 오브젝트가 된다.

그렇게 되면 자식은 이제 자식.name 속성을 자유롭게 사용할 수 있다.

#### 연습문제 네 개!

> 0. 오브젝트 자료 여러개를 만들고 싶습니다.

```js
var 학생1 = { name: "Kim", age: 20 };
var 학생2 = { name: "Park", age: 21 };
var 학생3 = { name: "Lee", age: 22 };
```

하드코딩해서 3개를 만들긴 했는데 앞으로 만들일이 더 많이 생길 것 같아서 constructor를 제작하려고 합니다.

constructor문법을 사용해서 위의 오브젝트와 똑같은 오브젝트 3개를 한번 뽑아보십시오.

- 여기에 학생1.sayHi()라고 사용하면 "안녕 나는 Kim이야" 라는 글자가 콘솔창에 나오도록 sayHi()라는 함수도 constructor 안에 추가해보세요.

```js
function Student(이름, 나이) {
  this.name = 이름;
  this.age = 나이;
  this.sayHi = function () {
    console.log(`안녕 나는 ${this.name}이야`);
  };
}

var 학생1 = new Student("Kim", 20);
var 학생2 = new Student("Park", 21);
var 학생3 = new Student("Lee", 22);

학생1.sayHi(); // 안녕 나는 Kim이야
```

sayHi 함수를 prototype에 추가해도 똑같다.

```js
function Student(이름, 나이) {
  this.name = 이름;
  this.age = 나이;
}

Student.prototype.sayHi = function () {
  console.log(`안녕 나는 ${this.name}이야`);
};

var 학생1 = new Student("Kim", 20);
var 학생2 = new Student("Park", 21);
var 학생3 = new Student("Lee", 22);

학생1.sayHi(); // 안녕 나는 Kim이야
```

> 1. 다음 코드의 출력 결과는 무엇일까요?

```js
function Parent() {
  this.name = "Kim";
}
var a = new Parent();

a.__proto__.name = "Park";
console.log(a.name);
```

1번. 바꾼 건 부모의 유전자니까 나는 그대로 Kim이다!?
2번. 부모의 유전자가 바뀌었으니 나도 그걸 상속 받아 Park이다!?

정답은 1번!

첫 4줄에 의해서 `a = { name : 'Kim' }` 이 되고, `a.__proto__.name = 'Park'`; 이건 부모 prototype에 `{ name : 'Park' }` 이걸 추가하라는 뜻이다.

`a.name` 이라고 사용했을 때, 내가 **직접** 가지고 있는 `{ name : 'Kim' }`을 우선 출력해준다.

> 2. 함수가 안들어가요 엉엉

```js
function Student(이름, 나이) {
  this.name = 이름;
  this.age = 나이;
}

Student.prototype.sayHi = () => {
  console.log("안녕 나는 " + this.name + "이야");
};
var 학생1 = new Student("Kim", 20);

학생1.sayHi(); //왜 이 코드가 제대로 안나오죠?
```

왜냐면,,, 화살표함수이기 때뮤네~

```js

...

Student.prototype.sayHi = function () {
  console.log("안녕 나는 " + this.name + "이야");
};

...

학생1.sayHi(); 안녕 나는 Kim이야
```

이렇게 바꿔주면 된다. arrow function은 그냥 일반 function 대체품이 아니다. arrow function은 this를 바깥에 있는 this를 그대로 사용하고 싶을 때 쓰는 함수다!

함수 안에서 this 키워드의 뜻은 매번 재정의된다. object안에 들어있는 함수안에 있는 this는 함수를 부른 object가 된다. 하지만 arrow function의 경우 함수 안에서 this 뜻이 재정의되지 않고 바깥에 있던 this를 사용한다.

```js
var 오브젝트 = {
  sayHi: () => {
    console.log(this);
  },
};

오브젝트.sayHi();
```

그래서 위 코드는 Window가 출력된다.

> 3. 모든 array에 적용할 수 있는 함수를 직접 새로 만들려면 어떻게 해야할까요?

모든 array에 붙일 수 있는,

array 내에 있는 3이라는 값을 제거해주는 유용한 함수를 하나 만들고 싶습니다.

```js
var arr = [1, 2, 3];
arr.remove3();

console.log(arr); //[1,2]
```

이렇게 array뒤에 붙이기만 하면 array 내의 3이라는 모든 숫자 자료들이 삭제됩니다.

멋있게 이름은 remove3() 이라고 하겠습니다.

remove3()함수는 어떻게, 어디에 만들어야 모든 array에 쓸 수 있을까요?

```js
var arr = [1, 2, 3];
// var arr = new Array(1, 2, 3)이랑 같다고 했었찌..

Array.prototype.remove3 = function () {
  // this 에서 3을 찾아서 제거해주세요~!
  for (let i = 0; i < this.length; i++) {
    if (this[i] === 3) {
      this.splice(i, 1);
    }
  }
};

arr.remove3();

console.log(arr); //[1,2]
```

> 4. remove3을 만들었는데 remove(3) 이런 식으로 원하는 데이터를 집어넣으면 제거해주는 함수로 업그레이드 해보기!

```js
var arr = [1, 2, 3, 4, 5, 6];
// var arr = new Array(1, 2, 3)이랑 같다고 했었찌..

Array.prototype.remove = function (a) {
  // this 에서 3을 찾아서 제거해주세요~!
  for (let i = 0; i < this.length; i++) {
    if (this[i] === a) {
      this.splice(i, 1);
    }
  }
};

arr.remove(4);

console.log(arr); // [1, 2, 3, 5, 6]
```

### ES5방식으로 쉽게 구현하는 상속기능

`Object.create(부모object);`

이렇게 사용하면 이 자리에 오브젝트가 하나 남는다. 그리고 소괄호 안에 적은 부모Object가 프로토타입(유전자)이 됨.

```js
var 부모 = { name: "Kim", age: 50 };
var 자식 = Object.create(부모);

console.log(자식.age); // 50
```

위와 같이 쓰면 자식이라는 object는 부모를 prototype으로 두게 됨
(자식이 성공적으로 부모 속성들을 상속받음)

자식의 age를 바꾸고 싶으면 직관적으로 바꿔주면 됨

```js
var 부모 = { name: "Kim", age: 50 };
var 자식 = Object.create(부모);
자식.age = 20;

console.log(자식.age); // 20
```

자바스크립트에서 일차적으로는 직접 키를 가지고 있는지 묻고, 없으면 부모 프로토타입에 있는지 묻기 때문에 20이 나온다.

손자도 만들어보자.

```js
var 부모 = { name: "Kim", age: 50 };
var 자식 = Object.create(부모);
자식.age = 20;

var 손자 = Object.create(자식);

console.log(손자.name); // 'Kim'
console.log(손자.age); // 20
```

없으면 바로 위 부모에게서부터 찾아 올라간다.
