# 이터러블

## 1. 이터레이션 프로토콜

ES6에서 도입된 **이터레이션 프로토콜**(Iteration protocol)은 **순회 가능한**(Iterable) 데이터 컬렉션(자료구조)을 만들기 위해 ECMAScript 사양에 정의하여 미리 약속한 규칙입니다.

ES6 이전의 순회 가능한 데이터 컬렉션, 즉 배열, 문자열, 유사 배열 객체, DOM 컬렉션 등은 통일된 규약 없이 각자 나름의 구조를 가지고 `for`문, `for ... in`문, `forEach` 메서드 등 다양한 방법으로 순회할 수 있었습니다. ES6에서는 순회 가능한 데이터 컬렉션을 이터레이션 프로토콜을 준수하는 이터러블로 통일하여 `for ... of`문, 스프레드 문법, 배열 디스트럭처링 할당의 대상으로 사용할 수 있도록 일원화했습니다.

### 1.1 이터러블

Well-known Symbol인 `Symbol.iterator`를 프로퍼티 키로 사용한 메서드를 직접 구형하거나 프로토타입 체인을 통해 상속 받은 `Symbol.iteraotr` 메서드를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환합니다. 이러한 규약을 이터러블 프로토콜이라 하며, 이터러블 프로토콜을 준수한 객체를 이터러블이라고 합니다.

```javascript
const isIterable = (v) => {
  return v !== null && typeof v[Symbol.iterator] === "function";
};

isIterable([]); // true
isIterable(""); // true
isIterable(new Map()); // true
isIterable(new Set()); // true
isIterable({}); // false
```

이터러블은 `for...of` 문으로 순회할 수 있으며 스프레드 문법과 배열 디스트럭처링 할당의 대상으로 사용할 수 있습니다.

```javascript
const array = [1, 2, 3];

console.log(Symbol.iterator in array); // true

for (const item of array) {
  console.log(item);
}

console.log([...array]); // [1, 2, 3]

const [a, ...rest] = array;
console.log(rest); // [2, 3]
```

`Symbol.iterator` 메서드를 직접 구현하지 않거나 상속받지 않은 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아닙니다. 따라서 일반 객체는 `for...of` 문으로 순회할 수 없으며 스프레드 문법과 배열 디스트럭처링 할당의 대상으로 사용할 수 없습니다. 단, 2020년 7월 현재, TC39 프로세스의 stage 4(Finished) 단계에 제안되어 있는 스프레드 프로퍼티 제안은 일반 객체에 스프레드 문법의 사용을 허용합니다.

```javascript
const obj = { a: 1, b: 2 };
console.log({ ...obj });
```

### 1.2 이터레이터

이터러블의 `Symbol.iterator` 메서드를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환합니다. 이터레이터는 `next` 메서드를 소유하며, `next` 메서드를 호출하면 이터러블을 순회하며 `value`와 `done` 프로퍼티를 갖는 **이터레이터 리절트 객체**(Iterator Resoult Object)를 반환합니다. `value` 프로퍼티는 현재 순회중인 이터러블의 값을 나타내며, `done` 프로퍼티는 이터러블의 순회 완료 여부를 나타냅니다. 이러한 규약을 이터레이터 프로토콜이라 하며, 이터레이터 프로토콜을 준수한 객체를 이터레이터라 합니다. 이터레이터는 이터러블의 요 소를 탐색하기 위한 포인터 역할을 합니다.

```javascript
const array = [1, 2, 3];

const iterator = array[Symbol.iterator]();
console.log("next" in iterator); // true

console.log(iterator.next()); // { vlaue: 1, done: flase}
console.log(iterator.next()); // { vlaue: 2, done: flase}
console.log(iterator.next()); // { vlaue: 3, done: flase}
console.log(iterator.next()); // { vlaue: undefined, done: true}
```

## 2. 빌트인 이터러블

자바스크립트는 이터레이션 프로토콜을 준수한 객체인 빌트인 이터러블을 제공합니다., 다음의 표준 빌트인 객체들은 빌트인 이터러블입니다.

