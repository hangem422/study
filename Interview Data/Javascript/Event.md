# Event

브라우저는 처리해야 할 특정 사건이 발생하면 이를 감지하여 **이벤트**(Event)를 **발생**(Trigger) 시킵니다. 만약 애플리케이션이 특정 타입의 이벤트에 대해 반응하여 어떤 일을 하고 싶다면 해당하는 타입의 이벤트가 발생했을 때 호출될 함수를 브라우저에게 알려 호출을 위임합니다. 이때 이벤트가 발생했을 때 호출될 함수를 **이벤트 핸들러**(Event Handler)라 하고, 이벤트가 발생했을 때 브라우저에게 이벤트 핸들러의 호출을 위임하는 것을 **이벤트 핸들러 등록**이라 합니다.

## 1. 이벤트 핸들러 등록

**이벤트 핸들러**(Event Handler, Event Listener)는 이벤트가 발생했을 때 브라우저에 호출을 위임한 함수입니다. 이벤트 핸들러를 등록하는 방법은 3가지 입니다.

### 1.1 이벤트 핸들러 어트리뷰트 방식

HTML 요소의 어트리뷰트 중에는 이벤트에 대응하는 이벤트 핸들러 어트리뷰트가 있습니다. 이벤트 핸들러 어트리뷰트의 이름은 `onclick`과 같이 on 접두사와 이벤트 종류를 나타내는 이벤트 타입으로 이루어져 있습니다. 주의할 점은 이벤트 핸들러 어트리뷰트 값으로 함수 참조가 아닌 함수 호출문 등의 문을 할당하는 것입니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <button onclick="sayHi('Lee')">Click me!</button>
  </body>
  <script>
    function sayHi(name) {
      console.log(`Hi! ${name}.`);
    }
  </script>
</html>
```

이벤트 핸들러 어트리뷰트 값은 사실 암묵적으로 생성될 이벤트 핸들러의 함수 몸체를 의미합니다. 즉, `onclick="sayHi('Lee')"` 어트리뷰트는 파싱되어 다음과 같은 함수를 암묵적으로 생성하고, 이벤트 핸들러 어트리뷰트를 이름과 동일한 키 `onclick` 이벤트 핸들러 프로퍼티에 할당합니다. 이처럼 동작하는 이유는 이벤트 핸들러에 인수를 전달하기 위해서입니다.

```javascript
function onclick(event) {
  sayHi("Lee");
}
```

이벤트 핸들러 어트리뷰트 방식은 오래된 코드에서 간혹 이 방식을 사용한 것이 있기 때문에 알아둘 필요는 있지만 더는 사용하지 않는 것이 좋습니다. HTML과 자바스크립트는 관심사가 다르므로 혼재하는 것보다 분리하는 것이 좋습니다. 하지만 모던 자바스크립트에서는 이벤트 핸들러 어트리뷰트 방식을 사용하는 경우가 있습니다. **CBD**(Component Based Development) 방식의 Angular/React/Svelte/Vue.js 같은 프레임워크/라이브러리에서는 이벤트 핸들러 어트리뷰트 방식으로 이벤트를 처리합니다. CBD에서는 HTML, CSS 자바스크립트를 관심사가 다른 개별적인 요소가 아닌, 뷰를 구성하기 위한 구성 요소로 보기 때문에 관심사가 다르다고 생각하지 않습니다.

### 1.2 이벤트 핸들러 프로퍼티 방식

`window` 객체와 `Domcumet`, `HTMLElement` 타입의 DOM 노드 객체는 이벤트에 대응하는 이벤트 핸들러 프로퍼티를 가지고 있습니다. 이벤트 핸들러 프로퍼티의 키는 이벤트 핸들러 어트리뷰트와 마찬가지로 `on` 접두사와 이벤트 종류를 나타내는 이벤트 타입으로 이루어져 있습니다. 이벤트 핸들러 프로퍼티에 함수를 바인딩하면 이벤트 핸들러가 등록됩니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <button>Click me!</button>
  </body>
  <script>
    const $button = document.querySelector("button");

    $button.onclick = function () {
      console.log("button click");
    };
  </script>
</html>
```

