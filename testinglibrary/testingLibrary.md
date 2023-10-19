# Testing Library, JEST

## 유저 중심의 테스트?

- Testing Library는 웹 페이지가 사용자와 상호 작용하는 방식과 매우 유사한 테스트 기반으로 작동

```text
ex 투두앱

사용자 입장에서
1. 할 일을 작성하고 추가 버튼을 클릭하면 할 일 목록에 추가된다.
2. 할 일을 클릭하면 체크가 된다.
3. x 버튼을 클릭하면 할 일 목록에서 사라진다.

컴포넌트 입장에서
1. 할 일을 작성하고 추가 버튼을 클릭하면 추가 함수를 실행시킨다.
2. 할 일을 클릭하면 할 일을 찾아 상태값을 변경시킨다.
3. 할 일 삭제 버튼을 클릭하면 삭제 함수를.
```

- 이런식으로 사용자입장과 코드로 구현해야하는 입장을 최대한 sync를 맞추어야하는데 내가 짠 코드가 실제로 제대로 동작을 안하게 된다면? 버그가 생길 것이고 우리는 다시 유지보수를 위해 여러가지 얽혀있는 코드들을 뜯어 고쳐야한다.

- 이런것들을 방지하기위해 작은 단위로 테스트를 진행 해야한다.

## JEST

[JEST공식 사이트](https://jestjs.io/)

- Jest는 페이스북에서 만들어서 React와 더불어 많은 자바스크립트 개발자들로 부터 좋은 반응을 얻고 있는 테스팅 라이브러리

- JEST 이전에는 여러가지 라이브러리 설치를 통해 테스트를 해야 했지만 (Runner, Matcher, Mock) 등장 이후 굉장히 편리해짐 즉 all-in-one

```node
설치 및 실행

$ mkdir my-jest
$ npm init -y

$ npm i -D jest


In package.json

"scripts": {
  "test": "jest" npm test 입력시 jest command 실행
}

npm i -g jest-cli 단일 파일 테스트를 위한 설치

jest 파일명.test.js

```

```js
let temp;
describe("test", () => {
  beforeEach(() => {
    temp = 1;
  });

  afterEach(() => {
    temp = 0;
  });

  test("1 is 1", () => {
    expect(1).toBe(1);
  });

  test("[1,2,3] is [1,2,3]", () => {
    expect([1, 2, 3]).toEqual(1);
  });
});
```

- `describe`

  - 테스트 단위를 묶는 가장 큰 단위, 테스트시 `describe` 에 설명된 내용으로 테스트 단위를 크게 분류한다.

- `test`, `it`

  - `test()`, `it()`을 통해 기본 테스트를 진행한다. 두 방법의 기능적 차이는 없다. 하지만 it을 선호하자 (D-C-I 패턴)

- `expect`

  - `expect()` 안에 테스트 할 변수나 값을 넣어준다. 이후 `toBe`나 `toEqual`을 이용해 예측값과 비교한다.

- `toBe`, `toEqual`

  - 결과 예측으로 가장 많이 쓰이는 문법으로 `toBe`는 단순비교, `toEqual`은 배열이나 객체 내부 깊은 비교를 수행한다.

- `beforeEach`, `afterEach`

  - `beforeEach`는 테스트가 실행횔 때마다 실행해주는 전처리기이다. test를 진행하기 전 최초 1회 실행된다.

  - `afterEach`는 테스트가 실행횔 때마다 실행해주는 후처리기이다. test를 모두 끝내고 마지막에 1회 실행된다.

```js
example;

let temp;
describe("simple test", () => {
  beforeEach(() => {
    temp = 1;
  });

  afterEach(() => {
    temp = 0;
  });

  test("tmep is 1", () => {
    expect(temp).toBe(1); // true
  });

  test("temp is 1", () => {
    expect(temp).toBe(1); // true
  });
});
```
