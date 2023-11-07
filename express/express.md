# ExpressJS

[ExpressJS Official Site](https://expressjs.com/)

## Express 란

- Fast, unopinionated, minimalist web framework for Node.js(`Node.js` 를 위한 빠르고 개방적인 간결한 웹 프레임워크)

- `Node.js`란 자바스크립트를 브라우저 외부에서 사용할 수 있게 해주는 프로그램

- 즉, 쉽게 말해서 NodeJS를 사용하여 서버를 개발하고자 하는 개발자들을 위한 프레임워크이다.

```text
프론트 엔드 개발자역시도 실무를 뛰다보면 간단한 백엔드 개발자들이 프로토타입이나 어드민 페이지 같은 경우는 실제로 간단히 서버를 띄우고
데이터베이스(mySQL)와 연동하여 풀스택 맛보기로 진행하기도 한다. 이번기회에 간단하게 설치 및 서버를 띄우는 방벙베 대해 공부해보자
```

## URL 구조

```node
폴더를 하나 만들어주자
- $mkdir express-app

패키지 초기화
- $npm init -y

타입스크립트, 린트 등등 입맛대로 개발하고자 하는 것들을 설치해주자.

$npm i -D typescript
$npx tsc --init

$npm i -D eslint
$npx eslint --init

$npm i -D ts-node

드디어 express 설치를!!

npm i express
npm i -D @types/express
```

```ts
//root app.ts 생성

import express from 'express';

const port = 3000;

const app = express();

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

```node
실행해보자!!

$npx ts-node app.ts
```

- 화면에 잘 나올텐데 한가지 문제는 내가 send를 바꿔도 실시간으로 업데이트 해주지 않는다.

```node
서버를 자동으로 업데이트 시켜주는 라이브러리 노드몬 두둥 설치!!

$npm i -D nodemon
$npx nodemon app.ts
```

- 개발 서버를 브라우저 url에 입력하거나 터미널에서 해당 서버에 대한 내용을 가져 올 수 있다.!!

- 실제 개발 서버와 연동했을때 CORS에러가 날 수도 있다. (실제로 내 개발서버는 8080이었는데 3000 서버와 통신하는 과정에서 CORS에러 발생)

```node
당황하지말고 후딱 cors Library 설치하자

$npm i cors
$npm i -D @types/cors

사용하고자 하는 파일에서 추가하자 나의 경우 app.ts

import cors from 'cors';
app.use(cors());
```

[cors Libray Github](https://expressjs.com/en/resources/middleware/cors.html)

## REST API

[REST API 관련 자료 1](https://ics.uci.edu/~fielding/pubs/dissertation/top.htm)

[REST API 관련 자료 2](https://martinfowler.com/articles/richardsonMaturityModel.html)

- 원티드나 채용공고 사이트들에서 쉽게 볼 수 있는 단어이다 `REST API`

- 서버와 클라이언트를 인터넷을 통하여 데이터교환을 위한 한가지 방법이라고 하고 싶다. 왜냐 GraphQL 도 있기 떄문!!
  궁금 하신 분들은 [여기](/react/react_component.md) 여기도 한번 보자.

- 우선 rest 란 API 시스템을 구현하기 위한 아키텍쳐 중 하나(Graphql, SOAP, GRPC, REST, etc)

- rest API란 rest를 기반으로 서비스 API를 구현한 것

- `자원`(URL), `행위`(request method), `표현`(payload) 3가지 요소로 구성

- 대부분은 resource와 verb만 도입하는 수준으로 사용한다.

## HTTP Method(CRUD)

크게 5가지 정도가 존재한다.

- 실제로 서버에게 `어떤식으로 요청할게요.` 라고 명시한다고 생각하자.
- 그리고 `요청할거에요` 와 동시에 이 요청에 대한 표현이 필요한 메서드도 존재하고 필요하지 않은 메서드도 존재한다.

```text
- read(collection, item)
GET : 모든 / 특정 자원 취득 | payload : X

- create
POST : 자원 생성 | payload : O

- update
PUT : 자원의 전체 교체 | payload : O

- update
PATCH : 자원의 일부 수정 | payload : O

- delete
DELETE : 모든/특정 자원 리소스 삭제 | payload : X
```

## HTTP Method를 캐시의 관점에서

- 캐시란 데이터 값을 미리 복사해 놓는 임시장소
- 웹 캐시는 레이턴시와 네트워크 트래픽을 줄여줌으로써 리소스를 보여주는 데에 필요한 시간을 줄여준다.
- HTTP 캐싱을 활용하면 웹 사이트가 좀 더 빠르게 반응하도록 만들 수 있다.

- `GET` - 일반적으로 브라우저에서 캐시할 수 있다
- `POST` - 일반적으로 캐시가 불가능하다. 하지만 Expires와 Cache-Control header를 이용하여 구현하여 응답할 수 있다.
- `PUT`, `DELETE` - 해당 요청은 캐시를 적용할 수 없다.
