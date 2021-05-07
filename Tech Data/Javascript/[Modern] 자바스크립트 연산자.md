# 모던 자바스크립트 연산자

## 1. 동등/일치 비교 연산자

`NaN`은 자신과 일치하지 않는 유일한 값입니다. 따라서 숫자가 `NaN`이닞 조사하려면 빌트인 함수 `isNaN`을 사용합니다. 또한 자바스크립트에는 양의 0과 음의 0이 있는데 이들을 비교하면 `true`를 반환합니다. ES6에서 도입된 `Object.is` 메서드는 예측 가능한 정확한 비교 결과를 반환합니다. 그 이외에는 일치 비교 연산자와 동일하게 동작합니다.

```javascript
Object.is(-0, 0); // false
Object.is(NaN, NaN); // true
```

## 2. 지수 연산자

ES7에서 도입된 지수 연산자는 좌형의 피연산자를 **밑**(Base)으로, 우항의 피연산자를 **지수**(Exponent)로 거듭 제곱하여 숫자 값을 반환합니다. 지수 연산자가 도입되기 이전에는 `Math.pow` 메서드를 사용했습니다.

```javascript
2 ** 2; // 4
Math.pow(2, 2); // 4

// 음수를 거듭제곱의 밑으로 사용하려면 괄호로 묶어야 합니다.
(-2) ** 2; // 2
```

## 3. 옵셔널 체이닝 연산자

ES11(ECMAScript2020)에서 도입된 **옵셔널 체이닝** 연산자 `?.`는 좌항의 피연산자가 `null` 또는 `undefined`인 경우 `undefined`를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어갑니다.

```javascript
var elem = null;

elem && elem.value; // null
elem?.value; // undefined

var str = "";

str && str.length; // ""
str?.length; // 0
```

## 4. null 병합 연산자

S11(ECAMScript2020)에서 도입된 **null 병합**(Nullish Coalescing) 연산자 `??`는 좌항의 피연산자가 `null` 또는 `undefined`인 경우 우항의 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환합니다.

```javascript
null ?? "default string"; // "default string"
"" ?? "default string"; // ""

null || "default string"; // "default string"
"" || "default string"; // "default string"
```

## 5. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
