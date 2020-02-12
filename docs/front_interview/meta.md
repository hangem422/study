# Meta Tag

> `<meta />` 태그는 웹 페이지의 보이지 않는 정보를 제공하는데 쓰입니다. 페이지의 설명 요약, 핵심 키워드, 제작자, 크롤링 정책 등 수많은 정보를 제공할 수 있습니다.

## 1. name 속성

메타 정보에 대한 이름, 즉 뒤에 쌍으로 나오는 content 정보에 대한 이름입니다. `http-equiv` 속성을 설정하면 사용하지 않습니다.

#### 문법

```html
<meta name="name"/>
```

#### 속성 값

- **keywords:** 문서에 대한 핵심어를 쉼표로 분리하여 표시합니다. 문서에 대한 정보를 검색 엔진에 제공합니다.
- **description:** 문서에 대한 설명입니다. 검색 엔진은 검색 결과와 함께 이 설명을 가져갑니다.
- **author:** 저자 이름
- **generator:** 그 문서를 생성하기 위해 사용된 소프트웨어입니다.
- **application-name:** 문서를 표시하는 웹 어플리케이션 이름입니다.
- **viewport:** 

## 2. contnet 속성

name 속성과 http-equiv과 연관된 값을 줍니다.

#### 문법

```html
<meta name="description" content="text" />
```

#### 속성 값

메타 정보에 대한 내용

## 3. http-equiv 속성

content 속성의 정보/값을 위한 HTTP header를 제공합니다.

#### 문법

```html
<meta http-equiv="content-type" content="text/html; charset=UTF-8" />
```

#### 속성 값

- **content-type:** 문서에 대한 문자 부호화 방식을 지정
- **refresh:** 새로고침하기 위한 문서의 간격
- **default-style:** 사용할 선호된 스타일 시트

## 4. charset 속성

html 문서의 문자 부호화 방식을 지정

#### 문법

```html
<meta charset="UTF-8" />
```

#### 속성 값

문서에 대한 문자 부호화 방식