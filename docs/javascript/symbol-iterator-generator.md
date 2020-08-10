# Symbol

심볼은 ES6에서 새로 선보인 원시 타입입니다.

```javascript
typeof Symbol(); // 'symbol'
```

심볼은 다음과 같은 특징을 가지기에, Unique한 속성을 만들기 위해서 사용합니다.

1. 심볼은 Unique하기 때문에, description이 같아도 충돌하지 않습니다.
2. 심볼은 객체 속성을 순회하기 위한 `for in`, `Object,key()`, `Object.getOwnPropertyNames()`에 걸리지 않습니다.

```javascript
const mySymbol1 = Symbol("mySymbol");
const mySymbol2 = Symbol("mySymbol");
mySymbol1 === mySymbol; // false

const prop2 = Symbol("prop2");
const obj = {
  prop1: 1,
  [prop2]: 2,
};

for (const key in obj) {
  console.log(key); // prop1만 출력됨
}

obj[prop2]; // 대괄호 []로만 접근 가능
```

`Object.getOwnPropertySymbols()`를 사용하면 심볼 키들을 조회할 수 있고, `Refelect.ownKeys()`에서는 문자열 키와 심볼 키를 모두 조회합니다.

## 심볼의 생성

심볼은 다음과 같은 3가지 방법으로 생성할 수 있습니다.

- `Symbol('some')`: 고유한 심볼을 리턴합니다.
- `Symbol.for('some')`: 심볼 레지스트리에서 lookup하여 description이 같으면 매번 같은 심볼을 리턴합니다.
- 상용 심볼: `Symbol.iterator`와 같이 표준에 정의된 심볼을 사용합니다. 상용 심볼은 특별한 용도를 위해서 미리 만들어 놓은 것입니다.

대표적인 상용 심볼은 다음과 같습니다.

- `Symbol.iterator`: 이터러블한 객체를 정의하기 위한 심볼
- `Symbol.hasInstance`: `instanceof`를 확장하기 위한 심볼
- `Symbol.match`: `str.match(obj)` 메소드는 `obj`를 정규표현식으로 변환해서 문자열 검색을 수행하는데, 이 기능을 확장하기 위한 심볼
- 기타 등등

상용 심볼은 `Symbol.match` 대신 `@@match`로 표기할 수도 있다.

## Iterable

그렇다면 **순회 가능한** 객체란 무엇일까? `Symbol.iterator` 심볼을 속성으로 가지고 있고, Iterator 객체를 반환하는 객체를 뜻합니다. 이런 스펙을 Iterable 프로토콜이라고 하며 이 프로토콜을 지킨 객체를 Iterable 객체라고 합니다.

## Iterator

그럼 이터러블 객체가 `[Symbol.iterator]()` 메서드로 반환하는 이터레이터 객체는 무엇일까? `next()` 메소드를 구현하고 있고, `done`과 `value` 속성을 가진 객체를 반환하는 객체입니다. 이런 스펙을 Iterator 프로토콜이라고 합니다. 디음은 0을 반환하는 무한 Iterator입니다.

```javascript
const zeroesForeverIterator = {
  [Symbol.iterator]: function () {
    return this;
  },
  next: function () {
    return { done: false, value: 0 };
  },
};
```

이터레이터 객체는 필요에 다라서 `return()` 메소드나 `throw()` 메서도를 구현할 필요도 있습니다.

- `return()`: `for of` 루프가 `break`, `return`문 때문에 정상 상황(`done`이 `true`인 상황)보다 먼저 루프를 빠져 나갈때 호출됩니다.
- `throw()`: `for of` 루프에서 발생한 예외를 Iterator` 안으로 전달하고 싶을 경우 사용합니다.

이터레이터를 이용하면 다양한 타입의 객체를 하나의 프로토콜로 순회하며 다룰 수 있고, 엄청나게 커다란 크기의 데이터라도 메모리의 부담 없이 Lazy-Evaluation이 가능하다는 성능상의 이점도 있습니다.

# Generator

Generator를 한마디로 말하자면, 이터러블, 이터레이터 객체를 만드는 손쉬운 방법입니다.

```javascript
class RangeIterator {
  constructor(start, stop) {
    this.value = start;
    this.stop = stop;
  }

  [Symbol.iterator]() {
    return this;
  }

  next() {
    const value = this.value;
    if (value < this.stop) {
      this.value += 1;
      return { done: false, value: value };
    } else {
      return { done: true, value: undefined };
    }
  }
}

