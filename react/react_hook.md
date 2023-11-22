# React-With-Hook

## React Hook

### 개요

- React 16.8에서 Hooks가 도입

  - 컴포넌트 사이에서 상태 로직을 재사용하기 어렵

    - 기존 방식 (`renderProps` 또는 `고차 컴포넌트` Class Function) 이런식으로 문제를 해결
    - React는 상태 관련 로직을 공유하는 것이 필요했다.
    - Hook을 사용하면 컴포넌트로부터 상태 관련 로직을 추상화할 수 있고 이를 이용해 독립적인 테스트와 재사용이 가능하다.
    - Hook은 계층의 변화 없이 상태 관련 로직을 재사용

  - 복잡한 컴포넌트들은 이해하기 어렵다.

    - 기존 생명주기에셔는 유지보수 과정에서 해당 컴포넌트와 관련없는 로직들이 들어가곤 했음.
    - Hook을 통해 서로 비슷한 것을 하는 작은 함수의 묶음으로 컴포넌트를 나누는 방법

  - Class은 사람과 기계를 혼동시킨다.
    - JS의 `This`를 모르고는 사용이 불가한 수준이었음

- 결국 훅의 등장은 기존의 상태관련로직을 재사용 할 수 있고, Class 없이 React로 개발을 하게 해주겠다는 리액트측의 배려? 아니면 본인들이 불편해서 만들어낸 결과물인것인가.

```text
그럼에도 불구하고 수없이 외부 API나 라이브러리등을 만나면 Class로 쓰여진 JS라이브러리들을 많이 만날 수 있고 Class를 모르면 그것들을 함수형으로 만들어낼 수 없으니 알고있자.
```

### 그래서 훅이 뭘까?

- `Hook`은 함수 컴포넌트에서 React state와 생명주기 기능(lifecycle features)을 “연동(hook into)“할 수 있게 해주는 함수

## useState (내장 훅)

```ts
const [state, setState] = useState(initialState);
```

