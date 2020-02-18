# Helmet

> helmet은 일부 HTTP 응답 헤더를 설정하는데 도움이 되는 13개의 미들웨어 기능을 모아놓은 것 입니다.

## 1. 설치하기

```zsh
npm install helmet --save
```

## 2. 사용하기

헤더가 확실하게 설정되로록 미들웨어 스택의 앞부분에 사용하는 것이 가장 좋다. 익스프래스 3을 사용하는 경우, `app.router` 전에 미들웨러를 나열해야 합니다.

```javascript
const express = require("express");
const helmet = require("helmet");

const app = express();

app.use(helmet());
```

개별적으로 사용할 수도 있다.

```javascript
app.use(helmet.noCache());
app.use(helmet.frameguard());
```

default로 실행되는 미들웨어를 사용하지 않도록 설정할 수 있다.

```javascript
app.use(
  helmet({
    fraeguard: false
  })
);
```

미들웨어에 대한 옵션을 설정할 수 있습니다. 이와 같은 설정 옵션에는 미들웨어가 기본값인지 관계없이 항상 포함되어 있다.

```javascript
app.use(
  helmet({
    framefuard: {
      action: "deny"
    }
  })
);
```

## 3. Modules

| Module                                            | Description                                   | Include by default |
| ------------------------------------------------- | --------------------------------------------- | ------------------ |
| [contentSecurityPolicy](#content-security-policy) | Setting Content Security Policy               |                    |
| dnsPrefetchControl                                | Controls browser DNS prefetching              | YES                |
| expectCt                                          | Handling Certificate Transparency             |                    |
| featurePolicy                                     | Limit your site’s features                    |                    |
| frameguard                                        | Prevent clickjacking                          | YES                |
| hidePoweredBy                                     | Remove the X-Powered-By header                | YES                |
| hsts                                              | HTTP Strict Transport Security                | YES                |
| ieNoOpen                                          | Sets X-Download-Options for IE8+              | YES                |
| noCache                                           | Disable client-side caching                   |                    |
| noSniff                                           | Keep clients from sniffing the MIME type      | YES                |
| permittedCrossDomainPolicies                      | Handling Adobe products’ crossdomain requests |                    |
| referrerPolicy                                    | Hide the Referer header                       |                    |
| xssFilter                                         | Adds some small XSS protections               | YES                |

### Content Security Policy

CSP 모듈은 악의적인 Javascript, Css 또는 plubins등의 주입을 막는 Content-Security-Policy 헤더를 설정한다.

##### 공격

해커는 당신의 웹 페이지에 무언가를 올릴 수 있다면, 많은 악행을 할 수 있다. 가장 끔찍한 공격은 아마 당신의 페이지에 악의적인 Javascript를 올리는 cross-site scripting (XSS)일 것이다. 만약 내가 덩신의 페이지에서 Javascript를 실행할 수 있다면, 나는 인증 쿠키나 사용자 로그를 훔치는것과 같은 많은 악행들을 저지를 수 있다.

Javascript를 실행할 수 없어도 공격자가 할 수 있는 것은 많다. 만약 내가 당신의 사이트에 아주 작은 1\*1의 투명 이미지를 넣을 수 있다면, 나는 당신의 사이트가 얼마나 많은 트래픽을 받는지 알 수 있을 것이다. 또한 플래시와 같은 취약한 브라우저 플러그인을 실행할 수 있다면, 나는 그 결함을 이용하여 당신이 원치 않는 일을 할 수 있을 것이다.

CSP는 특정한 공격을 막기 위한 것이 아니다. 이것의 목적은 당신이 기대하지 않는 그 어떤 것도 여러분의 웹 페이지에 올라갈 수 없게 하는 것이다.

##### Directives

Helmet의 `csp` 모듈은 Content Security Policies 설정을 도와준다.

```javascript
const csp = require("helmet-csp");

app.use(
  csp({
    directives: {
      defaultSrc: ["'self'", "default.com"],
      scriptSrc: ["'slef'", "'unsafe-inline'"],
      sandbox: ["allow-forms", "allow-scripts"],
      reportUri: "/report-violation",
      objectSrc: ["'none'"],
      upgradeInsecureRequests: true,
      workerSrc: false // This is not set.
    }
  })
);
```

다음과 같은 directives를 지원합니다.

| kebab-cased             | camel-cased          | description                                                                                                    |
| ----------------------- | -------------------- | -------------------------------------------------------------------------------------------------------------- |
| base-uri                | baseUri              | document의 `<base>` 요소에 사용할 수 있는 URL을 제한                                                           |
| block-all-mixed-content | blockAllMixedContent | HTTPS를 사용하여 페이지가 로드될 때 HTTP를 사용하여 asset을 로드하는 것을 방지                                 |
| child-src               | childSrc             | `<frame>`이나 `<iframe>`과 같은 요소를 사용하여 로드된 웹 작업자나 내포된 검색 컨텍스트에서 가능한 소스를 정의 |
| connect-src             | connectSrc           | 스크립트 인터페이스를 사용하여 로드할 수 있는 URL 제한                                                         |
| default-src             | defaultSrc           | 다른 fetch directive들의 fallback으로 제공됨                                                                   |