function range(start, stop) {
  return new RangeIterator(start, stop);
}
```

위와 같은 내욜을 제네레이터를 이용하면 다음과 같이 만들 수 있습니다.

```javascript
function* range(start, stop) {
  for (var i = start; i < stop; i += 1) {
    yield i;
  }
}
```

제네레이터는 `function*` 문으로 시작하고 `yiedl`문이 있는 함수입니다. 함수의 작동 순서는 다음과 같습니다.

1. 제네레이터 함수를 일단 처음 실행하면 아무일도 일어나지 않습니다. 그냥 제네레이터 객체가 리턴됩니다.
2. 제네레이터 객체는 `next()` 메소드를 실행할 때마다 다음 `yield` 문까지 실행되고 정지합니다. 다시 `next()`를 실행하면 아까 멈첬던 `yield`부터 다음 `yeild`까지 실행하고 다시 정지합니다.
3. 더 이상 `yield`가 없으며 제네레이터의 실행은 완전히 종료됩니다.

제네레이터 객체는 이터레이터 객체이고 제네레이터 함수는 이터러블 객체이다(이터레이터를 반홤함). `next()` 메소드에 값을 전달할 수 있습니다. 그러면 제네레이터 안으로 전달한 값이 주입됩니다.

```javascript
function* myGen() {
  const x = yield 1; // x = 10
  const y = yield x + 1; // y = 20
  const z = yield y + 1; // z = 30
  return x + y + z;
}

const myItr = myGen();
console.log(myItr.next()); // { value: 1, done: false }
console.log(myItr.next(10)); // { value: 11, done: false }
console.log(myItr.next(20)); // { value: 22, done: false }
console.log(myItr.next(30)); // { value: 60, done: true }
```

`next()` 메소드가 리턴하는 값은 제네레이터가 실행을 정지할 때 `yield` 문이 반환하는 값(`yield`문의 오른쪽)이고, `next()` 메소드의 파라미터는 제네레이터가 실행을 재개할 때 `yield` 문이 반환하는 값(`yield` 문의 왼쪽)이 됩니다.

## 제네레이터의 의의

제네레이터를 사용하면, 이ㄹ터레이터를 적은 코드로 가독성까지 높여 쉽게 만들 수 있습니다. 하지만 제네레이터는 단순히 이터레이터를 쉽게 만들기 위해 생긴게 아니라, 동시성/비동기 프로그램을 위해 생겨났습니다.

#### 코루틴

일반적인 함수가 콜 스택 위에서 어떻게 동작하는지 다들 알고 있듯이, 실행될 때 콜 스택으로 올라오고 리턴하면 콜 스택에서 사라집니다. 이 함수의 진입점은 함상 같은 곳(함수의 시작 부분)이고, 한번 콜 스택에서 사라진 함수를 다시 불러올 수 있는 방법은 없습니다.

그러나 제네레이터는 `yield`문에 도착하면, 제네레이터의 스택 프렙임을 복사해놓고 콜 스택에서 일단 제거합니다. 그러나 `next()` 메소드가 호출되면 저장해 놓았던 스택 프레임을 다시 복원하고 실행합니다. 즉 진입점을 개발자가 원하는 대로 설정할 수 있고 한번 올라온 컨텍스트를 원하는 만큼 유지시킬 수 있습니다. 이런 개념을 코루팀이라고 합니다.

#### 동시성

이 코루틴 형태를 잘 이용하면, 협력형 멀티태스킹 방식으로, 쓰레드 프로그래밍 없이 동시성 프로그래밍이 가능해집니다. 쓰레드는 필요한 비용에 비해 신경써야 할 것들이 너무 많은 단점이 있지만, 코루틴은 OS의 암묵적인 스케줄링이나 컨텍스트 스위치 오버헤드, 세미포어 설정 같은 곤민으로부터 자유롭습니다.

#### 비동기

코루틴은 비동기로 돌아가는 코드를 작성하는데도 도움을 줄 수 있습니다. 실제로 코루틴 자체가 비동기로 작동하는 것은 아닙니다. (코루틴은 항상 동기적으로 작동합니다) 비동기 동작 자체는 코루틴과 별개지만, 비동기 애플리케이션 프로그래밍에 항상 등장하는 콜백 함수와 그로 인한 콜백 지옥을 해결책으로 코루틴이 유효합니다. 즉 보기에는 동기 방식으로 돌아갈 것 처럼 생겼지만 실제로는 비동기로 돌아가는 코드를 만들어 낼 수 있습니다.

```javascript
function* orderCoffee(phoneNumber) {
  const id = yield getId(phoneNumber);
  const email = yield getEmail(id);
  const name = yield getName(email);
  const result = yield order(name, "coffee");
  return result;
}

const iterator = orderCoffee("010-1234-1234");
iterator.next();

function getId(phoneNumber) {
  // ...
  iterator.next(result);
}

// ...
```

## 출처

- [gist.github.com](https://gist.github.com/qodot/ecf8d90ce291196817f8cf6117036997)
