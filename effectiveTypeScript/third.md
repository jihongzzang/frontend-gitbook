# 2장

## 타입스크립트의 타입 시스템

### Item 11 잉여 속성 체크의 한계 인지하기

타입이 명시된 변수에 객체 리터럴을 할당할 때 타입스크립트는 해당 타입의 속성이 있는지 그외의 속성은 없는지 확인

```ts
interface Room {
  doors: number;
  height: number;
}

const r: Room = {
  doors: 1,
  height: 10,
  xxx: 'present',
}; // 에러 xxx 속성은 없어 (잉여 속성 체크라는 과정이 수행)

// 하지만 구조적 타이핑 관점에서 이런 경우는 에러를 뱉지 않음

const obj = {
  doors: 1,
  height: 10,
  xxx: 'present',
};

const r: Room = obj;

// obj의 타입은 Room타입의 부분집합을 포함하기 때문에 할당 가능하며 체커도 통과
```

```ts
interface Options {
  title: string;
  darkMode?: boolean;
}

function createWindow(options: Options) {
  if (options.darkMode) {
    setDarkMode();
  }
  //...
}

createWindow({
  title: 'Spider Solitaire',
  dakrmode: true, //대문자가 달라서 에러
});

const o1: Options = document; // 정상
const o2: Options = new HTMLAnchorElement(); // 정상
```

```ts
const o: Options = { darkmode: true, title: 'hi' }; //객체 리터럴

const intermediate = { darkmode: true, title: 'hi' }; //객체 리터럴이 아님

const o = intermediate; // 객테리터럴이 아니기때문에 할당했을때 잉여 속성 체크가 적용되지 않음
```

- 잉여 속성 체크는 타입 단언문에서도 적용되지 않음.

```ts
const intermediate = { darkmode: true, title: 'hi' } as Options; //이것도 정상

//타입 단언문보다 타입 선언문을 사용해야하는 이유중 하나임!!
```

- 인덱스 시그니처를 사용해서 잉여속성 체크를 피하는 방법

```ts
interface Options {
  darkMode?: boolean;
  [otherOptions: string]: unknown;
}

const o: Options = { darkmode: true };
```

- weak 타입에도 비슷한 체크가 동장

```ts
interface LineChartOptions {
  logscale?: boolean;
  invertedYAxis?: boolean;
  areaChart?: boolean;
}

const opts = { logScale: true };

const o: LineChartOptions = opts; // 에러
```

- 객체 리터럴을 변수에 할당하거나 함수에 매개변수로 전달할 때 잉여 속성 체크 수행

- 잉여 속성 체크는 오류를 찾는 효과적인 방법이지만, 타입스크립트 타입 체커가 수행하는 일반적인 구조적 할당 가능성 체크와 역할이 다르다.

- 임시 변수를 도입하면 잉여 속성 체크를 건너 뛸 수 있다.

### Item 12 함수 표현식에 타입 적용하기

- 함수 문장과 함수 표현식을 다르게 인식한다.

```ts
function rollDice1(sides: number): number; // 문장

const rollDice2 = function rollDice2(sides: number): number {
  /*...*/
}; // 표현식

const rollDice2 = (sides: number): number => {
  /*...*/
}; // 표현식
```

- 타입스크립트에서는 함수 표현식을 사용하는 것이 좋다. (매개변수부터 반환값까지 전체를 함수 타입으로 선언하여 함수 표현식에 재사용할 수 있다는 장점이 있기 때문이다.)

```ts
type DiceRollFn = (sides: number) => number;

const rollDice: DiceRollFn = (sides) => {
  /*...*/
};

// 이미 sides를 number로 인식하고 있음
```

- 함수 타입의 선언은 불필효한 코드의 반복을 줄인다.

```ts
function add(a: number, b: number) {
  return a + b;
}
function sub(a: number, b: number) {
  return a - b;
}
function mul(a: number, b: number) {
  return a * b;
}
function div(a: number, b: number) {
  return a / b;
}

type BinaryFn = (a: number, b: number) => number;

const add: BinaryFn = (a, b) => a + b;
const sub: BinaryFn = (a, b) => a - b;
const mul: BinaryFn = (a, b) => a * b;
const div: BinaryFn = (a, b) => a / b;
```

```text
라이브러리르 직접 만들고 있다면 공통 콜백 함수를 위한 타입 선언을 제공하자 (ex, MouseEventHandler)
```

시그니처가 일치하는 다른 함수가 있을때도 함수 표현식에 타입을 적용해 보자

```ts
const responseP = fetch('....'); //Promise<Response>

async function getQuote() {
  const response = await fetch('...');
  const quote = await response.json();

  return quote;
}

//만약에 존재하지 않는 api 라면 에러를 응답할텐데 json형식이 아니라면? => json형식이 아니라는 새로운 오류 메세지를 담아
//rejected 프로미스를 반환할것이다. 그래서 실제 오류가 감춰질 수 있다. 심지어

// refactored

declare function fetch(
  input: RequestInfo,
  init?: RequestInit
): Promise<Response>;

async function checkedFetch(input: RequestInfo, init?: RequestInit) {
  const response = await fetch(input, init);

  if (!response.ok) {
    throw new Error(`Requested faild: ${response.status}`);
  }

  return response;
}

// refactored

const checkedFetch: typeof fetch = (input, init) => {
  const response = await fetch(input, init);

  if (!response.ok) {
    throw new Error(`Requested faild: ${response.status}`);
  }

  return response;
};
```

- 함수의 매개 변수에 타입 선언을 하는 것보다 함수 표현식 전체 타입을 정하는 것이 코드도 간결하고 안전함!!

- 다른 함수의 시그니처와 동일한 타입을 가지는 새 함수를 작성하거나, 동일한 타입 시그니처를 가지는 여러 개의 함수를 작성할 때는 매개변수의 타입과 반환 타입을 반복해서 작성하지 말고 함수 전체의 타입 선언을 적용하자.

- 다른 함수의 시그니처를 참조하려면 `typeof fn`을 사용하자
