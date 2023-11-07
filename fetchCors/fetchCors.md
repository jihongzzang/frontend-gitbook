# Fetch API & CORS (Cross-Origin Resource Sharing)

## Fetch API 란

- 네트워크 요청에 관련된 것들을 제공하는 API
- `Window` 와 `Worker` 컨텍스트 양쪽에서 모두 사용할 수 있는 전역 메서드
- 사용법이 간단, 프로미스를 지원하기 때문에 비동기 처리를 위한 콜백 패턴의 단점에서 자유롭다.
- JSON을 기본 지원한다.

```ts
fetch('http://localhost:3000/products'); // → Promise

await fetch('http://localhost:3000/products'); // → Response

const response = await fetch('http://localhost:3000/products');
const reader = response.body.getReader();
const chunk = await reader.read();
const body = new TextDecoder().decode(chunk.value);
const data = JSON.parse(body);

//data를 가져올 수는 있으나 이렇게 실제로는 사용하지 말자.
```

```ts
const response = await fetch(`${URL}`);
const data = await response.json();
```

[MDN Fetch API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)
[MDN Using Fetch](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)

## Promise

[MDN Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
[MDN Using Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
[간단한 예시 코드](https://github.com/jihongzzang/js-cs-algorithm/blob/main/js_study/Promise/promise.js)

- 자바스크립트는 비동기 처리를 위한 하나의 패턴으로 `콜백 함수`를 사용.

- `콜백 패턴`은 콜백헬이 발생했을때 가독성이 나쁘고 비동기 처리과정에서 에러처리가 힘들댜. 또 여러개의 비동기를 한번에 처리하는 데도 한계가 있음.

- 따라서 `ES6`에서 비동기 처리를 위한 또 `다른 패턴으로 프로미스`가 등장

- 프로미스의 경우 비동기 처리 시점을 명확하게 표현할 수 있는 장점 (특히, 타입스크립트를 사용하는 환경에서는 Promise를 쓰는 것이 좋다.)

```ts
console.log(1); //---1
console.log(2); //---2
setTimeout(function () {
  console.log(3); //---4
}, 5000);
console.log(4); //---3
```

- 비동기 처리는 언제?
  - 언제 실행될지 예측하기 어렵거나 주가 되는 작업이 아닐때
  - 대표적으로 통신을 할때 많이 씀
  - 웹브라우저와 웹서버가 통신을 할때 동기적으로 통신을 했다면 통신하는 동안에는 어떤 행위도 못했다. (끔찍하다.)

```ts
fetch(`${URL}`)
  .then((response) => response.json())
  .then((data) => console.log(data));

//여기서 then이 Promise임!!
```

- Promise 를 사용하는 이유
  - 비동기 적인 작업을 처리할때 그 작업이 성공했는지 실패했는지에 대해 표준화된 방식을 이용해 처리 할 수 있도록 해줌
  - 성공시 `then`으로 전달된 함수가 실행, 실패시 `catch`로 전달된 함수가 실행

```ts
fetch(`${URL}`)
  .then(function (response) {
    console.log('response', response);
  })
  .catch(function (reason) {
    console.log('reason', reason);
  });
```

- 프로미스의 비동기 처리 상태가 변화했을때 이에 따른 후속처리

  - `then` 메서드의 경우 `두 개의 콜백 함수를 인자`로 받을 수 있다. `첫번째는 fulfilled` 상태, `두번째는 rejected` 상태
  - `catch` 메서드의 경우 `한개의 콜백 함수를 인자`로 전달 받는다. 이 경우 `프로미스가 rejected 인 경우에만 호출`
  - `finally` 메서드는 `한개의 콜백 함수를 인자`로 전달 받는데 프로미스의 `성공 또는 실패와 상관없이 한번만 호출`

- `Promise All` | `Promise Race`

- `all` 의 경우 가장 늦게 끝나는 작업 이후 후속으로 처리되는 작업이 있을때 사용
- `race` 의 경우 같은 일을 하는 함수끼리 경쟁을 시켜 가장 먼저 끝나는 작업의 결과값을 받아 그 다음 후속으로 처리되는 작업이 있을때

```ts
console.time('Promise.all');

Promise.all([timer(1000), timer(2000), timer(3000)]).then(function (result) {
  console.log('result', result);
  console.timeEnd('Promise.all');
});

//result (3) [1000, 2000, 3000]

//Promise.all: 3002.2490234375 ms

console.time('Promise.race');

Promise.race([timer(1000), timer(2000), timer(3000)]).then(function (result) {
  console.log('result', result);
  console.timeEnd('Promise.race');
});

//'result' 1000

//'Promise.race: 1003ms'
```

## ReableStream

[MDN ReableStream](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)

## Unicode

[Wikipedia](https://en.wikipedia.org/wiki/Unicode)

## CORS 란

- 많은 사람들의 정보가 들어가 있는 공간이기 때문에 인터넷 환경에서 보안은 굉장히 중요하다.
- 따라서 보안에 대해 신경쓰지 않으면 나쁜마음을 먹은 누군가는 사람들의 정보를 도용해 이익을 챙길 수도 있다.
- 즉, CORS란 브라우저와 서버간 통신에서 이상한 서버와 통신을 해서 원하지 않은 리소스를 받지 않도록 방어해주는 브라우저의 보안 규칙
- 웹 브라우저는 `Same-Origin-Policy`에 따라 웹 페이지와 리소스를 요청한 곳이 서로 다른 출처일 때 서버에서 얻은 결과를 사용할 수 없게 막는다.
  서버에 요청하고 응답을 받아오는 것까지는 이미 진행이 다 된 상황이란 점에 주의!

```text
https://www.jihong.com/users?sort=asc&page=1#foo

위의 URL은 프로토콜, 호스트, 패스, 쿼리스트링, 프라그먼트의 5가지로 이루어져 있다.
- 이때 출처는 Protocol과 Host, :80, :443과 같은 포트 번호까지 모두 합친 것을 의미한다.
- 출처에서 포트번호는 생략이 가능하고 이는 HTTP, HTTPS의 기본 포트번호가 정해져 있기 때문에 가능한 부분이다.
```

- CORS의 기본적인 동작방식

  - 웹 클라이언트 어플리케이션이 다른 출처의 리소스를 요청할 때 => HTTP 프로토콜을 사용하여 요청을 보낸다.
  - 이때 브라우저는 요청의 헤더부분에 => Origin이라는 필드에 요청을 보내는 출처를 함께 담아 보냄.
  - 이후 서버가 이 요청에 대한 응답하는 과정에서 => 응답 헤더에 `Access-Control-Allow-Origin` 이라는 값에 `이 리소스를 접근하는 것이 허용된 출처`를 내려준다.
  - 응답을 받은 브라우저는 => 자신이 보냈던 요청과 서버가 보내준 `Access-Control-Allow-Origin`를 비교해본 후 응답이 유효한지 결정

- Preflight Request

  - 예비 요청의 역할은 본 요청을 보내기 전 브라우저가 스스로 요청을 보내는 것에 대해 안전한지 확인
  - 예비 요청에는 HTTP 메서드에서 OPTIONS 메서드가 사용

- 동일 출처 정책(Same-Origin-Policy)

  - 어떤 출처에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호 작용할 수 있는 방법을 제한하는 중요한 보안 메커니즘
  - 즉, 쉽게 말해서 브라우저에서 js등을 실행시켜 실제 내부에 접근해서 데이터를 읽고 수정하고 이런것들을 막기위한 것
  - 프로토콜, 포트(명시된 경우), 그리고 호스트가 같은 경우 두 URL은 동일한 출처를 가진다.

[MDN CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
[MDN Same-origin-policy](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)
