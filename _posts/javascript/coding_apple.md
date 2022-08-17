```js
var 사람 = {
  name: "Kim",
  age: 30,
  nextAge() {
    return this.age + 1;
  },
};

console.log(사람.age + 1); // 31
console.log(사람.nextAge()); // 31
```

똑같은데 굳이 왜 만들어 쓰는거냐?

- object 안의 데이터가 복잡할수록 함수 만들어 놓는게 데이터 꺼내기 쉽다.

- 내부에 있는 name, age 변수를 건드리지 않아서 실수를 방지할 수 있어서 안전하다.

```js
var 사람 = {
  name: "Kim",
  age: 30,
  setAge(나이) {
    this.age = parseInt(나이);
  },
};

사람.setAge("200");

console.log(사람); // {name: 'Kim', age: 200, setAge: ƒ}
```

set 키워드를 사용하면 소괄호 안 써도 된다.

```js
var 사람 = {
  name: "Kim",
  age: 30,
  get nextAge() {
    return this.age + 1;
  },
  set setAge(나이) {
    this.age = parseInt(나이);
  },
};

사람.setAge = "20";

console.log(사람); // {name: 'Kim', age: 20}
console.log(사람.nextAge); // 21
```

위에는 데이터를 변경하는 함수니까 set을 쓰고,
데이터를 꺼내 쓰는 함수라면 get을 쓴다.

setter 함수는 파라미터가 있어야 하고,
getter 함수는 파라미터가 있으면 안 된다.

클래스 안에서 함수를 만들 때도 똑같다.

```js
class 사람 {
  constructor() {
    this.name = "Park";
    this.age = 20;
  }
  get nextAge() {
    return this.age + 1;
  }
  set setAge(나이) {
    this.age = 나이;
  }
}

var 사람1 = new 사람();

사람1.age = 30;

console.log(사람1.nextAge); // 31
```

#### class, extends, getter, setter 연습문제 5개

> 1. 직접 class 구조 만들어보기

갑자기 강아지 SNS를 만들고 싶어서 자바스크립트로 열심히 코딩하던 중,

여러 강아지 정보들을 담은 유사한 오브젝트 자료형을 테스트삼아 몇개 만들려고 합니다.

```js
var 강아지1 = { type: "말티즈", color: "white" };
var 강아지2 = { type: "진돗개", color: "brown" };
```

이렇게 쭉 많이 만들고 싶은데 하드코딩하기 싫어서 class를 만들어 강아지 오브젝트들을 뽑고 싶습니다.

그럼 class를 어떻게 만드는게 좋을까요?

```js
class Dog {
  constructor(견종, 색깔) {
    this.type = 견종;
    this.color = 색깔;
  }
}

var 강아지1 = new Dog("말티즈", "white");
var 강아지2 = new Dog("진돗개", "brown");

console.log(강아지1); // Dog {type: '말티즈', color: 'white'}
console.log(강아지2); // Dog {type: '진돗개', color: 'brown'}
```

> 2. 이번엔 고양이관련 object들을 만들고 싶습니다.

이번엔 class를 이용해 고양이 오브젝트 여러개를 뽑고 싶습니다.

```js
var 고양이1 = { type: "코숏", color: "white", age: 5 };
var 고양이2 = { type: "러시안블루", color: "brown", age: 2 };
```

type, color는 이전에 만든 강아지 object와 유사한데

고양이들만 특별하게 age라는 속성을 하나 더 추가하고 싶습니다. 어떻게 class를 만들면 될까요?

1번 문제에서 만들었던 강아지 class와 유사하기 때문에 extends라는 문법을 쓰는 것도 좋겠군요.

```js
class Dog {
  constructor(견종, 색깔) {
    this.type = 견종;
    this.color = 색깔;
  }
}

class Cat extends Dog {
  constructor(견종, 색깔, 나이) {
    super(견종, 색깔); // 나이 없어도 됨 (super()함수는 Dog의 constructor()를 그대로 복붙했다~라고 생각)
    this.age = 나이;
  }
}

var 고양이1 = new Cat("코숏", "white", 5);
var 고양이2 = new Cat("러시안블루", "brown", 2);

console.log(고양이1); // Cat {type: '코숏', color: 'white', age: 5}
console.log(고양이2); // Cat {type: '러시안블루', color: 'brown', age: 2}
```

> 3. 고양이와 강아지 object들에 기능을 하나 추가하고 싶습니다.

모든 고양이와 강아지 object들은 .한살먹기() 라는 함수를 사용할 수 있습니다.

