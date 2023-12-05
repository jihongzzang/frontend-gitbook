# CSS In JS

## CSS

- Cascading Style Sheets(CSS), 스타일 시트 언어.
- 실제로 프래그래밍 언어는 아님. 요소들에 선택적으로 스타일

## CSS Selector

- CSS In JS 든 Sass 든 기본 CSS든 무엇을 쓰더라도 Selector를 아는 것과 모르는 것은 정말 리소스를 줄이는게 가능함.

### 예시 선택자

전체 선택자 : `*`
태그 선택자 : `elementName`
클래스 선택자 : `.className`
아이디 선택자 : `#id`

복합 선택자

- 하위선택자와 자식 선택자

  - `E F` , `E > F`

- 속성 선택자

  - `E[attr]`, `E[attr="val"]`, `E[attr^="val"]`, `E[attr$="val"]` ...

- 가상 클래스 선택자

  - `E::root`, `E::nth-last-child(n)`, `E::nth-child(n)` ...

```text
복합선택자는 너무 많기때문에 사실상 꾸준한 연습만이 답이라고 생각함!!
```

- CSS 명시도

```tsx
!important > id [ 100 ] > class [ 10 ] > tag [ 1 ] > * [ 0 ]
```

[MDN](https://developer.mozilla.org/ko/docs/Web/CSS/Reference)

[W3Schools](https://www.w3schools.com/css/css_selectors.asp)

## CSS In JS란

[wiki](https://en.wikipedia.org/wiki/CSS-in-JS)

[css in js의 부정적 칼럼](https://junghan92.medium.com/%EB%B2%88%EC%97%AD-%EC%9A%B0%EB%A6%AC%EA%B0%80-css-in-js%EC%99%80-%ED%97%A4%EC%96%B4%EC%A7%80%EB%8A%94-%EC%9D%B4%EC%9C%A0-a2e726d6ace6)

- CSS-in-JS는 JavaScript를 사용하여 구성 요소의 스타일을 지정하는 스타일링 기술
- 이 JavaScript가 구문 분석되면 CSS가 생성되어(일반적으로 요소로 ) DOM 에 첨부

```text
전통적인 CSS는 전역 스코프를 가지고 있어서 충돌의 위험이 있음.
이것들을 컴포넌트 레벨에서 스타일을 적용함으로써 스코핑 문제를 해결
```

- 장점

  - `JS`를 사용하여 CSS를 구성 요소 수준 자체로 추상화.
  - `JS`와 CSS 사이의 상수와 함수를 쉽게 공유

- 단점
  - 별도의 라이브러리를 설치해야 하므로 번들 크기가 커진다.
  - 결국 자바스크립트이므로 모듈을 읽어와야한다.

```text
무언가를 도입한다는 것은 항상 장정만 존재하는 것은 아님
장점과 단점이 공존한다.

최근 CSS Utility Library 테일윈드도 각광을 받고 있다. NPM TRENDS 들어가보면 대혼돈의 시대..
모든 부분에있어서 성능이 급격하게 저하되는 것 뿐만이 아니라면 CSS in JS를 써도 무방하다고 생각한다.

결국 현재 내 능력에서 만들어 내야하는 시간 및 정확성 이런것들이 얽혀서 최적의 것을 선택해야한다.
그렇기에 여러가지를 잘 쓸줄 아는 시각이 필요함.
```

## Utility First CSS Library

```text
- HTML CSS에 직접 유틸리티 클래스를 적용하여 스타일을 정의. 매우 직관적
- 커스터마이징이 용이하며, 프로젝트의 디자인 시스템에 맞게 확장이 가능
- 스타일은 HTML과 분리되어 있어, 큰 스타일 시트가 생성되는 것을 방지
- 최종 빌드의 크기를 최소화
```

- 장점

  - 사전 정의된 클래스를 통해 빠른 개발이 가능
  - 유틸리티 클래스를 사용하여 일관된 스타일링을 유지
  - 복잡한 css구조와 스타일 시트 관리의 필요성을 감소

- 단점
  - HTML의 복잡성이 증가
  - 러닝커브
  - 매우 구체적인 스타일 옵션이 제공되기때문에 확장성이 제한될 수 있음
