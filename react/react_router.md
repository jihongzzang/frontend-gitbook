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

  - href
    - 온전한 URL을 값으로 하는 `DOMString`, 바뀔 경우 연결된 문서도 새로운 페이지로 이동
  - origin
    - 지정한 장소 오리진의 표준 형태를 값으로 하는 `DOMString`
  - protocol
    - URL의 프로토콜 부분을 값으로 하는 `DOMString`으로, 마지막의 ':'도 포함
  - host
    - URL의 호스트 부분을 값으로 하는 `DOMString`으로, 호스트명, ':', 포트 번호를 포함
  - hostname
    - URL의 도메인 부분을 값으로 하는 `DOMString`
  - port
    - URL의 포트 번호를 값으로 하는 `DOMString`
  - pathname
    - '/' 문자 뒤 URL의 경로를 값으로 하는 `DOMString`
  - search
    - '?' 문자 뒤 URL의 쿼리스트링을 값으로 하는 `DOMString`
  - hash
    - '#' 문자 뒤 URL의 프래그먼트 식별자를 값으로 하는 `DOMString`

- Location 객체 메서드 살펴보기

  - assign()
    - 주어진 URL의 리소스를 불러옴
  - reload()
    - 현재 URL의 리소스를 다시 불러옴. 매개변수로 true를 지정할 경우 캐시를 무시하고 서버에서 새롭게 불러옴
  - replace()
    - 현재 리소스를 제공된 URL의 리소스로 대체, 세션 히스토리에 저장되지 않기때문에 뒤로가기 불가능
  - toString()
    - 전체 URL이 포함하는 `DOMString`을 반환함.

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
- `React Router`는 `client side routing(csr)`을 가능하게 해줌

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

// Layout.tsx
import { Outlet } from 'react-router-dom';

import Header from './Header';

import Footer from './Footer';

// <Outlet/> 이게 우리가 실직적으로 중요하게 렌더링할 페이지 컴포넌트

export default function Layout() {
  return (
    <div>
      <Header />
      <main>
        <Outlet />
      </main>
      <Footer />
    </div>
  );
}


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

- URL 세그먼트를 구성 요소, 데이터 로딩 및 데이터 변형에 연결
- 경로 중첩을 통해 복잡한 애플리케이션 레이아웃과 데이터 종속성이 단순해지고 선언적

```tsx
<Route
  path={/** 경로 설정 */}
  loader={/** 사전에 불러올 작업이 존재할떄 */}
  action={/** http method에서 get이 아닌 다른 무언가를 했을때 호출 */}
  element={/** 렌더링할 컴포넌트 */}
/>

// action의 경우 로직이 정말 간단할때만 사용하기를 추천 왜냐면 수많은 Route가 존재하기 때문에 사실상 Route를 관리하는 곳에서 로직을 작성하면
// 코드의 가독성이 많이 떨어진다.
```

BroswerRouter와 Routes, Route를 활용하여 페이지를 설정하기

```tsx
//index.tsx는 BroswerRouter 부분 참고

//App.tsx

function App() {
  return (
    <Routes>
      <Route element={<Layout />}>
        <Route path='about' element={<About />} />
        <Route path='home' element={<Home />} />
        <Route path='logout' element={<Logout />} />
        <Route path='bye' element={<Bye />} />
      </Route>
    </Routes>
  );
}
```

상세페이지가 `product/1, product/2, product/3, product/...` 이런식으로 복잡한 경우라면?

```tsx
//index.tsx는 BroswerRouter 부분 참고

//App.tsx

function App() {
  return (
    <Routes>
      <Route element={<Layout />}>
        <Route path='product'>
          <Route path=':id' element={<About />} />
        </Route>
      </Route>
    </Routes>
  );
}
```

개인적으로 나는 RouterProvider보다는 이런식으로 라우팅하는것을 선호함 (순수 리액트와 리액트 라우터 돔을 활용한다면)

### Memory Router

- 예를들어 라우터를 테스트 해야하는 상황이 왔을때 이것을 활용함, 이는 테스트와 같이 기록 스택을 완벽하게 제어해야 하는 시나리오에 이상적

간단한 라우팅 테스트 코드

