# 46. 제너레이터와 async/await

## 46.1 제너레이터란?

ES6에서 도입된 **제너레이터**(Generator)는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수입니다. 재네레이터와 일반 함수의 차이는 다음와 같습니다.

1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있습니다.
2. 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있습니다.
3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환합니다.

## 46.2 제너레이터 함수의 정의

제너레이터 함수는 `function*` 키워드로 선언합니다. 그리고 하나 이상의 `yield` 표현식을 포함합니다. 이것을 제외하면 일반 함수를 정의하는 방법과 같습니다. **에스터리스크**(`*`)의 위치는 `function` 키워드와 함수 이름 사이라면 어디든지 상관없습니다. 하지만 일관성을 유지하기 위해 `function` 키워드 바로 뒤에 붙이는 것을 권장합니다.

```javascript
// 제너레이터 함수 선언문
function* getDecFunc() {
  yield 1;
}

// 재너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 재너레이터 매서드
const obj = {
  *genObjMethod() {
    yield 1;
  },
};

// 제너레이터 클래스 메서드
class MyClass {
  *genClsMethod() {
    yield 1;
  }
}
```

제너레이터 함수는 화살표 함수로 정의할 수 없습니다.

```javascript
const genArrowFunc = * () => {
  yield 1;
}; // SysteaxError: Unexpected token '*'
```

제너레이터 함수는 `new` 연산자와 함께 생성자 함수로 호출할 수 없습니다.

```javascript
function* genFunc() {
  yield 1;
}

const obj = new genFunc(); // TypeError: genFunc is not a constructor
```

## 46.3 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환합니다. 제너레이터 함수가 반환한 제너레이터 객체는 **이터러블**(Iterable)이면서 동시에 **이터레이터**(Iterator)입니다. 다시 말해 제너레이터 객체는 `Symbol.iterator` 메서드를 상속받는 이터러블이면서 `value`, `done` 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 `next` 메서드를 소유하는 이터레이터 입니다. 제너레이터 객체는 `next` 메서드를 가지는 이터레이터이므로 `Symbol.iterator` 메서드를 호출해서 별도로 이터레이터를 생성할 필요가 없습니다.

```javascript
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

const generator = genFunc();
console.log(Symbol.iterator in generator); // true
console.log("next" in generator); // true
```

제너레이터 객체는 `next` 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 `return`, `throw` 메서드를 갖습니다. 제너레이터 객체의 세 개의 메서드를 호출하면 다음과 같이 동작합니다.

- `next`: 제너레이터 함수의 `yield` 표현식까지 코드 블록을 실행하고 `yield`된 값을 `value` 프로퍼티 값으로, `false`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환합니다.
- `return`: 인수로 전달받은 `value` 프로퍼티를 값으로, `true`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환합니다.
- `throw`: 인수로 전달받은 에러를 발생시킵니다. Error가 핸들링 된다면 이후 로직은 `next`와 동일하게 실행됩니다.

> 책에서는 `throw` 메서드를 사용하면 에러를 발생시키고 `undefined`를 `value` 프로퍼티 값으로, `true`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다고 써있습니다. 하지만 Node.js 15.5.0 Version으로 확인한 결과 그렇게 동작하지 않습니다.

```javascript
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // { value: 1, done: false }
console.log(generator.return("End")); // { value: "End", done: true }
```

## 46.4 제너레이터의 일시 중지와 재개

제너레이터 함수를 호출하면 제너레이터 함수의 코드 블록이 실행되는 것이 아니라 제너레이터 객체를 반환한도고 했습니다. 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 `next` 메서드를 갖습니다. 제너레이터 객체의 `next` 메서드를 호출할면 `yield` 표현식까지 실행되고 **일시 중지**(Suspend)됩니다. 이때 함수의 제어권이 호출자로 **양도**(Yield)됩니다. 이후 필요한 시점에 호출자가 또다시 `next` 메서드를 호출하면 일시 중지된 코드부터 실행을 **재개**(Resume)하기 시작하여 다음 `yield` 표현식까지 실행되고 또 다시 일시 중지됩니다.

