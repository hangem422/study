# Event Loop

## 1. 동기 처리와 비동기 처리

함수가 실행되려면 **함수 코드 평가 과정**에서 생성된 함수 실행 컨텍스트가 실행 컨텍스트 스택에 푸시되어야 합니다. 다시 말해, 실행 컨텍스트 스택에 함수 실행 컨텍스트가 푸시되는 것은 바로 함수 실행의 시작을 의미합니다. 함수가 호출된 순서대로 순차적으로 실행되는 이유는 함수가 호출된 순서대로 함수 실행 컨텍스트가 실행 컨텍스트 스택에 푸시되기 때문입니다. 이처럼 함수의 실행 순서는 실행 컨텍스트 스택으로 관리힙니다.

자바스크립트 엔진은 단 하나의 실행 컨텍스트 스택을 갖습니다. 이는 함수를 실행할 수 있는 창구가 단 하나이며, 동시에 2개 이상의 함수를 실행할 수 없다는 것을 의미합니다. 실행 컨텍스트 스택의 최상위 요소인 **실행 중인 실행 컨텍스트**를 제외한 모든 실행 컨텍스트는 모두 실행 대기 중인 **테스크**(Task)들입니다. 대기 중인 태스크들은 현재 실행 중인 실행 컨텍스트가 팜되어 실행 컨텍스트 스택에서 제거되면, 다시 말해 현재 실행중인 함수가 졸료되면 비로서 실행되기 시작합니다.

이처럼 자바스크립트 엔진은 한 번에 하나의 테스크만 실행할 수 있는 **싱글 스레드**(Single Thread) 방식으로 동작합니다. 싱글 스레드 방식은 한 번에 하나의 테스크만 실행할 수 있기 때문에 처리에 시간이 걸리는 테스크를 실행하는 경우 **블로킹**(Blocking)이 발생합니다. 이처럼 현재 실행 중인 테스크가 종료될 때까지 다음에 실행될 테스크가 대기하는 방식을 **동기**(Synchronous) 처리라고 합니다. 동기 처리 방식은 테스크를 순서대로 하나씩 처리하므로 실행 순서가 보장된다는 장점이 있지만, 앞선 테스크가 종료할 때까지 이후 태스크들이 블로킹되는 단점이 있습니다.

```javascript
function sleep(func, delay) {
  const delayUnitl = Data.now() + delay;
  while (Data.now() < deldelayUnitl);
  func();
}

function foo() {
  console.log("foo");
}

function bar() {
  console.log("bar");
}

sleep(foo, 3 * 1000);
bar();
```

![동기 처리](../_images/javascript-eventloop01.png)

현재 실행 중인 테스크가 종료되지 않은 상태라 해도 다음 태스크를 곧바로 실행하는 방식을 **비동기**(Asnychronous)처리라고 합니다. 비동기 처리 방식은 현재 실행 중인 테스크가 종료되지 않은 상태라 해도 다음 테스크를 곧 바로 실행하므로 블로킹이 발생하지 않는다는 장점이 있지만, 태스크의 실행 순서가 보장되지 않는 단점이 있습니다. 비동기 처리를 수행하는 비동기 함수는 전통적으로 콜백 패턴을 사용합니다.

```javascript
function foo() {
  console.log("foo");
}

function bar() {
  console.log("bar");
}

setTimeout(foo, 3 * 1000);
bar();
```

![비동기 처리](../_images/javascript-eventloop02.png)

## 2. 이벤트 루프와 태스크 큐

![이벤트 루프와 브라우저 환경](../_images/javascript-eventloop03.png)

**이벤트 루프**(Event Loop)는 브라우저에 내장되어 있는 기능 중 하나이며, 자바스크립트의 **동시성**(Concurrency)을 지원합니다. 구글의 V8 자바스크립트 엔진을 비롯한 대부분의 자바스크립트 엔진은 크게 2개의 영역으로 구분할 수 있습니다.

