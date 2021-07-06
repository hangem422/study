# ES6 함수의 추가 기능

## 1. 함수의 구분

ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출활 수 있습니다. 다시 말해, ES6 이전의 모든 함수는 callable이면서 constructor입니다. 주의할 것은 ES6 이전에 일반적으로 메서드라고 부르던 객체에 바인딩된 함수도 callable이며 constructor라는 것입니다. 따라서 객체에 바인딩된 함수도 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수도 있습니다.

```javascript
var obj = {
  x: 10,
  f: function () {
    return this.x;
  },
};

// 메서드로서 호출
console.log(obj.f()); // 10

// 일반 함수로서 호출
var bar = obj.f;
console.log(bar()); // undefined

// 생성자 함수로서 호출
console.log(new obj.f()); // f {}
```

위 예제와 같이 객체에 바인딩된 함수를 생성자 함수로 호출하는 경우가 흔치는 않겠지만 문법상 가능하다는 것은 문제가 있습니다. 그리고 이는 성능 면에서도 문제가 있습니다. 객체에 바인딩된 함수가 constructor라는 것은 객체에 바인딩된 함수가 `prototype` 프로퍼티를 가지며, 프로토타입 객체도 생성한다는 것을 의미하기 때문입니다. 함수에 전달되는 보조 함수의 역할을 수행하는 콜백 함수도 마찬가지입니다. 이처럼 ES6 이전의 모든 함수는 사용 목적에 따라 명확한 구분이 없으므로 호출 방식에 특별한 제약이 없고 생성자 함수로 호출되지 않아도 프로토타입 객체를 생성합니다. 이는 혼란스러우며 실수를 유발할 가능성이 있습니다.

| ES6 함수의 구분    | constructor | prototype | super | arguments |
| ------------------ | :---------: | :-------: | :---: | :-------: |
| 일반 함수(Normal)  |      O      |     O     |   X   |     O     |
| 메서드(Method)     |      X      |     X     |   O   |     O     |
| 화살표 함수(Arrow) |      X      |     X     |   X   |     X     |

## 2. 메서드

ES6 이전 사양에는 메서드에 대한 명확한 정의가 없었습니다. 일반적으로 메서드는 객체에 바인딩된 함수를 일컫는 의미로 사용되었습니다. ES6 사양에서는 메서드에 대한 정의가 명확하게 규정되었습니다. ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미합니다. ES6 사양에서 정의한 메서드는 인스턴스를 생성할 수 없는 non-constructor입니다.

```javascript
const obj = {
  x: 1,
  // foo는 메서드입니다.
  foo() {
    return this.x;
  },
  // bar에 바인딩된 함수는 메서드가 아닌 일반 함수입니다.
  bar: function () {
    return this.x;
  },
};

console.log(obj.foo()); // 1
console.log(obj.bar()); // 1

new obj.foo(); // TypeError: obj.foo is not a constructor
new obj.bar(); // bar {}

obj.foo.hasOwnProperty("prototype"); // false
obj.bar.hasOwnProperty("prototype"); // true
```

참고로 표준 빌드인 객체가 제공하는 프로토타입 메서드와 정적 메서드는 모두 non-constructor입니다.

```javascript
String.prototype.toUpperCase.prototype; // undefined
String.formCharCode.prototype; // undefined
```

ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 `[[HomeObject]]`를 갖습니다. `super` 참조는 내부 슬롯 `[[HomeObject]]`를 사용하여 수퍼클래스의 메서드를 참조하므로 내부 슬롯 `[[HomeObject]]`를 갖는 ES6 메서드는 `super` 키워드를 사용할 수 있습니다.

