# 연사자를 이용한 타입

## Union Type

- 유니온 타입(Union Type)이란 자바스크립트의 OR 연산자(||)와 같이 A이거나 B이다 라는 의미의 타입

```ts
function logText(text: string | number) {
  // ...
}

parameter는 string 또는 number이다.
```

```ts
// any를 사용하는 경우
function getAge(age: any) {
  age.toFixed(); // 에러 발생, age의 타입이 any로 추론되기 때문에 toFixed 메서드를 사용하지 못하는 경우도 있음
  return age;
}

// 유니온 타입을 사용하는 경우
function getAge(age: number | string) {
  if (typeof age === 'number') {
    age.toFixed(); // age의 타입이 `number`로 추론되기 때문에 정상동작
    return age;
  }

  if (typeof age === 'string') {
    return age;
  }

  return 'age must be number or string';
}
```

## Intersection Type

인터섹션 타입(Intersection Type)은 여러 타입을 모두 만족하는 하나의 타입을 의미

```ts
interface Person {
  name: string;
  age: number;
}

interface Developer {
  name: string;
  skill: number;
}
type Jihong = Person & Developer;

{
  name: string;
  age: number;
  skill: string;
}
```

```ts
interface Person {
  name: string;
  age: number;
}

interface Developer {
  name: string;
  skill: string;
}
function introduce(p: Person | Developer) {
  p.name; // O 정상 동작
  p.age; // X 타입 오류
  p.skill; // X 타입 오류
}

함수를 호출하는 시점에 p의 타입이 무엇이 올지 알 수 없기 때문에 둘다 들어있는 name만 정상적으로 동작한다. 이런경우에는 타입가드를 통해 타입의 범위를 좁혀야함
```
