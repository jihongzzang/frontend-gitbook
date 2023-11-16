# 간단하게 E2E 테스트 해보기

## E2E(End to End) Test란?

[위키](https://en.wikipedia.org/wiki/Software_testing)
[카카오 디벨로퍼스](https://fe-developers.kakaoent.com/2023/230209-e2e/)

- E2E Test는 종단(Endpoint) 간 테스트로 사용자의 입장에서 테스트

- 보통 Web, App 등에서 GUI를 통해서 시나리오, 기능 테스트 등을 수행

### 프론트엔드에서 E2E

- 원하는 Text가 제대로 나오는지

- A를 클릭했을 때 기대하는 동작을 하는지 등을 테스트

### 왜? 해야해?

- IT 제품을 만드는 과정에서 만든다음 바로 완성이게 아니라
- 만들어진 제품을 QA를 통해 검수를 하고 버그를 수정하고 출시를 하는게 보통이다.
- 하지만 이 QA가 수천개, 수만개의 케이스가 존재한다면? 일일히 손으로 테스트 해야하는가? 비용적으로 엄청나게 들텐데?
- 물론 E2E 자동화를 통해 모든 케이스를 다 커버한다는건 아니지만 QA전 미리 시나리오를 작성하고 테스트를 해보면 많은 부분에서 리소스가 절감된다. => 이는 곧 더빠른 제품의 출시랑 연관된다고 생각

### Headless Chrome (테스트 도구)

[Headless Chrome](https://developer.chrome.com/blog/headless-chrome/)

- 헤드리스 브라우저는 눈에 보이는 UI 셸이 필요하지 않은 자동화된 테스트 및 서버 환경을 위한 도구
- 즉 UI가 없는 크롬

## Puppeteer (테스트 도구)

[Puppeteer Official Site](https://pptr.dev/)

- 구글에서 만든 노드 라이브러리로 HeadlessChorme 또는 Chrominum을 제어할 수 있다.
- 테스트 결과물을 스크린샷 하거나 PDF파일로 생성가능
- UI 테스트 가능
- 크롬에서 자동화 환경 설정이 가능하다고 한다.
- SPA 크롤링 및 Pre Render Contents 생성 가능

## CodeceptJS (테스트 도구)

[CodeceptJS Official Site](https://codecept.io/)

- 인터렉티브 그래픽 테스트 실행기라고 적혀있다. 역시나 브라우저에서 작동
- 그룹 또는 싱글로 생성이 가능
- 테스트 검토, 수정, 새로운 테스트, 헤드리스 전환 등등 여러가지 기능등을 제공한다.
- 개발자가 아니더라도 다양한 사람들이 코드를 봐도 이해하는 메소드 변수명이 특징

```node
- 웹 브라우저를 화면에 띄워 테스트를 실행
npm run codeceptjs

- 웹 브라우저를 화면에 띄우지 않고 테스트를 실행
npm run codeceptjs:headless

- 웹 브라우저에 CodeceptUI를 띄워 테스트를 실행
npm run codeceptjs:ui
```

```ts
//codecept.conf.js in rootDir

const { setHeadlessWhen, setCommonPlugins } = require('@codeceptjs/configure');
// turn on headless mode when running with HEADLESS=true environment variable
// export HEADLESS=true && npx codeceptjs run
setHeadlessWhen(process.env.HEADLESS);

// enable all common plugins https://github.com/codeceptjs/configure#setcommonplugins
setCommonPlugins();

/** @type {CodeceptJS.MainConfig} */
exports.config = {
  tests: './tests/**/*_test.ts',
  output: './output',
  helpers: {
    Playwright: {
      url: 'http://localhost:8080',
      show: true,
      browser: 'chromium',
    },
  },
  include: {
    I: './tests/steps_file.ts',
  },
  name: 'frontend-survival-week03',
};

//steps.d.ts in ./tests

/// <reference types='codeceptjs' />
type steps_file = typeof import('./steps_file.js');

declare namespace CodeceptJS {
  interface SupportObject {
    I: I;
    current: any;
  }
  interface Methods extends Playwright {}
  interface I extends ReturnType<steps_file> {}
  namespace Translation {
    interface Actions {}
  }
}

// steps_file.ts in ./tests

// in this file you can append custom step methods to 'I' object

module.exports = function () {
  return actor({
    // Define custom steps here, use 'this' to access default methods of I.
    // It is recommended to place a general 'login' function here.
  });
};

//test 코드
Feature(`${테스트코드의 제목}`);

Scenario("메뉴판 필터링", ({ I }) => {
  I.amOnPage('/'); // 내가 현재 페이지에 있을때
  I.see('푸드코트 키오스크'); // 내가 키오스크에서 메뉴검색을할떄


  I.click('중식');
  I.see('짜장면');
  I.dontSee('김밥');

  I.click('한식');
  I.see('메리김밥');
  I.see('김밥');
  I.see('제육김밥');

  I.fillField('검색', '혹등');
});
```

## Playwright (테스트 도구)

[Playwright Official Site](https://playwright.dev/)

- 하나의 API로 모든 최신 브라우저(크로미움, 파이어폭스, 웹킷)에서 빠르고, 안정적인 자동화를 지원하는 MS에서 만든 자동화 도구

- 여러 페이지, 도메인, iframe에 걸친 시나리오

- 클릭 같은 액션을 실행하기 전에 엘리먼트가 준비될 때까지 자동으로 기다림

- 네트워크 요청을 모킹하기 위한 네트워크 활동 가로채기

- 마우스, 키보드의 기본 입력 이벤트

```text
주요 개념

- 브라우저(크로미움, 파이어폭스, 웹킷)의 인스턴스를 나타낸다.
    - Playwright는 브라우저 인스턴스를 시작하고 브라우저 인스턴스를 닫는 것으로 끝난다.

- 컨텍스트(Browser context)
    - 브라우저 컨텍스트는 브라우저 인스턴스 내에서 분리된 유사 세션이다. 만드는데 빠르고 비용이 적다.
    - 각 테스트를 새로운 브라우저 컨텍스트에서 실행하여 브라우저 상태를 테스트 간에 분리하는 것이 좋다.
    - 컨텍스트별로 다른 기기를 시뮬레이션

- 페이지와 프레임(Pages and frames)
    - 페이지를 이용해서 각기 다른 탭에서 작업하는 것처럼 할 수 있다. 예제에서는 각기 다른 페이지를 탐색한다.
```

```ts
//in playwright.config.ts
import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
  testDir: './tests',
  retries: 0,
  use: {
    channel: 'chrome', //어떤 브라우저에서 테스트할지!!
    baseURL: 'http://localhost:8080',
    headless: !!process.env.CI,
    screenshot: 'only-on-failure',
  },
};

export default config;

//이떄 jest와 충돌이 날 수도 있는데 해당 테스트 폴더에 .eslintrc.js를 만들어주고

//.eslintrc.js in ./tests
module.exports = {
  env: {
    jest: false,
  },
  extends: [
    'plugin:codeceptjs/recommended',
    'plugin:playwright/playwright-test',
  ],
  rules: {
    'import/no-extraneous-dependencies': 'off',
  },
};
```

이제 테스트 코드를 작성해보자

```ts
// ${filename}.spec.ts
import { test, expect } from '@playwright/test';

test('키오스크에서 주문까지', async ({ page }) => {
  //순서대로 실행해주세요.!!! await는 자동대기해줌
  await page.goto('/');

  await expect(page.getByText('메가반점')).toBeVisible();

  const searchInput = page.getByLabel('input-검색');

  await searchInput.fill('데브');

  await expect(page.getByText('데브부엌')).toBeVisible();

  //button이나 인풋같은 곳에서 HTML 속성을 이용해 엘리먼트를 검색
  await page.click('button[value="일식"]');

  await page.click('button[name="#모듬초밥"]');

  await expect(page.getByText('모듬초밥(14,000원)')).toBeVisible();

  await page.click('button[class="cta-button"]');

  await expect(page.getByText('선택된 메뉴가 없습니다.')).toBeVisible();
});
```

```node
테스트 실행!!

$npx playwright test
```

- 실제로 브라우저 띄워서 테스트하면 좋긴하지만 오래걸릴 수 있으니 작은 테스트들은 headlessMode를 사용하자.

```node
$CI=true npx playwright test

다른 테스팅 도구들 처럼 failed가 안뜨면 성공
```

- 주의할점은 테스팅 라이브러리들을 테스트하기위해 여러가지들을 써본거지 여러가지들을 쓰면서 나는 테스트 명령어들이 서로 막 에러가 나는 상황들이 생겼었음...

- 또한 궁금한점은 MSW도 그렇고 playwright도 그렇고 api를 가로챈다는 것은 결국 내부 코드안에 서비스워커가 등록되어 있다는 것인가?

```ts
//playwright 내부 모듈
type ServiceWorkerPolicy = Exclude<
  BrowserContextOptions['serviceWorkers'],
  undefined
>;

//msw 내부 모듈
type StartReturnType = Promise<ServiceWorkerRegistration | undefined>;

//codecept 내부 모듈 정확하게 찾지는 못했는데 아마 여기서 serviceWork 비스무리한 동작을 하지 않을까 싶다.
class Worker extends EventEmitter {
  //...
  //postMessage()...
}
```

```text
다양한 테스트도구들이 존재합니다. 이것 외에도 cypress... 등등 하지만

중요한건 테스트 코드를 작성하는 실력!! 그외엔 브라우저 호환성, 어떤 이벤트까지 테스트코드가 가능한지 등등을 고려해서 선택하면 된다.

도구들마다 환경셋팅 및 메소드명이 다른거지 본질적으로 테스트 코드를 짜는 로직은 별다를게 없다.

그리고 테스트코드를 짜면서 무심코 HTML Tag들의 Attribute들을 잘 써야 겠다라는 생각도..
(코드가 길어지고 컴포넌트들이 많아지면 계속 text로 접근할 수 없다...)
```

- 나만의 푸드키오스크 및 깃헙

![img](/testinglibrary/kiosk.png)

[푸드키오스크 깃헙](https://github.com/jihongzzang/frontend-survival-week04)
