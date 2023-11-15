# Mocking With MSW

## Mock, Mocking 이란?

- Mocking이란 실제 객체들의 행동을 시뮬레이션하는 객체들을 만드는 것

```text
Mock-Object

프로그램을 테스트 할 경우 테스트를 수행할 모듈과 연결되는 외부의 다른 서비스나 모듈들을 실제 사용하는 모듈을 사용하지 않고
실제의 모듈을 "흉내"내는 "가짜" 모듈을 작성하여 테스트의 효용성을 높이는데 사용하는 객체

사실 너무나 많이 흔하게 사용하고 있지만 너무 흔해서 쓰고있다라는 생각조차도 못할지도 모른다고 생각이되어짐
실무를 하다보면 데이터가 만들어지지 않아서 이러한 데이터일때는 어떻게 처리되어야 하고해서 만든 가짜 데이터들을 생각해보자
```

[위키](https://en.wikipedia.org/wiki/Mock_object)

## Test Fixture

- `Fixture`는 '고정되어 있는 물체'
- 실행 에 필요한 시스템 상태 및 입력 데이터를 설정하는 데 사용
- 테스트 픽스처의 목적은 결과를 반복가능할 수 있도록 알 수 있고, 고정된 환경에서 테스트할 수 있음을 보장하기 위함

```text
장점
- 각 테스트가 항상 동일한 설정으로 시작되므로 테스트를 반복
- 테스트 픽스처는 이전 테스트 실행에서 남은 내용을 사용하는 대신 알려진 초기 상태로 테스트를 사전 구성

단점
- 인라인 설정을 사용할 경우 테스트 픽스처가 중복될 수 있다
```

```ts
// jest의 이러한 메소드들 역시 테스트 픽스쳐
beforeAll(() => {
  server.listen();
});

afterEach(() => {
  server.resetHandlers();
});

afterAll(() => {
  server.close();
});
```

[위키](https://en.wikipedia.org/wiki/Test_fixture#:~:text=A%20test%20fixture%20is%20a,electronics%2C%20software%20and%20physical%20devices.)
[관련 문서](https://www.codium.ai/glossary/test-fixture/)

### Service worker

- 웹 애플리케이션, 브라우저 및 네트워크(사용 가능한 경우) 사이에 있는 프록시 서버 역할을 함.
- 오프라인에서도 서비스를 사용할 수 있도록 해줌

- 서비스워커는 요청하지 않는 이상, 없는 것이나 다름없다. Web Worker에서와 같은 .ternimate() 명령은 존재하지 않음!!
- 웹 페이지 life cycle을 따르지 않는다. 서비스워커는 웹페이지가 닫히더라도 자동으로 비활성화되지 않는다.
- 웹 페이지와 별개로 존재하므로 DOM이나 window요소에 접근할 수 없다.

- 서비스 워커의 생명주기
  - 설치중(Installing)
    - 서비스 워커를 등록할 수 있다. 이때 자바스크립트 파일이 다운로드되고 파싱되고 나면 서비스 워커는 설치중 상태가됨.
  - 설치됨/대기중(Installed/waiting)
    - 서비스 워커가 성공적으로 설치되면 설치됨 상태가 된다.
    - 만약 현재 활성화된 서비스 워커가 없다면 즉시 활성화 중 상태
    - 다른 서비스 워커가 앱을 제어하고 있다면 대기중 상태
  - 활성화 중(Activating)
    - 서비스 워커가 활성화되기 직전의 상태.
  - 활성화됨(Activated)
    - 서비스 워커는 앱을 제어할 수 있고 fetch 이벤트와 같은 동작 이벤트를 받아서 처리
  - 중복(Redundant)
    - 서비스 워커는 앱에 아무런 영향을 미칠 수 없다.

```html
/** 간단한 서비스워커와 FETCH EVENT 가로채기*/
<script>
  self.addEventListener('fetch', function (event) {
    const { method, headers, url } = event.request;
    console.log('[SW] Fetch Method : ', method);
    console.log('[SW] Fetch Headers : ', headers);
    console.log('[SW] Fetch URL : ', url);
  });
</script>

<script>
  const registerServiceWorker = async () => {
    if ('serviceWorker' in navigator) {
      try {
        const registration = await navigator.serviceWorker.register(
          './service-worker.js'
        );
        if (registration.installing) {
          console.log('설치중');
        } else if (registration.waiting) {
          console.log('설치됨');
        } else if (registration.active) {
          console.log('활성화');
        }
      } catch (error) {
        console.error(`등록이 실패됨: ${error}`);
      }
    }
  };

  registerServiceWorker();
  fetch(`${API}`)
    .then((response) => response.json())
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
</script>
```

- 서비스 워커의 활용방안
  - 캐시와 상호작용
  - 푸쉬 알림
  - 백그라운드 동기화
  - API 모킹 등등 여러가지를 할 수 있음

[MDN](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)
[위키](https://en.wikipedia.org/wiki/Progressive_web_app#Service_workers)
[Chrome](https://developer.chrome.com/docs/workbox/service-worker-overview/?hl=ko)

### MSW(Mock Service Worker)

[MSW Official Site](https://mswjs.io/)

- 코드 레벨이 아니라 네트워크 레벨에서 가짜로 구현.
- 오프라인 작업 등을 지원하기 위한 서비스 워커의 기능(특히 API 모킹!!)을 유용히 활용한 것.

```node
설치하자 개발환경에서 사용할것이기때문에 -D 잊지말자

$npm i -D msw

In jest.config.js

setupFilesAfterEnv: [...,'<rootDir>/src/setupTests.ts'] 해당 설정

하지만 1.x => 2.x 로 Migration 되면서 추가 설정해야 할 것들이 존재.

root directory에 jest.polyfills.js 파일 생성

나의 node 버전 (18)에서는 추가로 설치해야함

$npm i undici
```

```js
//jest.polyfills.js;
const { TextDecoder, TextEncoder } = require('node:util');

Object.defineProperties(globalThis, {
  TextDecoder: { value: TextDecoder },
  TextEncoder: { value: TextEncoder },
});

const { Blob, File } = require('node:buffer');
const { fetch, Headers, FormData, Request, Response } = require('undici');

Object.defineProperties(globalThis, {
  fetch: { value: fetch, writable: true },
  Blob: { value: Blob },
  File: { value: File },
  Headers: { value: Headers },
  FormData: { value: FormData },
  Request: { value: Request },
  Response: { value: Response },
});

// jest.config.js
module.exports = {
  testEnvironmentOptions: {
    customExportConditions: [''],
  },
  ...rest,
};
```

```ts
//src/mocks/handlers.ts
// 1.x => 2.x 로 옮겨지면서 기존 req,res,ctx 매개변수는 사라짐, 또한 rest대신 http메소드를 사용해아함.
import { HttpResponse, http } from 'msw';

const BASE_URL = '당신의 baseURL';

const handlers = [
  http.get(`${BASE_URL}/${path}`, () => {
    // 여기에 원하는 데이터를 작성해주세요.
    return HttpResponse.json({ /**원하는 결과값을 넣어주세요*/ }, { status: /**상태코드도 넣을 수 있어요. (선택)*/ });
  }),
];

export default handlers;

//server.ts

import { setupServer } from 'msw/node';

import handlers from './handlers';

const server = setupServer(...handlers);

export default server;

//setupTests.ts => textFixture의 개념이 쓰인다!!

import 'whatwg-fetch';
// fetch를 구현하기하기위한 polyfill
// 하지만 최신 Node라면 적용안해도 있음.

import server from './mocks/server';

beforeAll(() => {
  server.listen();
});

afterEach(() => {
  server.resetHandlers();
});

afterAll(() => {
  server.close();
});
```

```ts
//App.test.tsx

// 간단하게 뜨는지 확인 해보기 성공!!

import { render, waitFor, screen } from '@testing-library/react';

import App from './App';

test('App ', async () => {
  render(<App />);

  await waitFor(() => {
    screen.getByText('기본카레(9,000원)');
  });
});
```

- 테스트는 테스트일 뿐 이걸 정말 실제랑 똑같이 모든걸 구현해야겠다보다 효율적으로 필요한 상황에서
  적절히 활용하자. 정말 깊게 들어가버리면 사실상 서버개발이랑 다를게 없어짐.

### polyfill(폴리필)

[polyfill](https://developer.mozilla.org/en-US/docs/Glossary/Polyfill)

- 자 글을 읽다보면 polyfill.js 작성하고, fetch를 구현하기 위한 polyfill 어쩌고 저쩌고도 쓰여있을텐데.
- 회사에서 개발과정에서 safari환경에서 `window.requestidlecallback` 메소드가 지원이 되지 않아서 polyfill을 해야 해었다.
- IE에서 사이트를 접속해야해서 polyfill을 활용했었다.

- 그래서 폴리필이란?
  - 구형 브라우저에서 동작되지 않는 최신 기능을 동작할 수 있도록 도와주는 기술이다.
