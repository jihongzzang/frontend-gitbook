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

### Item 13 타입과 인터페이스의 차이점 알기

- 타입스크립트에서 명명된 타입을 정의하는 방법은 두 가지가 있습니다. 다음처럼 타입을 사용할수 있다.

```ts
type State = {
  name: string;
  capital: string;
};

interface State {
  name: string;
  capital: string;
}

// 클래스의 경우 값으로도 쓰일 수 있는 자바스크립트의 런타임 개념이라 제외한다.
```

- 명명된 타입은 인터페이스로 정의하든 타입으로 정의하든 상태에는 차이가 없다.

- 인덱스 시그니처는 인터페이스와 타입에서 모두 사용이 가능하다

```ts
type Dict = {
  [key: string]: string;
};

interface Dict {
  [key: string]: string;
}
```

- 함수 타입도 인터페이스나 타입으로 정의할 수 있다.

```ts
type TFn = (x: number) => string;

interface IFn {
  (x: number): string;
}

const toStrT: TFn = (x) => '' + x;

const toStrI: IFn = (x) => '' + x;
```

```ts
type TFnWithProp = {
  (x: number): string;
  prop: string;
};

interface IFnWithProp {
  (x: number): string;
  prop: string;
}
```

- 자바스크립트에서 함수는 호출 가능한 객체!!

- 타입 별칭과 인터페이스 모두 제네릭이 사용 가능

```ts
type TPair<T> = {
  first: T;
  second: T;
};

interface IPair<T> {
  first: T;
  second: T;
}
```

- 인터페이스는 타입을 확장할 수 있으며, 타입은 인터페이스를 확장할 수 있다.!!

```ts
interface IStateWithPop extends TState {
  population: number;
}

type TStateWithPop = IState & {
  population: number;
};
```

- 인터페이스는 유니온 타입 같은 복잡한 타입을 확장하지는 못함. 복잡한 타입을 확장하고 싶을땐 타입별칭과 & 를 사용하자

- 한편 클래스를 구현할때는 타입과 인터페이스 둘다 사용 가능

```ts
class StateT implements TState {
  name: string = '';
  capital: string = '';
}

class StateI implements IState {
  name: string = '';
  capital: string = '';
}
```

- 유니온 타입은 들어본적 있겠지만 유니온 인터페이스라는 개념은 없다.

유니온 타입을 확장하는게 필요할때 하나의 변수명으로 매핑하는 인터페이스를 만들 수는 있다.

```ts
type Input = {};
type Output = {};

interface VariableMap {
  [name: string]: Input | Output;
}
```

또는 유니온 타입에 name 속성을 붙인 타입을 만들 수도 있다.

```ts
type NamedVariable = (Input | Output) & { name: stirng };

// 이타입은 interface로 표현 불가
```

- 튜플과 배열 타입도 type 키워드를 이용해 간결하게 표현이 가능
- 인터페이스로 튜플을 구현할 수 는 있지만 실제로 튜플에서 사용하는 메서드를 사용할 수는 없음

- 인터페이스 타입에는 보강이 가능하다.

```ts
interface IState {
  name: string;
  capital: string;
}

interface IState {
  population: number;
}

const p: IState = {
  name: 'a',
  capital: 'b',
  population: 10000,
};
```

- 속성을 확장하는 것을 `declaration merging` 이라고 함

- 복잡한 타입이라면 타입 별칭을 사용하자. 그러나 타입과 인터페이스 두 가지 방법으로 모두 표현 할 수 있는 간단한 객체
- 타입이라면 일관성과 보강의 관점에서 고려해보자.

- 프로젝트의 스타일에 맞게 사용해야함 api에 대한 타입 선언을 작성해야 하나면 인터페이스를 사용하는게 좋음, 왜냐면 병합이 가능하기 때문에 그러나 프로젝트 내부적으로 사용되는 타입에 선언 병합이 발생하는 것은 잘못된 설계이다.

### Item 14 타입 연산과 제네릭 사용으로 반복 줄이기

```ts
// need Refactor Code

const surfaceArea = (r, h) => 2 * Math.PI * r * (r + h);

const volume = (r, h) => 2 * Math.PI * r * r * h;

for (const [r, h] of [
  [1, 1],
  [1, 2],
  [1, 3],
]) {
  console.log(
    `Cylinder ${r} * ${h}`,
    `Surface area: ${surfaceArea(r, h)}`,
    `Volume: ${volume(r, h)}`
  );
}

interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate {
  firstName: string;
  lastName: string;
  birth: Date;
}
```

- 반복을 줄이는 가장 간단한 방법은 타입에 이름을 붙이는 것

```ts
function distance(a: { x: number; y: number }, b: { x: number; y: number }) {
  return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}

interface Point2D {
  x: number;
  y: number;
}

function distance(a: Point2D, b: Point2D) {}

function get(url: string, opts: Options): Promise<Response> {
  /*...*/
}

function post(url: string, opts: Options): Promise<Response> {
  /*...*/
}

type HTTPFunction = (url: string, opts: Options) => Promise<Response>;

const get: HTTPFunction = (url, opts) => {
  /*...*/
};

const post: HTTPFunction = (url, opts) => {
  /*...*/
};
```

