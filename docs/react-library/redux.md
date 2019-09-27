# Redux

> 기존에 부모에서 자식으로 상태가 흐르던 것을, 리덕스를 상용하면 스토어를 사용하여 상태를 컴포넌트 구조 바깥에 두고 업데이트를 하거나 새로운 상태를 전달받습니다. 따라서, 여러 컴포넌트를 거쳐서 받아올 필요 없이 아무리 깊숙한 컴포넌트에 있다 하더라도 직속 부모에게서 받아오는 것 처럼 원하는 상태값을 골라서 `porps`를 편리하게 받아올 수 있습니다.

## 1. 리덕스 3가지 규칙

### 하나의 애플리케이션 안에는 하나의 스토어가 있다.

권장되지는 않습니다. 여러개의 스토어를 만들고 싶다면 만들 수 있습니다. 특정 엡데이트가 너무 빈번하게 일어나거나, 애플리케이션의 특정 부분을 완전히 분리시키게 될 때 여러개의 스토어를 만들 수도 있습니다. 하지만 개발 도구를 활용하지는 못합니다.

### 상태는 읽기 전용이다.

리덕스에서 불변성을 유지해야하는 이유는 내부적으로 데이터가 변경 되는 것을 갑지하기 위하여 `shallow equality` 검사를 하기 때문입니다. 이를 통하여 객체의 변화를 감지 할 때 객체의 깊숙한 안쪽까지 비교하는 것이 아니라 겉핥기 식으로 비교하여 좋은 성능을 유지할 수 있습니다.

### 변화를 일으키는 함수, 리듀서는 순수한 함수다.

순수한 함수는 다음 사항을 지켜주세요.

- 작성중...

하지만 일부 로직들은 실행 할 때마다 다른 결과값이 나타날 수 있습니다. 그런것들은 `리덕스 미들웨어`에서 처리합니다.

## 2. 액션 생성하기

액션 객첵는 `type`과 함께 전달할 값을 가지고 있습니다. 액션을 만들때마다 객체를 만들기 보다는 **액션 생성자** 함수를 만들어 사용합니다.

#### src/module/reducer01

```javascript
const INCREMENT = 'counter/INCREMENT';
const DECREMENT = 'counter/DECREMENT';
const SET_COLOR = 'counter/SET_COLOR';

export const increment = () => ({
  type: INCREMENT,
});

export const decrement = () => ({
  type: DECREMENT,
});

export const setColor = color => ({
  type: SET_COLOR,
  color,
});
```

### redux-actions

`redux-acitons`의 `createAction`을 사용하면 다음과 같이 작성할 수 있습니다.

```javascript
import { createAction } from 'redux-actions';

const INCREMENT = 'counter/INCREMENT';
const DECREMENT = 'counter/DECREMENT';
const SET_COLOR = 'counter/SET_COLOR';

export const increment = createAction(INCREMENT);
export const decrement = createAction(DECREMENT);
export const setColor = createAction(SET_COLOR, color => color);
```

## 3. 리듀서 생성하기

리듀서는 `type`에따라 변화를 일으키는 함수입니다. 최초 변화를 일으키기전, **초기 상태**가 정의되어야 합니다.

#### src/module/reducer01

```javascript
const initialState = {
  number: 0,
  color: 'black',
};

function counter(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return {
        ...state,
        number: state.number + 1,
      }
    case DECREMENT:
      return {
        ...state,
        nuber: state.number - 1,
      }
    case SET_COLOR:
      return {
        ...state,
        color: action.color
      }
    default:
      return state;
  }
};

export default counter;
```

### handleActions

`redux-actions`의 `handleActions`를 사용하면 다음과 같이 작성할 수 있습니다.

```javascript
const initialState = {
  number: 0,
};

export default handleActions({
  [INCREMENT]: ({ number }) => ({ number: number + 1 }),
  [DVREMENT]: ({ number }) => ({ number: number - 1 }),
  [SET_COLOR]: (state, action) => {
    return { color: action.payload.color }
  },
}, initialState);
```

