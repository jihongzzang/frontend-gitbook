# Redux

Redux를 더 잘 다루는 방법이 아닌 하나의 상태관리 도구로써 Redux가 뭔지 알아보고
나만의 Action Dispatch Store 를 만들어보자.

[Redux Official Site](https://redux.js.org/)
[Redux Overview and Concepts](https://ko.redux.js.org/tutorials/essentials/part-1-overview-concepts/)
[Flux 알아보기](https://s-organization-196.gitbook.io/frontend-megatera/externalstore/first)

## Redux란? (이렇게 사용해주세요.) - 공식문서

- 하나의 상태관리 도구

- A single store containing "global" state

  전역 상태를 포함하는 하나의 스토어

- Dispatching plain object actions to the store when something happens in the app

  앱에서 어떤일이 일어났을때 액션을 스토어에 전달

- Pure reducer functions looking at those actions and returning immutably updated state

  액션을 바라보는 순수 리듀서 함수 그리고 그 함수는 불변적으로 업데이트된 상태를 반환시켜준다.

## Redux를 사용해야 하는 경우 - 공식문서

- 앱의 여러 위치에 필요한 많은 양의 애플리케이션 상태가 있습니다.
- 앱 상태는 시간이 지남에 따라 자주 업데이트됩니다.
- 해당 상태를 업데이트하는 논리는 복잡할 수 있습니다.
- 앱에 중간 또는 대규모 코드베이스가 있으며 많은 사람들이 작업할 수 있습니다.

## Redux와 ContextAPI

[Redux와 React Context](https://changelog.com/posts/when-and-when-not-to-reach-for-redux)

- React 16.3 에서 새롭게 ContextAPI를 출시했다.

- ContextAPI의 유일한 목적은 컴포넌트를 담당하는 여러 하위트리가 최상위의 값을 가져다 쓰기 위함 (전역상태관리 X)

```text
ContextAPI는 PropsDrillng을 해결할 수 있는 하나의 방법

조금 더 이야기 해보자면 useState나 useReducer를 활용해서 useContext훅과 함꼐 일종의 상태관리 시스템을
구성할 수 있는 것이지 Context그 자체가 상태관리 시스템이 아니다.
```

[Idiomatic Redux](https://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/#how-redux-actually-works)
[Idiomatic Redux Part2](https://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-2/)

## Redux의 createStore

[createstore](https://redux.js.org/api/createstore)

```tsx
function createStore(reducer) {
  var state;
  var listeners = [];

  function getState() {
    return state;
  }

  function subscribe(listener) {
    listeners.push(listener);
    return function unsubscribe() {
      var index = listeners.indexOf(listener);
      listeners.splice(index, 1);
    };
  }

  // dispatch내부의 함수는 단순히 createStore리듀서 함수를 호출하고 그것이 반환하는 값을 저장
  function dispatch(action) {
    state = reducer(state, action);
    listeners.forEach((listener) => listener());
  }

  dispatch({});

  return { dispatch, subscribe, getState };
}
```

`createStore` function의 두가지 제약 (`Flux Architecture`)

- actions that reach the store must be plain objects,

=> 순수 객체로서 스토어에 도달해야한다.!!

- actions must have a type field that is not undefined.

=> 타입필드가 필요하다.

## The Built-In Utility: combineReducers

[combineReducers](https://redux.js.org/api/combinereducers)

- 리듀서는 모든 하위 리듀서를 호출하고 그 결과를 단일 상태 객체로 수집

```tsx
export default function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([action.text]);
    default:
      return state;
  }
}

export default function counter(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
}

export default combineReducers({
  todos,
  counter,
});

const store = createStore(reducer);
console.log(store.getState());

// {
//   counter: 0,
//   todos: []
// }

store.dispatch({
  type: 'ADD_TODO',
  text: 'Use Redux',
});
```

## React-Redux and connect

- 현재 결과와 이전 반환 결과에 대해 얕은 동일성 검사를 수행하여 있는지 확인
  동일하다면 렌더링을 건너뜀

## Redux Toolkit

[Why Redux Toolkit is How To Use Redux Today](https://redux.js.org/introduction/why-rtk-is-redux-today)

- 리덕스를 활용하면 수많은 보일러 플레이트가 생겼다. => 이러한 불편함을 인지하고 => 더 쉬워진 툴킷을 만들어냄
- 리덕스 툴킷은 더 쉬워진 리덕스!!

```text
우리는 손으로 작성한 Redux 로직에서 "상용구"를 제거하고, 일반적인 실수를 방지하며,
표준 Redux 작업을 단순화하는 API를 제공하기 위해 특별히 Redux 툴킷을 만들었다.!!
```

```tsx
// 사용법 예시
const todosSlice = createSlice({
  name: 'todos',

  initialState: [],

  reducers: {
    todoAdded(state, action) {
      state.push({
        id: action.payload.id,
        text: action.payload.text,
        completed: false,
      });
    },
    todoToggled(state, action) {
      const todo = state.find((todo) => todo.id === action.payload);
      todo.completed = !todo.completed;
    },
  },
});

export const { todoAdded, todoToggled } = todosSlice.actions;

export default todosSlice.reducer;

export const store = configureStore({
  reducer: {
    todos: todosSlice.reducer,
  },
});
```

## 리덕스 흉내내보기

앞에서 리덕스의 코어인 스토어와 리듀서 디스패치를 직접 만들어보고 카운터 앱을 만들어보자

[code](https://github.com/jihongzzang/react-typescript/blob/master/src/Components/ExternalStore/reduxCopyExample/index.tsx)

```tsx
//BaseStore.ts
type Action<Payload> = {
  type: string;
  payload?: Payload;
};

type Reducer<State, Payload> = (state: State, action: Action<Payload>) => State;

type Reducers<State> = Record<string, Reducer<State, any>>;

type Listener = () => void;

class BaseStore<State> {
  state: State;

  reducer: Reducer<State, any>;

  listeners = new Set<Listener>();

  constructor(initialState: State, reducers: Reducers<State>) {
    this.state = initialState;
    this.reducers = (state: State, action: Action<any>): State => {
      const reducer = Reflect.get(reducers);

      if (!reducer) {
        return state;
      }

      return reducer(state, action);
    };
  }

  dispatch<T>(action: Action<T>) {
    this.state = this.reducer(this.state, action);
    this.listeners.forEach((listener) => listener());
  }

  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }
}

//Store.ts
const initialState = { count: 0, name: 'jihong' };

type State = typeof initialState;

function increase(state: State, action: Action<number>) {
  return { ...state, count: state.count + (action.payload ?? 1) };
}

function decrease(state: State, action: Action<number>) {
  return {
    ...state,
    count: state.count - (action.payload ?? 1),
  };
}

const reducers = {
  increase,
  decrease,
};

@singleton()
class Store extends BaseStore<State> {
  constructor() {
    super(initialState, reducers);
  }
}

export default function useDispatch<Payload>() {
  const store = container.resolve(Store);

  return (action: Action<Payload>) => {
    store.dispatch(action);
  };
}

type Selector<T> = (state: State) => T;

function useSelector<T>(selector: Selector<T>): T {
  const store = container.resolve(Store);

  const [state, setState] = useState<T>(selector(store.state));

  const forceUpdate = useForceUpdate();

  useEffect(() => {
    const update = () => {
      const newState = selector(store.state);

      if (!shallowEqual(newState, state)) {
        setState(newState);
        forceUpdate();
      }

      if (newState !== state) {
        forceUpdate();
        setState(newState);
      }
    };

    store.addListener(update);

    return () => store.removeListener(update);
  }, [state, forceUpdate]);

  return state;
}

function shallowEqual(objA: any, objB: any): boolean {
  for (const key in objA) {
    if (objA[key] !== objB[key]) {
      return false;
    }
  }
  return true;
}
```
