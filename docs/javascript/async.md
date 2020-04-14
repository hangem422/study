# Javascript 비동기 구현방식

> JS 비동기 프로그래밍의 중요한 요소들을 간단하게 알아봅니다. 그리고 비동기를 처리하는 방식 3가지 (Callback, Promise, Async Await)의 내부 또한 살펴봅니다.

## 1. JS는 싱글 쓰레드다?

자바스크립트는 흔히 싱글 쓰레드기반 언어라고 불립니다. 하지만 싱글 쓰레드만을 사용하지 않습니다. 싱글 쓰레드 언어라고 불리는 이유는 Call Stack이 하나이기 때문입니다. 그래서 전역 Main 함수를 포함해, 함수의 실행을 하나의 쓰레드가 순회하면서 실행합니다. 하지만 하나의 스레드로만 연산을 모두 처리하면 자바스크립트는 지금까지 살아남는 언어가 되지 않았을 겁니다.

## 2. JS의 비동기 프로그래밍 특징

JS는 하나가 모든 일을 담당하는 싱글 쓰레드의 단점을 회피하기 위해 비동기 프로그래밍을 사용합니다. Source를 순회하는 쓰레드는 하나이지만 Network IO나 DB를 조회하는 등, 시간 비용이 큰 로직은 다른 쓰레드로 위임을 하고 다른 로직으로 이동해 할 것들을 합니다. 이렇게 큰 일들을 다른 쓰레드로 던져서 `위임`하는 것은 `비동기` 특징입니다. 위임시키는 대상은 `API`라는 곳인데, 브라우저에선 `WebAPI`, NodeJS에선 `Node API`라고 부르는 별개의 쓰레드 영역입니다. 큰 일을 던져준 쓰레드는 쉬지 않습니다. 던져준 일을 기다리지 않고 다른 일로 진행하는 것을 `논 블러킹`이라고 합니다. 다른 쓰레드에게 던져진 일이 끝나면, 그 일에서 마저 해야할 것을 처리할 수 있게 Source를 순회하는 쓰레드에게 이벤트로 알려주는 시스템을 `이벤트 기반 아키텍처`라고 부릅니다.

1. 처리된 일은 `Event Queue`에 들어가고 대기합니다.
2. 제어 쓰레드가 일을 마쳐서 `CallStack`에서 실행할 게 없어집니다.
3. `Event Loop`는 `Event Queue`에 있는 일을 하나 꺼내서 `CallStack에` 넣어 실행합니다.

## 3. 비동기 처리 방식 3가지

### Callback

`Callback`을 ES6에서 `Promise`가 표중화될 때까지 비동기를 처리하는 공식 방법이였습니다. 비동기를 호출하는 함수를 호출하면서 콜백 함수라는 인자를 넣어 함수의 결과물을 필요로 하는 뒤의 로직을 구설할 수 있습니다.

#### Callback Hell

만약 NodeJS Express에서 한 유저가 팔로윙한 사용자들이 작성한 게시글의 댓글들을 가져오는 라우터를 처리해야 한다고 생각해봅시다. 릴레이션을 고려 안하기도 더러운 DB 접근 로직이지만 굳이 DB 접근 로직이 아니더라도 연속으로 비동기 함수를 호출해야 하는 상황이라고 생각하면 됩니다.

```javascript
app.get("/...", (req, res) => {
  const result = [];
  const id = req.params.id;

  FollowingModel.find({ followerId: id }, (users) => {
    for (let i = 0; i < users.length; i += 1) {
      const user = user[i];

      PostModel.find({ writer: user.id }, (posts) => {
        for (let j = 0; j < posts.length; j += 1) {
          const post = posts[j];

          CommentModel.find({ postId: post.id }, (commnets) => {
            result.push(...commnets);
          });
        }
      });
    }
  });
});
```

##### 어려운 비동기 제어

`function (req, res)` 함수 내에선 `FollowingModel.find`를 호출하고 결과를 가져오기 전에 `res.send`가 호출됩니다. 그런데 왜 잘 동작하는 코드일까요? 그 이유는 `res.send`가 express 내부에서 비동기 함수이기 때문입니다. express의 라우팅 시스템을 보면 아래와 같은 순서로 동작합니다.

