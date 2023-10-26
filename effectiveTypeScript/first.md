# 1장

## 자바스크립트와 타입스크립트

### Item 1 타입스크립트 알아보기

- 타입스크립트는 자바스크립트의 상위집합!!

  - 모든 자바스크립트 프로그램은 타입스크립트이다.(o)

  - 모든 타입스크립트 프로그램은 자바스크립트 프로그램이다. (x)

- 문법의 유효성과 동작의 이슈는 독립적인 문제

```text
타입시스템의 목표 중 하나는 런타임에 오류를 발생시킬 코드를 미리찾아 내는것!
```

**`타입구문을 추가하자`**

- 명시적으로 타입선언하기

```ts
const states = [
  { name: 'a', capital: 'A' },
  { name: 'b', capital: 'B' },
  { name: 'c', capital: 'C' },
];

// refactored;

interface State {
  name: string;
  capital: string;
}

const states: State[] = [
  { name: 'a', capital: 'A' },
  { name: 'b', capital: 'B' },
  { name: 'c', capital: 'C' },
];
```

- 타입스크립트는 자바스크립트의 런타임 동작을 모델링하는 타입시스템을 가지고 있다.

- 하지만 런타임 오류를 발생시키는 코드는 충분히 동작할 수 있다.

### Item 2 타입스크립트의 설정

`noImplicityAny` 와 `strictNullChecks`

- `noImplicityAny` 는 변수들이 미리 정의된 타입을 가져야 하는지 여부를 제어

  - 타입정보를 가질 때 효과적이기 때문에 되도록이면 설정하자

- `strictNullChecks` 는 `null`과 `undefined`가 모든 타입에서 허용되는지 확인하는 설정

```ts
const x: number = null; 체크 해제되었을때 유효한 코드

const x: number = null; 체크 설정시 오류
```

- 가능한 둘다 초반에 설정하자. 마이그레이션시에는 규모를 판단해서 설정

### Item 3 코드 생성과 타입이 관계 없음

- 최신 TS 와 JS를 브라우저에서 동작할 수 있도록 트랜스파일

- 코드의 타입오류 체크

하지만 이 둘은 완전히 독립적이다.

- 타입 오류가 있는 코드도 컴파일이 가능하다.

- 만약에 오류가 있을 때 컴파일 하지 않으려면, `noEmitOnError` 를 설정하자.

- 런타임에는 타입체크가 불가능하다

```ts
interface Square {
  width: number;
}

interface Rectangle extends Square {
  height: number;
}

type Shape = Square | Rectangle;

- 부정확한 코드

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) {
    //형식만 참조하지만 값으로 사용되었다

    return shape.width * shape.height;
    //shape형식에는 height속성이 없다.
  } else {
    return shape.width * shape.width;
  }
}

- 리팩토링 코드

function calculateArea(shape: Shape) {
  if ("height" in Shape) {
    shape; // 타입이 Rectangle
    return shape.width * shape.height;
  } else {
    shape; // 타입이 Square
    return shape.width * shape.width;
  }
}
```

- 타입정보를 유지하는 또 다른 방법으로 런타임에 접근 가능한 타입정보를 명시적으로 저장하는 `태그` 기법을 활용하자

```ts
interface Square {
  kind: 'square';
  width: number;
}

interface Rectangle extends Square {
  kind: 'rectangle';
  width: number;
  height: number;
}

type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape.kind === 'rectangle') {
    shape; // 타입이 Rectangle
    return shape.width * shape.height;
  } else {
    shape; // 타입이 Square
    return shape.width * shape.width;
  }
}
```

- 타입과 값을 둘다 사용하는 기법 `class`

- **인터페이스** 는 타입으로만 사용 **클래스**는 타입과 값 모두 사용 가능

```ts
class Square {
  constructor(public width: number) {}
}

class Rectangle extends Square {
  constructor(public width: number, public height: number) {
    super(width);
  }
}

type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) {
    shape; // 타입이 Rectangle
    return shape.width * shape.height;
  } else {
    shape; // 타입이 Square
    return shape.width * shape.width;
  }
}
```

- 타입연산은 런타임에 영향을 주지 않는다.

```ts
function asNumber(val: number | string): number {
  return val as number;
}

function asNumberConvertedJS(val) {
  return val;
}

// refactored;

function asNumber(val: number | string): number {
  return typeof val === 'string' ? Number(val) : val;
}
```

- 런타임 타입은 선언된 타입과 다를 수 있다.

