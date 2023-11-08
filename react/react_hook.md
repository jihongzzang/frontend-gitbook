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

[useState](https://react.dev/reference/react/useState)

## useEffect (내장 훅)

```ts
useEffect(setup, dependencies?)
```

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

[useEffect](https://react.dev/reference/react/useEffect)
[언제 써야할까?](https://react.dev/learn/you-might-not-need-an-effect)
[useEffect GoodBye](https://www.youtube.com/watch?v=bGzanfKVFeU&t=1019s)

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

[useReducer](https://react.dev/reference/react/useReducer)

[reducer 예제](https://github.com/jihongzzang/react-typescript/tree/master/src/Components/Reducer)

[useContext](https://react.dev/reference/react/useContext)

[context + reducer 예제](https://github.com/jihongzzang/react-typescript/tree/master/src/Components/ContextReducerRefactored)

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
