# Start

> 타입스크립트를 리액트 프로젝트에서 사용해보기 전에, 알아두면 유용한 타입스크립트의 기초 핵심을 다뤄보게 됩니다.

## 1. 환경 준비

프로젝틀르 생성합니다.

```zsh
$ mkdir ts-practice
$ cd ts-practice
$ yarn init -y
```

#### 타입스크립트 설정 파일 생성하기

먼저 typescript를 글로벌로 설치 해줍니다.

```zsh
$ yarn add typescript
```

그 다음에 프로젝트 디렉터리에서 다음 명령어를 입력하면 tsconfig.json 파일이 자동생성됩니다.

```zsh
tsc --init
```

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true
  }
}
```

- **target:** 컴파일된 코드가 어떤 환경에서 실행될지 정의합니다. 예를들어서 화살표 함수를 사용하고 target을 es5로 한다면 일반 function 키워드를 사용하는 함수로 변환해줍니다.
- **module:** 컴파일된 코드가 어떤 모듈 시스템을 사용할지 정의합니다. 예를들어서 이 값을 `common`으로하면 `export default Sample`을 하게 됐을 때 컴파일된 코드에서 `exports.default = helloWorld`로 변환해주지만 이 값을 es2015로 하면 `export default Sample`을 그대로 유지하게 됩니다.
- **strict:** 모든 타입 체킹 옵션을 활성화한다는 것을 의미합니다.
- **esModuleInterop:** commonjs 모듈 형태로 이루어진 파일을 es2015 모듈 형태로 불러올 수 있게 해줍니다.

컴파일된 파일들이 저장되는 경로를 지정하는 `outDir`을 추가합니다.

```json
{
  "compilerOptions": {
    // ...
    "outDir": "./dist"
  }
}
```

#### 타입스크립트 파일 만들기

package.json을 열어 다음과 같이 `build` 스크립트를 만듭니다.

```json
{
  // ...
  "scripts": {
    "build": "tsc"
  }
}
```

타입스크립트는 `*.ts` 확장자를 사용합니다. `: string`은 해당 상수 값이 문자열이라는 것을 명시합니다.

###### src/practice.ts

```typescript
const message: string = "hellow world";
console.log(message);
```

터미넣에서 `yarn build` 명령어를 입력합니다. 다음과 같은 파일이 생성됩니다.

###### dist/practice.js

```javascript
"use strict";
var message = "hello world";
console.log(message);
```

## 2. 기본 타입

```typescript
let count = 0; // 숫자
count += 1;
count = "문자열"; // 에러 발생

const message: string = "hellow world"; // 문자열

const dome: boolean = true; // 불리언

const numbers: number[] = [1, 2, 3]; // 숫자 배열
const messages: string[] = ["hello", "world"]; // 문자 배열

message.push(1); // 에러 발생

let mightBeUndefined: string | undefined = undefined; // string 혹은 undefined
let nullableNumber: number | null = null; // number 혹은 null

let color: "red" | "orange" | "yellow" = "red"; // red, orange, yellow 중 하나
color = "yellow";
color = "green"; // 에러 발생
```

## 3. 함수에서 타입 정의하기

가장 우측에 `): number`와 같이 결과물의 타입을 명시합니다.

```typescript
function sum(x: number, y: number): number {
  return x + y;
}

sum(1, 2);
```

## 4. interface 사용해보기

`interface`는 클래스 또는 객체를 위한 타입을 지정할 때 사용되는 문법입니다.

#### 클래스에서 interface를 implements 하기

클래스를 만들 때, 특정 조건을 준수해야 함을 명시하고 싶을 때 `interface`를 사용하여 클래스가 가지고 있어야 할 요구사항을 설정합니다. 그리고 클래스를 선언 할 때 `implements` 키워드를 사용하여 해당 클래스가 특정 `interface`의 요구사항을 구현한다는 것을 명시합니다.

```typescript
interface Shace {
  getArea(): number;
}

class Circle implements Shape {
  radius: number;

  constructor(public radius: number) {
    this.radius = radius;
  }

  getArea() {
    return this.radius * this.radius * Math.PI;
  }
}

class Ractangle implements Shape {
  width: number;
  height: number;

