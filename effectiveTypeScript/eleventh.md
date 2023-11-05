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

- `unknown`에는 함수의 반환값과 관련된 형태, 변수 선언과 관련된 형태, 단언문과 관련된 형태가 있다.

- `unknown`은 어떠한 타입에든 할당 가능

- `unknown`타입인채로 값을 사용하면 오류가 발생한다.

- `unknown`은 변수 선언시 어떠한 값이 있지만 그 타입을 모르는 경우에 사용

- `unknown`과 `object`,`{}`는 좀 다른데 후자가 범위가 약간 좁다.

  - `{}`타입은 `null`과 `undefined`를 제외한 모든 값을 포함
  - `object` 타입은 모든 비기본형 타입으로 이루어진다. (`true`,`"foo"`,`12`는 포함되지 않지만 객체와 배열은 포함됨)

### Item 43 몽키 패치 보다는 안전한 타입을 사용하기

- 자바스크립트의 가장 유명한 특징 중 하나는, 객체와 클래스에 임의의 속성을 추가할 만큼 유연하다는 것.

```ts
window.monkey = 'Tamarin';
document.monkey = 'Howler'; // error
(document as any).monkey = 'Howler'; // ok
(document as any).monky = 'Howler'; // ok
// 그러나 타입 안정성을 상실해버림

const el = document.getElementById('colobus');
el.home = 'tree';

RegExp.prototype.monkey = 'Capuchin';
```

- 타입 보강을 통해 추가

```ts
interface Document {
  monkey: string;
}
```

- 보강을 사용한 방법이 `any`보다 나은 점
  - 타입이 더 안전하다. 타입 체커는 오타나 잘못된 타입의 할당을 오류로 표시
  - 속성에 주석을 붙일 수 있따.
  - 자동완성 사용가능
  - 몽키 패치가 어떤 부분에 적용되었는지 확인 가능

```ts
//모듈의 관점에서 제대로 동작하게 하려면 global 선언을 추가하자

export {};
declare global {
  interface Document {
    monkey: string;
  }
}
```

- 더 구체적인 타입 단언 문 사용하기

```ts
interface MonkeyDocument extends Document {
  mokney: string;
}

(document as MonkeyDocument).monkey = 'Howler'; // ok
```

- `MonkeyDocument`는 `Document`를 확장하기 때문에 타입 단언문이며 할당문의 타입은 안전하다.
- `Document` 타입을 건드리지 않고 확장하는 새로운 타입을 도입했기 때문에 모듈 영역 문제도 해결 가능
- 그러나 몽키패치를 남용해서는 안되며 더 잘 설계된 구조로 리팩토링 하는것이 좋다.
- 전역변수나 DOM에 데이터를 저장하지 말고, 데이터를 분리하여 사용하자.
- 내장 타입에 데이터를 저장해야 하는 경우, 안전한 타입 접근법 중 하나(보강, 사용자 정의 인터페이스로 단언)을 사용하자.
- 보강의 모듈 영역 문제를 이해하자