- **콜 스택**(Call Stack): 소스코드(전역 코드나 함수 코드 등) 평가 과정에서 생성된 실행 컨텍스트가 추가되고 제고되는 스택 자료구조인 실행 컨텍스트 스택이 바로 콜 스택입니다. 함수를 호출하면 함수 실행 컨텍스트가 순차적으로 콜 스택에 푸시되어 순차적으로 실행됩니다. 자바스크립트 엔진은 단 하나의 콜 스택을 사용하기 때문에 최상위 컨텍스트(실행 중인 컨텍스트)가 종료되어 콜 스택에서 제거되기 전까지 다른 어떤 태스크도 실행되지 않습니다.
- **힙**(Heap): 힙은 객체가 저장되는 메모리 공간입니다. 콜 스택의 요소인 실행 컨텍스트는 힙에 저장된 객체를 참조합니다. 메모리에 값을 저장하려면 먼저 값을 저장할 메모리 공간의 크기를 결정해야 합니다. 객체는 원시 값과는 달리 크기가 정해져 있지 않으므로 할당해야 할 메머리 공간의 크기를 런타임에 결정(동적 할당)해야 합니다. 따라서 객체가 저장되는 메모리 공간인 힙은 구조화 되어 있지 않다는 특징이 있습니다.

콜 스택과 힙으로 구성되어 있는 자바스크립트 엔진은 단순히 태스크가 요청되면 콜 스택을 통해 요청된 작업을 순차적으로 실행할 뿐입니다. 비동기 처리에서 소스코드의 평가와 실행을 제외한 모든 처리는 자바스크립트 엔진을 구동하는 환경인 브라우저 또는 Node.js가 담당합니다.

- **태스크 큐**(Task Queue/Event Queue/Callback Queue): `setTimeout`이나 `setInterval`과 같은 비동기 함수의 콜백 함수 또는 이벤트 핸들러가 일시적으로 보관되는 영역입니다. 테스크 큐와는 별도로 프로미스의 후속 처리 메서드의 콜백 함수가 일시적으로 보관되는 마이크로태스크 큐도 존재합니다.
- **이벤트 루프**(Event Loop): 이벤트 루프는 콜 스택에 현재 실행 중인 실행 컨텍스트가 있는지, 그리고 태스크 큐에 대기 중인 함수(콜백 함수, 이벤트 핸들러 등)가 있는지 반복해서 확인합니다. 만약 콜 스택이 비어 있고 태스크 큐에 대기 중인 함수가 있다면 이벤트 루프는 순차적으로 태스크 큐에 대기 중인 함수를 콜 스택으로 이동시킵니다. 이때 콜 스택으로 이동한 함수는 실행됩니다. 즉, 태스크 큐에 일시 보관된 함수들은 비동기 처리 방식으로 동작합니다.

우리가 AJAX나 `setTimeout` 혹은 DOM event 함수를 실행하면 자바스크립트 엔진은 **Call Stack**에서 **Web API**로 보내지고 정해진 시간 혹은 이벤트가 발생하는 순간에 순차적으로 **Task Queue**에 적재합니다. **Task Queue**에 줄을 선 함수들은 **Call Stack**에 쌓여있던 것들이 모두 제거되어 깨끗해지면 차례대로 스택에 쌓여서 실행됩니다.

자바스크립트는 싱글 스레드 방식으로 동작합니다. 이때 싱글 스레드 방식으로 동작하는 것은 브라우저가 아니라 브라우저에 내장된 자바스크립트 엔진이라는 것에 주의하기 바랍니다. 만약 모든 자바스크립트 코드가 자바스크립트 엔진에서 싱글 스레드 방식으로 동작한다면 자바스크립트는 비동기로 동작할 수 없습니다. 즉, 자바스크립트 엔진은 싱글 스레드로 동작하지만 브라우저는 멀티 스레드로 동작합니다.

## 3. setTimeout(fn, 0)

프론트엔드 환경의 자바스크립트 코드를 보다 보면 `setTimeout(fn, 0)`와 같은 코드를 종종 보게됩니다. 관용적으로 쓰이는 코드이지만, 사실 처음 보는 사람에게는 직관적으로 이해하기 힘든 코드일 것입니다. 실제 이 코드는 그냥 `fn`을 실행하는 것과는 상당히 다른 결과를 가져옵니다. 위에서 보았겠지만 `setTimeout` 함수는 콜백 함수를 바로 실행하지 않고 **Task Queue**에 추가합니다.

```javascript
function foo() {
  console.log("foo");
}

function bar() {
  console.log("bar");
}

setTimeout(foo, 0);
bar();
```

