# Describe-Context-It, Given-When-Then

## Describe-Context-It

- 이 패턴은 코드의 행동을 설명하는 테스트 코드를 작성한다.

- BDD 테스트 코드 패턴으로 알려진 `Given-When-Then`과 비슷하지만 미묘하게 다르다.

- `Describe-Context-It`은 상황을 설명하기보단 테스트 대상을 주인공 삼아 행동을 더 섬세하게 설명 (유저 중심)

`Describe`

- 설명할 테스트 대상을 명시. 테스트 대상이 되는 클래스, 메소드 이름을 명시.

`Context`

{ Green } 단- 테스트의 대상이 놓인 상황을 설명. 테스트할 메소드에 입력할 파라미터를 설명.

`It`

- 테스트 대상의 행동을 설명. 테스트 대상 메소드가 무엇을 리턴하는지 설명.

```text
사용법

- 영어로 Context 문을 작성할 때는 반드시 with 또는 when으로 시작
- It 구문은 It returns true, It responses 404와 같이 심플하게 설명
```

```text
장점

- 코드의 계층화 (대상 - 상황 - 행동)
- 테스트 코드를 추가하거나 읽을 때 스코프 범위만 신경쓰면 된다.
- 빠뜨린 테스트 코드를 찾기 쉽다.
```

### parameterized in JEST

```js
function areAnagrams(first, second) {
  const counter = {};
  for (const ch of first) {
    counter[ch] = (counter[ch] || 0) + 1;
  }
  for (const ch of second) {
    counter[ch] = (counter[ch] || 0) - 1;
  }

  return Object.values(counter).every((cnt) => cnt == 0);
}

descirbe('에너그램출력', () => {
  context('car and bike are not anagrams', () => {
    expect(areAnagrams('car', 'bike')).toBe(false);
  });
});

descirbe('에너그램출력', () => {
  context('car and arc are anagrams', () => {
    expect(areAnagrams('car', 'arc')).toBe(true);
  });
});

descirbe('에너그램출력', () => {
  context('cat and dog are not anagrams', () => {
    expect(areAnagrams('cat', 'dog')).toBe(false);
  });
});

descirbe('에너그램출력', () => {
  context('cat and act are not anagrams', () => {
    expect(areAnagrams('cat', 'act')).toBe(true);
  });
});
```

너무 귀찮다. 이걸 파라미터화 하자!!

### test.each() 함수

```js
test.each([
  ['cat', 'bike', false],
  ['car', 'arc', true],
  ['cat', 'dog', false],
  ['cat', 'act', true],
])('areAnagrams(%s, %s) returns %s', (first, second, expected) => {
  expect(areAnagrams(first, second)).toBe(expected);
});
```

- 테스트 데이터를 2차원 배열에 담에서 `test.each()` 함수의 인자로 넘기면, 배열을 루프 돌면서 각 테스트 데이터를 대상으로 테스트 함수를 호출

### describe.each() 함수

```js
describe('areAnagrams()', () => {
  it.each([
    ['cat', 'bike', false],
    ['car', 'arc', true],
    ['cat', 'dog', false],
    ['cat', 'act', true],
  ])('areAnagrams(%s, %s) returns %s', (first, second, expected) => {
    expect(areAnagrams(first, second)).toBe(expected);
  });
});
```

- `describe.each()` 사용 함수는 여러 테스트 함수를 여러 테스트 데이터를 대상으로 실행해야할 때 사용

```js
function areAnagrams(
  first,
  second,
  options = { ignoreCase: false, ignoreSpaces: false }
) {
  if (options.ignoreCase) {
    first = first.toLowerCase();
    second = second.toLowerCase();
  }

  if (options.ignoreSpaces) {
    first = first.replace(/ /g, '');
    second = second.replace(/ /g, '');
  }

  const counter = {};
  for (const ch of first) {
    counter[ch] = (counter[ch] || 0) + 1;
  }
  for (const ch of second) {
    counter[ch] = (counter[ch] || 0) - 1;
  }

  return Object.values(counter).every((cnt) => cnt == 0);
}
```

```js
//example;

describe.each([
  ['Cat', 'Act'],
  ['Save', 'Vase'],
  ['Elbow', 'Below'],
])('areAnagrams(%s, %s)', (first, second) => {
  it('return true with ignoreCase option', () => {
    expect(areAnagrams(first, second, { ignoreCase: true })).toBe(true);
  });

  it('return false without ignoreCase option', () => {
    expect(areAnagrams(first, second)).toBe(false);
  });
});
```

## Given-When-Then

[Given-When-Then](https://martinfowler.com/bliki/GivenWhenThen.html)

- 테스트를 표현하는 스타일

- 핵심 아이디어는 시나리오(또는 테스트) 작성을 세 가지 섹션

  - Given : 시나리오에서 지정하는 동작을 시작하기 전의 상태를 설명
  - When : 사용자가 지정하는 동작
  - Then : 지정된 동작으로 인해 예상되는 변경 사항을 설명
