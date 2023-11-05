# 5장

## any 다루기

### Item 41 any의 진화를 이해하기

- 타입스크립트에서 일반적으로 변수의 타입은 변수를 선언할 때 결정

- any타입과 관련해서는 예외가 존재한다.

```ts
function range(start: number, limit: number) {
  const out = []; // any[]

  for (let i = start; i < limit; i++) {
    out.push(i); // any[]
  }
  return out; // number[]
}

// out이 처음에는 any[] => number[]로 추론
```

- 타입의 진화는 좁히기와 다르다. 배열에 다양한 타입의 요소를 넣으면 배열의 타입이 확장되며 진화함

```ts
const result = []; // any[]
result.push('a');
result; // string[]
result.push(1);
result; // (strong | number) []
```

- 조건문에서는 분기에 따라 타입이 변할 수도 있다.

```ts
let val; //any

if (Math.random() < 0.5) {
  val = /hello/;
  val; //RegExp
} else {
  val = 12;
  val; //number
}
val; //number | RegExp
```

- 초기값이 `null`인 경우에도 진화가 일어남

```ts
let val = null; // any

try {
  val = 12;
  val; // number
} catch (e) {
  console.warn('alas!');
}
val; // number | null
```

- `any`의 진화는 `noImplicityAny`가 설정된 상태에서 변수의 타입이 암시적 `any`인 경우에만 발생함.

```ts
function range(start: number, limit: number) {
  const out = []; //일부 위치에서 임시적으로 "any[]" 형식이 포함

  if (start === limit) {
    return out; //임시적으로 "any[]" 형식이 포함
  }

  for (let i = start; i < limit; i++) {
    out.push(i);
  }

  return out;
}
```

- `any`타입의 진화는 암시적 `any`타입에 어떤 값을 할당할 떄만 발생한다. (호출을 거쳐도 진화하지 않음)

- 일반적인 타입들은 정제되기만 하는 반면, 암시적 any와 any[] 타입은 진화 할 수 있다.

- any를 진화시키는 방식보다 명시적 타입 구문을 사용하는 것이 안전한 타입을 유지하는 방법

### Item 42 모르는 타입의 값에는 any 대신 unknown을 사용하기

### Item 43 타입 커버리지를 추적하여 타입 안정성 유지하기
