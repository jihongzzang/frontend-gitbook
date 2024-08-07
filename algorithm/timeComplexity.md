# 시간 복잡도

- 입력크기에 대한 연산 횟수의 상한.

[인프런 강의](https://www.inflearn.com/course/lecture?courseSlug=cpp-%EC%BD%94%EB%94%A9%ED%85%8C%EC%8A%A4%ED%8A%B8-%ED%95%A9%EA%B2%A9&unitId=225757)를 보고 정리 한 내용입니다.

[코딩 테스트 합격자 되기: 자바스크립트 편](https://product.kyobobook.co.kr/detail/S000213641007)

## 알고리즘

알고리즘은 특정 문제를 해결하거나 작업을 수행하기 위한 명확하고 순차적인 단계들의 집합

코드로 구현되어 원하는 결과를 얻기 위한 논리적인 과정

```text
1. 변하지않는 명확한 작업단계를 가져야 한다.

2. 단계별 명확한 다음 단계를 가져야 한다.

3. 구현할 수 있고 실용적이어야 한다.

4. 정의된 입력을 받아들일 수 있어야 한다.

5. 답으로 출력을 내보낼 수 있어야 한다.

6. 특정 수의 작업 이후에 정지해야 한다.

7. 정의된 일벽들에 일반적으로 적용할 수 있어야 한다.
```

## 알고리즘 성능 측정법 및 시간 복잡도 개선

알고리즘의 성능 측정에는 크게 두가지가 있다.

`시간 복잡도: 알고리즘이 실행되는 데 걸리는 시간`

- 시간 복잡도는 입력 크기에 따라 알고리즘의 실행 시간이 어떻게 변하는지를 나타낸다.

  - 예를들어 책장에 꽂혀 있는 5권의 책을 정리해야 한다면 이때 입력크기는 5

- 크게 최선, 평균, 최악의 경우가 존재한다. (보통은 최악의 경우를 기준으로 시간복잡도를 따진다.)

- 코드가 동일하면 연산횟수는 모두 동일.

- 입력값에 따라 연산 횟수가 일정하지 않은 경우 => 최악의 경우

`공간 복잡도: 알고리즘이 사용하는 메모리의 양`

## 시간 복잡도를 빅오 표기법으로 표기하기

- 일반적으로 연산 횟수는 `n^2 + 3n + 5`로 정의 할수 있음.

![img](/algorithm/bigograph.png)

```text
O(1): 상수 시간 (입력 크기와 관계없이 일정한 시간)

O(log n): 로그 시간 (ex: 이진 검색)

O(n): 선형 시간 (입력 크기에 비례) (ex: 1차원 배열)

O(n*m) (ex: 2차원 배열)

O(nlogn): 선형 로그 시간 (ex 효율적인 정렬 알고리즘들)

O(n^2): 제곱 시간 (ex: 중첩 반복문)
```

## 접근적 표기법 (최고차항만을 남기기)

- 수학적인 부분인데 극한이라는 개념을 활용하면 n이 굉장히 큰 수로 커진다고 가정하면 실제로 최고차항을 제외한 나머지는 차이를 거의 보이지 않음

## 점근적 표기법을 코딩테스트에 활용하기

- 실제로 제일 중요한데 우리가 어떤 문제를 해결할떄 항상 어느정도의 입력값까지 들어갈지를 측정해야함. 측정한 후에는 입력값의 크기에 따라 자료구조 선택을 명확히 할 수 있다.

```text
시간 복잡도 / 최대 연산횟수

O(n!)  10

O(2^n)  20-25

O(n^3)  200-300

O(n^2)  3000-5000

O(nlong)  100만

O(n)  1000~2000만

O(logn)  10억

```

- 크게 요약하면

1000000 이하면 o(n)까지는 괜찮음

100000 이하면 o(nlogn)정도까지 가능

1000 이하면 o(n^2)도 가능

```js
// O(1) 예시
function getFirst(arr) {
  return arr[0];
}

// O(n) 예시
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return i;
  }
  return -1;
}

// O(n log n) 예시 (quick sort)
function quickSort(arr) {
  if (arr.length <= 1) return arr;

  const pivot = arr[0];

  const left = arr.filter((x, i) => i > 0 && x < pivot);

  const right = arr.filter((x) => x >= pivot);

  return [...quickSort(left), pivot, ...quickSort(right)];
}
```

## 알고리즘 최적화 팁

- 불필요한 반복문 줄이기

- 적절한 자료구조 선택하기 (예: 검색이 많다면 Set이나 Map 사용)

- 분할 정복, 동적 프로그래밍 등의 기법 활용하기

## 결론

- 본격적인 알고리즘을 공부하기전에 시간복잡도에 대한 개념을 공부하는 것은 문제를 해결하는데 있어서 출발점에서 어떠한 도구들을 들고가야하는지 판단하는 중요한 적도가 된다.
