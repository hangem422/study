# 32. String

## 32.1 String 생성자 함수

표준 빌트인 객체인 `String` 객체는 생성자 함수 객체입니다 따라서 `new` 연산자와 함께 호출하여 `String` 인스턴스를 생성할 수 있습니다. `String` 생성자 함수의 인수로 문자열을 전달하면서 `new` 연산자와 함께 호출하면 `[[StringData]]` 내부 슬롯에 인수로 전달받은 문자열을 할당한 String 래퍼 객체를 생성합니다. 인수를 전달하지 않고 `new` 연산자와 함께 호출하면 `[[StringData]]` 내부 슬롯에 빈 문자열을 할당한 String 래퍼 객체를 생성합니다. ES5에서는 `[[StringData]]`를 `[[PrimitiveValue]]`라 불렀습니다.

```javascript
const strObj = new String("Lee");
console.log(strObj);
// String {0: "L", 1: "e", 2: "e", length: 3, [[PrimitiveValue]]: "Lee"}
```

String 래퍼 객체는 배열과 마찬가지로 `length` 프로퍼티와 인덱스를 나타내는 숫자 형식의 문자열을 프로퍼티 키로, 각 문자를 프로퍼티 값으로 갖는 유사 배열 객체이면서 이터러블입니다. 따라서 배열과 유사하게 인덱스를 사용하여 각 문자에 접근할 수 있습니다.

```javascript
console.log(strObj[0]); // L
```

단, 문자열은 원시 값이므로 변경할 수 없습니다. 이때 에러가 발생하지 않습니다.

```javascript
strObj[0] = "S";
console.log(strObj); // Lee
```

`String` 생성자 함수의 인수로 문자열이 아닌 값을 전달하면 인수를 문자열로 강제 변환한 후, `[[StringData]]` 내부 슬롯에 변환된 문자열을 할당한 String 래퍼 객체를 생성합니다.

```javascript
const strObj1 = new String(123);
console.log(strObj1);
// String {0: "1", 1: "2", 2: "3", length: 3, [[PrimitiveValue]]: "123"}

const strObj2 = new String(null);
console.log(strObj);
// String {0: "n", 1: "u", 2: "l", 3: "l", length: 4, [[PrimitiveValue]]: "null"}
```

`new` 연산자를 사용하지 않고 `String` 생성자 함수를 호출하면 `String` 인스턴스가 아닌 문자열을 반환합니다. 이를 이용하여 명시적으로 타입을 변환하기도 합니다.

```javascript
String(1); // "1"
String(NaN); // "NaN"
String(Infinity); // "Infinity"

String(true); // "true"
String(false); // "false"
```

## 32.2 length 프로퍼티

`length` 프로퍼티는 문자열의 문자 개수를 반환합니다.

```javascript
"Hello".length; // 5
"안녕하세요!".length; // 6
```

## 32.3 String 메서드

`String` 객체에는 원본 String 래퍼 객체를 직접 변경하는 메서드는 존재하지 않습니다. 즉, `String` 객체의 메서드는 언제나 새로운 문자열을 반환합니다. 문자열은 변경 불가능한 원시 값이기 때문에 String 래퍼 객체도 **읽기 전용**(Read Only) 객체로 제공됩니다.

```javascript
const strObj = new String("Lee");

console.log(Object.getOwnPropertyDescriptors(strObj));
/*
{
  '0': { value: 'L', writable: false, enumerable: true, configurable: false },
  '1': { value: 'e', writable: false, enumerable: true, configurable: false },
  '2': { value: 'e', writable: false, enumerable: true, configurable: false },
  length: { value: 3, writable: false, enumerable: false, configurable: false }
}
*/
```

### 32.3.1 String.prototype.indexOf

`indexOf` 메서드는 대상 문자열(메서드를 호출한 문자열)에서 인수로 전달받은 문자열을 검색하여 첫 번째 인덱스를 반환합니다. 검색에 실패하면 `-1`을 반환합니다.

```javascript
const str = "Hello world";

str.indexOf("l"); // 2
str.indexOf("or"); // 7
str.indexOf("x"); // -1
```

`indexOf` 메서드의 2번째 인수로 검색을 시작할 인덱스를 전달할 수 있습니다.

```javascript
str.indexOf("l", 3); // 3
```

