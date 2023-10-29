# 2장

## 타입스크립트의 타입 시스템

### Item 16 number 인덱스 시그니처보다는 Array, 튜플, ArrayLike 사용하기

- 암시적 타입 강제와 관련된 부분

- 자바스크립트에서 객체란 키 / 값 쌍의 모음이다.

- 키는 보통 문자열이거나 ES2015 이후로는 심벌일 수 있다.

```js
let x = {};
// => {}

x[[1, 2, 3]] = 2;
// => 2

x;
// => { '1,2,3': 2 }
```

- 숫자는 키로 사용할 수 없다. 숫자를 속성이름으로 쓰려고하면 JS 런타임은 문자열로 변환할것임

```ts
let x = { 1: 2, 3: 4 };

x;

// => { '1': 2, '3': 4 }
```

- 배열은 분명히 객체이다.

```js
console.log(typeof []); // 'object'

let x = [1, 2, 3];

x[0]; // 1

x['1']; // 1

Object.keys(x); // [ '0', '1', '2' ]
```

- 타입스크립트는 숫자 키를 허용하고, 문자열 키와 다른 것으로 인식

```ts
interface Array<T> {
  //,,,
  [n: number]: T;
}

const xs = [1, 2, 3];
const x0 = xs[0];
const x1 = xs['1']; // 인덱스 식이 "number" 형식이 아니므로 요소에 암시적으로 "any" 형식이 있다.

function get<T>(array: T[], k: string): T {
  return array[k];
  // 인덱스 식이 "number" 형식이 아니므로 요소에 암시적으로 "any" 형식이 있다.
}

const keys = Object.keys(xs);

for (const key in xs) {
  key; // key type string
  const x = xs[key]; // type number
}
```

- string이 number에 할당 될 수 없기 때문에, 에제의 마지막 줄이 동작하것이 이상하게 보이나. 배열을 순회하는 코드 스타일에 대한 실용적인 허용이라고 생각하자.

- 인덱스에 신경쓰지 않는다면, for-of를 사용하자

```ts
for (const x of xs) {
  x; // type number
}

//만약 인덱스 타입이 중요하다면 forEach메서드를 사용하자

xs.forEach((x, i) => {
  i; // type number
  x; // type number
});

// 루프 중간에 멈춰야 한다면 for(;;) 루프를 사용하자

for (let i = 0; i < xs.length; i++) {
  const x = xs[i];
  if (x < 0) break;
}
```

- 인덱스 시그니처가 number로 표현되어 있다면, 입력한 값이 number여야 한다는 것을 의미하지만, 실제 런타임에 사용되는 키는 string 타입이다.

- 만약 숫자를 사용하여 인덱스 할 항목을 지정한다면 Array 또는 튜플 타입을 대신 사용하게 될 것이다.

- 한편 Array 타입이 사용하지 않은 push나 concat 같은 다른 속성을 가지는게 어려울 수도 있지만, 구조적인 고려를 하고 있다는 뜻이다.

- 어떤 길이를 가지는 배열과 비슷한 형태의 튜플을 사용하고 싶다면 타입스크립트에 있는 ArrayLike 타입을 사용하자

```ts
function chekedAccess<T>(xs: ArrayLike<T>, i: number): T {
  if (i < xs.length) {
    return xs[i];
  }

  throw new Error(`배열의 끝을 지나서 ${i}를 접근하려고 했습니다.`);
}

// 길이와 숫자 인덱스 시그니처만 있다. 이런 경우는 실제로는 드물지만 필요하다면 사용하자. 하지만 키는 여전히 문자열이다.

const tupleLike: ArrayLike<string> = {
  '0': 'A',
  '1': 'B',
  length: 2,
};
```

- 배열은 객체이므로 키는 숫자가 아니라 문자열이다. 인덱스 시그니처로 사용된 `number` 타입은 버그를 잡기 위한 순수 타입스크립트 코드이다.

- 인덱스 시그니처에 `number`를 사용하기 보다 `Array`나 `튜플`, 또는 `ArrayLike`타입을 사용하자.

### Item 17 변경 관련된 오류 방지를 위해 readonly 사용하기

- 삼각수를 출력하는 코드를 보자

```ts
function printTriangles(n: number) {
  const nums = [];
  for (let i = 0; i < n; i++) {
    nums.push(i);
    console.log(arraySum(nums));
  }
}

function arraySum(arr: number[]) {
  let sum = 0,
    num;

  while ((num = arr.pop()) !== undefined) {
    sum += num;
  }

  return sum;
}

// 이 함수는 계산이 끝나면 배열이 전부 비게 된다. 타입스크립트에서도 오류 없이 통과하는데.

// 오류의 범위를 좁히기 위해 readonly 접근 제어자를 사용하자.

function arraySum(arr: readonly number[]) {
  let sum = 0,
    num;

  while ((num = arr.pop()) !== undefined) {
    // ~~~'readonly number[]' 형식에 'pop' 속성이 없다.
    sum += num;
  }

  return sum;
}
```

- readonly number[] 와 number[] 와 구분되는 몇 가지 특징

  - 배열의 요소를 읽을 수는 있지만, 쓸 수는 없다.
  - length를 읽을 수는 있지만, 바꿀 수는 없다.
  - 배열을 변경하는 pop을 비롯한 다른 메서드를 호출 할 수는 없다.
  - number[]는 readonly number[] 보다 기능이 많기 때문에, readonly number[]의 서브타입이 된다.
  - 따라서 변경가능한 배열을 readonly 배열에 할당할 수 있다. (그 반대는 불가능)

