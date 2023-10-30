# 3장

## 타입 추론

### 추론 가능한 타입을 사용해 장황한 코드 방지하기

- 타입스크립트는 결국 타입을 위한 언어이다. 하지만 변수를 선언할때마다 타입을 명시할 필요는 없다. (체커 타입추론)

```ts
let x: number = 12;
//  refactored
let x = 12;

const person: {
  name: string;
  born: {
    where: string;
    when: string;
  };
  died: {
    where: string;
    when: strin;
  };
} = {
  name: 'a',
  born: {
    where: 'a',
    when: 'b',
  },
  died: {
    where: 'c',
    when: 'd',
  },
};

//refactored

const person = {
  name: 'a',
  born: {
    where: 'a',
    when: 'b',
  },
  died: {
    where: 'c',
    when: 'd',
  },
};
```

- 타입스크립트는 입력 받아 연산을 함수가 어떤 타입을 반환하는지도 알고 있음

```ts
function square(nums: number[]) {
  return nums.map((x) => x * x);
}

const squares = square([1, 2, 3, 4]); // type number[]
```

- 타입스크립트는 더 정확하게 추론하기도 한다.

```ts
const axis1: string = 'x'; //type string
const axis2 = 'y'; //type "y"

interface Product {
  id: number;
  name: string;
  price: number;
}

function logProduct(product: Product) {
  const id: number = product.id;
  const name: string = product.name;
  const price: number = product.price;
}

// 그렇지만 id에 무자도 들어 있을 수 있음을 나중에 알게 되었다고 생각해보자.

// 타입스크립트는 에러를 내뱉을 것이다. 이럴때는 비구조화 할당문을 사용해 구현하자
```

- 비구조화 할당문을 통해 사용하기

```ts
function logProduct(product: Product) {
  const { id, name, price } = product;
}
```

- 비구조화 할당문은 모든 지역 변수의 타입이 추론되도록 한다. 여기에 추가로 명시적 타입 구문을 넣으면 코드가 번잡해질 뿐임.

```ts
function logProduct(product: Product) {
  const { id, name, price }: { id: number; name: string; price: number } =
    product;
}
```

- 정보가 부족해서 타입스크립트가 판단하기 어려울땐 명시적 타입구문을 사용하자. (ex 매개변수)

- 타입스크립트에서 변수의 타입은 일반적으로 처음 등장할 때 결정

- 타입스크립트에서 기본값이 있을때는 타입 구문을 생략하는 경우도 존재

```ts
function parseNum(str: string, base = 10) {
  //...
}
```

- 타입이 추론될 수 있음에도 여전히 타입을 명시하고 싶은 상황

1. 객체 리터럴을 정의 할때

```ts
const a: Product = {
  name: 'a',
  id: 1,
  price: 28,
};

//a 의 경우 잉여 속성 체크가 동작함.

//따라서 변수가 사용되는 순간이 아닌 할당되는 순간에 오류가 표시됨 (따라서 실수가 발생한 지점에 오류를 내뿜음)
```

- 마찬가지로 함수의 반환에도 타입을 명시하여 오류를 방지 할 수 있다.

```ts
function getQ(ticker: string) {
  return fetch(`${api}/?q=${ticker}`).then((response) => response.json());
}

//캐시 추가로직

const cache: { [ticker: string]: number } = {};

function getQ(ticker: string) {
  if (ticker in cache) {
    return cache[ticker];
  }
  return fetch(`${api}/?q=${ticker}`)
    .then((response) => response.json())
    .then((quote) => {
      cache[ticker] = quote;
      return quote;
    });
}

// 하지만 getQ는 항상 Promise를 반환하므로 if구문에는 프로미스가 반환되도록 해야한다.
// 이때 의도된 반환타임 (Promise<number>)를 명시한다면 정확한 위치에 오류가 표시됨
```

- 반환 타입을 명시했을때 함수에 대해 명확하게 알 수 있다.

- 미리 타입을 명시하는 방법은, TDD와 유사하다.

- 명명된 타입을 사용하기 위해 반환값의 타입을 명시한다.

- 추론되는 반환 타입이 복잡해 질수록 명명된 타입을 제공하는 이점은 커진다.

### Item 20 다른 타입에는 다른 변수 사용하기

```ts
let id = '12-34-56';

fetchProduct(id);

id = 123456;

fetchProductBySerialNumber(id);

//에러!!
```

- 변수의 값은 바뀌지만 타입은 보통 바뀌지 않는다.

- 타입을 바꿀수 있는 한 가지 방법은 범위를 좁히는 것이다.

```ts
//refactored

let id: string | number = '12-34-56';

//...

//유니온 타입으로 코드가 동작하기는 하겠지만, 매번 매개변수가 어떤 타입인지 확인해야해서 복잡하다.

//별도의 변수를 도입하자.

const id: string = '12-34-56';

//...

const serial: number = '12-34-56';
```

- `const`로 변수를 선언하면 코드가 간결해지고 타입체커가 타입을 추론하기에도 좋다.

- 타입이 바뀌는 변수는 되도록 피하자.

- 재사용되는 변수와, 가련지는 변수를 혼동하지는 말자

```ts
const id = '12-34-56';

fetchProduct(id);

{
  const id = 123456;
  fetchProductBySerialNumber(id);
}

//여기서 두 id는 서로 아무런 관계가 없다. 하지만 혼동을 줄 수 있다.
```

