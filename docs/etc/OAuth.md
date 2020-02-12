# 네이버

## 오픈 API 이용 신청

#### 서비스 환경

| 안드로이드   | iOS          | 모바일 웹              | PC 웹        |
| ------------ | ------------ | ---------------------- | ------------ |
| 다운로드 URL | 다운로드 URL | 서비스 URL             | 서비스 URL   |
| 패키지 이름  | URL Scheme   | Callback URL(최대 5개) | Callback URL |

#### 발급

- 클라이언트 ID
- 클라이언트 Secret

## 서버 API 명세

#### 로그인 인증 요청 API

웹 또는 앱에 네이버 로그인 화면을 띄우는 API입니다.

| 메서드     | 인증      | 요청 URL                                 | 출력 포맷      | 설명                             |
| ---------- | --------- | ---------------------------------------- | -------------- | -------------------------------- |
| GET / POST | OAuth 2.0 | https://nid.naver.com/oauth2.0/authorize | URL 리다이렉트 | 네이버 아이디로 로그인 인증 요청 |

##### 요청 변수

| 요청 변수명   | 타입   | 필수 여부 | 기본값 | 설명                                                                                                           |
| :------------ | :----- | :-------: | :----- | :------------------------------------------------------------------------------------------------------------- |
| response_type | string |     Y     | code   | 인증 과정에 대한 내부 구분값으로 'code'로 전송해야 함                                                          |
| client_id     | string |     Y     | -      | 애플리케이션 등록 시 발급받은 Clinest ID 값                                                                    |
| redirect_uri  | string |     Y     | -      | 애플리케이션 등록 시 입력한 Callback URL 값으로 URL 인코딩을 적용한 값                                         |
| state         | string |     Y     | -      | 사이트 간 요청 위조 공격을 방지하기 위해 애플리케이션에서 생성한 상태 토큰값으로 URL 인코딩을 적용한 값을 사용 |
| scope         | string |     N     | -      | 접근 허용 범위를 처리하기 위한 내부 구분값으로 전송할 필요 없음                                                |

```javascript
const client_id = "YOUR_CLIENT_ID";
const redirectURI = encodeURI("YOUR_CALLBACK_URL");
const state = "RANDOM_STATE";
const api_url = `https://nid.naver.com/oauth2.0/authorize?response_type=code&client_id=${client_id}&redirect_uri=${redirectURI}&state=${state}`;
```

##### 출력 결과

- API 요청 성공시 : http://콜백URL/redirect?code={code값}&state={state값}
- API 요청 실패시 : http://콜백URL/redirect?state={state값}&error={에러코드값}&error_description={에러메시지}

| 필드              | 타입   | 설명                                                                                                  |
| ----------------- | ------ | ----------------------------------------------------------------------------------------------------- |
| code              | string | 네이버 아이디로 로그인 인증에 성공하면 반환받는 인증 코드, 접근 토큰 발급에 사용                      |
| state             | string | 사이트 간 요청 위조 공격을 방지하기 위해 애플리케이션에서 생성한 상태 토큰으로 URL 인코딩을 적용한 값 |
| error             | string | 네이버 아이디로 로그인 인증에 실패하면 반환받는 에러 코드                                             |
| error-description | string | 네이버 아이디로 로그인 인증에 실패하면 반환받는 에러 메시지                                           |

#### 접근 토큰 발급/갱신/삭제 요청

이용자가 네이버 회원 인증에 성공하면 API로부터 받은 code 값을 이용해서 접근 토큰 발급 요청 API를 호출합니다.

| 메서드     | 인증      | 요청 URL                             | 출력 포맷 | 설명                          |
| ---------- | --------- | ------------------------------------ | --------- | ----------------------------- |
| GET / POST | OAuth 2.0 | https://nid.naver.com/oauth2.0/token | json      | 접근 토큰 발급/갱신/삭제 요청 |

##### 요청 변수

| 요청 변수명      | 타입   |  필수 여부   | 기본값  | 설명                                                                                                           |
| :--------------- | :----- | :----------: | :------ | :------------------------------------------------------------------------------------------------------------- |
| grant_type       | string |      Y       | -       | 인증 과정에 대한 구분 값                                                                                       |
| client_id        | string |      Y       | -       | 애플리케이션 등록 시 발급받은 Client ID 값                                                                     |
| client_secret    | string |      Y       | -       | 애플리케이션 등록 시 발급받은 Client secret 값                                                                 |
| code             | string | 발급 때 필수 | -       | 로그인 인증 요청 API 호출에 성공하고 리턴받은 인증 코드값                                                      |
| state            | string | 발급 때 필수 | -       | 사이트 간 요청 위조 공격을 방지하기 위해 애플리케이션에서 생성한 상태 토큰값으로 URL 인코딩을 적용한 값을 사용 |
| refresh_token    | string | 갱신 때 필수 | -       | 네이버 사용자 인증에 성공하고 발급받은 갱신 토큰                                                               |
| access_token     | string | 삭제 때 필수 | -       | 기 발급받은 접근 토큰으로 URL 인코딩을 적용한 값을 사용                                                        |
| service_provider | string | 삭제 때 필수 | 'NAVER' | 인증 제공자 이름으로 'NAVER'로 세팅해 전송                                                                     |

```javascript
const code = req.query.code;
const state = req.query.state;
const api_url = `https://nid.naver.com/oauth2.0/token?grant_type=authorization_code&client_id=${client_id}&client_secret=${client_secret}&redirect_uri${redirectURI}&code=${code}&state=${state}`;
```

##### 접근 토큰 발근 요청 출력 결과

| 필드              | 타입    | 설명                                                                 |
| ----------------- | ------- | -------------------------------------------------------------------- |
| access_token      | string  | 접근 토큰, 발급 후 expires_in 파라미터에 설정된 시간이 지나면 만료됨 |
| refresh_token     | string  | 갱신 토큰, 접근 토큰이 만료될 경우 접근 토큰을 다시 발급받을 때 사용 |
| token_type        | string  | 접근 토큰의 타입으로 Bearer와 Mac의 두 가지를 지원                   |
| expire_in         | integer | 접근 토큰의 유효 기간                                                |
| error             | string  | 에러 코드                                                            |
| error_description | string  | 에러 메시지                                                          |

# 카카오
