# 20. strict mode

## 20.1 strict mode란?

ES5부터 **strict mode**(엄격 모드)가 추가되었습니다. strict mode는 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킵니다. ES6에 도입된 클래스와 모듈은 기본적으로 strict mode가 적용됩니다. ESLint 같은 린트 도구를 사용해서 strict mode와 윻사한 효과를 얻을 수 있습니다. 린트 도구는 **정적 분석**(Static Analysis) 기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 원인을 리포팅해주는 유용한 도구입니다. 린트 도구는 strict mode가 제한하는 오류는 물론 코딩 컨벤션을 설정 파일 형태로 정의하고 강제할 수 있기 때문에 더욱 강력한 효과를 얻을 수 있습니다.

## 20.2 strict mode의 적용

strict mode를 적용하려면 전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가합니다. 코드의 선두에 `'use strict';`를 위치시키지 않으면 strict mode가 제대로 동작하지 않습니다. 전역의 선두에 추가하면 스크립트 전체에 strcit mode가 적용됩니다.

```javascript
"use strict";

function foo() {
  x = 10; // ReferenceError: x is not defined
}

foo();
```

함수 몸체의 선두에 추가하면 해당 함수와 중첩 함수에 strict mode가 적용됩니다.

```javascript
function foo() {
  "use strict";

  x = 10; // ReferenceError: x is not defined
}

foo();
```

## 20.3 전역에 strict mode를 적용하는 것은 피하자

전역에 적용한 strict mode는 스크립트 단위로 적용됩니다. 스크립트 단위로 적용된 strict mode는 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용됩니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      "use strict";

    </script>
    <script>
      x = 1; // 에러가 발생하지 않습니다.
    </script>
  </body>
