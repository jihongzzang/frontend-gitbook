# ESLint

[ESLint Official Site](https://eslint.org/)

- ES는 Ecma Script로서 쉽게말해 표준 Javascript

- Lint는 에러가 있는 코드에 표시를 달아놓는 것

- 자바스크립트 문법에서 에러를 표시해주는 도구

무엇을 위해?

- 스타일 통일 (여러사람들이 코드스타일이 다르기때문에 통일이 필요)

## env

- env는 eslint를 적용할 환경을 설정하는 부분으로, 쉽게 생각해서 프로젝트가 어떤 환경에서 실행되는 코드인지를 설정하는 것

## extends

- extends는 설정을 특정(specify)하는 문자열이거나, 이전 설정을 확장(extends)하는 추가적인 설정에 대한 문자열 리스트

## parser

- parser는 코드를 ESLint에 맞게 파싱하기 위해 사용하는 것

## parserOptions

- parserOption는 자바스크립트 지원 옵션을 설정하기 위한 부분

## plugins

- ESLint는 다양한 설정을 위한 외부 플러그인을 지원하는데, 사용하기 전에 패키지 다운로드가 필요

## rules

- rules는 린트로 잡아낼 에러에 대한 규칙을 설정하는 부분

```node
설치 및 실행

$ npm install -D eslint
$ npx eslint --int
```

개인적으로 airbnb의 lint 설정을 선호하기때문에 궁금하신 분들은 한번 참고하기 바란다.

[Airbnb github](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb)
