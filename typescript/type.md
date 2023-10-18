# Type in TypeScript

- `String`

```ts
let str: string = 'hi';

Type Annotation : 변수뒤에 `:` 붙인 후 타입을 정의하는 방식
```

- `Number`

```ts
let num: number = 10;
```

- `Boolean`

```ts
let bool: boolean = false;
```

- `Object`

```ts
let obj: { name: string; age: number } = { name: 'jihong', age: 30 };
```

- `Array`

```ts
let arr: number[] = [1, 2, 3];

let arr: Array<number> = [1, 2, 3];
```

- `Tuple`

```ts
튜플은 배열의 길이가 고정되고 각 요소의 타입이 지정되어 있는 배열 형식

let tuple: [string, number] = ['hi', 10];
```

- `Enum`

```ts
이넘은 C, Java와 같은 다른 언어에서 흔하게 쓰이는 타입으로 특정 값(상수)들의 집합

enum Alphabet { A, B, C }

let a: Alphabet = Alphabet.A;

enum Alphabet { A, B, C }

let a: Alphabet = Avengers[0]; // A

enum Alphabet { B = 2, C, D }

let b: Alphabet = Avengers[2]; // B

let d: Alphabet = Avengers[4]; // D

```

- `any`

```ts
모든 타입에 대해서 허용한다는 의미

타입스크립트에서 지양해야하는 개발방식

let str: any = 'hi';

let num: any = 10;

let arr: any = ['a', 2, true];
```

- `void`

```ts
반환 값이 없는 함수의 반환 타입
아래와 같이 return이 없거나 return이 있더라도 반환하는 값이 없으면 함수의 반환 타입을 void로 지정

function printHello(): void {
  console.log('hello');
}

function retrunNothing(): void {
  return;
}
```

- `null`

```ts
let null: null = null;
```

- `undefined`

```ts
사실상 쓸일이 없음 굳이 들어가지 않아도 된다면 optionalParameter를 활용하자
let undefined: undefined = undefined;
```

- `never`

```ts
함수의 끝에 절대 도달하지 않는다는 의미

function neverEnd(): never {
  while (true) {
    ...
  }
}

```
