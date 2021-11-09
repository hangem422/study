# Recoil

## 1. 특징

1. 간단한 get/set 인터페이스로 사용할 수 있습니다.
2. Facebook에서 개발하기 때문에 새로운 React의 기능들과 호환이 가장 좋을것으로 기대됩니다.
3. 상태 정의는 증분 및 분산되므로 코드 분할이 가능합니다.

## 2. Atoms

- 상태의 단위이며, 엡데이트와 구독이 가능합니다.
- 업데이트되면 각각의 구독된 컴포넌트는 새로운 값을 반영하여 다시 렌더링 됩니다.
- 런타임에서 생성될 수도 있습니다.

```tsx
const fontSizeState = atom<number>({
  key: "fontSizeState",
  default: 14,
});
```

| 프로퍼티 | 설명                                                                                                |
| -------- | --------------------------------------------------------------------------------------------------- |
| key      | 고유한 키가 필요합니다. 디버깅, 지속성 및 모든 atoms의 map을 볼 수 있는 특정 고급 API에 사용됩니다. |
| default  | 기본값                                                                                              |

### 2.1 atomFamily(options)

- Atom Family는 atom 모음을 의미합니다. `atomFamily`를 호출하면 전달한 매개변수에 따라 `RecoilState`를 제공하는 함수를 반환합니다.
- 상태가 control의 인스턴스 또는 특정 요소와 연관된 경우, 각각의 요소들은 각각의 상태 atom을 가질 수 있습니다.
- `atomFamily`는 원시 타입, 배열, 또는 배열, 원시 타입을 갖는 객체를 허용합니다.

```tsx
const elementPositionStateFamily = atomFamily({
  key: "ElementPosition",
  default: [0, 0],
});

function ElementListItem({ elementID }) {
  const position = useRecoilValue(elementPositionStateFamily(elementID));
  return (
    <div>
      Element: {elementID}
      Position: {position}
    </div>
  );
}
```

- `atomFamily()`는 매개 변수값을 받아 실제 기본값을 반환하는 함수를 제공할 수 있습니다.

```tsx
const myAtomFamily = atomFamily({
  key: ‘MyAtom’,
  default: param => defaultBasedOnParam(param),
});

function defaultBasedOnParam(param) {
  /// ...
}
```

## 3. Selectors

- atoms나 다른 selectors를 입력으로 받아들이는 **순수 함수**입니다.
- 상위의 atoms 또는 selectors가 업데이트되면 하위의 selector 함수도 다시 실행됩니다.
- 컴포넌트들은 selectors를 atoms처럼 구독할 수 있으며 selectors가 변경되면 컴포넌트들도 다시 렌더링됩니다.

> Selectors는 상태를 기반으로 하는 파생 데이터를 계산하는데 사용됩니다. 최소한의 상태 집합만 atom에 저장하고 다른 모든 파생되는 데이터는 selectors에 명시한 함수를 통해 효율적으로 계산함으로써 쓸모없는 상태의 보존을 방지합니다.

```tsx
const proxySelector = selector({
  key: "ProxySelector",
  get: ({ get }) => ({ ...get(myAtom), extraField: "hi" }),
  set: ({ set }, newValue) => set(myAtom, newValue),
});
```

| 프로퍼티     | 설명                                                                                                                                                                                                                 |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| key          | 고유한 키가 필요합니다. 디버깅, 지속성 및 모든 atoms의 map을 볼 수 있는 특정 고급 API에 사용됩니다.                                                                                                                  |
| get          | 전달되는 `get` 인자를 통해 atoms와 다른 selectors에 접근할 수 있습니다. 다른 atoms나 selectors에 접근하면 자동으로 종속 관계가 생성되므로, 참조했던 다른 atoms나 selectors가 업데이트되면 이 함수도 다시 실행됩니다. |
| set (option) | 이 속성이 설정되면 selector는 쓰기 가능한 상태를 반환합니다. 매개변수로 콜백 객체와 입력 값이 전달됩니다. `set` 인자를 통해 atoms와 다른 selectors의 값을 설정할 수 있습니다.                                        |

### 3.1. selectorFamily(options)

- `get`, `set`, `selector`의 콜백을 매개변수로 전달할 수 있습니다
- 매개 변수에 사용할 수 있는 타입을 제한하게 됩니다. 원시 타입 혹은 직렬화 가능한 타입을 사용해주세요.

```tsx
const myNumberState = atom({
  key: "MyNumber",
  default: 2,
});

const myMultipliedState = selectorFamily({
  key: "MyMultipliedNumber",
  get:
    (multiplier) =>
    ({ get }) => {
      return get(myNumberState) * multiplier;
    },

  // optional set
  set:
    (multiplier) =>
    ({ set }, newValue) => {
      set(myNumberState, newValue / multiplier);
    },
});

function MyComponent() {
  const number = useRecoilValue(myNumberState);
  const multipliedNumber = useRecoilValue(myMultipliedState(100));

  return <div>...</div>;
}
```

## 4. 사용하기

