# let, const, var

## 1. var 키워드

### 1.1. 변수 중복 선언 허용

`var` 키워드로 선언한 변수를 중복 선언하면 초기화문(변수 선언과 동시에 초기값을 할당하는 문) 유무에 따라 다르게 동작합니다. 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 `var` 키워드가 없는 것처럼 동작하고, 초기화문이 없는 변수 선언문은 무시됩니다. 이 때 에러는 발생하지 않습니다.

```javascript
var x = 1;
var y = 1;

var x = 100;
var y;

console.log(x); // 100
console.log(y); // 1
```

### 1.2 함수 레벨 스코프

`var` 키워드로 선언한 변수는 오로지 함수의 블록만을 지역 스코프로 인정합니다. 따라서 외부에서 `var` 키워드로 선언한 변수는 코드 블록 내에서 선언해도 모두 전역 변수가 됩니다.

```javascript
var x = 1;
var i = 1;

if (true) {
  var x = 10;
}

for (var i = 0; i < 5; i += 1) {
  console.log(i); // 0 1 2 3 4
}

console.log(x); // 10
console.log(i); // 5
```

### 1.3 변수 호이스팅

`var` 키워드로 변수를 선언하면 변수 호이스팅에 의해 변수 선언문이 스코프의 선두로 끌어 올려진 것처럼 동작합니다. 즉, 변수 호이스팅에 의해 `var` 키워드로 선언한 변수는 변수 선언문 이전에 참조할 수 있습니다. 단, 할당문 이전에 변수를 참조하면 언제나 `undefined`를 반환합니다.

```javascript
console.log(foo); // undefined

foo = 123;

console.log(foo); // 123

var foo;
```

## 2. let 키워드

### 2.1 변수 중복 선언 금지

`let` 키워드로 이름이 같은 변수를 중복 선언하면 **문법 에러**(SyntaxError)가 발생합니다.

```javascript
let bar = 123;
let bar = 456; // SyntaxError
```

### 2.2 함수 레벨 스코프

`let` 키워드로 선언한 변수는 모든 코드 블록(함수, `if` 문, `for` 문, `try/catch` 문 등)을 지역 스코프로 인정하는 **쁠록 레벨 스코프**(Block-Level Scope)를 따릅니다. 함수도 코드 블록이므로 스코프를 만듭니다. 이때 함수 내의 코드 블록은 함수 레벨 스코프에 중첩됩니다.

```javascript
let foo = 1;

{
  let foo = 2;
  let bar = 3;
}

console.log(foo); // 1
console.log(bar); // ReferenceError
```

### 2.3 변수 호이스팅

`let` 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작합니다. `let` 키워드로 선언한 변수를 변수 선언문 이전에 참조하면 **참조 에러**(ReferenceError)가 발생합니다. `let` 키워드로 선언한 변수는 선언 단계와 초기화 단계가 분리되어 진행됩니다. 즉, 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 선언 닥꼐가 먼저 실행되지만, 초기화 단계는 변수 선언문에 도달했을 떄 실행됩니다. 스코프의 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간을 **일시적 사각지대**(Temporal Dead Zone, TDZ)라고 부릅니다.

```javascript
console.log(foo); // ReferenceError

let foo;
console.log(foo); // undefined

foo = 1;
console.log(foo); // 1
```

![var 키워드와 let 키워드로 선언한 변수의 생명 주기](../_images/javascript-variable01.png)

## 2.4 전역 객체와 let

`let` 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아닙니다. 즉 `window.foo`와 같이 접근할 수 없습니다.

```javascript
let x = 1;
console.log(window.x); // undefined
```

## 3. const 키워드

`const` 키워드는 **상수**(Constant)를 선언하기 위해 사용합니다. `const` 키워드의 특징은 `let` 키워드와 대부분 동일하므로 `let` 키워드와 다른 점을 줌심으로 살펴봅니다.

### 3.1 선언과 초기화

`const` 키워드로 선언한 변수는 반드시 선언과 동시에 초기화해야 합니다. 그렇지 않으면 문법적 에러가 발생합니다. `const` 키워드로 선언한 변수는 `let` 키워드로 선언한 변수와 마찬가지로 블록 레벨 스코프를 가지며, 변수 호이스팅이 발생하지 않는 것처럼 동작합니다.

```javascript
const foo = 1;
const bar; // SyntaxError

{
  console.log(foo); // ReferenceError
  const foo = 2;
  console.log(foo); // 2
}

console.log(foo); // 1
```

### 3.2 재할당 금지

`var` 또는 `let` 키워드로 선언한 변수는 재할당이 자유로우나, `const` 키워드로 선언한 변수는 재할당이 금지됩니다.

```javascript
const foo = 1;
foo = 2; // TypeError
```

### 3.3 상수

`const` 키워드로 선언한 변수에 원시 값을 할당한 경우 변수 값을 변경할 수 없습니다. 원시 값은 **변경 불가능한 값**(Immutable Value)이므로 재할당 없이 값을 변경할 수 있는 방법이 없기 떄문입니다. 이러한 특징을 이용해 `const` 키워드를 상수를 표현하는데 사용하기도 합니다.

```javascript
const TAX_RATE = 0.1;

let preTaxPrice = 100;
let afterTaxPrice = preTaxPrice + preTaxPrice * TAX_RATE;

console.log(afterTaxPrice); // 110
```

### 3.4 const 키워드와 객체

`const` 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있습니다. 변경 가능한 값인 객체는 재할당이 없어도 직접 변경 가능하기 때문입니다. `const` 키워드는 재할당을 금지할 분 불변을 의미하지는 않습니다.

```javascript
const person = {
  name: "lee",
};

person.name = "Kim";
console.log(person); // { name: "Kim" }
```

## 4. var vs. let vs. const

- ES6를 사용한다면 `var` 키워드는 사용하지 않습니다.
- 재할당이 필요한 경우에 한정해 `let` 키워드를 사용합니다. 이떄 변수의 스코프는 좁게 만듭니다.
- 변경이 발생하지 않고 일긱 전용으로 사용하는 원시 값과 객체는 `const` 키워드를 사용합니다. `const` 키워드는 재할당을 금지하므로 `var`, `let` 키워드보다 안전합니다.

## 5. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
