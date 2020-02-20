# Hooks 사용하기

> `usetState`및 `useReducer`를 사용하여 컴포넌트의 상태를 관리하는 방법과 `resRef`를 사용하여 컴포넌트 내부에 관리하는 변수 및 DOM을 이용하는 방법을 알아봅니다.

## 1. useState 및 이벤트 관리

#### useState

```TSX
const [count, useCount] = useState<number>(0);
```

참고로 `useState`를 사용 할 때 `Generics`를 사용하지 않아도 알아서 타입을 유추하기 때문에 생략해도 상관없습니다. 상태가 `null`일 수도 잇고 아닐수도 있을 때 Generics를 활용하면 좋습니다.

```TSX
type Infromation = { name:string, description: string };
const [info, setInformation] = useState<Information | null>(null);
```

추가적으로 상태의 까다로운 구조를 가진 객체이거나 배열일 때는 Generics를 명시하는 것이 좋습니다. 배열인 경우에는 아래와 같이 빈 배열만 엏었을 때 해당 배열이 어떤 타입으로 이루어진 배열인지 추록 할 수 엇기 떄문에 Generics를 명시하셔야 합니다.

```TSX
type Todo = { id: number; text: string; done: boolean };
const [todos, setTodos] = useState<Todo[]>([]);
```

Generics를 사용하지 않는다면 다음과 같이 할 수도 있습니다. 여기서 사용된 `as`는 Type Aessertion 이라는 문법인데요, 특정 타입이다라는 정보를 덮어 쓸 수 있는 문법입니다.

```TSX
type Todo = { id: number; text: string; done: boolean };
const [todos, setTodos] = useState([] as Todo[]);
```

다음와 같이 사용할 수 있습니다.

```TSX
import React, { useState } from 'react';

type SampleProps = {
  sampleFunc: (form: { name: string, description: string }) => void;
};

function Sample({ smapleFunc }: MyFormProps) {
  const [form, setForm] = useState({
    name: '',
    description: ''
  });

  // ...
}
```

#### 이벤트

e 값을 무엇으로 설정해야 할까요? 일단 모를때는 any로 설정합니다.

```TSX
const onChange = (e: any) => {
  // ...
}

const handleSubmit = (e: any) => {
  // ...
}

return (
  <form onSubmit={handleSubmit}>
    <input name="name" value={name} onChange={onChange} />
    <input name="description" value={description} onChange={onChange}>
  </form>
)
```

여기서 e객체의 타입이 무엇일지, 타입스크립트를 처음 쓰는 사람이라면 모릅니다. 편집기에서 커서를 올리면 어떤 타입을 사용해야하느닞 알려줍니다. 그리고 마우스로 드래그해서 복사하면 됩니다.

```TSX
const onChange = (e: React.ChangeEvent<HRMLInputElement>) => {
  // ...
}

const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
  // ...
}
```

## 2. useReducer

```TSX
import React, { useReducer } from 'react';

type Action = {type: 'INCREASE'} | {type: 'DECREASE'};

function reducer(state: number, action: Action): number {
  switch (action.type) {
    case 'INCREASE':
      return state + 1;
    case 'DECREASE':
      return state - 1;
    default:
      throw new Error('Unhandled action');
  }
}

function Sample() {
  const [count, dispatch] = useReducer(reducer, 0);
  const onIncrease = () => dispatch({ type: 'INCREASE' });
  const onDecrease = () => dispatch({ type: 'DECREASE' });
}
```

## 3. useRef

`useRef`는 리액트 컴포넌트에서 외부 라이러리의 인스턴스 또는 DOM을 특정 값 안에 담을 떄 사용합니다. 추각적으로, 이를 통해 컴포넌트 내부에서 관리하고 있는 값을 관리할 때 유용합니다. 단, 이 값은 랜더링과 관계가 없어야 합니다.

```TSX
const inputRef = useRef<HTMLInputElement>(null);
```

추가적으로, `inputRef.current` 안의 값을 사용 하려면 `null` 채킹을 해주어야 합니다. 타입스크립트에 만약 어떤 타입이 `undefined` 이거나 `null`일수 있는 상황에는, 해당 값이 유효한지 체킹하는 작업을 꼭 애야 자동완성도 잘 이루어지고, 오류도 사라집니다.

```TSX
if(!inputRef.current) {
  return;
}
inpuRef.current.focus();
```
