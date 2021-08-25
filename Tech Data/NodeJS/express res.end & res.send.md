# res.send() vs res.json() vs res.end()

## 1. Response

Express 서버는 HTTP Request를 수신할 떄 마다, 일반적으로 `res`라고 불리는 객체를 제공합니다. 이 `res` 객체는 Node.js의 `res` 객체의 향상된 버전입니다.

```javascript
app.get("api/test", (req, res) => {
  // ...
});
```

## 2. Sending Response

### 2.1 res.send()

`res.send([body])`와 같은 방식으로 메서드를 호출하여 응답을 보낼 수 있습니다. `body`는 **Buffer**, **String**, **Object**, 그리고 **Array** 중 하나가 될 수 있습니다. `res.send()` 메서드는 **Content-Type** 응답 헤더를 별도로 지정하지 않는 한 `body`에 따라 자동으로 설정합니다.

많은 개발자들은 Node.js를 사용하여 RESTful API 서버를 만들며, 대부분의 경우 JSON 데이터를 반환합니다. 문제는 이 경우 `res.send()`를 사용해야 하는가, 아니면 `res.json()`을 사용해야 하는가 입니다.

### 2.2 res.json()

`res.json()`은 사실 `res.send()`로는 사용할 수 없는 JSON 객체와 관련된 몇 가지 기능을 가지고 있습니다. `res.json()`은 JSON 데이터의 반환 포멧을 두가지 옵션을 적용하여 지정할 수 있습니다.

```javascript
app.set("json replacer", replacer); // property transformation rules
app.set("json spaces", 2); // number of spaces for indentation
```

이 옵션들은 수집되어 `JSON.stringify()` 메서드에서 사용됩니다. `res.json()` 메서드는 `JSON.stringify(object, replacer, space)` 메서드를 호출한 뒤, `res.send()` 메서드를 사용해서 마무리합니다.

### 2.3 res.end()

응답이 있고 데이터를 수집하거나 작업을 수행했으면 호출자에게 응답을 제공하고 세션을 종료해야 합니다. 이 때 `res.end()`를 사용할 수 있습니다. 하지만 이게 필요할까요?

데이터를 제공하지 않고 응답을 종료하려면 `res.end()`를 사용할 수 있습니다. 아래 코드는 HTTP 상태 코드를 명시적으로 설정한 직후, 응답을 종료합니다.

```javascript
res.status(404).end();
```

데이터를 보내고 응답을 끝내려면 `res.send()`(혹은 `res.json()`)을 사용하여 데이터를 보내고 응답을 종료할 수 있습니다. 따라서 `res.end()`를 명시적으로 사용할 필요가 없습니다.

> 참고로 `res.end()`는 캐시의 유효성 검사에 사용하는 ETag를 보내지 않습니다.

3. 출처

- [res.json() vs res.send() vs res.end() in Express - Tamas Piros](https://tpiros.dev/blog/res-json-vs-res-send-vs-res-end-in-express/)