```javascript
const base = {
  name: "Lee",
  sayHi() {
    return `Hi! ${this.name}`;
  },
};

const derived = {
  __proto__: base,
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  },
};

console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

이처럼 ES6 메서드는 본연의 기능(super)을 추가하고 의미적으로 맞지 않는 기능(constructor)은 제거했습니다. 따라서 메서드를 정의할 때 프로퍼티 값으로 익명 함수 표현식을 할당하는 ES6 이전의 방식은 사용하지 않는 것이 좋습니다.

## 3. 화살표 함수

**화살표 함수**(Arrow Function)는 `function` 키워드 대신 화살표(`=>`, fat arrow)를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의할 수 있습니다. 화살표 함수는 표현만 간략한 것이 아니라 내부 동작도 기존의 함수보다 간략합니다. 특히 화살표 함수는 콜백 함수 내부에서 `this`가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용합니다.

### 3.1. 화살표 함수 정의

#### 3.1.1 함수 정의

화살표 함수는 함수 선언문으로 정의할 수 없고, 함수 표현식으로 정의해야 합니다. 호출 방식은 기존 함수와 동일합니다.

```javascript
const multiply = (x, y) => x + y;
multiply(2, 3); // 6
```

#### 3.1.2 매개변수 선언

매개변수가 여러 개인 경우 소괄호 `()` 안에 매개변수를 선언합니다. 매개변수가 한 개인 경우 소괄호 `()`를 생략할 수 있습니다. 매개변수가 없는 경우 소괄호 `()`를 생략할 수 없습니다.

```javascript
const arrow1 = (x, y) => { ... };
const arrow2 = x => { ... };
const arrow3 = () => { ... };
```

#### 3.1.3 함수 몸체 정의

함수 몸체가 하나의 문으로 구성된다면 함수 몸체를 감사는 중괄호 `{}`를 생략할 수 있습니다. 이때 함수 몸체 내부의 문이 값으로 평가될 수 있는 표현식이 문이라면 암묵적으로 반환됩니다. 함수 몸체를 감싸는 중괄호 `{}`를 생략한 경우 함수 몸체 내부의 문이 표현식이 아닌 문이라면 에러가 발생합니다. 표현식이 아닌 문은 반환할 수 없기 때문입니다.

```javascript
const power = (x) => x ** 2;
// const power = (x) => { return x ** 2; };
power(2); // 4
```

객체 리터럴을 반환하는 경우 객체 리터럴을 소괄호 `()`로 감싸 주어야 합니다. 객체 리터럴을 소괄호 `()`로 감싸지 않으면 객체 리터럴의 중괄호 `{}`를 함수 몸체를 감싸는 중괄호 `{}`로 잘못 해석합니다.

```javascript
const create = (id, content) => ({ id, contnet });
// const create = (id, contnet) => { return { id, contnet }; };
create(1, "JavaScript"); // { id: 1, content: "JavaScript" }
```

함수 몸체가 여러 개의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 `{}`를 생략할 수 없습니다. 이때 반환값이 있다면 명시적으로 반환햐야 합니다.

```javascript
const sum = (a, b) => {
  const result = a + b;
  return result;
};
```

화살표 함수도 **즉시 실행 함수**(IIFE)로 사용할 수 있습니다.

```javascript
const person = ((name) => ({
  sayHi() {
    return `Hi? My name is ${name}.`;
  },
}))("Lee");

console.log(person.sayHi()); // Hi? My name is Lee.
```

화살표 함수도 일급 객체이므로 **고차 함수**(Higher-Oreder Function, HOF)에 인수로 전달할 수 있습니다. 이 경우 일반적인 함수 표현식보다 표현이 간결하고 가독성이 좋습니다.

```javascript
[1, 2, 3].map((v) => v * 2); // [2, 4, 6]
```

### 3.2 화살표 함수와 일반 함수의 차이

#### 3.2.1 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor입니다.

화살표 함수는 인스턴스를 생성할 수 없으므로 `prototype` 프로퍼티가 없고 프로토타입도 생성하지 않습니다.

```javascript
const Foo = () => {};
Foo.hasOwnProperty("prototype"); // false
```

#### 3.2.2 중복된 매개변수 이름을 선언할 수 없습니다.

strict mode가 아닌 일반 함수는 중복된 매개변수 이름을 선언해도 에러가 발생하지 않습니다.

```javascript
function normal(a, a) {
  return a + a;
}

