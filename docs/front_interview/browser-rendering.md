# 브라우저 렌더링 과정

> 렌더링이란 HTML, CSS, Javascript등 개발자가 작성한 문서를 브라우저에서 그래픽 형태로 출력하는 과정을 말합니다. ㅜ엡 개발을 하며 브라우저의 렌더링 과정을 정확하게 이해하고 있으면 어떻게 개발을 해야 좋은 성능을 내는지, 성능 최적화를 어떻게 해야하는지 정확히 판단할 수 있습니다.

## 렌더링 엔진

대부분의 브라우저는 렌더링을 수행하는 렌더링 엔진(Rendering Engine)을 가지고 있습니다. 다만 모든 브라우저가 같은 렌더링 엔진을 사용하지는 않습니다. 파이어폭스는 게코(Gecko)를 사용하고 있고 사파리는 웹킷(Webkit)을 사용하고 있습니다. 크롬의 경우 웹킷을 사용하다가 웹킷을 Fork하여 블링크(Blink) 엔진을 자체적으로 구현하여 사용하고 있습니다.

각각의 렌더링 엔진들을 웹 표준에 따라서 개발자들이 작성한 문서를 브라우저에 보여주지만, 개발 진척도나 별도 규칙에 따라서 지원하는 표준이 다르거나 렌더링 알고리즘과 방식에 차이가 있을 수 있습니다. 따라서 각각 엔진마다 개발자가 의도하지 않게 웹페이지가 동작할 수 있습니다. 따라서 프론트엔드 테스트 환경에서는 크롬, 파이어폭스 등 각 브러우저별로 자동 테스트를 수행하여 여러 브라우저의 호환성에 문제가 없는지 검사해야 합니다.

## 렌더링 과정

#### 1. DOM(Document Object Model), CSSOM(CSS Object Model) 생성

가장 첫번쨰 단계는 서버로부터 받은 HTML, CSS를 다운로드 받습니다. 그리고 HTML, CSS파일은 단순한 텍스트이므로 여산과 관리가 유리하도록 Object Model로 만들게 됩니다. HTML CSS 파일은 각각 DOM Tree와 CSSDOM으로 만들어집니다.

![front_interview_browser_rendering01](../img/front_interview_browser_rendering01.png)
![front_interview_browser_rendering02](../img/front_interview_browser_rendering02.png)

여기서 좀더 설명하자면, 렌더링 엔진은 더 나은 사용자 경험을 위해 가능한 빠르게 내용을 표시하게 만들어졌습니다. 따라서 모든 HTML 파싱이 끝나기도 전에 이후의 과정을 수행하여 미리 사용자에게 보여줄 수 있는 일부 내용들을 출력하게 됩니다.

#### 2. Render Tree 생성

DOM Tree와 CSSOM Tree가 만들어졌으면 그 다음으로는 이 둘을 이용하여 Render Tree를 생성합니다. 순수한 요소들의 구조와 텍스트만 존재하는 DOM Tree와는 달리 Render Tree에는 **스타일 정보가 설정**되어 있으며 **실제 화면에 표현되는 노드들로만 구성**됩니다.

![front_interview_browser_rendering03](../img/front_interview_browser_rendering03.png)

그러면 여기서 각 요소는 스타일 정보들이 설정되어 잇는건 이해할 수 있겠는데 실제 화면에 표현되는 노드들로만 구성된다는 이야기에 **"모든 요소는 다 화면에 표현되는거 아닌가?"**라는 의문을 가지게 됩니다. 결론을 말하자면 아닙니다. 간단한 예로 `display: none` 속성이 설정된 노드는 화면에 어떠한 공간도 차지하지 않기 떄문에 Render Tree를 만드는 과정에서 제외됩니다.s 여기서 조금만 더 말하자면 `visibility: invisible`은 공간은 차지하고 요소가 보이지 않는 것이기에 Render Tree에 포함됩니다.

#### 3. Layout

