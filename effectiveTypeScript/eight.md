# 4장

## 타입 설계

### Item 32 유니온의 인터페이스보다는 인터페이스의 유니온을 사용하기

- 유니온 타입의 속성을 가지는 인터페이스를 작성할때는 인터페이스의 유니온 타입을 사용하는게 더 맞지 않을까 검토해보자.

```ts
interface Layer {
  layout: FillLayout | LineLayout | PointLayout;
  paint: FillPaint | LinePaint | PointPaint;
}

//서로 맞지않는 타입이 생길수도 있음

//refactored

//각각 타입의 계층을 분리된 인터페이스로 두기

interface FillLayer {
  type: 'fill';
  layout: FillLayout;
  paint: FillPaint;
}

interface LineLayer {
  type: 'line';
  layout: LineLayout;
  paint: LinePaint;
}

interface PointLayer {
  type: 'point';
  layout: PointLayout;
  paint: PointPaint;
}

type Layer = FillLayer | LineLayer | PointLayer;
```

- 해당 패턴의 가장 일반적인 예시는 태그된 유니온이다.

- 타입 속성은 `태그`이며 런타임 시에 어떤 타입의 레이어가 사용되는지 판단하는데 쓰임

```ts
function drawLayer(layer: Layer) {
  if (layer.type === 'fill') {
    const { paint, layout } = layer;
  } else if (layer.type === 'line') {
    const { paint, layout } = layer;
  } else {
    const { paint, layout } = layer;
  }
}

//타입의 속성들 간의 관계를 제대로 모델링하면, 타입스크립트가 정확성을 체크하는데 도움이된다.
```

- 여러개의 선택적 필드가 동시에 값이 있거나 동시에 `undefined`인 경우도 태그된 유니온 패턴이 잘 맞는다

```ts
interface Person {
  name: string;
  placeOfBirth?: string;
  dateOfBirth?: Date;
}

// refactored

// 두 개의 속성을 하나의 객체로 모으는 것이 더 나은 설계이다. null 값을 경계로 두는 방법과 비슷하다.

interface Person {
  name: string;
  birth?: {
    place: string;
    date: Date;
  };
}
```

- 타입의 구조를 손 댈 수 없는 상황이면, 앞서 다룬 인터페이스의 유니온을 사용해서 속성 사이의 관계를 모델링하자

```ts
interface Name {
  name: string;
}

interface PersonWithBirth extends Name {
  placeOfBirth?: string;
  dateOfBirth?: Date;
}

type Person = Name | PersonWithBirth;
```

- 유니온 타입의 속성을 여러 개 가지는 인터페이스에서는 속성 간의 관계가 분명하지 않기때문에 주의해야한다.
- 유니온의 인터페이스보다 인터페이스의 유니온이 더 정확하고 타입스크립트가 이해하기도 좋다.
- `태그`를 활용하는것은 타입스크립트가 제어 흐름을 분석하는데 도움을 준다.

### Item 33 string 타입보다 더 구체적인 타입 사용하기

- `string`타입의 범위는 매우 넓기 때문에, 더 좁은 타입이 적절하지는 않은지 생각하자

```ts
interface Album {
  artist: string;
  title: string;
  releaseDate: string; // YYYY-MM-DD
  recordingType: string; // "live" or "studio"
}

// string 타입이 남발되었고 주석을 확인했을때도 현재 인터페이스가 잘못 설계되었다. (recordingType => xxx 가능)

// refactored

type RecordingType = 'studio' | 'live';

interface Album {
  artist: string;
  title: string;
  releaseDate: Date;
  recordingType: RecordingType;
}
```

- 타입을 명시적으로 정희함으로써 다른 곳으로 값이 전달되어도 타입정보가 유지된다.

- 타입을 명시적으로 정의하고 해당 타입의 의미를 설명하는 주석을 붙여 넣을 수 있다.

- `keyof`연산자로 세밀하게 객체의 속성 체크가 가능해진다.