## 4. 리듀서 합치기

한 프로젝트에 여러개의 리듀서가 존재할 수 있습니다. 여러개의 리듀서가 있을 때에는, `redux`의 `combineReducers`를 사용하여 하나의 리듀서로 합쳐줄 수 있습니다. 이렇게 합쳐진 리듀서는 **루트 리듀서**라고 부릅니다.

#### src/module/index.js

```javascript
import { combineReducers } from 'redux';
import Reducer01 from './reducer01';
import reducer02 from './reducer02';

export default combineReducers({
  reducer01,
  reducer02,
});
```

## 5. 스토어 만들기

먼저 스토어를 만드는 함수를 만들어 내보냅니다.

#### src/store/configure.js

```javascript
import { createStore } from 'redux';
import modules from './modules';

const configure = () => {
  // devTool은 크롬의 redux-devtools를 사용하기 위해서 선언합니다. 불필요하면 생략합니다.
  const devTools = window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__();
  const store = createStore(modules, devTools);

  return store;
}

export default configure;
```

#### src/store/index.js

```javascript
import configure from './configure';
export default configure();
```

### 6. 리덕스 적용하기

리액트 앱에 리덕스를 적용 할 때에는, `react-redux`에 들어있는 **Provider**를 사용합니다.

```javascript
import React from 'react';
import { Provider } from 'react-redux';
import start from './store';

import App from './components/App';

const Root = () => {
  return (
    <Provider store={store}>
      <App />
    </Provider>
  );
}

export default Root;
```

### 7. 리덕스 사용하기

컴포넌트를 리덕스와 연동할 때는 `connect`를 사용합니다.

```javascript
import React, { Component } from 'react';
import counter from 'component/Counter';
import { connect } from 'react-redux';
import * as reducer01Actions from 'store/modules/Reducer01';
import { getRandomColor } from '../urtils';

class SampleContainer extends Component {
  handleIncrement = () => {
    this.props.increment();
  }

  handleDecrement = () => {
    this.props.decrement();
  }

  handleColor = () => {
    const color = getRandomColor();
    this.props.setColor(color);
  }

  render() {
    const {
      handleIncrement,
      handleDecrement,
      handleColor,
    } = this;
    const { number, color } = this.props;

    return (
      <Counter
        onIncrement={handleIncrement}
        onDecrement={handleDecrement}
        onSetColor={handleColor}
        number={number}
        color={color}
      />
    );
  }
}

const mapStateToProps = state => ({
  color: state.reducer01.color,
  number: state.reducer01.number,
});

const mapDispatchToProps = dispatch => ({
  increment: () => dispatch(reducer01Actions.increment()),
  decrement: () => dispatch(reducer01Actions.decrement()),
  setColor: color => dispatch(reducer01Actions.setColor(color));
});

export default connect(mapStateToProps, mapDispatchToProps)(SampleContainer);
```

### connect 내부에 선언하기

보통은 위와 같은 코드처럼, `mapStateToProps`와 `mapDispatchToProps`를 따로 만들곤 하는데, 사람마다 차이가 있을 수 있겠지만 그냥 함수를 `connect` 내부에서 정의하면 코드가 조금 더 깔끔해집니다.

```javascript
class SampleContainer extends Component {
  // ...
}

export default connect(
  state => ({
    number: state.reducer01.number,
  }),
  dispatch => ({
    increment: () => dispatch(reducer01Actions.increment()),
    decrement: () => dispatch(reducer01Actions.decrement()),
    setColor: color => dispatch(reducer01Actions.setColor(color));
  })
)(SampleContainer);
```

### bindActionCreator

액션 함수마다 일일히 `dispatch(actionCreator())` 형식으로 작성하기 보다는 `redux`의 `bindActionCreator` 함수를 이용하면 간소화 할 수 있습니다.