```
미들웨어 -> app.get('/..')가 실행되어 request, response 인자 생성 -> functions(req, res) 호출 -> function(req, res) 내부에서 response 메서드 호출 -> function(req, res)가 끝난 뒤, response 메서드 로직 동작
```

이 때, `result`라는 배열 변수는 레퍼런스가 하나로 고정된 상태로, 변수 호출 시와 `function(req, res)`가 끝나 `res.send` 로직안에서 같은 래퍼런스를 가지고 있습니다. 이는 Express가 제공해주는 시스템이라 문제가 없이 돌아가지만 보통의 경우 중첩된 콜백안의 결과물들로 예측 가능한 결과를 만들기 어렵습니다.

##### 코드 가시성 하락

코드 자체도 들여 쓰기로 인해 가시성이 떨어져 개발 생산성이 떨어집니다. 그렇다고 callback 함수들을 각각 함수로 정의해 코드 가시성을 증대시킬 수 있겠지만 코드의 리딩이 계속 함수를 건너 다른 함수로 건너는 방식으로 되기 때문에 사람에게 익숙한 명령형 사고방식과 거리가 멀어져 여전히 개발 생상성이 떨어지게 됩니다.

### Promise

이런 Callback Hell의 단점을 극복하기 위해 ES6부터는 `Promise`를 표준으로 채택했습니다.

#### 비동기를 값으로 표현

`Promise`의 가장 큰 특징은 비동기 상황을 하나의 객체(값)로 표현한 것입니다. 그 상황은 `pending`, `fulfilled`, `rejected`가 있습니다.

1. **Pending:** `resolve`나 `reject`를 하지 않아 여전히 `Promise` 생성자 로직이 실행되는 상태
2. **Fulfilled:** `resolve`가 호출되어 값을 넘길 수 있는 상태, `then` 사용 가능 상태
3. **reject:** `reject`가 호출되어 값을 넘길 수 있는 상태, `catch` 사용 가능 상태

##### 병령 처리

비동기를 값으로 다루는 것의 장접은 비동기를 쉽게 제어할 수 있다는 점입니다. 병령 처리도 `Promise.all` 메서드로 쉽게 처리할 수 있습니다.

```javascript
const p1 = new Promise((resolve) => {
  setTimeout(() => resolve("resolve: p1"), 3000);
});
const p2 = new Promise((resolve) => {
  setTimeout(() => resolve("resolve: p2"), 5000);
});
console.time("test");

Pormise.all([p1, p2]).then(([r1, r2]) => {
  console.log(r1, r2);
  console.timeEnd("test");
});
```

##### Then Chaining

서로 연관 있는 로직들은 `then`의 체이닝으로 이어갑니다.

```javascript
const p1 = new Promise((resolve) => {
  setTimeout(() => resolve("resolve: p1"), 3000);
});
const p2 = (param) =>
  new Promise((resolve) => {
    setTimeout(() => resolve(`${param}, resolve: p2`), 5000);
  });

p1.then((r1) => {
  console.log("after p1 resolve");
  return p2(r1);
}).then((r2) => {
  console.log("after p2 resolve");
  console.log(r2);
});
```

#### Reject 처리

`reject`는 보통 예외, 에러 상황에서 발생합니다. `reject` 처리 방법은 `then`의 두번째 인자 함수(trjected 함수)와 `catch`로 처리할 수 있습니다. 하지만 보통 `catch`를 사용하는 것을 권장합니다.

##### Rejected 함수

먼저 `then`의 `rejected` 함수의 예시입니다.

```javascript
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => reject("reject: p1"), 3000);
});
const p2 = (param) =>
  new Promise((resolve) => {
    setTimeout(() => resolve(`${param}, resolve: p2`), 5000);
  });

p1.then(
  (r1) => {
    console.log("after p1 resolve");
    return p2(r1);
  },
  (e1) => {
    console.log("after p1 reject");
    console.log(e1);
  }
).then(
  (r2) => {
    console.log("after p2 resolve");
    console.log(r2);
  },
  (e2) => {
    console.log("after p2 reject");
  }
);
```

콘솔 출력 결과는 아래와 같이 나옵니다.

```
after p1 reject -> reject: p1 -> after p2 resolve -> undefined
```

보통 `rejected` 상황은 에러, 예외 상황에 발생합니다. 그래서 보통 아래와 같은 출력을 기대할 것입니다.

