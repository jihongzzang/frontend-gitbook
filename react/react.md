# React

[React Official Site](https://ko.legacy.reactjs.org/)

[React Dev Site](https://react.dev/)

선언형

- 애플리케이션의 각 상태에 대한 간단한 뷰만 설계하세요. 그럼 React는 데이터가 변경됨에 따라 적절한 컴포넌트만 효율적으로 갱신하고 렌더링 합니다.

컴포넌트 기반

- 스스로 상태를 관리하는 캡슐화된 컴포넌트를 만드세요. 그리고 이를 조합해 복잡한 UI를 만들어보세요.

홈페이지에 들어갔을때 두가지가 나오는데 두개를 봤을때

리액트는 애플리케이션의 상태에 대한 뷰를 만들어내는 도구라고 생각한다.

실제로 공식사이트에 정의 된 내용은

```text
React는 사용자 인터페이스를 구축하기 위한 선언적이고 효율적이며 유연한 JavaScript 라이브러리입니다.

“컴포넌트”라고 불리는 작고 고립된 코드의 파편을 이용하여 복잡한 UI를 구성하도록 돕습니다.
```

```text
컴포넌트?

재사용이 가능한 각각의 독립된 모듈
```

- 결국 리액트에서 컴포넌트는 재사용이 가능한 각각의 독립된 모델들로 복잡한 UI를 구성하는 도구라고 할 수 있다.

리액트의 리렌더링 조건

- 부모 컴포넌트가 리렌더링되면 자식 컴포넌트도 리렌더링

- 자식 컴포넌트의 props나 state에 변경 사항이 있었느냐는 무관

```js
const Parent = () => {
  return (
    <Child1>
      <Child2>
        <Child3 />
      </Child2>
    </Child1>
  );
};

const Child1 = ({ children }) => <div>{children}</div>;

const Child2 = ({ children }) => <div>{children}</div>;

const Child3 = () => <div>나는 막내</div>;
```

- 부모 컴포넌트가 리렌더링되면 자식 컴포넌트들도 리렌더링

- 자식 컴포넌트란 부모 컴포넌트의 JSX 안에서 사용된 모든 컴포넌트들

- 형제 관계인 컴포넌트들끼리는 서로 리렌더링에 영향을 미치지 않음

```js
function App() {
  return (
    <Parent lastChild={<ChildC />}>
      <ChildB />
    </Parent>
  );
}

function Parent({ children, lastChild }) {
  return (
    <div className='parent'>
      <ChildA />
      {children}
      {lastChild}
    </div>
  );
}

여기서 Parent가 리렌더링 되면 무엇이 렌더링 될까? A만 리렌더링!!
```

리액트에서 상태

- 부모로부터 props를 통해 전달? 그러면 확실히 state가 아니다.

- 시간이 지나도 변하지 않나? 그러면 확실히 state가 아니다.

- 컴포넌트 안의 다른 state나 props를 가지고 계산 가능한가? 그렇다면 state가 아니다.

리액트에는 렌더 단계(render phase)와 커밋 단계(commit phase)의 두 단계가 있습니다. 위 그림과 함께 보시면, 2 가지 렌더링과 2 가지 단계는 서로 일치하지 않습니다:

2가지 렌더링과 2가지 단계(phase)

- Render 단계: 컴포넌트 렌더링부터 엘리먼트 렌더링의 재조정까지. 즉 이전 렌더링과 비교하여 변경된 부분을 파악

- Commit 단계: 변경된 부분만 DOM을 업데이트

- Commit 단계에서 DOM을 업데이트한 후에는 라이프사이클 메소드와 useEffect가 실행

제어의 역전

- 코드의 로직이 일반적인 제어 흐름이 아니라 역전된 것

왜 IOC를 사용해?

- 유지보수 비용을 줄일 수 있다.

- 구현이 좀 더 단순해진다.

```js
function filter(
  array,
  { filterNull = true, filterUndefined = true, filterNumber = false } = {}
) {
  let newArray = [];

  array.forEach((element) => {
    if (filterNull && element === null) return;
    if (filterUndefined && element === undefined) return;
    if (filterNumber && typeof element === 'number') return;

    newArray.push(element);
  });

  return newArray;
}

filter([0, 1, undefined, 2, null, 3, 'four', '']);
// [0, 1, 2, 3, 'four', '']

filter([0, 1, undefined, 2, null, 3, 'four', ''], { filterNumber: true });
// [ 'four', '' ]

만약에 이 코드를 여러사람이 쓰고 있어서 수정해야한다면? 원치않는 버그가 발생할지도 모른다. 하지만 이것을 콜백 함수가 처리하게 한다면?

function filter(array, callback) {
  let newArray = [];

  array.forEach(element => {
    if (callback(element)) {
      newArray.push(element);
    }
  });

  return newArray;
}

// 더 특별한 case에 대한 처리가 가능함
filter(
  [
    { name: '독서하기', duration: 120 },
    { name: '밥먹기', duration: 60 },
    { name: '공부하기', duration: 100 },
  ],
  task => task.duration >= 100,
);
// [ { name: '독서하기', duration: 120 }, { name: '공부하기', duration: 100 } ]
```

리액트에서 쓰이는 IoC

- 어떤 방식으로 view를 업데이트 할 것인가

- 언제 view를 업데이트 할 것인가

Framework(프레임워크)

- 특정 개념들의 추상화를 제공하는 여러 클래스나 컴포넌트로 구성
- 추상적인 개념들이 문제를 해결하기 위해 같이 작업하는 방법을 정의
- 컴포넌트들은 재사용이 가능
- 높은 수준에서 패턴들을 조작화 할 수 있다.

Library(라이브러리)는 단순 활용가능한 도구들의 집합

그 둘의 차이는 쉽게 말해 어플리케이션의 Flow를 누가 쥐고 있느냐

가상돔 (Virtual DOM) 과 재조정

```text
저는 "가상 DOM"이라는 표현이 폐기되길 바랍니다. - Dan Abramov(Redux 개발자)의 트윗
```

React는 렌더링 과정에서 확인한 ReactElements로 이루어진 하나의 트리 구조를 만든다.

- 가상돔과 현재 노드 트리를 비교해서 변경점들을 DOM에 적용시키는 것도 결국 같은 종류의 노드 트리끼리 비교하는 것

- 렌더링 결과물도 가상돔이고 만들어져 있던 이전의 트리도 가상돔이라면 그냥 둘 다 트리로 부를 수 있다.

재조정

```text
재조정은 React에서 어떤 부분들이 변해야 하는지 두 개의 트리를 비교하는 데 사용하는 알고리즘이다.
```

Fiber

- ReactElement를 확장시켜 만드는 객체에 더 다양한 정보를 담아 트리를 만든다.

즉,

- React는 렌더링 과정에서 JSX로 만든 ReactElement를 확장시킨 Fiber 객체를 만든다.

- Fiber객체들로 이루어진 트리를 만들어 이전에 만들어둔 트리와 비교하고, DOM에 대입시킨다.

- 서로 다른 type의 element는 완전히 다른 트리를 생성할 것이라고 예상한다. React는 이런 경우에 둘을 비교하지 않고, 이전의 트리를 버린 뒤 완전히 새로 만든다.

- List는 key 속성을 이용해서 비교한다. key값은 안정적이고, 예측 가능하며, 유일해야 한다.

[컴포넌트와 Props](https://ko.legacy.reactjs.org/docs/components-and-props.html)

[State와 생명주기](https://ko.legacy.reactjs.org/docs/state-and-lifecycle.html)

[React로 사고하기](https://ko.legacy.reactjs.org/docs/thinking-in-react.html)

[Reconciliation](https://ko.legacy.reactjs.org/docs/reconciliation.html)