(1) 한살먹기 함수는 강아지 class로부터 생성된 오브젝트가 사용하면 콘솔창에 에러를 출력해주어야합니다.

(2) 한살먹기 함수는 고양이 class로 부터 생성된 오브젝트가 사용하면 현재 가지고있는 age 속성에 1을 더해주는 기능을 실행해야합니다.

한살먹기 함수는 어떻게 만들면 좋을까요? (검색이 필요할 수 있습니다)

```js
class Dog {
  constructor(견종, 색깔) {
    this.type = 견종;
    this.color = 색깔;
  }
}

class Cat extends Dog {
  constructor(견종, 색깔, 나이) {
    super(견종, 색깔, 나이);
    this.age = 나이;
  }
  한살먹기() {
    console.log(this.age + 1);
  }
}

var 강아지1 = new Dog("말티즈", "white");
var 강아지2 = new Dog("진돗개", "brown");
var 고양이1 = new Cat("코숏", "white", 5);
var 고양이2 = new Cat("러시안블루", "brown", 2);

console.log(강아지1.한살먹기()); // Uncaught TypeError: 강아지1.한살먹기 is not a function
console.log(강아지2.한살먹기()); // Uncaught TypeError: 강아지2.한살먹기 is not a function
console.log(고양이1.한살먹기()); // 6
console.log(고양이2.한살먹기()); // 3
```

ㅇㅣ게 맞나??!?!?!!?

```js
class Dog {
  constructor(타입, 칼라) {
    this.type = 타입;
    this.color = 칼라;
  }
  한살먹기() {
    if (this instanceof Cat) {
      this.age++;
    }
  }
}

class Cat extends Dog {
  constructor(타입, 칼라, 나이) {
    super(타입, 칼라);
    this.age = 나이;
  }
}
```

자바스크립트는 `instanceof` 라는 고마운 연산자가 있습니다.

`a instanceof b` 이렇게 쓰시면 `a가 b로부터 생성된 오브젝트인지 아닌지`를 true/false로 알려주는 연산자입니다.

그래서 한살먹기()라는 함수를 만들고 this.age++를 해주는 기능을 넣었는데 이 기능은 this가 instanceof Cat인 경우에만 실행하도록 if문을 추가했습니다.

그럼 이제 Cat으로 부터 생성된 오브젝트들만 한살먹기() 내부 기능을 사용가능합니다.

> 4. get/set을 이용해봅시다

자바스크립트로 간단한 게임 기능을 가진 오브젝트를 뽑는 class를 만들고 싶습니다.

다음 조건에 따라 class를 만들어보세요. class 이름은 Unit이라고 합시다.

(1) 모든 Unit의 인스턴스는 공격력, 체력 속성이 있으며 기본 공격력은 5, 기본 체력은 100으로 설정되어 있어야 합니다.

(2) 모든 Unit의 인스턴스는 전투력을 측정해주는 battlePoint라는 getter가 있습니다.

`console.log( 인스턴스.battlePoint )` 이렇게 사용하면 현재 공격력과 체력을 더한 값을 콘솔창에 출력해주어야합니다.

(3) 모든 Unit의 인스턴스는 heal이라는 setter가 있습니다.

인스턴스.heal = 50 이렇게 사용하면 체력 속성이 50 증가해야합니다.

\*인스턴스는 class로부터 새로생성되는 오브젝트를 뜻합니다.

```js
class Unit {
  constructor() {
    this.체력 = 100;
    this.공격력 = 5;
  }
  get battlePoint() {
    return this.체력 + this.공격력;
  }
  set heal(a) {
    this.체력 = this.체력 + a;
  }
}

var 쎈애 = new Unit();

console.log(쎈애.battlePoint); // 105
쎈애.heal = 50;
```

> 5. get/set을 이용해봅시다2

다음과 같은 오브젝트가 있습니다.

```js
var data = {
  odd: [],
  even: [],
};
```

(1) data 오브젝트안에 setter 역할 함수를 하나 만들어보십시오.

setter 함수에 1,2,3,4 이렇게 아무 자연수나 파라미터로 입력하면 홀수는 odd, 짝수는 even 이라는 속성에 array 형태로 저장되어야합니다.

(2) data 오브젝트안에 getter 역할 함수를 하나 만들어보십시오.

getter 함수를 사용하면 odd, even에 저장된 모든 데이터들이 숫자순으로 정렬되어 출력되어야합니다.

예를 들면

`data.setter함수(1,2,3,4,5)` 이렇게 입력하면