```
after p1 reject -> reject: p1
혹은
after p1 reject -> reject: p1 -> after p2 reject -> ...
```

e1 출력인 reject: p1까지 출력 하거나 다음 `rejected` 함수를 원하는 상황이 많을텐데, 실제론 `rejected` 함수 다음에 `fulfilled` 함수로 `chaining` 됩니다. `return`을 하지 않아도 JS의 함수는 암묵적으로 `undefined`를 `return`하기 때문에 `undefined`가 인자인 `fulfilled` 함수가 자동으로 실행되어 예측 불가능한 결과를 만듭니다. 그리고 `fulfilled` 함수 내의 에러를 해당 `then rejected` 함수에서 핸들링할 수 없다는 단점도 있습니다.

```javascript
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve("rejected: p1"), 3000);
});
const p2 = (param) =>
  new Promise((resolve) => {
    setTimeout(() => resolve(`${param}, resolve: p2`), 5000);
  });

p1.thne(
  (r1) => {
    console.log("after p1 resolve");
    throw new Erorr("error");
    return p2(r1);
  },
  (e1) => {
    console.log("after p1 reject");
    console.log(e1);
  }
).then(
  (r2) => {
    console.log("after p2 resolve");
    console.log(r2);
  },
  (e2) => {
    console.log("after p2 reject");
    console.log(e2);
  }
);
// after p1 resolve -> after p2 reject -> error
```

##### catch 메서드

```javascript
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => reject("reject: p1"), 3000);
});
const p2 = new Promise((resolve) => {
  setTimeout(() => resolve("resolve: p2"), 5000);
});

p1.then((r1) => {
  console.log("after p1 resolve");
  return p2(r1);
})
  .then((r2) => {
    console.log("after p2 resolve");
    console.log(r2);
  })
  .catch((e) => {
    console.log("after reject");
    console.log(e);
  });
// after reject -> reject p1
```

`rejected` 함수말고 `catch`를 사용하는 이유는 다음과 같습니다.

1. 예외 처리 같은 `reject` 로직을 뒤에서 작성할 수 있습니다.
2. 그래서 중간에 섞인 `rejected` 함수 사용 로직보다 역할이 명확하게 보입니다.
3. `reject` 상황을 뒤에 배치하여 `then chaining`으로 이어서 로직을 작성할 수 있습니다.
4. 무엇보다 `then`의 `fulfilled` 로직에서 발생하는 에러를 잡아줄 수 있습니다.

```javascript
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve("resolve: p1"), 3000);
});
const p2 = new Promise((resolve) => {
  setTimeout(() => resolve("resolve: p2"), 5000);
});

p1.then((r1) => {
  console.log("after p1 resolve");
  return p2;
})
  .then((r2) => {
    console.log("after p2 resolve");
    throw new Error("error");
    console.log(r2);
  })
  .catch((e) => {
    console.log("after reject");
    console.log(e, "r2 fixed");
    return e;
  })
  .then((e) => {
    console.log(e, "r1 fixed");
  });
// after p1 resolve -> after p2 resolve -> after reject -> error, r2 fixed -> error r1 fixed
```

### Async Await

`async await` 구문은 ES8부터 적용된 비동기 처리 방식입니다. 그래서 Front에서 사용하는 경우 호환성을 위해 `babel`의 사용이 필요합니다. `Promise`는 비동기 처리하기 좋은 방식이지만 `then chaining`이 무수히 많아지면 가독성이 떨어지는 단점이 존재합니다. 그래서 이런 단점을 보완하기도 하며, 좀 더 비동기 처리를 명령형 프로그래밍에 익숙하게 만들어 코드를 보기 좋게 만들 수 있습니다.

#### Async function

`Async Await` 구문을 사용하기 첫 시작은 `async function`을 선언하는 것입니다. `await` 구문은 `async function` 내에서만 사용할 수 있습니다.`async function`은 반환 값은 `Promise`입니다.

```javascript
async function func(a) {
  return a;
}
console.log(func(2)); // 2를 resolve할 Promise
```

## 4. 출처

- [velog.id/@thsoon](https://velog.io/@thsoon/JS-%EB%B9%84%EB%8F%99%EA%B8%B0%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EA%B5%AC%ED%98%84%EB%90%98%EC%96%B4%EC%9E%88%EB%8A%94%EA%B0%80)
