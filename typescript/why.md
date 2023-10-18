# TypeScript

[TypeScript Handbook](https://www.typescriptlang.org/ko/docs/handbook/intro.html)

- 타입스크립트는 자바스크립트에 타입을 부여한 언어
- 타입스크립트는 자바스크립트와 달리 브라우저에서 실행하려면 파일을 한번 변환 (컴파일)해주어야 한다.

```node
TypeScript => Static Types / during development
JavaScript => Dynamic Types / resolved at runtime
```

## Why Use

- 에러의 사전 방지
- 코드 가이드 및 자동 완성(개발 생산성 향상)

```js
의도치 않은 결과
function sum(a, b) {
  return a + b;
}

sum(10, 20); // 30
sum('10', '20'); // 1020
```

```ts
컴파일 과정에서 에러 검출
function sum(a: number, b: number) {
  return a + b;
}

sum(10, 20); // 30
sum('10', '20'); // error
```

```ts
function sum(a: number, b: number): number {
  return a + b;
}

const total = sum(10, 20);

total.toLocaleString();

타입스크립트로 개발할 시에 .toLocaleString() 이라는 메서드를 바로 꺼내서 사용 할 수 있음
```
