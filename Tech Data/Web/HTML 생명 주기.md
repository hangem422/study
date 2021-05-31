# HTML 생명 주기

## 1. DOMContentLoaded

브라우저가 HTML을 전부 읽고 DOM 트리를 완성하는 즉시 발생합니다. 이미지 파일(`<img>`)이나 스타일시트 등의 기타 자원은 기다리지 않습니다. `DOMContentLoaded` 이벤트는 `document` 객체에서 발생합니다.

```javascript
document.addEventListener("DOMContentLoaded", readyFunc);
// document.onDOMContentLoaded = ... 방식은 동작하지 않습니다.
```

### 1.1 DOMContentLoaded와 scripts

브라우저는 HTML 문서를 처리하는 도중에 `<script>` 태그를 만나면, DOM 트리 구성을 멈추고 `<script>`를 실행합니다. 스크립트 실행이 끝난 후에야 나머지 HTML 문서를 처리합니다. `<script>`에 있는 스크립트가 DOM 조작 관련 로직을 담고 있을 수 있기 때문에 이런 방지책이 만들어졌습니다. 따라서 `DOMContentLoaded` 이벤트 역시 `<script>` 안에있는 스크립트가 처리되고 난 후에 발생합니다. 하지만 이 규칙에는 두 가지 예외 사항이 있습니다.

1. `asnyc` 속성이 있는 스크립트는 `DOMContentLoaded`를 맞기 않습니다.
2. `document.createElement('script')`로 동적으로 생성되고 웹페이지에 추가된 스크립트는 `DOMContentLoaded`를 막지 않습니다.

## 1.2 DOMContentLoaded와 styles

외부 스타일시트는 DOM에 영향을 주지 않기 때문에 `DOMContentLoaded`는 외부 스타일시트가 로드되길 기다리지 않습니다. 그런데 한 가지 예외가 있습니다. 스타일시트를 불러오는 태그 바로 다음에 스크립트가 위치하면 이 스크립트는 스타일시트가 로드되기 전까지 실행되지 않습니다.

```html
<link type="text/css" rel="stylesheet" href="style.css" />
<script>
  alert("this is script");
</script>
```

이런 예외는 스크립트에서 스타일에 영향을 받는 요소의 프로퍼티를 사용할 가능성이 있기 때문에 만들어졌습니다. `DOMContentLoaded`는 스크립트가 로드되길 기다립니다. 위의 경우라면 당연히 스타일시트 역시 기다리게 됩니다.

## 1.3 브라우저 내장 자동완성

Firefox와 Chrome, Opera의 **폼 자동완성**(form autofill)은 `DOMContentLoaded`에서 일어납니다. 따라서 실행해야 할 스크립트가 길어서 `DOMContentLoaded` 이벤트가 지연된다면 자동완성 역시 뒤늦게 처리됩니다.

## 1.4 페이지 하단 스크립트와의 차이점

`script`를 페이지 하단에 위치시키면 아주 조금 더 빨리 호출될 수 있지만, 실질적으로 큰 차이는 없습니다. 스크립트를 `<head>`에 포함시키면 브라우저는 사용자에게 내용을 표시하기 전에 스크립트를 만나게 됩니다. Javascript는 인터프리터를 실행하고, 스크립트를 넘겨준 다음 인터프리터가 스크립트를 처리하는 동안 브라우저는 대기합니다. 하지만 페이지 끝에 `<script>` 태그를 포함하면 사용자에게 페이지가 표시될 때까지 브랑줘는 이 모든 작업을 수행하지 않습니다.

## 2. load

HTML로 DOM 트리를 만드는 것이 완성되었을 뿐만 아니라 이미지, 스타일시트 같은 외부 자원도 모두 불러오는 것이 끝났을 때 발생합니다. `load` 이벤트는 `onload` 프로퍼티를 통해서도 사용할 수 있습니다.

```javascript
window.onload = function () {
  // ...
};
```

## 3. onbeforeunload

사용자가 현제 페이지를 떠나 다른 페이지로 이동하려 할 떄나 창을 닫으려고 할 때 `beforeunload` 핸들러에서 추가 확인을 요청할 수 있습니다. `beforeunload` 이벤트를 취소하려 하면 브라우저는 사용자에게 확인을 요청합니다.

```javascript
window.onbeforeunload = function () {
  return false;
};
```

