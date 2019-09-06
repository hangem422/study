#Sass

> **Sass**(Syntactically Awesome Style Sheets)는 **CSS pre-processor**로서, 복잡한 작업을 쉽게 도와주고 코드의 재활용성을 높여줄 뿐만 아니라 가독성을 높여주어 유지보수를 쉽게 해줍니다.

## 1. Sass 혹은 SCSS

**Sasss**는 처음에 들여쓰기의 감지를 그 핵심 특성으로 갖는 구문을 가리켰습니다. Sass를 유지하는 사람들은 Sassy Css를 의미하는 **SCSS**라는 CSS 친화적인 구문을 제공함으로써 Sass와 CSS 사이의 차이를 좁히기로 결정했습니다.

### .sass
```scss
$font-stack: Helvetica, sans-serif
$primary-color: #333

body
  font: 100% $font-stack
  color: $primary-color
```

### .scss
```scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% &font-stack;
  color: &primary-color;
}
```

### 사용하기

우선 node-sass라는 라이브러리를 설치합니다. 이 라이브러리는 Sass 를 변환해줍니다.

```bash
npm install --save node-sass
```

이제 .sass 파일을 `import`하여 사용할 수 있습니다.

```javascript
import './main.scss';
```

## 2. 문자열

대부분의 CSS 값들은 숫자 혹은 (주로 따옴표가 없는) 문자열이기 떄문에, Sass에서 문자열을 다룰 때는 어느정도 가이드라인을 따르는 것이 중요합니다.

### 인코딩

문자 인코딩과 관련한 잠재적인 문제를 피하기 위해서는, **메인 스타일시트**에서 `@charset` 지시어를 사용해 **UTF-8** 인코딩을 강제하는 것이 권장됩니다.

```scss
@charset 'utf-8';
```

### 따옴표

CSS에 문자열은 따옴표로 둘러싸일 필요가 없습니다. Sass 역시 문자열의 따옴표 둘라써일 것을 요구하지는 않지만, 다음과 같은 이유로 권장합니다.

- 색 이름은 따옴표가 없으면 색으로 취급되는데, 이는 심각한 문제로 이어질 수 있다.
- 대부분의 구문 강조기는 따옴표 없는 문자열을 지원하지 못한다.
- 전반적인 가독성에 도움이 된다.
- 문자열을 따옴표로 감싸지 않을 적절한 이유가 없다.

```scss
// 작은 따옴표를 사용합니다.
$direction: 'left';
```

```scss
// 특정 CSS 값은 따옴표로 싸여서는 안됩니다.
$font-type: sans-serif;
```

```scss
// 하나 혹은 여러개의 작은 따옴표를 포함하고 있다면 큰 따옴표로 문자열을 감싸는 것을 고려합니다.
@warn "You can't do that.";
```
## 2. 숫자

Sass에서 숫자는 단위가 없는 숫자부터 길이, 기간, 빈도, 각도 등에 이르기까지 모든 것을 포함하는 데이터 타입입니다. 덕분에 연산이 가능해집니다.

### 단위

길이를 다룰 때, **0**은 절대로 단위를 가져선 안됩니다.
```scss
$length: 0;
```

숫자에 단위를 붙이기 위해서는 1 단위를 곱해야 합니다.

```scss
$value: 42;
$length: $value * 1px;
```

값의 단위를 제거하기 위해서는 한 단위로 나누어야 합니다.

```scss
$length: 42px;
$value: &length / 1px;
```

### 연산

최상위 숫자 계산은 언제나 괄호로 감싸져야 합니다.

```scss
.foo {
  width: (100% / 3);
}
```

`+`, `-` 연산을 사용할 때는 단위를 언제나 통일시켜야 합니다. 이런 작업을 해야한다면 CSSDML `calc()` 함수를 사용합니다.

```scss
.foo {
  width: calc(100% -16px);
}
```

## 3. 변수

변수는 복사를 반복하지 않고도 값을 재사용할 수 있게 해줍니다. 가장 중요한 점은 값 수정을 매우 쉽게 만들어준다는 것입니다. 하지만 적절한 이유 없이 변수를 선언하면 안됩니다. 다음 기준을 충족할때만 생섬해야 합니다.

