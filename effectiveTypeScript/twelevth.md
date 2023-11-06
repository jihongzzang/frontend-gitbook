# 6장

## 타입 선언과 @types

### Item 45 devDependencies에 typescript와 @types 추가하기

- dependencies

  - 현재 프로젝트를 실행하는데 필수적인 라이브러리를 포함

- devDependencies

  - 현재 프로젝트를 개발하고 테스트 하는데 사용되지만 런타임에는 필요 없는 라이브러리들이 포함

- peeerDependencies

  - 런타임에 필요하긴 하지만, 의존성을 직접 관리하지 않는 라이브러리들이 포함 (플러그인)

- 타입스크립트를 시스템 레벨로 설치하지말고 `devDependencies`에 포함시키자.
- 런타임에 @types가 필요한 경우라면 별도의 작업이 필요할 수 있따.

### Item 46 타입 선언과 관련된 세 가지 버전 이해하기

- @types 의존성과 관련된 세 가지 버젼이 있다. 라이브러리 버전, @types 버전, 타입스크립트 버전

- 라이브러리를 업데이트 하는 경우, 해당 @types 역시 업데이트 해야한다.

- 타입선언을 라이브러리에 포함하는 것과 `DefinitelyTyped` 에 공개하는 것 사이의 장단점을 이해해야 한다.

- 타입스크립트로 작성된 라이브러리라면 타입 선언을 자체적으로 포함하고, 자바스크립트로 작성된 라이브러리라면 타입선언을
  DefinitelyTyped에 공개하는 것이 좋다.

### Item 47 공개 API에 등장하는 모든 타입을 익스포트하기

- 공개 메서드에 등장한 어떤 형태의 타입이든 익스포트하자.

### Item 48 API 주석에 TSDoc 사용하기

```ts
/**인사말을 생선한다. 결과는 보기 좋게 꾸며진다.*/
function greetJSDoc(name: string, title: string) {
  return `Hello ${title} ${name}`;
}
/**
 * 인사말을 생성합니다.
 * @param name 인사할 사람의 이름
 * @param title 그 사람의 칭호
 * @param returns 보기 좋은 형태의 인사말
 */
function greetFullTSDoc(name: string, title: string) {
  return `Hello ${title} ${name}`;
}

/** 특정 시간과 장소에서 수행된 측정*/
interface Measurement {
  /** 어디서에서 측정 되었나?*/
  position: Vector3D;
  /** 언제 측정되었는지?*/
  time: number;
  /** 측정된 운동량*/
  momentum: Vector3D;
}
```

- 익스포트된 함수, 클래스 타입에 주석을 달 때는 JSDoc/TSDoc 형태를 사용하자.

- @param, @returns 구문과 문서 서식을 위해 마크 다운을 사용할 수 있다.

- 주석에 타입 정보를 포함하면 안된다.

### Item 49 콜백에서 this에 대한 타입 제공하기

- JS에서 this는 매우 혼란스러운 기능이다. let이나 const로 선언된 변수가 렉시컬 스코프인 반면
  this는 다이나믹 스코프이기 때문이다.

- this는 전형적으로 객체의 현재 인스턴스를 참조하는 클래스에서 가장많이 사용된다.

```ts
class C {
  vals = [1, 2, 3];
  logSquares() {
    for (const val of this.vals) {
      console.log(val * val);
    }
  }
}

const c = new C();
C.logSquares(); //1 4 9

const method = c.logSquares;

method(); // undefined의 'vals' 속성을 읽을 수 없다.

method.call(c); //this 바인딩

//dom을 바인딩하기

document.querySelector('input')!.addEventListener('change', function (e) {
  console.log(this); // 이벤트가 발생한 input 엘리먼트를 출력
});

// this 바인딩이 콜백함수에서 쓰이는 경우

class ResetButton {
  render() {
    return makeButton({ text: 'Reset', onClick: this.onClick });
  }

  onClick() {
    alert(`Reset ${this}`);
  }
}

// ResetButton에서 onClick을 호출하면 바인딩 문제로 인해 정의되지 않을것임.

// 생성자에서 this를 바인딩 시키자.

class ResetButton {
  constructir() {
    this.onClick = this.onClick.bind(this);
  }
  render() {
    return makeButton({ text: 'Reset', onClick: this.onClick });
  }

  onClick() {
    alert(`Reset ${this}`);
  }
}

// onClick을 화살표 함수로 바꾸자
class ResetButton {
  render() {
    return makeButton({ text: 'Reset', onClick: this.onClick });
  }

  onClick = () => {
    alert(`Reset ${this}`);
  };
}
```

- this 바인딩은 자바스크립트의 동작이기 때문에, 타입스크립트 역시 그대로 모델링한다.

- 라이브러리에 this를 사용하는 콜백 함수가 있다면, 바인딩 문제를 고려해보자

```ts
function addKeyListner(
  el: HTMLElement,
  fn: (this: HTMLElement, e: KeyboardEvent) => void
) {
  el.addEventListener('keydown', (e) => {
    fn.call(el, e);
  });
}
```

- this의 사용법을 반드시 기억하자. 모르겠으면 자바스크립트로 다시 돌아가기
- 콜백 함수에서 this를 사용해야 한다면, 타입 정보를 명시하자