</html>
```

하지만 strict mode 스크립트와 non-strict 스크립트를 혼용하는 것은 오류를 발생시킬 수 있습니다. 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않습니다. 이러한 경우 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용합니다.

```javascript
(function () {
  "use strict";
  // Do Something ...
})();
```

## 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

어떤 함수는 strict mode를 적용하고 어떤 함수는 strict mode를 적용하지 않는 것은 바람직하지 않으며 모든 함수에 일일이 strict mode를 적용하는 것도 번거로운 일입니다. 그리고 strict mode가 적용된 함수가 참조할 외부의 컨텍스트에 strict mode를 적용하지 않는다면 이 또한 문제가 발생할 수 있습니다. 따라서 strict mode는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직합니다.

```javascript
(function () {
  var let = 10;

  function foo() {
    "use strict";

    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
})();
```

## 20.5 strict mode가 발생시키는 에러

### 20.5.1 암묵적 전역

선언하지 않은 변수를 참조하면 ReferenceError가 발생합니다.

```javascript
(function () {
  "use strict";

  x = 1; // ReferenceError: x is not defined
})();
```

### 20.5.2 변수, 함수, 매개변수의 삭제

`delete` 연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생합니다.

```javascript
(function () {
  "use strict";

  var x = 1;
  delete x; // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a; // SyntaxError: Delete of an unqualified identifier in strict mode.
  }

  delete foo; // SyntaxError: Delete of an unqualified identifier in strict mode.
})();
```

### 20.5.3 매개변수 이름의 중복

중복된 매개변수 이름을 사용하면 SyntaxError가 발생합니다.

```javascript
(function () {
  "use strict";

  // SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }

  console.log(foo(1, 2));
})();
```

### 20.5.4 with 문의 사용

`with` 문을 사용하면 SyntaxError가 발생합니다. `with` 문은 전달된 객체를 스코프 체인에 추가합니다. `with` 문은 동일한 객체의 프로퍼티를 본복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠지는 문제가 있습니다. `with` 문은 사용하지 않는 것이 좋습니다.

```javascript
(function () {
  "use strict";

  // SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

### 추가 자료: with를 사용하지 마라

> 추가 자료 출처: Effactive JavaScript - 데이비드 허먼

자바스크립트는 모든 변수를 동일하게 처리합니다. 자바스크립트 변수를 스코프 내에서 찾을 때, 가장 안쪽에서부터 시작해 바깥쪽으로 넓혀가면서 찾습니다. `with` 선언문은 변수 스코프를 대표하는 것처럼 객체를 처리하여, `with` 블록 내부에서는 주어진 변수 이름을 가진 프로퍼티부터 찾기 시작합니다. 객체 내에서 프러퍼티가 발견되지 않는다면, 그때는 외부 스코프로 이어서 탐색합니다.

`with`에 제공된 객체에 어떤 프러피티가 있는지 혹은 없는지 얼마나 확신할 수 있을까요? `with` 블록 내에서 외부 변수로의 모든 참조는 암묵적으로 `with` 객체 내에, 혹은 `prototype` 객체 내에 같은 이름의 프로퍼티가 없다는 것을 가정합니다. 하지만 프로그램의 다른 부분에서 `with` 객체와 이 객체의 `prototype`을 생성하거나 수정할 수 있으며, 이 `prototype`이 그런 가정을 공유하지 않을 수도 있습니다. 이러한 다른 코드에서 여러분이 작성한 지역 코드가 어떤 지역 변수를 사용하게 될지 반드시 찾아 읽어야 할 필요는 없습니다.

```javascript
function f(x, y) {
  with (Math) {
    return min(round(x), sqrt(y));
  }
}

Math.x = 0;
Math.y = 0;
console.log(f(2, 9)); // 0
```

변수 스코프 객체 네임스페이스 사이의 충돌은 `with` 블록을 극도로 불안정하게 만듭니다. 아마도 `Math`에 `x`와 `y` 프로퍼티를 추가할 사람은 아무도 없을 것입니다. 하지만 어떤 특정 객체가 수저될지, 혹은 알지 못하는 프로퍼티를 가지고 있을지 예측하기란 언제나 쉽지 않습니다. 또한 모두 알다시피, 사람에게 예측하기 어려운 기능은 최적화 컴파일러도 예측하기 쉽지 않습니다. 보통, 자바스크립트 스코프는 효율적인 내부 데이터 구조로 표현될 수 있고 변수 탐색이 빠르게 수행될 수 있습니다. 하지만 `with` 블록은 본문의 모든 변수를 찾기 위해 객체의 프로토타입 체인을 탐색하게 하고, 보통 일반적인 블록에 비해 현저히 느리게 실행될 것입니다.

자바스크립트에는 `with`에 대안으로 사용할 만한 직접적인 대체 기능은 없습니다. 어던 경우에는 객체를 단순히 짧은 이름의 변수로 바인딩하는 것이 최선의 대안입니다. 다른 경우에는, 지역 변수를 명시적으로 연관된 프로퍼티와 바인딩하는 것이 최선의 방법입니다.

```javascript
function f(x, y) {
  const min = Math.min;
  const round = Math.round;
  const squr = Math.sqrt;

  return min(round(x), sqrt(y));
}
```

## 20.6 strict mode 적용에 의한 변화

### 20.6.1 일반 함수의 this

strict mode에서 함수를 일반 함수로서 호출하면 `this`에 `undefined`가 바인딩됩니다. 생성자 함수가 아닌 일반 함수 내부에서는 `this`를 사용할 필요가 없기 때문입니다. 이때 에러는 발생하지 않습니다.

```javascript
(function () {
  "use strict";

  function foo() {
    console.log(this);
  }

  foo(); // undefined
  new foo(); // foo
})();
```

### 20.6.2 arguments 객체

strict mode에서는 매개변수에 전달된 인수를 재항당하여 변경해도 arguments 객체에 반영되지 않습니다.

```javascript
(function (a) {
  "use strict";

  a = 2;
  console.log(arguments); // { 0: 1 }
})(1);
```
