# 자주 사용하는 DOM API

## 1. 요소 노드 취득 방법

| 메소드                                                                   | 반환값         | 설명                                    |
| ------------------------------------------------------------------------ | -------------- | --------------------------------------- |
| `Document.prototype.getElementById(id)`                                  | Node           | id를 이용한 요소 노드 취득              |
| `Document.prototype/Element.prototype.getElementsByTagName(TagName)`     | HTMLCollection | 태그 이름을 이용한 요소 노드 취득       |
| `Document.prototype/Element.prototype.getElementsByClassName(className)` | HTMLCollection | class를 이용한 요소 노드 취득           |
| `Document.prototype/Element.prototype.querySelector(selector)`           | Node           | CSS 선택자를 이용한 요소 노드 취득      |
| `Document.prototype/Element.prototype.querySelectorAll(selector)`        | NodeList       | CSS 선택자를 이용한 요소 노드 모두 취득 |
| `Element.prototype.matches(selector)`                                    | Boolean        | 특정 요소 노드를 취득할 수 있는지 확인  |

## 2. 노드 탐색

| 프로퍼티                                   | 설명                                                                                                                                                                                |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Node.prototype.childNodes`                | 자식 노드를 모두 탐색하여 DOM 컬렉션 객체인 NodeList에 담아 반환합니다. `childNodes` 프로퍼티가 반환한 `NodeList`에는 요소 노드뿐만 아니라 텍스트 노드도 포함되어 있을 수 있습니다. |
| `Element.prototype.children`               | 자식 노드 중에서 요소 노드만 모두 탐색하여 DOM 컬렉션 객체인 `HTMLCollection`에 담아 반환합니다. `children` 프로퍼티가 반환한 `HTMLCollection`에는 텍스트 노드가 포함되지 않습니다. |
| `Node.prototype.firstChild`                | 첫 번째 자식 노드를 반환합니다. `firstChild` 프로퍼티가 반환한 노드는 텍스트 노드이거나 요소 노드입니다.                                                                            |
| `Node.prototype.lastChild`                 | 마지막 자식 노드를 반환합니다. `lastChild` 프로퍼티가 반환한 노드는 텍스트 노드이거나 요소 노드입니다.                                                                              |
| `Element.prototype.firstElementChild`      | 첫 번째 자식 요소 노드를 반환합니다. `firstElementChild` 프로퍼티는 요소 노드만 반환합니다.                                                                                         |
| `Element.prototype.lastElementChild`       | 마지막 자식 요소 노드를 반환합니다. `lastElementChild` 프로퍼티는 요소 노드만 반환합니다.                                                                                           |
| `Node.prototype.previousSibling`           | 부모 노드가 같은 형제 노드 중에서 자신의 이전 형제 노드를 탐색하여 반환합니다.`previousSibling` 프로퍼티가 반환하는 형제 노드는 요소 노드뿐만 아니라 텍스트 노드일 수도 있습니다.   |
| `Node.prototype.nextSibling`               | 부모 노드가 같은 형제 노드 중에서 자신의 다음 형제 노드를 탐색하여 반환합니다. `nextSibling` 프로퍼티가 반환하는 형제 노드는 요소 노드뿐만 아니라 텍스트 노드일 수도 있씁니다.      |
| `Element.prototype.previousElementSibling` | 부모 노드가 같은 형제 요소 노드 중에서 자신의 이전 형제 요소 노드를 탐색하여 반환합니다. `previsousElementSibling` 프로퍼티는 요소 노드만 반환합니다.                               |
| `Element.prototype.nextElementSibling`     | 부모 노드가 같은 형제 요소 노드 중에서 자신의 다음 형제 요소 노드를 탐색하여 반환합니다. `nextElementSibling` 프로퍼티는 요소 노드만 반환합니다.                                    |

## 3. 자식 노드 존재 확인

| 메서드 / 프로퍼티                     | 반환값  | 설명               |
| ------------------------------------- | ------- | ------------------ |
| `Node.prototype.hasChildNodes()`      | Boolean | 텍스트 노드 포함   |
| `Element.prototype.children.length`   | Number  | 텍스트 노드 미포함 |
| `Element.prototype.childElementCount` | Number  | 텍스트 노드 미포함 |

## 4. 노드 정보 취득

| 프로퍼티                  | 설명                                                                                                    |
| ------------------------- | ------------------------------------------------------------------------------------------------------- |
| `Node.prototype.nodeType` | 노드 객체의 종류, 즉 노드 타입을 나타내는 상수를 반환합니다. 노드 타입 상수는 Node에 정의되어 있습니다. |
| `Node.prototype.nodeName` | 노드의 이름을 문자열로 반환합니다.                                                                      |

## 5. 텍스트 조작

| 프로퍼티                      | 설명                                                                                                                                                                                                                                                |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Node.prototype.nodeValue`    | 따라서 텍스트 노드가 아닌 노드, 즉 문서 노드나 요소 노드의 `nodeValue` 프로퍼티를 참조하면 `null`                                                                                                                                                   |
| `Node.porototype.textContent` | 요소 노드의 childNodes 프러퍼티가 반환한 모든 노드들의 텍스트 노드의 값, 즉 텍스트를 모두 반환합니다. 이떄 HTML 마크업은 무시됩니다. 요소 노드의 `textContent` 프로퍼티에 문자열을 할당하면 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가 |

## 6. DOM 조작

