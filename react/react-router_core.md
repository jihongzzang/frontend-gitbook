# 리액트 라우터의 주요 개념 번역

[리액트 라우터 concept](https://reactrouter.com/en/main/start/concepts) 의 내용중 중요하다고 생각되는 내용을 번역한 글입니다.

리액트 라우터는 어떻게 CSR을 가능하도록 했는지 이것이 마법인지 아닌지 한번 찬찬히 뜯어보자.

## 정의

다음은 React 라우터에 대해 이야기할 때 많이 사용하는 몇 가지 단어입니다. 이 가이드의 나머지 부분에서는 각 단어에 대해 더 자세히 설명합니다.

`Location` - 브라우저에 내장된 window.location 객체를 기반으로 하는 React 라우터 전용 객체입니다. "사용자가 현재 어디에 있는지"를 나타냅니다. 대부분 URL을 객체로 표현한 것이지만 그보다 더 많은 것을 담고 있습니다.

`Location State` - URL에 인코딩되지 않은 위치에 대해 지속되는 값입니다. 해시 또는 검색 매개변수(URL에 인코딩된 데이터)와 매우 유사하지만 브라우저의 메모리에 보이지 않게 저장됩니다.

`History Stack` - 사용자가 탐색할 때 브라우저는 각 위치를 스택에 추적합니다. 브라우저에서 뒤로 가기 버튼을 길게 클릭하면 브라우저의 히스토리 스택을 바로 볼 수 있습니다.

`CSR` - 일반 HTML 문서는 다른 문서에 링크할 수 있으며 브라우저는 히스토리 스택을 자체적으로 처리합니다. 클라이언트 측 라우팅을 사용하면 개발자가 서버에 문서를 요청하지 않고도 브라우저 히스토리 스택을 조작할 수 있습니다.

`History` - 브라우저 히스토리 스택을 프로그래밍 방식으로 조작할 수 있는 API를 제공할 뿐만 아니라 URL의 변경 사항을 구독할 수 있는 객체입니다.

`History Action` - 팝, 푸시 또는 바꾸기 중 하나입니다. 사용자는 이 세 가지 이유 중 하나로 URL에 도달할 수 있습니다. 푸시는 새 항목이 기록 스택에 추가될 때 발생합니다(일반적으로 링크 클릭 또는 프로그래머가 강제로 탐색). 교체는 새 항목을 푸시하는 대신 스택의 현재 항목을 교체한다는 점을 제외하면 비슷합니다. 마지막으로, 사용자가 브라우저 크롬에서 뒤로 또는 앞으로 버튼을 클릭하면 팝이 발생합니다.

`URL Params` - 동적 세그먼트와 일치하는 URL의 파싱된 값입니다.

`Router` - 다른 모든 컴포넌트와 훅이 작동하도록 하는 스테이트풀 최상위 컴포넌트입니다.

`Router Config` - 경로 일치 분기를 만들기 위해 현재 위치에 대해 순위를 매기고 일치시킬(중첩 포함) 경로 객체의 트리입니다.

`Route` - 일반적으로 {경로, 요소 } 또는 <경로 경로 요소>의 모양을 가진 개체 또는 경로 요소입니다.경로는 경로 패턴입니다.경로 패턴이 현재 URL과 일치하면 요소가 렌더링됩니다.

`Route Element` - 이 요소의 프롭은 `<Routes>`에 의해 경로를 생성하기 위해 읽히지만, 그렇지 않으면 아무 작업도 수행하지 않습니다.

`Nested Routes` - 경로에는 자식이 있을 수 있고 각 경로가 세그먼트를 통해 URL의 일부를 정의하기 때문에 단일 URL이 트리의 중첩된 "가지"에서 여러 경로와 일치할 수 있습니다. 이를 통해 아웃렛, 상대 링크 등을 통한 자동 레이아웃 중첩이 가능합니다.

`Relative links` - /로 시작하지 않는 링크는 렌더링되는 가장 가까운 경로를 상속합니다. 따라서 전체 경로를 알고 구축할 필요 없이 더 깊은 URL로 쉽게 연결할 수 있습니다.

`Match` - 경로가 URL과 일치할 때 일치하는 URL 매개변수 및 경로 이름과 같은 정보를 보유하는 객체입니다.

`Matches` - 현재 위치와 일치하는 경로(또는 경로 구성의 분기)의 배열입니다. 이 구조를 사용하면 중첩된 경로를 사용할 수 있습니다.

`Parent Route` - 하위 경로가 있는 경로입니다.

`Outlet` - 일치 집합에서 다음 일치 항목을 렌더링하는 컴포넌트입니다.

`Index Route` - 경로가 없는 자식 경로로, 부모의 URL에서 부모의 아웃렛에 렌더링합니다.

`Layout Route` - 경로가 없는 상위 경로로, 하위 경로를 그룹화하는 데만 사용됩니다.

## History and Locations

- React Router가 어떤 작업을 수행하려면 먼저 **브라우저 `History Stack`의 변경 사항을 구독**할 수 있어야 한다.

- **브라우저는 사용자가 탐색할 때 자체 기록 스택을 유지 => 이것이 뒤로 및 앞으로 버튼이 작동하는 방식**

- 전통적인 웹사이트(JavaScript가 없는 HTML 문서)에서는 사용자가 링크를 클릭하거나, 양식을 제출하거나, 뒤로 및 앞으로 버튼을 클릭할 때마다 브라우저가 서버에 요청

```text
예를 들어 다음과 같은 사용자를 생각해 보세요.

1. 어떤 링크를 클릭하면 `/dashboard`
2. 어떤 링크를 클릭하면 `/accounts`
3. 어떤 링크를 클릭하면 `/customers/123`
4. 뒤로 버튼을 클릭합니다
5. 어떤 링크를 클릭하면 `/dashboard`

// 이것을 스택으로 생각해보면

[*/dashboard*]
[/dashboard, */accounts*]
[/dashboard, /accounts, */customers/123*]
[/dashboard, */accounts*, /customers/123]
[/dashboard, /accounts, */dashboard*]
```

### History Object in CSR

- CSR에서 브라우저의 히스토리 스택을 조작이 가능하다.
- 서버에 요청하는 브라우저의 기본 동작 없이 URL을 변경할 수 있다.

```tsx
<a
  href='/contact'
  onClick={(event) => {
    // stop the browser from changing the URL and requesting the new document
    event.preventDefault();
    // push an entry into the browser history stack and change the URL
    window.history.pushState({}, undefined, '/contact');
  }}
/>
```

이 코드는 URL을 변경 하지만 UI에 대해서는 아무 작업도 수행하지 않는다.

UI가 연락처 페이지로 변경되도록 하려면 어딘가에서 일부 상태를 변경하는 코드를 더 작성해야 한다.

**_문제는 브라우저가 "URL을 수신"하고 이와 같은 변경 사항을 구독하는 방법을 제공하지 않는다는 것_**

```tsx
window.addEventListener('popstate', () => {
  // URL changed!
  //이렇게 변경 사항을 작성할 수 있지만..
});
```

하지만 이는 사용자가 뒤로 또는 앞으로 버튼을 클릭할 때만 실행된다는 것
`window.history.pushState` 또는 `window.history.replaceState`를 호출한 경우에 대한 이벤트는 없다.

**_바로 이 지점에서 React 라우터 전용 히스토리 객체가 등장. 이 객체는 히스토리 액션이 푸시, 팝 또는 교체인지 여부에 관계없이 "URL 변경을 수신"하는 방법을 제공_**

```tsx
let history = createBrowserHistory();

history.listen(({ location, action }) => {
  // this is called whenever new locations come in
  // the action is POP, PUSH, or REPLACE
});

// 내부 모듈의 함수

function createBrowserRouter(routes, opts) {
  return createRouter({
    basename: opts == null ? void 0 : opts.basename,
    future: _extends({}, opts == null ? void 0 : opts.future, {
      v7_prependBasename: true,
    }),
    history: createBrowserHistory({
      window: opts == null ? void 0 : opts.window,
    }),
    hydrationData:
      (opts == null ? void 0 : opts.hydrationData) || parseHydrationData(),
    routes,
    mapRouteProperties: UNSAFE_mapRouteProperties,
    window: opts == null ? void 0 : opts.window,
  }).initialize();
}
```

- 앱이 자체 히스토리 객체를 설정할 필요는 없으며, **이는 <라우터>가 담당**

- 라우터는 이러한 객체 중 하나를 설정하고 **히스토리 스택의 변경 사항을 구독한 다음 URL이 변경되면 해당 상태를 최종적으로 업데이트**

- 이렇게 하면 앱이 다시 렌더링되고 올바른 UI가 표시

- **상태 변경에 필요한 것은 위치뿐이며, 그 외의 모든 것은 단일 객체에서 작동**

## Locations

- 브라우저에는 window.location에 위치 객체가 존재
- 이 객체는 URL에 대한 정보를 알려줄 뿐만 아니라 URL을 변경하는 몇 가지 메서드도 제공

- React 라우터에는 window.location에를 본뜬 위치라는 개념이 있지만 훨씬 더 간단.

```tsx
{
  pathname: "/bbq/pig-pickins",
  search: "?campaign=instagram",
  hash: "#menu",
  state: null,
  key: "aefz24ie"
}
```

- `pathname`, `serach`, `hash` 를 통해 url을 얻고
- `state`, `key` 는 React 라우터에만 해당

### Location pathname

- https://example.com/teams/hotspurs의 경우 pathname은 /teams/hotspurs
- 경로가 일치하는 위치의 유일한 부분

### Location search

- location search
- search params
- URL search params
- query string

- React 라우터에서는 이를 "location search" 이라고 부름.
- URLSearchParams의 직렬화된 버전

### Location hash

- URL의 해시는 현재 페이지의 스크롤 위치를 나타냄
- 서버에 새로 요청하지 않고도 조작할 수 있는 유일한 부분

### Location State

- 브라우저는 pushState에 값을 전달하여 탐색에 대한 정보를 유지할 수 있다.
- 사용자가 다시 클릭하면 history.state의 값이 이전에 '푸시'된 값으로 변경.

- **React Router는 이 브라우저 기능을 활용, 약간 추상화하여 History 대신 Loaction의 값을 표시**

- Location State의 몇 가지 훌륭한 사용 사례는 다음과 같다.

  - 다음 페이지에서 사용자가 어디에서 왔는지 알려주고 UI를 분기하는 경우.
  - 목록의 일부 레코드를 다음 화면으로 전송하여 부분 데이터를 즉시 렌더링한 다음 나중에 나머지 데이터를 가져올 수 있다.

### Location Key

- 각 위치에는 고유한 키가 부여.
- 이는 위치 기반 스크롤 관리, 클라이언트 측 데이터 캐싱 등과 같은 고급 사례에 유용.
- 각각의 새 위치가 고유한 키를 갖기 때문에 일반 객체, 새로운 Map(), 심지어 locationStorage에 정보를 저장하는 추상화를 구축할 수 있다.

- 기본적인 클라이언트 측 데이터 캐시는 위치 키(및 가져오기 URL)로 값을 저장하고 사용자가 다시 클릭할 때 데이터 가져오기를 건너뛸 수 있다.

### Matching

- 초기 렌더링에서 `history stack`이 변경되면 React Router는 `location`를 `Route Config`와 일치시켜 렌더링할 `Matches`을 생성합니다.

### Defining Routes

```tsx
// route config is routes의 tree이고 아래는 예시
<Routes>
  <Route path='/' element={<App />}>
    <Route index element={<Home />} />
    <Route path='teams' element={<Teams />}>
      <Route path=':teamId' element={<Team />} />
      <Route path=':teamId/edit' element={<EditTeam />} />
      <Route path='new' element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path='/privacy' element={<Privacy />} />
    <Route path='/tos' element={<Tos />} />
  </Route>
  <Route path='contact-us' element={<Contact />} />
</Routes>
```

- `<Routes>` 컴포넌트는 props.children를 통해 재귀적으로 호출하여 props을 제거한 후 다음과 같은 객체를 생성.

```tsx
let routes = [
  {
    element: <App />,
    path: '/',
    children: [
      {
        index: true,
        element: <Home />,
      },
      {
        path: 'teams',
        element: <Teams />,
        children: [
          {
            index: true,
            element: <LeagueStandings />,
          },
          {
            path: ':teamId',
            element: <Team />,
          },
          {
            path: ':teamId/edit',
            element: <EditTeam />,
          },
          {
            path: 'new',
            element: <NewTeamForm />,
          },
        ],
      },
    ],
  },
  {
    element: <PageLayout />,
    children: [
      {
        element: <Privacy />,
        path: '/privacy',
      },
      {
        element: <Tos />,
        path: '/tos',
      },
    ],
  },
  {
    element: <Contact />,
    path: '/contact-us',
  },
];
```

- route config의 모든 분기의 모든 세그먼트를 더하면 앱이 응답하는 route config은 다음과 같다.

```tsx
[
  '/',
  '/teams',
  '/teams/:teamId',
  '/teams/:teamId/edit',
  '/teams/new',
  '/privacy',
  '/tos',
  '/contact-us',
];
```

- URL /teams/new를 생각해 보세요. 이 목록에서 어떤 패턴이 URL과 일치할까?

- React 라우터는 여기서 한 가지 결정을 내린다.
  대부분의 라우터는 클라이언트 측과 서버 측 모두 패턴이 정의된 순서대로 처리하고 먼저 일치하는 것이 승리

- 이 경우 우리는 /를 일치시키고 `<Home/>` 컴포넌트를 렌더링할 것이지만 확실히 우리가 원했던 것은 아니다.
  이런 종류의 라우터에서는 예상한 결과를 얻기 위해 경로를 완벽하게 정렬 이것이 v6까지 React 라우터가 작동하는 방식

- 매칭할 때 세그먼트 수, 정적 세그먼트, 동적 세그먼트, 스타 패턴 등에 따라 경로의 순위를 매기고 가장 구체적으로 일치하는 경로를 선택

### Pathless Routes

```tsx
<Route index element={<Home />} />
<Route index element={<LeagueStandings />} />
<Route element={<PageLayout />} />
```

- 경로도 없는데 어떻게 경로가 될 수 있을까?
- 바로 이 부분에서 React Routes에서 "Route"라는 단어가 꽤 느슨하게 사용

- `<Home/>`과 `<LeagueStandings/>`는 인덱스 경로이고 `<PageLayout/>`은 레이아웃 경로.
- 둘 다 매칭과 큰 관련이 없다!!

### Route Matches

경로가 URL과 일치하면 일치 객체로 표시.

```tsx
<Route path=':teamId' element={<Team />} />

{
  pathname: "/teams/firebirds",
  params: {
    teamId: "firebirds"
  },
  route: {
    element: <Team />,
    path: ":teamId"
  }
}
```

- pathname은 이 route와 일치하는 URL의 일부(이 경우에는 전체)를 보유.

- params는 일치하는 모든 동적 세그먼트에서 구문 분석된 값을 보유,

- params의 객체 키는 세그먼트의 이름에 직접 매핑

- :teamId는 params.teamId가 된다.

- **경로는 트리이기 때문에 하나의 URL이 트리의 전체 분기와 일치**

- **/teams/firebirds URL을 예로 들면 다음과 같은 경로 브랜치가 됨.**

```tsx
<Routes>
  <Route path='/' element={<App />}>
    <Route index element={<Home />} />
    <Route path='teams' element={<Teams />}>
      <Route path=':teamId' element={<Team />} /> <= 여기가 일치하는 부분
      <Route path=':teamId/edit' element={<EditTeam />} />
      <Route path='new' element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path='/privacy' element={<Privacy />} />
    <Route path='/tos' element={<Tos />} />
  </Route>
  <Route path='contact-us' element={<Contact />} />
</Routes>
```

## Rendering

`/teams/firebirds URL을 예`로 들어보자. `<Routes>`는 위치를 경로 구성과 일치시키고,
일치하는 집합을 가져온 다음, 다음과 같이 React 엘리먼트 트리를 렌더링

```tsx
<App>
  <Teams>
    <Team />
  </Teams>
</App>
```

- 상위 경로의 요소 안에 렌더링된 각 일치 항목은 매우 강력한 추상화
- 대부분의 웹사이트와 앱은 상자 안의 상자, 상자 안의 상자, 페이지의 하위 섹션을 변경하는 탐색 섹션이 있는 상자 안의 상자라는 특징을 공유

## Outlets

- 이 중첩 요소 트리는 자동으로 생성되지 않는다.
- `<Routes>`는 첫 번째 일치 요소를 렌더링, 다음 일치 요소는 `<Teams>`입니다. 이를 렌더링하기 위해 App은 아웃렛을 렌더링해야 한다.

```tsx
function App() {
  return (
    <div>
      <GlobalNav />
      <Outlet />
      <GlobalFooter />
    </div>
  );
}
```

- outlet 컴포넌트는 항상 다음 매치되는 것을 렌더링

```tsx
// /teams/firebirds/edit

<App>
  <Teams>
    <EditTeam />
  </Teams>
</App>
```

## Index Routes

- Index Route를 다른 방식으로 생각하면 부모는 일치하지만 자식이 일치하지 않을 때 기본 자식 경로
- 사용자 인터페이스에 따라 인덱스 경로가 필요하지 않을 수도 있지만, 부모 경로에 어떤 종류의 지속적인 탐색이 있는 경우
  사용자가 아직 항목을 클릭하지 않았을 때 그 공간을 채우기 위해 인덱스 경로가 필요할 가능성이 높음

## Layout Routes

- 레이아웃 라우트라고 부르는 이유는 이 라우트가 매칭에 전혀 참여하지 않기때문이다.
- 동일한 레이아웃에서 여러 개의 자식 경로를 더 간단하게 래핑하기 위해 존재

## Navigating

- url을 변경시키는 행위
- `<Link>`와 `navigate` 두가지가 존재.

- `<Link>`를 렌더링하면 사용자가 클릭할 때 URL을 변경할 수 있습니다.
- **React 라우터는 브라우저의 기본 동작을 방지**하고 history에 새 항목을 history stack에 푸시하도록 지시
- 위치가 변경되고 새로운 일치 항목이 렌더링됩니다.

- 접근성 문제(키보드, 집중력, SEO 등)가 충족되도록 여전히 `<a href>`를 렌더링

## NavigateFunction

- Navigating을 함수로 사용하게 해줌.
