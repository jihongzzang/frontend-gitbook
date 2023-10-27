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

### Item 8 타입 공간과 값 공간의 심벌 구분하기

- 심벌은 이름이 같더라도 속한 공간에 따라 다르다.

```ts
interface Cylinder {
  raidus: number;
  height: number;
}

const Cylinder = (radius: number, height: number) => ({ radius, height });

function calculater(shape: unknown) {
  if (shape instanceof Cylinder) {
    shape.radius;
    //"{}" 형식에 "radius" 속성이 없다.
  }
}

type T1 = 'string literal';
type T2 = 123;

const v1 = 'string literal';
const v2 = 123;
```

- 서로 아무런 연관이 없음

- 타입스크립트 코드에서 타입과 값은 번갈아 나올 수 있다.

```ts
interface Person {
  first: string;
  last: string;
}

const p: Person = { first: 'Jane', last: 'Jacobs' };

function email(p: Person, s: string, b: string): Response {
  //값 타입
}

class Cylinder {
  radius = 1;
  height = 1;
}

function calc(shape: unknown) {
  if (shape instanceof Cylinder) {
    shape; // (o)
    shape.radius; // (o)
  }
}
```

- 클래스가 타입으로 쓰일때는 형태가 사용되지만, 값으로 쓰일때는 생성자가 사용

```ts
const v = typeof Cylinder; //값이 "function"
type T = typeof Cylinder; //타입이 typeof Cylinder

declare let fn: T;

const c = new fn(); //타입이 Cylinder

// 제네릭을 사용해 생성자 타입과 인스턴스 타입 전환하기

type C = InstanceType<typeof Cylinder>; //타입이 Cylinder

const first: Person['first'] = p['first'];

type PersonEl = Person['first' | 'last']; //타입은 string
type Tuple = [string, number, Date];
type TupleEl = Tuple[number]; // 타입은 string|number|Date
```

- 타입스크립트에서 구조 분해 할당시

```ts
function email({
  person,
  subject,
  body,
}: {
  person: Person;
  subject: string;
  body: string;
}) {}
```

- 타입스크립트 코드를 읽을 때 타입인지 값인지 잘 구분하자

- 값은 타입을 가지지만, 타입은 값을 가지지 않는다.

- class나 enum은 값과 타입 두가지로 사용될 수 있다.

- typeof, this 등의 연산자들과 키워드들은 타입 공간과 값 공간에서 다른 목적으로 사용될 수 있다.

### Item 9 타입 단언보다는 타입 선언을 사용하기

```ts
interface P {
  name: string;
}

const alice: P = { name: 'alice' }; // 타입은 P

const bob = { name: 'bob' } as P; // 타입은 P
```

- 실제로 앞에 엘리스는 타입 선언을 해서 선언된 타입임을 명시함 하지만 두번쨰 밥은 타입 단언을 수행.

```ts
const alice: P = {}; // 에러

const bob = {} as P; // 에러가 안 남

// 선언은 값이 해당 인터페이스를 만족하는지 검사

// 단언은 강제로 타입을 지정해서 오류를 무시하라고 하는것과 동일 이는 속성을 추가할때도 마찬가지

const people = ['a', 'b', 'c'].map((name) => ({ name } as P)); // 타입은 P[] 하지만 런타임 시에 문제발생

const people = ['a', 'b', 'c'].map((name) => ({} as P)); // 오류 없음 하지만 런타임 시에 문제발생
```

- 단언문을 쓰지 않고 올바르게 리팩토링

```ts
const people = ['a', 'b', 'c'].map((name): P => ({ name }));
```

- 타입 단언이 꼭 필요한 경우에는 우리가 확실하게 타입스크립트보다 더 잘 알고 있을때 DOMElement에 대해서는 우리가 더많이 알기때문에 사용하는것이 타당하다.

```ts
const el = document.getElementById('foo')!; //HTMLElement
```

하지만 타입 단언문으로 임의의 타입 간에 변환을 할 수는 없다. 서브타입에서 동작한다.

정 바꾸고 싶으면

```ts
el = document.body as unkonw as P; //에러는 나지 않지만 위험하다.
```

- 타입 단언보다는 타입 선언을 사용하자

- 화살표 함수의 반환 타입을 명시하는 방법에 대해 알자

- 내가 타입스크립트보다 더 잘 알고 있을때는 단언문을 사용하는것이 타당하다.

### Item 10 객체 래퍼 타입 피하기

자바스크립트에는 객체 이외에도 기본형 값들이 있다. (string, boolean, number, null, undefined, symbol...)

- 기본형들은 불면이며 메서드를 가지지 않는다.

```ts
'aaaaa'.charAt(3);

// 이는 string의 메서드가 아니며, 실제로 자바스크립트는 해당 메서드를 사용할때 String객체로 래핑하고, 메서드를 호출하고, 마지막에 래핑한 객체를 버린다.
```

- 타입스크립트는 기본형과 객체 래퍼 타입을 별도로 모델링함.

따라서 헷갈리지말고 쓰자

- 타입스크립트는 기본형 타입을 객체래퍼에 할당하는 선언을 허용하나 그냥 쓰지말자

- 기본형 값에 메서드를 제공하기 위해 객체 래퍼 타입이 어떻게 쓰이는지 이해해야한다. 직접 사용하거나 인스턴스를 생성하는것은 피하자.
