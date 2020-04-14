# Redux 사용하기

> TypeScript 환경에서 Redux를 사용하는 방법을 다뤄봅니다.

## 1. 프로젝트 만들고 라이브러리 설치

redux의 경우 자체적으로 TypeScript를 지원합니다. 하지만 react-redux의 경우 그렇지 않기 떄문에 패키지명 앞에 `@types`을 붙인 패키지를 설치해주어야 합니다. `@types`는 TypeScript 미지원 라이브러리에 TypeScript 지원을 받을 수 있게 해주는 써드파티 라이브러리입니다.

```zsh
$ npx create-react-app sample --typescript
$ cd sample
$ yarn add redux react-redux @types/react-redux
```

## 2. 리덕스 모듈 작성

#### 액션 타입 선언

type을 선언 할 때에는 다음과 같이 문자열 뒤에 `as const`라는 키워드를 붙여야합니다.

```TSX
const INCREASE = 'counter/INCREASE' as const;
const DECREASE = 'counter/DECREASE' as const;
const INCRESE_BY = 'counter/INCREASE_BY' as const;
```

`as const`는 **const assertions**라는 TypeScript입니다. 이 문법을 사용하면 type의 TypeScript 타입이 `string`이 되지 않고 실제값을 가르키게 됩니다.

#### 액션 생성 함수

액션 생성 함수를 작성 할 떄에는 `function` 키워드를 사용해도 되고, 화살표 함수 문법을 사용해도 됩니다. 화살표 함수 문법을 사용하면 `return`을 생략 할 수 있습니다.

```TSX
export const increase = () => ({ type: INCREASE });
export const decrease = () => ({ type: DECREASE });
export const increaseBy = (diff: number) => ({
  type: INCREASE_BY,
  payload: diff
});
```

#### 액션 객체들에 대한 type 준비하기

리듀서를 작성 할 떄 action 파라미터의 타입을 설정하기 위해서 우리가 만든 모든 액션들의 TypeScript 타입을 준비해주어야 합니다. 여기서 사용하는 `ResturnType`은 함수에서 반환하는 타입을 가져올 수 있게 해주는 유틸 타입입니다.

```TSX
type CounterAction =
  | ReturnType<typeof increase>
  | ReturnType<typeof decrease>
  | ReturnType<typeof increaseBy>
```

#### 상태의 타입과 상태의 초깃값 선언

```TSX
type CounterState = {
  count: number;
}

const initialState: CounterState = {
  count: 0
};
```

#### 리듀서 작성하기

```JSX
function counter(state: CounterState = initialState, action: CounterAction) {
  switch (action.type) {
    case INCREASE:
      return { count: state.count + 1 };
    case DECREASE:
      return { count: state.count - 1 };
    case INCREASE_BY:
      return { count: state.count + action.payload };
    default:
      return state;
  }
}

export default counter;
```

## 3. 프로젝트에 리덕스 적용하기

지금은 리듀서가 하나뿐이지만, 추후 다른 리듀서를 더 만들 것이므로 루트 리듀서를 만들어 주겠습니다. `RootState` 라는 타입을 만들어서 내보내주어야 합니다. 이 타입은 추후 우리가 컨테이너 컴포넌트를 만들게 될 때 스토어에서 관리하고 있는 상태를 조회하기 위해 `useSelector`를 사용할 때 필요합니다.

```TSX
import { combineReducers } from 'redux';
import counter from './counter';

const rootReducer = combineReducers({
  counter,
});

export default rootReducer;

export type RootState = ReturnType<typeof rootReduxer>;
```

Provider 컴포넌트를 사용하여 리액트 프로젝트에 리덕스를 적용합니다.

```JSX
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import rootReducer from './modules';

const store = createStore(rootReducer);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root'),
);
```

## 4. 컨테이너 컴포넌트 만들기

```JSX
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { RootState } from '../modules';
import { increase, decrease, increaseBy } from '../modules/counter';
import Counter from '../components/Counter';

function CounterContainer() {
  const count = useSelector((state: RootState) => state.counter.count);
  const dispatch = useDispatch();

  const onIncrease = () => {
    dispatch(increase());
  }

  const onDecrease = () => {
    dispatch(decrease());
  }

  const onIncreaseBy = (diff: number) => {
    dispatch(increaseBy(diff));
  }

  return (
    <Counter
      count={count}
      onIncrease={onIncrease}
      onDecrease={onDecrease}
      onIncreaseBy={onIncreaseBy}
    />
  )
}

export default CounterContainer;
```