```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}

interface TopNavState {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
}

//refactored

interface TopNavState {
  userId: State['userId'];
  pageTitle: State['pageTitle'];
  recentFiles: State['recentFiles'];
}

// 여전히 반복되는 코드가 존재함

interface TopNavState {
  [k in ('userId' | 'pageTitle' | 'recentFiles')]: State[k];
}
```

- 매핑된 타입은 배열의 필드를 루프 도는 것과 같은 방식인데 이 패턴을 `Pick`이라고 한다.

```ts
type Pick<T, K> = { [k in K]: T[k] };

type TopNavState = Pick<State, 'userId' | 'pageTitle' | 'recentFiles'>;
```

- Pick은 제네릭 타입이다.

- 태그된 유니온에서도 중복이 발생 할 수 있다.

```ts
interface SaveAction {
  type: 'save';
  //...
}
interface LoadAction {
  type: 'load';
  //...
}

type Action = SaveAction | LoadAction;
type ActionType = 'save' | 'load';

//refactored
type ActionType = Action['type']; //"save" | "load"

type ActionRec = Pick<Action, 'type'>; //{ type : "save" | "load" }

// 둘은 다름
```

- 생성하고 난 다음에 업데이트가 되는 클래스를 정의한다면

```ts
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}

interface OptionsUpdate {
  width?: number;
  height?: number;
  color?: string;
  label?: string;
}

class UIWidget {
  constructor(init: Options);
  update(options: OptionsUpdate) {
    /*...*/
  }
}

// 매핑된 타입과 keyof를 사용하면 Options로부터 OptionsUpdate를 쉽게 만들 수 있다.

type OptionsUpdate = { [k in keyof Options]?: Options[k] };
```

- `keyof`는 타입을 받아서 속성 타입의 유니온을 반환한다.

```ts
type OptionsKeys = keyof Options; // "width" | "height" | "color" | "label"
```

- 해당 패턴은 일반적이며 `Partial` 이라는 이름으로 포함되어 있음

```ts
class UIWidget {
  constructor(init: Options);
  update(options: Partial<Options>) {
    /*...*/
  }
}
```

- 값의 형태에 해당하는 타입을 정의하고 싶을 때

```ts
const INIT_OPTIONS = {
  width: 640,
  height: 480,
  color: '#00FF00',
  label: 'VGA',
};

interface Options {
  width: number;
  height: number;
  color: string;
  height: string;
}

type Options = typeof INIT_OPTIONS;
```

- 함수나 메서드의 반환 값에 명명된 타입을 만들고 싶을 때 조건부 타입이 필요하다.

```ts
function getUserInfo(userId: string) {
  /*...*/

  return {
    userId,
    naem,
    age,
    height,
    //...
  };
}

type UserInfo = ReturnType<typeof getUserInfo>; // 신중하게 사용하자
```

- 제네릭 타입은 타입을 위한 함수라고 생각하자.

- 제네릭 타입에서 매개변수를 제한하는 방법은 `extends`를 사용하자

```ts
interface Name {
  first: string;
  last: string;
}

type Duo<T extends Name> = [T, T];

const duo: Duo<Name> = [
  { first: 'A', last: 'a' },
  { first: 'B', last: 'b' },
];

const duo2: Duo<{ first: string }> = [{ first: 'A' }, { first: 'B' }];

// 에러 방출 (last 속성이 없다.)

//타입스크립트에서는 선언부에 항상 제네릭 매개변수를 작성하게 되어있음.

const duoFunc = <T extends Name>(x: Duo<T>) => x;

const duo = duoFunc([
  { first: 'A', last: 'a' },
  { first: 'B', last: 'b' },
]);

const duo2 = duoFunc([{ first: 'A' }, { first: 'B' }]); //에러 방출 (last 속성이 없다.)
```

```ts
type Pick<T, K> = {
  [k in K]: T[k];
};

// K는 T타입과 무관하고 K의 타입이 너무 많다. 에러

type Pick<T, K extends keyof T> = {
  [k in K]: T[k];
};

// 정상 K는 실제로 T의 key 부분 집합, 즉 keyof T가 되어야 한다.

type FirstLast = Pick<Name, 'first' | 'last'>;
type FirstMiddle = Pick<Name, 'first' | 'middle'>; // 에러
```

- 타입에 이름을 붙여서 반복을 피하자.

- 타입들 간의 매핑을 위해 제공한 도구들을 공부하면 좋다.

- 제네릭 타입은 타입을 위한 함수와 같다.

- 제네릭 타입을 제한하려면 `extends` 를 사용하자

- 표준 라이브러리에 정의된, `Pick`, `Partial`, `ReturnType`과 같은 제네릭 타입에 익숙해지자.
