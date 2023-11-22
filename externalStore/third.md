# TSyringe를 활용한 간단한 상태관리 만들어보기

앞선 버튼을 1씩 증가시키는 예제를 이런식으로 바꾸어보자.

두개의 `CounterComponent`는 같은 버튼을 눌렀을때 같은 결과값을 내어야 하고
버튼은 `CounterControllerComponent`에 있어야 한다면 어떻게 할 수 있을까?

```tsx
function App() {
  return (
    <div>
      <Counter />
      <Counter />
      <CounterController />
    </div>
  );
}
```

## 첫번쨰 방법

```tsx
function App() {
  const [number, setNumber] = useState(0);

  const increase = () => {
    setNumber((prev) => prev + 1);
  };

  return (
    <div>
      <Counter number={number} />
      <Counter number={number} />
      <CounterController onClickButton={increase} />
    </div>
  );
}
```

이정도만 구현하고 절대 변하지 않으면 나는 이걸로도 만족할 것 같다.
하지만 추가기능들이 들어간다면?
Contoller로 내려주는 props는 계속해서 증가시켜서 setNumber 자체를 내려주고 내부에서 업데이트를 진행해야 할것이다.

### 의존성 주입(Dependency Injection)

[위키](https://ko.wikipedia.org/wiki/%EC%9D%98%EC%A1%B4%EC%84%B1_%EC%A3%BC%EC%9E%85)

- 의존성 주입(dependency injection)은 하나의 객체가 다른 객체의 의존성을 제공하는 테크닉
- `의존성`은 예를 들어 서비스로 사용할 수 있는 객체이다. 클라이언트가 어떤 서비스를 사용할 것인지 지정하는 대신, 클라이언트에게 무슨 서비스를 사용할 것인지를 말해주는 것
- 즉, 클라이언트에게 서비스를 전달하는 것이 패턴의 기본 요건
- 클라이언트의 생성에 대한 의존성을 클라이언트의 행위로부터 분리 (의존관계 역전시키고 단일 책임 원칙을 위함)

```text
의존관계란?

A가 B를 의존한다. => 의존대상 B가 변하면, 그것이 A에 영향

의존관계를 인터페이스로 추상화하게 되면
  - 더 다양한 의존 관계를 맺을 수가 있다
  - 실제 구현 클래스와의 관계가 느슨해진다.
  - 결합도가 낮아진다.
```

장점

- 의존성이 줄어든다.
- 재사용성이 높은 코드가 된다.
- 테스트하기 좋은 코드가 된다.
- 가독성이 높아진다.

### reflect-metadata

#### 메타 프로그래밍과 메타언어

프로그래밍 하는 언어를 메타 언어라고 한다. 이 중 스스로 메타 언어가 되는 것을 리플렉션(Reflection)이라고 한다.
그리고 이러한 프로그래밍을 메타 프로그래밍이라고 함

#### 리플렉션

```tsx
function foo() {}
console.log(foo.name); // 'foo'

foo.name = 'bar';

console.log(foo.name); // NOT 'bar' But 'foo'

//함수의 name 필드는 읽기 전용으로 수정할 수 없는 게 특징

Object.defineProperty(foo, 'name', {
  //이 값을 true로 설정해보면?
  writable: true,
});

foo.name = 'bar';

console.log(foo.name); // 'bar'
```

### 리플렉트

- ES6부터 지원 Object API의 관련 기능을 모두 지원한다.

```tsx
Reflect.defineProperty(foo, 'name', { writable: true });

foo.name = 'bar';

console.log(foo.name); // 'bar'
```

[proxy와 reflect](https://ko.javascript.info/proxy)

[Typescript Handbook](https://typescript-handbook-ko.org/pages/decorators.html)

=> 특정 어플리케이션에서만 다루는 도메인 데이터를 프로그램 수준에 저장할 방법이 없는 한계가 있음.

[리플렉트 메타데이터](https://rbuckton.github.io/reflect-metadata/)

- 메타 데이터를 저장할 내부 슬롯을 추가하고 여기에 접근할 수 있는 `Reflect API`를 추가하는 것

```tsx
import 'reflect-metadata';

function foo() {}

Reflect.defineMetadata('정보', 1, foo);

Reflect.getMetatdata('정보', foo);
```

[내부 구현관련 로직](https://github.com/rbuckton/reflect-metadata)

### singleton (싱글톤)

[위키](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80%ED%84%B4_%ED%8C%A8%ED%84%B4)
[싱글톤이란](https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/)

- 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴
- 싱글톤 패턴은 전체 시스템에서 하나의 인스턴스만 존재하도록 보장

장점

- 최초 한번의 new 연산자를 통해서 고정된 메모리 영역을 사용하기 때문에 추후 해당 객체에 접근할 때 메모리 낭비를 방지
- 다른 클래스 간에 데이터 공유가 쉽다

그러나 싱글톤 패턴을 구현하기 위한 보일러플레이트 코드들과 테스트할때 매번 초기화도 해주어야하는 이슈등이 있으므로

`trade-off`를 따져서 도입하자.

jest의 `beforeEach` 메소드 그리고 singleton을 구현해주는 tsyringe등 다양한 라이브러리를 활용하는것도 방법

```js
//js에서 구현하는 간단한 싱글톤패턴
let instance = null;

class Singleton {
  constructor() {
    if (instance) return instance;
    this.name = 'jujihong';
    this.age = 30;
    instance = this;
  }
}

const first = new Singleton();
const second = new Singleton();

console.log(first === second); //true

console.log(instance === first); //true
```

### tsyringe

TypeScript용 DI 도구(IoC Container).

전역상태를 관리하는데 활용할 수 있다.

React 컴포넌트 입장에서는 `전역`처럼 여겨진다.

`Prop Drilling` 문제를 해결할 수 있는 방법 중 하나 (React로 한정하면 Context도 쓸 수 있다).

[official github](https://github.com/microsoft/tsyringe)

```tsx
쓰고자 하는 곳에서

import 'reflect-metadata';
```

## 두번째 방법 (전역 상태관리)

- 앞서서 말한 것들을 엮어서 tsyringe를 활용한 방법으로 해결해보자.

```tsx
//counter store를 먼저 만들어주자

import { singleton } from 'tsyringe';

type Listener = () => void;

type ForceUpdate = () => void;

@singleton()
class CounterStore {
  count = 0;

  listeners = new Set<ForceUpdate>();

  publish() {
    this.listeners.forEach((listener) => {
      listener();
    });
  }

  //methods
  increase() {
    this.count += 1;
    this.publish();
  }

  decrease() {
    this.count -= 1;
    this.publish();
  }

  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }
}

// useCounterStore.tsx
import { container } from 'tsyringe';
import useForceUpdate from '../../firstEx/hooks/useForceUpdate';
import CounterStore from '../stores/CounterStore';

function useCouterStore() {
  const forceUpdate = useForceUpdate();
  const store = container.resolve(CounterStore);

  useEffect(() => {
    //리스너를 등록해주자.
    store.addListener(forceUpdate);

    // cleanup을 통해 제거하자.
    return () => store.removeListener(forceUpdate);
  }, [store, forceUpdate]);

  return store;
}

// Counter.tsx
function Counter() {
  const store = useCouterStore();

  return <span>{`count: ${store.count}`}</span>;
}

// CounterController.tsx
function CounterController() {
  const store = useCouterStore();

  const increaseCount = () => {
    store.increase();
  };

  const decreaseCount = () => {
    store.decrease();
  };

  return (
    <div>
      <button type='button' onClick={increaseCount}>
        increase
      </button>
      <button type='button' onClick={decreaseCount}>
        decrease
      </button>
    </div>
  );
}

//App.tsx
function App() {
  return (
    <div>
      <Counter />
      <Counter />
      <CounterController />
    </div>
  );
}
```

## 후기

첫번째 방식에서도 물론 커스텀 훅을 통해 내부에서 빼더라도 App 컴포넌트 자체에서 해야하는 일(propDrilling)은 여전히 존재한다.
하지만 두번째 방식처럼 store 내부에서 로직을 처리해 주면 실제 App 컴포넌트 자체의 코드는 훨씬 더 깔끔해진다.
문제가 생겼을때는 Store내부에서 로직을 처리해주면 된다.
