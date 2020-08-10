# ECMAScript 6

## 1. Arrows

일반함수의 자신을 호출하는 객체를 가리키는 `dynamic this`와 달리 코드의 상위 스코프를 가리키는 `lexical this`를 가집니다.

```javascript
var evens = [2, 4, 6, 8];

// Express bodies (표현식의 결과가 반환됨)
var odds = evens.map((v) => v + 1);
var nums = evens.map((v, i) => v + i);
var pairs = evens.map((v) => ({ evens: v, odd: v + 1 }));

// Statement bodies (블럭 내부를 실행만 함, 반환을 위해선 return을 명시)
nums.forEach((v) => {
  if (v % 5 === 0) fives.push(v);
});

// Lexical this
var bob = {
  _name: "Bob",
  _friends: ["Jhon, Brian"],
  printFriends() {
    this._frineds.forEach((f) => console.log(this._name + " knows " + f));
  },
};
```

## 2. Classes

ES6 클래스는 프로토타입 기반 객체지향 패턴을 더 쉽게 사용할 수 잇는 대체재입니다.

```javascript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    this.idMatrix = SkinnedMesh.defaultMatrix();
    this.bones = [];
    this.boneMatrices = [];
  }

  update(camera) {
    super.update();
  }

  get boneCount() {
    return this.bones.length;
  }

  set matrixType(matrixType) {
    this.idMatrix = SkinnedMesh[matrixType]();
  }

  static defaultMatrix() {
    return new THREE.Matrix4();
  }
}
```

## 3, Enhanced Object Literals

```javascript
var obj = {
  __proto__: thiProtoObj, // __proto__
  handler, // handler: handler의 단축 표기
  toString() {
    return "d " + super.toString(); // super calls
  },
  ["prop_" + (() => 42)()]: 42, // dynamic names
};
```

## 4. Template Strings

```javascript
// Basic literal string creation
var test1 = `In JavaScript '\n' is a line-feed.`;

// Multiline strings
var test2 = `In JavaScript this is
 not legal.`;

// String interpolation
var name = "Bob";
var time = "today";
var test3 = `Hello ${name}, how are you ${time}?`;
```

## 5. Destructuring

Destructuring는 배열과 객체에 패턴 매칭을 통한 데이터 바인딩을 제공합니다. Destructuring는 할당 실패에 유연하며, 실패 시 undefined 값이 자동할당 됩니다. 또한 foo["bar"]와 같이 객체의 속성 값도 자동으로 검색하여 바인딩해줍니다.

```javascript
// list matching
var [a, , b] = [1, 2, 3];

// object matching
var { op, lhs, rhs } = getASTNode();
var {
  op: a,
  lhs: { op: b },
  rhs: c,
} = getASTNode();

// parameter에서도 사용 가능
function g({ name: x }) {
  console.log(x);
}

// Fail-soft destructuring
var [a] = [];
a === undefined;

// Fail-soft destructuring with defaults
var [a = 1] = [];
```

## 6. Default + Rest + Spread

```javascript
function f1(x, y = 12) {
  return x + y;
}
f1(3); // 15

function f2(x, ...y) {
  return x * y.length;
}
f2(3, "hello", true); // 6

function f3(x, y, z) {
  return x + y + z;
}
f3(...[1, 2, 3]); // 6
```

## 7. Let + Const

블록 유효 범위를 갖는 새로운 변수 선언 방법을 지원합니다. let은 var와 유사하게 동작합니다. const는 재할당 및 재선언이 불가능합니다. var의 유효 범위는 전체 외부 함수까지이지만 let은 변수를 선언한 블록과 그 내부 블록들에서 유효합니다.

## 8. Iterators + For…Of

Iterator 객체는 CLR의 IEnumerable 혹은 Java의 Iterable처럼 사용자 정의의 반복을 가능하게 해줍니다. `for..of` 반복문이 ES6에서 추가되었으며 `for..in` 반복문과 달리 iterator 기반의 컬렉션 전용 반복문입니다.

```javascript
const fibonacci = {
  [Symbol.iterator]() {
    let pre = 0;
    let cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur };
      },
    };
  },
};

for (const n of fibonacci) {
  if (n > 1000) break;
  console.log(n);
}
```

## 9. Generators

