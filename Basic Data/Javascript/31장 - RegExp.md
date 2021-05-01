# RegExp

## 3.1 정규 표현식이란?

**정규 표현식**(Regular Expression)은 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 **형식 언어**(Formal Language)입니다. 정규 표현식은 자바스크립트의 고유 문법이 아니며, 대부분의 프로그래밍 언어와 코드 에디터에 내장되어 있습니다. 자바스크립트눈 **펄**(Perl)의 정규 표현식 문법을 ES3부터 도입했습니다. 정규 표현식은 문자열을 대상으로 패턴 매칭 기능을 제공합니다. 패턴 매칭 기능이란 특정 패턴과 일치하는 문자열을 검색하거나 추출 또는 치환할 수 있는 기능을 말합니다.

```javascript
const tel = "010-1234-567팔";
const regExp = /^\d{3}-\d{4}-\d{4}$/;

regExp.text(tel); // false
```

정규표현식을 사용하면 반복문과 조건문 없이 패턴을 정의하고 테스트하는 것으로 간단히 체크할 수 있습니다. 다만 정규표현식은 주석이나 공백을 허용하지 않고 여러 가지 기호를 혼합하여 사용하기 때문에 가독성이 좋지 않다는 문제가 있습니다.

## 31.2 정규 표현식의 생성

정규 표현식은 객체(RegExp 객체)를 생성하기 위해서는 정규 표현식 리터럴과 `RegExp` 생성자 함수를 사용할 수 있습니다. 일반적인 방법은 정규 표현식 리터럴을 사용하는 것입니다. 정규 표현식은 패턴과 플래그로 구성됩니다.

```javascript
// 패턴: is
// 플래그: i => 대소문자를 구별하지 않고 검색합니다.

/// 리터럴 사용
const regExp1 = /is/i;

// 생성자 함수 사용
const regExp1 = new RegExp(/is/i); // ES6
const regExp2 = new RegExp(/is/, "i");
const regExp3 = new RegExp("is", "i");
```

## 31.3 RegExp 메서드

### 31.3.1 RegExp.prototype.exec

`exec` 메서드는 인수로 전달받은 문자열에 대한 정규 표현식의 패턴을 검색하여 매칭 결과를 배열로 반환합니다. 매칭 결과가 없을 경우 `null`을 반환합니다. `exec` 메서드는 문자열 내의 모든 패턴을 검색하는 `g` 플래그를 지정해도 첫 번째 매칭 결과만 반환하므로 주의하기 바랍니다.

```javascript
const target = "Is this all there is?";
const regExp = /is/;

regExp.exec(target);
// ["is", index: 5, input: "Is this all there is?", groups: undefined]
```

| 속성          | 설명                                                                                   |
| ------------- | -------------------------------------------------------------------------------------- |
| `[0]`         | 일치하는 전체 문자                                                                     |
| `[1], ...[n]` | (존재하는 경우) 괄호로 감산 부분문자열. 괄호로 감싼 부분문자열 숫자의 제한은 없습니다. |
| `index`       | 일치가 문자열에서 위치하는 인덱스                                                      |
| `input`       | 원본 문자열                                                                            |

### 31.3.2 RegExp.prototype.test

`test` 메서드는 인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환합니다.

```javascript
const target = "Is this all there is?";
const regExp = /is/;

regExp.test(target); // true
```

### 31.3.3 String.prototype.match

`String` 표준 빌트인 객체가 제공하는 `match` 메서드는 대상 문자열과 인수로 전달받은 정규 표현식과의 미챙 결과를 배열로 반환합니다.

```javascript
const target = "Is this all there is?";
const regExp = /is/;

target.match(regExp);
// ["is", index: 5, input: "Is this all there is?", groups: undefined]
```

`Srring.prototype.match` 메서드는 `g` 프래그가 지정되면 모든 매칭 결과를 배열로 반환합니다.

```javascript
const target = "Is this all there is?";
const regExp = /is/g;

target.match(regExp); // ["is", "is"]
```

## 31.4 플래그

패턴과 함께 정규 표현식을 구성하는 플래그는 정규 표현식의 검색 방식을 설정하기 위해 사용합니다. 총 6개의 플르그 중 중요한 3개의 플래그를 살펴봅니다.

| 플래그 | 의미        | 설명                                                             |
| :----: | ----------- | ---------------------------------------------------------------- |
|  `i`   | ignore case | 대소문자를 구별하지 않고 패턴을 검색합니다.                      |
|  `g`   | Global      | 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색합니다 |
|  `m`   | Multi line  | 문자열의 행이 바뀌더라도 패턴 검색을 계속합니다.                 |

플래그는 옵션이므로 선택적으로 사용할 수 있으며, 순서와 상관없이 하나 이상의 플래그를 동시에 설정할 수도 있습니다.

```javascript
const target = "Is this all there is?";
const regExp = /is/gi;

target.match(regExp); // ["is", "is", "is"]
```

## 31.5 패턴

