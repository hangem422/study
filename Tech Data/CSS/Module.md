# CSS Module

CSS Module은 CSS 클래스를 불러와서 사용할 떄 `[파일이름]_[클래스이름]__[해쉬값]` 형태로 클래스네임을 고유하게 자동으로 만들어주어 컴포넌트 중첩현상을 방지해주는 기술입니다.

## 1. 사용하기

### 1.1 CSS 파일

CSS Module을 사용하기 위해서는 `[파일이름].module.css` 이런식으로 파일을 저장해야합니다.

```css
/* 자동으로 고유해짐으로 흔히 사용되는 단어를 클래스 이름으로 사용 가능합니다. */
.wrapper {
  background: black;
  padding: 1rem;
  color: white;
  font-size: 2rem;
}

.invert {
  color: black;
  background: white;
}

/* 글로벌 CSS를 작성하고 싶다면 :global을 사용합니다 */
:global .something {
  font-weight: 800;
  color: aqua;
}
```

### JS 파일

JS에서 사용하기 위해서는 `classNmae={style.[클래스이름]}` 형태로 설정해주시면 됩니다.

```javascript
import React from "react";
import styles from "./CSSModule.module.css";

const CSSModule = () => {
  return (
    <div className={styles.wrapper}>
      안녕하세요, 저는 <span className="something">CSS Module!</span>
    </div>
  );
};

export default CSSModule;
```

style을 불러오면 하나의 객체를 전달받게 되는데 그 안에는 CSS Modules에서 사용한 클래스 이름과, 해당 이름을 고유화시킨 값이 `key-value` 형태로 들어있습니다.

```json
{
  "wrapper": "CSSModule_wrapper__CUMkx"
}
```

만약에 CSS Module을 사용한 클래스이름을 두개 이상 적용할 떄는 다음과 같이 합니다.

```javascript
return (
  <div className={`${styles.wrapper} ${styles.inverted}`}>
    안녕하세요, 저는 <span className="something">CSS Module!</span>
  </div>
);
```

## 2. classNames

classNames는 CSS 클래스를 조건부로 설정할 떄 매우 유용한 라이브러리입니다. 그리고 CSS Moudle을 사용할 떄 이 라이브러리를 함께 사용한다면 여러 클래스를 적용할 떄 편해집니다.

### 2.1 설치하기

```bash
npm install --save classnames
```

### 2.2 사용하기

여러가지 종류의 파라미터를 조합해 CSS 클래스를 설정할 수 있어 컴포넌트 조건부 클래스를 설정할 때 굉장히 편합니다.

```javascript
import classNames from 'classNames';

classNames('one', 'two'); // one two
classNames('one', { two: true }); // one two
classNames('one' { two: false }); // one
classNames('one', ['two', 'three']) // one two three

const myClass = 'hello';
classNames('one', myClass, { myCondition: true }); // 'one hello myCondition'

const MyComponent = ({ highlighted, theme }) => (
  <div className={classNames('MyComponent', { highlighted }, theme)}>Hello</div>
);
```

### 2.3 CSS Module과 함께 사용하기

classNames를 불러올 때 `classNames/bind`를 사용하면 클래스를 넣어줄 때마다 `styles.[클래스]` 형식으로 할 필요없이, 사전에 미리 style을 받아와서 사용하게 설정하고 `cx('class1', 'class2')` 형태로 사용할 수 있습니다.

```javascript
import React from "react";
import classNames from "classNames/bind";
import styles from "./CSSModule.module.css";

const cx = classNames.bind(styles);

const CSSMoudle = () => {
  return (
    <div clasName={cx("wrapper", "inverted")}>
      안녕하세요, 저는 <span className="something">CSS Module!</span>
    </div>
  );
};
```

## 3. Sass와 함께 사용하기

Sass를 사용할때도 파일이름 뒤에 `.module.scss`를 입력해주면 CSS Module로 사용할 수 있습니다.

```javascript
import styles from "./CSSModule.module.scss";
```

## 4. CSS Module이 아닌 파일에서 CSS Module 사용하기

우리가 CSS Module에서 글로벌 클래스를 정의할 때 `:global`을 썼었던 것 처럼, CSS Module이 아닌 일반 .css/.scss 파일에서도 `:local`을 통해 CSS Module을 사용할 수 있습니다.

```css
/* 일반 CSS를 CSS Module 처럼 사용하기 */
:local .wrapper {
  /* 스타일 */
}
```

```scss
/* 일반 Sass를 CSS Module 처럼 사용하기 */
:local {
  .wrapper {
    /* 스타일 */
  }
}
```

## 5. 출처

- [다양한 방식의 리액트 컴포넌트 스타일링 방식 CSS, Sass, CSS Module, styled-components - velopert](https://velog.io/@velopert/react-component-styling)
