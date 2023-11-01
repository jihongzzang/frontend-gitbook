# 4장

## 타입 설계

### Item 28 유효한 상태만 표현하는 타입을 지향하기

- 타입을 잘 설계하면 코드는 직관적으로 작성이 가능하다.

```ts
interface State {
  pageText: string;
  isLoading: boolean;
  error?: string;
}

function renderPage(state: State) {
  if (state.error) {
    return `Error! Unable to load ${currentPage}: ${state.error}`;
  } else if (state.isLoading) {
    return `Loading ${currentPage}`;
  }

  return `<h1>${currentPage}</h1>\n${state.pageText}`;
}

// 분기 조건이 명확히 분리되어 있지 않음.

// isLoading이 true이고 error가 동시에 존재 했을때는 어떻게 할 것인가

async function changePage(state: State, newPage: string) {
  state.isLoading = true;

  try {
    const response = await fetch(getUrlForPage(newPage));
    if (!response.ok) {
      throw new Error(`Unable to load ${newPage}: ${response.statusText}`);
    }
    const text = await response.text();
    state.isLoading = false;
    state.pageText = text;
  } catch (error) {
    state.error = '' + error;
  }
}
```

- 오류가 발생했을때 loading의 상태를 돌리는 로직이 빠져있다.

- error가 초기화 되지 않았기 때문에, 전환 상태에서 과거의 오류 메세지를 보여 주게 된다.

- 로딩 중에 사용자가 페이지를 바꿔버리면 예상하기 어렵다.

- 상태 값의 두 가지 속성이 동시에 정보가 부족하거나, 두 가지 속성이 충돌할 수 있다.

```ts
//refactored

interface RequestPending {
  state: 'pending';
}

interface RequestError {
  state: 'error';
  error: string;
}

interface RequestSuccess {
  state: 'ok';
  pageText: string;
}

type RequestState = RequestPending | RequestError | RequestSuccess;

interface State {
  currentpage: string;
  requests: { [page: string]: RequestState };
}

function renderPage(state: State) {
  const { currentpage } = state;
  const requestState = state.requests[currentpage];

  switch (requestState.state) {
    case 'pending':
      return `Loading ${currentpage}...`;

    case 'error':
      return `Error! Unable to load ${currentpage}: ${requestState.error}`;

    case 'ok':
      return `<h1>${currentpage}</h1>\n${requestState.pageText}`;
  }
}

async function chagnePage(state: State, newPage: string) {
  state.requests[newPage] = { state: 'pending' };

  state.currentpage = newPage;
  try {
    const response = await fetch(getUrlForPage(newPage));

    if (!response.ok) {
      throw new Error(`Unable to load ${newPage}: ${response.statusText}`);
    }

    const pageText = await response.text();
    state.requests[newPage] = { state: 'ok', pageText };
  } catch (e) {
    state.requests[newPage] = { state: 'error', error: '' + e };
  }
}
```

- 두 함수의 모호함을 없애고, 현재 페이지가 무엇인지, 모든 요청은 정확히
- 하나의 상태만을 담당하고 있다.

- 유효한 상태와 무효한 상태를 둘 다 표현하는 타입은 혼란을 초래하기 쉽고 오류를 유발한다.
- 유효한 상태만 표현하는 타입을 지향하자. 장기적으로 봤을때 시간을 절약하고 고통을 줄일 수 있다.

### Item 29 사용할 때는 너그럽게, 생성할 때는 엄격하게

- 함수의 매개변수는 타입의 범위가 넓어도 되지만, 결과를 반환할 때는 일반적으로 타입의 범위가 구체적이어야한다.

```ts
type LngLat =
  | { lng: number; lat: number }
  | { lon: number; lat: number }
  | [number, number];

type LngLatBounds =
  | { northeast: LngLat; southwest: LngLat }
  | [LngLat, LngLat]
  | [number, number, number, number];

interface CameraOptions {
  center?: LngLat;
  zoom?: number;
  bearing?: number;
  pitch?: number;
}

declare function setCamera(camera: CameraOptions): void;

declare function viewportForBounds(bounds: LngLatBounds): CameraOptions;
```

- 3D 매핑 API가 존재하고, 카메라의 위치를 지정하고 경계 박스의 뷰포트를 계산하는 방법을 제공한다.

- 일부 값은 건드리지 않으면서 동시에 다른 값을 설정할 수 있어야 하므로 `CameraOptions`의 필드는 모두 선택적이다.

- `LngLat` 타입도 `setCamera`의 매개변수의 범위를 넓혀 준다.

- 하지만 근본적으로 `viewportForBounds`의 타입 선언이 만들어질 때에도 너무 자유롭다.

- 카메라 값을 안전한 타입으로 사용하는 유일한 방법은 유니온 타입의 각 요소별로 코드를 분기하는것!!

```ts
interface LngLat {
  lng: number;
  lat: number;
}

type LngLatLike = LngLat | { lon: number; lat: number } | [number, number];

interface Camera {
  center: LngLat;
  zoom: number;
  bearing: number;
  pitch: number;
}

interface CameraOptions extends Omit<Partial<Camera>, 'center'> {
  center?: LngLatLike;
}

type LngLatBounds =
  | { northeast: LngLatLike; southwest: LngLatLike }
  | [LngLatLike, LngLatLike]
  | [number, number, number, number];
```

- 카메라가 너무 엄격하므로 조건을 완화하여 느슨한 `CameraOptions` 타입으로 만들었음.

- 보통 매개변수 타입은 반환 타입에 비해 범위가 넓은 경향이 있다.

- 매개변수와 반환 타입의 재사용을 위해서 기본 형태와 느슨한 형태(매개변수타입)를 도입하는 것이 좋다.

### Item 30 문서에는 타입 정보를 쓰지 않기

- 주석 대신 타입 정보를 작성한다면 코드가 변경된다 하더라도 정보가 정확히 동기화된다.

- 특정 매개변수를 설명하고 싶을땐 JSDoc `@param` 구문을 사용하자.

- 타입이 명확하지 않은 경우는 변수명에 단위 정보를 포함하는 것을 고려하는 것이 좋다.

### Item 31 타입 주변에 null값 배치하기

```ts
function extent(nums: number[]) {
  let min, max;

  for (const num of nums) {
    if (!min) {
      min = num;
      max = num;
    } else {
      min = Math.min(min, num);
      max = Math.max(max, num);
    }
  }
  return [min, max];
}

const [min, max] = extent([0, 1, 2]);
const span = max - min; //error
```

- 최솟값이나 최댓값이 0인 경우, 값이 오버라이드 됨,

- nums 배열이 비어있다면, `[undefined, undefined]`를 반환

```ts
//refactored

function extent(nums: number[]) {
  let result: [number, number] | null = null;

  for (const num of nums) {
    if (!result) {
      result = [num, num];
    } else {
      result = [Math.min(num, result[0]), Math.max(num, result[1])];
    }
  }

  return result;
}

const [min, max] = extent([0, 1, 2]);
const span = max - min; //정상
```

- 결과값으로 단일 객체를 사용함으로써 설계를 개선

- 한 값의 `null` 여부가 다른 값의 `null`여부에 암시적으로 관련 되도록 설계하면 안 된다.

- API 작성 시에 반환 타입을 큰 객체로 만들고 반환 타입 전체가 `null`이거나 `null`이 아니게 만들어야 한다.

- 클래스를 만들 때는 필요한 모든 값이 준비되었을 때 생성하여 `null`이 존재하지 않도록 하는 것이 좋다.