```javascript
import { bindActionCreators } from 'redux';

class SampleContainer extends Component {
  // ...
}

export default connect(
  state => ({
    number: state.reducer01.number,
  }),
  dispatch => ({
    dispatch => bindActionCreators(reducer01Actions, dispatch)),
  })
)(SampleContainer);
```

### 여러 모듈 액션 참조하기

```javascript
class SampleContainer extends Component {
  handleIncrement = () => {
    const { Reducer01Actions } = this.props;
    Reducer01Actions.increment();
  }

  // ...
}

export default connect(
  state => ({
    number: state.reducer01.number
  }),
  dispatch => ({
    Reducer01Actions: bindActionCreator(reducer01Actions, dispatch),
  }),
)(SampleContainer);
```

### 액션 함수 미리 bind

프로젝트에서 한 종류의 모듈을 여러 곳에서 사용할 일이 많습니다. **액션 생성 함수**를 미리 `bind`하면 편리합니다. 이것을 하기 위해서는 리덕스 스토어 인스턴스가 모듈화되어 불러올 수 있는 상태여야 합니다.

#### src/sotore/actionCreators.js

```javascript
import { bindActionCreator } from 'redux';
import * as reducer01Actions from './modules/reducer01';
import * as reducer02Actions from './modules/reducer02';

import store from './index';

const { dispatch } = store;

export const Reducer01Actions = bindActionCreators(reducer01Actions, dispatch);
export const Reducer02Actions = bindActionCreators(reducer02Actions, dispatch);
```

#### src/container/SampleContainer.js

```javascript
import React, { Component } from 'react';
import Counter from 'component/Counter';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import { Reducer01Actions } from 'store/actionCreators';
import { getRandomColor } from '../urtils';

class SampleContainer extends Component {
  handleIncrement = () => {
    Reducer01Actions.increment();
  }

  handleDecrement = () => {
    Reducer01Actions.decrement();
  }

  handleColor = () => {
    const color = getRandomColor();
    Reducer01Actions.setColor(color);
  }

  render() {
    const {
      handleIncrement,
      handleDecrement,
      handleColor,
    } = this;
    const { number, color } = this.props;

    return (
      <Counter
        onIncrement={handleIncrement}
        onDecrement={handleDecrement}
        onSetColor={handleColor}
        number={number}
        color={color}
      />
    );
  }
}

export default connect(
  state => ({
    number: state.reducer01.number,
    color: state.reducer01.color,
  });
)(SampleContainer);
```

# Midlleware

> 미들웨어는 액션이 디스패치 되어서 리듀서에서 이를 처리하기저에 사전에 지정된 작업들을 설정합니다. 미들웨어를 액션과 리듀서 사이의 중간자입니다.

## 1. 미들웨어 만들기

실제 프로젝트를 작업할 때느에는, 미들웨어를 직접 만들어서 사용하는 경우는 많지 않습니다. 대부분의 경우에는 이미 만들어놓은 미들웨어들을 사용합니다.

```javascript
const Middleware = store => next => action => {
  // ...
}
```

`next`는 `store.dispatch`와 비슷합니다. 차이점은 `next(action)`은 바로 리듀서로 넘기거나, 혹은 미들웨어가 더 있다면 다음 미들웨어 처리를 진햅합니다. 하지만 `store.dispatch`는 처음부터 다시 액션이 디스패치 되어 현재 미들웨어를 다시한번 처리하게 됩니다.

```javascript
const loggerMiddleware = sotre => next => action => {
  console.log(store.getState()); // 현재 스토어 상태값 출력
  console.log(action); // 액션 출력

  const result = next(action) // 액션을 다음 미들웨어, 혹은 리듀서로 넘김

  console.log(store.getState()); // 액션 처리 후의 스토어 상택 출력

  return result; // 여기서 반환하는 값은 sotre.dispatch(ACTION_TYPE) 했을때의 결과로 설정
}

export default loggerMiddleware;
```

