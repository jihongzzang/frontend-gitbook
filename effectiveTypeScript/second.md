# 2장

## 타입스크립트의 타입 시스템

### Item 6 편집기를 사용해 타입 시스템 탐색

```text
- 타입스크립트 컴파일러 (tsc)

- 단독으로 실행 할 수 있는 타입스크립트 서버 (tsserver)
```

- 타입스크립트는 기본적으로 추론 기능이 존재

  - 타입 넓히기와 좁히기의 개념을 더 잘 이해하고 싶다면 조건문에서 조건들이 어떤지 살펴보기\

  - 객체에서는 개별 속성들을 꼼꼼하게 살펴보기

```ts
function logMessage(message: string | null) {
  if (message) {
    message; // type string
  }
}

const foo = {
    x: [1,2,3] // type number[]

  bar: { name: 'Fred' },
};
```

- 타입 오류를 살펴보는 것도 타입 시스템의 성향을 파악하는데 좋은 방법

```ts
function getElement(elOrId: string | HTMLElement | null): HTMLElement {
  if (typeof elOrId === 'object') {
    return elOrId; //에러 null 도 "object"
  } else if (elOrId === null) {
    document.body;
  } else {
    const el = document.getElementById(elOrId);
    return el; // 애러 null은 HTMLElement에 할당 할 수 없다.
  }
}
```

- lib.dom.d.ts를 살펴보는 것도 좋은 방법이다.

### Item 7 타입이 값들의 집합이라고 생각하기

변수에는 다양한 값들을 할당 할 수 있음 (ex: 42, null, undefined, "Canada", etc...)

- 타입스크립트가 오류를 체크하는 순간에는 타입을 가지고 있다.

- 모든 숫자값의 집합을 number타입

- 가장 작은 집합은 아무 값도 포함하지 않는 공집합, 타입스크립트에서는 never 타입. never 타입으로 선언된 변수의 범위는 공집합!!!

- 그 다음으로 작은 집합은 한 가지 값만 포함하는 타입인데 unit타입이라고도 불리는 리터럴 타입

- 여기서 두 개 또는 세 개이상로 묶으려면 유니온 타입을 사용

```ts
const x: never = 12;
에러;

type A = 'A';
type B = 'B';
type Tweleve = 12;

type AB = 'A' | 'B';
```

연산자 & 두 타입의 인터섹션을 계산한다.

```ts
interface Person {
  name: string;
}

interface LifeSpan {
  birth: Date;
  death?: Date;
}

type PersonSpan = Person & LifeSpan;

const ps: PersonSpan = {
  name: 'hi',
  birth: new Date('1912/06/23'),
  death: new Date('1912/06/23'),
}; //정상이야

// 타입 연산자는 인터페이스의 속성이 아닌 값의 집합에 적용이 된다. 그리고 추가적인 속성을 가지는 값도 여전히 그 타입에 속한다.
```

규칙이 속성에 대한 인터섹션에 관해서는 맞지만, 두 인터페이스의 유니온에서는 다름

```ts
type K = keyof (Person | LifeSpan); //type never

keyof (A & B) = (keyOf A) | (keyOf B)
keyof (A | B) = (keyOf A) & (keyOf B)

//refactored

interface PersonSpan extends Person{}
```

- `extends` 키워드는 제네릭 타입에서 한정자로도 쓰이며, ~의 부분집합을 의미하기도 함

```ts
function getKey<K extends string>(val: any, key: K) {
  // ...
}

// string을 상속한다.

getKey({}, 'x'); // (o)
getKey({}, Math.random() < 0.5 ? 'a' : 'b'); // (o)
getKey({}, document.title); // (o)
getKey({}, 12); // (x)
```

- 여기가 좀 어려운데 위에 string을 상속한다는 의미를 객체 상속으로 생각하지 말고 집합의 관점에서 string의 부분 집합 범위를 가지는 어떤 타입이 된다고 이해하자

```ts
interface Point {
  x: number;
  y: number;
}

type PointKeys = keyof Point; // 타입은 "x" | "y"

function sortBy<K extends keyof T, T>(vals: T[], key: K): T[] {}

const pts: Point[] = [
  { x: 1, y: 1 },
  { x: 2, y: 0 },
];

sortBy(pts, 'x'); //(o)

sortBy(pts, 'y'); //(o)

sortBy(pts, Math.random() < 0.5 ? 'x' : 'y'); //(o)

sortBy(pts, 'z'); //(x)
```

- 타입을 값의 집합으로 생각하자
- 타입스크립트 타입은 겹쳐지는 집합으로 표현되어진다.
- 한 객체의 추가적인 속성이 타입선언에 언급되지 않더라도 그 타입에 속할 수 있다.
- 타입 연산은 집합의 범위에 적용
- `A는 B를 상속`, `A는 B에 할당 가능`, `A는 B의 서브타입` 이런 말들은 `A는 B의 부분집합`이라고 생각하자.