```tsx
import { render, screen, waitFor } from '@testing-library/react';

import { RouterProvider, createMemoryRouter } from 'react-router-dom';

import routes from './Components/Location/components/routes';

const context = describe;

describe('LoactionRoutes', () => {
  function renderRouter(path: string) {
    const router = createMemoryRouter(routes, {
      initialEntries: [`/${path}`],
    });
    render(<RouterProvider router={router} />);
  }

  context('when the current path is home', () => {
    it('renders the home page', () => {
      renderRouter('home');

      screen.getByText(/Welcome!/);
    });
  });

  context('when the current path is about', () => {
    it('renders the about page', () => {
      renderRouter('about');

      screen.getByText(/This is test/);
    });
  });

  context('when the current path is logout', () => {
    it('redirects to the bye page', async () => {
      renderRouter('logout');

      screen.getByText(/...남은시간/);

      await waitFor(
        () => {
          expect(screen.getByText(/로그아웃/)).toBeInTheDocument();
        },
        { timeout: 4000 }
      );
    });
  });
});
```

## Navigation

### NavLink,Link in React-Router

- `<Link>`는 사용자가 클릭하거나 탭하여 다른 페이지로 이동할 수 있는 컴포넌트

  - 브라우저의 주소만 바꿀뿐, 페이지를 새로 불러오지는 않는다.
  - `preventScrollReset`: 링크를 클릭할 때 스크롤 위치가 최 상단으로 재설정 되는 것을 방지,
    사용자가 링크를 클릭할 때 재설정되는 것을 방지할 뿐
  - `state`: 상태state 내부에 저장되는 새 위치에 대한 상태 저장 값을 설정하는 데 사용
    useLocation()으로 접근 가능.

    ```tsx
    <Link to='path' state={{ some: 'value' }} />;

    // in path
    const { state } = useLocation();
    ```

  - `reloadDocument`: 클라이언트측 라우팅을 건너뛰고 새롭게 문서를 불러옴.

- `<NavLink>`는 `<Link>`의 special version

  - 특정 링크에 스타일을 넣어 줄 수 있다. 실제로 개발자도구로 보면 className이 바뀜
  - `activeStyle` prop을 통해 스타일변경이 가능함

