# StyledComponents Deep

## Props

- 컴포넌트에 적용되는 속성
- 동적인 스타일링에 많이 활용 (재사용을 가능하게 해줌.)
- 테마나 스타일 변형을 관리하기 위해 props를 사용할 수 있다.

```tsx
// Input에 컬러 적용해보기

const Input = styled.input<{ $inputColor?: string }>`
  padding: 0.5em;
  margin: 0.5em;
  color: ${(props) => props.$inputColor || '#BF4F74'};
  background: papayawhip;
  border: none;
  border-radius: 3px;
`;
```

## Attrs

- attrs는 styled-components에서 고정 속성을 컴포넌트에 추가할 때 사용
- 일부 Element를 렌더링된 구성 요소나 요소에 전달하는 불필요한 Wrraper를 피하기위해 .attrs생성자를 사용
- 말 그대로 attributes를 삽입하기 위한 메서드이며 1개의 객체타입의 arg를 받는다
- 속성의 값은 동적으로 바꿀수있음
- 오버라이딩을 지원해줌

```tsx
const Input = styled.input.attrs<{ $size?: string }>((props) => ({
  type: 'text',
  $size: props.$size || '1em',
}))`
  color: #bf4f74;
  font-size: 1em;
  border: 2px solid #bf4f74;
  border-radius: 3px;

  margin: ${(props) => props.$size};
  padding: ${(props) => props.$size};
`;

render(
  <div>
    <Input placeholder='A small text input' />
    <br />
    <Input placeholder='A bigger text input' $size='2em' />
  </div>
);
```

- attrs는 언재 사용할까?
  - 스타일이 지정된 구성 요소의 모든 인스턴스에 해당 속성을 갖고 싶을 때 사용
  - 모든 인스턴스에 다른 속성이 필요할 때 속성을 직접 전달

## Reset CSS

- 브라우저의 기본요소의 디자인을 모두 없애자는 것 (브라우저 마다 다르다... 크로스 브라우징과도 연관됨)

[Chrome](https://chromium.googlesource.com/chromium/src/third_party/+/master/blink/renderer/core/html/resources/html.css)

[Webkit](https://trac.webkit.org/browser/trunk/Source/WebCore/css/html.css)

[에릭마이어의 CSS Reset](https://meyerweb.com/eric/tools/css/reset/)

[StyledComponent의 Reset](https://www.npmjs.com/package/styled-reset)

## CreateGlobalStyle

- 전역 스타일을 처리하는 특수 항목을 생성하는 도우미 함수

- 생각외로 여기서 처리 할것들이 은근 많아짐.

- 전체 관련된

```tsx
import { createGlobalStyle } from 'styled-components';

import reset from 'styled-reset';

const GlobalStyle = createGlobalStyle`
 ${reset}

 :root {
     --${전역으로 쓸 것} : ...
 // 이렇게 루트해서 전역으로 쓸 스타일 변수도 지정이 가능함.
 }

 //전역으로 쓸걸 지정해주자. 개인적으로 styled-reset 역시 나는 여기에다 가져다 같이 쓰는것을 좋아한다.
`;

export default GlobalStyle;

const App = () => {
  return (
    <>
      <GlobalStyle />
      <YourComponent />
    </>
  );
};
```

## Theme

- 애플리케이션 전체에 걸쳐 일관된 스타일링을 적용하기 위한 일련의 스타일 규칙을 정의하는 방법

```text
사실상 매번 필요한 컬러를 그때그때마다 해당 컴포넌트에서 지정해주면 만약 공통적으로 바뀌어야한다면?
모두 찾아야 하고 누락된 곳은 없는지 확인해야하고...

물론 기획이 수정될수도 있고 여러 상황에서 바뀌는 상황이 많을텐데
보통은 key값을 의미론적으로 둘것인지(buttonColor: ##...) 표현하는 값(yellow: ##...)으로 둘것인지에 대한 부분은
정답은 없다고 생각한다.

현재 회사프로젝트의 경우 일관된 UI 컴포넌트 디자인패키지도 없고 의미있는 부분에대한 영역에 대한 정의도 거의 존재하지 않아서
color는 표현하는 값으로, typo는 의미론적으로 두고 컴포넌트내부에서 variant를 활용해서 사용한다.
```

## ThemeProvider

어떻게 보면 테마라는 하나의 거대한 상태를 전역으로 관리하게 해줌.

StyledComponent와 그외의 상태관리는 이렇게 모듈을 뜯어보면 생각보다 우리에게 굉장히 친숙한 훅들이 많이 보인다.

생각해보면 보통 APP Route 부분에서 ThemeProvider를 감싸서 그냥 필요한곳에서 조회하는데

=> 리액트에서 제공하는게 뭐지? propsDrilling이 아니라면? => ContextAPI

=> 더이상 마법이 아니다.

```tsx
//내부 구현모듈
function ThemeProvider(props) {
  var outerTheme = useContext(ThemeContext);
  var themeContext = useMemo(
    function () {
      return mergeTheme(props.theme, outerTheme);
    },
    [props.theme, outerTheme]
  );

  if (!props.children) {
    return null;
  }

  return /*#__PURE__*/ React.createElement(
    ThemeContext.Provider,
    {
      value: themeContext,
    },
    props.children
  );
}

- 그냥 이렇게 감싸주면 됩니다.

const App = () => (
  <ThemeProvider theme={theme}>
    <YourComponent />
  </ThemeProvider>
);

const StyledButton = styled.button`
  background-color: ${props => props.theme.primaryColor};
  color: white;
`;
```

물론 props는 구조분해 할당이 가능하다.

```tsx
const StyledButton = sㅅtyled.button`
  background-color: ${({ theme }) => theme.primaryColor};
  color: white;
`;
```

```text
이번에 공부하면서 알았던 것들도 존재했었고 몰랐던것들도 존재했었다.
실제로 비슷하게 구현해보면서 내부 모듈도 스윽 보는 시간도 가져봤었다.

css는 어느정도는 창의력적인 부분이 필요하다고 생각이 된다. 하지만 그 창의력을 위해서는 많은 경험이 필요하고
또 해당 라이브러리에서 제공하는 문법들에 대해 익숙해져야한다. (근본적으로 CSS 속성들을 잘 알고 있어야함..)

반복만이 살길!!
```
