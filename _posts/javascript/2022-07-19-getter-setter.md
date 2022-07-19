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

- object 안의 데이터가 복잡할 수록 함수 만들어놓는게 데이터 꺼내기 쉽다.

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

### class, extends, getter, setter 연습문제 5개

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
