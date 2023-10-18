# Interface in TypeScript

- 인터페이스는 상호 간에 정의한 약속 혹은 규칙

- 타입스크립트에서의 인터페이스는 보통 다음과 같은 범주에 대해 약속을 정의할 수 있다.

  - 객체의 스펙(속성과 속성의 타입)
  - 함수의 파라미터
  - 함수의 스펙(파라미터, 반환 타입 등)
  - 배열과 객체를 접근하는 방식
  - 클래스

```ts
let person = { name: 'jihong', age: 30 };

function logAge(obj: { name: string; age: number }) {
  console.log(obj.age); // 30
}

logAge(person); // 30

interface Person {
  name: string;
  age: number;
}

function logAge(obj: Person) {
  console.log(age);
}

logAge()의 인자는 Person 이라는 타입을 가져야한다

let person = { name: 'Capt', age: 28 };

logAge(person);
```

```ts
interface Person {
  name?: string;
  age: number;
}

여기서 속성앞에 ?를 붙이는 것을 optionalParmeter라고 하는데

- 속성을 선택적으로 적용
- 인터페이스에 정의되어 있지 않은 속성에 대해서 인지
```

```ts
interface Product {
  readonly brand: string;
}

- readonly 속성은 인터페이스로 객체를 처음 생성할 때만 값을 할당
- 이후에는 변경할 수 없는 속성

let shoe: Product = {
  brand: 'nike'
};

shoe.brand = 'adidas'; // error!

let arr: ReadonlyArray<number> = [1, 2, 3];

arr.splice(0,1); // error

arr.push(4); // error

arr[0] = 100; // error

ReadonlyArray로 선언하면 배열의 내용을 변경
```

## 객체 선언과 관련된 타입 체킹

```ts
interface Product {
  brand?: string;
}

function makeShoe(shoe: Product) {
  // ..
}
makeShoe({ brandon: 'adidas' });

// error: Object literal may only specify known properties, but 'brandon' does not exist in type 'Product'. Did you mean to write 'brand'?

타입스크립트는 친철하게 타입을 추론해준다.

타입 추론을 무시하고 싶다면 아래와 같이 선언해보자

let myShoe = { brandon: 'adidas' }';

makeShoe(myShoe as Product);
```

만약 인터페이스 정의하지 않은 속성들을 추가로 사용하고 싶을 때는 아래와 같은 방법을 사용

```ts
interface Proudct {
  brand?: string;
  [propName: string]: any;
}
```

## 함수 타입

```ts
interface Login {
  (username: string, password: string): boolean;
}

let loginUser: Login;

loginUser = function (id: string, pw: string) {
  console.log('로그인 했습니다');

  return true;
};
```

## 클래스 타입

```ts
interface Product {
  brand: string;
  nameShoe(shoe: string): void;
}

class MyShoe implements Product {
  brand: string = 'nike';

  nameShoe(b: string) {
    this.brand = b;
  }

  constructor() {}
}
```

## 인터페이스 확장

```ts
인터페이스도 인터페이스 간 확장이 가능

사실상 인터페이스를 공부해야하는 이유는 이렇게 확장이 되기때문에 외부 라이브러리나

여러사람이 협업을 할때 오버라이딩이 되기 때문이다.

interface Person {
  name: string;
}

interface Developer extends Person {
  skill: string;
}

let fe = {} as Developer;

fe.name = 'jihong';

fe.skill = 'TypeScript';
```

```ts
상속;
interface Person {
  name: string;
}

interface Developer extends Person {
  skill: string;
}

let fe = {} as Developer;

fe.name = 'jihong';

fe.skill = 'TypeScript';
```
