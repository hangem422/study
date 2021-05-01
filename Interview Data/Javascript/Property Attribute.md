# Property Attribute

## 1. 내부 슬롯과 내부 메서드

내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 **의사 프로퍼티**(Pseudo Property)와 **의사 메서드**(Pseudo Method)입니다. ECMAScript 사양에 등장하는 이중 대괄호(`[[...]]`)로 감싼 이름들이 내부 슬롯과 내부 메서드입니다. 내부 슬롯과 내부 메서드는 SCMAScript 사양에 정의된 대로 구현되어 자바스클비트 엔진에서 실제로 동작하지만 개발자가 직접 접근할 수 있도록 외부로 공개된 객체의 프로퍼티는 아닙니다. 즉, 내부 슬롯과 내부 메서드는 자바스크립트 내부 로직이므로 원칙적으로 자바스크립트 내부 슬롯과 내부 메서드에 직접적으로 접근하거나 호출할 수 있는 방법을 제공하지 않습니다. 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 합니다.

예를 들어, 모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 갖습니다. 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 원칙적으로 직접 접근할 수 없지만 `[[Prototype]]` 내부 슬롯의 경우, `__proto__`를 통해 간접적으로 접근할 수 있습니다.

## 2. 프로퍼티 어트리뷰트와 프로퍼티 디스트립터 객체

자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의합니다. 프로퍼티의 상태랑 **프로퍼티 값**(Value), **값의 갱신 가능 여부**(Writable), **열거 가능 여부**(Enumerable), **재정의 가능 여부**(Configurable)을 말합니다. 프로퍼티 어트리뷰트는 자바스크립트 엔진이 관리하는 **내부 상태**(Meta-Property)인 내부 슬롯 `[[Value]]`, `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]` 입니다. 따라서 프로퍼티 어트리뷰트에 직접 접근할 수 없지만 `Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 확인할 수는 있습니다.

`Object.getOwnPropertyDescriptor` 메서드를 호출할 때 첫 번째 매개변수에는 객체의 참조를 전달하고, 두 번째 매개변수에는 프로퍼티 키를 문자열로 전달합니다. 이때 `Object.getOwnPropertyDescriptor` 메서드는 프로퍼티 어트립뷰트 정보를 제공하는 **프로퍼티 디스크립터**(Property Descriptor) 객체를 반환합니다. 만약 존재하지 않는 프로퍼티나 상속받은 프로퍼티에 대한 프로퍼티 디스크립터를 요구하면 `undefined`를 반환합니다.

```javascript
const person = {
  name: "Lee",
};

const descriptor = Object.getOwnPropertyDescriptor(person, "name");
console.log(descriptor);
// { vlaue: "Lee", writable: true, enumerable: true, configurable: true }
```

`Object.getOwnPropertyDescriptor` 메서드는 하나의 프로퍼티에 대해 프로퍼티 디스크립터 객체를 반환하지만 ES6에 도입된 `Object.getOwnPropertyDescriptors` 메서드는 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환합니다.

```javascript
const person = {
  name: "Lee",
  age: 20,
};

