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

### Item 24 일관성 있는 별칭 사용하기

```ts
const borough = { name: 'a', location: [-1, -1] };

const loc = borough.location; // loc 별칭

loc[0] = [10];

borough.location; //[10, -1]
```

- 별칭을 남발해서 사용하면 흐름을 분석하기 어려움

```ts
interface Cooridnate {
  x: number;
  y: number;
}

interface BoundingBox {
  x: [number, number];
  y: [number, number];
}

interface Polygon {
  exterior: Cooridnate[];
  holes: Cooridnate[][];
  bbox?: BoundingBox;
}

function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  if (polygon.bbox) {
    if (
      pt.x < polygon.bbox.x[0] ||
      pt.x > polygon.bbox.x[1] ||
      pt.y < polygon.bbox.y[0] ||
      pt.y > polygon.bbox.y[1]
    ) {
      return false;
    }
  }

  //...
}

// 문제없이 동작하지만 리팩토링을 해보자

function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  const box = polygon.bbox; // BoundingBox || undefined

  if (polygon.bbox) {
    if (
      pt.x < box.x[0] ||
      pt.x > box.x[1] || // 객체가 undefined ...
      pt.y < box.y[0] ||
      pt.y > box.y[1]
    ) {
      return false;
    }
  }

  //...
}

// 동작하지만 편집기에서 오류로 표시된다.
// 새로운 별칭이 타입스크립트의 제어 흐름분석을 방해했기 때문이다.

// refactoring

// 하지만 box와 bbox는 같은 값인데 다른 이름을 사용함. (일관되지 않음)
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  const box = polygon.bbox;

  if (box) {
    if (
      pt.x < box.x[0] ||
      pt.x > box.x[1] ||
      pt.y < box.y[0] ||
      pt.y > box.y[1]
    ) {
      return false;
    }
  }

  //...
}

// 객체 비구조화를 이용해 간결한 문법으로 일관된 이름을 사용할 수 있다.
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  const { bbox } = polygon;

  if (bbox) {
    const { x, y } = bbox;
    if (pt.x < x[0] || pt.x > x[1] || pt.y < y[0] || pt.y > y[1]) {
      return false;
    }
  }

  //...
}
```

- 객체 비구조화를 사용할때 주의 사항

  - 선택적 속성일 경우에 속성 체크가 더 필요하다.

  타입스크립트의 제어 흐름 분석은 지역 변수에는 생각보다 꽤 잘 동작하나 객체 속성에서는 주의 해야한다.

```ts
function fn(p: Polygon) {
  const { bbox } = p;
  //...
  if (bbox) {
    innerFb(p);
  }
}
```

- 에를들면 해당 속성이 존재할때 해당 속성을 내부 함수에 매개변수로 넣는경우
- 타입스크립트는 함수가 타입 정제를 무효화 하지 않는다고 가정하나 실제로는 무효화 될 가능성이 있다.
- 별칭은 타입스크립트가 타입을 좁히는 것을 방해한다. 따라서 변수에 별칭을 사용할때는 일관되게 사용하자.
- 비구조화 문법을 사용해서 일관된 이름을 사용하자
- 함수 호출이 객체 속성의 타입 정제를 무효화 할수 있다. (지역변수를 사용하자)

### Item 25 비동기 코드에는 콜백 대신 async 함수 사용

```ts
fetchURL(url1, function (response1) {
  fetchURL(url2, function (response2) {
    fetchURL(url3, function (response3) {
      console.log(1);
    });
    console.log(2);
  });
  console.log(3);
});

console.log(4);

// 4, 3, 2, 1
```

- 콜백지옥 대신 프로미스를 사용하면 더 간결한 코드가 된다.

```ts
const page1Promise = fetch(url1);

page1Promise
  .then((response1) => {
    return fetch(url2);
  })
  .then((response2) => {
    return fetch(url3);
  })
  .then((response3) => {
    //...
  })
  .catch((error) => {
    //...
  });
```

- 코드의 중첩도 적어졌고 실행 순서도 코드 순서와 같아졌다.

```ts
async function fetchPages() {
  try {
    const response1 = await fetch(url1);
    const response2 = await fetch(url2);
    const response3 = await fetch(url3);
    //,,,
  } catch (e) {
    //...
  }
}
```

- 콜백보다는 프로미스가 코드를 작성하기 쉽다.

- 프로미스가 타입을 추론하기 쉽다.