```ts
const a: number[] = [1, 2, 3];
const b: readonly number[] = a;
const c: number[]; = b //readonly 타입은 readonly 이므로 변경가능한 number[]에 할당 할 수 없다.
```

- 타입 단언문 업싱 접근 제어자를 제거할 수 있다면 readonly는 쓸모 없을 것이기 때문에 오류가 발생하는게 맞다.

```text
매개변수를 readonly로 선언시 발생하는 일

- 타입스크립트는 매개 변수가 함수 내에서 변경이 일어나는지 체크

- 호출하는 곳에서 함수가 매개변수를 변경하지 않는다는 보장을 받게 됨

- 호출하는 곳에서 함수에서 readonly 배열을 매개변수로 넣을 수도 있다.
```

```ts
//refactored arraySum

function arraySum(arr: readonly number[]) {
  let sum = 0;

  for (const num of arr) {
    sum += num;
  }

  return sum;
}
```

- 만약 함수가 매개 변수를 변경하지 않는다면, readonly로 선언해야 한다.

- readonly를 사용하면 지역 변수와 관련된 모든 종류의 오류를 방지할 수 있다.

```ts
function parseTaggedText(lines: string[]): string[][] {
  const paragraphs: string[][] = [];
  const currPara: string[] = [];

  const addParagraph = () => {
    if (currPara.length) {
      paragraphs.push(currPara); //별칭과 변경을 동시에 사용해 발생
      currPara.length = 0; // 배열을 비움
    }
  };

  for (const line of lines) {
    if (!line) {
      addParagraph();
    } else {
      currPara.push(line);
    }
  }

  addParagraph();

  return paragraphs;
}

function parseTaggedText(lines: string[]): string[][] {
  const paragraphs: string[][] = [];
  const currPara: readonly string[][] = [];
  /*...*/

  const addParagraph = () => {
    if (currPara.length) {
      paragraphs.push(currPara); // readonly 라서 할당 불가하다.
      currPara.length = 0; // readonly 속성이라 length에 할당할 수 없다.
    }
  };

  for (const line of lines) {
    if (!line) {
      /*...*/
    } else {
      currPara.push(line); // readonly 속성에 push가 없다.
    }
  }

  /*...*/
}

let currPara: readonly string[] = [];

currPara = [];

currPara = currPara.concat([line]);

// 그럼에도 불가하고 오류가 있는데 이 오류를 잡아내는 방법은 3가지이다.

paragraphs.push([...currPara]); //복사본은 원하는대로 변경이 가능

const paragraphs: (readonly string[])[] = [];

paragraphs.push(currPara as string[]);
```

- readonly 는 얕게 동작한다.

```ts
const dates: readonly Date[] = [new Date()];

dates.push(new Date()); // 에러

dates[0].setFullYear(2037); //정상

interface Outer {
  inner: { x: number };
}

const o: Readonly<Outer> = { inner: { x: 0 } };

o.inner = { x: 1 }; // 에러

o.inner.x = 1;

type T = Readonly<Outer>;

// Type T = {
//   readonly inner: {
//   x: number;
//   }
// }
```

- 인덱스 시그니처에도 readonly를 사용할때 읽기는 허용하나, 쓰기는 방지

```ts
let obj: { readonly [k: string]: number } = {};

let obj: Readonly{  [k: string]: number } = {};

obj.hi = 45 // 에러

obj = { ...obj, hi: 12 } // 정상

obj = { ...obj, bye: 12 } // 정상
```

- 함수가 매개변수를 수정하지 않을땐 `readonly`로 선언하자.

- `readonly`를 사용하면 변경하면서 발생하는 오류를 방지할 수 있고, 변경이 발생하는 코드도 쉽게 찾을 수 있다.

- `const` 와 `readonly`의 차이를 알자

- `readonly`는 얕게 동작.

### Item 18 매핑된 타입을 사용하여 값을 동기화 하기

```ts
interface ScatterProps {
  xs: number[];
  ys: number[];

  xRange: [number, number];
  yRange: [number, number];
  color: string;

  onClick: (x: number, y: number, index: number) => void;
}

function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
  let k: keyof ScatterProps;

  for (k in oldProps) {
    if (oldProps[k] !== newProps[k]) {
      if (k !== 'onClick') return true;
    }
  }

  return false;
}

const REQUIRES_UPDATE: { [k in keyof ScatterProps]: boolean } = {
  xs: true,
  ys: true,
  xRange: true,
  yRange: true,
  color: true,
  onClick: false,
};

//Refactored

function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
  let k: keyof ScatterProps;

  for (k in oldProps) {
    if (oldProps[k] !== newProps[k] && REQUIRES_UPDATE[k]) {
      return true;
    }
  }

  return false;
}
```

- 매핑된 타입을 사용해 타입스크립트가 코드에 제약을 강제하도록 할 수 있다.

- 매핑된 타입을 사용해서 관련된 값과 타입을 동기화하자.

- 인터페이스에 새로운 속성을 추가할 때, 선택을 강제하도록 매핑된 타입을 고려해야한다.
