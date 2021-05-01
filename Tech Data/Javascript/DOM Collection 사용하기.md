# Dom Collection

DOM 컬렉션 객체인 `HTMLCollection`과 `NodeList`는 DOM API가 여러 개의 결과값을 반환하기 위한 DOM 컬렉션 객체입니다. `HTMLCollection`과 `NodeList`는 모두 유사 배열 객체이면서 이터러블입니다. 따라서 `for...of` 문으로 순회할 수 있으며 스프레드 문법을 사용하여 간단히 배열로 변환할 수 있습니다.

## 1. HTMLCollection

`getElementsByTagName`, `getElementsByClassName` 메서드가 반환하는 HTMLCollection 객체는 노드 객체의 상태 변화를 실시간으로 반영하는 **살아있는**(Live) 컬렉션 객체입니다. 따라서 `HTMLCollection` 객체를 살아있는 객체라고 부르기도 합니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .red {
        color: red;
      }
      .blue {
        color: blue;
      }
    </style>
  </head>
  <body>
    <ul id="fruits">
      <li class="red">Apple</li>
      <li class="red">Banana</li>
      <li class="red">Orange</li>
    </ul>
    <script>
      const $elems = document.getElementByClassName("red");

      for (let i = 0; i < $elems.length; i += 1) {
        $elems[i].className = "blue";
      }

      console.log($elems); // HTMLCollection(1) [li.red]
    </script>
  </body>
</html>
```

위 예제가 에러 없이 실행되면 두 번째 `li` 요소만 `class` 값이 변경되지 않습니다. `HTMLCollection` 객체는 실시간으로 노드 객쳉듸 상태 변경을 반영하여 요소를 제거할 수 있기 때문에 `HTMLCollection` 객체를 `for` 문으로 순회하면서 노드 객체의 상태를 변경해야 할 때 쥐의해야 합니다. 이 문제는 `for` 문을 역방향으로 순회하는 방법으로 회피하거나 `while` 문을 사용하여 `HTMLCollection` 객체에 노드 객체가 남아 있지 않을 때까지 반복하는 방법으로 회피할 수 있습니다.

## 2. NodeList

`qureySelecorAll` 메서드는 DOM 컬렉션 객체인 `NodeList` 객체를 반환합니다. `NodeList` 객체는 대부분의 경우 노드 객체의 상태 변경을 실시간으로 반영하지 않고 과거의 정적 상태를 유지하는 non-live 객체로 동작합니다. NodeList 객체는 NodeList.prototype.forEach 메서드를 상속받아 사용할 수 있습니다.

```javascript
const $elem = document.qeurySelectorAll(".red");

// NodeList 객체는 NodeList.prototype.forEach 메서드를 상속받아 사용할 수 있습니다.
$elems.forEach((elem) => (elem.className = "blue"));
```

하지만 `chilNodes` 프로퍼티가 반환하는 `NodeList` 객체는 `HTMLCollection` 객체와 같이 실시간으로 노드 객체의 상태 변경을 반영하는 live 객체로 동작하므로 주의가 필요합니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
    <script>
      const $fruits = document.getElementById("fruits");

      const { childNodes } = $fruits;
      console.log(childNodes instanceof NodeList);

      // 자식 노드는 공백 텍스트 노드를 포함해 모두 5개입니다.
      console.log(childNodes); // NodeList(5) [text, li, text, li, text]

      for (let i = 0; i < childNodes.length; i += 1) {
        $fruits.removeChild(childNodes[i]);
      }

      console.log(childNodes); // NodeList(2) [li, li]
    </script>
  </body>
</html>
```

## 3. Array

이처럼 `HTMLCollection`이나 `NodeList` 객체는 예상과 다르게 동작할 때가 있어 다루기 까다롭고 실수하기 쉽습니다. 따라서 노드 객체의 상태 변경과 상관없이 안전하게 DOM 컬렉션을 사용하려면 `HTMLCollection`이나 `NodeList` 객체를 배열로 변환하여 사용하는 것을 권장합니다. `HTMLCollection`이나 `NodeList` 객체가 메서드를 제공하기는 하지만 배열의 고차 함수만큼 다양한 기능을 제공하지는 않습니다. `HTMLCollection`이나 `NodeList` 객체를 배열로 변환하면 유용한 고차함수(`forEach`, `map`, `filter`, `reduuce` 등)를 사용할 수 있다는 장점도 있습니다.

```javascript
[...$elems].forEach((elem) => (elem.className = "blue"));
```

## 4. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
