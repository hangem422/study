# Event

이벤트는 마우스를 클릭하거나 키보드를 누르는 등 사용자 액션에 의해 발생할 수도 있고, 혹은 비동기적 작업의 진행을 나타내기 위해 API가 생성할 수도 있습니다.

## 1. 이벤트 등록

이벤트 프로그래밍을 하기 위해서서는 이벤트의 대상에 이벤트 핸들러를 등록해야 합니다. 웹 브라우저에서는 아래와 같은 등록 방법을 제공하고 있습니다.

### 1.1 inline(인라인) 방식

이벤트를 등록하는 방법 중의 인라인 방식은 이벤트를 이벤트 대상의 태그 속성으로 지정하는 것입니다. 이벤트가 발생한 대상을 필요로하는 경우 `this`를 통해서 참조할 수 있습니다.

```html
<input type="button" onclick="alert('Hello World');" value="button" />
<input
  type="button"
  onclick="alert('Hello World, ' + this.value);"
  value="button"
/>
```

### 1.2 프로퍼티 리스너 방식

프로퍼티 리스너 방식은 이벤트 대상에 해당하는 객체의 프로퍼티로 이벤트를 등록하는 방식입니다. 인라인 방식에 비해서 HTML과 Javascript를 분리할 수 있습니다.

```javascript
const target = document.getElementById("target");
target.onclick = function () {
  alert("Hello World");
};
```

### 1.3 addEventListenr 방식

addEventListener은 이벤트를 등록하는 가장 권장되는 방식입니다. 이 방식을 이요하면 여러개의 이벤트 핸들러를 등록할 수 있습니다. 이 방식을 이용하면 여러개의 이벤트 핸들러를 등록할 수 있습니다.

```javascript
const target = document.getElementById("target");
target.addEventListener("click", function (event) {
  alert("Hello World, " + event.target.value);
});
```

이 방식은 IE8 이하에서는 호환되지 않습니다. IE8에서는 아래와 같은 `attachEvent` 메소드를 사용해야 합니다.

```javascript
const target = document.getElementById("target");
if (target.addEventListner) {
  target.addEventListener("click", function (event) {
    alert("Hello World, " + event.target.value);
  });
} else if (target.attachEvent) {
  target.attachEvent("onclick", function (event) {
    alert("Hello World, " + event.target.value);
  });
}
```

## 2. 이벤트 버블링 (Event Bubbling)

이벤트 버블링은 특정 화면 요소에서 이벤트가 발생했을 때 해당 이벤트가 더 상위의 화면 요소들로 전달되어가는 특성을 의미합니다. 브라우저는 특정 화면 요서에서 이벤트가 발생했을 때 그 이벤트를 최상위에 있는 화면 요소까지 전파시킵니다. 여기서 주의해야 할 점은 각 태그마다 이벤트가 등록되어 있어야 이벤트가 전달됩니다. 만약 이벤트가 특정 태그에만 달려 있다면 아래와 같은 동작 결과는 확인할 수 없습니다. 이와 같은 하위에서 사위 요소로 이벤트 전파 방식을 **이벤트 버블링**(Evetn Bubbling)이라고 합니다.

![이벤트 버블링](../_images/javascript-event01.png)

## 3. 이벤트 캡쳐 (Event Capture)

이벤트 캡쳐는 이벤트 버블링과 반대 방량으로 진행되는 이벤트 전파 방식입니다. `addEventListener()` API에서 옵션 객체에 `capture: true`를 설정해주면 됩니다. 그러면 해당 이벤트를 감지하기 위해 이벤트 버블링과 반대 방향으로 탐색합니다.

```javascript
const target = document.getElementById("target");
target.addEventListener(
  "click",
  function (event) {
    alert("Hello World, " + event.target.value);
  },
  { capture: true } // default 값은 false입니다.
);
```

![이벤트 캡쳐](../_images/javascript-event02.png)

## 4. 이벤트 전파 막기

원하는 화면 요소의 이벤트만 신경 쓰고 싶을 때는, `stopPropagation()` 웹 API를 사용합니다. 이 API는 이벤트가 전파되는 것을 막습니다. 다라서, 이벤트 버블링의 경우에는 클릭한 요소의 이벤트만 발생시키고 상위 요소로 이벤트를 전달하는 것을 방해합니다. 그리고 이벤트 캡쳐의 경우에는 클릭한 요소의 최상위 요소의 이벤트만 동작시키고 하위 요소들로 이벤트를 전달하지 않습니다.

```javascript
function eventFunc(event) {
  event.stopPropagation();
}
```

## 5. 출처

- [event란 무엇인가? - WEBCLUB](https://webclub.tistory.com/340)
- [이벤트 버블링, 이벤트 캡처 그리고 이벤트 위임까지 - CAPTAIN PANGYO](https://joshua1988.github.io/web-development/javascript/event-propagation-delegation/)