[useState](https://react.dev/reference/react/useState)

- 컴포넌트에 상태 변수를 추가할 수 있게 해주는 함수

- 컴포넌트의 최상위 수준에서 호출

- setState가 상태를 변경시키고 다시 렌더링을 트리거 할 수 있게해주는 함수

- Strict 모드에서 React는 실수로 발생한 부작용들을 찾는 데 도움을 주기 위해 초기화 함수를 두 번 호출

- `상태 업데이트를 일괄 처리, 모든 이벤트 핸들러가 실행되고 해당 기능을 호출한 후에 화면을 업데이트!!`

- 만약 화면을 더 일찍 업데이트하도록 강제해야 하는 드문 경우에는 `flushSync` 사용하자.

```ts
const [name, setName] = useState('');

function event() {
  setName('jihong');
  console.log(name); // "" 여기서는 이전값이 나옴
}

event();
```

- 만약 객체또는 리스트 상태를 업데이트 해야하는 경우에는 기존 객체를 변경하기 보다는 상태를 교체 해야 한다.

## useEffect (내장 훅)

```ts
useEffect(setup, dependencies?)
```

[useEffect](https://react.dev/reference/react/useEffect)
[언제 써야할까?](https://react.dev/learn/you-might-not-need-an-effect)
[useEffect GoodBye](https://www.youtube.com/watch?v=bGzanfKVFeU&t=1019s)

- 컴포넌트를 외부의 시스템과 동기화 할 수 있게 해주는 함수

- 렌더링 이후 해야 할 일, 즉 React의 외부와 관련된 일을 정해줄 수 있다.

- 컴포넌트 최상위 레벨에서 호출

- Strict 모드에서 역시나 두번 호출됨

- 실제로 데이터를 받아와서 초기 상태값으로 쓴다던지. 외부의 어떤 상태에 따라 내가 보여지는 ui가 업데이트 되어야한다던지 등..
  정말 여러가지상황에서 쓰여질 수 있다. 점점 effect에 잠식당할지도 모른다. 그렇지만 `언제 써야할까?`를 보면 어느상태에서 써야하는지 예시들이 있음
  생각외로 공식문서에 예제들을 보면 좋은예제들이 많아서 따라해보면 몰랐던부분들을 알 수 있다.

- Effect가 필요하지 않은 두 가지 일반적인 경우

  - 렌더링을 위해 데이터를 변환하는 데 효과가 필요하지 않다.
  - 사용자 이벤트를 처리하는 데 Effects가 필요하지 않다.

- 추가로 `useEffect GoodBye`라는 영상을 링크가 있는데 시간될때 한번씩들 보시기를 바란다.

```ts
//something component

const [count, setCount] = useState(1);

useEffect(() => {
  console.log(1); //마운트 되었을떄, 의존성 배열에 따라 리렌더링이 일어났을때(second)

  return () => {
    console.log(2); //의존성 배열에 따라 리렌더링이 일어났을때(first), 언마운트 되었을떄
  };
}, [dependencies]);

useEffect(() => {
  console.log('first'); // 마운트 되었을때

  return () => {
    console.log('end'); // 언마운트 되었을때
  };
}, []);
```

## useReducer, useContext(내장 훅)

- ExternalStore Section의 Flux 부분과 연관해서 보면 더 쉽게 이해가 될 것이다.

[useReducer](https://react.dev/reference/react/useReducer)

[reducer 예제](https://github.com/jihongzzang/react-typescript/tree/master/src/Components/Reducer)

[useContext](https://react.dev/reference/react/useContext)

[context + reducer 예제](https://github.com/jihongzzang/react-typescript/tree/master/src/Components/ContextReducerRefactored)

```ts
const [state, dispatch] = useReducer(reducer, initialArg, init?);
const value = useContext(SomeContext);
//reducer => 상태가 업데이트되는 방법을 지정
//initialArg => 초기 상태가 계산되는 값
//init => 초기 상태를 반환해야 하는 초기화 함수
```

- `useReducer`의 경우 복잡한 상태관리 (form) 같은 로직을 핸들링 할떄 쓰면 한곳에서 관리가 수월함!!
- `useContext`의 경우 최상위 컴포넌트레벨에서 지정한 어떠한 상태를 하위컴포넌트에서 props없이 값을 가져다 쓸 수 있게 해준다. (ex: theme... )

```ts
나의 경우는 처음엔 state 코드들로 틀을 작성한 후에 reducer로 옮기는 작업을 선호한다.
```

```tsx
//나만의 커스텀 리듀서 만들기
import { useState } from 'react';

type Reducer<State, Action> = (state: State, action: Action) => State;

export function useCustomReducer<State, Action>(
  reducer: Reducer<State, Action>,
  initialState: State
): [State, (action: Action) => void] {
  const [state, setState] = useState<State>(initialState);

  function dispatch(action: Action) {
    const nextState = reducer(state, action);
    setState(nextState);
  }

  return [state, dispatch];
}
```

## useState와 useReducer를 비교

```text
- 코드 사이즈

useState의 경우 컴포넌트 상단 부분에 한번만 선언하면 되는 반면, useReducer는 reducer, action을 만들어 주어야한다.
번거롭지만 수많은 이벤트가 발생하는 경우에 reducer는 좀더 확장성이고 여러 로직코드들이 폭 넓게 퍼지는 것을 방지해줌.

- 가독성

비즈니스 로직이 많아질 경우 reducer를 통해 가독성을 올릴 수 있다. 상태를 업데이트 하는 부분들은 reducer라는 곳에 명확히 존재하기 때문에.

- 디버깅

useState의 경우 잘못사용하거나 이해도가 적을 경우 디버깅이 힘듬.. 혹여나 코드가 엄청나게 복잡해질경우 로깅이 더더욱 힘들어짐.
useReducer를 사용할 경우 reducer 내부에서 action에 대해 console.log를 찍을 수 있기 때문에 상태 업데이트가 어떻게 되고 있는지 투명하게 볼 수 있습니다.

- 테스팅

reducer 로직이 컴포넌트에 종속적이지 않기에 테스트 코드를 작성하기 용이함.
```

## useRef (내장 훅)

```ts
const ref = useRef(initialValue);
```

[useRef](https://react.dev/reference/react/useRef)

- 렌더링에 필요하지 않은 값을 참조할 수 있는 함수

- 컴포넌트 최상위 레벨에서 호출

- 반환값은 하나의 속성을 가진 객체 {current: ....}

- 속성을 변경할 수 있으나 렌더링에 사용되는 개체를 가지고 있을경우 해당 개체를 변경하면 안됨.

- 속성을 변경하면 구성 요소를 다시 렌더링 하지는 않음

- 초기화를 제외하고 렌더링 중에는 읽거나 쓰지 말자

- Strict 모드에서 역시나 두번 호출됨

## useMemo (내장 훅)

```tsx
const cachedValue = useMemo(calculateValue, dependencies);
```

[useMemo](https://react.dev/reference/react/useMemo)

- 재렌더링 간의 계산 결과를 캐시

- 구성 요소의 최상위 수준에서 호출

- React는 특별한 이유가 없는 한 캐시된 결과를 버리지 않는다.

- 루프나 조건 내에서는 호출할 수 없다.

- Strict 모드에서 역시나 두번 호출됨

```tsx
// 계산 비용이 많이 드는지 어떻게 알 수 있나요?
console.time('filter array');
const visibleTodos = filterTodos(todos, tab);
console.timeEnd('filter array'); //계산해보자.

//가장 정확한 타이밍을 얻으려면 프로덕션용 앱을 빌드하고 사용자가 사용하는 것과 같은 기기에서 테스트해야함.

//useMemo를 어디에나 추가해야 합니까?

//앱이 이 사이트와 같고 대부분의 상호 작용이 대략적(예: 페이지 또는 전체 섹션 교체)인 경우 일반적으로 메모가 필요하지 않다.

//반면에 앱이 편집기에 더 가깝고 대부분의 상호 작용이 세분화된 경우(예: 모양 이동) 메모 기능이 매우 유용할 수 있습니다.
```

```tsx
//다른 훅의 의존성을 메모이징하기

function Dropdown({ allItems, text }) {
  const searchOptions = { matchMode: 'whole-word', text };

  const visibleItems = useMemo(() => {
    return searchItems(allItems, searchOptions);
  }, [allItems, searchOptions]);
}

function Dropdown({ allItems, text }) {
  const searchOptions = useMemo(() => {
    return { matchMode: 'whole-word', text };
  }, [text]);

  const visibleItems = useMemo(() => {
    return searchItems(allItems, searchOptions);
  }, [allItems, searchOptions]);
}
```

## useCallback (내장 훅)

```tsx
const cachedFn = useCallback(fn, dependencies);
```

[useCallback](https://react.dev/reference/react/useCallback)

- 재렌더링간의 함수를 메모이제이션 하게 해주는 훅

- 즉, 리렌더링이 발생했을때 dependencies에 따라 비교여부를 결정하고 새로운걸 쓸지 말지를 결정함.

- 컴포넌트의 최상위 수준 이나 자체 Hook에서만 호출할 수 있다.

- 루프나 조건 내에서는 호출할 수 없다.

- React는 특별한 이유가 없는 한 캐시된 함수를 버리지 않는다.

- Strict 모드에서 역시나 두번 호출됨

```tsx
import { memo } from 'react';

const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  // ...
});

// 만약 handleSubmit 함수가 만약에 useCallback을 쓰지 않는다면, theme이 바뀌어도 매번 바뀔것이고

// memo로 감싼 ShippingForm은 그냥 쓰지않은것과 별반 다르지 않다. => handleSubmit은 매번 갱신될 것이기 때문에
// useCallback으로 감싼 handleSubmit의 경우 theme이 바뀌더라고 함수를 재사용할 것이고 불필요한 리렌더링을 방지할 수 있다.
function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = (orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  };

  const handleSubmit = useCallback(
    /* ... **/
    [productId, referrer]
  );

  return (
    <div className={theme}>
      {/* ...ShippingForm will receive the same props and can skip re-rendering */}
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}
```

- 만약에 useCallback을 감싸기전에는 원하지 않는 방향으로 동작하고 감쌌을때 원하는 방향으로 동작한다면.. useCallback은 근본적인 해결책이 아님!! 본인의 코드를 수정하자.

```tsx
//메모된 콜백으로 부터 상태 업데이트

function TodoList() {
  const [todos, setTodos] = useState([]);

  const handleAddTodo = useCallback(
    (text) => {
      const newTodo = { id: nextId++, text };
      setTodos([...todos, newTodo]);
    },
    [todos]
  );

  const handleAddTodo = useCallback((text) => {
    const newTodo = { id: nextId++, text };
    setTodos((todos) => [...todos, newTodo]);
  }, []); // 의존성 주입이 필요하지 않게 바꿀수 있음.
}
```

```tsx
//커스텀 훅 최적화

function useRouter() {
  const { dispatch } = useContext(RouterStateContext);

  const navigate = useCallback(
    (url) => {
      dispatch({ type: 'navigate', url });
    },
    [dispatch]
  );

  const goBack = useCallback(() => {
    dispatch({ type: 'back' });
  }, [dispatch]);

  //반환되는 모든 훅을 함수로 작성하는 것이 좋다.
  return {
    navigate,
    goBack,
  };
}
```

## useSyncExternalStore (내장 훅)

```ts
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

- 리액트의 바깥 상태도구 내부의 상태관리도구와 싱크를 맞춰주는 훅
- 18버젼 들어서 새롭게 생김!!
- 외부 상태의 변경사항을 관찰하고 있다가 tearing이 발생하지 않도록 상태 변경이 관찰되면 다시 렌더링을 시작
- getSnapshot은 직전 렌더링 시점과 비교해 스토어의 상태 값이 변경되었는지를 확인하기 위해 넣는 값

[useSyncExternalStore](https://react.dev/reference/react/useSyncExternalStore)

- 특정 필드만 subscribe 하는 코드

```tsx
const selectedField = useSyncExternalStore(
  store.subscribe,
  () => store.getSnapshot().selectedField
  //이 부분이 selector
);
```

### concurrent feature

- 렌더링 타이밍 도중 사용자의 입력과 같은 즉각적으로 UI에 적용되어야 하는 부분에 대해 우선순위를 정해 렌더링 할 수 있는 기능
- 리액트의 내부 상태스토어는 concurrent feature에 대비해 알고리즘을 구현했지만, 외부렌더링을 사용할 경우 문제가 발생할 수도 있었음.

[React Github Issue](https://github.com/reactwg/react-18/discussions/84)

## useLayoutEffect (내장 훅)

```ts
useLayoutEffect(setup, dependencies?)
```

- 브라우저가 화면을 다시 그리기 전에 수행되어야 하는 것이 있을떄 사용하는 함수 (개인적으로는 absolute한 포지션을 가진 ui의 위치계산등에 쓰면 편함)

- 클라이언트 렌더링 중에만 발생함.

- 컴포넌트 최상위 레벨에서 호출

## Hook's Rule

[훅의 규칙](https://ko.legacy.reactjs.org/docs/hooks-rules.html)

- 최상위에서만 Hook을 호출해야함
- React 함수 내에서 훅을 호출해야함
- 훅의 이름을 명명할때는 use를 붙여주는것이 관례임

## StrictMode

- 개발단계에서 버그를 찾는데에 도움을 주는 모드

  - will re-render an extra time to find bugs caused by impure rendering.
  - will re-run Effects an extra time to find bugs caused by missing Effect cleanup.
  - will be checked for usage of deprecated APIs.

- 전체가 아니라 일부 컴포넌트에만 Strict 모드가 가능함

## Custom Hook

- [Reusing Logic with Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)

- 컴포넌트간에 로직을 공유하도록 해준다. (연결상태가 끊어졌는데 연결됐는지? 이런것들은 모든 컴포넌트가 공유해야하는 로직일 수도 있음)

- 커스텀 훅은 상태 자체가 아닌 상태 저장하는 논리만 공유

- 하나의 Hook에서 다른 Hook으로 상태 값을 전달할 수 있으며 최신 상태로 유지

- 구성 요소가 다시 렌더링될 때마다 모든 Hook이 다시 실행

- 커스텀 훅은 순수함수를 유지해야한다.

```text
처음부터 아 이건 커스텀 훅으로 저건 커스텀훅으로 빼야겠다라는 생각보다는
일단 구현을 해보고나서 공통되는 로직이 있는지, 포넌트 레벨에서 이 코드들을 관리하는것이 맞는지 등
을 고민하고 커스텀훅으로 빼내는 것이 편하다.
```

```ts
// 공식 문서의 예제인데 만약에 커스텀 훅이 없으면 매번 이런 코드들이 컴포넌트에 들어있어야 하는데 굉장히 비효율적임

// 그냥 이 로직을 재사용이 가능하도록 커스텀 훅으로 변환
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}

//원하는 곳에서는 이렇게 한줄의 간단한 코드로 관리가 가능함

const isOnline = useOnlineStatus();
```

## usehooks-ts

[usehooks-ts](https://usehooks-ts.com/introduction)

- 다양한 커스텀 훅들이 공유된 사이트. 특히 타입스크립트로 작성되어 타입관련된 부분에 대해서도 한눈에 볼 수 있음

- 있는 훅들중에 몇가지만 간단하게 소개하자면

  - useBoolean

    - 참인지 거짓인지 다룰때 쓰기 편한 훅

  - useEffectOnce

    - 의존성 배열을 한 번만 실행하는 Effect를 구현하고자 할때.

  - useFetch

    - 정말 간단하게 Fetch를 사용하고 싶을때 (그렇지만 거의 쓸일은 없음.)
    - 개인적으로는 `SWR`, `React-Query` 등 유용한 대안이 될 수 있는 라이브러리들이 많이 존재해서 앞에 말한 두가지를 추천한다. (mutate, cache, 전역적으로 데이터 상태를 관리한다던지 정말 편한 기능들이 많음!!)

  - useInterval

    - 타이머 관련해서 여러가지 사이드 이펙트들이 있어서 이거를 가져다 쓰는게 유용함. 여기서 추가로 더 커스텀도 가능하다.

  - useEventListener

    - 모든 종류의 이벤트를 확인할 수 있음. (window, HTMLelement, customEvent 등)
    - 특히 dispatchEvent로 전달되는 커스텀 이벤트에 반응하기 좋다.

  - useLocalStorage

    - localStorage와 JSON으로 객체 Persist 해준다.
    - 이벤트를 통해(dispatchEvent + useEventListener) 다른 컴포넌트와 동기화하는 게 매우 중요한 특징.

  - useIntersectionObserver

    - 무한 스크롤 관련해서 구현할때 쓰기 편하다.
    - 대신 intersectionObserverAPI 에 대한 지식은 필요하다.

  - useDarkMode

    - 다크모드인지 아닌지를 구별해서 전체적으로 스타일을 바꿔줄때 좋음

  - useWindowSize

    - 윈도우 사이즈에 따라 어떠한 효과가 필요할때

  - useDebounce
    - 어떠한 debounce 이벤트를 주고 싶을때

이것 외에도 [toss library](https://slash.page/ko/) 토스에서 제공하는 유틸리티 도 유용하다.

```ts
가져다 쓰는 건 좋지만 적어도 이게 어떤식으로 코드들이 이루어져 있는지는 보는것이 굉장히 중요하다.
실제로 나는 해당 커스텀훅에 추가적인 callback을 받는다던가 추가적인 매개변수를 받아서 수정해서 사용했었던 경험도 있었다.
특히 훅들의 타입들에 제네릭이 도입되어 있기때문에 타입스크립트 제네릭을 공부하는 부분들에있어도 좋은 코드들이 될 수 있다고 생각한다.
```
