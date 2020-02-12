# Promise

> 프로미스는 자바스크립트 비동기 처리에 사용되는 객체입니다. 비동기 처리란 '특정 코드의 실행이 완료될 떄까지 기다리지 않고 다음 코드를 먼저 수행하는 자바스크립트의 특성'을 의미합니다.

## 1. 프로미스 기초

아래 코드는 제이쿼리의 ajax 통신을 이용하여 지정한 url에서 1번 산품 데이터를 받아오는 코드입니다. 비동기 처리를 위해 프로미스 대신에 **콜백 함수**를 이용했습니다.

```javascript
function getData(callbackFunc) {
  $.get('url 주소/products/1', function (response) {
    callvackFunc(response);
  });
}

getData(function (tableData) {
  console.log(tableData);
});
```

위 코드에 프로미스를 적용하면 아래와 같은 코드가 됩니다. 콜백 함수로 처리하던 구조어세 `new Promise()`, `resolve()`, `then()`과 같은 프로미스 API를 사용한 구조로 바뀝니다.

```javascript
function getData() {
  return new Promise(function (resolve, reject) {
    $.get('url 주소/products/1', function (response) {
      resolve(response);
    });
  });
}

getData().then(function (tableData) {
  console.log(tableData);
});
```

## 2. 프로미스의 3가지 상태

여기서 말하는 상태란 프로미스의 처리 과정을 의마합니다. `new Promise()`로 프로미스를 생성하고 종료될 때까지 3가지 상태를 갖습니다.

- **Pending(대기):** 비동기 처리 로직이 아직 완료되지 않은 상태
- **Fulfilled(이행):** 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
- **Rejected(실패):** 비동기 처리가 실패하거나 오류가 발생한 상태

### Pending(대기)

먼저 아래와 같이 `new Promise()` 메서드 호출시 **Pending(대기)** 상태가 됩니다.

```javascript
new Promise();
```
이렇게 `new Promise()` 메서드를 호출할 때 콜백 함수의 인자로 `resolve`, `reject`에 접근할 수 있습니다.

```javascript
new Promise(function (resolve, reject) {

});
```

### Fulfilled(이행)

콜백 함수의 인자 `resolve`를 아래와 같이 실행하면 Fulfilled 상태가 됩니다. 그리고 이행 상태가 되면 아래와 같이 `then()`을 이용하여 처리 결과 값을 받을 수 있습니다.

```javascript
function getData() {
  return new Promise(function (resolve, reject) {
    var data = 100;
    resolve(data);
  });
}

getData().then(function (resolveData) {
  console.log(resolveData);
});
```

### Rejected(실패)

`reject()`를 실행하면 **Rejected(실패)** 상태가 됩니다. 그리고, 실패 상태가 되면 실패한 이우(실패 처리의 결과 값)를 `catch()`로 받을 수 있습니다.

```javascript
function getData() {
  return new Promise(function (resolve, reject) {
    reject(new Error('Request is failed'));
  });
}

getData().then().catch(function (error) {
  console.log(error);
});
```

## 3. 여러 개의 프로미스 연결하기 (Promeise Chaining)

프로미스의 또 다른 특징은 여러 개의 프로미스를 연결하여 사용할 수 있다는 점입니다. 앞 예제에서 `then()`메서느를 호출하고 나면 새로운 프로미스 객체가 반환됩니다.

```javascript
new Promise(function(resolve, reject){
  setTimeout(function() {
    resolve(1);
  }, 2000);
})
.then(function(result) {
  console.log(result); // 1
  return result + 10;
})
.then(function(result) {
  console.log(result); // 11
  return result + 20;
})
.then(function(result) {
  console.log(result); // 31
});
```

## 4. 프로미스의 에러 처리 방법

에러 처리 방법에는 두가지 방법이 있습니다. 두가지 모두 프로미스의 `reject()` 메서드가 호출되어 실패 상태가 된 경우에 실행됩니다.

#### `then()`의 두 번재 인자로 에러를 처리하는 방법

```javascript
getData().then(
  handleSuccess,
  handleError,
);
```

#### `catch()`를 이용하는 방법

```javascript
getData().then().catch();
```

### 프로미스 에러 처리는 가급적 catch()로

개개인의 코딩 스타일에 따라서 `then()`의 두 번째 인자로 처리할 수도 있고 `catch()`로 처리할 수도 있겠지만 가급적 `catch()`로 에러를 처리하는게 더 효율적입니다. 더 많은 예외 처리 상황을 위해 프로미스의 끝에 가급적 `catch()`를 붙입니다.

## 5. 출처

- [CAPTAIN PANGYO](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/)