Generators는 `function*`와 `yield` 키워드를 이용하여 iterator 선언을 단순하게 작성할 수 있게 도와줍니다. `function*`로 선언한 함수는 Generator 객체를 반환합니다. Generators는 iterator의 하위 타입이며 `next`와 `throw` 메서드를 가지고 있습니다. 이 메서드들로 인해 `yield`키워드로 반환된 값은 다시 generator에 주입되거나 예외처리를 할 수 있게 되었습니다.

```javascript
const fibonacci = {
  [Symbol.iterator]: function* () {
    let pre = 0;
    let cur = 1;
    while (1) {
      [pre, cur] = [cur, pre + cur];
      yield cur;
    }
  },
};

for (const n of fibonacci) {
  if (n > 20) break;
  console.log(n);
}
```

```javascript
function* gen() {
  yield* ["a", "b", "c"];
}
const a = gen();

a.next(); // { value: "a", done: false }
a.next(); // { value: "b", done: false }
a.next(); // { value: "c", done: false }
a.next(); // { value: undefined, done: true }
```

## 10. Unicode

완전한 유니코드를 지원하기 위해 문자열에 새로운 유니코드 리터럴과 정규표현식에 u 모드가 추가되었습니다. 또한 21비트 형식까지 처리하기 위한 신규 API가 추가되었습니다. 이 추가된 기능은 JavaScript로 글로벌 앱을 만들 수 있도록 지원합니다.

```javascript
// same sa ES5.1
"𠮷".length === 2;

// new RegExp behaviour, opt-in 'u'
"𠮷".match(/./u)[0].length === 2;

// new form
"\u{20BB7}" === "𠮷";
"𠮷" === "\uD842\uDFB7";

// new String ops
"𠮷".codePointAt(0) === 0x20bb7;

// for-of iterates code points
for (const c of "𠮷") {
  console.log(c); // 𠮷
}
```

## 11. Modules

언어 차원에서 컴포넌트 정의를 위한 모듈을 지원합니다. 목시적 비동기 형태로 요구되는 모듈들이 정상적으로 로드되기 전까지 코드가 실행되지 않습니다.

```javascript
import * as math from "lib/math";
import { sum, pi } from "lib/math";
import ln, { pi, e } from "lib/math";

export * from "lib/math";
export var e = 2.71828182846;
export default function (x) {
  return Math.log(x);
}
```

## 12. 동적 로딩(Dynamic loading)

```javascript
// 동적 로딩 – ‘System’ is default loader
System.import("lib/math").then(function (m) {
  console.log("2π = " + m.sum(m.pi, m.pi));
});
```

## 13. Map + Set + WeakMap + WrakSet

일반 알고리즘을 위한 효율적인 데이터 구조를 제공합니다. WeakMap과 WeakSet는 메모리 누수로 부터 자유롭게 해줍니다. 이들 내 저장된 객체에 다른 참조가 없는 경우, garbage collaction 될 수 있습니다.

```javascript
const s = new Set();
s.add("hello").add("hoodbye").add("hello");
s.size === 3;
s.has("hello") === true;

const m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) === 34;

const ws = new WeakSet();
ws.add({ data: 42 });
ws.size; // undefined (사용된 곳이 없기 때문)

const wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size; // undefined (사용된 곳이 없기 때문)
```

## 14. Proxies

프록시를 사용하면 호스트 객체에 다양한 기능을 추가하여 객체를 생성할 수 있습니다. interception, 객체 추상화, 로깅 / 수집, 값 검증 등에 사용될 수 있습니다.

```javascript
const test = new Proxy(
  {},
  {
    set: function (target, property, value) {
      target[property] = value;
    },
    get: function (target, property) {
      return target[property];
    },
    has: function (target, property) {
      return target[property] ? true : false;
    },
  }
);

/* set */
test.hello = "world";
/* get */
console.log(test.hello); // world
/* has */
console.log("hello" in test);
```

```javascript
const test = new Proxy(function () {}, {
  apply: function (target, thisArg, argumentsList) {
    console.log(this, argumentsList.join(","));
  },
});

test(1, 2, 3); // window 1,2,3
```

다음은 **Proxy**의 `handler`가 가질 수 있는 트랩들입니다.

```javascript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

## 15. Symbols

심볼은 객체 상태의 접근 제어를 가능하ㅔ 합니다. Symbol은 새로운 원시 타입으로 충돌의 위험 없이 속성의 키를 사용할 수 있습니다. 옵션 파라미터인 description는 디버깅 용도로 사용되면 식별 용도는 아닙니다. Symbol은 고유하며, `Object.getOwnPropertySymbols`와 같은 reflection 기능들로 접근할 수 있기 때문에 private 하지 않습니다. (`for in`나 `Object.keys()`로는 접근 불가).

```javascript
const map = {};
const a = Symbol("a");

