# CSS In JS

## CSS

- Cascading Style Sheets(CSS), 스타일 시트 언어.
- 실제로 프래그래밍 언어는 아님. 요소들에 선택적으로 스타일

## box-sizing 속성

- 요소의 전체 너비와 높이를 계산하는 방법을 설정
  - `content-box` : 기본 CSS 상자 크기 조정 동작을 제공 (테두리나 패딩의 너비는 최종 렌더링 너비에 추가)
  - `border-box` : 요소의 너비와 높이에 대해 지정한 값의 테두리와 패딩을 고려

## word-break 속성

- 텍스트가 콘텐츠 상자를 초과할 때마다 줄 바꿈을 표시할지 여부를 결정
  - `normal` : 기본 줄 바꿈 규칙
  - `break-all` : 오버플로우를 방지하기 위해서는 두 문자 사이에 단어 분리를 삽입(중국어/일본어/한국어 텍스트 제외).
  - `keep-all` : 중국어/일본어/한국어 텍스트에는 단어 나누기를 사용하면 안 된다.
  - `break-word` : 속성의 실제 가치에 관계없이 `overflow-wrap: anywhere`와 결합된 것과 동일한 효과

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

[Grid](https://studiomeal.com/archives/533)

[Flex](https://studiomeal.com/archives/197)

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

[MUI V5 styling solution](https://github.com/mui/material-ui/issues/22342)

- 위의 링크는 MUI 팀이 왜 emotion을 선택했는지 무엇들을 고려했는지에 대한 글

## CSSOM(CSS Object Model)

- CSS를 조작하고 적용하는 방법을 정의하는 일련의 API

```tsx
// 쉽게 말해서 document의 styleSheet를 직접적으로 조작
 <script>
    const style = document.querySelector("#s");
    const sheet = style.sheet;
    const rules = sheet.cssRules;
    sheet.insertRule(".insert{color: skyblue}", rules.length);
  </script>
```

### 주요 특징

- 이는 웹 페이지의 동적인 스타일 변경을 가능하게 한다.
- 이는 실제로 브라우저에 의해 렌더링된 스타일 값을 얻는 데 유용하다.
- 요소의 레이아웃에 관한 정보를 제공 예를 들어, 요소의 크기나 위치 등을 알아낼 수 있다.
- CSSOM은 DOM과 마찬가지로 브라우저의 렌더링 엔진과 밀접하게 연관되어 있다.
  따라서 CSSOM을 사용할 때는 성능에 주의를 기울여야 한다. 불필요한 레이아웃 재계산이나 리플로우를 유발할 수 있는 연산은 피해야 한다.

  ```text
  브라우저 렌더링 프로세스과 연관지어

  - 런타임에 스타일시트를 동적으로 생성
  - <style> 태그를 통해 DOM에 삽입
  - DOM과 CSSOM을 결합하여 렌더링 트리를 생성 (CSS-in-JS를 통해 추가된 스타일은 이 렌더링 트리에 영향)
  - 렌더링 트리가 완성되면, 브라우저는 각 요소의 위치와 크기를 계산하고 화면에 렌더링. CSS-in-JS에서 정의된 스타일 이때 적용
  - CSS-in-JS는 컴포넌트의 상태나 프로퍼티에 따라 스타일을 동적으로 변경
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
