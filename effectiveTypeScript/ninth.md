# 4장

## 타입 설계

### Item 36 해당 분야의 용어로 타입 이름 짓기

- 컴퓨터 과학에서 어려운 일은 단 두가지 뿐이다. 캐시 무효화와 이름 짓기 (필 칼튼)

- 이름 짓기 역시 타입 설계에서 중요한 부분임!!

- 엄선된 타입, 속성, 변수의 이름은 의도를 명확히 하고 코드와 타입의 추상화를 높여줌

```ts
interface Animal {
  name: string;
  endangered: boolean;
  habitat: string;
}

// name은 매우 일반적인 용어이므로. 동물과 관련된 이름인지 일반적인 명칭인지 알 수 없음
// 멸종 위기를 표현하기 위해 boolean을 쓰는게 맞는지?
// 서식지를 나타내는 타입은 너무 모호하고 범위가 넓음

// refactored

interface Animal {
  commonName: string;
  genus: string;
  species: string;
  status: ConservationStatus;
  climates: KoppenClimate[];
}

type ConservationStatus = 'xx' | 'xx' | 'xx' | 'xx' | 'xx' | 'xx';

type KoppenClimate = 'xx' | 'xx' | 'xx' | 'xx' | 'xx';

// name은 commonName, genus, species 등 더 구체적인 용어로 대체
// endangered는 동물 보호 등급에 대한 IUCN의 표준 분류 체계의 타입으로 변경
// habitat는 기후를 뜻하는 climeates로 변경, 쾨펜 기후 분류를 사용
```

- 코드를 표현하고자 하는 모든 분야에는 주제를 설명하기 위한 전문 용어들이 있다. 자체적으로 사용하지말고 존재하는 용어를 사용하자.

- 전문 분야의 용어는 정확하게 사용해야한다.

- 동일한 의미를 나타낼 때는 같은 용어를 사용하자.

- 모호하고 의미없는 이름은 피하자 ("info","data","thing","object")

- 이름을 지을때는 포함된 내용이나 계산 방식이 아니라 데이터 자체가 무엇인지를 고려하자.

### Item 37 공식 명칭에는 상표를 붙이기

- 구조적 타이핑의 특성 때문에 가끔 이상한 결과를 낼 수 있다.

```ts
interface Vector2D {
  x: number;
  y: number;
}

function calc(p: Vector2D) {
  return Math.sqrt(p.x * p.x + p.y * p.y);
}

calc({ x: 3, y: 4 }); // 정상 결과 5

const vector3D = { x: 3, y: 4, z: 1 };

calc(vector3D); // 정상! 결과 5
```

- 구조적 타이핑 관점에서는 문제가 없지만, 수학적으로는 2차원 벡터를 사용해야함

- 이럴땐 공식 명칭을 사용하자. 공식 명칭을 사용하는것은, 타입이 아니라 값의 관점에서 말하는 것

```ts
interface Vector2D {
  _brand: '2d';
  x: number;
  y: number;
}

function vec2D(x: number, y: number): Vector2D {
  return { x, y, _brand: '2d' };
}

function calc(p: Vector2D) {
  return Math.sqrt(p.x * p.x + p.y * p.y);
}

calc(vec2D(3, 4));

const vector3D = { x: 3, y: 4, z: 1 };

calc(vector3D); // "_brand" 속성이 없다.
```

- 이렇게 한다고 해서 악의적으로 \_brand를 추가해서 사용하는것은 막을 수 없지만, 단순한 실수를 방지하기에는 충분

- 상표기법은 타입 시스템에서 동작하지만 런타임에 상표를 검사하는 것과 동일 효과를 얻을 수 있다.

```ts
// 절대경로를 이용해서 파일 시스템에 접근하는 예제
type AbsolutePath = string & { _brand: 'abs' };

function listAbsolutePath(path: AbsolutePath) {
  //...
}

function isAbsolutePath(path: string): path is AbsolutePath {
  return path.startsWith('/');
}

function func(path: string) {
  if (isAbsolutePath(path)) {
    listAbsolutePath(path);
  }

  listAbsolutePath(path); // 에러
  // 이부분에 단언문을 사용할 수도 있지만 단언문은 지양해야한다.
}
```

```ts
// 이진 검색 예제
function binarySearch<T>(xs: T[], x: T): boolean {
  let low = 0,
    high = xs.length - 1;

  while (high >= low) {
    const mid = low + Math.floor((high - low) / 2);
    const v = xs[mid];
    if (v === x) {
      return true;
    }
    [low, high] = x > v ? [mid + 1, high] : [low, mid - 1];
  }
  return false;
}

// 이진 검색은 이미 정렬된 상태를 가정하기 때문에, 목록이 정렬되어 있지 않다면 잘못된 결과가 나온다.

type SortedList<T> = T[] & { _brand: 'sorted' };

function isSorted<T>(xs: T[]): xs is SortedList<T> {
  for (let i = 1; i < xs.length; i++) {
    if (xs[i] < xs[i - 1]) {
      return false;
    }
  }
  return true;
}

function binarySearch<T>(xs: SortedList<T>, x: T): boolean {
  //
}

// 목록 전체를 루프 도는 것이 효율적이지는 않지만, 적어도 안정성은 확보가 가능함
```

- 값을 구분하기 위해 공식 명칭이 필요하다면 상표를 붙이자.

- 상표 기법은 타입 시스템에서 동작하지만 런타임에 상표를 검사하는 것과 동일한 효과를 얻을 수 있다.