## 5. Custom Hooks 사용

```JSX
import { useSelector, useDispatch } from 'react-redux';
import { RootState } from '../modules';
import { increase, decrease, increaseBy } from '../modules/counter';
import { useCallback } from 'react';

export default function useCounter() {
  const count = useSelector((state: RootState) => state.counter.count);
  const dispatch = useDispatch();

  const onIncrease = useCallback(() => dispatch(increase()), [dispatch]);
  const onDecrease = useCallback(() => dispatch(decrease()), [dispatch]);
  const onIncreaseBy = useCallback(
    (diff: number) => dispatch(increaseBy(diff)),
    [dispatch],
  );
}

return {
  count,
  onIncrease,
  onDecrease,
  onIncreaseBy,
}
```

## 6. typesafe-actions로 리덕스 모듈 리팩토링하기

`redux-actions`는 Typescript 환경에서 사용하기에는 적합하지 않습니다. 공식적으로 Typescript 지원을 해주는 것이 아니기 때문에 래퍼런스도 별로 없습니다.

```zsh
$ npm install typesafe-actions
```

#### 필요한 함수와 타입 import 하기

```TSX
import {
  createStandardAction,
  ActionType,
  createReducer,
} from 'typescript-actions'
```

#### 액션 type 선언의 as const 지우기

```JSX
const ICREASE = 'counter/INCREASE';
const DECREASE = 'counter/DECREASE';
const INCREASE_BY = 'counter/INCREASE_BY';
```

#### 액션 함수 생성

액션의 페이로드로 들어가는 값은 Generic을 사용하여 정해줄 수 있으며, 만약 앤션의 페이로드에 아무것도 필요 없다면 Generic을 생략하면 됩니다.

```JSX
export const increase = createStandardAction(INCREASE);
export const decrease = createStandardAction(DECREASE);
export const increaseBy = createStandardAction(INCREASE_BY)<number>();
```

가끔은 액션 생성 함수로 파라미터로 넣어주는 값ㄷ과 액션의 페이로드 값이 와변히 일치하지 않을 때도 있습니다.

```JSX
const createItem = (name: string) => ({ type: CREATE_ITEM, payload: { id: nanoid(), name }});
```

위 코드처럼, id 값을 nanoid같은 라이브러리를 사용하여 고유 값을 생성하여 넣어주고 싶을 떄도 있을 겁니다. 그럴 때에는 다음과 같이 작성하면 됩니다.

```JSX
const createItem = createStandardAction(CREATE_ITEM).map(name => ({ payload: { id: nanoid(), name }}));
```

#### 액션의 객체 타입 만들기

이전에 우리가 액션들의 객체 타입을 만들 때 `ReturnType` 유틸 타입을 선언했습니다.

```JSX
ReturnType<typeof increaseBy>
```

typesafe-actions에 들어있는 `ActionType` 유틸 타입을 사용하면 액션들의 객체 타입을 맏느는 작업을 더욱 짧은 코드로 작성 할 수 있습니다.

```JSX
const actions = { increase, decrease, increaseBy };
type CounterAction = ActionType<typeof actions>
```

#### createReducer로 리듀서 만들기

`switch/case` 문이 아닌 object map 형태로 구현 할 수 있어서 코드가 더욱 간결해집니다.s

```JSX
const counter = createReducer<CounterState, CounterAction>(initialState, {
  [INCREASE]: state => ({ count: state.count + 1 }),
  [DECREASE]: state => ({ count: state.count - 1 }),
  [INCREASE_BY]: (state, action) => ({ count: state.count + action.payload }),
})
```

#### 리듀서 메서드 체이닝 방식을 통해 구현하기

`createReducer`를 사용 할 때 우리가 방금 했었던 것 처럼 함수들로 이루어진 object map 형태로 리듀서를 규현 할 수도 있고, 메서드 체이닝 방식을 통해서 구현할 수도 있습니다.

```JSX
const counter = createReducer<CounterState, CounterAction>(initialState)
  .handleAction(INCREASE, state => ({ count: state.count + 1 }))
  .handleAction(DECREASE, state => ({ count: state.count - 1 }))
  .handleAction(INCREASE_BY, (state, action) => ({
    count: state.count + action.payload
  }))
```
