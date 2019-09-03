# 라이프 사이클 이벤트

> React 컴포넌트는 **생명주기**를 가진다. **생명주기**란 컴포넌트가 생성되고 사용되고 소명될 떄 까지 일련의 과정을 말한다. 이러한 생명주기 안에서는 특정 시점에 자동으로 호출되는 메서드가 있는데, 이를 **라이프 사이클 이벤트**라고 한다.

## 1. Mounting

```JSX
class Mounting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value = "Hello World",
    };
  }

  // render()

  // Dom Update

  componentDidlMount() {
    
  }
}
```

## constructor

- 생성자 메소드로 컴포넌트가 생성될 때 **단 한번만** 실행됩니다.
- 이 메소드에서만 `state`를 설정할 수 있습니다.

## componentDidMount

- 컴포넌트가 만들어지고 `render()` 함수가 끝난 이후에 호출되는 메소드입니다. 
- 다음과 같은 작업을 수행하기 적합합니다.
    - DOM을 사용해야하는 외부 라이브러리 연동
    - `axios`나 `fetch`들을 통하여 **AJAX** 요청
    - DOM의 속성을 읽거나 직접 변경하는 작업

## 2. Component Update

```JSX
class CompUpdate extends React.Component {
  static getDerivedStateFromProps(nextProps, prevState) {
    if (nextProps.value !== prevState.value) {
      return { value: nextProps.value}
    }
    return null;
  }

  shouldComponentUpdate(nextProps, nextState) {
    const { value } = this.props;
    return nextProps.value !== value;
  }

  // render()

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 새 데이터가 상단에 추가되어도 스크롤 위치를 유지하는 로직 예시
    const { array } = this.state;
    if (prevState.array !== array) {
      const { scrollTop, scrollHeight } = this.list;

      return { scrollTop, scrollHeight };
    }
  }

  // Dom Update

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 새 데이터가 상단에 추가되어도 스크롤 위치를 유지하는 로직 예시
    if (snapshot) {
      const { scrollTop } = this.list;
      const diff = this.list.scrollHeight - snapshot.scrollHeight;
      this.list.scrollTop += diff;
    }
  }
}
```

### getDerivedStateFromProps

- `setState()`를 하는것이 아니라 state값을 리턴하는 형태로 사용됩니다.
- `null`을 리턴하면 따로 업데이트 할 것이 없다는 의미로 해석됩니다.
- 주로 `state`가 `props`에 따라 변해야 하는 경우 사용합니다.

### shouldComponentUpdate

- React 엘리먼트를 Virtual DOM에 렌더링하기 직전에 호출되는 메소드입니다.
- DOM이 생성되지 않았으므로 DOM을 조작할 수 없고, `render()` 함수가 호출되기 전이기 떄문에 `setState()`를 사용해도 `render()`가 호출되지 않습니다.
- 기본적으로 `true`를 반환합니다. `false`를 반환하면 `render()` 함수가 호출되지 않습니다.

### getSnapshotBeforeUpdate

- DOM 변화가 일어나기 직전의 DOM 상태를 가져오기 적합합니다.
- 리턴하는 값은 `componentDidUpdate`에서 3번째 파라미터로 받아올 수 있습니다.

### componentDidUpdate

- `render()` 함수가 끝난 다음에 호출됩니다.
- `this.props`와 `this.sate`가 바뀌어있습니다.
- `getSnapshotBeforeUpdate()`의 반환 값을 세번째 파라미터로 받아옵니다.

## 3. Unmounting

```JSX
class Unmounting extends React.Component {
  componentWillUnmount() {

  }
}
```

### componentWillUnmount

- 컴포넌트가 DOM에서 삭제된 후 실행되는 메소드입니다.
- 다음과 같은 작업을 수행하기 적합합니다.
    - 이벤트 제거
    - `clearTimeout()`이나 `clearInterval()`을 사용하여 Timout과 Interval을 제거합니다.
    - 외부 라이브러리의 `dispose` 기능을 호출합니다.

## 4. Error

```JSX
class Error extends React.Component {
  componentDidCatch(error, info) {
    this.setState({
      error: true,
    });
  }
}
```

### componentDidCatch

- 자식 컴포넌트에서 발생하는 에러만 잡을 수 있고, 자신이나 부모의 에러는 잡을 수 없다.
- 에러 발생시 `state`를 변경하고 `render()`에서 해당 처리를 구현한다.

## 5. 출처

 - [https://velog.io/@kyusung](https://velog.io/@kyusung/%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B5%90%EA%B3%BC%EC%84%9C-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EC%99%80-%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4-%EC%9D%B4%EB%B2%A4%ED%8A%B8)
- [https://velopert.com](https://velopert.com/3631)