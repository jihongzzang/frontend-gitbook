# 나만의 푸드 키오스크 만들기

[github](https://github.com/jihongzzang/frontend-survival-week05/tree/jihongzzang)

ContextAPI를 활용하여 작은 푸드 키오스크를 만들어보자.

조건

- 검색어로 식당을 필터링 할 수 있다.

- 메뉴를 선택 할 수 있다.

- 선택된 메뉴와 금액이 보여져야한다.

- 주문하기를 눌르면 영수증이 나오고 5초후에 사라진다.

![img](/contextAPI/foodkiosk.png)

우선 앱의 구조를 설명하자면

작은 `atoms`를 활용하여 `작은 단위의 ui`를 관리한다.

`provider`와 `reducer`를 통해 최대한 propsdrilling을 피하자.

```tsx
//APP.tsx
function App() {
  return (
    <ThemeProvider theme={theme}>
      <GlobalStyles />
      <FoodKioskAppProvider>
        <FookKioskAPP />
      </FoodKioskAppProvider>
    </ThemeProvider>
  );
}

// types.ts

// 최대한 하나의 api에서 받아오는 데이터와 dispatch에 관련된 타입은 한쪽에서 작성하는 것이 편하다고 생각해서 types.ts를 만들어주고 그곳에서 타입을 지정해주자.
export type CategoryType = '전체' | '중식' | '한식' | '일식';

export type MenuItem = {
  id: string;
  name: string;
  price: number;
};

export type ReceiptType = {
  id?: string;
  menu?: MenuItem[];
  totalPrice?: number;
};

export type ReceiptResponse = {
  receipt: ReceiptType;
};

export type RestaurantItem = {
  id: string;
  category: string;
  name: string;
  menu: MenuItem[];
};

export type RestaurantsItem = {
  restaurants: RestaurantItem[];
};

export type RenderType = 'none' | 'card' | 'notFound';

export interface State {
  restaurants: RestaurantItem[];
  category: CategoryType;
  query: string;
  receipt: ReceiptType;
  addedMenus: MenuItem[];
}

export type Action =
  | { type: 'fetch_data'; payload: RestaurantItem[] }
  | { type: 'update_category'; payload: CategoryType }
  | { type: 'search'; payload: string }
  | { type: 'add_menu'; payload: MenuItem }
  | { type: 'delete_menu'; payload: number }
  | { type: 'reset_menu'; payload: MenuItem[] }
  | { type: 'get_receipt'; payload: ReceiptType }
  | { type: 'delete_receipt'; payload: ReceiptType };

//reducer.ts

//처음에 바로 reducer를 작성하면서 애를 먹었지만 초기에 걸리는 공수에 비해 나중에 수정사항이 생겼을때는 압도적으로 빠르게 유지보수가 가능하다.
import { Action, State } from '../types';

const reducer = (state: State, action: Action): State => {
  switch (action.type) {
    case 'fetch_data':
      return {
        ...state,
        restaurants: action.payload,
      };

    case 'update_category':
      return {
        ...state,
        category: action.payload,
      };

    case 'search':
      return {
        ...state,
        query: action.payload,
      };

    case 'add_menu':
      return {
        ...state,
        addedMenus: [...state.addedMenus, action.payload],
      };

    case 'reset_menu':
      return {
        ...state,
        addedMenus: action.payload,
      };

    case 'delete_menu':
      return {
        ...state,
        addedMenus: [
          ...state.addedMenus.filter((_, index) => index !== action.payload),
        ],
      };

    case 'get_receipt':
      return {
        ...state,
        receipt: action.payload,
      };

    case 'delete_receipt':
      return {
        ...state,
        receipt: action.payload,
      };

    default:
      return state;
  }
};

//FoodKioskAPP.tsx
function FoodKioskAPP() {
  useInit(); // 초기 데이터를 관장하는 부분

  return <FilterableMenuContainer />;
}

//FilterableMenuContainer.tsx

function FilterableMenuContainer() {
  return (
    <Stack $direction='column'>
      <Header title='푸드코트 키오스크' />
      <LunchBasket title='점심 바구니' />
      <SearchMenu htmlFor='input-검색' placeholder='식당 이름' />
      <FilterCategory />
      <MenuTable />
      <OutputReceipt />
    </Stack>
  );
}
```

- 컴포넌트를 최대한 쪼개어보자 (하나의 책임만 할 수 있도록)

![img](/contextAPI/component.png)

- 내부의 커스텀 훅은 provider의 상태값을 가져오므로 최상위 계층에서 쓰지않고 필요한 곳에서 쓸 수 있다.

![img](/contextAPI/hooks.png)

- 테스트 코드

![img](/contextAPI/testscode.png)

개인적으로 공수에 비해 가장 좋다고 여겨졌던 부분은 테스트코드 관련 된 부분 인것 같다.

propsDrilling을 최소화하니 컴포넌트마다 어떤 부분들을 테스트해야하는지 명확해진다.

해보면서 느꼈던점은

**컴포넌트 단위테스트 => 이벤트의 결과를 예상하는 것보다 이벤트가 발생하는지를 테스트하는 것이 중요하다.**

```tsx
// ex) SearchMenuComponent Test
import { render, screen, fireEvent } from '@testing-library/react';

import SearchMenu from '../../src/components/FoodKioskApp/components/SearchMenu';

// 모킹해주는거 잊지말기
const handleChangeQuery = jest.fn();
jest.mock('../../src/components/FoodKioskApp/hooks/useInput', () => () => ({
  handleChangeQuery,
}));

describe('SearchMenu', () => {
  it('renders label and input', () => {
    render(<SearchMenu htmlFor='input-검색' placeholder='식당 이름' />);

    expect(screen.getByLabelText('input-검색')).toBeInTheDocument();
    expect(screen.getByPlaceholderText('식당 이름')).toBeInTheDocument();
  });
});

describe('SearchMenu Input Events', () => {
  beforeEach(() => {
    handleChangeQuery.mockClear();
  });

  it('renders label and input', () => {
    render(<SearchMenu htmlFor='input-검색' placeholder='식당 이름' />);

    //더 좋은 테스트 코드를 작성하기 위해서는 html의 attribute를 잘활용해야한다.
    const input = screen.getByPlaceholderText('식당 이름') as HTMLInputElement;

    fireEvent.change(input, { target: { value: '메' } });

    expect(input).toHaveValue('메');
  });
});

//ex) FilterCategory Test

import { render, screen, fireEvent } from '@testing-library/react';

import FilterCategory from '../../src/components/FoodKioskApp/components/FilterCategory';
import categories from '../../src/components/FoodKioskApp/constants/categories';

const handleClickCategory = jest.fn();

jest.mock(
  '../../src/components/FoodKioskApp/hooks/useChangeCategory',
  () => () => ({
    handleClickCategory,
  })
);

describe('FilterCategory', () => {
  it('renders all category buttons', () => {
    render(<FilterCategory />);

    categories.forEach((category) => {
      expect(screen.getByText(category)).toBeInTheDocument();
    });
  });
});

describe('FilterCategory Click Events', () => {
  beforeEach(() => {
    handleClickCategory.mockClear();
  });

  categories.forEach((category) => {
    it('calls handleClickCategory when a category is clicked', () => {
      render(<FilterCategory />);

      const categoryButton = screen.getByText(category);
      fireEvent.click(categoryButton);

      expect(handleClickCategory.mock.calls[0][0].target.value).toBe(category);
    });
  });
});
```

**중간의 비즈니스 로직은 => utils등을 통해 최대한 정제하는 함수들을 만들어주고 이것들을 테스트하자.**

```tsx
//filterBySearchQuery test

function filterBySearchQuery<T extends { name: string }>(
  list: T[],
  searchText: string
): T[] {
  return list.filter((item) => item.name.includes(searchText.trim()));
}

describe('메뉴판 필터링', () => {
  context('filterBySearchQuery()', () => {
    // each를 통해 한꺼번에 테스트 해보자.
    it.each([
      [filterBySearchQuery([...restaurants], '메가'), 1],
      [filterBySearchQuery([...restaurants], '감감'), 0],
    ])('filterBySearchQuery(%s, %s) returns %s', (params, expected) => {
      expect(params.length).toBe(expected);
    });
  });
});
```

**통과된 단위테스트들을 모아 E2E를 진행**

```tsx
Scenario('메뉴판 필터링', ({ I }) => {
  I.amOnPage('/');

  I.see('푸드코트 키오스크');

  I.see('메가반점');
  I.see('메리김밥');
  I.see('혹등고래카레');

  I.click('중식');
  I.see('짜장면');
  I.dontSee('김밥');

  I.click('한식');
  I.see('메리김밥');
  I.see('김밥');
  I.see('제육김밥');

  I.click('전체');

  I.fillField('검색', '혹등');
  I.see('기본카레');
  I.see('소시지카레');
  I.dontSee('메리김밥');

  I.fillField('검색', ' ');

  I.see('메가반점');
  I.see('메리김밥');
  I.see('혹등고래카레');
});

Scenario('음식 주문하기', ({ I }) => {
  I.amOnPage('/');

  I.see('푸드코트 키오스크');

  I.click({ name: '#짜장면' });
  I.click({ name: '#짬뽕' });
  I.click({ name: '#김밥' });
  I.click({ name: '#기본카레' });

  I.click('합계: 28,500원 주문');

  I.waitForText('주문번호');
  I.see('총 가격: 28,500원');

  I.wait(7);

  I.see('영수증 나오는 곳');
});
```

## 후기

시간이 지나고 나서 보니 리팩토링을 해야하는 부분들이 존재하는 것 같다.
우선 reducer와 provider를 조금더 작은 책임단위로 여러개로 쪼갤 수 있는지를 한번 파악해보자.
예를들면 전역 데이터 provider 영수증, 메뉴판, 선택된메뉴 provider로 더 분리시킬수 있지 않을까?
그렇게 되면 해당 맞는 부분에만 각각 provider들로 wrrapping 해주면 더 좋을 것 같다는 생각이 들었다.
이 구조역시도 처음엔 이렇게 바로짜기 힘들었고 머릿속으로 프롭스를 내려주면 어떤식으로 내려줄지에 대한 고민을 많이 하면서
간단하게 푸드키오스크를 만들어보았다.

같은 기능을 하는 앱이라도 매번 볼때마다 다른방식을 생각해보고는 하는데 이번주에는 이번 코드에 보완해서 더 나은 코드로 가도록 다시 만들어봐야지.