정규 표현식의 패턴은 문자열의 일정한 규칙을 표현하기 위해 사용하며, 정규 표현식의 플래그는 정규 표현식의 검색 방식을 설정하기 위해 사용합니다. 패턴은 `/`로 열고 닫으며 문자열의 따옴표는 생략합니다. 따옴표를 포함하면 따옴표까지도 패턴에 포함되어 검색됩니다. 또한 패턴은 특별한 의미를 가지는 **메타문자**(Meta Character) 또는 기호로 표현할 수 있습니다. 어떤 문자열 내에 패턴과 일치하는 문자열이 존재할 때 **정규 표현식과 매치**(Match)한다고 표현합니다.

### 31.5.1 문자열 검색

정규 표현식의 패턴에 문자 또는 문자열을 지정하면 검색 대상 문자열에서 패턴으로 지정한 문자 또는 문자열을 검색합니다. 물론 정규 표현식을 생성하는 것만으로 검색이 수행되지는 않습니다. 앞서 살펴본 `RegExp` 메서드를 사용하여 검색 대상 문자열과 정규 표현식의 매칭 결과를 구하면 검색이 수행됩니다.

```javascript
const target = "Is this all there is?";
const regExp = /is/;

target.match(regExp); // ["is", index: 5, input: "Is this all there is?", groups: undefined]
```

### 31.5.2 임의의 문자열 검색

`.`은 임의의 문자 한 개를 의미합니다. 문자의 내용은 무엇이든 상관 없습니다.

```javascript
const target = "Is this all there is?";
const regExp = /.../g;

target.match(regExp); // ["Is ", "this", "s a", "ll ", "the", "re ", "is?"]
```

### 31.5.3 반복 검색

`{m, n}`은 앞선 패턴이 최소 m번, 최대 n번 반복되는 문자열을 의미합니다. 콤마 뒤에 공백이 있으면 정상 동작하지 않으므로 주의하기 바랍니다.

```javascript
const target = "A AA B BB Aa Bb AAA";
const regExp = /A{1,2}/g;

target.match(regExp); // ["A", "AA", "A", "AA", "A"]
```

`{n}`은 앞선 패턴이 n번 반복되는 문자열을 의미합니다. `{n}`은 `{n,n}`과 같습니다.

```javascript
const target = "A AA B BB Aa Bb AAA";
const regExp = /A{2}/g;

target.match(regExp); // ["AA", "AA"]
```

`{n,}`은 앞선 패턴이 최소 n번 이상 반복되는 문자열을 의미합니다.

```javascript
const target = "A AA B BB Aa Bb AAA";
const regExp = /A{2,}/g;

target.match(regExp); // ["AA", "AAA"]
```

`+`는 앞선 패턴이 최소 한번 이상 반복되는 문자열을 의미합니다. 즉 `+`는 `{1,}`과 같습니다.

```javascript
const target = "A AA B BB Aa Bb AAA";
const regExp = /A+/g;

target.match(regExp); // ["A", "AA", "A", "AAA"]
```

`?`는 앞선 패턴이 최대 한 번(0번 초함) 이상 반복되는 문자열을 의미합니다. 즉 `?`는 `{0,1}`과 같습니다.

```javascript
const target = "color colour";
const regExp = /colou?r/g;

target.match(regExp); // ["color", "colour"]
```

### 31.5.4 OR 검색

`|`은 or의 의미를 갖습니다.

```javascript
const target = "A AA B BB Aa Bb";
const regExp = /A|B/g;

target.match(regExp); // ["A", "A", "A", "B", "B", "B", "A", "B"]
```

분해되지 않은 단어 레벨로 검색하기 위해서는 `+`를 함께 사용합니다.

```javascript
const target = "A AA B BB Aa Bb";
const regExp = /A+|B+/g;

target.mathc(regExp); // ["A", "AA", "B", "BB", "A", "B"]
```

위 예제는 패턴을 or로 한 번 이상 반복하는 것인데 이를 간단히 표현하면 다음과 같습니다. `[]` 내의 문자는 or로 동작합니다. 그 뒤에 `+`를 사용하면 앞선 패턴을 한 번 이상 반복합니다.

```javascript
const target = "A AA B BB Aa Bb";
const regExp = /[AB]+/g;

target.mathc(regExp); // ["A", "AA", "B", "BB", "A", "B"]
```

범위를 지정하려면 `[]` 내에 `-`를 사용합니다. 다음 예제의 경우 대문자 알파벳을 검색합니다.

```javascript
const target = "A AA BB ZZ Aa Bb";
const regExp = /[A-Z]+/g;

target.mathc(regExp); // ["A, "AA", "BB", "ZZ", "A", "B"]
```

대소문자를 구별하지 않고 알파벳을 검색하는 방법은 다음과 같습니다.

```javascript
const target = "AA BB Aa Bb 12";
const regExp = /[A-Za-z]+/g;

target.mathc(regExp); // ["AA", "BB", "Aa", "Bb"]
```

숫자를 검색하는 방법은 다음과 같습니다.

