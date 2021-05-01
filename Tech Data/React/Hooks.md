# React Hooks

Hooks는 리액트 v16.8에 새로 도입된 기능으로서, 함수형 컴포넌트에서도 상태 관리를 할 수 있는 useState, 그리고 렌더링 직후 작업을 설정하는 useEffect 등의 기능을 제공하여 기존의 함수형 컴포넌트에서 할 수 없었던 다양한 작업을 할 수 있게 해줍니다.

## 1. useState

`useState`는 가장 기보적인 Hook으로서, 함수형 컴포넌트에서도 가변적인 상태를 지니고 있을 수 있게 해줍니다. `UseState`를 사용 할 땐 코드이 상단에 `import` 구문을 통하여 불러오고 다음과 같이 사용합니다.

```JSX
import React, { useState } from 'react';

const Counter = () => {
  const [ value, setValue ] = useState(0);
  return (
    <div>
      <p>
        현재 카운터 값은 <b>{value}</b> 입니다.
      </p>
      <button type="button" onClick={() => setValue(value + 1)}>
        +1
      </button>
      <button type="button" onClick={() => setValue(value - 1)}>
        -1
      </button>
    </div>
  );
}
```

`useState Hook`을 이해해보자면, 이 함수의 파라미터에는 상태의 기본값을 넣어줍니다. 이 함수가 호출되고 나면 배열을 반환합니다. 첫번째 원소는 상태값이고, 두번째 원소는 상태를 설정하는 합수입니다. 이 함수에 파라미터를 넣어서 호출하게 되면 전달받은 파라미터로 값이 바뀌게 되고 컴포넌트는 정상적으로 리렌더링 됩니다.

### 1.1 useState 여러번 사용하기

하나의 `useState` 함수는 하나의 상태 값만 관리할 수 있기 때문에 만약에 컴포넌트에서 관리해야 할 상태가 여러개라면 `useState`를 여러번 사용하면 됩니다.

```javascript
const [name, setName] = useState("");
const [nickname, setNickname] = useState("");
```

## 2. useEffect

`useEffect`는 리액트 컴포넌트가 렌더링 될 때마다 특정 작ㅇ버을 수행하도록 설정 할 수 있는 Hook 입니다. 클래스형 컴포넌트의 `componentDidMount`와 `componentDidUpdate`를 합친 형태로 보아도 무방합니다.

```javascript
import React, { useState, useEffect } from "react";

const Sample = () => {
  const [name, setName] = useState("");
  const [nickname, setNickname] = useState("");
  useEffect(() => {
    console.log({ name, nickname });
  });
};
```

### 2.1 마운트 될 때만 실행하고 싶을 때

만약 `useEffect`에서 설정한 하무가 컴포넌트 화면에 가장 처음 렌더링 될 떄만 실행되고 업데이트 할 경우에는 실행 할 필요가 없는 경우에 함수의 두번쨰 파라미터로 비어있는 배열을 넣어주시면 됩니다.

```javascript
useEffect(() => {
  console.log("마운트 될 때만 실행됩니다.");
}, []);
```

### 2.2 특정 값이 업데이트 될 떄만 실행하고 싶을 떄

`useEffect`를 사용 할 떄 특정 값이 변경 될 때만 호출하고 싶은 경우도 있을 겁니다. `useEffect`의 두번쨰 파라미터로 전달되는 배열 안에 검사하고 싶은 값을 넣어주시면 됩니다.

```javascript
useEffect(() => {
  console.log(name);
}, [name]);
```

### 2.3 뒷정리하기

만약 컴포넌트가 언마운트되기 전이나, 업데이트 되기 직전에 어떠한 작업을 수행하고 싶다면 `useEffect`에서 뒷정리 함수를 반환해주어야 합니다. 만약에, 오직 언마운트 될 때만 뒷정리 함수를 호출하고 싶으시다면 uesEffect 함수의 두번쨰 파라미터에 비어있는 배열을 넣으시면 됩니다.

```javascript
useEffect(() => {
  console.log("effect");
  console.log(name);
  return () => {
    console.log("cleanup");
    console.log(name);
  };
});
```

## 3. useContext

Hook를 사용하면 함수형 컴포넌트에서 `Context`를 보다 더 쉽게 사용 할 수 있습니다.

```JSX
import React, { createContext, useContext } from 'react';

const ThemeContext = createContext('black');
const ContextSample = () => {
  const theme = uesContext(ThemeContext);
  const style = {
    width: '24px',
    height: '24px',
    background: theme,
  };
  return <div style={style} />;
};
```

## 4. useReducer

`useReducer`는 `useState`보다 컴포넌트에서 더 다양한 상황에서 다양한 상태를 다른 값으로 업데이트해주고 싶을 때 사용하는 Hook 입니다. `Redux`에서는 액션 객체에서 어떤 액션인지 알려주는 `type` 필드가 꼭 있어야 하지만, `useReducer`에서 사용하는 액션 객체는 곡 `type`을 지니고 있을 필요가 없습니다.

```JSX
import React, { uesReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { value: state.value + 1 };
    case 'DECREMENT':
      return { value: state.value - 1 };
    default:
      return state;
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { value: 0 });

  return (
    <div>
      <p>{state.value}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+1</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-1</button>
    </div>
  );
};

export default Counter;
```

## 5. useMemo