```tsx
import { NavLink, Link } from 'react-router-dom';

export default function Header() {
  return (
    <header>
      <nav>
        <ul>
          <li>
            <Link to='/home'>home</Link>
            <NavLink to='/home'>home</NavLink>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

### Navigate in React-Router

- `<Navigate>`는 렌더링될 때 현재 위치를 변경

- 예를 들면 로그아웃 버튼을 눌렀을때 => 특정페이지로 보냄 => 특정페이지의 render 부분에 `<Navigate>` 가 쓰여지면 거기서 다른곳으로 보내버림

### useNavigate in React-Router

- 특정 페이지로 이동하게 해주는 훅
- `useEffect` 내부에서도 사용이 가능함.
- 뿐만이 아니라 버튼을 눌렀을때 로직을 처리하고 어디로 보낸다. 이럴때도 많이 활용됨.
- `replace(스택에 저장할 것인지) 와 state(페이지로 이동시 넘겨야 하는 데이터가 존재할때)` 즉 `navigate(pathname, options)` 여기 options의 내부에서 활용할 수 있다.

  ```tsx
  import { useNavigate } from 'react-router-dom';

  function Component() {
    const navigate = useNavigate();

    useEffect(() => {
      navigate('....');
    }, []);

    const handleClickButton = () => {
      /**your logic**/
      navigate('....');
    };

    return; /**...*/
  }
  ```

- 데이터를 로드하거나 변경하는 동안 사용자를 다른 경로로 보내야 한다면 이것 보다는 `Route의 Loader와 actions`에서 사용할것을 권장함.
  선행작업이 이루어져야 할때를 생각하자. 그렇지만 수많은 api에서 요청을 해야할때도 과연 그럴지는 좀 의문이긴함.

  ```tsx
  <Route
    path='dashboard'
    loader={async () => {
      const user = await fake.getUser();
      if (!user) {
        // if you know you can't render the route, you can
        // throw a redirect to stop executing code here,
        // sending the user to a new route
        throw redirect('/login');
      }

      // otherwise continue
      const stats = await fake.getDashboardStats();
      return { user, stats };
    }}
  />
  ```

### History

[MDN](https://developer.mozilla.org/ko/docs/Web/API/History)

- 브라우저의 세션 기록, 즉 현재 페이지를 불러온 탭 또는 프레임의 방문 기록을 조작할 수 있는 방법을 제공.

- 객체내의 키의 의미

  - length(읽기 전용)
    현재 페이지를 포함해, 세션 기록의 길이를 나타내는 정수를 반환

  - scrollRestoration
    기록 탐색 시 스크롤 위치 복원 여부를 명시

  - state 읽기 전용
    기록 스택 최상단의 스테이트를 나타내는 값을 반환, popstate 이벤트를 기다리지 않고 현재의 스테이트를 볼 수 있는 방법

- History 객체 메서드 살펴보기

  - back()

    - 세션 기록의 바로 뒤 페이지로 이동하는 비동기 메서드
    - 브라우저의 뒤로 가기 버튼을 눌렀을 때, 그리고 history.go(-1)을 사용했을 때와 같다.

  - forward()

    - 세션 기록의 바로 앞 페이지로 이동하는 비동기 메서드
    - 브라우저의 앞으로 가기 버튼을 눌렀을 때, 그리고 history.go(1)을 사용했을 때와 같다.

  - go()

    - 현재 페이지를 기준으로, 상대적인 위치에 존재하는 세션 기록 내 페이지로 이동하는 비동기 메서드
    - 매개변수로 -1을 제공하면 바로 뒤로, 1을 제공하면 바로 앞으로 이동
    - 세션 기록의 범위를 벗어나는 값을 제공하면 아무 일도 일어나지 않는다.

  - pushState()

    - 주어진 데이터를 지정한 제목(제공한 경우 URL도)으로 세션 기록 스택에 넣음.
    - Safari를 제외한 모든 브라우저는 title 매개변수를 무시

  - replaceState()
    - 세션 기록 스택의 제일 최근 항목을 주어진 데이터, 지정한 제목 및 URL로 대체
    - Safari를 제외한 모든 브라우저는 title 매개변수를 무시

## 회고

```text
라우터와 라우팅은 현재에 너무나 중요한 기능이다. NextJS의 라우터 방식도 공부하면 좋다.
React-Router의 정말 기본적인 내용들을 다룬것들이고 공식 사이트를 보면 많은 기능들을 제공하고 있다.
Router를 공부하는 이유에 대해서 한번 이야기를 해보자면. 앞서 말했듯이 수많은 페이지가 존재하고 페이지별로 다르게 컨트롤 해야하는 경우들이 비일비재하다.

실제로 내가 일하고 있는 회사의 중개사 플랫폼은 굳이 SSR이 필요없어서 CSR을 택했다. 그리고 react-router-dom을 통해 사용하고 있다.

그렇다면 여기서 제공하는 모든 메서드들을 알고 있어야할까?
메서드들을 모두 알고 있을필요는 없다고 생각한다. 그렇지만 틈틈히 라우팅을 공부해서 해당 지식에대한 인사이트를 넓히는것은 좋다.

여기서 다루는 모든 개념을 챗 GPT처럼 척척박사로 알고 있으면 더할나위 없이 좋겠지만 인사이트를 늘리는 것만으로도 나름의 큰 소득이라고 생각한다.

라우팅을 공부하면서 가장 큰 이점은 개발은 혼자하는 것이 아니기때문에ㄴ
기획팀과 이야기를 했을때 더 효율적인 의사전달이 가능하다. (페이지에서 여기로 바로 데이터 쏴주세요. 스크롤 복원해주세요.., 뒤로가기 막아주세요.. 내가 제읾 많이 들은말..)

또한 요구사항이 들어왔을때 적어도 많은 인사이트들을 알고 있다면 그때 그때 필요한것들을 찾아서
바로바로 적용이 가능하기 때문이다. 추가적으로 올해가 가기전에 푸드키오스크 앱을 NextJS로 만들어보면서 AppRouter를 공부해보는 것도 굉장히 좋을 것 같다.
```