console.log(normal(1, 2)); // 4
```

화살표 함수에서는 중복된 매개변수 이름을 선언하면 에러가 발생합니다.

```javascript
const arrow = (a, a) => a + a; // SyntaxError
```

#### 3.2.3 화살표 할수는 자체의 this, arguments, new.target 바인딩을 갖지 않습니다.

따라서 화살표 함수 내부에서 `this`, `arguments`, `super`, `new.target`을 참조하면 스코프 체인을 통해 상위 스코프의 식별자를 참조합니다.

### 3.3 this

화살표 함수가 일반 함수와 구별되는 가장 큰 특징은 바로 `this`입니다. 이는 **콜백 함수 내부의 this 문제**, 즉 콜백 함수 내부의 `this`가 외부 함수의 `this`와 다르기 때문에 발생하는 문제를 해결하기 위해 의도적으로 설계된 것입니다. 이와 같은 **콜백 함수 내부의 this 문제**를 해결하기 위해 ES6 이전에는 다음과 같은 방법을 사용했습니다.

#### 3.3.1 객체를 가리키는 this를 일단 회피시킨 후에 콜백 함수 내부에서 사용합니다.

```javascript
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    //this를 일단 회피시킵니다.
    const that = this;
    return arr.map(function (itme) {
      return that.prefix + " " + item;
    });
  }
}
```

#### 3.3.2 빌트인 객체 프로토타입 메서드의 두 번째 인수로 객체를 가리키는 this를 전달합니다.

```javascript
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map(function (itme) {
      return this.prefix + " " + itme;
    }, this);
  }
}
```

#### 3.3.3 Fuction.prototype.bind 메서드를 사용하여 객체를 가리키는 this를 바인딩합니다.

```javascript
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map(
      function (item) {
        return this.prefix + " " + item;
      }.bind(this)
    );
  }
}
```

ES6에서는 화살표 함수를 사용하여 **콜백 함수 내부의 this 문제**를 해결할 수 있습니다. 화살표 함수는 함수 자체의 `this` 바인딩을 갖지 않습니다. 따라서 화살표 함수 내부에서 `this`를 참조하면 상위 스코프의 `this`를 그대로 참조합니다. 이를 `Lexical this`라고 합니다. 이는 마치 렉시컬 스코프와 같이 화살표 함수의 `this`가 함수가 정의된 위치에 의해 결정된다는 것을 의미합니다. 만약 화살표 함수와 화살표 함수가 중첩되어 있다면 상위 화살표 함수에도 `this` 바인딩이 없으므로 스코프체인 상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 `this`를 참조합니다.

```javascript
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map((item) => this.prefix + " " + item);
  }
}
```

화살표 함수는 함수 자체의 `this` 바인딩을 갖지 않기 때문에 `Function.prototype.call`, `Function.prototype.apply`, `Function.prototype.bind` 메서드를 사용해도 화살표 함수 내부 `this`를 교체할 수 없습니다. 화살표 함수가 `Function.prototype.call`, `Function.prototype.apply`, `Function.prototype.bind` 메서드를 호출할 수 없다는 의미가 아닙니다. 화살표 함수는 함수 자체의 `this` 바인딩을 갖지 않기 때문에 `this`를 교체할 수 없고 언제나 상위 스코프의 `this` 바인딩을 참조합니다.

```javascript
window.x = 1;

const normal = function () {
  return this.x;
};
const arrow = () => this.x;

console.log(normal.call({ x: 10 })); // 10
console.log(arrow.call({ x: 10 })); // 1
```

메서드를 화살표 함수로 정의하는 것은 피해야 합니다. 화살표 함수 내부의 `this`는 메서드를 호출한 객체를 가리키지 않고 상위 스코프의 `this`를 가리킵니다. 프로토타입의 객체의 프로퍼티에 화살표 함수를 할당하는 경우도 동일한 문제가 발생합니다.

```javascript
const person = {
  name: "Lee",
  sayHi: () => console.log(`Hi, ${this.name}`),
};

person.sayHi(); // Hi
```

클래스 필드 정의 제안을 사용하여 클래스 필드에 화살표 함수를 할당할 수 있습니다. 이때 클래스 필드에 할당한 화살표 함수 내부에서 `this`를 참조하면 상위 스코프의 `this` 바인딩을 참조합니다. 클래스 필드에 할당한 화살표 함수의 상위 스코프는 `constructor`입니다. 따라서 클래스 필드에 할당한 화살표 함수 내부에서 참조한 `this`는 `constructor` 내부의 `this` 바인딩과 같습니다. `constructor` 내부의 `this` 바인딩은 클래스가 생성한 인스턴스를 가리키므로, 클래스 필드에 할당한 화살표 함수 내부의 `this` 또한 클래스가 생성한 인스턴스를 가리킵니다. 하지만 클래스 필드에 할당한 화살표 함수는 프로토타입 메서드가 아니라 인스턴스 메서드가 됩니다.

```javascript
class Person {
  name = "Less";
  sayHi = () => console.log(`Hi ${this.name}`);

  // constructor() {
  //   this.name = "Lee";
  //   this.sayHi = () => console.log(`Hi ${this.name}`);
  // }
}

const person = new Person();
person.sayHi(); // Hi Lee
```

### 3.4 super

`super`는 내부 슬롯 `[[HomeObject]]`를 갖는 ES6 메서드 내에서만 사용할 수 있는 키워드입니다. 화살표 함수는 자체의 `super` 바인딩을 갖지 않습니다. 따라서 화살표 함수 내부에서 `super`를 참조하면 `this`와 마찬가지로 상위 스코프의 `super`를 참조합니다.

```javascript
class Base {
  constructor(name) {
    this.name = name;
  }

  seyHi() {
    return `Hi! ${this.name}`;
  }
}

class Derived extends Base {
  sayHi = () => `${super.sayHi()} how are you doing?`;
}