- 값이 적오도 두 번 반복된다.
- 값이 적어도 한 번으 수정될 가능성이 크다.
- 값의 실현은 모두 변수와 관련되어 있다.

```scss
$primary-color: #333;

body {
  color: &primary-color;
}
```

### 스코프

변수를 특정 **selector**에서 선언하면 해당 **selector**에서만 접근이 가능합니다. 전역 스코프에 이미 존재하는 변수를 내부 스코프에서 선얼할 떄, 지역 변수가 전역 변수를 가립니다.

```scss
$variable: 'initial value';

.local-scope::before {
  $varialbe: 'local value';

  content: $variable; // local value
}

.other-local-scope::before {
  content: $variable; // initial value
}
```

### !default 플래그

라이브러리나 프레임워크, 그리드 시스템, 혹은 배포되어 외부의 개발자들에 의해 사용될 Sass 소품을 개발할 떄는, 덮어쓰일 수 있도록 모든 환경설절 변수들을 `!default` 플래그를 붙여 정의합니다.

```scss
$vaseline: 1rem !default;
```

이렇게하면 개발자는 라이르러리를 `@import`하기 전에 재정의될 걱정 없이 변수를 정의할 수 있습니다.
```scss
$vaseline: 2em;

@import 'your-library';

// $baseline == 2em
```

### !global 플래그

`!global` 플래그는 지역 스코프로부터 전역 변수를 정의할 떄에 사용됩니다. 루트 레벨에서 변수를 정의할 떄는 생략합니다.

```scss
$baseline: 2em !global;
```

## 4. 리스트

어떤 타입의 값이든(리스트도 포함) 저장할 수 있게 의도된 **평면적인 데이터 구조**입니다. 다음 가이드 라인을 준수해야합니다.

- 80자 줄에 안 들어갈 정도로 길면 반드시 여러 줄에 표기
- CSS 상에서 그대로 사용되지 않는 한, 언제나 쉼표로 분리
- 괄호로 감싼다

```scss
$font-stack: (
  'Helvetica',
  'Arial',
  sans-serif,
)
```

리스트에 새로운 아이템을 추가할 경우 제공된 API를 사용합니다.

```scss
$shadows: append($shadows, $shadow, comma);
```

## 5. 맵

맴은 키를 모든 유형의 값과 연결하는 자료 구조입니다. 키는 어떤 유형도 될 수 있습니다. 다음과 같이 작성합니다.

- 문자열인 키는 따옴표로 감싼다.
- 각각의 키/값 쌍은 새 줄을 차지한다.
- 각 키/값 뒤에는 쉼표를 축가한다.

```scss
$breakpoints: (
  'small': 767px,
  'medium': 992px,
  'large': 1200px,
);
```

### 디버그

```scss
$toString: inspect($map);
$length: length($map);
$depth: if(function-exists('map-depth'), map-depth($map), null);
$keys: map-keys($map);
$value: map-get($map, 'small');

@function map-depth($map) {
  $level: 1;

  @each $key, $value in $map {
    @if type-of($value) == 'map' {
      $level: max(map-depth($value) + 1, $value);
    }
  }

  @return $level;
}
```

## 6. Extend

마치 선택자 B에도 연결된 것처럼 요소 A를 꾸미라고 Sass에게 말하는 것을 가능하게 합니다. `extend`는 사용을 하지 않는 것이 권장됩니다. 또한 `@media` 블록과는 제대로 작동하지 않습니다.

```scss
.foo {
  content: 'foo';
}

.bar {
  @extend .foo;
}
```

## 7. Mixins

믹스인은 Sass 언어 전체에서 가장 많이 사용되는 기능 주 하나이며, 항상 같은 모습을 보이는 CSS 속성들의 그룹들을 재사용할 수 있게 해줍니다.

```scss
@mixin size($width, $height: $width) {
  width: $width;
  height: $height;
}
```

### 매개변수 리스트

