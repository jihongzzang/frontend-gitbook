# 2장

## 타입스크립트의 타입 시스템

### Item 16 잉여 속성 체크의 한계 인지하기

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
