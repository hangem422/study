# Cache Control

`Cache-Control` 일반 헤드 필드는 요청과 응답 내의 캐싱 메커니즘을 위한 디렉트브를 정하기 위해 사용됩니다. 캐싱 디렉티브는 단방향성이며, 이는 요청 내에 주어진 디렉티브가 응답 내에 주어진 디렉티브와 동일하다는 것을 뜻하지는 않는다는 것을 의미합니다.

## 1. 문법

디렉티브는 대소문자를 구문하지 않으며 토큰과 따옴표로 둘러쌓인 문자열 문법을 모두 사용할 수 있는 부가적인 인자를 가집니다. 다중 디렉티브는 쉼표로 구분됩니다.

### 1.1 캐시 요청 디렉티브

HTTP 요청 내에서 클라이언트에 의해 사용될 수 있는 표준 Cache-Control 디렉티브

```
Cache-Control: max-age=<second>
Cache-Control: max-stale[=<second>]
Cache-Control: min-fresh=<second>
Cache-Control: no-cache
Cache-Control: no-store
Cache-Control: no-transform
Cache-Control: only-if-cached
```

### 1.2 캐시 응답 디렉티브

HTTP 응답 내에서 서버에 의해 사용될 수 있는 표준 `Cache-Control` 디렉티브.

```
Cache-control: must-revalidate
Cache-control: no-cache
Cache-control: no-store
Cache-control: no-transform
Cache-control: public
Cache-control: private
Cache-control: proxy-revaldiate
Cache-control: max-age=<seconds>
Cache-control: s-maxage=<seconds>
```

### 1.3 확장 Chache-Control 디렉티브

확장 `Cache-Control` 디렉티브는 핵심 HTTP 표준 문서에 속하지 않습니다.

```
Cache-control: immutable
Cache-control: stale-while-revalidate=<second>
Cache-control: stale-if-error=<second>
```

## 2. 디렉티브

### 2.1 캐시 능력

- `public`: 응답이 어떤 캐시에 의해서든 캐시된다는 것을 나타냅니다.
- `private`: 응답이 단일 사용자를 위한 것이며 고유 캐시에 의해 저장되지 않아야 한다는 것을 나타냅니다. 사설 캐시는 응답을 저장할 수도 있습니다.
- `no-cache`: 캐시된 복사본을 사용자에게 보여주기 이전에, 재검증을 위한 요청을 원 서버로 보내도록 강제합니다.
- `only-if-cached`: 새로운 데이터를 내려받지 않음을 나타냅니다. 클라이언트는 캐시된 응답만을 원하며, 더 최신 복사본이 존재하는지를 알아보기 위해 서버에 요청해선 안됩니다.

### 2.2 만료

- `max-age=<seconds>`: 리소스가 최신 상태라고 판단할 최대 시간을 지정합니다. `Expires`에 반해, 이 디렉티브는 요청 시간과 관련이 있습니다.
- `s-maxage=<seconds>`: `max-age`혹은 `Expires` 헤더를 재정의하나, (프록시와 같은) 공유 캐시에만 적용되며 사설 캐시에 의해서는 무시됩니다.
- `max-stale[=<seconds>]`: 클라이언트가 캐시의 만료 시간을 초과한 응답을 받아들일지를 나타냅니다. 부가적으로, 초 단위의 값을 할당할 수 있는데, 이는 응답이 결코 만료되서는 안되는 시간을 나타냅니다.
- `min-fresh=<second>`: 클라이언트가 지정된 시간(초단위) 동안 신선한 상태로 유지될 응답을 원한다는 것을 나타냅니다.

### 2.3 재검증과 리로딩

- `must-revalidate`: 캐시는 사용하기 이전에 기존 리소스의 상태를 반드시 확인해야 하며 만료된 리소스는 사용되어서는 안됩니다.
- `proxy-revalidate`: `must-revalidate`와 동일하지만, (포록시와 같은) 공유 캐시메만 적용되며 사설 캐시에 의해서는 무시됩니다.

### 2.4 기타

- `no-store`: 캐시는 클라이언트 요청 혹은 서버 응답에 관해서 어떤 것도 저장해서는 안됩니다.
- `no-transform`: 응답에 대해 변형이나 변환이 일너나서는 안됩니다. Content-Encoding, Content-Range, Content-Type 헤더는 프록시에 의해서 수정되어서는 안됩니다. 반투명 프록시는, 예를 들어, 캐시 공간을 절약하고 느린 링크 상의 트래픽량을 줄이기 위해 이미지 포맷들을 변환합니다. no-transform 디렉티브는 이를 허용하지 않습니다.

## 3. 캐싱 막기

캐싱을 끄기 위해서, 다음의 디렉트브들을 보낼 수 잇습니다. 추가로 `Expires`와 `Pragma`헤더를 참고하시기 바랍니다.

```
Cache-Control: no-cache, no-store, must-revalidate
```

4. 출처

- [Cache-Control - MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Cache-Control)