| 메서드 / 프로퍼티                                           | 섧명                                                                                                                                                                                                                                                                                                                  |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Element.prototype.innerHTML`                               | 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역(시작 태그와 종료 태그 사이) 내에 포함된 모든 HTML 마크업을 문자열로 변환합니다. 요소 노드의 `innerHTML` 프로퍼티에 문자열을 할당하면 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열에 포함되어 있는 HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영됩니다. |
| `Element.prototype.insertAdjacentHTML(position, DOMString)` | 두 번째 인수로 전달한 HTML 마크업 문자열(DOMSring)을 파싱하고 그 결과로 생성된 노드를 첫 번째 인수로 전달한 위치(position)에 삽입할여 DOM에 반영합니다.                                                                                                                                                               |

## 7. 노드 조작

| 메서드                                            | 설명                                |
| ------------------------------------------------- | ----------------------------------- |
| `Document.prototype.createElement(tagName)`       | 요소 노드 생성                      |
| `Document.prototype.createTextNode(text)`         | 텍스트 노드 생성                    |
| `Node.prototype.appendChild(childNode)`           | 노드를 요소 노드의 자식 노드로 추가 |
| `Node.prototype.cloneNode([deep: boolean])`       | 노드 복사                           |
| `Node.prototype.replaceChild(newChild, oldChild)` | 노드 교체                           |
| `Node.prototype.removeChild(child)`               | 노드 삭제                           |

## 8. 어트리뷰트 조작

| 메서드 / 프로퍼티                                               | 설명                                                                                                     |
| --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `Element.prototype.attributes`                                  | 읽기 전용 접근자 프러퍼티이며, 요소 노드의 모든 어트리뷰트 노드의 참조가 담긴 `NamedNodeMap` 객체를 반환 |
| `Element.prototype.getAttribute(attributeName)`                 | `attributes` 프로퍼티를 통하지 않고 요소 노드에서 메서드를 통해 직접 HTML 어트리뷰트 값을 취득           |
| `Element.prototype.setAttribute(attributeName, attributeValue)` | `attributes` 프로퍼티를 통하지 않고 요소 노드에서 메서드를 통해 직접 HTML 어트리뷰트 값을 변경           |
| `Element.prototype.hasAttribute(attributeName)`                 | 특정 HTML 어트리뷰트가 존재하는지 확인                                                                   |
| `Element.prototype.removeAttribute(attributeName)`              | 특정 HTML 어트리뷰트를 삭제                                                                              |

## 9. data 어트리뷰트

| 프로퍼티              | 설명                                                                                                                                               |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `HTMLElement.dataset` | HTML 요소의 모든 `data` 어트리뷰트의 정보를 제공하는 `DOMStringMap` 객체를 반환. 이 프로퍼티로 `data` 어티르뷰트의 값을 취득하거나 변경할 수 있음. |

## 10. 스타일

| 프로퍼티                                    | 반환값              | 설명                                                                                                                                                                                                                                                                                                         |
| ------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `HTMLElement.prototype.style`               | CSSStyleDeclaration | 요소 노드의 **인라인 스타일**(Inline Style)을 취득하거나 추가 또는 변경                                                                                                                                                                                                                                      |
| `window.getCoputedStyle(element[, pseudo])` | CSSStyleDeclaration | 요소 노드에 적용되어 있는 모든 스타일, 즉 링크 스타일, 임베딩 스타일, 인라인 스타일, 자바스크립트에서 적용한 스타일, 상속된 스타일, 기본 스타일 등 모든 스타일이 조합되어 최종적으로 적용된 스타일을 반환. 두 번째 인수(`pseudo`)로 `:affter`, `:before`와 같은 의사 요소를 지정하는 문자열을 전달할 수 있음 |
| `Element.prototype.className`               | String              | 프로퍼티를 참조하면 `class` 어트리뷰트 값을 문자열로 반환하고, 프로퍼티에 문자열을 할당하면 `class` 어트리뷰트 값을 할당한 문자열로 변경                                                                                                                                                                     |
| `Element.prototype.classList`               | DOMTokenList        | `class` 어트리뷰트의 정보를 담은 `DOMTokenList` 객체를 반환                                                                                                                                                                                                                                                  |

## 11. classList

| 메서드                                                            | 설명                                                                                                                                                                                                                                                                                               |
| ----------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Element.prototype.classList.add(...className)`                   | 인수로 전달한 1개 이상의 문자열을 `class` 어티르뷰트 값으로 추가                                                                                                                                                                                                                                   |
| `Element.prototype.classList.remove(className)`                   | 인수로 전달한 1개 이상의 문자열과 일치하는 클래스를 `class` 어트리뷰트에서 삭제                                                                                                                                                                                                                    |
| `Element.prototype.classList.item(index)`                         | 인수로 전달한 `index`에 해당하는 클래스를 `class` 어트리뷰트에서 반환                                                                                                                                                                                                                              |
| `Element.prototype.classList.contains(className)`                 | 인수로 전달한 문자열과 일치하는 클래스가 `class` 어트리뷰트에 포함되어 있는지 확인                                                                                                                                                                                                                 |
| `Element.prototype.classList.replace(oldClassName, newClassNmae)` | `class` 어트리뷰트에서 첫 번쨰 인수로 전달한 문자열을 두 번째 인수로 전달한 문자열로 변경                                                                                                                                                                                                          |
| `Element.prototype.classList.toggle(className[, force])`          | `class` 어트리뷰트에 인수로 전달한 문자열과 일치하는 클래스가 존재하면 제거하고, 존재하지 않으면 추가. 이때 조건식의 평가 결과가 `true`이면 `class` 어티르뷰트에 강제로 첫 번쨰 인수로 전달받은 문자열을 추가하고, `false`이면 `class` 어트리뷰트에서 강제로 첫 번째 인수로 전달받은 문자열을 제거 |
