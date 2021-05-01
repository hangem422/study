# DOCTYPE

모든 웹 문서의 시작은 문서형 **정의**(DTD)의 선언으로부터 시작합니다. 일단 웹 사이트의 목적과 목표를 정확히 정한 후, 첫 번째로 HTML로 마크업할 것인지, XHTML, HTML5로 마크업 할지를 결정해야 합니다. 한, 웹 사이트를 제작할 때 필요한 끼능을 구현하기 위해 비추천 요소와 속성을 사용할 것인지를 고려해야 합니다.

## 1. DOCTYPE 정의 및 선언이란?

**문서형 정의**(Document Type Definition, DTD)은 HTML5, XHTML, HTML의 세가지 문서 유형이 존재하며, 기술한 유형에 따라 마크업 문서의 요소와 속성등을 처리하는 기준이 되며 유효성 검사에 이용됩니다. 문서형 정의를 생략하는 경우, 웹 브라우저가 표준모드가 아니라 **비표준모드**(Quirks mode)로 렌더링되어 크로스 브라우징에 어려움을 겪습니다. 문서형 정의는 HTML 문서의 최상위에 위치해야 합니다.

### 1.1 DTD

이전 버전의 HTML(HTML2 ~ HTML4)은 **SGML**(Standard Generalized Markup Language)에 기반을 두어 만들어졌기 때문에 DTD 참조가 필요하며, 이 때문에 DOCTPYE 선언을 하려면 공개 식별자와 시스템 식별자가 포함된 긴 문자열을 작성해야 합니다.

HTML과 XHTML은 각 버전에서 사용가능한 태그나 속성 등을 DTD로 상세히 정의합니다. DTD는 XML 문서들의 클래스에 논리적인 구조를 강요하는 법칙입니다. 따라서 DTD는 모든 적법한 마크업을 쓰고 마크업이 문서에 포함될 수 있는 장소와 그 방법을 지정합니다. 이런 요소 구문 또는 문법은 요소와 그들의 속성의 의미를 정의합니다.

## 2. XHTML과 HTML의 차이

**XHTML**은 기존에 사용되던 **HTML** 규격이 가진 문제점을 극복하고, 보다 다양한 분야에 응용될 수 있도록 해주는 여러가지 확장된 기능을 포함하고 있습니다. 하지만 XHTML이 XML을 기반으로 만들어졌고, 이 XML을 모든 브라우저가 지원하지 않는다는 현실적인 문제를 가직고 있습니다. 차이점은 다음과 같습니다.

- XHTML이 XML 문법을 따르므로, HTML과 문법 규칙이 약간 다릅니다.
- XHTML을 사용하면 할 수 있으나, HTML로는 불가능한 일이 있습니다.
- HTML을 사용하면 할 수 있으나, XHTML로는 불가능한 일이 있습니다.
- CSS를 이해하는 방식에 차이가 있습니다.
- 클라이언트 쪽의 스크립트를 다루는 방식에 차이가 있습니다.

### 2.1 XHTML로 할 수 있는 일

- CDATA 섹션(`<![CDATA[ ... ]]>`) 사용 할 수 있습니다.
- processing-instruction 사용. 예를 들어 XML 문서에 스타일시트를 연결시킬 수 있습니다.
- 다른 XML 이름 영역에 있는 요소들을 포함시킬 수 있습니다.
- `&apos;` 캐릭터 엔티티를 사용할 수 있습니다.

### 2.2 XHTML로 할 수 없는 일

- 기존 HTML에서 사용하던 `<!-- ... -->` 코멘트로 스타일이나 스크립트 일부를 주석 처리할 수 없습니다.
- 문서를 읽고 잇는 도중에 페이지의 일부를 동적으로 생성할 수 없다.(`document.write()`)
- `&nbsp;` 같은 named entity를 사용할 수 없다. 미리 정의된 `&it;`, `&gt;`, `&amp;`, `&quot;`는 사용 가능합니다.
- 자바 스크립트에서 `.innerHTML` 속성을 사용할 수 없다.

### 2.3 CSS를 이해하는 방식의 차이

- CSS의 Element type 선택자가 대문자와 소문자를 구별합니다.
- HTML 에서는 BODY 요소의 backgroud-color, background-imnage, overflow 속성이 최상위 요소(HTML)에도 적용되지만 XHTML에서는 정용되지 않습니다.

### 2.4 XHTML이 HTML보다 더 엄격한가?

단순히 문법적인 면을 따져봤을때는 XGTML이 HTML보다 엄격한 것이 분명합니다. 하지만 브라우저가 XHTML을 HTML과 동일하게 해석하기 때문에 아니기도 합니다.

### 2.5 XHTML이 HTML보다 더 의미 있는 마크업인가?

그렇지 않습니다. XHTML 1.0 rbrurdms HTML 4..01 규격을 새롭게 구성한 것이므로 두 규격은 똑같은 요소와 속성을 가지며 세 사지 문서 타입도 동일합니다. 의미론적으로는 두 규격에 아무 차이가 없습니다. 오히려 Strict 규격을 비교해보면 HTML이 **구조**와 **표현**을 훨씬 엄격하게 구분하므로 보다 의미있다고도 할 수 있습니다.

## 3. HTML5 DOCTYPE 선언

HTML5에서는 SGML에 기반을 두지 않아서 DTD 참조가 필요 없으며, 최소한의 코드 작성이 기본 방향이라 매우 간단히 선언할 수 있다.

