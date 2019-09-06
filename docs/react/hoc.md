# Higher-order Component

> 소프트웨어 개발 원리 중에서 DRY라는 개녕이 있습니다. **같은 작업을 반복하지 마라**(Don't repeat yourself). 리액트 컴포넌트를 작성하게 될 때 반복될 수 있는 코드들을 **HOC**라는 함수로 만들어서 해결할 수 있습니다.

## 1. HOC 작성하기

**HOC**의 원리는, 파라미터로 컴포넌트를 받아오고 함수 내부에서 새 컴포넌트를 만든 다음에 새 컴포넌트 안에서 받아온 컴포넌트를 렌더링하는 것입니다. 그리고, 자신이 받아온 props들은 그대로 받아온 컴포넌트에게 다시 주입해주고, 필요에 따라 추가 props도 넣어줍니다.

```JSX
import React, { Component } from 'react';
import axios from 'axios';

// 주로 HOC의 이름을 만들때는 'with[이름]' 형식으로 짓습니다.
const withRequest = url = WrappedComponent => {
  return class extends Component {
    constructor(props) {
      super(props)
      this.state = {
        data: null,
      }
    }

    async initialize() {
      try {
        const response = await axios.get(url);
        this.setState({
          data: response.data
        });
      } catch (error) {
        console.log(error);
      }
    }

    componentDidMount() {
      this.initialize();
    }

    render() {
      return (
        <WrappedComponent {...this.props} data={data}/>
      );
    }
  };
}
```

## 2. HOC 사용하기

```JSX
import React, { Component } from 'react';
import withRequest from './withRequest';

class Post extends Component {
  render() {
    const { data } = this.props;

    return (
      <div>
        {JSON.stringify(data)}
      </div>
    );
  }
}

export default withRequest('https://jsonplaceholder.typicode.com/posts/1')(Post);
```

## 3. 출처

- [VELOPERT.LOG](https://velopert.com/3537)