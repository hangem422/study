# Immutable

## 1. 설치하기

프로젝트에서 **Immutable**을 사용 할 땐, 다음과 같이 패키지를 설치해서 사용합니다.

```bash
npm install --save immutable
```

## 2. 사용 규칙

**Immutable**을 사용할때는 다음 규칙들을 기억해야 합니다.

### 2.1 객체

`Map()`을 사용합니다.

```javascript
const obj = Map({
  foo: 1,
  inner: Map({
    bar: 10,
  }),
});
```

### 2.2 배열

`List()`를 사용합니다.

```javascript
const arr = List([Map({ foo: 1 }), Map({ bar: 2 })]);
```

### 2.3 설정

`set()`을 사용합니다.

```javascript
const nextObj = obj.set("foo", 5);
```

### 2.4 읽기

`get()`을 사용합니다.

```javascript
const foo = obj.get("foo");
const value = arr.get(0);
```

### 2.5 읽은 다음에 설정

`update()`를 사용합니다.

```javascript
const updateObj = obj.update("foo", (value) => value + 1);
```

### 2.6 내부 데이터 접근

`getIn()`, `setIn()`, `updateIn()`을 사용합니다.

```javascript
const setObj = obj.setIn(["Inner", "bar"], 20);
const bar = setObj.getIn(["Inner", "bar"]);

const setArr = arr.setIn([0, "foo"], 10);
const value = setArr.getIn([0, "foo"]);
```

### 2.7 일반 자바스크립트 객체로 변환

`toJS()`를 사용합니다.

```javascript
const nomalObj = obj.toJS();
const nomalArr = arr.toJS();
```

### 2.8 List 내장 함수

`push()`, `slice()`, `filter()`, `sort()`, `concat()`과 같이 배열 내장함수와 비슷한 함수가 있습니다.

```javascript
const pushArr = arr.push(Map({ qaz: 3 }));
const filterArr = arr.filter((item) => item.get("foo") === 1);
```

### 2.9 삭제

`Map`에서 특정 key를 지우거나 `List`의 원소를 지울 떄는 `delete()`를 사용합니다.

```javascript
const deleteObj = obj.delete("foo");
const deleteArr = arr.delete(0);
```

## 3. Record 사용하기

**Record**를 사용하면 **Immutable**의 `set()`, `update()`, `delete()` 등을 계속 사용 할 수 있으면서, 값을 조회할 때 `get()`, `getIn()`을 사용하지 않고 `data.input`과 같이 직접 조회할 수 있습니다.

```javascript
const Person = Record({
  name: "홍길동",
  age: 1,
});

const user01 = Person();
// { name: "홍길동", age: 1 }

const user02 = Person({
  name: "영희",
  age: 10,
});
// { name: "영희", age: 10 }

const nested = Record({
  foo: Record({
    bar: true,
  })(),
})();
// nested.foo.bar === true

const nextNested = nested.setIn(["foo", "bar"], false);
// nextNested.foo.bar === false
```

## 4. 출처

- [Immutable.js – 리액트의 불변함, 그리고 컴포넌트에서 Immutable.js 사용하기 - VELOPERT.LOG](https://velopert.com/3486)
