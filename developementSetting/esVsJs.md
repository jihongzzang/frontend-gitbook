# ES Modules vs CommonJS

함수들끼리 데이터를 공유하고 싶다면 어떻게 해야할까?

전역 스코프에 해당 데이터를 둔다면 다른 내부 요소에 의해 망가질 수도 있고,

순서가 뒤엉켜 전역 스코프의 요소가 존재하지 않을 수도 있다.

따라서 모듈이라는 방식을 이용하면 각각 필요한 모듈을 import하여 사용함으로써 어디에서 에러가 나는지 파악하기 쉽다.

즉 ES Modules와 commonJS는 모듈시스템들이다.

```js
CommonJS;

const module = require('...');
const { func } = require('...');

module.exports = '...'; // default exports
module.exports.func = () => {}; // named exports

ES Modules;

import a from '...';
import { func } from '...';

export const a = '...'; // default exports
export default func = () => {}; // named exports
```

ES Modules

- 모듈을 비동기 환경에서 다운로드하며, import export 구문을 찾아서 파싱한다.
- 따라서 ES modules는 실행해보지 않아도 import, export 에러를 감지할 수 있다.
- top-level await가 가능하다.

```text
지정한 파일을 entry point로 하여 나머지 파일들은 import문을 따라가며 찾는다.

파일을 브라우저에서 사용하려면 모듈 레코드 라는 과정이 필요하고 이후 모듈 레코드를 모듈 인스턴스로 변환

코드와 현재 상태를 결합함
```

CommonJS

- require가 동기로 이루어지므로 promise를 return 하지 않는다.
- 따라서 CommonJS는 실행해보아야 import, export 에러를 감지할 수 있다.
  기본 값이다.
- top-level await가 불가능하다.

```text
파일을 import 순서에 따라 순차적으로 완전 실행하므로 변수 값을 할당할 수 있다.

변수에 할당할 시 export 값을 사본으로 복사하여 사용한다.

따라서 export, import하는 변수가 다르다.
```

```text
순환관계가 있을 때 CommonJS는 변수 값을 복사해서 사용하므로 다론곳에서 변수는 변하지 않는다.

ES Modules에서는 같은 변수를 참조하고 있으므로 setTimout에서 제대로 할당된 변수가 출력된다. ES Module은 위와 같이 순환 의존성을 지원한다.
```
