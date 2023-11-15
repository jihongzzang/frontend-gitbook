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

## Playwright (테스트 도구)

## CodeceptJS (테스트 도구)