이때 제너레이터 객체의 `next` 메서드는 `value`, `done` 프로퍼티를 갖는 이터레이터 리절트 객체를 반환합니다. `next` 메서드가 반환한 이터레이터 리절트 객체의 `value` 프로퍼티에는 `yield` 표현식에서 `yield`된 값이 할당되고 `done` 프러퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값이 할당됩니다.

이처럼 `next` 메서드를 반복 호출하여 `yield` 표현식까지 실행과 일시 중지를 반복하다가 제너레이터 함수가 끝까지 실행되면 `next` 메서드가 반환하는 이터레이터 리절트 객체의 `value` 프로퍼티에는 제너레이터 함수의 반환값이 할당되고, `done` 프로퍼티에는 제너레이터 함수가 끝까지 실행되었을을 나타내는 `true`가 할당됩니다.

이터레이터의 `next` 메서드와 달리 제너레이터 객체의 `next` 메서드에는 인수를 전달할 수 있습니다. 제너레이터 객체의 `next` 메서드에 전달한 인수는 제너레이터 함수의 `yield` 표현식을 할당받는 변수에 할당됩니다.

```javascript
function* genFunc() {
  const x = yield 1;
  const y = yield x + 10;

  return x + y;
}

const generator = genFunc();

let res = generator.next();
console.log(res); // { value: 1, done: false }

res = generator.next(10);
console.log(res); // { value: 20, done: false }

res = generator.next(20);
console.log(res); // { value: 30, done: true }
```

## 46.5 제너레이터의 활용

### 46.5.1 이터러블의 구형

제네리이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블을 구현할 수 있습니다.

```javascript
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
})();

for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8 ... 2584 4181 6765
}
```

### 46.5.2 비동기 처리

제너레이터 함수는 `next` 메서드와 `yield` 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있습니다. 이러한 특성을 활용하면 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있습니다. 다시 마랳, 프로미스의 후속 처리 메서드 `then`, `catch`, `finally` 없이 비동기 처리 결과를 반환하도록 구현할 수 있습니다.

```javascript
const fetch = require("node-fetch");

const async = (generatorFunc) => {
  const generator = generatorFunc();

  const onResolved = (arg) => {
    const result = generator.next(arg);

    return result.done
      ? result.value
      : result.value.then((res) => onResolved(res));
  };

  return onResolved;
};

async(function* fetchTodo() {
  const url = "https://jsonplaceholder.typicode.com/todos/1";

  const response = yield fetch(url);
  const todo = yield response.json();

  console.log(todo);
})();
```

위 예제의 제너레이터 함수를 실행하는 제너레이터 실행긴인 `async` 함수는 이해를 돕기 위해 갈략화한 예제이므로 완전하지 않습니다. 다음에 설명할 `async/await`를 사용하면 `async` 함수와 같은 제너레이터 실행기를 사용할 필요가 없지만 혹시 제너레이터 실행기가 필요하다면 직접 구현하는 것보다 **co** 라이브러리를 사용하기 바랍닙다.

```javascript
const fetch = require("node-fetch");
const co = require("co");

co(function* fetchTodo() {
  const url = "https://jsonplaceholder.typicode.com/todos/1";

  const response = yield fetch(url);
  const todo = yield response.json();

  console.log(todo);
});
```

## 46.6 async/await

제너레이터를 사용해서 비동기 처리를 동기 처리처럼 동작하도록 구현했지만 코드가 무척이나 장황해지고 가독성도 나빠졌습니다. ES8(ECMAScript 2017)에서는 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 `async/await`가 도입되었습니다.

