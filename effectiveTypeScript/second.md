# 2장

## 타입스크립트의 타입 시스템

### Item 6 편집기를 사용해 타입 시스템 탐색

```text
- 타입스크립트 컴파일러 (tsc)

- 단독으로 실행 할 수 있는 타입스크립트 서버 (tsserver)
```

- 타입스크립트는 기본적으로 추론 기능이 존재

  - 타입 넓히기와 좁히기의 개념을 더 잘 이해하고 싶다면 조건문에서 조건들이 어떤지 살펴보기\

  - 객체에서는 개별 속성들을 꼼꼼하게 살펴보기

```ts
function logMessage(message: string | null) {
  if (message) {
    message; // type string
  }
}

const foo = {
    x: [1,2,3] // type number[]

  bar: { name: 'Fred' },
};
```

- 타입 오류를 살펴보는 것도 타입 시스템의 성향을 파악하는데 좋은 방법

```ts
function getElement(elOrId: string | HTMLElement | null): HTMLElement {
  if (typeof elOrId === 'object') {
    return elOrId; //에러 null 도 "object"
  } else if (elOrId === null) {
    document.body;
  } else {
    const el = document.getElementById(elOrId);
    return el; // 애러 null은 HTMLElement에 할당 할 수 없다.
  }
}
```

- lib.dom.d.ts를 살펴보는 것도 좋은 방법이다.

### Item 7 타입이 값들의 집합이라고 생각하기