- 목적이 다른 곳에는 별도의 변수명을 사용하자

### Item 21 타입 넓히기

- 런타임에 모든 변수는 유일한 값을 가진다.

- 타입스크립트는 정적 분석시점에, 변수는 가능한 값들의 집합인 타입을 가진다.

- 따라서 상수를 사용해서 변수를 초기화 할때 타입을 명시하지 않으면 타입 체커는 타입을 결정해야한다.

- 이러한 과정을 `넓히기`라고 한다.

```ts
interface Vector3 {
  x: number;
  y: number;
  z: number;
}

function getComponent(vector: Vector3, axis: 'x' | 'y' | 'z') {
  return vector[axis];
}

let x = 'x';
let vec = { x: 10, y: 20, z: 30 };

getComponent(vec, x);
//stirng 어쩌고는 할당 할수 없다.
```

- 타입을 넓히다가 할당이 불가능하다고 말함

```ts
const mixed = ['x', 1];
```

이것만 보고 상당히 많은 타입을이 추론된다.

- 넓히기 과정을 제어하는 첫번째 방법은 `const`

- 하지만 `const`의 경우 배열에서는 여전히 문제를 보여줌. 객체도 마찬가지

- 객체의 경우 타입스크립트의 넗히기 알고리즘은 각 요소를 `let`으로 할당된 것처럼 다룬다.

- 타입 추론의 강도를 제어하기위해서는,

  - 명시적 타입 구문 제거
  - 타입 체커에 추가적인 정보를 제공
  - const 단언문을 사용하기

- `const`단언문은 타입스크립트의 기법

```ts
const v1 = {
  x: 1,
  y: 2,
}; // { x: number; y: number; }

const v2 = {
  x: 1 as const,
  y: 2,
}; // { x: 1; y: number; }

const v3 = {
  x: 1,
  y: 2,
} as const; // { readonly x: 1; readonly y: 2; }

const a1 = [1, 2, 3]; // number[]

const a2 = [1, 2, 3] as const; // readonly [1, 2, 3]
```

- 상수의 타입을 추론하는 법을 이해하자

- 동작에 영향을 줄 수 있는 방법인 `const`, `타입 구문`, `문맥`, `as const`에 익숙해지자.

### Item 22 타입 좁히기

- 타입 좁히기는 타입 넓히기에 반대이다.

```ts
//분기문
const el = document.getElementById('foo');

if (el) {
  el;
  el.innderHTML = 'xxx'.blink();
} else {
  el;
  alert('No element #foo');
}
```

- 분기처리를 통해 타입 좁히기를 구현

- 타입 별칭이 존재하는 경우 타입 좁히기를 잘 못 할 수도 있음

```ts
//분기문에서 예외를 던지거나 함수를 반환하기

const el = document.getElementById('foo');

if (!el) throw new Error('Unable to find #foo');

el;

el.innerHTML = 'xxx'.blink();
```

```ts
//instance 사용

function contains(text: string, search: string | RegExp) {
  if (search instanceof RegExp) {
    search; //type RegExp
    return !!search.exec(text);
  }
  search; //type string
  return text.includes(search);
}
```

```ts
// 속성체크
interface A {
  a: number;
}

interface B {
  b: number;
}

function pickAB(ab: A | B) {
  if ('a' in ab) {
    ab; // type A
  } else {
    ab; // type B
  }

  ab; //type A|B
}
```

```ts
// 내장 함수로 좁히기
function contains(text: string, terms: string | string[]) {
  const termList = Array.isArray(terms) ? terms : [terms];

  termList; // string[]
}
```

- 조건문에서 타입을 좁히는데 매우 능하다.

- 타입을 좁히는 또 다른 방법은 명시적 `태그`를 붙이는 것

```ts
interface UploadEvent {
  type: 'upload';
  //...
}

interface DownloadEvent {
  type: 'download';
  //...
}

type AppEvent = UploadEvent | DownloadEvent;

function handleEvent(e: AppEvent) {
  switch (e.type) {
    case 'download':
      e; // 타입이 DownloadEvent
      break;

    case 'upload':
      e; // 타입이 UploadEvent
      break;
  }
}
```

- 타입스크립트가 타입을 식별하지 못할때는 함수를 도입할 수도 있음

```ts
//type Guard

function isInputElement(el: HTMLElement): el is HTMLInputElement {
  return 'value' in el;
}

function getElementContent(el: HTMLElement) {
  if (isInputElement(el)) {
    el; //type HTMLInputElement
    return el.value;
  }
  el; //type HTMLElement
  return el.textContent;
}
```

```ts
const a = ['a', 'b', 'c'];

const members = ['a'].map((who) => a.find((n) => n === who));

// 타입이 (string | undefined)[]

const members = ['a']
  .map((who) => a.find((n) => n === who))
  .filter((who) => who !== undefined);

// 타입이 (string | undefined)[]

//refactored

function isDefined<T>(x: T | undefined): x is T {
  return x !== undfined;
}

const member = ['a'].map((who) => a.find((n) => n === who)).filter(isDefined);
```

- 분기문 외에도 여러 종류의 제어 흐름을 살피며 타입스크립트가 타입을 좁히는 과정을 이해하자.

- 태그된/구별된 유니온과 사용자 정의 타입 가드를 사용하여 타입 좁히기 과정을 원활하게 만들 수 있다.