믹스인에 들어가는 매개변수의 개수를 알 수 없을 떄는, 리스트 대신 항상 `arglist`를 사용합니다.

```scss
@mixin shdows($shadows...) {
  // type-of($shdows) == 'arglist'
}
```

리스트나 맵을 함수 / 믹스인에 매개변수 리스트로 전달해 일련의 매개변수들로 읽히도록 할 수 있습니다.

```scss
@mixin dummy($a, $b, $c) {
  //...
}

$list: (true, 42, 'kittens');
@include dummy($list...);

$map: (
  'c': 'kittens',
  'a': true, 
  'b' 42,
);
@include dummy($params...);
```

### 벤더 프리픽스

지원이 미비하거나 부분적으로 지원되는 CSS 속성을 위한 벤더 프리픽스 처리용 믹스인을 정의할 수 있습니다. 하지만 `Flexbox` 같이 복잡한 폴리필이 필요한 경우 처리하지 못합합니다. 가능한 `Autoprefixer`를 사용하는게 좋습니다.

```scss
@mixin prefix($property, $value, $prefixes: ()) {
  @each $prefix in $prefixes {
    -#{$prefix}-#{property}: $value;
  }

  #{$property}: $value;
}
```

```scss
.foo {
  @include prefix(transform, rotate(90deg), ('webkit', 'ms'));
}
```

## 8. 브레이크 포인트

미더어 쿼리가 특정 기기에 의존해서는 안되기 때문에 브레이크 포인트는 기기의 이름이 아니라 보다 보편적인 것을 따라서 이름 지어야 합니다.

```scss
$breakpoints: (
  'medium': (min-width: 800px),
  'large': (min-width: 1000px),
  'huge': (min-width: 1200px),
)
```

### 브레이크 포인트 메니저

getter 함수로 읽을 수 있는 브레이크 포인트 맵을 사용합니다.

```scss
@mixin respond-to($breakpoint) {
  $raw-query: map-get($breakpoints, $breakpoint);

  @if $raw-query {
    $query: if(
      type-of($raw-query) == 'string',
      unquote($raw-query),
      inspect($raw-query)
    );

    @media #{$query} {
      @content;
    }
  } @else {
    @error 'No value found for `#{$breakpoint}`.';
  }
}
```

### 미디어 쿼리 사용

미디어 쿼리를 어디에 작성해야 하는지에 대해서, 선택자 안에 넣는지 분리시키는지는 자유입니다.

```scss
// Sass
.foo {
  color: red;

  @include respond-to('medium') {
    color: blue;
  }
}
```

```scss
// CSS
.foo {
  color: red;
}

@media (min-width: 800px) {
  .foo {
    color: blue;
  }
}
```

## 9. 조건문

코드에 복잡한 놀리를 갖고 있는게 아니라면, 일상적인 스타일시트에선 조건문이 필요하지 않습니다. 조건문이 존재하는 가장 큰 이유는 라이브러리와 프레임워크입니다.

```scss
@if $support-legacy {
  // ...
} @else {
  // ...
}
```

거짓 값을 테스트할 때는 `not` 키워드를 사용합니다.

```scss
@if not $value == 42 {
  // ...
}
```

## 10. 반복문

Sass는 **리스트**와 **맵** 같은 복잡한 데이터 구조를 제공하기 떄문에, 그 개체들을 반복할 수 있는 방법 역시 제공합니다.

### Each

`@each` 반복문은 가장 많이 사용되는 반복문입니다.

```scss
// List에서 사용
@each $theme in $themes {
  .section-#{theme} {
    background-color: map-get($colors, $theme);
  }
}

