## 1. 프로퍼티 축약 표현

ES6에서는 프로퍼티 값으로 변수를 사용하는 경우 변수 이름과 프러퍼티 키가 동일한 이름일 때 프로퍼티 키를 **생략**할 수 있습니다.

```javascript
var x = 1;
var y = 1;

const obj = { x, y };

console.log(obj); // { x: 1, y: 2 }
```

## 2. 계산된 프로퍼티 이름

문자열 또는 문자열로 타입 변환할 수 있는 값으로 평가되는 표현식을 사용해 프로퍼티 키를 동적으로 생성할 수도 있습니다. 단, 프로퍼티 키로 사용할 표현식을 대괄호(`[ .. ]`)로 묶어야 합니다. 이를 **계산된 프로퍼티 이름**(Computed Property Name)이라 합니다.

```javascript
var prefix = "prop";

// ES5
var i = 0;
var obj_es5 = {};

obj[prefix + "-" + ++i] = i;
obj[prefix + "-" + ++i] = i;
obj[prefix + "-" + ++i] = i;

// ES6
i = 0;

var obj_es6 = {
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i,
};

console.log(obj_es5); // { prop-1: 1, prop-2: 2, prop-3: 3 }
console.log(obj_es5); // { prop-1: 1, prop-2: 2, prop-3: 3 }
```

## 3. 메서드 단축 표현

ES6에서는 메서드를 정의할 때 function 키워드를 생략한 **축약 표현**을 사용할 수 있습니다. 축약 표현으로 정의한 메서드는 프로퍼티에 할당한 함수와 다르게 동작합니다.

```javascript
// ES5
var obj_es5 = {
  name: "Lee",
  sayHi: function () {
    console.log("Hi! " + this.name);
  },
};

// ES6
var obj = {
  name: "Lee",
  sayHi() {
    console.log("Hi! " + this.name);
  },
};
```

## 4. 객체 리터럴 내부에서 \_\_proto\_\_에 의한 직접 상속

ES6에서는 객체 리터럴 내부에서 `__proto__` 접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있습니다.

```javascript
const myProto = { x: 10 };

const obj = {
  y: 20,
  __proto__: myProto,
};

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 5. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
