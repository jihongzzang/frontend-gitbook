# 5장

## any 다루기

### Item 38 any 타입은 가능한 좁은 범위에서 사용하기

```ts
function processBar(b: Bar) {
  /*...*/
}

function f() {
  const x = expressionReturningFoo();
  processBar(x);
}

// 문백상 x라는 변수가 Foo, Bar 두타입에 할당 가능하다면, 오류를 제거하는 방법은 두가지 인데

function f() {
  const x: any = expressionReturningFoo(); // 이렇게 하지말자
  processBar(x);
}

function f() {
  const x = expressionReturningFoo();
  processBar(x as any); // 매개변수 안에서만 any가 사용되므로 다른 코드에는 영향을 미치지 않는다.
}

function f1() {
  const x: any = expressionReturningFoo(); // 이렇게 하지말자
  processBar(x);
  return x;
}

function g() {
  const foo = f1(); // 타입이 any
  foo.fooMethod(); //이 함수 호출은 체크되지 않는다. 버그의 시작이 이러남
}
```

- 타입스크립트가 함수의 반환 타입을 추론할 수 있는 경에도 함수의 반환 타입을 명시하는 것이 좋다.(any타입이 함수 바깥으로 영향을 미치는 것을 방지)

```ts
const config: Config = {
  a: 1,
  b: 2,
  c: {
    key: value, //어쩌고 저쩌고
  },
};

const config: Config = {
  a: 1,
  b: 2,
  c: {
    key: value,
  },
} as any; //이렇게 하지 말자

const config: Config = {
  a: 1,
  b: 2,
  c: {
    key: value as any,
  },
};
```

- 의도치 않은 타입 안정성의 손실을 피하기 위해 any의 사용 범위를 최소한으로 좁히자.
- 함수의 반환 타입이 `any`인 경우 타입 안정성이 나빠집니다. 따라서 any타입을 반환하면 절대 안된다.
- 강제로 타입 오류를 제거하려면 `any` 대신 `@ts-ignore` 사용하자.

### Item 39 any를 구체적으로 변형해서 사용하기

- any는 매우 큰 범위의 타입.
- 일반적으로는 더 구체적인 타입을 찾아 안정성을 높여야함
- any타입의 값을 그대로 정규식이나 함수에 넣는 것은 권장 되지 않는다.
- 함수의 매개변수를 구체화할 때, (요소의 타입에 관계없이) 배열의 배열 형태라면 `any[][]`처럼 선언하기.
- 함수의 매개변수가 객체이긴 하지만 값을 알 수 없다면 `{[key:string]: any}`처럼 선언하기.

```ts
//함수의 매개변수가 객체이긴 하지만 값을 알 수 없다면 앞의 정리한 내용 외에도 object타입을 사용할 수도 있음

// 키를 열거할 수는 없지만 속성에 접근할 수는 없음

function hasTwelveLetterKey(o: object) {
  for (const key in o) {
    if (key.length === 12) {
      console.log(key, o[key]);
      // 콘솔 부분 o[key] 시그니처 없으므로, 요소에 암시적으로 any 형식이 있다.
      return true;
    }
  }
  return false;
}
```

- 객체지만 속성에 접근 할 수 없어야 한다면 unknown 타입이 필요한 상황 일 수도 있다.

```ts
//함수의 ㅏ타입에도 단순히 any를 사용하지 말고

type Fn0 = () => any;
type Fn1 = (arg: any) => any;
type FnN = (...args: any[]) => any;
```

- any를 사용할 떄는 정말로 모든 값이 허용되어야만 하는지 검토하자.
- 더 구체적인 any의 형태를 사용하자.

### Item 40 함수 안으로 타입 단언문 감추기

- 내부 로직이 복잡해서 안전한 타입으로 구현하기 어려운 경우가 존재한다.

```ts
//함수 캐싱 예제
declare function cacheLast<T extends Function>(fn: T): T;
declare function shallowEqual(a: any, b: any): boolean;

function cacheLast<T extends Function>(fn: T): T {
  let lastArgs: any[] | null = null;
  let lastResult: any;

  return function (...args: any[]) {
    // '(...args: any[]) => any' 형식은 "T" 형식에 할당 할 수 없다.
    if (!lastArgs || !shallowEqual(lastArgs, args)) {
      lastResult = fn(...args);
      lastArgs = args;
    }
    return lastResult;
  };
}

//refactored 타입 단언문을 추가해서 오류를 제거하기 (정확한 타입이 추론되기 떄문에)

function cacheLast<T extends Function>(fn: T): T {
  let lastArgs: any[] | null = null;
  let lastResult: any;

  return function (...args: any[]) {
    //
    if (!lastArgs || !shallowEqual(lastArgs, args)) {
      lastResult = fn(...args);
      lastArgs = args;
    }
    return lastResult;
  } as unknown as T;
}

function shallowEqual<T extends object>(a: T, b: T): boolean {
  for (const [k, aVal] of Object.entries(a)) {
    // 요소에 암시적으로 any 형식이 있다.
    if (!(k in b) || aVal !== b[k]) {
      return false;
    }
  }
  return Object.keys(a).length === Object.keys(b).length;
}

function shallowEqual<T extends object>(a: T, b: T): boolean {
  for (const [k, aVal] of Object.entries(a)) {
    if (!(k in b) || aVal !== (b as any)[k]) {
      //..
    }
  }
  //
}
```

- 타입 선언문은 일반적으로 타입을 위험하게 만들지만 현실적인 해결책이 되기도한다.

- 선언문을 사용한다고 하면 정확한 정의를 가지는 함수 안으로 숨기자.
