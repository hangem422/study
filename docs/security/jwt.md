# JWT

> 토큰 기반  인증은 도던 웹서비스에서 많이 사용되고 있습니다.

## 1. 토큰 기반 인증

토큰 기반 인증 시스템을 서택하는데는 여러가지 이유가 있습니다.

#### Stateless 서버

**Stateful 서버**는 클라이언트에게서 요청을 받을 때 마다, 클라이언트의 상태를 계속해서 유지하고 이 정보를 서브스 제공에 이용합니다. **Stateful 서버**의 예제로는 세션을 유지하는 웹서버가 있습니다. **Stateless 서버**는 반대로 상태를 유지하지 않습니다. 상태 정보를 저장하지 않으면, 서버는 클라이언트 측에서 들어오는 요청만으로 작업을 처리합니다. 이렇게 상태가 없는 경우 클라이언트와 서버의 연결고리가 없기 때문에 서버의 **확장성**이 높아집니다.

#### 모바일 어플리케이션에 적합합니다.

만약 Android/iOS 모바일 어플리케이션을 개발 한다면, 안전한 API를 만들기 위해선 쿠키같은 인증시스템은 이상적이지 않습나다. 토큰 기반 인증을 도입한다면, 더욱 간단하게 번거로움을 해결 할 수 있습니다.

#### 인증정보를 다른 어플리케이션으로 전달

대표적으로 OAuth가 있습니다. 페이스북/구글 같은 소셜 계정들을 이용하여 다른 웹서비스에서도 로그인 할 수 있습니다.

#### 보안

토큰 기반 인증 시스템을 사용하여 어플리케이션의 보안을 높일 수 있습니다. 단, 이 토큰 기반 인증을 사용한다고 해서 무조건 해킹의 위험에서 벗어나는 것은 아닙니다.

## 2. JSON Web Token

**JWT**는 웹표준으로서 두 개체에서 JSON 객체를 사용하여 가볍고 자가수용적인 방식으로 정보를 안전성 있게 전달해줍니다. 

- 필요한 모든 정보를 자체적으로 지니고 있습니다.
- 발급된 토큰은 토큰에 대한 기본정보, 전달 할 정보, 그리고 토큰이 검증됐다는 것을 증명해주는 `signature`를 포함하고 있습니다.
- 두 개체 사이에서 손쉽게 전달 될 수 있습니다. 웹 서버의 경우 HTTP의 헤더에 넣어서 전달 할 수도 있고, URL의 파라미터로 전달 할 수도 있습니다.

## 3. JWT 구조

JWT는 `.`을 구분자로 3가지의 문자열로 되어있습니다. 구조는 다음과 같습니다.

![interview_jwt01](../img/interview_jwt01.png)

### 헤더(Header)

**Header**는 두가지의 정보를 지니고 있습니다.

- **typ:** 토큰의 타입을 지정합니다. (JWT)
- **alg:** 해싱 알고리즘을 지정합니다. 해싱 알고리즘으로는 보통 `HMAC SHA256` 혹은 `RSA`가 사용되며, 이 알고리즘은 토큰을 검증 할 때 사용되는 `signature` 부분에서 사용됩니다.

```JSON
{
  "typ": "JWT",
  "alg": "HS256"
}
```

#### Node.js 환경에서 인코딩하기
```javascript
const header = {
  "typ": "JWT",
  "alg": "HS256"
};

const encodedPayload = new Buffer(JSON.stringify(payload))
  .toString('base64')
  .replace('=', '');
```

