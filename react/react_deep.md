# React-With-JSX

## React에서 JSX를 사용하는 목적

```js
JSX
const element = <h1>Hello, world!</h1>;

변환;
const element = React.createElement("h1", null, "Hello, world!");

JSX
<div className="test">
  <p>Hello, world!</p>
  <Button type="submit">Send</Button>
</div>;

변환;
React.createElement(
  "div",
  { className: "test" },
  React.createElement("p", null, "Hello, world!"),
  React.createElement(Button, { type: "submit" }, "Send")
);
```

- `JSX` 란 JS에 XML을 확장한 문법 (JS XML)
- `JSX`는 리액트로 프로젝트를 개발할 때 사용되므로 공식적인 자바스크립트 문법은 아니다.
- `JSX`는 브라우저에서 바로 실행되는게 아니라 JS로 변환되어져 실행됨 (결국 표현식일뿐이다.)

**왜 쓰는거지?**

```text
React는 별도의 파일에 마크업과 로직을 넣어 기술을 인위적으로 분리하는 대신, 둘 다 포함하는 “컴포넌트”라고 부르는 느슨하게 연결된 유닛으로 관심사를 분리합니다.

대부분의 사람은 JavaScript 코드 안에서 UI 관련 작업을 할 때 시각적으로 더 도움이 된다고 생각합니다.

기본적으로 React DOM은 JSX에 삽입된 모든 값을 렌더링하기 전에 이스케이프 하므로, 애플리케이션에서 명시적으로 작성되지 않은 내용은 주입되지 않습니다. 모든 항목은 렌더링 되기 전에 문자열로 변환됩니다. 이런 특성으로 인해 XSS (cross-site-scripting) 공격을 방지할 수 있습니다.

- 공식문서
```

## 관심사의 분리? (SOC?)

- 한번쯤은 모두가 들어본 말이다.

```text
컴퓨터 프로그램을 여러 섹션으로 분리하는 설계 원칙.

모듈화, 즉,컴포넌트 분리는 잘 정의된 인터페이스를 가진 코드 섹션 내부에 정보를 캡슐화함으로써 달성

- 코드의 간소화 및 유지 관리에 대한 자유도가 높아진다.
- 재사용 및 독립적인 개발 기회가 더 많아집니다.

- 관심사를 분리한다는 것은 코드 인터페이스를 추가하는 것을 의미하기때문에 실행할 코드가 많이 생성되어 실행 패널티가 있다.

객체지향 언어에서는 관심사를 객체로 분리,

MVC 또는 MVP와 같은 아키텍처 설계 패턴은 프레젠테이션과 데이터 처리(모델)를 콘텐츠에서 분리

관심사를 절차 또는 함수로 분리

HTML, CSS, JS 로 나누는것도 관심사의 분리
```

## Syntactic sugar

```text
`Syntax Sugar`는 한국어로 문법 설탕이라고 번역
```

- 프로그래밍 언어 전반적으로 적용되는 개념

- 읽는 사람 또는 작성하는 사람이 편하게 디자인 된 문법이라는 뜻을 갖고 있습니다.

- 삼항 연산자, Null 병합 연산, 변수 단축 선언, 전개 구문, Rest Parameter, Default Parameter...

- 자기도 모르게 줄여서 쓰는 코드들 대부분들이 포함

## React.createElement

`createElement()`

```js
React.createElement(type, [props], [...children]);

태그 이름 문자열('div' 또는 'span' 등), React 컴포넌트 타입, 또는 React Fragment 타입 중 하나가 올 수 있다.
```

`cloneElement()`

```js
const clonedElement = cloneElement(element, props, ...children)

cloneElement를 사용하면 다른 엘리먼트를 시작점으로 사용하여 새로운 React 엘리먼트를 만들 수 있다.

원래 엘리먼트의 props와 새 props가 얕게 병합됨

새 child가 기존 child를 대체

원래 엘리먼트의 key와 ref가 유지

const CreateTextWithProps = ({ text, ASCIIChar, ...props }) => {
 return (
   <span {...props}>
    {text}{ASCIIChar}
   </span>
)
};
​
const RepeatCharacters = ({ times, children }) => {
 return React.cloneElement(children, {
   // This will override the original ASCIIChar in the text.
   ASCIIChar: children.props.ASCIIChar.repeat(times),
})
};

```