Layout 단계는 브라우저의 뷰포트 내에서 각 노드들의 정확한 위치와 크기를 계산합니다. 풀어서 얘기하자면 생성된 Render Tree 노드들이 가지고 있는 스타일과 속성에 따라서 **브라우저 화면의 어느 위치에 어느 크기로 출력될지 계산하는 단계**라고 할 수 있습니다. Layout 단계를 통해 `&`, `vh`, `vw`와 같이 상대적인 위치, 크기 속성은 실제 화면에 그려지는 `pixel` 단위로 변환됩니다.

![front_interview_browser_rendering04](../img/front_interview_browser_rendering04.png)

여기서 **뷰포트(Viewport)**란 그래픽이 표시되는 브라우저의 영역, 크기를 말합니다. 뷰포트는 모바일의 경우 디스플레이의 크기, PC의 경우 브라우저 창의 크기에 따라 달라집니다. 그리고 화면에 그려지는 각 요소들의 크기와 위치는 `%`, `vh`, `vw`와 같이 상대적으로 계산하여 그려지는 경우가 많기 떄문에 viewport 크기가 달라질 경우 매번 계산을 다시해야 합니다.

#### 3. Paint

Layout 계산이 완료되면 요소들을 실제 화면에 그리게 됩니다. 이전 단계에서 이미 요소들의 위치와 크기, 스타일 계산이 완료된 Render Tree를 이용해 실제 픽셀 값을 채워넣게 됩니다. 이 때 텍스트, 색, 이미지, 효과등이 모두 처리되어 그려집니다.

이 때 처리해야 하는 스타일이 복잡할수록 Paint 단계에서 소요되는 시간이 늘어나게 됩니다. 간단한 예시로 단순한 단색 `background-color`의 경우 pain 속도가 빠르지만 그라데이션이나 그림자 효과등은 painting 소요 시간이 비굑적 더 오래 소요됩니다.

## 렌더링 최적화 (Reflow, Repaint 줄이기)

지금까지 웹 페이지가 렌더링되는 과정을 알아보았습니다. 그렇다면 웹 성능 최적화를 어떻게 할 수 있을까요? 이를 알려면 `Reflow`와 `Repain`에 대해 먼저 짚고 넘어가야 합니다.

#### Reflow (Layout)

위에서 원급된 렌더링 과정을 거친 뒤 최종적으로 페이지가 그려진다고 해서 렌더링 과정이 다 끝난것이 아닙니다. 어떠한 액션이나 이벤트에 따라 html 요소의 크기나 위치등 레이아웃 쉬를 수정하면 그에 영향을 받는 자식 노드나 부모 노도들을 포함하여 Layout 과정을 다시 수행하게 됩니다. 이렇게되면 Render Tree와 각 요소들의 크기와 위치를 다시 꼐산하게 됩니다. 이러한 과정을 **Reflow**라고 합니다.

```javascript
// reflow 발생 예제
function reflow() {
  document.getElementById("content").style.width = "600px";
}
```

Reflow가 일어나는 대표적인 경우는 아래와 같습니다.

- 페이지 초기 렌더링 시 (최초 Layout 과정)
- 윈도우 리사이징 시 (Viewport 크기 변경 시)
- 노드 추가 또는 제거
- 요소의 위치, 크기 변경 (left, top, margin, padding, border, width, height, 등 ...)
- 폰트 변경(텍스트 내용)과 이미지 크기 변경(크기가 다른 이미지로 변경 시)

#### Repaint (Paint)

Reflow만 수행되면 실제 화면에 반영되지 않습니다. 위에서 언급된 렌더링 과정과 같이 Render Tree를 다시 화면에 그려주는 과정이 필요합니다. 결국은 Paint 단계가 다시 수행되는 것이며 이를 Repaint라고 합니다.

하지만 무조건 Reflow가 일어나야 Repaint가 일어나는 것은 아닙니다. `background-color`, `visibility`와 같이 레이아웃에는 영향을 주지 않는 스타일 속성이 변경되었을 대는 Reflow를 수행할 필요가 없기 때문에 Repaint만 수행하게 됩니다.