앞서 살펴본 **이벤트 핸들러 어트리뷰트 방식**도 결국 DOM 노드 객체의 이벤트 핸들러 프로퍼티로 변환되므로 결과적으로 이벤트 핸들러 프로퍼티 방식과 동일하다고 할 수 있습니다. **이벤트 핸들러 프로퍼티 방식** 은 **이벤트 핸들러 어트리뷰트 방식**의 HTML과 자바스크립트가 뒤섞이는 문제를 해결할 수 있습니다. 하지만 이벤트 핸들러 프로퍼티에 하나의 이벤트 핸들러만 바인딩할 수 있다는 단점이 있습니다.

### 1.3 addEventListener 메서드 방식

DOM Level 2에서 도입된 `EventTarget.prototype.addEventListener` 메서드를 사용하여 이벤트 핸들러를 등록할 수 있습니다. 앞서 살펴본 **이벤트 핸들러 어트리뷰트 방식**과 **이벤트 핸들러 프로퍼티 방식**은 DOM Level 0부터 제공되던 방식입니다.

```
EventTarget.addEventListener('eventType', functionName [, useCaputer]);
```

- `addEventListener` 메서드의 첫 번쨰 매개변수에는 이벤트의 종류를 나타내는 문자열인 이벤트 타입을 전달합니다. 이떄 이벤트 핸들러 프로퍼티 방식과는 달리 `on` 접두사를 붙이지 않습니다.
- 두 번째 매개변수에는 이벤트 핸들러를 전달합니다.
- 마지막 매개변수에는 이벤트를 캐치할 이벤트 전파 단계(캡처링 또는 버블링)를 지정합니다. 생략하거나 `false`를 지정하면 버블링 단계에서 이벤트를 캐치하고, `true`를 지정하면 캡처링 단계에서 이벤트를 캐치합니다.

이벤트 핸들러 프로퍼티 방식은 이벤트 핸들러 프로퍼티에 이벤트 핸들러를 바인딩하지만 `addEventListener` 메서드에는 이벤트 핸들러를 인수로 전달합니다. 동일한 HTML 요소에서 발생한 동일한 이벤트에 대해 인벤트 핸들러 프로퍼티 방식과 `addEventListener` 메서드 방식을 모두 사용하여 이벤트 핸들러를 등록하면 서로 아무런 영향을 주지 않습니다. 따라서 2개의 이벤트 핸들러가 모두 호출됩니다.

동일한 HTML 요소에서 발생한 동일한 이벤트에 대해 이벤트 핸들러 프로퍼티 방식은 하나 이상의 이벤트 핸들러를 등록할 수 없지만, `addEventListener` 메서드는 하나 이상의 이벤트 핸들러를 등록할 수 있습니다. 단, `addEventListener` 메서드를 통해 참조가 동일한 이벤트 핸들러를 중복 등록하면 하나의 이벤트 핸들러만 등록됩니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <button>Click me!</button>
  </body>
  <script>
    const $button = document.querySelector("button");

    $button.onclick = function () {
      console.log("[이벤트 핸들러 프로퍼티 방식] button click");
    };

    $button.addEventListener("click", function () {
      console.log("[addEventListener 메서드 방식] button click 1");
    });

    $button.addEventListener("click", function () {
      console.log("[addEventListener 메서드 방식] button click 2");
    });
  </script>
</html>
```

## 2. 이벤트 핸들러 제거

`addEventListener` 메서드로 등록한 이벤트 핸들러를 제거하려면 `EventTarget.prototype.removeEventListener` 메서드를 사용합니다. `removeEventListener` 메서드에 전달할 인수는 `addEventListener` 메서드와 동일합니다. 단, `addEventListener` 메서드에 전달한 인수와 `removeEventListener` 메서드에 전달한 인수가 일치하지 않으면 이벤트 핸들러가 제거되지 않습니다. 따라서 무명 홤수를 이벤트 핸들러로 등록한 경우 제거할 수 없습니다. 이벤트 헨들러를 제거하려면 이벤트 핸들러의 참조를 변수나 자료구조에 저장하고 있어야 합니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <button>Click me!</button>
  </body>
  <script>
    const $button = document.querySelector("button");
    const handleClick = () => console.log("button click");

    $button.addEventListener("click", handleClick);

    // 전달한 인수가 일치하지 않으면 이벤트 핸들러가 제거되지 않습니다.
    $button.removeEventListener("click", handleClick, true); // 실패
    $button.removeEventListener("click", handleClick); // 성공
  </script>
</html>
```

