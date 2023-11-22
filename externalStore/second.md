# 버튼을 1씩 증가 시키는 함수를 만들어보기

문득 useState의 앞의 인자 없이 어떻게 UI에 보여지는 count의 값을 증가시킬 수 있을까?

useState를 쓴다면 간단하게 이런식으로 구현할 수 있다.

## 첫번쨰 방법

```tsx
function App() {
  const [number, setNumber] = useState(0);

  const increase = () => {
    setNumber((prev) => prev + 1);
  };

  return (
    <div>
      <span>{`count: ${count}`}</span>
      <button type='button' onClick={increaseCount}>
        Increase
      </button>
    </div>
  );
}
```

=> 곱하기 만들어주세요. 뺴기 만들어주세요, 나누기 만들어주세요... 등등 여러가지 기획들이 더 들어온다면?

```tsx
function App() {
  /**state*/

    // const increase = () => {
    //     //...
    // };
    // const minus = () => {
    //     //...
    // };
    // const mulitply = () => {
    //     //...
    // };
    // const divide = () => {
    //     //...
    // };
    // const other.... = () => {
    //     //...
    // };

  return (
    //...
  );
}
```

## 두번째 방법

=> 상태를 그냥 전역으로 설정하고, 로직도 분리할 수는 없을까?

```tsx
type State = {
  count: number;
};

const state = {
  count: 0,
};

function reducer(state: State) {
  return { ...state, count: state.count + 1 };
}

function App() {
  const { count } = state;

  const [, forceUpdate] = useReducer(reducer, { count: 0 });

  const increaseCount = () => {
    state.count += 1;
    forceUpdate();
  };

  return (
    //...
  );
}
```

## 리팩토링

forceUpdate를 훅으로 만들어보자

```tsx
function useForceUpdate() {
  const [, setState] = useState({});

  return useCallback(() => setState({}), []);
}

function increase() {
  state.count += 1;
}

function App() {
  const { count } = state;

  const forceUpdate = useForceUpdate();

  const increaseCount = () => {
    increase();
    forceUpdate();
  };

  return (
   //...
  );
}
```

## 후기

이런식으로 구현할 수 있을 것이다.
처음과 다른점은 점점 상태를 변경시킬때 변경시키고자 하는 부분들을 컴포넌트 바깥으로 뺴버렸다.

사실 굳이 이렇게 까지 해야 하나라고 생각이 들수도 있지만
추후 여러 UI들이 결합된 수백줄짜리의 코드와 얽혀있는 컴포넌트들을 만나게되었을때 더 효과적인 유지보수를 하게 해준다.