#### Reflow, Repaint 줄이기

###### 1. 사용하지 않는 노드에는 visibility: invisible 보다 display: node을 사용하기

visibility inivisible은 레이아웃 공간을 차지하기 때문에 reflow의 대상이 됩니다. 하지만 display none은 Layout 공간을 차지하지 않아 Render Tree에서 아예 제외됩니다.

###### 2. Reflow, Repaint가 발생하는 속성 사용 피하기

아래는 각각 Reflow, Repaint가 일어나는 CSS 속성들 입니다. Reflow가 일어나면 Repaint는 필욘적으로 일어나야 하기 때문에 가능하다면 Reflow가 발생하는 속성보다 Repaint만 발생하는 속성을 사용하는 것이 좋습니다.

Reflow가 일어나는 대표적인 속성

|              |                |             |             |             |
| ------------ | -------------- | ----------- | ----------- | ----------- |
| position     | width          | height      | left        | top         |
| right        | bottom         | margin      | padding     | border      |
| border-width | clear          | display     | float       | font-family |
| font-size    | font-weight    | line-height | min- height | overflow    |
| text-align   | vertical-align | white-space | ...         |             |

Repaint가 일어나는 대표적인 속성

|               |                  |                     |                   |                 |
| ------------- | ---------------- | ------------------- | ----------------- | --------------- |
| background    | background-image | background-position | background-repeat | background-size |
| border-radius | border-style     | box-shadow          | color             | line-style      |
| outline       | outline-color    | outline-style       | outline-width     | text-decoration |
| visibility    | ...              |                     |                   |                 |

또한 Reflow, Repaint가 일어나지 않는 `transform`, `opacity`와 같은 속성도 있습니다. 따라서 `left`, `right`, `width`, `height`보다 `transform`을, `visibility` / `display`보다 `opacity`를 사용하는 것이 성능 개선에 도움이 됩니다.

###### 3. 영향을 주는 노드 줄이기

Javascript + CSS를 조합하여 애니메이션이 많거나 레이아웃 변화가 많은 요소의 경우 `position`을 `absolute` 또는 `fixed`를 사용하여 영향을 받는 주변 노드들을 줄일 수 있습니다. `fixed`와 같이 영향을 받는 노드가 전혀 없는 경우 Reflow 과정이 전혀 필요가 없어지기 때문에 Repaint 연산비용만 들게 됩니다.

또 다른 방법은 애니메이션 시작시 요소를 `absolute`, `fixed`로 변경 후 애니메이션이 종료되었을 때 원상복구 하는 방법도 Reflow, Repaint 연산을 줄이는대 도움이 됩니다.

###### 4. 프레임 줄이기

단순히 생각하면 0.1초에 1px씩 이동하는 요소보다 3px씩 이동하는 요소가 Reflow, Repaint 연산비용이 3배가 줄어듭니다. 따라서 부드러운 효과를 조금 줄여 성능을 개선할 수 있습니다.

## React의 Virtual DOM, Angular Change Detector는 왜 필요한가?

일반적으로 DOM에 접근하여 여러번의 속성 변화, 여러번의 스타일 변화를 수행하면 그에 따라 여러번의 Reflow, Repaint가 발생하게 됩니다. 하지만 Virtual DOM은 이렇게 변화가 일어나 Reflow, Repaint가 필요한 것들을 한번에 묶어서 DOM에 전달하게 됩니다. 따라서 처리되는 Reflow, Repaint의 규모가 커질 수는 있지만 한번만 연산을 수행하게 됩니다. 이를 통해 여러번 Reflow, Repaint를 수행하여 연산이 반복적으로 일어나는 부분이 줄어들어 성능이 개선됩니다.

물론 프레임워크 없이 순수한 Javascript로 똑같은 알고리즘을 구현할 수 있겠지만 실제로 구현하기에는 매우 어렵기 때문에 React, Angular가 이를 대신해주어 인기를 얻었습니다.

## 출처

- [박스여우](https://boxfoxs.tistory.com/408)
