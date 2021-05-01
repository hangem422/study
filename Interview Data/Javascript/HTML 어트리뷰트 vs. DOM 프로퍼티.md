# HTML 어트리뷰트 vs. DOM 프로퍼티

요소 노드 객체에는 HTML 어트리뷰트에 대응하는 프로퍼티(이하 DOM 프로퍼티)가 존재합니다. 이 DOM 프로퍼티들은 HTML 어트리뷰트 값을 초기값으로 가지고 있습니다. DOM 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티입니다. 따라서 DOM 프로퍼티는 참조와 변경이 가능합니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <input id="user" type="text" value="ungmo2" />
  </body>
  <script>
    const $input = document.getElementById("user");

    console.log($input.id); // user
    console.log($input.type); // text
    console.log($input.value); // ungmo2

    $input.value = "foo";
    console.log($input.value); // foo
  </script>
</html>
```

결론부터 말하자면 HTML 어트리뷰트는 DOM에서 중복 관리되고 있지 않습니다. 요소 노드는 2개의 상태, 즉 초기 상태와 최신 상태를 관리해야 합니다.

- **어트리뷰트 노드**: HTML 어트리뷰트로 지정한 HTML 요소의 초기 상태는 어티이뷰트 노드에서 관리합니다. 어티르뷰트 노드에서 관리하는 어트리뷰트 값은 사용자의 입력에 의해 상태가 변경되어도 변하지 않고 HTML 어트리뷰트로 지정한 HTML 요소의 초기 상태를 그대로 유지합니다. 어티리뷰트 노드가 관리하는 초기 상태 값을 취득학서나 변경하려면 `getAttribute.setAttribute` 메서드를 사용합니다.
- **DOM 프로퍼티**: 사용자가 입력한 최신 상태는 HTML 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티가 관리합니다. DOM 프러퍼티는 사용자의 입력에 의한 상태 변화에 반응하여 언제나 최신 상태를 유지합니다.

단, 모든 DOM 프로퍼티가 사용자의 입력에 의해 변경되는 최신 상태를 관리하는 것은 아닙니다. 예를 들어, `input` 요소의 사용자 입력에 의한 상태 변화는 `value` 프로퍼티가 관리하고 `checkbox` 요소의 사용자 입력에 의한 상태 변화는 `checked` 프로퍼티가 관리합니다. 하지만 `id` 어트리뷰트에 대응하는 `id` 프로퍼티는 사용자의 입력과 아무런 관계가 없습니다. 따라서 사용자 입력에 의한 상태 변화와 관계없는 `id` 어트리뷰트와 `id` 프로퍼티는 사용자 입력과 관계없이 항상 동일한 값을 유지합니다. 즉, `id` 어트리뷰트 값이 변하면 `id` 프로퍼티 값도 변하고 그 반대도 마찬가지입니다.

## 1. HTML 어트리뷰트와 DOM 프로퍼티의 대응 관계

대부분의 HTML 어트리뷰트는 HTML 어트리뷰트 이름과 동일한 DOM 프러퍼티와 1:1로 대응합니다. 단, 다음과 같이 HTML 어트리뷰트와 DOM 프로퍼티가 언제나 1:1로 대응하는 것은 아니며 HTML 어트리뷰트 이름과 DOM 프로퍼티 키가 반드시 일치하는 것도 아닙니다.

- `id` 어트리뷰트와 `id` 프러피티는 1:1 대응하며, 동일한 갑승로 연동합니다.
- `input` 요소의 `value` 어트리뷰트는 `value` 프러피티와 1:1 대응합니다. 하지만 `value` 어트리뷰트는 초기 상태를 `value` 프로퍼티는 최신 상태를 갖습니다.
- `class` 어티르뷰트는 `classNAme`, `classList` 프로퍼티와 대응합니다.
- `for` 어트리뷰트는 `htmlFor` 프로퍼티와 1:1 대응합니다.
- `td` 요쇼의 `colspan` 어트리뷰트는 대응하는 프로퍼티가 존재하지 않습니다.
- `textContent` 프로퍼티는 대응하는 어트리뷰트가 존재하지 않습니다.
- 어트리뷰트 이름은 대소문자를 구별하지 않지만 대응하는 프로퍼티 키는 카멜 케이스를 따릅니다.

## 2. DOM 프로피티 값의 타입

`getAttribute` 메서드로 취득한 어티르뷰트 값은 언제나 문자열입니다. 하지만 DOM 프로퍼티로 취득한 최신 상태 값은 문자열이 아닐 수도 있습니다.

```html
<!DOCTYPE html>
<html>
  <body>
    <input type="checkbox" checked />
  </body>
  <script>
    const $checkbox = document.querySelector("input[type=checkbox]");

    console.log($checkbox.getAttribute("checked")); // ""
    console.log($checkbox.checked); // true
  </script>
</html>
```

## 3. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
