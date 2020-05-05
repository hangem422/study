# 리액트 라우터

> SPA도 화면에 따ㅏㄹ 주소를 만들어줘야 합니다. 주소가 있어야 유저들이 북마크도 할 수 있고 서비스로 구글을 통해 유입될 수 있기 때문입니다. 다른 주소에 따라 다른 뷰를 보여주는 것을 라우팅이라고 합니다. 리액트 자체에는 이 기능이 내장돼있지 않아 별도의 라이브러리를 사용해야합니다.

## 1. 설치하기

- **react-router-dom:** 브라우저에서 사용되는 래액트 라우터 입니다.
- **cross-env** 프로젝트에서 NODE_PATH를 사용하여 절대 경로로 파일을 불러오기 위해 환경 변수를 설정할 때 운영체제마다 방식이 다르므로 공통적인 방법으로 설정 할 수 있게 해주는 라이브러리입니다.
- **query-string:** 쿼리를 해석하기 위한 라이브러리입니다. 자체적으로 구현하는 방법도 있지만 라이브러리를 사용하는 것이 훨씬 간편합니다.

```bash
npm install --save react-router-dom
npm install --save-dev cross-env
npm install --save query-string
```

## 2. 준비하기

### NODE_ENV 설정

코드를 불러올 때 `../components/Something`으로 불러오던 코드를 `component/Something` 이렇게 불러 올 수 있도록 프로젝트의 루트 경로를 설정하겠습니다.

```json
"script": {
  "start": "cross-env NODE_PATH=src react-scripts start",
  "build": "cross-env NODE_PATH=src react-scripts build",
  "test": "react-scripts test --env=jsdom",
  "eject": "react-scripts eject"
}
```

### 컴포넌트 준비

```JSX
import React from 'react';
import { BrowserRouter } from 'react-router-dom';
import App from 'shared/App';

const Root = () => (
  <BrowserRouter>
    <App/>
  </BrowserRouter>
);

export default Root;
```

## 3. Route

라우트를 설정할 때에는 **Route** 컴포넌트를 사용하고, 경로는 `path` 값으로 설정합니다. `exact`는 주어진 경로와 정확히 맞아 떨어져야만 설정한 컴포넌트를 보여줍니다.

```JSX
import React, { Component } from 'react';
import { Route } from 'react-router-dom';
import { Home, About } from 'pages';

class App extends Component {
  render() {
    return (
      <div>
        <Route exact path="/" component={Home} />
        <Route path="/about" component={About} />
      </div>
    );
  }
}
```

지금의 경우에는 개발 서버쪽에서 `historyApiFallback` 설정을 통하여 어떤 요청으로 들어오던 어플리케이션이 불러와져있는 index.html을 보여주도록 설정하기 때문입니다.

## 4. 파라미터 읽기

라우트로 설정한 컴포넌트는 3가지의 props를 전달받게 됩니다.

- **history:** 이 객체를 통해 `push`, `replace`를 사용하여 다른 경로로 이동하거나 앞 뒤 페이지로 전환 할 수 있습니다.
- **location:** 이 객체는 현재 경로에 대한 정보를 지니고 있고 URL 쿼리 정보도 가지고 있습니다.
- **match:** 이 객체에는 어떤 라우트에 매칭이 되었는지에 대한 정보가 있고 params 정보를 가지고 있습니다.

**Switch** 컴포넌트를 사용하면 매칭되는 첫번쨰 라우트만 보여주고 나머지는 보여주지 않습니다.

```JSX
<div>
  <Route exact path="/" component={Home} />
  <Swtich>
    <Route path="/about" component={About} />
    <Route path="/about/:name" component={About} />
  </Switch>
</div>
```

```JSX
import React from 'react';
import queryString from 'query-string';

const About = ({ location, match }) => {
  const query = queryString.parse(location.search);
  const detail = query.detail === 'ture';

  return (
    <div>
      <h2>About {match.params.name}</h2>
      {detail && 'detail is true'}
    </div>
  );
};
```

## 5. 라우트 이동하기

앱 내에서 다른 루트로 이동 할 떄에는 `<a href="">`를 사용하면 안됩니다. 새로고침을 하기 떄문입니다. 새로고침을 피해 이동하기 위해서는 라우터에 있는 **Link** 컴포넌트를 사용합니다.

```JSX
import React from 'react';
import { Link } from 'react-router-dom';

const Menu = () => {
  return (
    <div>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">About</Link></li>
      </ul>
    </div>
  );
}
```

### NavLink 컴포넌트

**NavLink** 컴포넌트는 **Link**와 비슷합니다. 만약에 설정한 URL이 활성화되면, 특정 스타일 혹은 클래스를 지정할 수 있습니다. **Route**를 지정할 대 처럼, 중첩될 수 있는 라우트들은 `exact`로 설정해야 합니다.

```JSX
import React from 'react';
import { NavLink } from 'react-router-dom';

const Menu = () => {
  const aciveStyle = {
    color: 'green',
    fontSize: '2rem',
  };

  return (
    <div>
      <ul>
        <li>
          <NavLink exact to="/" activeStyle={activeStyle}>Home</NavLink>
        </li>
        <li>
          <NavLink exact to="/about" activeStyle={activeStyle}>About</NavLink>
        </li>
      </ul>
    </div>
  );
}
```

## 6.라우트 속의 라우트

v4에서는, `props.children`을 사용하지 않고 라우트에서 보여주는 컴포넌트 내부에 또 **Route**를 사용할 수 있게 됐습니다.

```JSX
import React from 'react';
import { Link, Route } from 'react-router-dom';
import { Post } from 'pages';

const Posts = ({ match }) => {
  return (
    <div>
      <h2>Post List</h2>
      <ul>
        <li><Link to={`${match.url}/1`}>Post #1</Link></li>
        <li><Link to={`${match.url}/2`}>Post #2</Link></li>
        <li><Link to={`${match.url}/3`}>Post #3</Link></li>
        <li><Link to={`${match.url}/4`}>Post #4</Link></li>
      </ul>
      <Route exact path={match.url} render={()=>(<h3>Please select any post</h3>)} />
      <Route path={`${match.url}/:id`} component={Post} />
    </div>
  );
};
```

## 7. 출처

- [VELOPERT.LOG](https://velopert.com/3417)