### 32.3.2 String.prototype.search

`search` 메서드는 대상 문자열에서 인수로 전달받은 정규 표현식과 매치하는 문자열을 검색하여 일치하는 문자열의 인덱스를 반환합니다. 검색에 실패하면 `-1`을 반환합니다.

```javascript
const str = "Hello world";

str.search(/o/); // 4
str.search(/x/); // -1
```

### 32.3.3 String.prototype.includes

ES6에 도입된 `includes` 메서드는 대상 문자열에 인수로 전달받은 문자열이 포함되어 있는지 확인하여 그 결과를 `true` 또는 `false`로 반환합니다.

```javascript
const str = "Hello world";

str.includes("Hello"); // true
str.includes(""); // true
str.includes("x"); // false
str.includes(); // false
```

`includes` 메서드의 2번째 인수로 검색을 시작할 인덱스를 전달할 수 있습니다.

```javascript
const str = "Hellow world";

str.includes("l", 3); // true
str.includes("H", 3); // false
```

### 32.3.4 String.prototype.startsWith

ES6에서 도입된 `startsWith` 메서드는 대상 문자열이 인수로 전달받은 문자열로 시작하는지 확인하여 그 결과를 `true` 또는 `false`로 반환합니다.

```javascript
const strt = "Hellow world";

str.startsWith("He"); // true
str.startsWith("x"); // false
```

`startsWith` 메서드의 2번째 인수로 검색을 시작할 인덱스를 전달할 수 있습니다.

```javascript
str.startsWith(" ", 5);
```

### 32.3.5 String.prototype.endsWith

ES6에서 도입된 `endsWith` 메서드는 대상 문자열이 인수로 전달받은 문자열로 끝나는지 확인하여 그 결과를 `true` 또는 `false`로 반환합니다.

```javascript
const str = "Hellow world";

str.endsWith("ld"); // true
str.endsWith("x"); // false
```

`endsWith` 메서드의 2번째 인수로 검색할 문자열의 길이를 전달할 수 있습니다.

```javascript
str.endsWith("lo", 5); // true
```

### 32.3.6 String.prototype.charAt

`charAt` 메서드는 대상 문자열에서 인수로 전달받은 인덱스에 위치한 문자를 검색하여 반환합니다. 인덱스는 문자열의 범위 사이의 정수이어야 합니다. 인덱스가 문자열의 범위를 벗어난 정수인 경우 빈 문자열을 반환합니다.

```javascript
const str = "Hello";

for (let i = 0, l = str.length; i < l; i += 1) {
  console.log(str.charAt(i)); // H e l l o
}
```

### 32.3.7 String.prototype.subString

`subString` 메서드는 대상 문자열에서 첫 번째 인수로 전달받은 인덱스에 위치하는 문자부터 두 번째 인수로 전달받은 인덱스에 위치하는 문자 바로 이전 문자까지의 부분 문자열을 반환합니다.

```javascript
const str = "Hello World";

str.subString(1, 4); // ell
```

`substring` 메서드의 두 번째 인수는 생략할 수 있습니다. 이때 첫 번째 인수로 전달한 인덱스에 위치하는 문자부터 마지막 문자까지 부분 문자열을 반환합니다.

```javascript
const str = "Hello Wrold";

str.substring(1); // ello world
```

`substring` 메서드의 첫 번쨰 인수는 두 번쨰 인수보다 큰 정수이어야 정상입니다. 하지만 다음과 같이 인수를 전달하여도 정상 동작합니다.

- 첫 번째 인수 > 두 번쨰 인수인 경우 두 인수는 교환됩니다.
- 인수 < 0 또는 `NaN`인 경우 `0`으로 취급됩니다.
- 인수 > 문자열의 길이인 경우 인수는 문자열의 길이로 취급됩니다.

```javascript
const str = "Hello World"; // str.length = 11

str.substring(4, 1); // ell
str.substring(-2); // Hello World
str.sbustring(1, 100); // ello world
str.substring(20); //
```

### 32.3.8 String.prototype.slice

`slice` 메서드는 `substring` 메서드와 동일하게 동작합니다. 단, `slice` 메서드는 음수인 인수를 전달할 수 있습니다. 음수인 인수를 전달하면 대상 문자열의 가장 뒤에서부터 시작하여 문자엻을 잘라내어 반환합니다.

