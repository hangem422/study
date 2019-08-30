# DocType

> DTD(Document Type Definition)은 문서 형식을 정의해줍니다. HTML이 어떤 버전으로 작성되었는지 문서의 첫번쨰 줄에 선언하여, 브라우저가 내용을 올바르게 표시할 수 있도록 해줍니다.

## 1. 버전 종류

주로 사용하며 W3C에서 권장하는 독차입 버전은 다음과 같습니다. (일단 HTML5는 제외했습니다.)

- HTML 4.0.1
- XHTML 1.0
- XHTML 1.1

위 세가지 버전 중 **XHTML1.1**은 가장 엄격한 타입으로 보통 잘 사용하지 않습니다. 여기서 엄격하다는 것은 **XHTML1.0**에서 쓸 수 있는 태그 요소들 중에 몇몇은 **XHTML1.1**에서 사용할 경우 비 표준이 됩니다. 보통 비 표준으로 인식하는 태그 요소들은 주로 외형적인 요소 들입니다. 이는 CSS로 대체 할 수 있기 떄문에 비 표준으로 처리하여 해당 요소를 안 쓰도록 하는 것 입니다.

## 2. XHTML과 HTML의 차이

XHTML은 기존에 사용되던 HTML을 XML 바탕으로 새롭게 구성하여 문제점을 극복하고, 보다 다양한 분야에 응용될 수 있도록 해주는 여러가지 확장된 기능을 포함하여 `웹 표준`의 중요한 요소가 되었습니다. 과거에는 XML을 모든 브라우저가 지원하지 않는다는 현신적인 문제가 있었으나, 현재는 대부분의 부라우저들이 지원합니다.

- XHTML이 XML 문법을 따르므로 HTML과 문법 규칙이 약간 다르다.
- XHTML을 사용하면 할 수 있으나, HTML로는 불가능한 일이 있다.
- HTML을 사용하면 할 수 있으나, XHTML로는 불가능한 일이 있다.
- CSS를 이해하는 방식에 차이가 있다.
- 클라이언트 쪽의 스크립트를 다루는 방식에 차이가 있다.

### XHTML에서만 가능한 일

- CDATA 섹션 사용
    <pre><code class="html">
    <![CDATA[ ... ]]>
    </code></pre>
- processing-instruction 사용. 예를 들어 XML 문서에 스타일시트를 연결시킬 수 있다.
    <pre><code class="html">
        <?xml-stylesheet type="text/css" href="style.css" media="screen"?>
    </code></pre>
- 다른 XML 이름 영역(namespace)에 있는 요소들을 포함 시킬 수 있다.

### HTML에서는 가능하나, XHTML에서는 불가능한 일

- 기존 HTML에서 사용하던 `<!-- ... -->` 코멘트로 스타일이나 스크립트의 일부를 주석 처리할 수 없다.
- 문서를 읽고 잇는 도중에 `document.write()`와 같이 페이지의 일부를 동적으로 생성할 수 없다.
- `&npsp;`와 같은 named entity를 사용할 수 없다. 미리 정의된 `&lt;`, `&gt;`, `&amp;`, `&quot;`는 사용 가능하다.
- 자바 스크립트에서 `innerHTML`속성을 사용할 수 없다.
- 스스로 닫히는 태그(self closing tag)를 꼭 닫아줘야 한다.
    ```xml
    <!-- HTML 방식 -->
    <img src="../img.png" alt="이미지는 그 스스로가 내용입니다." />

    <!-- XHTML 방식 -->
    <img src="../img.png" alt="이미지는 그 스스로가 내용입니다." />
    ```

### CSS를 이해하는 방식의 차이

- XHTML에서는 CSSdml Elemnet type 선택자가 대문자와 소문자를 구별한다.
- HTML에서는 BODY 요쇼의 background-color, background-image, overflow 속성이 최상위 요소(HTML)에도 적용되지만 XHTML에서는 적용되지 않는다.

### XHTML에서 스크립트를 다루는 방식