```javascript
const target = "AA BB 12,345";
const regExp = /[0-9]+/g;

target.match(regExp); // ["12", "345"]
```

위 예제의 경우 쉼표 때문에 매칭 결과가 분리되므로 쉼표를 패턴에 포함시킵니다.

```javascript
const target = "AA BB 12,345";
const regExp = /[0-9,]+/g;

target.match(regExp); // ["12,345"]
```

`\d`는 숫자를 의미합니다. 즉, `\d`는 `[0-9]`와 같습니다. `\D`는 `\d`와 반대로 동작합니다. 즉, `\D`는 숫자가 아닌 문자를 의미합니다.

```javascript
const target = "AA BB 12,345";

const regExp1 = /[\d,]+/g;
target.match(regExp1); // ["12,345"]

const regExp2 = /[\D,]+/g;
target.match(regExp2); // ["AA BB", ","]
```

`\w`는 알파벳, 숫자, 언더스코어를 의미합니다. 즉, `\w`는 `[A-Zz-z0-9_]`와 같습ㄴ디ㅏ. `\W`는 `\w`와 반대로 동작합니다. 즉, `\W`는 앞파벳, 숫자, 언더스코어가 아닌 문자를 의미합니다.

```javascript
const target = "Aa Bb 12,345 _$%&";

const regExp1 = /[\w,]+/g;
target.match(regExp1); // ["Aa", "Bb", "12,345", "_"]

const regExp2 = /[\W,]+/g;
target.match(regExp2); // [" ", " ", ",", " ", "$%&"]
```

### 31.5.5 NOT 검색

`[...]` 내의 `^`은 not의 의미를 갖습니다. 따라서 `\D`는 `[^0-9]`와 같고, `\W`는 `[^A-Za-z0-9_]`와 같습니다.

```javascript
const target = "AA BB 12 Aa Bb";
const regExp = /[^0-9]+/g;

target.match(regExp); // ["AA BB ", " Aa Bb"];
```

### 31.5.6 시작 위치로 검색

`[...]` 밖의 `^`은 문자열의 시작을 의미합니다. 단, `[...]` 내의 `^`은 not의 의미를 가지므로 주의하기 바랍니다.

```javascript
const target = "https://poiemaweb.com";
const regExp = /^https/;

regExp.test(target); // true
```

### 31.5.7 마지막 위치로 검색

`$`는 문자열의 마지막을 의미합니다.

```javascript
const target = "https://poiemaweb.com";
const regExp = /com$/;

regExp.test(target); // true
```

## 31.6 자주 사용하는 정규표현식

### 31.6.1 특정 단어로 시작하는지 검사

```javascript
const url = "https://example.com";

// 'http://' 또는 'https://'로 시작하는지 검사합니다.
/^https?:\/\//.test(url); // true
```

### 특정 단어로 끝나는지 검사

```javascript
const fileName = "index.html";

// 'html'로 끝나는지 검사합니다.
/html$/.tets(fileName); // true
```

### 31.6.3 숫자로만 이루어진 문자열인지 검사

```javascript
const target = "12345";

// 숫자로만 이루어진 문자열인지 검사합니다.
/^\d+$/.test(target); // true
```

### 31.6.4 하나 이상의 공백으로 시작하는지 검사

```javascript
const target = " Hi!";

// 하나 이상의 공백으로 시작하는지 검사
/^[\s]+/.test(target); // true
```

### 31.6.5 아이디로 사용 가능한지 검사

```javascript
const id = "abc123";

// 알파벳 대소문자 또는 숫자로 시작하고 끝나며 4 ~ 20자리인지 검사합니다.
/^[A-Za-z0-9]{4,10}$/.test(id); // true
```

### 31.6.6 메일 주소 형식에 맞는지 검사

```javascript
const email = "ungmo2@gmail.com";

/^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,3}$/.test(
  email
); //  true
```

참고로 **인터넷 메시지 형식**(Internet Meesage Format)규약인 RFC 5322에 맞는 정교한 패턴 매칭이 필요하다면 다음과 같이 무척이나 복잡한 패턴을 사용할 필요가 있습니다.

```
(?:[a-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+/=?^_`{|}~-]+)*|"(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21\x23-\x5b\x5d-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])*")@(?:(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?|\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[a-z0-9-]*[a-z0-9]:(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21-\x5a\x53-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])+)\])
```

```javascript
/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
```

### 31.6.7 핸드폰 번호 형식에 맞는지 검사

```javascript
const cellphone = "010-1234-5678";

/^\d{3}-\d{3,4}-\d{4}$/.test(cellphone); // true
```

### 31.6.8 특수 문자 포함 여부 검사

특수 문자는 `A-Za-z0-9` 이외의 문자입니다.

```javascript
const traget = "abc#123";

/[^A-Za-z0-9]/gi.test(target); // true
```

다음 방식으로 대체해 사용할 수도 있습니다. 이 방식은 특수 문자를 선택적으로 검사할 수 있다는 장점이 있습니다.

```javascript
/[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]/gi.test(target);
```