```ts
function setLightSwitch(value: boolean) {
  switch (value) {
    case true:
      turnLightOn();
      break;
    case false:
      turnLightOff();
      break;
    default:
      console.log('실행되지 않습니다');
  }
}

마지막을 실행 시킬할 수 있는 경우가 무엇일까?
```

- 타입스크립트에서도 마지막 같은 코드를 실행 시킬 수 있는 방법

```js
interface LightApiResponse {
  lightSwitchValue: boolean;
}

async function setLight() {
  const response = await fetch(`${api}`);

  const result: LightApiResponse = await response.json();

  setLightSwitch(result.lightSwitchValue);
}

- 실제로 그렇게 될까?

- 실제로 결과값이 문자열이었다면 런타임에 함수까지 전달 될 것이다.

- 타입스크립트에서는 런타임 타입과 선언된 타입이 맞지 않을 수 있다. (매우 중요!!)
```

- 타입스크립트에서 타입으로는 함수를 오버로드 할 수 없다.

```ts
function add(a: number, b: number) {
  return a + b;
}
//~중복된 함수 ...

function add(a: string, b: string) {
  return a + b;
}
//~중복된 함수 ...
```

- 하지만 하나의 함수에 대해 여러 개의 선언은 가능하지만 구현체는 오직 한개!!

```ts
function add(a: number, b: number): number;

function add(a: string, b: string): string;
```

- 타입 스크립트 타입은 런타임 성능에 영향을 주지는 않는다.

- 자바스크립트로 변환되는 시점에 타입연산자는 제거됨

### Item 4 구조적 타이핑에 익숙해지기

- 자바스크립트는 덕 타이핑 기반

- 덕 타이핑이란 타입에 부합하는 변수와 메서드를 가질 경우 객체를 해당 타입에 속하는 것으로 간주

```ts
interface Vector2D {
  x: number;
  y: number;
}

interface NamedVector {
  name: string;
  x: number;
  y: numbe;
}

function calculateLength(v: Vector2D) {
  return Math.sqrt(v.x * v.x + v.y + v.y);
}

const v: NamedVector = { x: 3, y: 4, name: 'ju' };

calculateLength(v); //문제없이 동작

interface Vector3D {
  x: number;
  y: number;
  z: number;
}

function normalize(v: Vector3D) {
  const length = calculateLength(v);

  return {
    x: v.x / length,
    y: v.y / length,
    z: v.z / length,
  };
}

// 하지만 다른 결과를 가진 값을 출력할 것이다.

// Vector3D 와 호환되는 {x, y, z} 객체로 계산함수를 호출하면 구조적으로 x와 y가 있어서 2D와 호환이 되기때문에 문제가 발생하지 않았다.
```

```ts
function calculateLength1(v: Vector3D) {
  let length = 0;

  for (const axis of Object.keys(v)) {
    const coord = v[axis];
    //엘리먼트는 암시적으로 any 타입입니다.
    length += Math.abs(coord);
  }

  return length;
}

//오류를 내뱉음

const vec3D = { x: 3, y: 4, z: 1, address: '...' };

calculateLength1(vec3D); // NaN을 반환함

// v는 어떤 속성이든 가질 수 있기 때문에, axis의 타입은 string이 될 수도 있다.

// 이런 경우

// refactored;

function calculateLength1(v: Vector3D) {
  return Math.abs(v.x) + Math.abs(v.y) + Math.abs(v.z);
}
```

- 구조적 타이핑은 클래스와 관련된 할당문 에서도 적용됨

```ts
class C {
  foo: string;
  constructor(foo: string) {
    this.foo = foo;
  }
}

const c = new C('instance of C');

const d: C = { foo: 'object literal' }; //(정상)

// 하나의 매개변수로 호출이 되는 생성자를 가진다. 따라서 구조적으로 필요한 속성과 생성자가 존재하기 때문에 문제가 없음

// 만약 C 생성자에 연산로직까지 존재한다면 , d의 경우는 문제가 발생
```

### Item 5 any 타입 지양하기

- any 타입에는 타입 안전성이 없습니다.

- any는 함수 시그니처(contract)를 무시해 버립니다.

- any 타입에는 언어 서비스가 적용되지 않습니다.

- any 타입은 코드 리팩터링시 버그를 감춘다.

- any는 타입 설계를 감춰 버립니다.

- any는 타입시스템의 신뢰도를 떨어뜨립니다.