```ts
function getALbumsOfType(recordingType: RecordingType): Album[] {
  //,,,
}
function pluck(records, key) {
  return records.map((r) => r[key]);
}

//refactored (타입 체크가 되지만 정밀하지 못하다. 특히 any를 사용하는것은 지양해야한다.)

function pluck(records: any[], key: string): any[] {
  return records.map((r) => r[key]);
}

//refactored

function pluck<T>(records: T[], key: string): any[] {
  return records.map((r) => r[key]); //"{}" 형식에 인덱스 시그니처가 없으므로 어쩌고...
}

//refactored
function pluck<T>(records: T[], key: keyof T): T[keyof T][] {
  return records.map((r) => r[key]);
}

const releaseDates = pluck(albums, 'releaseDate'); // (string | Date)[]
// key의 값으로 하나의 문자열을 넣게 되면, 범위가 너무 넓어서 적절하지 않음

//refactored 범위를 더 좁혀보자
type K = keyof Album;

function pluck<T, K extedns keyof T>(records: T[], key: K): T[K][] {
  return records.map((r) => r[key]);
}

pluck(albums, 'releaseDate'); // Date[]
pluck(albums, 'artist'); // string[]
pluck(albums, 'recordingType'); // RecordingType[]
```

- `문자열을 남발하여 선언된` 코드를 피하자.
- 변수의 범위를 보다 정확하게 표현하고 싶을땐 `문자열 리터럴 타입의 유니온`을 사용하자
- 객체의 속성이름을 함수 매개변수로 받을 때는 `string`보다 `keyof T`를 사용하자

### Item 34 부정확한 타입보다는 미완성 타입을 사용하기

- 타입 선언을 작성하다가 코드의 동작을 더 구체적으로 또는 덜 구체적으로 모델링하게 되는 상황

- 타입선언의 정밀도를 높이는 일에는 주의를 기울이자

```ts
interface Point {
  type: 'Point';
  coordinates: number[];
}

interface LineString {
  type: 'LineString';
  coordinates: number[][];
}

interface Polygon {
  type: 'Polygon';
  coordinates: number[][];
}

type Geometry = Point | LineString | Polygon;

type GeoPosition = [number, number];

interface Point {
  type: 'Point';
  coordinates: GeoPositiong;
}

//타입 선언을 세밀하게 만들고자 했지만 타입이 부정확해졌다.

//다른 예제 Lisp과 비슷한 언어의 타입선언

type Expression1 = any;
type Expression2 = number | string | any[];

const tests: Expression2[] = [
  10,
  'red',
  true,
  ['+', 10, 5],
  ['case', ['>', 20, 10], 'red', 'blue', 'green'],
  ['rgb', 255, 0, 127, 0],
];

type FnName = '+' | '-' | '*' | '/' | '>' | '<' | 'case' | 'rgb';

type CallExpression = [FnName, ...any[]];

type Expression3 = number | string | CallExpression;

//---

type Expression4 = number | string | CallExpression;

type CallExpression = MathCall | CaseCall | RGBCall;

interface MathCall {
  0: '+' | '-' | '/' | '*' | '>' | '<';
  1: Expression4;
  2: Expression4;
  length: 3;
}

interface CaseCall {
  0: 'case';
  1: Expression4;
  2: Expression4;
  3: Expression4;
  length: 4 | 6 | 8 | 10 | 12 | 14 | 16; // 등등
}

interface RGBCall {
  0: 'rgb';
  1: Expression4;
  2: Expression4;
  3: Expression4;
  length: 4;
}

// 무조건 타입을 세밀하게 나누는것이 개선은 아니다.
```

- 타입을 정제 할때, 매우 추상적인 타입(any)는 정제하는 것이 좋다. 하지만 타입이 구체적으로 정제된다고해서 정확도가 올라가지는 않는다.

- 정확하게 타입을 모델링할 수 없다면, 부정확하게 모델링하지말아야한다. 또한 `any`와 `unknown`을 구별해서 사용하자

- 타입 정보를 구체적으로 만들수록 오류 메세지와 자동완성 기능에 주의를 기울이자.

### Item 35 데이터가 아닌, API와 명세를 보고 타입 만들기

- 코드의 구석 구석까지 타입 안정성을 얻기 위해 API 또는 데이터 형식에 대한 타입 생성을 고려하자

- 데이터에 드러나지 않는 예외적인 경우들이 문제가 될 수 있기 때문에 데이터보다는 명세로부터 코드를 생성하는 것이 좋다.
