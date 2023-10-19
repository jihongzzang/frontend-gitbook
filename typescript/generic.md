# Generic

제네릭은 C#, Java 등의 언어에서 재사용성이 높은 컴포넌트를 만들 때 자주 활용

특히, 한가지 타입보다 여러 가지 타입에서 동작하는 컴포넌트를 생성하는데 사용

```txt
제네릭이란 타입을 마치 함수의 파라미터처럼 사용하는 것을 의미
```

```ts
function getText(text) {
  return text;
}

getText('hi'); // 'hi'
getText(10); // 10
getText(true); // true

function getText<T>(text: T): T {
  return text;
}

getText<string>('hi');
getText<number>(10);
getText<boolean>(true);

flow 중요!!

function getText<string>(text: T): T {
  return text;
}

getText<string>();

getText<string>('hi');

function getText<string>(text: string): string {
  return text;
}
```

## 왜 사용하는가

```ts
function logText(text: string): string {
  return text;
}

인자를 하나 넘겨 받아 반환해주는 함수

만약 여러 가지 타입을 허용하고 싶으면

function logText<T>(text: T): T {
  return text;
}

const text = logText<string>("Hello Generic"); //ok!!
// #2
const text = logText("Hello Generic"); //ok!!
```

```ts
flow 중요!!

function logText<T>(text: T): T {
  console.log(text.length); // Error: T doesn't have .length
  return text;
}

function logText<T>(text: T[]): T[] {
  console.log(text.length); // 제네릭 타입이 배열이기 때문에 `length`를 허용
  return text;
}

function logText<T>(text: Array<T>): Array<T> {
  console.log(text.length);
  return text;
}
```

## 제네릭 타입

```ts
function logText<T>(text: T): T {
  return text;
}
// #1
let str: <T>(text: T) => T = logText;

// #2
let str: { <T>(text: T): T } = logText;
```

```ts
interface GenericLogTextFn {
  <T>(text: T): T;
}
function logText<T>(text: T): T {
  return text;
}
let myString: GenericLogTextFn = logText; // Okay

interface GenericLogTextFn<T> {
  (text: T): T;
}
function logText<T>(text: T): T {
  return text;
}
let myString: GenericLogTextFn<string> = logText;
```

## 제네릭 클래스

```ts
class GenericMath<T> {
  pi: T;
  sum: (x: T, y: T) => T;
}

let math = new GenericMath<number>();
```

## 제네릭 제약조건

```ts
function logText<T>(text: T): T {
  console.log(text.length); // Error: T doesn't have .length
  return text;
}
```

```ts
interface LengthSolution {
  length: number;
}

function logText<T extends LengthSolution>(text: T): T {
  console.log(text.length);
  return text;
}

logText(10); // Error, 숫자 타입에는 `length`가 존재하지 않으므로 오류 발생
logText({ length: 0, value: 'hi' }); // `text.length` 코드는 객체의 속성 접근과 같이 동작하므로 오류 없음
```

## 객체속성 제약

```ts
function getProperty<T, O extends keyof T>(obj: T, key: O) {
  return obj[key];
}
let obj = { a: 1, b: 2, c: 3 };

getProperty(obj, 'a'); // Okay
getProperty(obj, 'z'); // error: "z"는 "a", "b", "c" 속성에 해당하지 않는다..
```

```ts
interface Array<T> {
  forEach(
    callbackfn: (value: T, index: number, array: T[]) => void,
    thisArg?: any
  ): void;

  map<U>(
    callbackfn: (value: T, index: number, array: T[]) => U,
    thisArg?: any
  ): U[];

  filter<S extends T>(
    predicate: (value: T, index: number, array: T[]) => value is S,
    thisArg?: any
  ): S[];
}

interface Arr<T> {
  forEach(callback: (item: T) => void): void;

  map<S>(callback: (value: T) => S): S[];

  filter<S extends T>(callback: (v: T) => v is S): S[];
}
```