`false` 말고도 비어있지 않은 문자열을 반환하면 이벤트를 취소한 것과 같은 효과를 볼 수 있는데, 이는 역사적인 이유 때문에 남아있는 기능입니다. 과거엔 문자열을 반환하면 브라우저에서 이 문자열을 보여줬는데, 근래의 명세서에서는 이를 권장하지 않습니다. 문자열을 반환하도록 해도 얼럿창에 문자열이지 보이지 않게 된 이유는 몇몇 사이트 관리자들이 오해가 생길 법하거나 성가신 메시지를 띄우면서 `beforeunload`를 남용했기 때문입니다. 따라서 모던 브라우저에서는 `beforeunload` 이벤트를 취소할 때 보이는 메시지를 커스터마이징 할 수 없습니다.

```javascript
window.onbeforeunload = function () {
  return "저장되지 않은 변경사항이 있습니다. 정말 페이지를 떠나실 건 가요?";
};
```

## 4. onuload

`window` 객체의 `unload` 이벤트는 사용자가 페이지를 떠날 때, 즉 문서를 완전히 닫을 떄 실행됩니다. `unload` 이벤트에서 팝업창을 닫는 것과 같은 딜레이가 없는 작업을 수행할 수 있습니다.

하지만 분석 정보를 조내는 것은 예외사항에 속합니다. 메서드 `navigator.sendBeacon(url, data)`은 바로 이런 용도를 위해 만들어졌습니다. `sendBeacon`는 데이터를 백그라운드에서 전송합니다. 다른 페이지로 전환시 분석 정보는 제대로 서버에 전송되짐나, 딜레이가 없는 것이 바로 이 때문입니다. 메서드에 대한 자세한 설명은 [https://w3c.github.io/beacon](https://w3c.github.io/beacon)에서 찾아볼 수 있습니다.

```javascript
const analysticsData = {};

window.addEventListener("unload", function () {
  navigator.sendVeacon("/analystics", JSON.stringify(analysticsData));
});
```

- 요청은 POST 메서드로 전송됩니다.
- 요청 시 문자열뿐만 아니라 폼이나 `fetch`에서 설명하는 기타 포맷들도 보낼 수 있습니다. 대개는 문자열 형태의 객체가 전송됩니다.
- 전송 데이터는 64KB를 넘을 수 없습니다.
- 요청이 종료된 시점에서 브라우저가 다른 페이지로 전환을 마친 상태일 확률이 높습니다. 다라서 서버 응답을 받을 수 없습니다.

## 5. readyState

문서가 완전히 로 드된 후에 `DOMContentLoaded` 핸들러를 설정하면, 아마도 실행되지 않을 겁니다. 그런데 가끔은 문서가 로드되었는지 아닌지를 판단할 수 없는 경우가 있습니다. DOM이 완전히 구성된 후에 특정 함수를 실행해야 할 때는 DOM 트리 완성 여부를 알 수 없어 조금 난감합니다. 이럴 때 현재 로딩 상태를 알려주는 `document.readyState` 프로퍼티를 사용할 수 있습니다. 프로퍼티의 값은 세 종류가 있습니다.

- **loading**: 문서를 불러오는 중
- **interactive**: 문서가 완전히 불러옴
- **complete**: 문서를 비롯한 이미지 등의 리소스들도 모두 불러옴

```javascript
function work() {
  // ...
}

if (document.readyState === "loading") {
  document.addEventListener("DOMContentLoaded", work);
} else {
  work();
}
```

이 이외도 상태가 변경되었을 때 실행되는 이벤트 `readystatechange`를 사용하면 상태에 맞게 원하는 작업을 할 수 있습니다.

```javascript
document.addEventListener("readystatechange", () => {
  console.log(document.readyState);
});
```

이렇게 아주 오래전부터 있었던 readystatechange 이벤트라는 대안을 사용해도 문서 로딩 상태를 파악할 수 있습니다. 그런데 이 이벤트는 요즘엔 잘 사용하지 않습니다.

## 6. 예시

```html
<script>
  log("initial readyState:" + document.readyState);

  document.addEventListener("readystatechange", () =>
    log("readyState:" + document.readyState)
  );
  document.addEventListener("DOMContentLoaded", () => log("DOMContentLoaded"));

  window.onload = () => log("window onload");
</script>

<iframe src="iframe.html" onload="log('iframe onload')"></iframe>

<img src="http://en.js.cx/clipart/train.gif" id="img" />
<script>
  img.onload = () => log("img onload");
</script>
```

1. initial readyState:loading
2. readyState:interactive
3. DOMContentLoaded
4. iframe onload
5. img onload
6. readyState:complete
7. window onload

## 7. 출처

- [DOMContentLoaded, load, beforeunload, unload 이벤트 - JAVASCRIPT.INFO](https://ko.javascript.info/onload-ondomcontentloaded#ref-367)