`data = { odd : [1,3,5], even : [2,4] }`

이렇게 저장이 되어야합니다.

빨리 위의 역할을 하는 함수 두개를 data 오브젝트 내에 만들어보십시오.

<!-- ```js
var data = {
  odd: [],
  even: [],
  set setter함수(arguments) {
    for (let i = 0; i <= arguments.callee.length; i++) {
      if (arguments.callee[i] % 2 === 0) {
        this.odd.push(arguments.callee[i]);
      } else {
        this.even.push(arguments.callee[i]);
      }
    }
  },
};
``` -->

**(1) data 오브젝트에는 setter 역할 함수가 하나 필요합니다.**

setter 함수에 1,2,3,4 이렇게 아무 자연수나 파라미터로 입력하면 홀수는 odd, 짝수는 even 이라는 속성에 array 형태로 저장되어야합니다.

```js
var data = {
  odd: [],
  even: [],
  setter함수: function (...숫자들) {
    숫자들.forEach((a) => {
      if (a % 2 == 1) {
        this.odd.push(a); //홀수일때
      } else {
        this.even.push(a); //짝수일때
      }
    });
  },
};

data.setter함수(1, 2, 3);
```

set 키워드는 취향에 따라 알아서 추가해보시길 바랍니다.

**(2) data 오브젝트에는 getter 역할 함수가 하나 필요합니다.**

getter 함수를 사용하면 odd, even에 저장된 모든 데이터들이 숫자순으로 정렬되어 출력되어야합니다.

```js
var data = {
  odd: [1, 3],
  even: [2, 4, 6],
  get getter함수() {
    return [...this.odd, ...this.even].sort();
  },
};

console.log(data.getter함수);
```

... 기호로 spread operator 문법을 이용하시면 this.odd 그리고 this.even이라는 array 두개를 쉽게 합칠 수 있습니다.

그리고 그걸 출력해주는 getter함수를 만들었구염.

근데 출력하기 전에 정렬하고 싶으면 .sort()만 뒤에 붙이시면 됩니다.

그럼 문자정렬 해주는데 문자정렬이 아니라 숫자정렬을 잘 해주셔야하니

숫자정렬하는 법은 구글에 찾아서 한번 적용해보시길 바랍니다.

<!-- ?? -->

> constructor는 object 복사 기계!

object를 마구 찍어내고 싶을 때 사용한다.

(비슷한 object 여러 개 만들 때)

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

<!-- 프로토타입 -->

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

<!-- this -->

this 의미 정리를 하자면

1. 그냥 일반 함수에서 쓰면 window

2. 오브젝트 내의 함수(메소드)에서 쓰면 함수를 동작시키는 오브젝트

가 출력된다고 배워봤습니다.

```js
document.getElementById("버튼").addEventListener("click", function (e) {
  var 어레이 = [1, 2, 3];
  어레이.forEach(function () {
    console.log(this);
  });
});
```

> Q. 위의 코드에서 this를 출력하면 무엇이 나올까요?

: 일반함수니까 Window 나오게찌

```js
var 오브젝트 = {
  이름들: ["김", "이", "박"],
  함수: function () {
    오브젝트.이름들.forEach(function () {
      console.log(this);
    });
  },
};

오브젝트.함수();

// Window {0: Window, window: Window, self: Window, document: document, name: '', location: Location, …}
// Window {0: Window, window: Window, self: Window, document: document, name: '', location: Location, …}
// Window {0: Window, window: Window, self: Window, document: document, name: '', location: Location, …}
```

> Q .그럼 여기 안에서의 this값을 출력하면 뭐가나올까요?

: 얘도 일반함수니까 Window 나오지 않겠니?

```js

```

```js
var 오브젝트 = {
  이름들: ["김", "이", "박"],
  함수: function () {
    console.log("this1", this);
    오브젝트.이름들.forEach(() => {
      console.log("this2", this);
    });
  },
};

오브젝트.함수();

// this1 {이름들: Array(3), 함수: ƒ}
// this2 {이름들: Array(3), 함수: ƒ}
// this2 {이름들: Array(3), 함수: ƒ}
// this2 {이름들: Array(3), 함수: ƒ}
```

> 화살표 함수는 함수 내의 this 값을 변경 시키지 않음.

: 오브젝트 내의 함수(메소드)에서 쓰면 함수를 동작시키는 오브젝트인 `오브젝트`가 세 번 출력된다.

