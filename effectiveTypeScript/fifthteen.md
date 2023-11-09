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
