# Express Rate Limit

## 1. Install

```zsh
$ npm install --save express-rate-limit
```

## 2. Usage

#### All Request

```javascript
const rateLimit = require("exporess-rate-limit");

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});

app.use(limitter);
```

#### Certain Request

```javascript
const rateLimit = require("express-rate-limit");

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});

app.use("/api/", apiLimiter);
```

#### Different Routes

```javascript
const rateLimit = require("exporess-rate-limit");

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});
app.use("/api/", aliLimiter);

const createAccountLimiter = rateLimit({
  windowMs: 60 * 60 * 1000,
  max: 5,
  message: "Too many accounts created from this IP, please try after an hour"
});
app.post("/create-accoutn", createAccountLimiter, function(req, res) {
  // ...
});
```

## 3. Request API

`limit`, `current`, 그리고 `remaining`가 포함된 `req.rateLimit` 프로퍼티가 모든 request에 추가됐다. 만약 스토어가 제공한다면, `restTime` Date 객체도 포함한다. 이것을 이용해 코드에 추가적인 조치를 취하거나 사용자에게 상태를 알려줄 수도 있다.

## 4. Configure Option

#### max

429 응답을 보내기 전 wondowMS 밀리초 동안의 쵀대 연결 수 입니다. 숫자나 숫자를 반환하는 함수 혹은 Promise를 할당할 수 있습니다. 기본값은 `5`이고, 사용하지 않으려면 0을 할당합니다.

#### windowMs

요청 레코드를 메모리에 보관하는 시간(ms) 시간을 할당합니다. 기본값은 `60000`입니다.

#### message

최대값을 초과랗 때 사용자에게 전송되는 오류 메시지를 할당합니다. 문자열, JSON와 같이 Express가 지원하는 값이면 모두 할당할 수 있숩나더, 기본값은 `Too many requests, please try again later.`입니다.

#### statusCode

최대값을 초과할때 반환되는 HTTP 상태 코드 값을 할당합니다. 기본값은 `429`입니다.

#### headers

모든 response에 대해 request 제한 값(X-RateLimit-Limit)과 현재 사용량(X-RateLimit-Ramaining) 그리고 초과 후 재시도까지의 대기시간(Retry-After) header 값을 활성화합니다. 기본값은 `true`입니다.

#### keyGenerator

키를 생성하는 함수를 할당합니다. 기본값:

```javascript
function (req /*, res*/) {
  return req.ip;
}
```

#### handler

요청이 초과했을때 처리하는 함수를 할당합니다. 이 함수는 `reqeust` 객체와 `response` 객체를 받습니다. 다음 미들웨어로 전달해야 할 경어 `next` 파라미터를 사용할 수 있습니다. 가본값:

```javascript
function(req, res/*, next*/) {
  res.status(options.statusCode).send(options.message)
}
```

#### onLimitReached

사용자가 요청 한도를 초과한 첫 순간에 사용되는 함수입니다. 기본값:

```javascript
function (req, res, options) {
  // empty...
}
```

#### skipFailedRequests

실패한 요청은 계산되지 않습니다. 싪패로 간주하는 경우는 다음과 같습니다.

- 응답 상태가 400이상
- 마지막 chunk 데이터가 전송되기 전에 취소된 요청 (resoponse closed 이벤트가 트리거됨)
- resonse 에러 이벤트가 트리거 됨 (기술적으로 계산되었다가 취소되기 때문에, 느린 요청이 한꺼번에 많이 들어오면 제한을 초과할 수 있다. 추후에 수정될 계획입니다.)

기본값은 `false`입니다.

#### skipSuccessfulRequests

성공한 요청(응답 상태 400 미만)은 계산되지 않습니다. (기술적으로 계산되었다가 취소되기 때문에, 느린 요청이 한꺼번에 많이 들어오면 제한을 초과할 수 있다. 추후에 수정될 계획입니다.) 기본값은 `false`입니다.

#### skip

요청을 건너뛰는데 사용되는 함수를 할당합니다. true를 반환하면 해당 요청에 대한 제한을 건너뜁니다. 기본값:

```javascript
function (/*req, req*/) {
  return false;
}
```

#### store

사용할 저장소를 할당합니다. 기본값으로 `MemoryStore`이 할당됩니다.

- MemoryStore: 단순 인메모리 옵션입니다. 다중 프로세스나 서버를 가지는 앱일 경우 state를 공유하지 않습니다.
- late-limit-redis: 대규모 혹은 까다로운 구축 환경에 적합합니다.
- rate-limit-memcached: 나만의 저장소를 만들 수 있습니다. 반드시 다음 함수를 구현해야 합니다.

```javascript
function SomeStore() {
  /**
   * 주어진 키에 대한 기본 저장소의 값을 증가시킨다.
   * @method function
   * @param {string} key - RateLimit에서 전달된 고유 식별자용 키
   * @param {function} cb - 스토아가 완료되면 콜백이 실행 됨
   *
   * 콜백은 세가지 값과 함꼐 실행됩니다.
   * - error (일반적으로 null)
   * - 해당 IP의 hitCount
   * - restTime - JS 날짜 객체 (선택사항이지만, X-RateLimit_Rest 헤더에 필요)
   */
  this.incr = function(key, cb) {
    // increment storage
    cb(null, hits, restTime);
  };

  /**
   * 지정된 키에 대한 기본 저장소 값의 감소. skipFailedRequests가 true일 때만 사용
   * @method function
   * @param {string} key - RateLimit에서 전달된 고유 식별자용 키
   */
  this.decrement = function(key) {
    // decrement storage
  };

  /**
   * 주어진 키를 사용하여 값을 재설정합니다.
   * @method function
   * @param {[type]} key - 재설정할 키
   */
  this.resetKey = function(key) {
    // remove key from storage or reset it to 0
  };
}
```

## 5. Instance API

#### instance.resetKey(Key)

주어진 키에 대한 rate limiting을 재설정합니다. (사용자가 captcha 또는 rate limit을 재설정 할 수 있게 허용한 다음 이 방법을 호출하세요.)

## 6. 출처

[npm](https://www.npmjs.com/package/express-rate-limit)