1. 전역 코드가 평가되어 전역 실행 컨텍스트가 생성되고 콜 스택에 푸시됩니다.
2. 전역 코드가 실행되기 시작하여 `setTimeout` 함수가 호출됩니다. 이때 `setTimeout` 함수의 함수 실행 컨택스트가 생성 되고 콜 스택에 푸시되어 현재 실행 중인 실행 컨텍스트가 됩니다. 브라우저의 Web API(호스트 객체)인 타이머 함수도 함수이므로 함수 실행 컨텍스트를 생성합니다.
3. `setTimeout` 함수가 실행되면 콜백 함수를 호출 스케줄링하고 종료되어 콜 스택에서 팝됩니다. 이때 호출 스케줄링, 즉 타이머 설정과 타이머가 만료되면 콜백 함수를 태스크 큐에 푸시하는 것은 브라우저의 역할입니다.
4. 브라우저가 수행하는 4-1과 4-2는 병행 처리됩니다.
   1. 브라우저는 타이머를 설정하고 타이머의 만료를 기다립니다. 이후 타이머가 만료되면 콜백 함수 `foo`가 태스크 큐에 푸시됩니다. 위 예에의 경우 지연 시간이 0이지만 지연 시간이 4ms 이하인 경우 최소 지연 시간이 4ms가 지정됩니다. 따라서 4ms 후에 콜백 함수 `foo`가 태스크 큐에 푸시되어 대기하게 됩니다. 이 처리 또한 자바스크립트 엔진이 아니라 브라우저가 수행합니다. 이처럼 `setTimeout` 함수로 호출 스케줄링한 콜백 함수는 정확히 지연 시간 후에 호출된다는 보장은 없습니다. 지연 시간 이후에 콜백 함수가 테스크 큐에 푸싱되어 대기하게 되지만 콜 스택이 비어야 호출되므로 약간의 시간차가 발생할 수 있기 때문입니다.
   2. `bar` 함수가 호출되어 `bar` 함수의 함수 실행 컨텍스트가 생성되고 콜 스택에 푸시되어 현재 실행 중인 실행 컨텍스트가 됩니다. 이후 `bar` 함수가 종료되어 콜 스택에서 팝됩니다. 이때 브라우저가 타이머를 설정한 후 4ms가 경과했다면 `foo` 함수는 아직 태스크 큐에서 대기 중입니다.
5. 전역 코드 실행이 종료되고 정역 실행 컨텍스트가 콜 스택에서 팝됩니다. 이로서 콜 스택에는 아무런 실행 컨텍스트도 존재하지 않게 됩니다.
6. 이벤트 루프에 의해 콜 스택이 비어 있음이 감지되고 테스크 큐에서 대기 중인 콜백 함수 `foo`가 이벤트 루프에 의해 콜 스택에 푸시됩니다. 다시 말해, 콜백 함수 `foo`의 함수 실행 컨텍스트가 실행되고 콜 스택에 푸시되어 현재 실행중인 실행 컨텍스트가 됩니다. 이후 `foo` 함수가 종료되어 콜 스택에서 팝 됩니다.

프론트엔드 환경에서는 렌더링 엔진과 관련해서 이런 코드가 특히 요긴하게 쓰일 때가 있습니다. 브라우저 환경에서는 자바스크립트 엔진뿐만 아니라 다른 여러 가지 프로세스가 함께 구동되고 있습니다. 렌더링 엔진도 그 중의 일부이며, 이 렌더링 엔진의 태스크는 대부분의 브라우저에서 자바스크립트 엔진과 동일한 단일 **Task Queue**를 통해 관리됩니다. 이로 인해 가끔 예상치 못한 문제가 생길 경우가 있습니다.

```javascript
showWaitingMessage();
longTakingProcess();
hideWaitingMessage();
showResult();
```

`longTakingProcess`가 너무 오래 걸리는 작업이기 때문에 그 전에 `showWaitingMessage`를 호출해서 로딩 메시지를 보여주려고 합니다. 하지만 실제로 이 코드를 실행해 보면 화면에 로딩 메시지가 표시되는 일은 없습니다. 그 이유는 `showWaitingMessage` 함수의 실행이 끝나고 렌더링 엔진이 렌더링 요청을 보내도, 해당 요청은 **Task Queue**에서 이미 실행중인 태스크가 끝나기를 기다리고 있기 때문입니다. 실행중인 태스크가 끝나는 시점은 호춣 스택이 비워지는 시점인데, 그 때는 이미 `showResult`까지 실행이 끝나 있을 것이고, 결국 렌더링이 진행되는 시점에는 `hideWaitingMessage`로 인해 로딩 메시지가 숨겨진 상태일 것입니다. 이를 해결하기 위해서 `setTimeout`를 사용할 수 있습니다.