// Map에서 사용
@each $key, $value in $map {
  .section-#{key} {
    background-color: $value;
  }
}
```

### For

`@for` 반목문은 CSS의 `nth-*` 가상 클래스와 결합되었을 때 유용하게 사용할 수 있습니다.

```scss
@for $i from 1 through 10 {
  .foo:nth-of-type(#{$i}) {
    border-color: hsl($i * 36, 50%, 50%);
  }
}
```

### While

`while` 반복문은 실제 Sass 프로젝트에서 전혀 용도가 없습니다. 특히 내부에서 반복문을 중단시킬 방법이 없기 떄문입니다. **사용하지 마세요.**

## 11. 함수

**Function**은 **mixin**과 비슷합니다. 차이점은 **mixin**은 style markup을 반환하지만, **function**은 `@return` directive를 통하여 값을 반환합니다.

```scss
@function calc-percent($target, $container) {
  @return ($target / $container) * 100;
}

.my-module {
  width: calc-percent(650px, 1000px);
}
```

## 12. 경고와 오류

Sass에는 표준 출력 시스템에 내용을 표시하는 세 가지의 지시어가 있습니다.

- `@debug`
- `@warn`
- `@error` 오류는 경고와 달리 컴파일러의 진행을 막습니다. 이는 디버깅에 유용합니다.

## 13. 주석 쓰기

### C 스타일 주석

CSS 블록의 요점을 설명하는데 사용합니다. CSS로 컴파일 되었을 때 나타납니다.

```scss
/**
 * C 스타일 주석은 블록의 요점을 설명합니다.
 * 1. 요점 1번
 * 2. 요점 2번
 */
 .ellipsis {
   white-space: nowrap; /* 1 */
   text-overflow: ellipsis; /* 2 */
 }
