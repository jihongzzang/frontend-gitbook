# 4장

## 타입 설계

### Item 32 유니온의 인터페이스보다는 인터페이스의 유니온을 사용하기

- 유니온 타입의 속성을 가지는 인터페이스를 작성할때는 인터페이스의 유니온 타입을 사용하는게 더 맞지 않을까 검토해보자.

```ts
interface Layer {
  layout: FillLayout | LineLayout | PointLayout;
  paint: FillPaint | LinePaint | PointPaint;
}

//서로 맞지않는 타입이 생길수도 있음

//refactored

//각각 타입의 계층을 분리된 인터페이스로 두기

interface FillLayer {
  type: 'fill';
  layout: FillLayout;
  paint: FillPaint;
}

interface LineLayer {
  type: 'line';
  layout: LineLayout;
  paint: LinePaint;
}

interface PointLayer {
  type: 'point';
  layout: PointLayout;
  paint: PointPaint;
}

type Layer = FillLayer | LineLayer | PointLayer;
```

- 해당 패턴의 가장 일반적인 예시는 태그된 유니온이다.

- 타입 속성은 `태그`이며 런타임 시에 어떤 타입의 레이어가 사용되는지 판단하는데 쓰임

```ts
function drawLayer(layer: Layer) {
  if (layer.type === 'fill') {
    const { paint, layout } = layer;
  } else if (layer.type === 'line') {
    const { paint, layout } = layer;
  } else {
    const { paint, layout } = layer;
  }
}

//타입의 속성들 간의 관계를 제대로 모델링하면, 타입스크립트가 정확성을 체크하는데 도움이된다.
```

- 여러개의 선택적 필드가 동시에 값이 있거나 동시에 `undefined`인 경우도 태그된 유니온 패턴이 잘 맞는다

```ts
interface Person {
  name: string;
  placeOfBirth?: string;
  dateOfBirth?: Date;
}

// refactored

// 두 개의 속성을 하나의 객체로 모으는 것이 더 나은 설계이다. null 값을 경계로 두는 방법과 비슷하다.

interface Person {
  name: string;
  birth?: {
    place: string;
    date: Date;
  };
}
```

- 타입의 구조를 손 댈 수 없는 상황이면, 앞서 다룬 인터페이스의 유니온을 사용해서 속성 사이의 관계를 모델링하자

```ts
interface Name {
  name: string;
}

interface PersonWithBirth extends Name {
  placeOfBirth?: string;
  dateOfBirth?: Date;
}

type Person = Name | PersonWithBirth;
```

- 유니온 타입의 속성을 여러 개 가지는 인터페이스에서는 속성 간의 관계가 분명하지 않기때문에 주의해야한다.
- 유니온의 인터페이스보다 인터페이스의 유니온이 더 정확하고 타입스크립트가 이해하기도 좋다.
- `태그`를 활용하는것은 타입스크립트가 제어 흐름을 분석하는데 도움을 준다.
