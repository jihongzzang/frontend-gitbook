# React-Testing-Library

[React-Testing-Library Official Site](https://testing-library.com/docs/react-testing-library/intro/)

- 앞서말한 행위 주도의 테스트를 위한 라이브러리

- 구현중심이 아님

```node
설치 및 실행

npm i -D jest
npm i -D @testing-library/react
npm i -D @testing-library/jest-dom

src/setup-test.js
여기에 필효한 라이브러리 추가
```

```js
import { render, fireEvent } from '@testing-library/react';

const { getByText, getByLabelText, getByPlaceholderText } = render(
  <YourComponent />
);
```

[여러가지 메서드](https://testing-library.com/docs/dom-testing-library/cheatsheet/)

```js
헤더가 랜더링되고 있는지를 검증하는 테스트

import React from "react";
import { render } from "@testing-library/react";
import NotFound from "./NotFound";

describe("<NotFound />", () => {
  it("renders header", () => {
    const { getByText } = render(<NotFound path="/abc" />);
    const header = getByText("Page Not Found");
    expect(header).toBeInTheDocument();
  });
});
```

```js
특정 본문이 제대로 렌더링 되고 있는지 테스트
it("renders paragraph", () => {
  const { getByText } = render(<NotFound path="/abc" />);
  const paragraph = getByText(/^해당 페이지/);
  expect(paragraph).toHaveTextContent("해당 페이지(/abc)를 찾을 수 없습니다.");
});
```

```js
이미지의 경우 alt 속성값을 활용하자
it("renders image", () => {
  const { getByAltText } = render(<NotFound path="/abc" />);
  const image = getByAltText("404");
  expect(image).toHaveAttribute(
    "src",
    "https://media.giphy.com/media/14uQ3cOFteDaU/giphy.gif"
  );
});
```

기본적인 내용이고 그 이상으로 여러가지 비즈니스로직과 결합하여 테스팅도 가능!!

단, “F/E 테스트 = only React 컴포넌트 테스트”가 되는 상황은 최대한 피하는 게 좋다. 본질에 집중하지 못하고 너무 많은 테스트 코드를 작성할 위험이 있다. 유지보수를 돕기 위해 테스트 코드를 작성하는데, 테스트 코드를 잘못 작성하면 오히려 유지보수를 저해할 수 있다.

**한마디로 리액트를 위한 테스트가 아니라 소프트웨어 개발의 테스트라고 생각하자.!!**

[RTL을 활용한 선언적이고 확장가능한 테스트](https://ui.toast.com/posts/ko_20210630)
