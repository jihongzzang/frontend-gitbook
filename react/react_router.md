# React Router 알아보기

## Routing, Router

[cloudflare](https://www.cloudflare.com/ko-kr/learning/network-layer/what-is-a-router/)
[aws](https://aws.amazon.com/ko/what-is/routing/)

### Router (네트워크 용어)

- 라우터는 둘 이상의 패킷 전환 네트워크 또는 서브네트워크를 연결하는 장치
- 대표적으로, NAT(Network Address Translation), 방화멱, VPN(Virtual Private Network) 등의 부가기능을 함께 제공
- 즉, 네트워크와 네트워크 간의 경로(Route)를 설정하고 가장 빠른 길로 트래픽을 이끌어주는 네트워크 장비
- 프론트엔드 관점에서는 페이지들의 유연한 변화를 도와주는 라우터를 통해 라우팅을 실현

### Routing (네트워크 용어)

- 라우팅은 네트워크에서 경로를 선택하는 프로세스
- 프론트엔드 관점에서 사용자가 요청한 URL에 따라 해당 URL에 맞는 페이지를 보여주는 것

  - 웹사이트를 생각했을때 로그인하는 페이지, 홈 페이지, 상품 상세 페이지, 마이페이지 등등등

### 정적 라우팅 (네트워크 용어)

- 관리자가 네트워크에 대한 경로 정보를 직접 지정하여 라우팅 한다.

### 동적 라우팅 (네트워크 용어)

- 대규모 네트워크에 사용되었을때 라우터 간의 변경된 네트워크에 대한 정보를 자동으로 교환

### IGP(Internal Gateway Routing Protocol) (네트워크 용어)

- 동일 그룹 네트워크에서 라우팅 정보를 교환할 때 사용되는 라우팅 프로토콜
- Domain 내부 경로 설정에 관한 프로토콜이 IGP

### EGP(External Gateway Routing Protocol) (네트워크 용어)

- 다른 그룹과 라우팅 정보를 교환할때 사용되는 라우팅 프로토콜

```text
- 실무에서 느낀 라우터를 잘 써야 하는 이유는 매 페이지마다 특정 컨디션들을 만족하지 않았을때의 컨디션들이 존재
- 매 페이지마다 뿐만 아니라 웹 전체에서 특정 컨디션을 만족하지 않았을때의 컨디션역시 마찬가지로 존재

라우터를 잘써서 구현된 웹 어플리케이션은 획기적으로 매 페이지마다 들어가야하는 보일러플레이트를 획기적으로 줄여줄 뿐만 아니라
유지보수에 있어서 절대적으로 리소스를 감축

ex)페이지마다 들어가야하는 레이아웃, 인증, search 파라미터, 패스파라미터 등등
```

## Location

[MDN](https://developer.mozilla.org/ko/docs/Web/API/Location)

- 객체가 연결된 장소(URL)를 표현
- `Location Interface`에 변경을 가하면 연결된 객체에도 영향을 끼침 (URL을 변경하면 웹 어플리케이션이 가지고 있는 객체에도 영향을 끼침)
- `Document`와 `Window 인터페이스`가 Location을 가지고 있다.

### Location 객체 뜯어보기

```tsx
//chrome 개발자도구에서 확인해보기

console.log(window.location);
console.log(document.location);

// {
//     "ancestorOrigins": {},
//     "href": "chrome://new-tab-page/",
//     "origin": "chrome://new-tab-page",
//     "protocol": "chrome:",
//     "host": "new-tab-page",
//     "hostname": "new-tab-page",
//     "port": "",
//     "pathname": "/",
//     "search": "",
//     "hash": ""
// }

//  {
//   ancestorOrigins: {},
//   href: 'chrome://new-tab-page/',
//   origin: 'chrome://new-tab-page',
//   protocol: 'chrome:',
//   host: 'new-tab-page',
//   hostname: 'new-tab-page',
//   port: '',
//   pathname: '/',
//   search: '',
//   hash: '',
// };

// 둘은 정확히 일치하다고 나옴
console.log(window.location === document.location); //true
```

- 객체내의 키의 의미

  - href: 온전한 URL을 값으로 하는 `DOMString`, 바뀔 경우 연결된 문서도 새로운 페이지로 이동
  - origin: 지정한 장소 오리진의 표준 형태를 값으로 하는 `DOMString`
  - protocol: URL의 프로토콜 부분을 값으로 하는 `DOMString`으로, 마지막의 ':'도 포함
  - host: URL의 호스트 부분을 값으로 하는 `DOMString`으로, 호스트명, ':', 포트 번호를 포함
  - hostname: URL의 도메인 부분을 값으로 하는 `DOMString`
  - port: URL의 포트 번호를 값으로 하는 `DOMString`
  - pathname: '/' 문자 뒤 URL의 경로를 값으로 하는 `DOMString`
  - search: '?' 문자 뒤 URL의 쿼리스트링을 값으로 하는 `DOMString`
  - hash: '#' 문자 뒤 URL의 프래그먼트 식별자를 값으로 하는 `DOMString`

- Location 객체 메서드 살펴보기

  - assign(): 주어진 URL의 리소스를 불러옴
  - reload(): 현재 URL의 리소스를 다시 불러옴. 매개변수로 true를 지정할 경우 캐시를 무시하고 서버에서 새롭게 불러옴
  - replace(): 현재 리소스를 제공된 URL의 리소스로 대체, 세션 히스토리에 저장되지 않기때문에 뒤로가기 불가능
  - toString(): 전체 URL이 포함하는 `DOMString`을 반환함.

## PATHNAME

[MDN](https://developer.mozilla.org/en-US/docs/Web/API/URL/pathname)

- URL구조에서의 위치 (쉽게 생각해서 나 지금 어느 페이지에 있니?)
- Web Workers 에서 사용할 수 있다.

```tsx
const url = new URL('https://developer.mozilla.org');
console.log(url.pathname); // "/"

const url = new URL(
  'https://developer.mozilla.org/en-US/docs/Web/API/URL/pathname?q=value'
);
console.log(url.pathname); // "/en-US/docs/Web/API/URL/pathname"
```

## React Router

[React Router Official Site](https://reactrouter.com/en/main)

- 앞서서 말한 React에서 Routing을 효율적으로 할 수 있도록 도와주는 Router (라이브러리)

```node
설치하기

$npm install react-router-dom @types/react-router-dom
```

### RouterProvider

- 모든 데이터 라우터 개체는 이 구성 요소에 전달되어 앱을 렌더링하고 나머지 데이터 API를 활성화
- React 트리 외부에 라우터를 만들어야 함.!! (중요)

RouterProvider를 활용하여 페이지를 설정하기

```tsx
//index.tsx in rootDir (react 18 버젼 기준)

import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import routes from `${routes가 존재하는 경로}`;

const router = createBrowserRouter(routes);

const rootElement = document.getElementById('root');

if (!rootElement) throw new Error('Failed to find the root element');

const root = ReactDOM.createRoot(rootElement);

root.render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
);

// routes.tsx
import Layout from './Layout';

import Home from '../pages/Home';

import About from '../pages/About';

const routes = [
  {
    element: <Layout />, //각페이지들에 공통적으로 적용하는 레이아웃
    children: [
      { path: '/', element: <Home /> },
      { path: '/about', element: <About /> },
    ],
  },
];

export default routes;
```

- fallbackElementProp : 앱을 로더하는 동안 앱이 작동중이라는 표시를 제공해줌. (즉 Spinner 등을 활용할 수 있음)

```tsx
<RouterProvider
  router={router}
  fallbackElement={<로딩이라는 상태를 보여주는 컴포넌트 />}
/>
```

- futerProp : 나중에 v7으로 쉽게 마이그레이션할 수 있도록 쉽게 도와줌

```tsx
<RouterProvider router={router} future={{ v7_startTransition: true }} />
```

### Browser Router

- 브라우저의 주소창에 현재 위치를 저장하고 브라우저에 내장된 히스토리 스택을 사용하여 탐색

```tsx
import React from 'react';

import ReactDOM from 'react-dom/client';

import App from './App';

import { BrowserRouter } from 'react-router-dom';

const rootElement = document.getElementById('root');

if (!rootElement) throw new Error('Failed to find the root element');

const root = ReactDOM.createRoot(rootElement);

root.render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

- basenameProp : URL의 특정 기본 이름 아래에서 실행되도록 애플리케이션을 구성합

```tsx
<BrowserRouter basename=${basePathname}>
  <App />
</BrowserRouter>
```

- futerProp : 나중에 v7으로 쉽게 마이그레이션할 수 있도록 쉽게 도와줌

```tsx
<BrowserRouter future={{ v7_startTransition: true }}>
  <App />
</BrowserRouter>
```

### Route

### Memory Router