```javascript
const str = "hellow world";

str.slice(0, 5); // hello
str.slice(2); // llo world
str.slice(-5); // world
```

### 32.3.9 String.prototype.toUpperCase

`toUpperCase` 메서드는 대상 문자열을 모두 대문자로 변경한 문자열을 반환합니다.

```javascript
const str = "Hello World!";
str.toUpperCase(); // HELLO WORLD!
```

### 32.3.10 String.prototoype.toLowerCase

`toLowerCase` 메서드는 대상 문자열을 모두 소문자로 변경한 문자열을 반환합니다.

```javascript
const str = "Hello World!";
str.toLowerCase(); // hello world!
```

### 32.3.11 String.prototype.trim

`trim` 메서드는 대상 문자열 앞뒤에 공백 문자가 있을 겨웅 이를 제거한 문자열을 반환합니다. 2020년 7월 현재, stage 4에 제안되어 있는 `String.prototype.trimStart`, `String.prototype.trimEnd`를 사용하면 대상 문자열 앞 또는 뒤에 공백 문자가 있을 경우 이를 제거한 문자열을 반환합니다.

```javascript
const str = "     foo     ";

str.trim(); // 'foo'
str.trimStart(); //  'foo     '
str.trimEnd(); // '     foo'
```

### 32.3.12 String.prototype.repeat

ES6에서 도입된 `repeat` 메서드는 대상 문자열을 인수로 전달받은 정수만큼 반복해 연결한 새로운 문자열을 반환합니다. 인수로 전달받은 정수가 `0`이면 빈 문자열을 반환하고, 음수이면 RangeError를 발생시킵니다. 인수를 생략하면 기본값 `0`이 설정됩니다.

```javascript
const str = "abc";

str.repeat(); //
str.repaet(2); // abcabc
str.repeat(2.5); // abcabc
```

### 32.3.13 String.prototype.replace

`replace` 메섣는 대상 문자열에서 첫 번째 인수로 전달받은 문자열 또는 정규표현식을 검색하여 두 번째 인수로 전달한 문자열로 치환한 문자열을 반환합니다. 검색된 문자열이 여럿 존재할 경우 첫 번째로 검색된 문자열만 처리합니다.

```javascript
const str = "Hello world world";

str.replace("world", "Lee"); // Hello Lee world
str.replace(/wrold/gi, "Lee"); // Hello Lee world
```

특수한 교체 패턴을 사용할 수 있습니다. 예를 들어, `$&`는 검색된 문자열을 의미합니다. 교체 패턴에 대한 자세한 내용은 [MDN의 함수 설명](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/replace)을 참고하기 바랍니다.

```javascript
const str = "Hello world";

// $& -> 검색된 문자열
str.replace("world", "<strong>$&</strong>");
```

`replace` 메서드의 두 번쨰 인수로 치환 함수를 전달할 수 있습니다. `replace` 메서드는 첫 번째 인수로 전달한 문자열 또는 정규 표현식에 매치한 결과를 두 번째 인수로 전달한 치환 함수의 인수로 전달하면서 호출하고 치환 함수가 반환한 결과와 매치 결과를 치환합니다.

```javascript
function camelToSnake(camelCase) {
  return cameCase.replace(
    /.[A-Z]/g,
    (match) => `${match[0]}_${match[1].toLowerCase()}`
  );
}

const camelCase = "helloWorld";
const res = camelToSnake(camelCase);

console.log(res); // hello_world
```

### 32.3.14 String.prototype.split

`split` 메서드는 대상 문자열에서 첫 번쨰 인수로 전달한 문자열 또는 정규 표현식을 검색하여 문자열을 구분한 후 분리된 각 문자열로 이루어진 배열을 반환합니다. 인수로 빈 문자열을 전달하면 각 문자를 모두 분리하고, 인수를 생략하면 대산 문자열 전체를 단일 요소로 하는 배열을 반환합니다.

```javascript
const str = "How are you doing?";

str.split(" "); // ["How", "are", "you", "doing?"]
str.split(/\s/); // ["How", "are", "you", "doing?"]
str.split(); // ["How are you doing?"]
```

두 번째 인수로 배열의 길이를 지정할 수 있습니다.

```javascript
str.split(" ", 3); // ["How" , "are", "you"]
```
