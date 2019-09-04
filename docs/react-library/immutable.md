# Immutable

> `setState()`는 객체 깊숙한 곳 까지 확인하지 못하여, **전개 연산자**를 사용해야합니다. 이러한 작업은 복잡하므로 **immutable.js** 같은 라이브러리를 사용합니다.

## 1. 설치하기

프로젝트에서 **Immutable**을 사용 할 땐, 다음과 같이 패키지를 설치해서 사용합니다.

```bash
npm install --save immutable
```

## 2. 사용 규칙

**Immutable**을 사용할때는 다음 규칙들을 기억해야 합니다.

### 객체

`Map()`을 사용합니다.

```javascript
const obj = Map({
  foo: 1,
  inner: Map({
    bar: 10,
  })
});
```

### 배열

`List()`를 사용합니다.

```javascript
const arr = List([
  Map({ foo: 1 }),
  Map({ bar: 2 }),
]);
```

### 설정

`set()`을 사용합니다.

```javascript
const nextObj = obj.set('foo', 5);
```

### 읽기

`get()`을 사용합니다.

```javascript
const foo = obj.get('foo');
const value = arr.get(0);
```

### 읽은 다음에 설정

`update()`를 사용합니다.

```javascript
cost updateObj = obj.update('foo', value => value + 1);
```

### 내부 데이터 접근

`getIn()`, `setIn()`, `updateIn()`을 사용합니다.

```javascript
const setObj = obj.setIn(['Inner', 'bar'], 20);
const bar = setObj.getIn(['Inner', 'bar']);

const setArr = arr.setIn([0, 'foo'], 10);
const value = setArr.getIn([0, 'foo']);
```

### 일반 자바스크립트 객체로 변환

`toJS()`를 사용합니다.

```javascript
const nomalObj = obj.toJS();
const nomalArr = arr.toJS();
```

### List 내장 함수

`push()`, `slice()`, `filter()`, `sort()`, `concat()`과 같이 배열 내장함수와 비슷한 함수가 있습니다.

```javascript
const pushArr = arr.push(Map({ qaz: 3 }));
const filterArr = arr.filter(item => item.get('foo') === 1);
```

### 삭제

`Map`에서 특정 key를 지우거나 `List`의 원소를 지울 떄는 `delete()`를 사용합니다.

```javascript
const deleteObj = obj.delete('foo');
const deleteArr = arr.delete(0);
```

## 3. 리액트 컴포넌트에서 사용하기

**Immutable**은 페이스북에서 만들었기 때문에 **React**와 호환이 되긴 하지만, `state` 자체를 **immutable**로 사용할 수는 없습니다.

### state 만들기

```javascript
this.state = {
  data: Map({
    input: '',
    users: List([
      Map({
        id: 1,
        username: 'user01',
      }),
      Map({
        id: 2,
        username: 'user02',
      }),
    ]),
  }),
};
```

### state 수정하기

```javascript
onChnage = event => {
  const { value } = event.target;
  const { data } = this.state;

  this.setState({
    data: data.set('input', value),
  });
}
```

```javascript
onButtonClick = () => {
  const { data } = this.state;
  const { id } = this;

  this.setState({
    data: data.set('input', '')
      .update('users', users => users.push(Map({
        id: id + 1,
        username: data.get('input'),
      }))),
  });
}
```

### state 사용하기

```JSX
render() {
  const { onChange, onButtonClick } = this;
  const { data } = this.state;
  const input = data.get('input');
  const users = data.get('users');

  return (
    <div>
      <div>
        <input onChange={onChange} value={input} />
        <button type="button" onClick={onVuttonClick}>
          추가
        </button>
      </div>
      <h1>사용자 목록</h1>
      <div>
        <UserList users={users} />
      </div>
    </div>
  )
}
```

## 4. Record 사용하기

**Record**를 사용하면 **Immutable**의 `set()`, `update()`, `delete()` 등을 계속 사용 할 수 있으면서, 값을 조회할 때 `get()`, `getIn()`을 사용하지 않고 `data.input`과 같이 직접 조회할 수 있습니다.

```javascript
const Person = Record({
  name: '홍길동',
  age: 1,
});

const user01 = Person();
// { name: "홍길동", age: 1 }

const user02 = Person({
  name: '영희',
  age: 10,
});
// { name: "영희", age: 10 }

const nested = Record({
  foo: Record({
    bar: true,
  })(),
})();
// nested.foo.bar === true

const nextNested = nested.setIn(['foo', 'bar'], false);
// nextNested.foo.bar === false
```