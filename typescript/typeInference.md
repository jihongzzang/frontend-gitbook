# Type Inference

## 타입 추론

### 타입스크립트가 코드를 해석해 나가는 동작을 의미

```ts
let x = 3;

x에 대한 타입을 따로 지정하지 않더라도 일단 x는 number로 간주

변수를 선언하거나 초기화 할 때 타입이 추론
```

- 타입은 보통 몇 개의 표현식(코드)을 바탕으로 타입을 추론

- 타입스크립트에서 타입을 추론하는 또 하나의 방식은 바로 문맥상으로 타입을 결정

```ts
window.onmousedown = function (mouseEvent) {
  console.log(mouseEvent.button); //<- OK
  console.log(mouseEvent.hi); //<- Error!
};

타입스크립트 검사기 관점에서 보면

window.onmousedown에 할당되는 함수의 타입을 추론하기 위해 window.onmousedown 타입을 검사

함수의 타입이 마우스 이벤트와 연관이 있다고 추론

mouseEvent 인자에 button 속성은 있지만 hi 속성은 없다고 결론
```

## 타입스크립트의 타입 체킹

### 타입 체킹에 있어서 타입스크립트의 지향점은 타입 체크는 값의 형태에 기반

```text
Duck Typing : 객체의 변수 및 메서드의 집합이 객체의 타입을 결정하는 것을 의미. 동적 타이핑의 한 종류

Structural Subtyping : 객체의 실제 구조나 정의에 따라 타입을 결정하는 것을 의미
```