### 4.1. RecoilRoot

- 값들을 갖는 원자 컨텍스트로, hooks를 사용하는 모든 구성 요소의 조상이어야 합니다.
- 여러개의 루트가 같이 존재할 수 있습니다. 원자는 각각의 루트 안에서 구별되는 값들을 가집니다.
- 만약 중첩된다면 가장 안쪽의 루트가 우선시 됩니다.

```tsx
import { RecoilRoot } from "recoil";

function App() {
  return (
    <RecoilRoot>
      <ComponentThatUsesRecoil />
    </RecoilRoot>
  );
}
```

| 프로퍼티                 | 타입                        | 설명                                                                                                                   |
| ------------------------ | --------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| initializeState (option) | `(MutableSnapshot => void)` | MutableSnapshot을 사용하여 RecoilRoot의 원자 상태를 초기화하는 옵션 함수입니다.                                        |
| override (option)        | `boolean`                   | 기본값은 `true`로 설정되어 있으며, 다른 RecoilRoot와 중첩된 경우에 해당 루트는 새로운 Recoil 범위(scope)를 생성합니다. |

### 4.2. useRecoilState(state)

- 기본값 대신 React Recoil 상태를 인수로 받는 다는 점만 빼면 useState() hook과 비슷합니다.
- 컴포넌트가 상태를 읽고 쓰려고 할 때에 권장합니다.

```tsx
import { atom, selector, useRecoilState } from "recoil";

const tempFahrenheit = atom({
  key: "tempFahrenheit",
  default: 32,
});

const tempCelsius = selector({
  key: "tempCelsius",
  get: ({ get }) => ((get(tempFahrenheit) - 32) * 5) / 9,
  set: ({ set }, newValue) => set(tempFahrenheit, (newValue * 9) / 5 + 32),
});

function TempCelsius() {
  const [tempF, setTempF] = useRecoilState(tempFahrenheit);
  const [tempC, setTempC] = useRecoilState(tempCelsius);
  // ...
}
```

### 4.3. useRecoilValue(state)

- 이 hook는 읽기 전용 상태와 쓰기 가능 상태에서 모두 동작합니다.
- 컴포넌트가 상태를 읽을 수만 있게 하고 싶을 때에 추천하는 hook입니다.

```tsx
import { atom, selector, useRecoilValue } from "recoil";

const namesState = atom({
  key: "namesState",
  default: ["", "Ella", "Chris", "", "Paul"],
});

const filteredNamesState = selector({
  key: "filteredNamesState",
  get: ({ get }) => get(namesState).filter((str) => str !== ""),
});

function NameDisplay() {
  const names = useRecoilValue(namesState);
  const filteredNames = useRecoilValue(filteredNamesState);
  // ...
}
```

### 4.4. useSetRecoilState(state)

- 구독하지 않고도 값을 설정하게 해줍니다.
- 컴포넌트가 상태에 읽지 않고 쓰기만 하려고 할 때 추천합니다

```tsx
import { atom, useSetRecoilState } from "recoil";

const namesState = atom({
  key: "namesState",
  default: ["Ella", "Chris", "Paul"],
});

function Form() {
  const setNamesState = useSetRecoilState(namesState);
  // ...
}
```

### 4.5. useResetRecoilState(state)

- 구독하지 않고도 상태를 기본값으로 리셋할 수 있게 해줍니다.

```tsx
import { todoListState } from "../atoms/todoListState";

const TodoResetButton = () => {
  const resetList = useResetRecoilState(todoListState);
  return <button onClick={resetList}>Reset</button>;
};
```

## 5. 비동기 데이터 쿼리

- Selector는 비동기 데이터를 Recoil의 데이터 플로우 그래프로 포함하는 방법 중 하나로 사용될 수 있습니다.
- Selector `get`콜백에서 나온 값 그 자체 대신 프로미스를 리턴하면 인터페이스는 정확하게 그대로 유지됩니다.
- 결과는 쿼리가 유니크한 인풋이 있을 때에만 실행되도록 캐시됩니다.

```tsx
const currentUserNameQuery = selector({
  key: "CurrentUserName",
  get: async ({ get }) => {
    const response = await myDBQuery({
      userID: get(currentUserIDState),
    });
    return response.name;
  },
});

function CurrentUserInfo() {
  const userName = useRecoilValue(currentUserNameQuery);
  return <div>{userName}</div>;
}
```

- Recoil은 보류중인 데이터를 다루기 위해 React Suspense와 함께 동작하도록 디자인되어 있습니다.
- 컴포넌트를 Suspense의 경계로 감싸는 것으로 아직 보류중인 하위 항목들을 잡아내고 대체하기 위한 UI를 렌더합니다.

```tsx
function MyApp() {
  return (
    <RecoilRoot>
      <React.Suspense fallback={<div>Loading...</div>}>
        <CurrentUserInfo />
      </React.Suspense>
    </RecoilRoot>
  );
}
```

### 5.1. 에러 처리하기

