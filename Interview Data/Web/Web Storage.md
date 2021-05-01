# Web Storage

Web Storage API는 브라우저에서 쿠키를 사용하는 것보다 훨씬 직관적으로 key/value 데이터를 안전하게 저장할 수 있는 메커니즘을 제공합니다. Storage 객체는 단순한 key-value 저장소이며, 이는 객체와 비슷합니다. 그리고 **영구저장소**(LocalStorage)와 **임시저장소**(SeesionStorage)를 따로 두어 데이터의 지속성을 구분할 수 있어 응용 환경에 맞는 선택이 가능합니다.

## 1. Web Storage와 Cookie의 차이점

Web Storage와 쿠키의 차이점에 대해서 Web Storage가 특별히 좋은 이유는 없다고 봐도 무방합니다. 다만 한가지 매번 서버로 전송되지 않는다는 특징은 꽤나 유용합니다.

### 1.1 Cookie는 매번 서버로 전송됩니다.

웹 사이트에서 쿠키를 설정하면 이후 모든 웹 요청은 쿠키정보를 포함하여 서버로 전송됩니다. Web Storage는 저장된 데이터가 클라이언트에 존재할 뿐 서버로 전송되지 않습니다. 이는 네트워크 트레픽 비용을 줄여줍니다.

### 1.2 단순 문자열을 넘어(스크립트) 객체 정보를 저장할 수 있습니다.

문자열 기반 데이터 외에도 체계적으로 구조화된 객체를 저장할 수 있다는 것은 개발 편의성을 제공해주는 주요한 장점이 됩니다. 브라우저의 지원 여부를 확인해 봐야 하는 항목입니다.

### 1.3 용량의 제한이 없습니다.

쿠키는 개수와 용량에 제한이 있습니다. 하나의 사이트에서 저장할 수 잇는 최대 쿠키 수는 20개입니다. 그리고 하나의 사이트에서 저장할 수 있는 최대 쿠기 크기는 4KB로 제한되어 있습니다. 그러나 Web Storage에는 이러한 제한이 없습니다. 그러나 쿠키도 하위키를 이용하면 이러한 제한을 일부 해소할 수 있습니다. 그리고 대부분의 쿠키의 제한까지 데이터를 저장할 일이 없습니다.

### 1.4 영구 데이터 저장이 가능합니다.

쿠키는 만료일자를 지정하게 되어 있어 언젠가 제거됩니다. 만료일자로 지정된 날짜에 쿠키는 제거되는 겁니다. 만약 만료일자를 지정하지 않으면 세션 쿠키가 됩니다. 만일 영구 쿠키를 원한다면 만료일자를 굉장히 멀게 설정하여 해결할 수 있습니다. 반면 Web Storage는 만료기간의 설정이 없습니다. 즉, 한번 저장한 데이터는 영구적으로 존재하는 겁니다.

## 2. LocalStorage와 SessionStorage

Web Storage는 데이터의 지속성과 관련하여 두 가지 용도의 저장소를 제공합니다. 우선 기본적으로 Web Storage는 쿠키와 마찬가지로 사이트 도메인 단위로 접근이 제한됩니다. 예를 들어 A 도메인에서 저장한 데이터는 B 도메인에서 조회할 수 없다는 겁니다. 이는 데이터의 보안 측면에서 당연합니다.

### 2.1 LocalStorage

저장한 데이터를 명시적으로 지우지 않는 이상 영구적으로 보관이 가능합니다. 앞서 말한대로 도메인마다 별도로 로컬 스토리지가 생성됩니다. Windows 전역 객체의 LocalStarge라는 컬렉션을 통해 저장과 조회가 이루어집니다.

- 브라우저를 종료해도 데이터는 보관디어 다음번 접속에도 그 데이터를 사용할 수 있습니다.

### 2.2 SessionStorage

SessionStorage는 데이터의 지속성과 엑세스 범위에 특수한 제한이 존재합니다. SessionStorage는 windows 전역 객체의 sessionStorage라는 컬렉션을 통해 저장과 조회가 이루어집니다.

- 데이터가 지속적으로 보관되지 않습니다. 이는 마치 브라우저 기반 세션 쿠키와 그 성질이 비슷한데, 현재 페이지가 브라우징되고 있는 브라우저 컨텍스트 내에서만 데이터가 유지됩니다.
- 브라우저가 종료되면 데이터도 같이 지워집니다.
- 같은 사이트의 같은 도메인이라 할지라도 브라우저가 다르면 서로 다른 영역이 됩니다.

## 3. 주의 사항

### 3.1 사용 가능 검사

web Storage를 지원하는 브라우저는 windows 객체에 localStorage라는 property가 존재 합니다. 그러나 여러 가지 이유로 인해 예외가 발생할 수 있습니다. 존재한다 해도 다양한 브라우저가 localStorage를 비활성화하는 설정을 제공하기 때문에 localStorage가 실제로 사용 가능하가는 보장은 없습니다. 따라서 브라우저가 localStorage를 지원한다 해도 스크립트에서 사용 하지 못할 수 있습니다. 이러한 시나리오를 고려하여 사용가능 여부를 검사해야 합니다.

```javascript
function storageAvailable(type) {
  try {
    const storage = window[type];
    const message = "__storage_test__";
    storage.setItem(message, message);
    storage.removeItem(message);
    return true;
  } catch (e) {
    const isDomException = e instanceof DOMException;
    const firefox = e.code === 1014 || e.name === "NS_ERROR_DOM_QUOTA_REACHED"; // Firefox
    const others = e.code === 22 || e.name === "QuotaExceededError"; // Firefox를 제외한 모든 브라우저
    const notEmplty = window[type] && window[type].length !== 0; // 이미 저장된 것이있는 경우에만 QuotaExceededError를 확인하십시오.
    return isDomException && (firefox || others) && notEmplty;
  }
}

if (storageAvailable("localStorage")) {
  // localStorage를 사용할 수 있습니다.
}

if (storageAvailable("sessionStorage")) {
  // sessionStorage 사용할 수 있습니다.
}
```

### 3.2 데이터 수정

Web Storage의 보안은 서로 다른 도메인의 데이터 침범을 막고는 있지만 클라이언트, 즉 사용자를 막고있지는 않습니다. 클라이언트는 얼마든지 저장된 값을 임의로 수정이 가능합니다. 이것은 쿠키와 동일한 개념입니다. 그렇다고 쿠키에 비해 별다른 보안 취약점을 더 가진 것은 아닙니다. 따라서 개발자는 사용자에 의해 이러한 임의 변경에 항상 예의 주시하고 방어 코드의 작성을 잊지 말아야 합니다.

## 4. 출처

- [LocalStorage, SessionStorage, Cookie의 차이점 - CHAI53.log](https://velog.io/@ejchaid/localstorage-sessionstorage-cookie%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)
- [Web Storage API 사용하기 - MDN](https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)
