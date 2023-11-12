# 7장

## 코드를 작성하고 실행하기

### Item 55 DOM 계층 구조 이해하기

- 브라우저에서 동작하는 프로젝트에 관련된 사항

```ts
// div의 경계를 넘어서 움직이는 경우를 추적한다 가정

function handleDrag(eDown: Event) {
  const targetEl = eDown.currentTarget;

  targetEl.classList.add('dragging');

  const dragStart = [eDown.clientX, eDown.clientY];

  const handleUp = (eUp: Event) => {
    targetEl.classList.remove('dragging');
    targetEl?.removeEventListener('mouseup', 'handleup');
    const dragEnd = [eUp.clientX, eUp.clientY];
    console.log(
      'dx,dy = ',
      [0, 1].map((i) => dragEnd[i] - dragStart[i])
    );
  };

  targetEl?.addEventListener('mouseup', handleUp);
}

const div = document.getElementById('surface');

div?.addEventListener('mousedown', handleDrag);

//수많은 오류가 존재

//HTMLElement는 Element의 서브타입

//Element는 Node의 서브타입

//Node는 EventTarget의 서브타입

//refactored

function addDragHandler(el: HTMLElement) {
  el.addEventListener('mousedown', (eDown) => {
    const dragStart = [eDown.clientX, eDown.clientY];

    const handleUp = (eUp: MouseEvent) => {
      el.classList.remove('dragging');
      el?.removeEventListener('mouseup', handleUp);
      const dragEnd = [eUp.clientX, eUp.clientY];
      console.log(
        'dx,dy = ',
        [0, 1].map((i) => dragEnd[i] - dragStart[i])
      );
    };
    el.addEventListener('mouseup', handleUp);
  });
}

const div = document.getElementById('surface');

if (div) {
  addDragHandler(div);
}
```

```text
DOM 계층의 타입들 예시

EventTarget => window, XMLHttpRequest

Node => document, Text, Comment

Element => HTMLElement, SVGElement

HTMLElement => <i>, <b>

HTMLButtonElement => <button>
```

- DOM에는 타입 계층 구조가 존재하고, DOM 타입은 타입스크립트에서 중요한 정보이다.
- `Node` , `Element` , `HTMLElement` , `EventTarget` 간의 차이점, 그리고 `Event`와 `MouseEvent`의 차이점을 알아야 한다.
- DOM 엘리먼트와 이벤트에는 구체적인 타입 정보를 사용하거나, 문맥 정보를 활용해야한다.

### Item 56 정보를 감추는 목적으로 private 사용하지 않기

- 자바스크립트는 클래스에 비공개 속성을 만들 수 없다.

- \_의 접두사는 그저 관례였었다.

```ts
class Foo {
  _private = 'secret123';
}

const f = new Foo();
f._private; //"secret123"
```

타입스크립트의 `public`, `protected`, `private` 같은 접근 제어자는 컴파일 후에는 제거된다.

```ts
class Diary {
  constructor() {
    this.secret = 'cheated on my English test';
  }
}

const diary = new Diart();
diary.secret;
```

- `private` 키워드는 사라지고 `secret`은 일반적인 속성이므로 접긍ㄴ이 가능하다.

즉, 정보를 감추기 위해 private를 사용하면 안됨. 만약 감추고 싶다면 클로저를 만들어야함

```ts
declare function hash(text: string): number;

class PasswordChecker {
  checkPassword: (password: string) => boolean;

  constructor(passwordHash: number) {
    this.checkPassword = (password: string) => {
      return hash(password) === passwordHash;
    };
  }
}
```

하지만 passwordHash를 생성자 외부에서 접근할 수 없기 때문에, 메서드 역시 생성자 내부에 정의 되어야 함.

- 또 하나의 선택지로 `#` keyword로 타입 체크와 런타임 모두에서 비공개로 만들 수 있음

```ts
class PasswordChecker {
  #passwordHash: number;

  constructor(passwordHash: number) {
    this.#passwordHash = passwordHash;
  }

  checkPassword(password: string) {
    return hash(password) === this.#passwordHash;
  }
}

const checker = new PasswordChecker(hash('s3cret'));

checker.checkPassword('secret'); //false
checker.checkPassword('s3cret'); //true
```

- `public`,`protected`,`private` 접근 제어자는 타입 시스템에서만 강제될 뿐이다.

- 데이터를 확실하게 감추고 싶다면 클로저를 사용하자.
