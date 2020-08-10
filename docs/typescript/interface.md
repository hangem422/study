# Interface

```typescript
function printLabel(labeledObj: { label: string }) {
  console.log(labelObj.label);
}
let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

타입 검사는 `printLabel` 호출을 확인합니다. `printLabel` 함수는 `string` 타입 `label`을 갖는 객체를 하나의 매개변수로 가집니다. 이 객체가 실제로는 더 많은 프로퍼티를 갖고 있지만, 컴파일러는 최소한의 필요한 프로퍼티가 있는지와 타입이 잘 맞는지만 검사합니다. 이것을 인터페이스로 다시 작성하면 다음과 같습니다.

```typescript
interface LabeledValue {
  label: string;
}

function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

## 선택적 프로퍼티

인터페이스의 모든 포러파티가 필요한 것은 아닙니다. 어떤 조건에서만 존재하거나 아예 없을 수도 있습니다.

```typescript
interface SquareConfig {
  color?: string;
}
```

## 읽기 전용 프로퍼티

일부 프로퍼티들은 객체가 처음 생성될 때만 수정 가능해야합니다.

```typescript
interface Pont {
  readonly x: number;
  readonly y: number;
}
```

## 초과 프로퍼티 검사

객체 리터럴은 다른 변수에 할당할 때나 인수로 전닳할 때, 특별한 처리를 받고, **초과 프로퍼티 검사**를 받습니다. 만약 객체 리터럴이 **때상 타입**이 갖고 있지 않은 프로퍼티를 갖고 있으면, 에러가 발생합니다.

```typescript
interface SquareConfig {
  color?: string;
}

function createSquare(config: SquareConfig): { color: string } {}

let mySquare = createSquare({ color: "red", width: 100 }); // Error
```

이 검사를 받지 않는 방법은 다음와 같습니다.

#### 타입 단언

```typescript
let mySquare = create({ color: "red", width: 100 } as SquareConfig);
```

#### 문자열 인덱스 서명 추가

```typescript
interface {
  color?: string;
  [propName: string]: any;
}
```

#### 변수 할당

공통 프로퍼티가 있는 경위에만 사용할 수 있습니다.

```typescript
let squareOptions = { color: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

## 함수 타입

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function (src, sub) {
  let result = src.search(sub);
  return result > -1;
};
```

## 인덱서블 타입

인터페이스로 함수 타입을 설명하는 방법과 유사하게, `a[10]`이나 `ageMap['daniel']` 처럼 타입을 **인덱스로** 기술할 수 잇습니다. 인덱서블 타입은 언덱싱 할때 해당 반환 유형과 함께 객체를 인덱싱하는데 사용할 수 있는 타입을 기술하는 **인덱스 시그니처**를 가지고 있습니다.

```typescript
interface StringArray {
  [index: number]: string;
}
let myArray: StringArray;
maArray = ["Bob", "Fred"];
```

문자열 인덱스 시그니처는 **사전** 패턴을 기술하는데 강력한 방법이지만, 모든 프로퍼티들이 반환 타입과 일치하도록 강제합니다.

```typescript
interface NumberDicionary {
  [index: string]: number;
  length: number;
  name: string;
}
```

## 클래스 타입

#### 인터페이스 구현하기

```typescript
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Datae();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(j: number, m: number) {}
}
```

#### 클래스의 스태틱과 인스턴스의 차이점

클래스는 스태틱 타입과 인스턴스 타입 두 가지를 가집니다. 생성 시그니처로 인터페이스를 생성하고, 클래스를 생성하려고 하면, 인터페이스를 implements 할 때, 에러가 발생합니다. 클래스가 인터페이스를 implements 할 때, 클래스의 인스턴스만 검사하기 때문입니다. 생성자가 스태틱이기 때문에, 이 검사에 포함되지 않습니다.

```typescript
interface ClockConstructor {
  new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
  currentTime: Date;
  constructor(h: number, m: number) {}
} // Error
```

대신 클래스의 스태틱 부분을 직접적으로 다룰 필요가 있습니다.

```typescript
interface ClockConstructor {
  new (hour: number, minute: nmber): ClockInterface;
}

interface ClockInterface {
  tick(): void;
}

function createClock(
  ctor: ClockConstructor,
  hour: number,
  minute: number
): ClockInterface {
  return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {}
}

class AnalogClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {}
}
```

다른 쉬운 방법은 클래스 표현을 사용하는 것입니다.

```typescript
interface ClockConstructor {
  new (hour: number, minute: number);
}

interface ClockInterface {
  tick();
}

const Clock: ClockContructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {}
};
```

## 인터페이스 확장하기

```typescript
interface Shap {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}
```

## 하이브리드 타입

```typescript
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

function getCounter(): Counter {
  let counter = function (start: number) {} as Counter;
  counter.interval = 123;
  counter.reset = function () {};
  return counter;
}
```

## 클래스를 확장한 인터페익스

인터페이스 타입이 클래스 타입을 확장하면, 클래스의 맴버는 상속받지만 구현은 상속받지 않습니다. `private`와 `protected` 맴버도 상속받습니다.

```typescript
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

class Image implements SelectableControl {
  private state: ant; // Error: Property 'state' is missing in type 'Image'. 퍼블릭 구현이 안돼있음
  select() {}
}
```
