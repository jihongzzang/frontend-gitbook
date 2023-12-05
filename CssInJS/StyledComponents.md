# StyledComponents

[styledComponents](https://styled-components.com/)

[github](https://github.com/styled-components/styled-components)

- CSS In JS 라이브러리 중에 하나

대놓고 공식문서 Motivation 부분을 보면 장점이 맨위에 위치하는데

- Automatic critical CSS

  - 스타일드 컴포넌트는 페이지에서 어떤 컴포넌트가 렌더링되는지 추적하고 해당 컴포넌트의 스타일만 삽입하며 다른 것은 완전히 자동으로 삽입
    코드 분할과 함께 사용하면 사용자가 필요한 최소한의 코드를 로드할 수 있다.

- No class name bugs

  - 스타일드 컴포넌트는 스타일에 대해 고유한 클래스 이름을 생성한다. 중복 또는 철자 오류에 대해 걱정할 필요가 없다.

- Easier deletion of CSS

  - 코드베이스 어딘가에서 클래스 이름이 사용되었는지 알기 어려울 수 있지만, 스타일드 컴포넌트는 모든 스타일이 특정 컴포넌트에 연결되어 있기 때문에 명확하게 알 수 있다.
    컴포넌트가 사용되지 않아서(도구가 감지할 수 있는) 삭제되면 해당 스타일도 모두 함께 삭제된다.

- Simple dynamic styling

  - 소품이나 글로벌 테마를 기반으로 컴포넌트의 스타일을 조정하는 것은 수십 개의 클래스를 수동으로 관리할 필요 없이 간단하고 직관적이다.

- Painless maintenance
  - 컴포넌트에 영향을 미치는 스타일링을 찾기 위해 여러 파일을 뒤질 필요가 없으므로 코드베이스의 규모에 상관없이 유지 관리가 매우 간편하다.

[styledComponent magic explained](https://mxstbr.blog/2016/11/styled-components-magic-explained/)
[tagged template literals](https://dev.to/dekel/tagged-template-literals-the-magic-behind-styled-components-2f2c)

## 나만의 간다 StyledComponent 만들어보기

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
  color: #00f;
`;

export default function Greeting() {
  return <Paragraph>Hello, world!</Paragraph>;
}
```

```js
const myStyled =
  (Component) =>
  ([style]) =>
  (props) => {
    //component, style, props 를 받는 커링패턴 컴포넌트와 스타일 프롭스를 받자.

    const elementRef = useRef(null);

    useEffect(() => {
      if (elementRef) {
        elementRef.current.setAttribute('style', style);
      }
    }, []);

    return <Component {...props} ref={elementRef} />;
  };
```

=> 이렇게되면 `styled("button")...` 이렇게 까지는 만들 수 있다.

그렇다면 `styled.button은 어떻게 만들어낼 수 있을까?`

```js
const myStyledSecond = '위의 함수와 동일';

myDomElements.forEach((domElement) => {
  myStyledSecond[domElement] = myStyledSecond(domElement);
});

export default myStyledSecond;
```

=> 마지막 Props가 있던데 이부분은 어떻게 만들 수 있지?

```js
const myStyledThird =
  (Component) =>
  (strs, ...exprs) =>
  // strs (문자열 배열)와 ...exprs (표현식 배열)을 인자로 받자, 스타일 문자열과 함께, props를 기반으로 계산되는 표현식
  (props) => {
    //나머지 모두 동잃
    useEffect(() => {
      if (elementRef) {
        //reduce 함수를 사용하여, exprs 배열의 각 요소를 순회,
        //만약 expr가 함수인 경우, 현재의 props를 인자로 호출하여 값을 얻는다. 그렇지 않은 경우, expr 자체의 값을 사용!!
        const style = exprs.reduce((result, expr, index) => {
          const isFunc = typeof expr === 'function';
          const value = isFunc ? expr(props) : expr;

          return result + value + strs[index + 1];
        }, strs[0]);

        elementRef.current.setAttribute('style', style);
      }
    }, [elementRef, props]);
  };

export default myStyledSecond;
```

```js
const Home = () => {
  return (
    <>
      <Button>나만의 스타일드 컴포넌트</Button>
      <ButtonSecond>나만의 버튼 2</ButtonSecond>
      <div>
        <ButtonThird>나만의 버튼 3</ButtonThird>
        <ButtonThird color='blue'>나만의 버튼 3</ButtonThird>
      </div>
    </>
  );
};

const Button = myStyled('button')`
    //....
`;

const ButtonSecond = myStyledSecond.button` 
    //....
`;

const ButtonThird = myStyledThird.button`
  //...
  color: ${(props) => (props.color ? props.color : 'red')};
`;
```

=> 실제로는 이거보다 훨씬 더 복잡하고 위부분에서는 고유한 class(hash)를 생성해주는 부분이나 런타임에 hash에 포함된
class 에 한꺼번에 스타일을 입히는 과정이 없다.

실제로 내부 모듈과 깃 허브를 참고해본결과

이런식으로 해쉬를 생성해주는 부분도 존재했고

![img](스크린샷%202023-12-06%20오전%201.36.45.png)

아래 깃허브를 보면 `createStyledComponent` 라는 Function, `useInjectedStyle` Function 두가지가 존재를 하는데

`useInjectedStyle`의 핵심은 `resolvedAttrs()`와 `스타일시트 컨텍스트`를 기반으로 스타일을 생성하고 주입

`createStyledComponent` 의 경우

- 컴포넌트를 먼저 확인하고 옵션과 id를 설정
- ComponentStyle 인스턴스를 생성하여 props와 ref를 받는 새 컴포넌트를 정의
- 속성을 할당
- 후에 복합 컴포넌트일때 추가처리를해서
- 새로운 StyledComponent를 반환

그밖에도 구현부분이 궁금하면 아래 깃허브 주소를 남겨놨다.

[github](https://github.com/styled-components/styled-components/blob/main/packages/styled-components/src/models/StyledComponent.ts)

## Extending Styles

```tsx
const Parargraph = styled.p`...`;
const BigParargraph = styled.Paragraph`...`;
```

만들어진 스타일드 컴포넌트를 가지고 새로운 컴포넌트를 만들 수도 있다.

```tsx
function HelloWorld({ className }: React.HTMLAttributes<HTMLElement>) {
  return <p className={className}>Hello, world!</p>;
}

const Greeting = styled(HelloWorld)`
  color: #00f;
`;

export default Greeting;
```

이런식으로 기존에 컴포넌트에 스타일을 입히는 것도 가능하다., 단 Class를 잡아줘야함

## Pseudoelements, pseudoselectors, and nesting

리터럴 스타일 내부에 네스팅, 선택자 등 역시 지원

## 애니메이션

애니메이션 정의들은 일반적으로 전역적인 범위를 가지기때문에 keyframe 이라는 helper 함수를 제공해서

각 애니메이션에 대한 고유한 이름이 생성되어 충돌을 피할 수 있다.

```tsx
const rotate = keyframes`
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
`;
```

attr과 props 는 다음 장에서 살펴보자