## 2. 미들웨어 적용하기

redux 모듈 안에 들어있는 `appltMiddleware`를 사용하여 설정합니다. 미들웨어가 여러개인 경우에는 파라미터로 여러개를 전달해주면 됩니다. 미들웨어의 순서는 여기서 전달한 파라미터의 순서대로 지정됩니다.

```javascript
import { createStore, applyMiddleware } from 'redux';
import modules from './modules';
import loggerMiddleware from './lib/loggerMiddleware';

const store = createStore(modules, applyMiddleware(loggerMiddleware));

export default store;
```

## 3. redux-logger

오픈소스 커뮤니티에 만들어진 **로깅 미들웨어**입니다. **Redux DevTool**을 사용한다면 **redux-logger**는 사실 쓸모가 없습니다.

### 설치하기

```bash
npm install --save redux-logger
```

### 적용하기

로그 미들웨어를 생성 할 때 설정을 커스터마이징 할 수 있습니다. [커스터마이징 상세보기](https://github.com/evgenyrodionov/redux-logger#options)

```javascript
import { createStore, applyMiddleware } from 'redux';
import modules from 'modules';

import { createLogger } from 'redux-logger';

const logger = createLogger();
const store = createStore(modules, applyMiddleware(logger));

export default store;
```

## 4. redux-thunk

비동기 작업을 처리 할 때 가장 기본적인 방법으로 사용합니다. `redux` 공식 매뉴얼에도 이 미들웨어를 사용하여 비동기 작업을 다룹니다.

### thunk란?

특정 작업을 나중에 하도록 미루기 위해서 함수형태로 감싼것을 말합니다.

```javascript
const foo = () => 1 + 2;
```

### redux-thunk란?

객체 대신 함수를 생성하는 액션 생성함수를 작성 할 수 있게 해줍니다. 일반 액션 생성자는 다음과 같이 파라미터를 가지고 액션 객체를 생성하고, `redux`에서는 이 액션 객체를 `dispatch`합니다.

```javascript
const actionCreator = payload => ({ action: 'ACTION',  payload });
```

만약에 특정 액션을 몇초 뒤에 실행하거나, 현재 상태에 따라 무시되게 하려면 일반 액션 생성자로 할 수가 없습니다. 하지만, `redux-thunk`는 가능합니다.

```javascript
const INCREMENT_COUNTER = 'INCREMENT_COUNTER';

const increment = () => ({ type: INCREMENT_COUNTER });

// 액션을 몇초 뒤에 실행
const incrementAsync = () => dispatch => {
  setTimeout(() => {
    dispatch(increment());
  }, 1000);
};

// 액션을 현재 상태에 따라 무시
const incrementIfOdd => () => (dispatch, getState) => {
  const { counter } = getState();

  if (counter % 2 === 0) return;

  dispatch(increment());
}
```

`redux-thunk` 미들웨어에서 전달받은 액션이 함수이면 `dispatch`와 `getState`를 넣어서 실행합니다.

```javascript
function createThunkMiddleware(extraArgument) {
  return ({dispatch, getState}) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }

    return next(action);
  }
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```

### 설치와 적용

```bash
npm install --save redux-thunk
```

```javascript
import { createStore, applyMiddleware } from 'redux';
import modules from './modules';

import { createLogger } from 'redux-logger';
import ReduxThunk from 'redux-thunk';

const logger = createLogger();

const store = createStore(modules, applyMiddleware(logger, RduxThunk));

export default store;
```

### Thunk를 통하여 웹 요청 해보기

위에서 살펴본 `redux-thunk`를 사용하여 웹 요청을 하면 다음과 같습니다.

```javascript
import { handleActions } from 'redux=actions';

import axios from 'axios';

function getPostAPI(postId) {
  return axios.get(`https://jsonplaceholder.typicode.com/posts/${postId}`);
}

