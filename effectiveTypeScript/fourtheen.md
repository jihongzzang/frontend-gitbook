# 7장

## 코드를 작성하고 실행하기

### Item 53 타입스크립트 기능보다는 ECMAScript 기능을 사용하기

- TS와 JS가 서로 업데이트 됨에 따라 타입 공간과 값 공간의 경계를 혼란스럽게 만들어 내는 몇가지 들이 존재한다.

- enum (열거형)

  - 몇며 값의 모음을 나타내기 위해 열거형을 사용.

  ```ts
  enum Flavor {
    VANILLA = 0,
    CHOCOLATE = 1,
    STRAWBERRY = 2,
  }

  let flavor = Flavor.CHOCOLATE;

  Flavor; // 자동완성 추천: VANILLA, CHOCOLATE, STRAWBERRY
  Flavor[0]; // VANILLA
  ```

  - 단순히 값을 나열하는 것보다 실수가 적고 명확하기 때문에 좋다.
  - 하지만 타입스크립트 열거형에서 숫자 열거형 외에 다른 숫자가 할당되면 위험
  - 상수 열거형은 보통의 열거형과 달리 런타임에 완전히 제거됨
  - `preserverConstEnums` 플래그를 설정하면 상수 열거형은 런타임 코드에 상수 열거형 정보를 유지해줌
  - 문자열 열거형은 타입 안정성과 투명성을 제공해주나 타입스크립트의 다른 타입과 달리 명목적 타이핑을 사용

```ts
enum Flavor {
  VANILLA = 'vanilla',
  CHOCOLATE = 'chocolatte',
  STRAWBERRY = 'strawberry',
}

let flavor = Flavor.CHOCOLATE;
flavor = 'strawberry';
```

- 자바스크립트와 타입스크립트에서 동작이 다르기 때문에 문자열 열거형보다는 리터럴 타입의 유니온을 사용하자

```ts
type Flavor = 'vanilla' | 'chocolate' | 'strawberry';
```

- 리터럴 타입의 유니온은 열거형 만큼 안전하며 자바스크립트와 호환되는 장점이 있다.

- 매개변수 속성

  - 일반적으로 클래스를 초기화 할때 속성을 할당하기 위해 생성자의 매개변수를 사용

  ```ts
  class Person {
    name: string;
    constructor(name: string) {
      this.name = name;
    }
  }

  class Person {
    constructor(public name: string) {}
    //public name => 매개변수 속성이라고 불린다.  (앞선 코드와 정확하게 동작)
  }

  // 타입스크립트 컴파일은 타입 제거가 이루어지므로 코드가 줄어들지만, 매개변수 속성은 코드가 늘어난다.
  // 매개변수 속성이 타입스크립트 관점에서는 사용되지 않는것 처럼 보인다.
  // 섞어서 사용하면 클래스의 설계가 혼란스러워진다.

  class Person {
    first: string;
    last: string;
    constructor(public name: string) {
      [this.first, this.last] = name.split(' ');
    }
  }

  // 세 가지 속성이 존재하나 name은 매개변수 속성에 있어서 일관성이 없다.
  // 클래스에 매개변수 속성만 존재한다면 인터페이스로 만들고 객체리터럴을 사용하자.
  ```

- 데코레이터

  - 데코레이터는 클래스, 메서드, 속성에 어노테이션을 붙이거나 기능을 추가하는 데 사용이 가능

    ```ts
    class Greeter {
      greeting: string;
      constructor(message: string) {
        this.greeting = message;
      }
      @logged
      greet() {
        return 'Hello, ' + this.greeting;
      }
    }

    function logged(t: any, n: string, d: PropertyDescriptor) {
      const fn = t[name];
      d.value = function () {
        console.log(`Calling ${name}`);
        return fn.apply(this, arguments);
      };
    }

    console.log(new Greeter('jihong').greet());
    ```

    - 데코리어트는 experimentalDecorators 속성을 설정하고 사용하자.

- 타입스크립트의 역할을 명확하게 하려면, 열거형, 매개변수 속성, 트리플 슬래시 임포트, 데코레이터는 사용하지 않는 것이 좋다.

### Item 54 객체를 순회하는 노하우

```ts
const obj = {
  one: '1',
  two: '2',
  thiree: '3',
};

for (const k in obj) {
  const v = obj[k]; // 인덱스 시그니처 없어서 ...any 에러
}

// kd의 타입을 구체적으로 명시해줘야함

let k: keyof typeof obj; // "1" | "2" | "3"

for (k in obj) {
  const v = obj[k]; // 정상
}
```

- 그렇다면 왜 k타입이 string 이었을까?

```ts
interface ABC {
  a: string;
  b: string;
  c: number;
}

function foo(abc: ABC) {
  for (const k in abc) {
    const v = abc[k]; // 인덱스 시그니처 없어서 ...any
  }
}

const x = { a: 'a', b: 'b', c: 2, d: new Date() };

foo(x); // 정상 (구조적 타이핑)

function foo(abc: ABC) {
  let k: keyof ABC;
  //let k: "a" | "b" | "c"

  for (const k in abc) {
    const v = abc[k]; //string | number
  }
}

//어떠한 타입이 될 수 있기 때문에 v는 잘못된 추론

function foo(abc: ABC) {
  for (const [k, v] of Object.entries(abc)) {
    k; //string
    v; //any
  }
}
```

- 객체를 다룰 때는 프로토타입과 다른 속성의 가능성을 염두해 둬야한다.

```ts
Object.prototype.z = 3; //절대로 이렇게 하면 안됨

const obj = { x: 1, y: 2 };

for (const k in obj) {
  console.log(k); // x,y,z
}
```

- 객체를 순회할 때, 키가 어떤 타입인지 정확히 파악하고 있다면 `keyof T` 와 `for-in` 루프를 사용하자.

- 함수의 매개변수로 쓰이는 객체에는 추가적인 키가 존재할 수 있다.

- 객체를 순회하며 키와 값을 얻는 가장 일반적인 방법은 `Object.entries`를 사용하는 것