`useMemo`를 사용하면 함수형 컴포넌트 내부에서 발생하는 연산을 최적화 할 수 있습니다. 렌더링하는 과정에서 특정 값이 바뀌었을 때만 연선을 실행하고 만약에 원하는 값이 바뀐 것이 아니라면 이전에 연산했던 결과를 다시 사용하는 방식입니다. 두번째 파라미터에 들어가는 값이 변하면, 첫번쨰 파라미터의 함수를 호춣합니다.

```JSX
import React, { useState, useMemo } from 'react';

const getAverage = numbers => {
  console.log('평균값 계산중..');
  if (number.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
};

const Average = () => {
  const [list, setList] = useState([]);
  const [number, setNumber] = useState('');

  const onChange = e => {
    setNumber(e.target.value);
  };
  const onInsert = e => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
  }

  const avg = useMemo(() => getAverage(list), [list]);

  return (
    <div>
      <input value={number} onChange={onChange} />
      <button onClick={onInsert}>등록</button>
      <ul>
        {list.map((value, index) => {
          <li key={index}>{value}</ii>
        })}
      </ul>
      <div>
        <b>평균 값:</b> {avg}
      </div>
    </div>
  );
};
```

## 6. useCallback

`useCallback`은 `useMemo`와 상당히 비슷한 함수입니다. 주로 렌더링 성능을 최적화해야하는 상황에서 사용하빈다. 이 Hook을 사용하면 이벤트 헨들러 함수를 힐요할 때만 생성 할 수 있습니다. 비어있는 배열을 넣게 되면 컴포넌트가 렌더링 될 때 단 한번만 함수가 생성되며, 배열 안에 값을 넣게되면 값이 바뀔 때마다 함수가 생성됩니다.

```javascript
const onChange = useCallback((e) => {
  setNumber(e.target.value);
}, []);
const onInsert = useCallvack(
  (e) => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber("");
  },
  [numner, list]
);
```

`useCallback`은 함수를 만드는 것, `useMomo`는 함수를 실행하는 것 입니다. `useCallback`에서 함수 내부에 기존 상태 값을 의존해야 할 떄는 꼭 두번쨰 파라미터 안에 포함을 시켜주어야 합니다.

## 7. useRef

useRefHook은 함수형 컴포넌트에서 ref를 쉽게 사용 할 수 있게 해줍니다. `useRef`를 사용하여 `ref`를 설정하면, `useRef`를 통해 만든 객체 안의 current 값이 실제 엘리머트를 가르키게 됩니다.

```JSX
import React, { useCallback, useRef } from 'react';

const Sample = () => {
  const inputEl = useRef();

  const focus = useCallback(e => {
    inputEl.cureent.focus();
  });

  return (
    <div>
      <input ref={inputEl} />
      <button onClick={focus}>입력</button>
    </div>
  );
};
```

### 7.1 로컬 변수 사용하기

컴포넌트의 로컬 변수를 사용해야 할 대도 uesRef를 활용 할 수 있습니다. 여기서 로컬 변수라 함은, 렌더링이랑은 관계 없이 바뀔 수 있는 값을 의미합니다.

```JSX
import React, { useRef } from 'react';

const RefSample = () => {
  const id = useRef(1);
  const setId = n => {
    id.current = n;
  }

  // ...
}
```

## 8. 커스텀 Hooks 만들기

만약에 여러 컴포넌트에서 비슷한 기능을 공유하게 되는 일이 발생한다면 이를 우리들만의 Hook을 작성하여 로직을 재사용 할 수 있습니다.

### 8.1 useInputs

```JSX
import { useReducer } from 'react';

function reducer(state, action) {
  return {
    ...state,
    [action.name]: action.value
  }
}

export default function useInputs(initialForm) {
  const [state, dispatch] = useReducer(reducer, initialForm);
  const onChange = e => {
    dispatch(e.target);
  };

  return [state, onChange];
}
```

```JSX
import React from 'react';
import useInputs from './useInputs';

const Info = () => {
  const [state, onChange] = useInputs({
    name: '',
    nicknmae: '',
  });
  const { name, nickname } = state;
  // ...
}
```

### 8.2 usePromise

`useState`와 `useEffect`를 함께 사용했습니다. 이 함수는 프로미스를 생성하는 `promiseCreator`와 언제 프로미스를 새로 만들지에 대한 조건을 위한 `deps` 배열을 파라미터로 받아옵니다.

```JSX
import { useState, useEffect } from 'react';

export default function usePromise(promiseCreatorm, deps) {
  const [resolved, setResolved] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const process = async () => {
    setLoading(true);
    try {
      const result = await promiseCreator();
      setResolved(result);
    } catch (e) {
      setError(e);
    }
  };

  useEffect(() => {
    process();
  }, deps);

  return [loading, resolved, error];
}
```

```JSX
const wait = () => {
  return new Promise(resolve => setTimeout(() => {
    resolve('Hello hooks!');
  }, 3000))
}

const [loading, resolved, error] = usePromise(wait, []);
```

`useEffect`를 사용할 때 주의할 점은, 파라미터로 전달해주는 함수에서 `async`를 사용하면 안됩니다. `useEffect`에서는 뒷정리 함수를 반환해야 하는데, `async`를 사용하게 되면 함수가 아닌 프로미스를 반환하기 때문입니다.

## 9. 출처

- [리액트의 Hooks 완벽 정복하기 - velopert](https://velog.io/@velopert/react-hooks)