`async/await`는 프로미스를 기반으로 동작합니다. `async/await`를 사용하면 프로미스 `then`, `catch`, `finally` 후속 처리 메서드에 콜백 함수를 전달해서 비동기 처리 결과를 후속 처리할 필요 없이 마치 동기 처리처럼 프로미스를 사용할 수 있습니다. 다시 말해, 프로미스의 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있습니다.

### 46.6.1 async 함수

`await` 키워드는 반드시 `async` 함수 내부에서 사용해야 합니다. `async` 함수는 `async` 키워드를 사용해 정의하며 언제나 프로미스를 반환합니다. `asnyc` 함수가 명시적으로 프로미스를 반환하지 않더라도 `asnyc` 함수는 암묵적으로 반환값을 `resolve`하는 프로미스를 반환합니다.

```javascript
// async 함수 선언문
async function foo(n) {
  return n;
}
foo(1).then((v) => console.log(v)); // 1

// async 함수 표현식
const bar = async function (n) {
  return n;
};
bar(2).then((v) => console.log(v)); // 2

// async 화살표 함수
const baz = async (n) => n;
baz(3).then((v) => console.log(v)); // 3

// async 메서드
const obj = {
  async foo(n) {
    return n;
  },
};
obj.foo(n).then((v) => console.log(v)); // 4

// async 클래스 메서드
class MyClass {
  async bar(n) {
    return n;
  }
}
new MyClass().bar(5).then((v) => console.log(v)); // 5
```

클래스의 `constructor` 메서드는 `async` 메서드가 될 수 없습니다. 클래스의 `constructor` 메서드는 인스턴스를 반환해야 하지만 `async` 함수는 언제나 프로미스를 반환해야 합니다.

```javascript
class MyClass {
  async constructor() {
    // SyntaxError: Class constructor may not be an async method
  }
}

const myClass = new MyClass();
```

### 46.6.2 await 키워드

`await` 키워드는 프로미스가 `settled` 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 `settled` 상태가 되면 프로미스가 `resolve`한 처리 결과를 반환합니다. `await` 키워드는 반드시 프로미스 앞에서 사용해야 합니다.

```javascript
const fetch = require("node-fetch");

const getFithubUserName = async (id) => {
  const res = await fetch(`https://api.github.com/users/${id}`);
  const { name } = await res.json();
  console.log(nams);
};

getFithubUserName("ungmo2");
```

## 46.6.3 에러 처리

비동기 처리를 위한 콜백 패턴의 단점 중 가장 심각한 것은 에러 처리가 곤란하다는 것입니다. 에러는 **호출자**(Caller) 방향으로 전파됩니다. 즉, 콜 스택의 아래 방향(실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파됩니다. 하지만 비동기 함수의 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에 `try ... catch`문을 사용해 에러르 캐치할 수 없습니다.

```javascript
try {
  setTimeout(() => {
    throw new Error("Error!");
  }, 3000);
} catch (e) {
  console.error(e);
}
```

`async/await`에서 에러 처리는 `try ... catch` 문을 사용할 수 있습니다. 콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 하수는 명시적으로 호출할 수 있기 때문에 호출자가 명확합니다.

```javascript
const fetch = require("node-fetch");

const foo = async () => {
  try {
    const wrongUrl = "https://wrong.url";

    const response = await fetch(wrongUrl);
    const data = await response.json();
    console.log(data);
  } catch (e) {
    console.error(e); // TypeError: Failed to fetch
  }
};

foo();
```

`async` 함수 내에서 `catch` 문을 사용해서 에러 처리를 하지 않으면 `async` 함수는 발생한 에러를 `reject`하는 프로미스를 반환합니다. 따라서 `async` 함수는 호출하고 `Promise.prototype.catch` 후속 처리 메서드를 사용해 에러를 캐치할 수도 있습니다.

```javascript
const fetch = require("node-fetch");

const foo = async () => {
  const wrongUrl = "https://wrong.url";

  const response = await fetch(wrongUrl);
  const data = await response.json();

  return data;
};

foo().then(console.log).catch(console.error);
```
