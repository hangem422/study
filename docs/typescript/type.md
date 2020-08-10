# Type

## Basic

#### 1. Boolean

```typescript
let isDone: boolean = false;
```

#### 2. Number

```typescript
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

#### 3. String

```typescript
let color: string = "blud";
```

#### 4. Array

```typescript
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

#### 5. Tuple

```typescript
let x: [string, number];
```

#### 6. Enum

Javascript의 표준 자료형 집합과 사용하면 도움이 될만한 데이터 형은 `enum`입니다. C# 같은 언어처럼, `enum`은 값의 집합에 더 나은 이름을 붙여줄 수 있습니다. 기본적으로, `enum`은 `0`부터 시작하여 맴버들의 번호를 매깁니다. 맴버 중 하나 혹은 모든 값을 수동으로 설정하여 번호를 바꿀 수 있습니다.

```typescript
enum Color {
  Red = 1,
  Green,
  Blue,
}

let c: Color = Color.Green;
let colorName: string = Color[2]; // Green
```

#### 7. Any

애플리케이션을 만들 때, 알지 못하는 타입을 ㅛ표현해야 할 수도 있습니다.

```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false;
```

#### 8. Void

`void`는 어떤 타입도 존재할 수 없음을 나타내기 때문에, `any`의 반대 타입과 같습니다.

```typescript
function warnUser(): void {
  console.log("This is my warning message");
}
let unusable: void = undefined;
unusable = null;
```

#### Null and Undefined

기본적으로 `null`과 `undefined`는 다른 모든 타입의 하위 타입입니다. 이건, `null`과 `undefined`를 `number` 같은 타입에 할당할 수 있다는 것을 의미합니다. 하지만, `--strictNullChecks`를 사용하면, `null`과 `undefined`는 오직 `any`와 각자 자신들 타입에만 할당 가능합니다. (예외적으로 `undefined`는 `void`에 할당 가능합니다.)

```typescript
let u: undefined = undefined;
let n: null = null;
```

#### 9. Never

`never`는 절대 발생할 수 없는 타입을 나타냅니다.

```typescript
function error(message: string): never {
  throw new Error(message);
}
```

#### 10. Object

`object`는 원시 타입이 아닌 타입을 나타냅니다. 예를 들어, `number`, `string`, `boolean`, `bigint`, `symbol`, `null`, 또는 `undefined`가 아닌 나머지를 의미합니다

```typescript
declare function create(o: object | null): void;
create({ prop: 0 });
```

#### 11. Type Assertions

TypeScript보다 개발자가 값에 대해 더 잘 알고 잇을 때가 있습니다. 대개, 이런 경우는 어떤 엔티티의 실제 타입이 현재 타입보다 더 구체적일 때 발생합니다.

###### angle-bracket

```typescript
let somValue: any = "this is a string";
let stringLength: number = (<string>someValue).length;
```

###### as

```typescript
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```