이 정보를 `base64`로 인코딩하면 다음과 같습니다.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```

### 정보(payload)

**Payload** 부분에는 토큰에 담을 정보가 들어있습니다. 여기에 담는 정보의 한 조각을 클레임(claim)이라고 부르고, 이는 name/value의 한 쌍으로 이루어져 있습니다. 토큰에는 여러개의클레임을 넣을 수 있습니다.

#### 등록된 (registered) 클레임

등록된 클레임들은 서비스에서 필요한 정보들이 아닌, 토큰에 대한 정보들을 담기 위하여 이름이 이미 정해진 클레임들입니다. 등록된 클레임의 사용은 모두 선택적이며, 이에 포함된 클레임 이름들은 다음과 같습니다.

- `iss`: 토큰 발급자 (issuer)
- `sub`: 토큰 제목 (subject)
- `aud`: 토큰 대상자 (audience)
- `exp`: 토큰 만료시간 (expiration). 시간은 `NumericData` 형식으로 되어있어야하며 언제나 현재 시간보다 이후로 설정되어야 합니다.
- `nbf`: Not Before을 의미하며, 토큰의 활성 날짜와 비슷한 개념입니다. 여기에도 `NumericDate` 형식으로 날짜를 지정하며, 이 날짜가 지나기 전까지느 토큰이 처리되지 않습니다.
- `iat`: 토큰이 발급된 시간 (issued at), 이 값을 사용하여 토큰의 `age`가 얼마나 되었는지 판단 할 수 있습니다.
- `jti`: JWT의 고유 식별자로서, 주로 중복적인 처리를 방지하기 위하여 사용됩니다. 일회용 토큰에 사용하면 유용합니다

#### 공개 (public) 클레임

공개 클레임들은 충돌이 방지된 이름을 가지;고 있어야 합니다. 충돌을 방지하기 위해서는, 클레임 이름을 URI 형식으로 짓습니다.

```JSON
{
  "https://sample.con/jwt_claims/is_admin": true
}
```

#### 비공개 (private) 클레임

등록된 클레임도 아니고, 공개된 클레임도 아닙니다. 양 측간에 협의하에 사용되는 클레임 이름들입니다. 공개 클레임과는 달리 이름이 중복되어 충돌이 될 수 있으니 상요할때에 유의해야합니다.

```JSON
{
  "username": "sample"
}
```

#### Node.js 환경에서 인코딩하기

```javascript
const payload = {
  "iss": "sample.com",
  "exp": "1485270000000",
  "httpe://sample.com/jwt_claims/is_admin": true,
  "userId": "11028373727102",
  "uesrname": "sample"
};

const encodedPayload = new Buffer(JSON.stringfy(payload))
  .toString('base64')
  .replace('=', '');
```

이 정보를 `base64`로 인코딩하면 다음과 같습니다.

```
eyJpc3MiOiJ2ZWxvcGVydC5jb20iLCJleHAiOiIxNDg1MjcwMDAwMDAwIiwiaHR0cHM6Ly92ZWxvcGVydC5jb20vand0X2NsYWltcy9pc19hZG1pbiI6dHJ1ZSwidXNlcklkIjoiMTEwMjgzNzM3MjcxMDIiLCJ1c2VybmFtZSI6InZlbG9wZXJ0In0
```

### 서명 (signature)

JSON Web Token의 마지막 부분은 서명(signature)입니다. 이 서명은 헤더의 인코딩 값과, 정보의 인코딩 값을 합친 후 주어진 비밀키로 해쉬하여 생성합니다. 서명 부분을 만드는 슈도코드(pseudocode)의 구조는 다음과 같습니다.

```javascript
HMACSHA256(
  `${base64UrlEncode(header)}.${base64UrlEncode(payload)}`,
  secret
);
```

이렇게 만든 해쉬를, `base64` 형태로 나타내면 됩니다.

#### Node.js 환경에서 해싱 및 인코딩하기

```javascript
const crypto = require('crypto');
const signature = crypto.createHmac('sha256', 'secret').
  .update(encodedHeader + '.' + encodedPayload)
  .digest('base64')
  .replace('=', '');
```

```
WE5fMufM0NDSVGJ8cAolXGkyB5RmYwCto1pQwDIqo2w
```

## 4. jsonwebtoken 모듈

Synchronous Sign with default (HMAC SHA256)

```javascript
var jwt = require('jsonwebtoken');
var token = jwt.sign({ foo: 'bar' }, 'shhhhh');
```

Synchronous Sign with RSA SHA256

```javascript
var privateKey = fs.readFileSync('private.key');
var token = jwt.sign({ foo: 'bar' }, privateKey, { algorithm: 'RS256'});
```

Sign asynchronously

```javascript
jwt.sign({ foo: 'bar' }, privateKey, { algorithm: 'RS256' }, function(err, token) {
  console.log(token);
});
```

## 5. 출처

- [VELOPERT.LOG](https://velopert.com/2448)
- [npm](https://www.npmjs.com/package/jsonwebtoken)