기명 이벤트 핸들러 내부에서 `removeEventListner` 메서드를 호출하여 이벤트 핸들러를 제거하는 것은 가능합니다. 이때 이벤트 핸들러는 단 한번만 호출됩니다. 기명 함수를 이벤트 핸들러로 등록할 수 없다면 호출된 함수, 즉 함수 자신을 가리키는 `argument.callee`를 사용할 수도 있습니다. `arguments.callee`는 코드 최적화를 방해하므로 strict mode에서 사용이 금지됩니다. 따라서 가급적 이벤트 핸들러의 참조를 변수나 자료구조에 저장하여 제거하는 편이 좋습니다.

```javascript
$button.addEventListener("click", function foo() {
  console.log("button click");

  $button.removeEventListener("click", foo);
  // $button.removeEventListener("click", arguments.callee);
});
```

이벤트 핸들러 프로퍼티 방식으로 등록한 이벤트 핸들러는 `removeEventListener` 메서드로 제거할 수 없습니다. 이벤트 핸들러 프로퍼티 방식으로 등록한 핸들러르 제거하려면 이벤트 핸들러 프로퍼티에 `null`을 할당합니다.

## 3. 이벤트 전파

이벤트 객체는 이벤트를 발생시킨 DOM 요쇼인 **이벤트 타깃**(Event Target)을 중심으로 DOM 트리를 통해 전파됩니다. 이를 **이벤트 전파**(Event Popagation)라고 합니다. 이벤트 전파는 이벤트 객체가 전파되는 방향에 따라 다음과 같이 3단계로 구분한 수 있습니다.

![이벤트 전파](../_images/javascript-event01.png)

1. **캡쳐링 단계**(Capturing Phase): 이벤트가 상위 요소에서 하위요소 방향으로 전파됩니다.
2. **타깃 단계**(Target Phase): 이벤트가 이벤트 타깃에 도달합니다.
3. **버블링 단계**(Bubbling phase): 이벤트가 하위 요소에서 상위요소 방향으로 전파됩니다.

요소를 클릭하면 클릭 이벤트가 발생하여 클릭 이벤트 객체가 생성되고 클릭된 요소가 이벤트 타깃이 됩니다. 이떄 클릭 이벤트 객체는 `window`에서 시작해서 이벤트 타깃 방향으로 전파됩니다. 이것이 캡처링 단계입니다. 이후 이벤트 객체는 이벤트를 발생시킨 이벤트 타깃에 도달합니다. 이것이 타깃 단계입니다. 이후 이벤트 객체는 이 이벤트 타깃에서 시작해서 `window` 방향으로 전파됩니다. 이것이 버블링 단계입니다.

이벤트 핸들러 어트리뷰트/프로퍼티 방식으로 등록한 이벤트 핸들러는 타깃 단계와 버블링 단계의 이벤트만 캐치할 수 있습니다. 하지만 `addEventListener` 메서드 방식으로 등록한 이벤트 핸들러는 타깃 단계와 버블링 단계뿐만 아니라 캡쳐링 단계의 이벤트도 선별적으로 캐치할 수 있습니다. 캡처링 단계의 이벤트를 캐치하려면 `addEventListener` 메서드의 3번쨰 인수로 `true`를 전달해야 합니다. 3번째 인수를 생략하거나 `false`를 전달하면 타깃 단계와 버블링 단계의 이벤트만 캐치할 수 있습니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="furits">
      <li id="apple">Apple</li>
      <li id="banana">Banana</li>
      <li id="oramge">Orange</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById("fruits");
    const $banana = document.getElementById("banana");

    $fruits.addEventListener(
      "click",
      (e) => {
        console.log(e.eventPhase); // 1: 캡쳐링 단계
      },
      true
    );

    $banana.addEventListener("click", (e) => {
      console.log(e.eventPhase); // 2: 타깃 단계
    });

    $fruits.addEventListener("click", (e) => {
      console.log(e.eventPhase); // 3: 버블링 단계
    });
  </script>
