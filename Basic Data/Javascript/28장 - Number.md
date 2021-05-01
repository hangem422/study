# 28. Number

**표준 빌트인 객체**(Standard Build-In Object)인 `Number`는 원시 타입인 숫자를 다룰 때 유용한 프로퍼티와 메서드를 제공합니다.

## 28.1 Number 생성자 함수

`Number` 객체는 생성자 함수 객체입니다. 따라서 `new` 연산자와 함께 호출하여 `Number` 인스턴스를 생성할 수 있습니다. `Number` 생성자 함수에 인수를 전달하지 않고 `new` 연산자와 함께 호출하면 `[[NumberData]]` 내부 슬롯에 `0`을 할당한 `Number` 래퍼 객체를 생성합니다. ES5에서는 `[[NumberData]]`를 `[[PrimitiveValue]]`라 불렀습니다.

```javascript
const numObj = new Number();
console.log(numObj); // Number {[[PrimitiveValue]]: 0}
```

`Number` 생성자 함수의 인수로 숫자를 전달하면서 `new` 연산자와 함께 호출하면 `[[NumberData]]` 내부 슬롯에 인수로 전달받은 숫자를 할당한 `Number` 래퍼 객체를 생성합니다.

```javascript
const numObj = new Number(10);
console.log(numObj); // Number {[[PrimitiveValue]]: 10}
```

`Number` 생성자 함수의 인수로 숫자가 아닌 값을 전달하면 인수를 숫자로 강제 변환한 후, `[[NumberData]]` 내부 슬롯에 변환된 숫자를 할당한 `Number` 래퍼 객체를 생성합니다. 인수를 숫자로 변환할 수 없다면 `NaN`을 `[[NumberData]]` 내부 슬롯에 할당한 `Number` 래퍼 객체를 생성합니다.

```javascript
const numObj = new Number("10");
console.log(numObj); // Number {[[PrimitiveValue]]: 10}
```

`new` 연산자를 사용하지 않고 `Number` 생성자 함수를 호춣하면 `Number` 인스턴스가 아닌 숫자를 반환합니다. 이를 이용하여 명시적으로 타입을 변환하기도 합니다.

```javascript
Number("0"); // 0
Number("-1"); // -1
Number("10,53"); // 10.53

Number(true); // 1
Number(false); // 0
```

## 28.2 Number 프로퍼티

### 28.2.1 Number.EPSILON

ES6에서 도입된 `Number.EPSILON`은 1과 1보다 큰 숫자 중에서 가장 작은 숫자와의 차이와 같습니다. `Number.EPSILON`은 약 2.2204460492503130808472633367816 \* 10^-16^입니다.

부동소수점 산술 연산은 정확한 결과를 기대하기 어렵습니다. 정수는 2진법으로 오차 없이 저장 가능하지만 부동소수점을 표현하기 위해 가장 널리 쓰이는 표준인 IEEE 754는 2진법으로 변환했을 때 무한소수가 되어 미세한 오차가 발생할 수밖에 없는 구조적 한계가 있습니다. `Number.EPSILON`은 부동소수점으로 인해 발생하는 오차를 해결하기 위해 사용합니다.

```javascript
function isEqual(a, b) {
  return Math.abs(a - b) < Number.EPSILON;
}

0.1 + 0.2; // 0.30000000000000004
0.1 + 0.2 === 0.3; // false
isEqual(0.1, +0.2, 0.3); // true
```

#### 추가 자료: 자바스크립트 부동 소수점 숫자 이해하기

> 추가 자료 출처: Effactive JavaScript - 데이비드 허먼

자바스크립트 내의 모든 숫자는 IEEE 754 표준에서 정의한 64비트로 인코딩된 배정밀도의 부동 소수점, 즉 흔히 **double**로 알려진 숫자입니다. double는 53비트까지의 정확도로 완벽하게 integer로 표현할 수 있습니다. -9,007,199,254,740,992(-2^53^)부터 9,007,199,254,740,992(2^53^)까지의 모든 integer는 유효한 double 값들입니다. 따라서 자바스크립트에서 integer 연산은 별도의 integer형 없이도 완벽하게 가능합니다. 대부분의 산술 연산자는 정수형이나 실수 또는 이 둘의 조합으로 동작합니다.

```javascript
0.1 * 1.9; // 0.19
-99 + 100; // 1
```

비트단위 연산자는 인자들을 직접 부동 소수점 숫자처럼 처리하지 않고, 암묵적으로 32비트 정수로 변환합니다. 연산을 수행하고 나서, 표준 자바스크립트 부동 소수점 숫자 값으로 변환해 결과를 돌려줍니다. 자바스크립트의 숫자 `8`과 `1`은 항상 double형입니다. 하지만 32비트 정수형, 즉 32개의 0과 1로도 표현될 수 있습니다. 숫자 8과 1은 다음과 같은 형태가 됩니다.