- Recoil selector는 컴포넌트에서 특정 값을 사용하려고 할 때에 어떤 에러가 생길지에 대한 에러를 던질 수 있습니다.
- 이는 React `<ErrorBoundary>`로 잡을 수 있습니다.

```tsx
const currentUserNameQuery = selector({
  key: "CurrentUserName",
  get: async ({ get }) => {
    const response = await myDBQuery({
      userID: get(currentUserIDState),
    });
    if (response.error) {
      throw response.error;
    }
    return response.name;
  },
});

function CurrentUserInfo() {
  const userName = useRecoilValue(currentUserNameQuery);
  return <div>{userName}</div>;
}

function MyApp() {
  return (
    <RecoilRoot>
      <ErrorBoundary>
        <React.Suspense fallback={<div>Loading...</div>}>
          <CurrentUserInfo />
        </React.Suspense>
      </ErrorBoundary>
    </RecoilRoot>
  );
}
```

### 5.2. noWait(state)

- 제공된 `atom` 혹은 `selector`의 현재 상태에 대한 `Loadable`객체를 반환하는 selector helper입니다.
- 에러가 존재하거나 종속이 아직 보류중인 경우에도, 에러를 발생시키지 않고 비동기 종속의 현재 상태를 가져올 수 있습니다.

```tsx
const myQuery = selector({
  key: "MyQuery",
  get: ({ get }) => {
    const loadable = get(noWait(dbQuerySelector));

    return {
      hasValue: { data: loadable.contents },
      hasError: { error: loadable.contents },
      loading: { data: "placeholder while loading" },
    }[loadable.state];
  },
});
```

### 5.3. waitForAll(dependencies)

- 여러 비동기 종속성을 동시에 평가할 수 있는 동시성(concurrency) helper입니다.

#### 배열을 사용한 동시성 예시

```tsx
const friendsInfoQuery = selector({
  key: "FriendsInfoQuery",
  get: ({ get }) => {
    const { friendList } = get(currentUserInfoQuery);
    const friends = get(waitForAll(friendList.map((friendID) => userInfoQuery(friendID))));
    return friends;
  },
});
```

#### 객체를 사용한 동시성 예시

```tsx
const customerInfoQuery = selectorFamily({
  key: "CustomerInfoQuery",
  get:
    (id) =>
    ({ get }) => {
      const { info, invoices, payments } = get(
        waitForAll({
          info: customerInfoQuery(id),
          invoices: invoicesQuery(id),
          payments: paymentsQuery(id),
        })
      );

      return {
        name: info.name,
        transactions: [...invoices, ...payments],
      };
    },
});
```

### 5.4. React Suspense를 사용하지 않은 비동기 쿼리

- 보류중인 비동기 selector를 다루기 위해서 React Suspense를 사용하지 않고 `useRecoilValueLoadable()`를 사용할 수 있습니다.

```tsx
function UserInfo({ userID }) {
  const userNameLoadable = useRecoilValueLoadable(userNameQuery(userID));
  switch (userNameLoadable.state) {
    case "hasValue":
      return <div>{userNameLoadable.contents}</div>;
    case "loading":
      return <div>Loading...</div>;
    case "hasError":
      throw userNameLoadable.contents;
  }
}
```

### 5.5. 쿼리 새로고침

- selector 평가 함수들은 주어진 인풋에 관해서 여러번 캐시되고 실행되더라도 idempotent(멱등)해야 합니다
- 어플리케이션의 생명주기 동안 결과과 다양하게 나올거라 예상하는 쿼리에 사용되면 안됨을 의미합니다.
- 하지만 변경가능한 데이터를 다루고자 한다면 몇 가지 패턴을 사용할 수 있습니다.

#### 요청 ID 사용하기

- Selector 평가는 인풋을 바탕으로 주어진 상태에 일관된 값을 제공해야합니다.
- 요청 ID를 패밀리 매개변수 혹은 쿼리에 대한 종속성으로 추가하고 계속 변경해줍니다.

```tsx
const userInfoQueryRequestIDState = atomFamily({
  key: "UserInfoQueryRequestID",
  default: 0,
});

const userInfoQuery = selectorFamily({
  key: "UserInfoQuery",
  get:
    (userID) =>
    async ({ get }) => {
      get(userInfoQueryRequestIDState(userID)); // Add request ID as a dependency
      const response = await myDBQuery({ userID });
      if (response.error) {
        throw response.error;
      }
      return response;
    },
});

function useRefreshUserInfo(userID) {
  const setUserInfoQueryRequestID = useSetRecoilState(userInfoQueryRequestIDState(userID));
  return () => {
    setUserInfoQueryRequestID((requestID) => requestID + 1);
  };
}

function CurrentUserInfo() {
  const currentUserID = useRecoilValue(currentUserIDState);
  const currentUserInfo = useRecoilValue(userInfoQuery(currentUserID));
  const refreshUserInfo = useRefreshUserInfo(currentUserID);

  return (
    <div>
      <h1>{currentUser.name}</h1>
      <button onClick={refreshUserInfo}>Refresh</button>
    </div>
  );
}
```