```ts
function timeout(millis: number): Promise<never> {
  return new Promise((resolve, reject) => {
    setTimeout(() => reject('timeout'), millis);
  });
}

async function fetchWithTimeout(url: string, ms: number) {
  return Promise.race([fetch(url), timeout(ms)]);
}
```

- 타입 구문이 없어도 `Promise<Resoponse>` 로 추론이 된다. 공집합 `never`와의 유니온은 아무런 효과가 없기 때문에 모든 타입 추론이 제대로 동작

```ts
//error logic

const _cache: { [url: string]: string } = {};

function fetchWithCache(url: string, callback: (text: string) => void) {
  if (url in _cache) {
    callback(_cache[url]);
  } else {
    fetchURL(url, (text) => {
      _cache[url] = text;
      callback(text);
    });
  }
}

let requestStatus: 'loading' | 'success' | 'error';

function getUser(userId: string) {
  fetchWithCache(`${url}/${userId}`, (profile) => {
    requestStatus = 'success';
  });

  requestStatus = 'loading';
}

//getUser를 호출한 후에 값은 온전히 캐시되었는지 여부에 달렸다.

//캐시되어 있지 않다면 Status 는 "success"가 된다.

//캐시되어 있다면 "success"에서 바로 다시 "loading"으로 돌아간다.

//async를 두 함수에 모두 사용하면 일관적인 동작을 강제하게 된다.

async function fetchWithCache(url: string, callback: (text: string) => void) {
  if (url in _cache) {
    callback(_cache[url]);
  }
  const response = await fetch(url);

  const text = await response.text();

  _cache[url] = text;

  return text;
}

let requestStatus: 'loading' | 'success' | 'error';

async function getUser(userId: string) {
  requestStatus = 'loading';

  const profile = await fetchWithCache(`${url}/${userId}`);

  requestStatus = 'success';
}

// 이렇게 코드가 명백해진다.
```

- async 함수에서 프로미스를 반환하면 또 다른 프로미스로 래핑되지 않고 `Promise<T>`가 된다.

- 콜백 보다는 프로미스를 사용하자.
- 가능하면 `async` `await`를 사용하는 것이 좋다.
- 어떤 함수가 프로미스를 반환하면 `async`로 선언하는 것이 좋다.

### Item 26 타입추론에 문맥이 어떻게 사용되는지 이해하기

- 타입추론해서 단순히 값만 고려하지 않는다.

```ts
// 인라인 형태
setLanguate('JavaScript');

// 참조 형태
let language = 'JavaScript';
setLanguage(language);

function setLanguage(lan: string) {
  /*...*/
}

setLanguage('JavaScript');

let language = 'JavaScript';
setLanguage(language);

//모두 정상
```

```ts
type Language = 'JavaScript' | 'TypeScript' | 'Python';

function setLanguage(lan: Language) {
  /*...*/
}

setLanguage('JavaScript'); //정상

let language = 'JavaScript';
setLanguage(language); //에러

const language = 'JavaScript';
setLanguage(language); //정상
```

- 튜플 사용시 주의점

  - 상수 문맥을 제공하자 `as const` 그러나 타입 정의에 실수가 있다면 호출되는 곳에서 발생하기 때문에 디버깅이 힘듬
  - 타입 시그니처를 수정할 수 없는 경우라면 타입 구문을 사용하자.

```ts
function panTo(where: readonly [number, number]) {
  /*...*/
}
```

- 객체 사용시 주의점

  - 타입 선언을 추가하거나 `as const`를 사용해 해결하자

- 콜백 사용시 주의점

  - 콜백을 다른 함수로 전달할 때, 타입스크립트는 콜백의 매개변수 타입추론을 위해 문백을 활용한다.

  - 매개변수에 타입 구문을 추가하자.

  - 전체 함수 표현식에 타입 선언을 적용하자

- 타입추론에서 문백이 어떻게 쓰이는지 주의해서 살펴보자

- 변수를 뽑아서 별도로 선언했을 때 오류가 발생한다면 타입 선언을 추가해야한다.

- 변수가 정말로 상수라면 상수 단언을 사용하자. 그러나 이는 오류가 사용한곳에서 나기때문에 주의하자.

### Item 27 함수형 기법과 라이브러리로 타입 흐름 유지하기

- 같은 코드를 타입스크립트로 작성하면 서드파티 라이브러리를 사용하는 것이 유리하다. (타입을 참고할 수 있다.)

- 타입 흐름을 개선하고, 가독성을 높이고, 명시적인 타입 구문의 필요성을 줄이기 위해 직접 구현하기 보다는 내장된 함수형 기법과 유틸리티 라이브러리를 잘 활용하자
