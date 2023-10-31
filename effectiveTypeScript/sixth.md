# 3장

## 타입추론

### Item 23 한꺼번에 객체 생성하기

- 변수의 값은 변경 될 수 있지만, 타입스크립트의 타입은 일반적으로 변경되지 않습니다.

즉, 객체를 생성할 때는 여러 속성을 포함해서 한꺼번에 생성해야 타입 추론에 유리하다.

```ts
const pt = {};

pt.x = 3; // 속성 없음 에러
pt.y = 4; // 속성 없음 에러

interface Point {
  x: number;
  y: number;
}

const pt: Point = {}; // 속성 없음 에러

//refactored

const pt: Point = {
  x: 3,
  y: 4,
};

//객체를 반드시 나눠서 만들어야 한다면? 타입 단언문을 사용해서 통과를 시킬 수 있음

const pt = {} as Point;

pt.x = 3;
pt.y = 4;

//물론 이 경우에도 선언시 객체를 한꺼먼에 만드는게 좋다.
```

- 작은 객체들을 조합해서 큰 객체를 만들어야 하는 경우에도 여러단계를 거치는 것은 좋은 것은 아니다.

- 전개 연산자를 사용하면 한꺼번에 큰 객체를 만들 수 있다.

```ts
const pt = { x: 3, y: 4 };
const id = { name: '피타고라스' };
const namedPoint = {};

Object.assign(namedPoint, pt, id);

namedPoint.name; // type error

const namedPoint = { ...pt, ...id };
namedPoint.name; // type String
```

- 전개 연사자를 사용할때는 타입 걱정 없이 필드 단위로 객체를 생성할 수도 있다.
  - 모든 업데이트마다 새 변수를 사용하여 각각 새로운 타입을 얻도록 하는게 중요하다.
  - 객체에 속성을 추가하고 타입스크립트가 새로운 타입을 추론할 수 있게 해 유용

```ts
const pt0 = {};
const pt1 = { ...pt0, x: 3 };

const pt: Point = { ...pt1, y: 4 };
```

- 타입에 안전한 방식으로 조건부 속성을 추가하려면, 속성을 추가하지 않는 `null` 또는 `{}`으로 객체 전개를 사용하자

```ts
declare let hasMiddle: boolean;

const firstLast = { first: 'a', last: 'b' };

const president = { ...firstLast, ...(hasMiddle ? { middle: 's' } : {}) };

// type President = {
//   middle?: string;
//   first: string;
//   last: string;
// };

//전개 연산자로 한꺼번에 여러 속성을 추가할 수도 있음.

declare let hasDates: boolean;

const nameTitle = { name: 'a', title: 'b' };

const pharaoh = {
  ...nameTitle,
  ...(hasDates ? { start: -1, end: -1 } : {}),
};

const pharaoh:
  | {
      start: number;
      end: number;
      name: string;
      title: string;
    }
  | { name: string; title: string };

pharaoh.start; // error

// 유니온을 사용하는게 값의 집합을 더 정확히 표현할 수 있다.
// 만약에 선택적 필드방식으로 표현하려면 헬퍼 함수를 사용하자

function addOptional<T extends Object, U extends object>(
  a: T,
  b: U | null
): T & Partion<U> {
  return { ...a, ...b };
}

const pharaoh = addOptional(
  nameTitle,
  hasDates ? { start: -1, end: -1 } : null
);

pharaoh.start; // 정상 number | undefined
```

- 속성을 추가하지 말고 한꺼번에 객체로 만들자

- 객체에 조건부로 속성을 추가하는 방법을 익히자