```html
<!DOCTYPE html>
```

## 4. XHTML 1.0 DOCTYPE 선언

HTML5가 등장하면서 더 이상은 XHTML 1.0을 고집하는 경우는 적습니다.

### 4.1 strict DTD

W3C가 의도했던 문서 타입으로, 구조 표현을 분히하기 위해 단계적으로 사라질 표현에 관한 요소와 속성을 베제한 문서 타입입니다. center, font, ifram, strike, u, 새창 띄우기 등이 제한됩니다.

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1-strict.dtd">
```

### 4.2 Transitional DTD

기존에 만들어진 문서들과의 호환성을 유지하기 위해 사용합니다. iframe과 새창띄우기(target="\_blank")등을 사용할 수 있어 XHTML 사용시 이것을 추천합니다.

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1-transitional.dtd">
```

### 4.3 Frameset DTD

현재는 거의 사용하지 않는 프레임셋을 구현하기 위해서 사용한다.

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Framset//EN" "http://www.w3.org/TR/xhtml1-frameset.did">
```

### 4.4 XHTML 프롤로그(선언문)

모든 XML 문서는 XML 해석 방법을 브라우저에게 말하는 건언문에서 시작됩니다. XML 선언문 또는 프롤로그는 DOCTYPE과 네임스페이스 선언, 그리고 문서의 타입 또는 마크업 랭귀지를 정의하는 거세 앞서서 정의됩니다. 예를 들면 아래와 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

몇몇 옛 버전의 브라우저들은(IE6)은 `<?xml` 프롤롣그에 막혀서 공백 페이지를 표시합니다. 또한 Mac용 인터넷 익스플로러 4와 5버전과 네스케이프 네비게이터4는 `<?xml` 구문을 이지하지 못하기 때문에 XML 프롤로그가 있는 웹 페이지를 만나게 되면 오작동을 유발합니다. 그리고 프롤로그는 PHP처럼 서버 기반의 분석 엔진과 이따끔씩 문제를 일으키기도 합니다.

따라서 W3C는 프롤로그가 선탣ㄱ적으로 사용되도록 만들었다. 즉, HTML 페이지에서 프롤로그는 생략하거나 또는 새로운 버전의 브라우저를 위해 조건적으로 포함시키는 것 둘 다 가능하다. 만약 기본값이 UTF-8 또는 UTF-16 이외의 문자 셋을 사용하거나 원하거나, 프롤로그를 사용하지 않기를 원한다면 프롤로그 대신 아래와 같이 `meta http-equiv` 태그를 사용할 수 있다.

```html
<meta http-equiv="Content-type" content="text/html; charset=utf-8" />
```

## 5. HTML 4.01 DOCTYPE 선언

근래에는 HTML 4.01로 문서가 작성되는 경우는 없습니다. 기록은 남겨둡니다.

#### strict DTD

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
```

#### Transitional DTD

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
```

#### Framset DTD

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
```

## 6. DOCTYPE과 MIME의 차이

웹 서버는 송신하는 각 문서를 MIME(Multipart Internet Mail Extansions)으로 확인합니다. MIME은 콘텐츠 타입이라고 부르기도 합니다. 이 콘텐츠 타입은 문서의 HTTP head 요소에 들어있으며, 브라우저는 콘텐츠 타입에 따라 문서 처리 방식을 결정하게 됩니다. 콘텐츠 타입이 `text/html`인 문서가 수신되면 브라우저는 그 문서를 HTML로 렌더링합니다.

```html
<meta http-equiv="Content-type" content="text/html; charset=utf-8" />
```

웹 서버는 XHTML을 다음 세가지 콘텐츠 타입 중 하나로 송신한다.

- **text/html:** 브라우저가 문서를 XML로 인식해서는 안될 경우, 다른 XML 네임스페이스에 MathML 같은 내용을 넣지만 않는다면 XHTML 문서를 `text/html`로 송신할 수도 있다. 이렇게 송신된 문서를 브라우저는 HTML로 인식해서 처리합니다.
- **application/xhtml+xml:** XHTML을 `application/xhtml+xml`으로 송신하게 되면 IE7 이하 버전은 이 유현의 페이지를 표시하지 못합니다.
- **XML:** IE7 이하 버전과 같이 `application/xhtml+xml`을 처리하지 못하는 브라우저는 이 문서를 일반 XML로 인식해서 모든 XHTML의 의미론적 의미가 무시되므로 링크에 폼은 제 기능을 못하고 문서 렌더링 시간도 훨씬 오래걸립니다.

현재 XHTML 웹페이지 송신용으로 가장 신뢰성 높은 콘텐츠 타입은 `text/html`입니다. 이 유형은 W3C가 지원하며 대부분의 브라우저에서 인식됩니다. 형식을 이렇게 지정하면 브라우저는 HTML로 인식하고 유효성 검사를 하지 않고 잘 동작합니다(application/xhtml+xml인 경우에는 문서를 자도으로 유효성검사를 완료한 후 렌더링합니다). 브라우저는 웹페이지에 에러가 있든 없든 개의치 않고 어느 버전의 HTML이나 XHTML로든 표시할 수 있는 형식으로 파싱합니다. 반면, XHTML 문서에는 사소한 에러만 있어도 브라우저가 각종 XML 명령을 통해 XHTML 문서 전체 렌더링을 막습니다.

## 6. 출처

[DOCTYPE(문서형 정의) 선언 - WEBDIR](https://webdir.tistory.com/40)