const descriptors = Object.getOwnPropertyDescriptors(preson);
console.log(descriptors);
/*
{
  name: { vlaue: "Lee", writable: true, enumerable: true, configurable: true },
  age: { vlaue: 20", writable: true, enumerable: true, configurable: true }
}
*/
```

## 3. 데이터 프로퍼티와 접근자 프로퍼티

- **데이터 프로퍼티**(Data Property): 키와 값으로 구성된 일반적인 프로퍼티입니다.
- **접근자 프로퍼티**(Accessor Property): 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 함수로 구성된 프로퍼티입니다.

### 3.1 데이터 프로퍼티

**데이터 프로퍼티**(Data Property)는 다음과 같은 프로퍼티 어트리뷰트를 갖습니다. 이 어트리뷰트는 자바스크립트 엔젠이 프로퍼티를 생성할 떄 기본값으로 자동 정의합니다.

| 프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 | 설명                                                                                                                                                                                                                                                                                 |
| ------------------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `[[Value]]`         | value                               | 프로퍼티 키를 통해 프러퍼티 값에 접근하면 반환되는 값입니다. 프로퍼티 키를 통해 프로퍼티 값을 변경하면 `[[Value]]`에 값을 재할당합니다. 이떄 프로퍼티가 없으면 프로퍼티를 동적 생성하고 생상된 프로핕의 `[[Value]]`에 값을 지정합니다.                                               |
| `[[Writable]]`      | writable                            | 프로퍼티 값의 변경 가능 여부를 나타내며 불리언 값을 갖습니다. `[[Writable]]`의 값이 `false`인 경우 해당 프로퍼티의 `[[Value]]`의 값을 변경할 수 없는 읽기 전용 프로퍼티가 됩니다.                                                                                                    |
| `[[Enumerable]]`    | enumerable                          | 프로퍼티 열거 가능 여부를 나타내며 불리언 값을 갖습니다. `[[Enumerable]]`의 값이 `false`인 경우 해당 프로퍼티는 `for...in` 문이나 `Object.keys` 메서드 등으로 열거할 수 없습니다.                                                                                                    |
| `[[Configurable]]`  | configurable                        | 프로퍼티의 재정의 기능 여부를 나타내며 불리언 값을 갖습니다. `[[Configurable]]`의 값이 `false`인 경우 해당 프로퍼티의 삭제, 프로퍼티 어트리뷰트 값의 변경이 금지됩니다. 단, `[[Writable]]`이 `true`인 경우 `[[Value]]`의 변경과 `[[Writable]]`을 `false`로 변경하는 것은 허용됩니다. |

### 3.2 접근자 프로퍼티

접근자 프로퍼티는 다음과 같은 프로퍼티 어트리뷰트들 갖습니다.

| 프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 | 설명                                                                                                                                                                                                                                      |
| ------------------- | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[[Get]]`           | get                                 | 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 읽을 때 호출되는 접근자 함수입니다. 즉, 접근자 프로퍼티 키로 프로퍼티 값에 접근하면 프로퍼티 어트리뷰트 `[[Get]]`의 값, 즉 getter 함수가 호출되고 그 결과가 프로퍼티 값으로 반환됩니다.     |
| `[[Set]]`           | set                                 | 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 저장할 떄 호출되는 접근자 함수입니다. 즉, 접근자 프로퍼티가 키로 프로퍼티 값을 저장하면 프로퍼티 어트리뷰트 `[[Set]]`의 값, 즉 setter 함수가 호출되고 그 결과가 프로퍼티 값으로 저장됩니다. |
| `[[Enumerable]]`    | enumerable                          | 데이터 프로퍼티의 `[[Enumerable]]`과 같습니다.                                                                                                                                                                                            |
| `[[Confurable]]`    | configurable                        | 데이터 프로퍼티의 `[[Configurable]]`과 같습니다.                                                                                                                                                                                          |

## 4. 프로퍼티 정의

프로퍼티 정의랑 새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의할는 것을 말합니다. `Object.defineProperty` 메서드를 사용하면 프로퍼티의 어트리뷰트를 재정의 할 수 있습니다. 인수로는 객체의 참조와 프로퍼티의 키인 문자열, 프로퍼티 디스크립터 객체를 전달해야 합니다. `Object.defineProperty` 메서드로 프로퍼티를 정의할 때 프로퍼티 디스크립터 객체의 프로퍼티를 일부 생략할 수 있습니다.

| 프로퍼티 디스크립터 객체의 프로퍼티 | 대응하는 프로퍼티 어트리뷰트 | 생략했을 때의 기본 값 |
| ----------------------------------- | ---------------------------- | --------------------- |
| value                               | `[[Value]]`                  | `undefined`           |
| get                                 | `[[Get]]`                    | `undefined`           |
| set                                 | `[[Set]]`                    | `undefined`           |
| writable                            | `[[Writable]]`               | `false`               |
| enumerable                          | `[[Enumerable]]`             | `false`               |
| configurable                        | `[[Configurable]]`           | `false`               |

```javascript
const person = {};

Object.defineProperty(person, "firstName", {
  value: "Ungmo",
  writable: true,
  enumerable: true,
  configurable: true,
});

Object.defineProperty(person, "lastName", {
  value: "Lee",
});

Object.defineProperty(person, "fullName", {
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  set(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
  enumerable: true,
  configurable: true,
});

let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log(descriptor); // { value: "Ungmo", writable: true, enumerable: true, configurable: true }

descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log(descriptor); // { value: "Lee", writable: false, enumerable: false, configurable: false }

descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log(descriptor); // { get: f, set: f, enumerable: true, configurable: true }

console.log(Object.keys(person)); // ["firstName"]
person.lastName = "Kim"; // 에러는 발생하지 않고 무시됩니다.
delete person.lastName; // 에러는 발생하지 않고 무시됩니다.
```

`Object.defineProperty` 메서드는 한번에 하나의 프로퍼티만 정의할 수 있습니다. `Object.defineProperties` 메서드를 사용하면 여러 개의 프로퍼티를 한 번에 정의할 수 있습니다.

```javascript
const person = {};

Object.defineProperties(person, {
  firstName: {
    value: "Ungmo",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  lastName: {
    value: "Lee",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  fullName: {
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    set(name) {
      [this.firstName, this.lastName] = name.split(" ");
    },
    enumerable: true,
    configurable: true,
  },
});
```

## 5. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
