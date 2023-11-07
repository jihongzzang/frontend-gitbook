# 6장

## 타입 선언과 @types

### Item 50 오버로딩 타입보다는 조건부 타입을 사용하기

```ts
function double(x) {
  return x + x;
}

//타입 정보 추가

function double(x: number | string): number | string;

function double(x: any) {
  return x + x;
}

const num = double(12); // string | number
const str = double('x'); // string | number

//double 에 number 타입을 매개변수로 사용하면 number
//double 에 string 타입을 매개변수로 사용하면 string

//but 선언문에는 number 타이블 매개변수로 넣고 string 타입을 반한하는 경우도 포함되어 있는데

//refactored using Generic

function double<T extends number | string>(x: T): T;

function double(x: any) {
  return x + x;
}

const num = double(12); // type 12

const string = double('x'); // type "x"

// 타입이 너무 과하다.

// 여러가지 타입 선언으로 분리해보자 왜? 타입 선언은 몇개든지 만들 수 있다.

function double(x: number): number;
function double(x: string): string;
function double(x: any) {
  return x + x;
}

// 하지만 유니온 타입 관련해서는 문제가 있음

function f(x: number | string) {
  return double(x); // "string"|"number" 형식의 인수는 "string" 형식의 매개변수에 할당 할 수 없습니다.
}

// 세번째 오버로딩을 추가하여 문제를 해결할 수도 있지만 가장 좋은 해결책은 조건부 타입을 사용하는 것

function double<T extends number | string>(
  x: T
): T extends string ? string : number;
```

- 유니온에 조건부 타입을 적용하면, 조건부 타입의 유니온으로 분리됨.

```text
(number | string) extends string? string : number

=> (number extends string? string : number) | (string extends string? string : number )

=> number | string
```

- 오버로딩 타입보다는 조건부 타입을 사용하자. 조건부 타입은 추가적인 오버로딩 없이 유니온 타입을 지원할 수 있다.

### Item 51 의존성 분리를 위해 미러 타입 사용하기

- 필수가 아닌 의존성을 분리할 때는 구조적 타이핑을 사용하자 (node개발자, 웹 기반이나 자바스크립트 사용자 등)

- 공개한 라이브러리를 사용하는 자바스크립트 사용자가 @types 의존성을 가지지 않게 해야한다.

### Item 52 테스팅 타입의 함정에 주의하기

- 테스트 코드를 작성하는 것은 필수이며, 타입 선언도 테스트를 거쳐야 하지만 타입 선언을 테스트 하기는 매우 어렵다.

- 타입선언이 예상한 타입으로 결과를 내는지 체크하는 한 가지 방법은 함수를 호출하는 테스트 파일을 작성하는 것

```ts
//반환값을 특정 타입의 변수에 할당하여 간단히 반환 타입을 체크하는 예제

const lengths: number[] = map(['john', 'paul'], (name) => name.length);

// 일반적으로 불필요한 타입선언에 해당하나, 테스트 코드 관점에서는 number[]의 타입 선언은 함수의 반환 타입이 number[]임을 보장한다.

// 하지만 불필요한 변수를 만뜰어야 하고, 일부 린팅 규칙을 비활성해야 한다.

function assertType<T>(x: T) {}

assertType<number[]>(map(['john', 'paul'], (name) => name.length));

// 불필요한 변수문제는 해결 했지만, 두 타입이 동일한지 체크 하는 것이 아닌, 할당 가능성을 체크한다.

const n = 12;

assertType<number>(n); // 정상

const add = (a: number, b: number): number => a + b;

const double = (x: number): number => 2 * x;

assertType<(a: number, b: number) => number>(add); //정상

// 타입스크립트의 함수는 매개변수가 더 적은타입에 할당 가능하기 때문이다.
assertType<(a: number, b: number) => number>(double); //정상

//Parameters 와 ReturnType 제네릭 타입을 이용해 매개변수 타입과 반환 타입만 분리가 가능

const double = (x: number): number => 2 * x;

let p: Parameters<typeof double> = null;

assertType<[number, number]>(p); // 에러 매개변수에 할당이 불가능하다.

let r: ReturnType<typeof double> = null;

assertType<number>(r); // 정상

//this 타입 테스트 (화살표 함수 아님)

declare function map<U, V>(
  array: U[],
  fn: (this: U[], u: U, i: number, array: U[]) => v
): V[];
```

- 타입을 테스트할 때는 특히 함수 타입의 동일성과 할당 가능성의 차이점을 알고 있자.
- 콜백이 있는 함수를 테스트할 때, 콜백 매개변수의 추론된 타입을 체크하자.
- 타입 관련 테스트에서 any를 주의하자. 필요하다면 `dtslint` 같은 도구들을 활용하자.