| 빌트인 이터러블 | Symbol.iterator 메서드                                                             |
| --------------- | ---------------------------------------------------------------------------------- |
| Array           | `Array.prototype[Symbol.iterator]`                                                 |
| String          | `String.prototype[Symbol.iterator]`                                                |
| Map             | `Map.prototype[Symbol.iterator]`                                                   |
| Set             | `Set.prototype[Symbol.iterator]`                                                   |
| TypedArray      | `TypedArray.prototype[Symbol.iterator]`                                            |
| arguments       | `arguments[Symbol.iterator]`                                                       |
| DOM 컬렉션      | `NodeList.prototype[Symbol.iterator]`, `HTMLCollection.prototype[Symbol.iterator]` |

## 3. for...of 문

`for...of` 문은 이터러블을 순회하면서 이터러블의 요소를 변수에 할당합니다. `for...of` 문의 문법은 다음과 같습니다.

```
for (변수선언문 of 이터러블) { ... }
```

`for...of` 문은 `for...in` 문의 형식과 매우 유사합니다.

```
for (변수선언문 in 객체) { ... }
```

`for...in` 문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `true`인 프로퍼티를 순회하며 **열거**(enumeration)합니다. 이때 프로퍼티 키가 심벌인 프로퍼티는 열거하지 않습니다.

`for...of` 문은 내부적으로 이터레이터의 `next` 메서드를 호출하여 이터러블을 순회하며 `next` 메서드가 반환한 이터레이터 리절트 객체의 `value` 프러퍼티 값을 `for...of` 문의 변수에 할당합니다. 그리고 이터레이터 리절트 객체의 `done` 프로퍼티 값이 `false`이면 이터러블의 순회를 계속하고 `true`이면 이터러블의 순회를 중단합니다.

```javascript
for (const item of [1, 2, 3]) {
  console.log(item); /// 1 2 3
}
```

## 4. 사용자 정의 이터러블

### 4.1 사용자 정의 이터러블 구현

이터레이션 프로토콜을 준수하지 않는 일반 객체도 이터레이션 프로토콜을 준수하도록 구현하면 사용자 정의 이터러블이 됩니다.

```javascript
const fibonacci = {
  [Symbol.iterator]() {
    let [pre, cur] = [0, 1];
    const max = 10;

    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { value: cur, done: cur >= max };
      },
    };
  },
};

for (const num of fibonacci) {
  console.log(num); // 1 2 3 5 8
}

const arr = [...fibonacci];
console.log(arr); // [1, 2, 3, 5, 8]

const [first, second, ...rest] = fibonacci;
console.log(first, second, rest); // 1 2 [3, 5, 8]
```

### 4.2 이터러블을 생성하는 함수

다음은 수열의 최대값을 인수로 전달받아 이터러블을 반환하는 함수입니다.

```javascript
function fibonacciFunc(max) {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return {
        next() {
          [pre, cur] = [cur, pre + cur];
          return { value: cur, done: cur >= max };
        },
      };
    },
  };
}

for (const num of fibonacciFunc(10)) {
  console.log(num);
}
```

### 4.3 이터러블이면서 이터레이터인 객체를 생성하는 함수

이터러블이면서 이터레이터인 객체를 생성하면 `Symbol.iterator` 메서드를 호출하지 않아도 됩니다. 다음 객체는 `Symbol.iterator` 메서드와 `next` 메서드를 소유한 이터러블이면서 이터레이터입니다.

```javascript
{
  [Symbol.iterator]() {
    return this;
  },
  next() {
    return { value: any, done: boolean };
  },
};
```

### 4.4 무한 이터러블과 지연 평가

배열이나 문자열 등은 모든 데이터를 메모리에 미리 확보한 다음 데이터를 공급합니다. 하지만 위 예제의 이터러블은 **지연 평가**(lazy Evaluation)를 통해 데이터를 생성합니다. 지연 평가는 데이터가 필요한 시점 이전까지는 미리 데이터를 생성하지 않다가, 데이터가 필요한 시점이 되면 그때야 비로소 데이터를 생성하는 기법입니다. 즉, 평가 결과가 필요할 때까지 평가를 늦추는 기법이 지연 평가입니다. 이처럼 지연 평가를 사용하면 불필요한 데이터를 미리 생성하지 않고, 필요한 데이터를 필요한 순간에 생성하므로 빠른 실행 속도를 기대할 수 있고, 불필요한 메모리를 소비하지 않으며, 무한도 표현할 수 있다는 장점이 있습니다.

```javascript
const fibonacciFunc = function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre + cur];
      return { value: cur };
    },
  };
};
```

## 5. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