```js
console.log(1, this);
var 오브젝트 = {
  함수: function () {
    console.log(2, this);
  },
};

오브젝트.함수();

// 1 Window {0: Window, window: Window, self: Window, document: document, name: '', location: Location, …}
// 2 {함수: ƒ}
```

```js
console.log(1, this);
var 오브젝트 = {
  함수: () => {
    console.log(2, this);
  },
};

오브젝트.함수();

// 1 Window {0: Window, window: Window, self: Window, document: document, name: '', location: Location, …}
// 2 Window {0: Window, window: Window, self: Window, document: document, name: '', location: Location, …}
```

---

### this & arrow function 연습문제 3개

#### 1. 간단한 메소드 만들기

사람이라는 오브젝트가 하나 있습니다.

이 오브젝트에 sayHi라는 함수를 (메소드를) 하나 추가하고 싶습니다.

```js
var 사람 = {
  name: "손흥민",
};

사람.sayHi(); //안녕 나는 손흥민
```

위의 코드처럼 사람.sayHi()라고 작성하면 콘솔창에 '안녕 나는 손흥민' 이라는 글자가 나와야합니다.

'손흥민'이라고 이름을 하드코딩해서 출력하기보다는 실제 내 오브젝트에 있는 name에 해당하는 값이 출력되면 참 좋을 것 같군요.

> Q. sayHi()라는 함수를 어디서 어떻게 만들면 될까요?

```js
var 사람 = {
  name: "손흥민",
  sayHi: function () {
    return `안녕 나는 ${this.name}`;
  },
};

사람.sayHi(); //안녕 나는 손흥민
```

화살표 함수를 사용하면 this가 Window이기 때문에 앙댕당

```js
var 사람 = {
  name: "손흥민",
  sayHi: () => {
    return `안녕 나는 ${this.name}`;
  },
};

사람.sayHi(); // '안녕 나는 '
```

#### 2. 오브젝트 내의 데이터를 전부 더해주는 메소드 만들기

오브젝트가 하나 있습니다.

```js
var 자료 = {
  data: [1, 2, 3, 4, 5],
};
```

그런데 이 오브젝트에 전부더하기() 라는 함수를 하나 만들어서 사용하고 싶습니다.

```js
var 자료 = {
  data: [1, 2, 3, 4, 5],
};

자료.전부더하기();
```

위처럼 자료.전부더하기()라고 쓰면 자료.data 안에 있는 모든 숫자를 더해서 콘솔창에 출력해주어야합니다.

(아마 15가 뜨게 되겠죠?)

> Q. 어떻게 코드를 짜면 될까요? <br>
> 조건) 위에 있는 자료라는 object 중괄호 {} 내에 코드 작성 금지

```js
var 자료 = {
  data: [1, 2, 3, 4, 5],
};

자료.전부더하기 = function () {
  let sum = 0;
  this.data.forEach((a) => {
    sum += a;
  });
  return sum;
};

자료.전부더하기();
```

마찬가지로 화살표 함수를 쓰면 this가 Window를 가리키리 때문에 에러가 뜬다

```js
var 자료 = {
  data: [1, 2, 3, 4, 5],
};

자료.전부더하기 = () => {
  let sum = 0;
  this.data.forEach((a) => {
    sum += a;
  });
  return sum;
};

자료.전부더하기(); // Uncaught TypeError: Cannot read properties of undefined
```

#### 3. setTimeout 이용해보기

자바스크립트엔 setTimeout이라는 유용한 함수가 있습니다.

특정 코드나 함수를 X초 후에 실행하고 싶을 때 사용하는 함수입니다.

어떻게 쓰냐면

```js
setTimeout(콜백함수, ms단위의 시간)
```

이렇게 쓰면 됩니다. 예를 들어보면

```js
setTimeout(function () {
  console.log("안녕");
}, 1000);
```

이렇게 한줄 쓰시면 1000ms 후에 왼쪽에 있는 콜백함수 내의 코드를 실행해줍니다.

그럼 1초후에 안녕이 콘솔창에 출력되겠네요.

또는 콜백함수자리에 여러분이 직접 만든 함수를 넣을 수도 있습니다.

역시 예를 들어보면

```js
function 함수() {
  console.log("안녕");
}

setTimeout(함수, 1000);
```

이렇게 콜백함수 대신 내가 미리 만들어놓은 함수의 이름을 적어도 실행됩니다.

다만 콜백함수자리에 내가만들어놓은 함수를 입력하고 싶으면 소괄호는 빼셔야합니다.

setTimeout 개념정리 끝입니다.

본격 문제들어갑니다.