```

### 인라인 주석
한정된 부분에 주석을 달 때는 **C 스타일** 대신 **인라인 주석**을 사용합니다. CSS로 컴파일 되었을 때 나타나지 않습니다.

```scss
// 한정된 부분의 주석은 인라인 주석을 사용합니다.
```

## 14. 선택자 내포

선택 내포는 짧은 선택자들을 서로 쪼개어 넣음으로서 긴 선택자를 산출해내는 방식을 제안합니다. 선택자를 파악하고 무슨 일이 이러나고 있는지 이해하기 힘들어질 위험이 커져, 가능한 선택자 내포를 피해야 합니다.

### 일번적인 규칙

```scss
// Sass
.foo {
  .bar {
    &:hover {
      color:red;
    }
  }
}
```

```scss
// CSS
.foo .bar:hover {
  color: red;
}
```

**현재 선택자 참조**(&)를 이용해 고급 선택자를 생성하는 것이 가능합니다.

```scss
// Sass
.foo {
  &-bar {
    color: red;
  }
}
```

```scss
// CSS
.foo-bar {
  color: red;
}
```

### 사용하는 경우

원래 선택자가 안에 가상 클래스와 가상 요소를 내포하는 것은 허용됩니다.

```scss
.foo {
  color: red;

  &:hover {
    color: green;
  }

  &::before {
    content: 'pseudo-element';
  }
}
```

`is-active` 같은 컴포넌트에 독립적인 상태 클래스를 사용할 때, 컴포넌트의 선택자 아래에 내포하여 정리하는 것은 문제 없습니다.

```scss
.foo{
  //..

  &.is-sctive {
    font-weight: bold;
  }
}
```

요소가 우연히 다른 특정 요소 안에 들어가 있다면 그 컴포넌트에 관한 모든 것을 한 곳에 유지하기 위해 내포를 사요하는 것은 문제가 없습니다.

```scss
.foo {
  //..

  .no-opacity & {
    display: none;
  }
}
```

## 15. CSS 규칙

CSS와 관련된 가이드라인에 더해, Sass에서는 다음 사항들에 관심을 기울여야 합니다.

- 지역 변수는 어떤 선언보다 먼저 선언되어야 하며, 새 줄 하나로 다른 선언들과 간격을 둔다.
- `@content`가 없는 믹스인 호출은 다른 선언보다 앞에 위치한다.
- 내포된 선택자는 언제나 새 줄 뒤에 온다.
- `@content`를 가진 믹스인 호출은 내포된 선택자보다 뒤에 위치한다.

```scss
.foo, .foo-bar
.baz {
  // 지역 변수는 가장 먼저
  $length: 42rem;

  // @content가 없는 믹스인 호출은 다른 선언보다 앞에
  @include ellipsis;
  @include size($length);
  display: block;
  overflow: hidden;
  margin: 0 auto;

  // 내포된 선택자는 언제나 새 줄 뒤에
  &:hover {
    color: red;
  }

  // @content를 가진 믹스인 호출 내포된 선택자보다 뒤에
  @include respond-to('samll') {
    overflow: visible;
  }
}
```

## 16. 7-1 패턴 설계

폴더 7개, 파일 1개를 사용한 패턴입니다. 7개의 다른 폴더에 채워넣는 모든 부분 파일과, 이들을 불러들여 CSS 스타일시트로 컴파일하는 루트 레벨에 잇는 하나의 파일(`main.scss`)을 갖습니다.

### BASE

`base/` 폴더는 프로젝트의 보일러플레이트 코드라고 부를 만한 것을 담습니다. 거기에선 리셋 파일, 타이포그래피 규칙, 자주 사용되는 HTML 요소에대한 표준 스타일을 정의하는 스타일시트가 포함됩니다.

- _reset.scss
- _typography.scss
- _base.scss

### LAYOUT

`layout/` 폴더에는 사이트나 어플리케이션의 레이아웃에 기여하는 모든 것들이 들어갑니다. 사이트의 주요 부분, 그리드 시스템 혹은 모든 폼의 스타일을 위한 스타일시트가 포함됩니다.

- _header.scss
- _footer.scss
- _slider.scss
- _navigation.scss
- _grid.scss
- _forms.scss

### COMPONENT

`component/` 폴더에는 더 작은 컴퍼넌트들이 들어갑니다. 컴포넌트는 단 한가지 일만을 하고, 독립적이며, 재사용이 가능하고 프로젝트 전체에 걸쳐 재사용돼야 합니다.

- _media.scss
- _carousel.scss
- _thumbnail.scss

### PAGES

만약 페이지에 한정된 스타일이 있다면, `pages/` 폴더 속에 페이지 이름을 따서 넣는 것이 좋습니다.

- _home.scss
- _contact.scss

### THEMES

큰 사이트와 어플리케이션에서 여러 다른 테마들을 갖는 것은 흔히 있느 입니다. `themes/` 폴더에 전부 모아둡니다.

- _theme.scss
- _admin.scss

### UTILS

`utils/` 폴더는 그 자체만드로 컴파일되었을 때 한줄의 CSS도 산출하지 않는 모든 Sass 도구와 헬퍼를 모읍니다. 모든 전역 변수, 함수 , 믹스인, 플레이스홀더가 포함됩니다.

- _variables.scss
- _mixins.scss
- _fucntions.scss
- _placeholders.scss

### VENDORS

`vendors/` 폴더에는 Normalize, Bootstrap, JQueryUI 등의 외부 라이브러리와 프레임워크에서 나오는 모든 CSS 파일을 넣어줍니다.

- _normalize.scss
- _bootstrap.scss
- _jquery-ui.scss
- _select2.scss

### MAIN

`main.scss` 파일은 전체 코드베이스에서 언더스코어로 시작하지 않는 유일한 Sass 파일입니다. 이 파일은 `@import`와 주석 외에는 아무것도 포함하지 않습니다. 파일들은 각자 아래의 순서대로 불러옵니다.

1. vendors/
2. utils/
3. base/
4. layout/
5. components/
6. pages/
7. themes/

```scss
@import 'abstracts/variables';
@import 'abstracts/functions';
//...

@import 'vendors/bootstrap';
@import 'vendors/jquery-ui';
//...

@import 'base/reset';
@import 'base/typography';
//...

@import 'layout/navigation';
@import 'layout/grid';
//...


@import 'components/buttons';
@import 'components/carousel';
//...

@import 'pages/home';
@import 'pages/contact';
//...

@import 'themes/theme';
@import 'themes/admin';
//...
```

## 17. 출처

- [https://velog.io/@velopert](https://velog.io/@velopert/react-component-styling)
- [Sass Guildelines](https://sass-guidelin.es/ko/)
- [VELOPERT.LOG](https://velopert.com/1712)