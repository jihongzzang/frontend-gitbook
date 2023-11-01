# Thinking In React

## State

- 변경을 다루기 위한 요소

- 어떤 컴포넌트의 state가 바뀌면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하게 된다. (무조건은 아님)

- 아무렇게나 막 만들어도 되지만, 일관성과 효율을 위해 DRY 원칙을 따르는 SSOT를 만든다.

`React State`의 조건

1. 변경돼야 함. 변경되지 않는 건 state로 다룰 가치가 없다.

2. 부모 컴포넌트가 props를 통해 전달한다면 state가 아님.

3. 다른 state나 props를 이용해 계산 가능하다면 state가 아님.

### DRY 원칙

- 중복 배제 (Don't repeat yourself)는 소프트웨어 개발 원리
- 모든 형태의 정보 중복을 지양하는 원리
- 특히 다층 구조 시스템에서 유용
- 중복배제 원리는 한마디로 “모든 지식은 시스템 내에서 유일하고 중복이 없으며 권위있는 표상만을 가진다”는 말로 기술할 수 있다.

### SSOT(Single Source of Truth)

- 데이터베이스, 애플리케이션, 프로세스 등의 모든 데이터에 대해 하나의 출처를 사용하는 개념

### useState

- 리액트에서 제공하는 hook으로 State의 상태를 변경시키는 유틸 함수
- Strict 모드 에서 React는 일부 함수를 한 번이 아닌 두 번 호출

```ts
const [state, setState] = useState(initialState);
```

- [useState](https://react.dev/reference/react/useState)

- [useSyncExternalStore](https://react.dev/reference/react/useSyncExternalStore)

### 1급 객체(first-class object)란?

- 일급객체(First-class Object)란 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체

  - 변수에 할당(assignment)할 수 있다.
  - 다른 함수를 인자(argument)로 전달 받는다.
  - 다른 함수의 결과로서 리턴될 수 있다.

- 함수가 일급객체이기 때문에 할 수 있는 것은 무엇인가?

  - 다른 함수로부터 함수를 반환 (고차함수)
  - 콜백(callback)을 사용할 수 있다. (고차함수)

- [1급 함수](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)

### Lifting State Up

- 우리가 props로 모든 자바스크립트의 값을 내려줄 수 있다라고 했다.
- 그렇다면 상태를 바꾸는 `setState` 를 조작하는 함수를 props로 내려주면 하위 컴포넌트에서 스테이트의 상태변경이 가능함

- [Lifting State Up](https://ko.reactjs.org/docs/lifting-state-up.html)