`isValidElement()`

```js
React.isValidElement(object)

객체가 React 엘리먼트인지 확인합니다. true 또는 false를 반환
```

`React.Children`

```js
React.Children.map(children, function[(thisArg)])

React.Children는 불투명(Opaque) 자료 구조인 this.props.children를 다루는 유틸리티 함수들을 제공

```

## React Element

```text
엘리먼트는 React 앱의 가장 작은 단위입니다.

엘리먼트는 화면에 표시할 내용을 기술합니다.

브라우저 DOM 엘리먼트와 달리 React 엘리먼트는 일반 객체

React DOM은 React 엘리먼트와 일치하도록 DOM을 업데이트

불변객체이다.

 - 공식문서
```

DOM에 엘리먼트 렌더링 하기

```js
const root = ReactDOM.createRoot(
  document.getElementById('root')
);

const element = ...

root.render(element);
```

## React StrictMode

- 애플리케이션 내의 잠재적인 문제를 알아내기 위한 도구

```js
개발 중에 컴포넌트에서 흔히 발생하는 버그를 조기에 발견할 수 있다.

const root = createRoot(document.getElementById('root'));

root.render(
  <StrictMode>
    <App />
  </StrictMode>
);

부분 활성화도 가능

import { StrictMode } from 'react';

function App() {
  return (
    <>
      <Header />
      <StrictMode>
        <main>
          <Sidebar />
          <Content />
        </main>
      </StrictMode>
      <Footer />
    </>
  );
}
```

## VDOM(Virtual DOM)이란 과 재조정

사실 앞에 리액트에 관련된 부분에서 간략하게 다뤘던 내용인데 조금 더 깊게 들어가보면

- DOM이란?

```text
문서 객체 모델(The Document Object Model, 이하 DOM)은 HTML, XML 문서의 프로그래밍 interface 이다.

- DOM은 문서의 구조화된 표현을 제공하며 프로그래밍 언어가 DOM 구조에 접근할 수 있는 방법을 제공

- 기계가 문서 구조, 스타일, 내용 등을 변경할 수 있게 돕는다.

- 이들은 웹 페이지를 스크립트 또는 프로그래밍 언어들에서 사용될 수 있게 연결시켜주는 역할을 담당한다.

- DOM 은 웹 페이지의 객체 지향 표현이며, 자바스크립트와 같은 스크립팅 언어를 이용해 DOM 을 수정할 수 있다.
```

- Virtual DOM (트리)

```text
실제" DOM과 동기화되는 프로그래밍 개념

 "가상 DOM"이라는 용어는 일반적 으로 사용자 인터페이스를 나타내는 객체
```

- Shadow DOM은 Virtual DOM과 동일?

```text
- Shadow DOM은 주로 웹 구성 요소의 범위 지정 변수 및 CSS를 위해 설계된 브라우저 기술

- 가상 DOM은 브라우저 API 위에 JavaScript 라이브러리로 구현되는 개념
```

```text
Myth: React is “faster than DOM”. Reality: it helps create maintainable
applications, and is *fast enough* for most use cases.

가상 돔은 유지보수가 가능한 애플리케이션을 만드는 데 도움이 되며, 대부분의 사용 사례에서 충분히 빠릅니다.

- Dan의 트윗
```

## Reconciliation(재조정) 과정은 무엇인가?

```text
재조정은 React에서 어떤 부분들이 변해야 하는지 두 개의 트리를 비교하는 데 사용되는 개념이다.

- 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.

- 개발자가 key prop을 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해 줄 수 있다.

- 재렌더링은 모든 컴포넌트의 render를 호출하는 것이지 React가 언마운트시키고 다시 마운트하는 것은 아니다.
```

[React Dev](https://react.dev/)

[React](https://ko.legacy.reactjs.org/)

[SOC](https://en.wikipedia.org/wiki/Separation_of_concerns)

[DOM](https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/Introduction)