- `document.write()` 메소드 적용되지 않는다.
- `createElement()` 같은 DOM 메소드는 반드시 이름 영역(namespace)에 대응되는 메소드로 바꿔줘야 한다.
- 표준이 아닌 `.innerHTML` 속성 사용할 수 없다.
- CSS에서와 마찬가지로 명시적이지 않은 요소에 관한 문제가 자바스크립트에도 적용 된다.

### XHTML이 HTML보다 더 엄격한가?

단순히 문법적인 면을 따져봤을 때에는 XHTML이 HTML보다 엄격합니다.

### XHTML이 HTML보다 더 의미 있는 마크업인가?

많은 사람들이 XHTML이 HTML보다 더 의미 있는 규칙이라고 생각하지만 그렇지 않습니다. XHTML 1.0 규격은 HTML 4.0 규격을 새롭게 구성한 것이므로 차이가 없습니다. 오히려 XHTML Transitional 규격을 비교해보면 HTML Strict 규격이 **구조와 표현**을 훨씬 엄격하게 구분하므로 보다 의미 있는 규칙입니다.

## 3. 타입 종류

타입 종류는 **HTML4.01**과 **XHTML1.0**에만 있습니다. 그 외에 **XHTML1.1**이나 **HTML5** 같은 경우에는 타입 종류가 따로 없습니다. 이 타입에 따라 몇몇 태그 요소가 표준일 수도 있고, 비 표준일 수도 있습니다.

- Strict
- Transitional
- Frameset

### Strict

**엄격한 규격**을 나타냅니다. 이 타입에서는 center, font 요소 등을 사용할 수 없습니다. 이런 요소는 외형적인 것을 나타내는 요소로, 의미와 외형의 분리가 기본인 웹 표준 정신에 위배되기 떄문입니다.

> center와 font는 각각 문단의 가운데 정렬과 글씨체에 스타일을 줄 수 있는 HTML 요소입니다. 각각 CSS의 text-align, font 등으로 대체할 수 있습니다.

### Transitional

**과도기적인 규격**을 나타냅니다. 비 표준적인 요소들이 표준이 보급되지 않았던 시절에 많이 사용되어 왔습니다. 때문에, 만약 이미 예전부터 서비스하고 있던 페이지를 수정할 경우, 이런 요소들을 불가치하게 남겨놓을 수 밖에 없습니다. 그럴 경우 이 타입을 사용합니다.

### Frameset

프래임을 사용하는 페이지를 나타냅니다. 이 프레임이라는 것은 예전 인터넷이 보급되던 시절, 나모 웹 에디터 같은 것으로 웹 페이지를 만들떄 사용하던 방식으로, 지금은 거의 사용하지 않는 타입입니다. HTML5에선 폐기 되었습니다.

### 종류별 선언

- HTML4.01 Strict
    ```xml
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
    ```
- HTML4.01 Transitional
    ```xml
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    ```
- HTML4.01 Frameset
    ```xml
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
    ```
- XHTML 1.0 Strict
    ```xml
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
    ```
- XHTML 1.0 Transitional
    ```xml
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    ```
- XHTML 1.0 Frameset
    ```xml
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
    ```
- XHTML 1.1
    ```xml
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
    ```

## 4. 표준과 비 표준

DocType을 제거한 비 표준모드는 예측하기 힘든 결과를 가져다 줍니다. 만약 화면이 깨져 보이더라도, 모든 브라우저가 동일한 모습으로 깨진다면 수정이 어렵지 않을 것입니다. 그리고 이 **표준 모두 준수**는 **크로스 브라우징**의 가장 기본적인 시작입니다.

## 5. 출처

- [https://tuhbm.tistory.com/2](https://tuhbm.tistory.com/2)
- [http://blog.wystan.net/2007/05/24/xhtml-vs-html](http://blog.wystan.net/2007/05/24/xhtml-vs-html)
- [http://webberstudy.com/html-css/html-2/doctype/](http://webberstudy.com/html-css/html-2/doctype/)
