# DesignSystem

## 반응형 웹 디자인(Responsive web design)

[MDN](https://developer.mozilla.org/ko/docs/Learn/CSS/CSS_layout/Responsive_Design)

[css tricks](https://css-tricks.com/a-complete-guide-to-css-media-queries/)

[font size 62.5%](https://www.aleksandrhovhannisyan.com/blog/62-5-percent-font-size-trick/)

- 웹 페이지가 모든 화면 크기와 해상도에서 잘 렌더링되도록 하면서도 사용성을 보장하는 웹 디자인 접근 방식
- 반응형 웹 디자인은 기술이 아닌 하나의 접근 방식

- 반응형 사이트를 만들때 고려해볼 것들

  - Media Query
  - Flex, Grid
  - Responsive Image, Typography
  - Viewport tag

```text
구글의 SEO는 모바일 최적화를 굉장히 중요시 여긴다.
실제로 회사에서 SEO작업을 하면서 서치콘솔을 엄청나게 보는데 이걸 깊이 뜯어보면
웹 봇보다 모바일 봇으로 탐험한 것이 압도적임 그만큼 우리가 구현한 웹은 어느 디바이스 어느 화면에서든지
잘 보여져야한다.
```

## 디자인 시스템(Design System)

[Laura Kalbag의 “Design Systems”](https://24ways.org/2012/design-systems/)

- 디자인 시스템이란 `재사용 가능한 요소 및 패턴`을 도입하여 대규모 디자인을 관리하기 위한 표준 세트

```text
사실상 우리가 여러 CSS Library 배우는 이유라고 생각한다.
실제로 현업에서 일을하다보면 이 페이지의 버튼이 저기 사용되고 폰트는 여러 폰트들이 존재하고 등등
아 이거 그냥 계속 가져다쓰고싶은데 이런 생각을 가지고 라이브리러들을 만나게된다.

그렇지만 일을 하다보면 Library가 중요하지는 않다. 어떤것을 쓰던지
본인만의 재사용이 가능한 방식에 대한 근본적인 고민을 하는 것이 우선이라고 생각한다.
```

[Atomic Design 보러가기](https://s-organization-196.gitbook.io/frontend-megatera/react/react_component#atomic-design)
