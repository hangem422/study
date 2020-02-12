#Styled Components

> 자바 스크립트 파일 안에 CSS를 작성하는 **CSS-in-JS** 라이브러리 입니다.

## 1. 설치하기

```bash
npm install styled-components
```

## 2. Tagged Template Literal

**styled-components**ㅇ세ㅓ는 스타일을 입력할 떄 **Tagged Template Literal**이라는 ES6 문법을 사용합니다. 이 문법을 사용하면 내부에 Javascript 객체나 함수가 전달 될 때 이를 따로 추출할 수 있습니다.

```javascript
function tagged(...args) {
  console.log(args);
}

tagged`hello ${{ foo: "bar" }} ${() => "world"}!`;
```

```javascript
[
  0: ['hello', ' ', '!'],
  1: { foo: 'bar'},
  2: () => 'world',
]
```

## 3. 스타일린 된 엘리먼트 만들기

상단에 `styled`를 불러오고 `styled.태그명`을 사용하여 구현합니다.

```javascript
import styled from "styeld-components";

const MyComponent = styled.div`
  font-size: 2rem;

  &:hover {
    background: rgba(255, 255, 255, 0.9);
  }

  & + button {
    margin-top: 1rem;
  }
`;
```

만약에 보여줘야 할 태그 형식이 유동적이거나, 아니면 특정 컴포넌트에 스타일링을 해야 하는 상황이라면 다음과 같이 구현합니다.

```javascript
// 문자열을 styled의 인자로 전달
const MyInput = styled("input")`
  background: gray;
`;

// 컴포넌트 형식의 값을 넣어줌
const StyledLink = styled(Link)`
  color: blue;
`;
```

## 4. 스타일에서 props 조회하기

스타일링 한 컴포넌트에 전달하는 props 값을 스타일쪽에서 그대로 사용 할 수 있습니다.

```javascript
const Box = styled.div`
  background: ${props => props.color || "blue"};
  padding: 1rem;
  display: flex;
`;
```

### 조건부 스타일링

일반 CSS 클래스를 사용했으면 주로 클래스 이름으로 조건부 스타일을 했을겁니다. 하지만 **styled-components**에서는 그냥 props로 처리 가능합니다.

```javascript
import styled, { css } from "style-components";

const Button = styled.button`
  background: white;
  color: black;
  border-radius: 4px;

  &:hover {
    background: rgba(255, 255, 255, 0.9);
  }

  ${props =>
    props.inverted &&
    css`
      background: none;
      border: 2px solid white;
      color: white;
      &:hover {
        background: white;
        color: black;
      }
    `};
`;
```

## 5. 반응형 디자인

일반 CSS와 똑같이 하면 됩니다.

```javascript
const Box = styled.div`
  width: 1024px;
  @media (max-width: 1024px) {
    width: 768px;
  }
`;
```

이 작업을 함수화하여 더 쉽게 사용할 수 있습니다.

```javascript
const sizes = {
  dektop: 1024,
  tabled: 768
};

const media = Object.keys(sizes).reduce((acc, label) => {
  acc[label] = (...args) => css`
    @media (max-width: ${sizes[label] / 16}em) {
      ${css(...args)};
    }
  `;

  return acc;
}, {});

const Box = styled.div`
  width: 1024px;
  ${media.desktop`width: 768px;`};
`;
```

## 6. 출처

- [https://velog.io/@velopert](https://velog.io/@velopert/react-component-styling)
