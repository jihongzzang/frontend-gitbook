# 상태관리 튜토리얼 마지막

## ObjectStore를 통한 예시

```tsx
//ObjectStore.ts
type Listener = () => void;

class ObjectStore {
  private listners = new Set<Listener>();

  addListener(listener: Listener) {
    this.listners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listners.delete(listener);
  }

  protected publish() {
    this.listners.forEach((listner) => listner());
  }
}

//CounterStore.ts
@singleton()
class CounterStore extends ObjectStore {
  count = 0;

  increase(step = 1) {
    this.count += step;
    this.publish();
  }

  decrease(step = 1) {
    this.count -= step;
    this.publish();
  }
}

//useCounterStore.ts
function useCounterStore() {
  const store = container.resolve(CountStore);

  return useObjectStore(store);
}

//useObejctStore.ts
function useObejctStore<T extends ObejctStore>(store: T) {
  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);

    return () => store.removeListener(forceUpdate);
  }, [store]);

  return store;
}
```

## useStore-ts

```node
$npm i useStore-ts
설치
```

```tsx
//CounterStore.ts

@singleton()
@Store() //Store와 singleton의 위치 주의
export default class CounterStore {
  count = 0;

  // state = {
  //   x: 1,
  // };

  @Action()
  increase(step = 1) {
    this.count += step;
    // this.state = { ...this.state, x: this.state.x + 1 };
  }

  @Action()
  decrease(step = 1) {
    this.count -= step;
    // this.state = { ...this.state, x: this.state.x - 1 };
  }
}

function useCounterStore() {
  const store = container.resolve(CountStore);

  return useStore(store);
}

/**원하는 곳에서 가져다 쓰면 됨!!*/
const [{ count }, store] = useCounterStore();
```