  constructor(width: number, height: number) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

const shapes: Shape[] = [new Circle(5), new Rectangle(10, 5)];

shapes.forEach(shape => {
  console.log(shape.getArea());
});
```

타입스크립트에서는 `constuctor`의 파라미터 쪽에 `public` 또는 `private` accessor를 사용하여 직접 하나하나 설정해주는 작업을 생략해줄 수 있습니다.

```typescript
class Circle implements Shape {
  constructor(public radius: number) {
    this.radius = raduis;
  }

  // ...
}

class Rectangle implements Shape {
  constructor(private width: number, private height: number) {
    this.width = width;
    this.height = height;
  }
}
```

## 5. 일반 객체를 interface로 타입 설정하기

```typescript
interface Person {
  name: string;
  age?: number; // 설정을 해도 되고 안해도 되는 값
}

interface Developer {
  name: string;
  age?: number;
  skills: string[];
}

const person: Preson = {
  name: "김사람",
  age: 20
};

const expert: Developer = {
  name: "김개발",
  skills: ["javascript", "react"]
};
```

`interface`를 선언 할 때 다른 `interface`를 `extends` 해서 상속받을 수 있습니다.

```typescript
interface Deceloper extends Person {
  skills: string[];
}
```

## 6.Type Alias 사용하기

`type`은 특정 타입에 별칭을 붙이는 용도로 사용합니다. 이를 사용하여 객체를 위한 타입을 설정할 수도 있고, 배열, 또는 그 어떤 타입이던 별칭을 지어줄 수 있습니다.

```typescript
type Person = {
  name: string;
  age?: number;
};

type Developer = Person & {
  skills: string[];
};

const person: Person = {
  name: "김사람"
};

const expert: Developer = {
  name: "김개발",
  skills: ["javascript", "react"]
};

type People = Person[];
const people: People = [person, export];

type Coloe = 'red' | 'orange' | 'yellow'
const color: Color = red;
const colors: Color[] = ['red', 'orange'];
```

`type`과 `interface`는 어떤 용도로 사용해야 할까요? 무엇이든 써도 상관 없는데 일관성 있게 쓰면 됩니다. 구버전의 타입스크립트에서는 `type`과 `interface`의 차이가 많이 존재했었는데 이제는 큰 차이는 없습니다. 다만 라이브러리를 작성하거나 다른 라이브러리를 위한 타입 지원 파일을 작성하게 될 때는 `interface`를 사용하는 것이 권장 되고 있습니다.

## 6.Generics

제네릭은 타입스크립트에서 함수, 클래스, `interface`, `type`을 사용하게 될 때 여러 종류의 타입에 대하여 호환을 맞춰야 하는 상화엥서 사용하는 문법입니다.

#### 함사에서 Generics 사용하기

제네릭을 사용 할 때는 `<T>`처럼 꺽쇠 안에 타입의 이름을 넣어서 사용하며, 이렇게 설정을 해주면 제네릭에 해당하는 타입에는 뭐든지 들어올 수 있게 되며서도, 사용 할 때 타입이 깨지지 않게 됩니다. 만약 함수에 이렇게 제네릭을 사용하게 된다면 함수의 차라미터로 넣은 실제 값의 타입을 활용하게 됩니다.

```typescript
function merge<A, B>(a: A, b: B): A & B {
  return { ...a, ...b };
}

const merged = merge({ foo: 1 }, { bar: 1 });

function wrap<T>(param: T) {
  return {
    param
  };
}
```

#### Interface에서 Generics 사용하기

```typescript
interface Items<T> {
  list: T[];
}

const items: Items<string> = {
  list: ["a", "b", "c"]
};
```

#### Type alias 에서 Generics 사용하기

`type`에서 제ㅔ릭을 사용하는 방법은 `interface`에서 제네릭을 사용한 것과 매우 유사합니다.

```typescript
type Items<T> = {
  list: T[];
};

const items: Items<string> = {
  list: ["a", "b", "c"]
};
```

#### 클래스에서 Generics 사용하기

```typescript
class Queu<T> {
  list: T[] = [];

  get length() {
    return this.list.length;
  }

  enqueue(item: T) {
    this.list.psuh(item);
  }

  dequeue() {
    return this.liost.shift();
  }
}
```