const derived = new Derived("Lee");
console.log(derived.sayHi()); // Hi! Lee how are you doing?
```

### 3.5 arguments

화살표 함수는 자체의 `arguments` 바인딩을 갖지 않습니다. 따라서 화살표 함수 내부에서 `arguments`를 참조하면 `this`와 마찬가지로 상위 스코프의 `arguments`를 참조합니다. 상위 스코프의 `arguments` 객체를 참조할 수는 있지만 화살표 함수 자신에게 전달된 인수 목록을 확인할 수 없고 상위 함수에게 전달된 인수 목록을 참조하므로 그다지 도움이 되지 않습니다. 따라서 화살표 함수로 가변 인자 함수를 구현해야 할 때는 반드시 Rest 파라미터를 사용해야 합니다.

```javascript
(function () {
  const foo = () => console.log(arguments); // [Arguments] { '0': 1, '1': 2 }
  foo(3, 4);
})(1, 2);
```

## 4. Rest 파라미터

### 4.1 기본 문법

Rest 파라미터(나머지 매개변수)는 매개변수 이름 앞에 세개의 점 `...`를 붙여서 정의한 매개변수를 의미합니다. Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받습니다.

```javascript
function foo(...rest) {
  console.log(rest); // [1, 2, 3, 4, 5]
}

foo(1, 2, 3, 4, 5);
```

일반 매개변수와 Rest 파라미터는 함께 사용될 수 있습니다. 이때 함수에 전달된 인수들은 매개변수와 Rest 파라미터에 순차적으로 할당됩니다. Rest 파라미터는 이름 그대로 먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 할당됩니다. 따라서 Rest 파라미터는 반드시 마지막 파라미터이어야 하며, 단 하나만 선언할 수 있습니다.

```javascript
function foo(param, ...rest) {
  console.log(param); // 1
  console.log(rest); // [2, 3, 4, 5]
}

function bar(...rest, param) {}

foo(1, 2, 3, 4, 5);
bar(1, 2, 3, 4, 5); // SyntaxError: Rest parameter must be last formal parameter
```

Rest 파라미터는 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 `length` 프로퍼티에 영향을 주지 않습니다.

```javascript
function foo(...rest) {}
console.log(foo.length); // 0

function bar(x, ...rest) {}
console.llg(bar.length); // 1
```

### 4.2 Rest 파라미터와 arguments 객체

ES5에서는 함수를 정의할 때 매개변수의 개수를 확정할 수 없는 가변 인자 함수의 경우 매개변수를 통해 인수를 전달받는 것이 불가능하므로 `arguments` 객체를 활용하여 인수를 전달받았습니다. `arguments` 객체는 함수 호출 시 전달된 **인수**(Arguments)들의 정보를 담고 있는 순회 가능한 **유사 배열 객체**(Array-Like Object)이며, 함수 내부에서 지역 변수처럼 사용할 수 있습니다. 하지만 `arguments` 객체는 배열이 아닌 유사 배열 객체이므로 배열 메서드를 사용하려면 `Function.prototype.call`이나 `Function.prototype.apply` 메서드를 사용해 `arguments` 객체를 배열로 변환해야 하는 번거로움이 있습니다.

```javascript
function sum() {
  var array = Array.prototype.slice.call(arguments);

  return array.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

ES6에서는 Rest 파라미터를 사용하여 가변 인자 함수의 인수 목록을 배열로 직접 전달받을 수 있습니다. 이를 통해 유사 배열 객체인 `arguments` 객체를 배열로 변환하는 번거로움을 피할 수 있습니다.

```javascript
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

## 5. 매개변수 기본값

함수를 호출할 때 매개변수의 개수만큼 인수를 전달하는 것이 바람직하지만, 그렇지 않은 경우에도 에러가 발생하지 않습니다. 이는 자바스크립트 엔진이 매개변수의 개수와 인수의 개수를 체크하지 않기 때문입나다. 인수가 전달되지 않은 매개변수의 값은 `undefined`입니다. 이를 방치하면 의도치 않은 결과가 나올 수 있습니다. 따라서 매개변수에 인수가 전달되었는지 확인하여 인수가 전달되지 않은 경우 매개변수에 기본값을 할당할 필요가 있습니다. 즉, 방어 코드가 필요합니다.

```javascript
function sum(x, y) {
  x = x ?? 0;
  y = y ?? 0;

  return x + y;
}
```

ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있습니다. 매개변수 기본값은 매개변수에 인수를 전달하지 않은 경우과 `undefined`를 전달한 경우에만 유효합니다. 앞서 살펴본 Rest 파라미터에는 기본값을 설정할 수 없습니다.

```javascript
function LogName(name = "Lee") {
  console.log(name);
}

logName(); // Lee
logName(undefined); // Lee
logName(null); // null
```

매개변수 기본값은 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 `length` 프로퍼티와 `arguments` 객체에 아무런 영향을 주지 않습니다.

```javascript
function sum(x, y = 0) {
  console.log(arguments);
}

console.log(sum.length); // 1

sum(1); // Arguments { '0': 1 }
sum(1, 2); // Arguments { '0': 1. '1': 2 }
```

## 6. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