```javascript
showWaitingMessage();
setTimeout(function () {
  longTakingProcess();
  hideWaitingMessage();
  showResult();
}, 0);
```

이 경우에는 `longTakingProcess`가 바로 실행되지 않고 **Task Queue**에 추가될 것입니다. 하지만 `showWaitingMessage`로 인해 **Task Queue**에는 렌더링 요청이 먼저 추가되기 때문에 `longTakingProcess`는 그 다음 순서로 **Task Queue**에 추가될 것입니다. 이제 이벤트 루프는 **Task Queue**에 있는 렌더링 요청을 먼저 처리하게 되고 로딩 메시지가 먼저 화면에 보여지게 됩니다.

꼭 렌더링 관련이 아니라도, 실행이 너무 오래 걸리는 코드를 `setTimeout`을 사용하여 적절하게 다른 테스크로 나누어 주면 전체 어플리케이션이 멈추거나 스크립트가 너무 느리다며 경고창이 뜨는 상황을 방지할 수 도 있습니다. 한가지 짚고 넘어갈 사실은 `0`이라는 숫자가 실제롤 **즉시**를 의미하지 않는다는 점입니다.

## 4. Promise

```javascript
setTimeout(function () {
  console.log("A");
}, 0);

Promise.resolve()
  .then(function () {
    console.log("B");
  })
  .then(function () {
    console.log("C");
  });
```

콘솔에 찍히는 순서는 B -> C -> A 입니다. 이유는 바로 Promise가 마이크로 태스크를 사용하기 때문입니다. 마이크로 태스크는 쉽게 말해 일반 태스크보다 더 높은 우선순위를 갖는 태스크라고 할 수 있습니다. 즉, **Task Queue**에 대기중인 태스크가 있더라도 마이크로 태스크가 먼저 실행됩니다. 위의 예제를 통해 좀더 자세히 알아봅시다. `setTimeout()` 함수는 콜백 A를 **Task Queue**에 추가하고, Promise의 `then()` 메소드는 콜백 B를 **Task Queue**가 아닌 **마이크로 태스크 큐**에 추가합니다. 위의 코드의 실행이 끝나면 이벤트 루프는 **Task Queue** 대신 **마이크로 태스크 큐**가 비었는지 먼저 확인하고, 큐에 있는 콜백 B를 실행합니다.

## 5. 추가사항

이벤트 루프는 실제로 자바스크립트 언어의 명세보다는 구동 환경과 더 관련된 내용이기 때문에 다른 프로세스들(렌더링, IO등)과 밀접하게 연관되어 있어 잘 정리된 자료를 찾기가 쉽지 않습니다. 또한 Node.js의 libuv는 HTML 스펙을 완벽히 따르지 않기 때문에 브라우저 환경의 이벤트 루프와 상세 구현이 조금씩 다릅니다. 심지어 브라우저 별로도 구현이 조금씩 다릅니다. 또한, 최근에는 ES6에 Promise와 잡 큐라는 항목이 추가되며 마이크로 테스크의 개념과 혼동되며 이해하기 한 층 더 복잡해졌습니다. 여기서 끝이 아닙니다. 사실 이 글에서는 브라우저가 **단일 이벤트 루프**를 사용한다고 가정하고 설명했지만, 엡 워커(Web Worker)는 각각이 독힙적인 이벤트 루프를 사용하며, 이와 관련된 내용을 추가하면 더더욱 복잡해집니다. 하지만 자바스크립트의 비동기적 특성을 잘 활용하기 위해서는 이벤트 루프를 제대로 이해하는 것이 중요합니다.

## 6. 출처

- [자바스크립트, 이벤트 루프(Event Loop)와 동시성(concurrency)에 대하여 - Code Playground](https://im-developer.tistory.com/113)
- [자바스크립트와 이벤트 루프 - 김동우](https://meetup.toast.com/posts/89)
- 모던 자바스크립트 Deep Dive - 이웅모