버튼을 클릭하면 지금 누른 버튼에 담긴 글자를 출력하는 기능을 만들고 싶어서 이렇게 코드를 짰습니다.

```html
<button id="버튼">버튼이에요</button>

<script>
  document.getElementById("버튼").addEventListener("click", function () {
    console.log(this.innerHTML);
  });
</script>
```

> Q. 그럼 setTimeout을 이용해서 1초 후에 this.innerHTML을 콘솔창에 출력하고 싶으면 어떻게 코드를 수정해야할까요? <br>
> 1초 후에 '버튼이에요'라는 글자가 콘솔창에 등장하면 성공입니다.

```html
<button id="버튼">버튼이에요</button>

<script>
  document.getElementById("버튼").addEventListener("click", function () {
    console.log(this.innerHTML); // 버튼이에요
    setTimeout(function () {
      console.log(this.innerHTML); // undefined
    }, 1000);
  });
</script>
```

이렇게 하면 undefined 출력됨

왜?

this에서 가장 가까운 function을 보면 일반함수라서 this는 Window임

위의 this와 똑같이 나오게 하려면 뭐를 쓴다? arrow function.

```html
<script>
  document.getElementById("버튼").addEventListener("click", function () {
    setTimeout(() => {
      console.log(this.innerHTML);
    }, 1000);
  });
</script>
```

<!-- 클래스 -->

### ES6방식으로 안 쉽게 구현하는 상속기능 (class)

```js
class 부모 {
  constructor() {
    this.name = "Kim";
    // this.sayHi = function () {
    //   console.log("hello");
    // }; // 1번: 자식이 직접 함수를 가짐
  }
  sayHi() {
    console.log("hello"); // 2번: 부모.prototype에 추가됨
  }
}

var 자식 = new 부모();

console.log(자식.__proto__); // {constructor: ƒ, sayHi: ƒ}
console.log(자식.__proto__ === 부모.prototype); // true

console.log(Object.getPrototypeOf(자식)); // {constructor: ƒ, sayHi: ƒ}
console.log(Object.getPrototypeOf(자식) === 자식.__proto__); // true
console.log(Object.getPrototypeOf(자식) === 부모.prototype); // true
```

'부모의 프로토타입을 출력해주세용~'을 위와 같이 세 가지 방법으로 쓸 수 있다.

```js
class 부모 {
  constructor() {
    this.name = "Kim";
    // this.sayHi = function () { // 1번: 자식이 직접 함수를 가짐
    //   console.log("hello");
    // };
  }
  sayHi() {
    // 2-1번: 부모.prototype에 추가됨
    console.log("hello");
  }
  // sayHello() { // 여러 개 추가 가능
  //   console.log("Hello");
  // }
}

부모.prototype.sayHello = function () {
  // 2-2번
  console.log("Hello");
};

var 자식 = new 부모();
```

2-1번처럼 안에다가 써줘도 되고, 2-2번처럼 추가해줘도 된다.

```js
class 부모 {
  constructor(이름) {
    this.name = 이름;
  }
  sayHi() {
    console.log(`Hello, ${this.name}`);
  }
}

var 자식 = new 부모("서형");

console.log(자식.sayHi()); // Hello, 서형
```

class의 constructor 안에 파라미터를 추가할 수 있다.

### class를 복사하는 extends / super

```js
class 할아버지 {
  constructor(name) {
    this.성 = "Kim";
    this.이름 = name;
  }
}

// var 할아버지1 = new 할아버지('땡땡');
// console.log(할아버지1); // 할아버지 {성: 'Kim', 이름: '땡땡'}

class 아버지 extends 할아버지 {
  constructor() {
    this.나이 = 50;
  }
}

var 아버지1 = new 아버지();
console.log(아버지1); // Uncaught ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
```

extends해서 만든 class는 this를 그냥은 쓰지 못한다. super() 다음에 써야 한다.
super는 부모 class의 constructor를 의미한다.

```js
class 할아버지 {
  constructor(name) {
    this.성 = "Kim";
    this.이름 = name;
  }
}

class 아버지 extends 할아버지 {
  constructor() {
    super(); // 요기다가!!!!!
    this.나이 = 50;
  }
}

var 아버지1 = new 아버지();
console.log(아버지1); // 아버지 {성: 'Kim', 이름: undefined, 나이: 50}
```

이때 이름이 undefined로 뜨게 되는데, 파라미터를 사용하고 싶다면 똑같이 써주면 된다.