```javascript
8 | 1; // 9

(8).toString(2); // "1000"
(1).toString(2); // "1"
parseInt("1001", 2); // 9
```

64비트의 정확도는 충분히 넓지만, double은 실수에 비해 여전히 유현한 숫자 범위만 표현할 수 있습니다. 부동 소수점 산술 연산은 근사 값만을 만들어 낼 수 있고 가장 가까운 표현가능한 실수로 반올림합니다. 계산을 계속 수행하다 보면 이런 반올림 오류가 누적되어 더욱 더 부정확한 결과를 낳게 됩니다. 반올림은 간혹 일반적인 산술 결과에서 기대하기 어려운 어이없는 편차를 보이기도 합니다.

```
(0.1 + 0.2) + 0.3; // 0.6000000000000001
0.1 + (0.2 + 0.3); // 0.6
```

정확도가 관건이라면, 이런 한계에 대해 반드시 알아두어야 합니다. 한가지 유용한 대안은 가능한 정수 값을 사용하는 것입니다. 정수 값은 반올림 없이 표현이 가능하기 때문입니다. 정수를 처리할 때 모든 연산이 -2^53^과 2^53^ 범위 내에 맞춰진다는 점도 염두에 두어야 합니다.

### 28.2.2 Number.MAX_VALUE

`Number.MAX_VALUE`는 자바스크립트에서 표현할 수 있는 가장 큰 양의 값(1.7976931348623157 \* 10^308^)입니다. `Number.MAX_VALUE`보다 큰 숫자는 `Infinity`입니다.

```javascript
Number.MAX_VALUE; // 1.7976931348623157e+308
Infinity > Number.MAX_VALUE; // true
```

### 28.2.3 Number.MIN_VALUE

`Number.MIN_VALUE`는 자바스크립트에서 표현할 수 있는 가장 작은 양수 값(5 \* 10^-324^)입니다. `Number.MIN_VALUE`보다 작은 숫자는 `0`입니다.

```javascript
Number.MIN_VALUE; // 5e-324
Number.MIN_VALUE > 0;
```

### 28.2.4 Number.MAX_SAFE_INTEGER

`Number.MAX_SAFE_INTEGER`는 자바스크립트에서 안전하게 표현할 수 있는 가장 큰 정수값(9,007,199,254,740,991)입니다.

### 28.2.5 Number.MIN_SAFE_INTEGER

`Number.MIN_SAFE_INTEGER`는 자바스크립트에서 안전하게 표현할 수 있는 가장 작은 정수값(-9,007,199,254,740,991)입니다.

### 28.2.6 Number.POSITIVE_INFINITY

`Number.POSITIVE_INFINITY`는 양의 무한대를 나타내는 숫자값 `Infinity`와 같습니다.

### 28.2.7 Number.NEGATIVE_INFINITY

`Number.NEGATIVE_INFINITY`는 음의 무한대를 나타내는 숫자값 `-Infinity`와 같습니다.

### 28.2.8 Number.NaN

`Number.NaN`은 숫자가 아님(Not-A-Number)을 나타내는 숫자값입니다. `Number.NaN`은 `window.NaN`과 같습니다.

## 28.3 Number 메서드

### 28.3.1 Number.isFinite

ES6에서 도입된 `Number.isFinite` 정적 메서드는 인수로 전달된 숫자값이 정상적인 유한수, 즉 `Infinity` 또는 `-Infinity`가 아닌지 검사하여 그 결과를 불리언 값으로 반환합니다. `Number.isFinite` 메서드는 빌트인 전역 함수 `isFinite`와 차이가 있습니다. 빌트인 전역 함수 `isFinite`는 전달받은 인수를 숫자로 암묵적 타입 변환하여 검사를 수행하지만, `Number.isFinite`는 전달받은 인수를 숫자로 암묵적 타입 변환하지 않습니다. 따라서 숫자가 아닌 인수가 주어졌을 때 반환값은 언제나 `false`입니다. 만약 인수가 `NaN`이여도 언제나 `false`를 반환합니다.

```javascript
Number.isFinite(0); // true
Number.isFinite(Number.MAX_VALUE); // true

Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false

Number.isFinite(NaN); // false

Number.isFinite(null); // false
isFinite(null); // true
```

### 28.3.2 Number.isInteger

ES6에서 도입된 `Number.isInteger` 정적 메서드는 인수로 전달된 숫자값이 **정수**(Integer)인지 검사하여 그 결과를 불리언 값으로 반환합니다. 검사하기 전에 인수를 숫자로 암묵적 타입 변환하지 않습니다.