</html>
```

대부분의 이벤트는 캡처링과 버블링을 통해 전파됩니다. 하지만 다음 이벤트는 버블링을 통해 전파되지 않습니다. 이 이벤트들은 버블링을 통해 이벤트를 전파하는지 여부를 나타내는 이벤트 객체의 공통 프로퍼티 `event.bubbles`의 값이 모두 `false`입니다.

- **포커스 이벤트**: `focus`, `blur`
- **리소스 이벤트**: `load`, `unload`, `abort`, `error`
- **마우스 이벤트**: `mouseenter`, `mouseleave`

위 이벤트는 버블링되지 않으므로 이1벤트 타깃의 상위 요소에서 위 이벤트를 캡치하려면 캡처링 단계의 이벤트를 캐치해야 합니다. 하지만 위 이벤트를 상위 요소에서 캐치해야 할 경우는 그리 많지 않지만 위 이벤트를 상위 요소에서 캐치해야 한다면 대체할 수 있는 이벤트가 존재합니다. 예를 들어, `focus`/`blur` 이벤트는 `focusin`/`focusout`으로, `mouseenter`/`mouseleave`는 `mouseover`/`mouseout`으로 대체할 수 있습니다. 따라서 캡처링 단계에서 이벤트를 캐치해야 할 경우는 거의 없습니다.

## 4.이벤트 위임

**이벤트 위임**(Event Delegation)은 여러 개의 하위 DOM 요소에 각각 이벤트 핸들러를 등록하는 대신 하나의 상위 DOM 요소에 이벤트 핸들러를 등록하는 방법을 맗합니다. 이벤트는 이벤트 타깃은 물론 상위 DOM 요소에서도 캐치할 수 있습니다. 이벤트 위임을 통해 상위 DOM 요소에 이벤트 핸들러를 등록하면 여러 개의 하위 DOM 요소에 이벤트 핸들러를 등록할 필요가 없습니다. 또한 동적으로 하위 DOM 요소를 추가하더라도 일일이 추가된 DOM 요소에 이벤트 핸들러를 등록할 필요가 없습니다.

일반적으로 이벤트 객체의 `target` 프로퍼티와 `currentTarget` 프로퍼티는 동일한 DOM 요소를 가리키지만 이벤트 위임을 통해 상위 DOM 요소에 이벤트를 바인딩한 경우 이벤트 객체의 `target` 프로퍼티와 `currentTarget` 프로퍼티는 다른 DOM 요소를 가리킬 수 있습니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      #fruits {
        display: flex;
        list-style-type: none;
        padding: 0;
      }

      #fruits li {
        width: 100px;
        cursor: pointer;
      }

      #fruits .active {
        color: red;
        text-decoration: underline;
      }
    </style>
  </head>
  <body>
    <nav>
      <ul id="fruits">
        <li id="apple" class="active">Apple</li>
        <li id="banana">Banana</li>
        <li id="orange">Orange</li>
      </ul>
    </nav>
    <div>선택된 내비게이션 아이템: <em class="msg">apple</em></div>
  </body>
  <script>
    const $fruits = document.getElementById("fruits");
    const $msg = document.querySelector(".msg");

    function activate({ target }) {
      if (!target.matches("#fruits > li")) return;
      $msg.textContent = target.id;

      [...$fruits.children].forEach(($fruit) => {
        $fruit.claassList.toggle("active", $fruit === target);
      });
    }

    $fruits.onclick = activate;
  </script>
</html>
```

## 5. DOM 요소의 기본 동작의 조작

### 5.1 DOM 요소의 기본 동작 중단

DOM 요소는 저마다 기본 동작이 있습니다. 예를 들어 `a` 요소를 클릭하면 `href` 어트리뷰트에 지정된 링크로 이동하고, `checkbox` 또는 `radio` 요소를 클릭하면 체크 또는 헤제됩니다. 이벤트 객체의 `preventDefault` 메서드는 이러한 DOM 요소의 기본 동작을 중단시킵니다.

```javascript
document.querySelector("a").onclick = (e) => {
  e.preventDefault();
};
```

### 5.2 이벤트 전파 방지

이벤트 객체의 `stopPropagation` 메서드는 이벤트 전파를 중지시킵니다.

```javascript
document.querySelector(".btn").onclick = (e) => {
  e.stopPropagation();
  e.target.style.color = "blue";
};
```

## 6. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
