# Resize Observer

Reszie Oberver는 설정한 요소의 크기 변화를 관찰하며, 크기 변화를 제어할 경우 발생할 수 있는 무한 콜백 루프나 **순환 종속성**(Circular Dependency) 등의 다양한 문제 없이 사용할 수 있습니다.

## 1. Polyfill

ResizeOberver는 표준화 초안 단계이므로 브라우저 버전에 따라 네이티브 ReesizeObserver의 사용법이 다를 수 있으니 **폺리필**(Polyfill) 사용을 적극 추천합니다. 이 포스트에서는 `@juggle/resize-observer`를 사용합니다. 표준 사양이 크게 변경되면 이 라이브러리에 변경 사항이 있을 수 있으므로 주 버전 충돌에 주의하여 사용합니다.

## 2. Installation and Basic usage

```bash
$ npm install @juggle/resize-observer
```

```javascript
import ResizeObserver from "@juggle/resize-observer";
```

### 2.1 ResizeObserver

`new ResizeObserver`를 통해 생성한 인스턴스로 **관찰자**(Observer)를 초기화하고 관찰할 **대상**(Element)을 지정합니다. 생성자는 1개의 callback 함수를 인수로 가집니다.

```javascript
const ro = new ResizeObserver(callback);
ro.observe(element);
```

### 2.2 Callback

관찰할 대상이 등록되거나 크기에 변화가 생기면 관찰자는 콜백을 실행합니다. 콜백은 두개의 인수 `entries`, `observer`를 가집니다.

```javascript
const ro = new ResizeObserver((entries, observer) => {});
ro.observe(element);
```

#### 2.2.1 entries

`entries`는 `ResizeObserverEntry` 인스턴스의 배열입니다. `ResizeObserverEntry`는 다음 속성들을 포함합니다.

- **contentRect**(legacy): 관찰 대상의 사각형 정보 ([DOMRectReadOnly](https://developer.mozilla.org/en-US/docs/Web/API/DOMRectReadOnly))
- **target**(legacy): 관찰 대상 요소 ([Element](https://developer.mozilla.org/en-US/docs/Web/API/Element))
- **contentBoxSize**: 관찰 대상의 `content-box` 크기 (content)
- **borderBoxSize**: 관찰 대상의 `border-box` 크기 (content + padding + border)

#### 2.2.2 observer

콜백이 실행되는 해당 인스턴스를 참조합니다.

### 2.3 Method

#### 2.3.1 observe()

대상 요소의 관찰을 시작합니다.

```javascript
const ro1 = new ResizeObserver(callback1);
const ro2 = new ResizeObserver(callback2);

const div = document.querySelector("div");
const button = document.querySelector("button");
const textarea = document.querySelector("textarea");

ro1.observe(div); // DIV 요소 관찰
ro2.observe(button); // BUTTON 요소 관찰
ro2.observe(textarea); // TEXTAREA 요소 관찰
```

#### 2.3.2 unobserve()

대상 요소의 관찰을 중집합니다.

```javascript
const ro1 = new ResizeObserver(callback1);
const ro2 = new ResizeObserver(callback2);

ro1.observe(div);
ro2.observe(button);
ro2.observe(textarea);

ro1.unobserve(button); // Nothing..
ro2.unobserve(button); // BUTTON 요소 관찰 중지
```

#### 2.3.3 disconnect()

`ResizeObserver` 인스턴스가 관찰하는 모든 요소의 관찰을 중지합니다.

```javascript
const ro1 = new ResizeObserver(callback1);
const ro2 = new ResizeObserver(callback2);

ro1.observe(div);
ro2.observe(button);
ro2.observe(textarea);

ro2.disconnect(); // `ro2`가 관찰하는 모든 요소(BUTTON, TEXTAREA) 관찰 중지
```