const GET_POST_PENDING = 'GET_POST_PENDING';
const GET_POST_SUCCESS = 'GET_POST_SUCCESS';
const GET_POST_FAILURE = 'GET_POST_FAILURE';

export const getPost = postId => dispatch => {
  disptach({type: GET_POST_PENDING});

  return getPostAPI(postId).then(
    response => {
      dispatch({
        type: GET_POST_SUCCESS,
        payload: response,
      });
    }
  ).catch(error => {
    dispatch({
      type: GET_POST_FAILURE,
      payload: error,
    });
  });
};

const initialState = {
  pending: false,
  error: false,
  data: {
    title: '',
    body: '',
  }
};

export default handleActions({
  [GET_POST_PENDING]: (state, action) => {
    return {
      ...state,
      pending: true,
      error: false,
    };
  },
  [GET_POST_SUCCESS]: (state, action) => {
    const { title, body } = action.payload.data;

    return {
      ...state,
      pending: false,
      data: {
        title, body
      },
    };
  },
  [GET_POST_FAILURE]: (state, action) => {
    return {
      ...state,
      pending: false,
      error: true,
    };
  },
}, initialState);
```

### 요청 완료 후 & 에러 발생시 추가 작업하기

만약에 요청 완료 후 컴포넌트에서 해야 할 작업이 있거나, 에러가 발생했을 때 어떤 작업을 해야한다면, `anync`와 `await`를 사용하세요.

```javascript
getPost = async (postId) => {
  const { PostActions } = this.props;

  try {
    await PostActions.getPost(postId);
    // 요청 완료 된 다음에 실행할 작업
  } catch(e) {
    // 에러가 발생했을 떄 작업
  }
};
```

`babel`의 **Async to generator transform** 플러그인이 설치되어있지 않다면 다음과 같이 합니다.

```javascript
getPost = postId => {
  const { PostActions } = this.props;

  PostActions.getPost(postId).then(
    () => {
      // 요청 완료 된 다음에 실행할 작업
    }.catch(e) => {
      // 에러가 발생했을 떄 작업
    }
  )
};
```

## 5. redux-promise-middleware

프로미스 기반의 비동기 작업을 조금 더 편하게 해주는 미들웨어입니다.

### 설치와 적용

```bash
npm install --save redux-promise-middleware
```

이 미들웨어는 프로미스가 payload로 전달되면, 요청이 시작, 성공, 실패 할 때 액션의 뒷부분에 `_PENDING`, `_FULFILED`, `REJECTED`를 반환합니다. 이 뒷부분에 붙는 접미사는 커스터마이징 할 수도 있습니다.

#### src/store.js

```javascript
import { createStore, applyMiddleware } from 'redux';
import { createLogger } from 'redux-logger';
import ReduxThunk from 'redux-thunk';
import { createPromise } from 'redux-promise-middleware';

import modules from './modules';

const logger = createLogger();

const store =  createStore(
  modules,
  applyModdleware(
    logger,
    ReduxThunk,
    createPromise({
      promiseTypeSuffixes: ['PENDING', 'SUCCESS', 'FAILURE'],
    }),
  ),
);

export default store;
```

#### src/modules/post.js

```javascript
import { handleActions } from 'redux-actions';

import axios from 'axios';

function getPostAPI(postId) {
  return axios.get(`https://jsonplaceholder.typicode.com/posts/${postId}`);
}

const GET_POST = 'GET_POST';
const GET_POST_PENDING = 'GET_POST_PENDING';
const GET_POST_SUCCESS = 'GET_POST_SUCCESS';
const GET_POST_FAILURE = 'GET_POST_FAILURE';

export const getPost = postId => ({
  type: GET_POST,
  payload: getPostAPI(postId),
});

// ...
```

## 6. 출처

- [VELOPERT.LOG](https://velopert.com/3401)