```javascript
Number.isInteger(0); // true
Number.isInteger(123); // true
Number.isInteger(-123); // true

Number.isInteger(0.5); // false
Number.isInteger("123"); // false
Number.isInteger(false); // false

Number.isInteger(Infinity); // false
Number.isInteger(-Infinity); // false
```

### 28.3.3 Number.isNaN

ES6에서 도입된 `Number.isNaN` 정적 메서드는 인수로 전달된 숫자값이 `NaN`인지 검사하여 그 결과를 불리언 값으로 반환합니다. `Number.isNaN` 메서드는 빌트인 전역 함수 `isNaN`과 차이가 있습니다. 빌트인 전역 함수 `isNaN`은 전달받은 인수를 숫자로 암묵적 타입 변환하여 검사를 수행하지만 `Number.isNaN` 메서드는 전달받은 인수를 숫자로 암묵적 타입 변환하지 않습니다. 따라서 숫자가 아닌 인수가 주어졌을 때 반환값은 언제나 `false`입니다.

```javascript
Number.isNaN(NaN); // true

Number.isNaN(undefined); // false
isNaN(undefined); // true
```

### 28.3.4 Number.isSafeInteger

ES6에서 도입된 `Number.isSafeInteger` 정적 메서드는 인수로 전달된 숫자값이 안전한 정수인지 검사하여 그 결과를 불리언 값으로 변환합니다. 안전한 정수값은 `-(2^53^ -1)`과 `2^53^ - 1` 사이의 정수값입니다. 검사전에 인수를 숫자로 암묵적 타입 변환하지 않습니다.

```javascript
Number.isSafeInteger(0); // true
Number.isSageInteger(1000000000000000); // true
Number.isSageInteger(10000000000000001); // false

Number.isSageInteger(0.5); // false
Number.isSafeInteger("123"); // false
Number.isSafeInteger(false); // false

Number.isSafeInteger(Infinity); //  false
```

### 28.3.5 Number.prototype.toExponential

`toExponential` 메서드는 숫자를 지수 표기법으로 변환하여 문자열로 반환합니다. 지수 표기법이란 매우 크거나 작은 숫자를 표기할 때 주로 사용하며 `e`(Exponent) 앞에 있는 숫자에 10의 n승을 곱하는 형식으로 수를 나타내는 방식입니다. 인수로 소수점 이하로 표현할 자릿수를 전달할 수 있습니다.

숫자 뒤의 `.`은 의미가 모호합니다. 부동 소수점 숫자의 소수 구분 기호일 수도 있고 객체 프로퍼티에 접근하기 위한 프로퍼티 접근 연산자일 수도 있습니다. 자바스크립트 엔진은 숫자 뒤의 `.`을 부동 소수점 숫자의 소수 구분 기호로 해석합니다. 따라서 숫자 뒤의 `.`을 소수 구분 기호로 해석하면 뒤에 이어지는 `toExponential`을 프로퍼티로 해석할 수 없으므로 에러(StnextError)가 발생합니다. 숫자 리터럴과 함께 메서드를 사용할 경우 혼란을 방지하기 위해 그룹 연산자를 사용할 것을 권장합니다.

```javascript
(77.1234).toExponential(); // "7.71234e+1"
(77.1234).toExponential(4); // "7.7123e+1"
(77.1234).toExponential(2); // "7.714e+1"
```

### 28.3.6 Number.prototype.toFixed

`toFixed` 메서드는 숫자를 반올림하여 문자열로 반환합니다. 반올림하는 소수점 이하 자릿수를 나타내는 0 ~ 20 사이의 정수값을 인수로 전달할 수 있습니다. 인수를 생략하면 기본값 `0`이 지정됩니다.

```javascript
(12345.6789).toFixed(); // 12346
(12345.6789).toFixed(1); // 12345.7
(12345.6789).toFixed(2); // 12346.68
(12345.6789).toFixed(3); // 12346.679
```

### 28.3.7 Number.prototype.toPrecision

`toPrecision` 메서드는 인수로 전달받은 전체 자릿수까지 유효하도록 나머지 자릿수를 반올림하여 문자열로 반환합니다. 인수로 전달받은 전체 자릿수로 표현할 수 없는 경우 지수 표기법으로 결과를 반환합니다.

```javascript
(12345.6789).toPrecision(); // "12345.6789"
(12345.6789).toPrecision(); // "1e+4"
(12345.6789).toPrecision(); // "1.2e+4"
(12345.6789).toPrecision(); // "12345.7"
```

### 28.3.8 Number.prototype.toString

`toString` 메서드는 숫자를 문자열로 변환하여 반환합니다. 진법을 나타내는 2 ~ 36 사이의 정수값을 인수로 전달할 수 있슴니다. 인수를 생략하면 기본값 10진법이 지정됩니다.

```javascript
(10).toString(); // "10"
(16).toString(2); // "10000"
(16).toString(8); // "20"
(16).toString(16); // "10"
```