```js
class 할아버지 {
  constructor(name) {
    this.성 = "Kim";
    this.이름 = name;
  }
}

class 아버지 extends 할아버지 {
  constructor(name) {
    super(name); // 요기다가!!!!!
    this.나이 = 50;
  }
}

var 아버지1 = new 아버지("댕댕");
console.log(아버지1); // 아버지 {성: 'Kim', 이름: '댕댕', 나이: 50}
```

근데 파라미터가 두 개 이상이라면???

```js
class 할아버지 {
  constructor(name, name1) {
    this.성 = "Kim";
    this.이름 = name;
    this.이름1 = name1;
  }
}
var 할아버지1 = new 할아버지("땡땡", "땅땅");
console.log(할아버지1); // 할아버지 {성: 'Kim', 이름: '땡땡', 이름1: '땅땅'}

class 아버지 extends 할아버지 {
  constructor(name, name1) {
    super(name, name1); // 요기다가!!!!!
    this.나이 = 50;
  }
}

var 아버지1 = new 아버지("댕댕", "냥냥");
console.log(아버지1); // 아버지 {성: 'Kim', 이름: '댕댕', 이름1: '냥냥', 나이: 50}
```

super()에는 또다른 용도가 있다!!

```js
class 할아버지 {
  constructor(name) {
    this.성 = "Kim";
    this.이름 = name;
  }
  sayHi() {
    // 추가한 함수
    console.log("안녕 나는 할아버지");
  }
}

var 할아버지1 = new 할아버지("땡땡");
console.log(할아버지1.sayHi()); // 안녕 나는 할아버지

class 아버지 extends 할아버지 {
  constructor(name) {
    super(name);
    this.나이 = 50;
  }
  sayHi() {
    // 추가한 함수
    console.log("안녕 나는 아버지");
  }
}

var 아버지1 = new 아버지("댕댕");
console.log(아버지1.sayHi()); // 안녕 나는 아버지
```

요기까진 뭐, 제일 가까운 것부터 찾으니까 출력되는 결과가 쉽게 이해된다.

요기는 암기해야 한다.

```js
class 할아버지 {
  constructor(name) {
    this.성 = "Kim";
    this.이름 = name;
  }
  sayHi() {
    console.log("안녕 나는 할아버지");
  }
}

var 할아버지1 = new 할아버지("땡땡");
console.log(할아버지1.sayHi()); // 안녕 나는 할아버지

class 아버지 extends 할아버지 {
  constructor(name) {
    super(name); // 여기서 super는 부모 class의 constructor 의미함
    this.나이 = 50;
  }
  sayHi() {
    console.log("안녕 나는 아버지");
    super.sayHi(); // 여기서 super는 부모 class의 prototype을 의미함
  }
}

var 아버지1 = new 아버지("댕댕");
console.log(아버지1.sayHi()); // 안녕 나는 아버지 안녕 나는 할아버지
```

super가 어디서 쓰이냐에 따라 역할이 달라진다.

1. constructor 안에서 쓰면 부모 class의 constructor

2. prototype 함수 안에서 쓰면 부모 class의 prototype

#### 예제 문제

```js
class 할아버지 {
  constructor(name) {
    this.성 = "Kim";
    this.이름 = name;
  }
}

class 아버지 extends 할아버지 {
  constructor(name) {
    super(name);
    this.나이 = 50;
  }
}
```

할아버지 constructor()에 name이라는 파라미터가 있던걸 그대로 아버지 constructor()에도 따라했습니다.

(파라미터 작명은 자유롭게 가능합니다)

이제 new 아버지(); 할 때 파라미터를 입력하면 this.이름 속성에 들어가게 되겠네요.

그럼 예상해봅시다.

> Q. 위 코드 하단에 `var a = new 아버지('만수');` 이렇게 적으면 a라는 변수는 어떤 내용을 가지고 있을까요?

{ 성: 'Kim', 이름: '만수', 나이: 50 }

```js
class 할아버지 {
  constructor(name) {
    this.성 = "Kim";
    this.이름 = name;
  }
  sayHi() {
    console.log("안녕 나는 할아버지");
  }
}

class 아버지 extends 할아버지 {
  constructor(name) {
    super(name);
    this.나이 = 50;
  }
  sayHi2() {
    console.log("안녕 나는 아버지");
    super.sayHi();
  }
}

var a = new 아버지("만수");
```

> Q. 그럼 위의 예제 코드에서 a.sayHi2()를 실행하면 무엇이 콘솔창에 출력될까요?

'안녕 나는 아버지'
'안녕 나는 할아버지'
