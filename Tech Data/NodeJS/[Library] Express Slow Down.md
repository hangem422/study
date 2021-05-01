# Express Slow Down

응답을 차단하기 보다는 속도를 늦추는 Express의 기본 rate-limiting 미들웨어입니다. Public API나 비밀번호 재설정 같은 Endpoint에서 반복 요청을 제한하기 위해 사용합니다.

## 1. Install

```zsh
$ npm install --save express-slow-down
```

## 2. Usage

### 2.1 All Request

```javascript
const slowDown = require("express-slow-down");

app.enable("trust proxy"); // 역박향 프록시 뒤에 있는 경우만 사용 (Heroku, Bluemix, AWS에서 ELB, 사용자 지정 Nginx setup 등을 사용할 경우)

const speedLimiter = slowDown({
  windowMs: 15 * 60 * 1000,
  delayAfter: 100,
  delyMs: 500, // 100 이상의 요청부터는 500ms씩 지연됩니다.
});

app.use(speedLimiter);
```

### 2.2 Certain Request

```javascript
const slowDown = require("express-slow-donw");

app.enalbe("trust proxy");

const resetPasswordSpeedLimiter = slowDown({
  windowMs: 15 * 60 * 1000,
  delayAfter: 5,
  delayMs: 100,
});

app.post("/reset-password/", resetPasswordSpeedLimiter, function (req, res) {
  // ...
});
```

### 2.3 req.slowDown

다음 필드들을 갖는 `req.slowDown` 프로퍼티가 모든 request에 추가됩니다.

- `limit`: `options.delayAfter`의 값 (기본값: 1)
- `current`: 현재 요청 수
- `remaining`: 남은 요청 수
- `restTime`: 윈도우가 리셋되어 `current`가 0이 되고 `remaining`이 `limit`이 될땨까지 남은 시간(밀리 초). 스토어가 지원하지 않으면 `undefined`를 반환한다.
- `delay`: 현재 요청에 추가된 지연 시간(밀리초)

## 3. configuration

##### windowMS

밀리초 단위. request에 대한 기록을 메모리에 보관하는 기간입니다.. 기본값: `60000`입니다.

##### delayAfter

응답을 지연시키기 전 windowMs 동안 최대 연결 수입니다. number 혹은 number를 반환하는 함수를 할당합니다. 기본값은 `1`이고, 사용하지 않으려면 0을 할당합니다.

##### delayMs

밀리초 단위. 응답을 지연하는 추가 시간입니다. 실제 지연 시간은 (현재 요청 수 - delayAfter)에 dleayMs를 곱한 값입니다. 기본 값은 `1000`입니다.

##### maxDelayMs

밀리초 단위. 연속적인 연결로 올라갈 수 있는 delayMs의 최대 값입니다. n번째 이후 요청지연은 항상 maxDelayMs가 됩니다. request timeout이 있는 로드 밸런서나 역방향 프록시를 통해 사용되는 앱에서는 매우 중요합니다. 기본값은 `infinity`입니다.

##### skipFailedRequest

true일때 실패한 요청(응답 상태 400 이상)은 카운팅 되지 않습니다. 기본값은 `false`입니다.

##### skipSuccessfulRequest

true일때 성공한 요청(응답 상태 400 미만)은 카운팅 되지 않습니다. 기본값은 `false`입니다.

##### keyGenerator

키를 생성하는데 사용되는 함수입니다. 기본적으로 사용자 IP 주소(req.ip)가 사용됩니다. 기본값:

```javascript
function (req /*, res*/) {
  return req.ip;
}
```

##### skip

요청을 건너뛰는데 사용되는 함수입니다. 함수에서 true를 반환하면 해당 요청에대한 카운트를 건너뜁니다. 기본값:

```javascript
function (/*req, res*/) {
  return false;
}
```

##### onLimitReached

wibdowMs에 처음으로 도달했을 때 사용되는 함수입니다. 기본값:

```javascript
function (req, res, options) {
  // ...
}
```

##### store

rate limit를 지속적으로 사용하는데 화룡ㅇ되는 저장소를 할당합니다. 기본적으로 `MemoryStore`가 사용됩니다.

> **참고:** Express-slow-down 및 express-rate-limit을 외부 스토어와 함께 사용할 경우, 요청을 이중으로 계산하지 않도록 하기위해 스토어에 두가지 인스턴스를 생성하고 다른 prefix를 제공해야 합니다.

## 4. 출처

[Express Slow Down - npm](https://www.npmjs.com/package/express-slow-down)
