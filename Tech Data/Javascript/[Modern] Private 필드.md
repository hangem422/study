# Private 필드

## 1. 클래스 필드 정의 제안

**클래스 필드**(Class Field)는 클래스 기반 객체지향 언어에서 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어입니다. 자바스크립트의 **클래스 몸체**에는 메서드만 선언할 수 있습니다. 따라서 클래스 몸체에 자바와 유사하게 클래스 필드를 선언하면 **문법 에러**(SyntaxError)가 발생합니다. 클래스 몸체에서 클래스 필드를 정의할 수 있는 **클래스 필드 정의**(Class Field Definitions) 제안은 아직 ECMAScript의 정식 표준 사양으로 승급되지 않았습니다. 하지만 최신 브라우저(Chrome 72 이상)와 최신 Node.js(버전 12 이상)는 표준 사양으로 승급이 확실시되는 이 제안을 선제적으로 미리 구현해 놓았습니다.

```javascript
class Person {
  name = "Lee";
}

const me = new Person();
console.log(me); // Person {name: "Lee"}
```

클래스 몸체에서 클래스 필드를 정의하는 경우 `this`에 클래스 필드를 바인딩해서는 안됩니다. `this`는 클래스의 `constructor`와 메서드 내에서만 유효합니다.

```javascript
class Person {
  this.name = ''; // SyntaxError
}
```

클래스 필드를 참조하는 경우 자바와 같은 클래스 기반 객체지향 언어에서는 `this`를 생략할 수 있으나 자바스크립트에서는 `this`를 반드시 사용해야 합니다.

```javascript
class Person {
  name = "Lee";

  constructor() {
    console.log(name); // ReferenceError
  }
}
```

클래스 필드에 초기값을 할당하지 않으면 `undefined`를 갖습니다. 인스턴스를 생성할 때 외부의 초기값으로 클래스 필드를 초기화해야 할 필요가 있다면 `constructor`에서 클래스 필드를 초기화해야 합니다. 따라서 인스턴스를 생성할 때 클래스 필드를 초기화할 필요가 있다면 `constructor` 밖에서 클래스 플드를 정의할 필요가 없습니다.

```javascript
class Person {
  name;

  constructor(name) {
    this.name = name;
  }
}
```

함수는 일급 객체이므로 함수를 클래스 필드에 할당할 수 있습니다. 이 경우, 함수는 프로퍼티 메서드가 아닌 인스턴스 메서드가 됩니다. 따라서 클래스 필드에 함수를 할당하는 것은 권장하지 않습니다.

```javascript
class Person {
  name = "Lee";

  getName = function () {
    return this.name;
  };
}
```

클래스 필드 정의 제안으로 인해 인스턴스 프로퍼티를 정의하는 방식은 두 가지가 되었습니다. 인스턴스를 생성할 때 외부 초기값으로 클래스 필드를 초기화할 필요가 있담녀 `constructor`에서 인스턴스 프로퍼티를 정의하는 기존 방식을 사용하고, 인스턴스를 생성할 때 외부 초기값으로 클래스 필드를 초기화할 필요가 없다면 기존의 `constructor`에서 인스턴스 프로퍼티를 정의하는 방식과 클래스 필드 정의 제안 모두 사용할 수 있습니다.

## 2. private 필드 정의 제안

2020년 7월 현재, TC39 프로세스 stage 3(candidate)에는 private 필드를 정의할 수 있는 새로운 표중 사양이 제안되어 있습니다. 표준 사양으로 승급이 확실시되는 이 제안도 최신 브라우저(Chrome 74 이상)와 최신 Node.js(버전 12 이상)에 이미 구현되어 있습니다.

```javascript
class Person {
  #name = "";

  constructor(name) {
    this.#name = name;
  }
}

const me = new Person("Lee");
console.log(me.#name); // SyntaxError
```

public 필드는 어디서든 참조할 수 있지만 private 필드는 클래스 내부에서만 참조할 수 있습니다. 클래스 외부에서 private 필드에 직접 접근할 수 있는 방법은 없습니다. 다만 접근자 프로퍼티를 통해 간접적으로 접근하는 방법은 유효합니다.

| 접근 가능성                 | public | private |
| --------------------------- | :----: | :-----: |
| 클래스 내부                 |   O    |    O    |
| 자식 클래스 내부            |   O    |    X    |
| 클래스 인스턴스를 통한 접근 |   O    |    X    |

private 필드는 반드시 클래스 몸체에 정의해야 합니다. private 필드를 직접 `constructor`에 정의하면 에러가 발생합니다.

```javascript
class Person {
  constructor(name) {
    this.#name = name; // SyntaxError: Private field '#name' must be declared in an enclosing class
  }

  get naem() {
    return this.#name;
  }
}
```

## 3. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
