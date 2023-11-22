# External Store

- 응집도

  - 하나의 클래스가 하나의 기능을 온전히 순도 높게 담당하고 있는 정도

- 결합도

  - 클래스간의 서로 다른 책임들이 얽혀 있는 상호 의존도의 정도

- 프론트엔드 생태계에는 여러가지 상태들이 존재한다. `데이터의 상태`, `UI의 상태`, `서버응답의 상태`... 등등
  웹을 이루는 모든 요소들이 상태하나의 상태라고 할 수 있고, 나는 크게 변하는 것과 변하지 않는것으로 나누어 진다고 생각한다.
  위에 정의한 두 단어처럼 결합이 엄청 쌔고 응집이 낮은 코드 보다는 응집되어있지만 서로 얽혀있는 정도가 낮은 코드란 무엇일까?

## 관심사의 분리

[관심사의 분리 보러가기](https://s-organization-196.gitbook.io/frontend-megatera/react/react_deep#soc)

[SRP 보러가기](https://s-organization-196.gitbook.io/frontend-megatera/react/react_component#srp)

실제로 하나의 모듈에서 여러 기능을 할 수 있으면 어떻게 보면 좋지 않을까?
그러면 유지보수 할때도 거기만 뜯어고치면 되고 편한데 `뭐하러 나누지?` 이런 생각을 과거에는 했었다.

만들고자 하는 서비스가 굉장히 간단하다면(단순 정적페이지, no action, 절대로 변하지않음) 하지 않아도 된다라고 생각한다.
하지만 시중의 서비스나 IT 제품들은 시시각각으로 변하고 바꿔야하고 우리는 이러한 변화에 더 효율적으로 대처해야한다.

=> 그렇기에 최대한 단일 책임을 지는 모듈들이 필요하다라고 생각한다.
=> 결국 이말은 내가 잘 나누어둔 모듈들은 최대한 적은 목적을 가지고 그 목적과 부합하지 않는 상황이 생겼을떄 고치면 된다.

```text
추가로 내가 현업에서 느꼈던 좋았던 점은
하나의 기능에서 모든걸 관리한다고 유지보수가 빠르지않다. 코드가 길어지면 현저히 가독성이 떨어지고 머리가 복잡해진다.
잘 나누어진 모듈들은 상황에 유연하게 대처가 가능하다. (유지보수적인 측면에서도 압도적으로 빠르다고 생각한다.)
```

- 소프트웨어

  - 코드로 구성된 무형의 제품
  - 코드로 이루어져있기 때문에 상대적으로 수정이 쉽다.
  - 수정하기 위해 일부만 수정하면 동작을 변경할 수 있다.
  - 잘못 설계되어서 바꾸기 힘든 상황이라면 이는 실체를 가진 제품과 다를 바 없다.

- 이번 `Section` 에서 다루고자 하는 내용들은 조금 더 효율적으로 나눌 수 있는 방식이 무엇인지에 대해 공부해보고자 한다.

## Layered Architecture (계층화된 아키텍쳐)

[Layered Architecture 글](https://cio-wiki.org/wiki/Layered_Architecture)

[Layered Architecture 또다른 글](https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/ch01.html)

- 애플리케이션을 각각 고유한 책임과 기능을 갖는 별도의 계층 또는 계층으로 구성하는 소프트웨어 아키텍처 패턴
- 계층을 구성햐는 레이어 숫자나 각 레이어의 유형을 명시하고 있지는 않지만, 일반적인 경우 아래와 같은 유형의 4개의 레이어로 구분한다.

`Presentaion`

- 유저 인터페이스 레이어라고도 하며, 유저가 애플리케이션과 직접 상호 작용하는 레이어
- 비즈니스 로직이 어떻게 수행되는지 알 필요가 없다.

`Business`

- 비즈니스 로직을 수행하는 것을 주 관심사로 둔다.
- 화면에 데이터를 출력하는 방법이나 혹은 데이터를 어디서, 어떻게 가져오는지에 대한 내용은 알고있지 않다.
- `Persistence Layer`에서 데이터를 가져와 비즈니스 로직을 수행하고 그 결과를 `Presentation Layer`로 전달
- 대표적인 구성요소는 `Service` 와 `Domain Model`

`Persistence`

- 어플리케이션의 영속성을 구현하기 위한 레이어
- 데이터 출처와 그 데이터를 가져오고 다루는 것을 주 관심사로 둔다.

`DataBase`

- `MySQL`, `MariaDB`, `PostgreSQL`, `MongoDB` 등 데이터베이스가 위치한 계층을 의미한다.

`Infrasturcture`

- `logging`, `caching`, `networking`, `security`과 같은 서비스를 제공하여 다른 모든 계층을 지원

![img](/externalStore/layerdarchitecture.png)

- 장점

  - 별도의 팀에서 다양한 레이어를 동시에 개발할 수 있다.
  - 한 레이어의 변경 사항은 다른 레이어에 영향을 주지 않으므로 새 기능을 업데이트하거나 추가하기가 더 쉽다.
  - 레이어는 잠재적으로 여러 프로젝트에서 재사용이 가능

- 단점

  - 데이터가 통과해야 하는 각 계층은 데이터 전송에 대기 시간을 추가한다.
  - 계층화된 아키텍처는 단순한 애플리케이션에서 과도하고 불필요하게 복잡할 수 있다.
  - 아키텍처에 지나치게 의존하면 유연성과 혁신이 부족해질 수 있다.

- 싱크홀 안티패턴

  - 특정 레이어가 아무런 로직도 수행하지 않고 들어온 요청을 그대로 다시 하위 레이어로 내보내는 경우를 의미!! 너무 많아지면 제대로된 설계가 아님.

- 아키텍쳐는 문제를 해결하는 한가지 방법이다. 각자 구현하고자 하는 제품에 맞춰서 잘 활용해서 더 좋은 결과물을 도출하는 방법론이지 너무 이것에 얽매이진 말자. 방법론은 방법론일뿐 중요한것은 어떤식으로 잘 활용할 것인가!!

- 그렇다면 조금 큰 관점에서 프론트엔드로는 `UI` - `화면적 Bussiness` - `통신하는 부분` 이런식으로도 나눌 수 있다라고 생각한다.

## Flux Architecture

[Flux](https://haruair.github.io/flux/docs/overview.html)

[Redux Overview and Concepts](https://ko.redux.js.org/tutorials/essentials/part-1-overview-concepts/)

- Facebook(현 Meta)에서 MVC의 대안으로 내세운 아키텍처.
- `2-way binding`을 썼을 때 생길 수 있는 `Model`-`View`의 복잡한 관계를 겨냥해 명확히 `unidirectional data flow`를 강조

Flux => 양방향이 아니라 단방향으로 데이터가 흐름

View에서 상호작용을 할때 중앙의 `disaptcher`를 통해 action을 전파,

데이터와 비즈니스 로직을 가지고 있는 Store는 action이 전파되면 action에 옇양이 있는 모든 view를 갱신

view가 어떤 방식으로 갱신해야 되는지는 알필요가 없이 데이터변경이 가능

![img](/externalStore/flux.png)

![img](/externalStore/flux_action.png)

```text
Action → 이벤트/메시지 같은 객체.

Dispatcher → (여러) Store로 Action을 전달. 메시지 브로커와 유사하다.

Store (여러 개) → 받은 Action에 따라 상태를 변경. 상태 변경을 알림.

View → Store의 상태를 반영.

- Redux는 단일 Store를 사용함으로써 좀 더 단순한 그림을 제안한다.

Action

Store → dispatch를 통해 Action을 받고, 사용자가 정의한 reducer를 통해 State를 변경

View → State를 반영.
```