map[a] = 123;
map["b"] = 456;

console.log(map[a]); // 123
console.log(map["b"]); // 456

for (let key in map) {
  console.log(key); // b
}

Object.keys(map); // ["b"]
```

## 16. Subclassable Built-ins

ES6에서 Array, Date, DOM Element 같이 내장 객체들은 상속이 가능합니다. 객체 생성 시 호출되는 `Ctor` 함수는 다음의 2단계를 가집니다. (둘다 가상적으로 실행)

1. 객체 할당을 위해 Ctor[@@create] 호출하여
2. 새로운 인스턴스의 생성자를 호출해 초기화 진행

아시가싶아ㅣ `@@create` 심볼은 `Symbold.create`를 통해 만들어졌습니다.

```javascript
// Pseudo-code of Array
class Array {
  constructor(...args) {
    /* ... */
  }
  static [Symbol.create]() {
    // Install special [[DefineOwnProperty]]
    // to magically update 'length'
  }
}

// User code of Array subclass
class MyArray extends Array {
  constructor(...args) {
    super(...args);
  }
}

// Two-phase 'new':
// 1) Call @@create to allocate object
// 2) Invoke constructor on new instance
var arr = new MyArray();
arr[1] = 12;
arr.length == 2;
```

## 17. Math + Number + String + Array + Object APIs

core Math 라이브러리, Array 생성 helper, String helper, 복사를 위한 Object.assign 등 많은 라이브러리들이 추가되었습니다.

```javascript
Number.EPSILSON;
Number.isInteger(Infinity); // false
Number.isNaN("NaN"); // false

Math.acosh(3); // hyperbolic arc-cosine of a number => 1.76274...
Math.hypot(3, 4); // 	인수로 전달받은 값들을 각각 제곱한 후 더한 총합의 제곱근을 반환함 => 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2); // 	인수로 전달받은 두 값의 32비트 곱셈의 결과를 반환함 => 2

"abcde".includes("cd");
"abc".repeat(3);

Array.from(document.querySelectorAll("*")); // Returns a real Array
Array.of(1, 2, 3); // Array()와 비슷하지만, argument가 하나여도 배열을 생성
[0, 0, 0].fill(7, 1); // [0,7,7]
[1, 2, 3].find((x) => x == 3); // 3
[1, 2, 3].findIndex((x) => x == 2); // 1
[1, 2, 3, 4, 5].copyWithin(3, 0); // [1, 2, 3, 1, 2]
["a", "b", "c"].entries(); // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys(); // iterator 0, 1, 2
["a", "b", "c"].values(); // iterator "a", "b", "c"
```

## 18. Binary and Octal

2진법 (b), 8진법 (o) numeric 리터럴 형식이 추가되었습니다.

```javascript
0b111110111 === 503; // true
0o767 === 503; // true
```

## 19. Promises

Promise는 비동기 프로그래밍을 위한 라이브러리입니다. Promise는 미래에 생성되는 값을 나타내는 일급 개게이빈다. Promsie는 현존하는 많은 Javascript 라이브러리에 사용되고 있습니다.
[자세한 내용](./promise.md)

## 20. Reflect API

Reflection API는 런타임 시 객체에 대해 작업을 수행할 수 있습니다. 프록시 트랩(proxy traps)와 같은 메타 함수들을 가지고 있습니다. Reflection은 프록시를 구현하는데 유용합니다.

```javascript
class Greeting {
  constructor(name) {
    this.name = name;
  }

  greet() {
    return `Hello ${name}`;
  }
}

function greetingFactory(name) {
  return Reflect.construct(Greeting, [name], Greeting);
}

greetingFactory("a"); // Greeting {name: "a"}
```

## 21. Tail Calls

마지막에 호출되는 함수가 호출 스택이 초과되게 하지 않습니다. 재귀 알고리즘을 매우 큰 입력 값에서도 안전하게 만듭니다.

```javascript
function factorial(n, acc = 1) {
  "use strict";
  if (n <= 1) return acc;
  return factorial(n - 1, n * acc);
}

// 현재 대부분의 자바스크립트 엔진에서 스택 오버플로우가 일어나지만,
// ES6에서는 입력 값이 커도 안전하다
factorial(100000);
```

## 22. 츨차

- [JSDEV - kkd927](https://jsdev.kr/t/es6/2944)
