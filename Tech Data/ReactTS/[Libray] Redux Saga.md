# Redux Saga

사이드 이팩트를 더 쉽게 관리하고 더 효과적으로 실행하며 더 쉽게 테스트하고 더 나은 에러 처리를 할 수 있게 만드는 것이 목표입니다.

## 1. 시작하기

```zsh
& npm install redux-saga @types/redux-saga
```

리덕스 사가에서 제공하는 `createSagaMiddleware()` 함수는 사가 미들웨어를 생성합니다. 이것을 리덕스 패키지에서 제공하는 `applyMiddleware()` 함수의 인자로 전할하여 리덕스 미들웨어로 추가할 수 있습니다. 그리고나서 미들웨어의 `run()` 함수로 루트 사가를 실행해주면 스토어 작업은 끝납니다.

```typescript
import { createStore, applyMiddleware } from "redux";
import rootReducer from "../reducers";
import createSagaMiddleware from "redux-saga";
import rootSaga from "../sagas";

const sagaMiddleware = createSagaMiddleware();
const configureStore = () => {
  const store = createStore(rootReducer, applyMiddleware(sagaMiddleware));
  sagaMiddleware.run(rootSaga);
  return store;
};

export default configureStore;
```

## 2. 비동기 작업 세분화

비동기를 세 단계로 나눠 액션과 액션 생성자를 정의합니다. 컴포넌트에서 이 액션 생성자로 패치 스토어로 디스패치하면 이를 리덕스 사가에서 잡아 낼 수 있습니다.

```typescript
export const FETCH_TEST_REQUEST = "FETCH_TEST_REQUEST";
export const FETCH_TEST_SUCCESS = "FETCH_TEST_SUCCESS";
export const FETCH_TEST_FAILURE = "FETCH_TEST_FAILURE";

export interface FetchTestAction {
  type: typeof FETCH_TEST_REQUEST;
}

export const fetchTest = (): FetchTestAction => ({
  type: FETCH_TEST_SUCCESS,
});
```

Saga는 액션을 구독하는 `Watcher`와 실제 작업을 수행하는 `Worker`의 구성을 따릅니다. 리덕스 사가 패키지는 사이드 에펙트를 다루는 몇가지 함수를 제공합니다. 이들 중 `takeLatest()` 함수는 스토어에 들어오는 액션을 보고 있다가 특정 액션만 잡아서 로직을 수행해주는 기능을 합니다. 이팩터 중에 `call()` 함수는 인자로 받은 함수를 실행해주는 역할을 합니다. 전달 받은 함수가 Promise를 반환하는 경우 Promise 처리가 끝날 때까지 재너레이터를 중지 시킵니다. Pormise가 resolve되면 그 값으로 제네레이터를 다시 시작하고 reject되면 제네레이터는 에러를 던지는 동작을 합니다. `put()` 함수는 액션을 스토어로 디스패티하는 역할을 합니다.

```typescript
import { takeLatest } from "redux-saga/effects";

export function* rootSaga() {
  takeLatest(FETCH_TEST_REQUEST, fetchTest$);
}

export function* fetchTest$() {
  try {
    const tests = yield call(fetchTest);
    yield put({ type: FETCH_TEST_SUCCESS, payload: tests });
  } catch (err) {
    // 샐패 로직
  }
}
```

여기까지가 사가 함수의 역할입니다. 불러온 데이터로 스토어 상태를 갱신하는 것은 리듀서의 몫 입니다. 이 재네레이터는 액션 객체를 인자로 받는데 미리 정의한 Action 인터페이스를 이용해 전달받은 액션 구조를 정확히 추적할 수 있다. 또한 비동기 제어를 담당하는 사가는 다양한 함수를 통해 마치 동기 코드처럼 관리할 수 있는데 이를 `이펙터`라고 부른다.

```typescript
export function* testFunction$(action: TetsAction) {
  const { payload } = action;
  if (payload) return;

  try {
    // call: 인자로 받은 함수를 실행시킵니다.
    const test = yield call(testFunction, payload);
    // put: 액션을 스토어로 디스패치합니다. (다이얼로그를 엽니다.)
    yield put({
      type: SHOW_DIALOG,
      payload: {
        type: "show",
        text: "Test가 성공했습니다.",
      },
    });
    // 액션 발행을 기다립니다. (확인 버튼을 누르기를 기다립니다.)
    yield take(CONFIRM_DIALOG);
    // 실행을 지연합니다. (3초 지연합니다.)
    yield delay(3000);
  } catch (error) {}
}
```

리덕스 사가 패키지는 사이드 이펙트를 다루는 몇가지 함수를 제공합니다. 최신 발행된 액션 타입만 감시하는 `takeLatest()` 이팩터와 달리 `takeEvery()`는 발행되는 모든 액션 타입을 감시합니다.

```typescript
import { takeEvery } from "redux-saga/effects";

export default function* rootSaga() {
  yield takeEvery(TEST_TYPE, testaction$);
}

export function* testAction$(action: TestActionType) {
  // ...
}
```

## 3. 사가 모듈화

액션이 많아질수록 사가 함수 객수도 늘어나는데 하나의 제네레이터로만 관리하면 한계가 있다.

```typescript
export default function* sample() {
  yield all([
    takeLatest(SAMPLE_TYPE_01, sampleAction01$),
    takeLatest(SAMPLE_TYPE_02, sampleAction02$),
  ]);
}
```

```typescript
export default function* app() {
  yield all([
    takeEvery(APP_TYPE_01, appAction01$),
    takeEvery((action: any) => {
      return action.type.endsWith("_FAILEURE");
    }, failAction$),
  ]);
}
```

```typescript
export default function* rootSaga() {
  yield all([fork(sample), fork(app)]);
}
```

## 4. 출처

- [리덕스 사가 사용하기 (타입스크립트 버전) - 김정환 블로그](https://jeonghwan-kim.github.io/dev/2019/07/22/react-saga-ts-1.html#)
