# Cookie vs Session

## 1. 쿠키와 세션을 사용하는 이유

HTTP 프로토콜의 특징이자 약점을 보완하기 위해서 사용합니다. HTTP 프로토콜 환경에서 서버는 클라이언트가 누구인지 확인해야합니다. 그 이유는 HTTP 프로토콜이 **Connectionless**, **Stateless**한 특성이 있기 때문입니다.

### 1.1 Conectionless 프로토콜 (비연결지향)

- 클라이언트가 서버에 **요청**(Reqeust)을 했을 때, 그 요청에 맞는 **응답**(Response)을 보낸 후 연결을 끊는 처리방식입니다.
- HTTP1.1 버전에서 연결을 유지하고, 재활용하는 기능이 Default로 추가되었습니다. (keep-alice 값으로 변경 가능)

### 1.2 Stateless 프로토콜 (상태정보 유지 안함)

- 클라이언트의 상태 정보를 가지지 않는 서버 처리 방식입니다.
- 클라이언트와 첫번쨰 통신에서 데이터를 주고받았다 해도, 두번째 통신에서 이전 데이터를 유지하지 않습니다.
- 화지만, 실제로는 데이터 유지가 필요한 경우가 많습니다.

## 2. 쿠키 (Cookie)

쿠키는 클라이언트(브라우저) 로컬에 저장되는 키와 값이 들어있는 작인 데이터 파일입니다. HTTP의 일종으로 사용자가 어떤한 웹 사이트를 방문할 경우, 그 사이트가 사용하고 있는 서버에서 사용자의 컴퓨터에 저장합니다.

- 필요시 정보를 참조하거나 재사용할 수 있으며, 유효시간을 명시할 수 있습니다.
- 최대 300개까지 저장 가능하며, 하나의 도메인당 20개의 값만 가질 수 있습니다. 하나의 쿠키값은 4KB까지 저장합니다.
- 쿠키는 사용자가 따로 요청하지 않아도 브라우저가 Request시에 Request Header에 넣어서 자동으로 서버에 전송됩니다.

### 2.1 구성 요소

- **이름**: 각가의 쿠키를 구별하는데 사용되는 이름
- **값**: 쿠키의 이름과 관련된 값
- **유효시간**: 쿠키의 유지시간
- **도메인**: 쿠키를 전송할 도메인
- **경로**: 쿠키를 전송할 요청 경로

### 2.2 세션 쿠키와 지속 쿠키

쿠키는 **세션 쿠키**(Session Cookie)와 **지속 쿠키**(Persistent Cookie)로 나뉩니다.

- 만료 날짜/시간을 지정하지 않으면 항상 유지하라는 것으로 판단하고 지속 쿠키에 저장되고, 만료 날짜/시간을 지정하면 세션 쿠키로 저장됩니다.
- 세션 쿠키는 브라우저 메모리에 저장되므로 브라우저가 종료되면 쿠키는 사라지게 됩니다.
- 지속 쿠키는 파이롤 저장되므로 브라우저가 종료되어도 쿠키는 남아있게 됩니다.
- 세션 쿠키의 값은 보안상 꽤나 안전한 브라우저의 메모리에 저장되기 때문에 보안에 유리하지만, 파일로 저장되는 지속 쿠키의 경우 비교적으로 보안에 취약합니다.

### 2.3 동작 방식

1. 클라이언트가 서버에 요청을 보냅니다.
2. 서버에서 쿠키를 생성 후, HTTP 헤더에 포함시켜 응답합니다.
3. 브라우저가 종료되어도 쿠키의 만료 기간이 있다면 클라이언트에서 보관하고 있습니다.
4. 동일 사이트 요청시 할 경우 HTTP 헤더에 쿠키를 함께 보냅니다.
5. 서버에서 쿠키를 읽어 이전 상태 정보를 변경 할 필요가 있을 때, 쿠키를 업데이트하여 변경된 쿠키를 HTTP 헤더에 포함시켜 응답합니다.

## 3. 세션 (Session)

일정 시간동안 같은 사용자(브라우저)로부터 들어오는 일련의 요구를 하나의 상태로 보고, 그 상태를 일정하게 유지시키는 기술입니다. 여기서 일정 시간은 방문자가 웹 브라우저를 통해 웹 서버에 접속한 시점으로부터 웹 브라우저를 종료하여 연결을 끝내는 시점을 맗합니다. 즉, 방문자가 웹 서버에 접속해 있는 상태를 하나의 단위로 보고 그것을 세션이라고 합니다.

- 세션은 쿠키를 기반하고 있지만, 사용자 정보를 브라우저에 저장하는 쿠키와 달리 세션은 서버 측에서 관리합니다.
- 사용자에 대한 정보를 서버에 두며 브라우저를 닫거나 서버에 세션을 삭제했을 때만 삭제가 되므로 쿠키보다 비교적 보안이 좋습니다.
- 저장 데이터에 제한이 없지만, 사용자가 많아질수록 서버 메모리를 많이 차지하게 됩니다.
- 동접자 수가 많은 웹 사이트인 경우 서버에 과부하를 주게 되므로 성능 저하의 요인이 됩니다.
- 클라이언트가 Request를 보내면, 해당 서버의 엔진이 클라이언트에게 유일한 ID를 부여하는데 이것이 세션 ID입니다.

### 3.1 동작 방식

5. 클라이언트는 서버에서 받은 세션 ID에 대해 쿠키를 사용해서 저장합니다.
6. 클라이언트는 서버에 요청할 때, 세션 ID를 쿠키로 서버에 전달합니다.
7. 서버는 세션 ID로 세션에 있는 클라이언트 정보를 가져와 응답합니다.

## 4. 쿠키와 세션의 차이점

- 사용자 정보가 저장되는 위치가 다릅니다. 쿠키는 개인 PC에 저장되며, 세션은 접속중인 서버에 저장됩니다.
- 보안 면에서 세션이 더 우수지만, 요청 속도는 쿠키가 더 빠릅니다. 세션은 서버의 처리가 필요하기 떄문입니다.
- 쿠키는 클라이언트 로컬에 저장되기 때문에 변질되거나, Reqeuest에서 스니핑 당할 우려가 있어서 보안에 취약하지만, 세션은 쿠키를 이용해서 세션 ID만 저장하고 정보는 서버에 저장하기 때문에 비교적 보안성이 좋습니다.
- 지속 쿠키의 경우에 브라우저를 종료해도 계속해서 정보가 남아 있을 수 있습니다. 반면에 세션도 만료시간을 정할 수 있지만, 브라우저가 종료되면 만료시간에 상관없이 삭제됩니다.

## 5. 쿠키와 세션은 캐시와 엄연히 다릅니다.

브라우저 캐시는 이미지나 파일 등이 사용자의 브라우저에 저장되는 것입니다. 이를 이용해 같은 자원을 **로드**(Load)해야 할 때, 해당 자원을 다시 불러오지 않고 캐시되어 있는 자원을 써서 클라이언트 자원을 아끼는 것입니다. 해당 자원이 한 번 브라우저 캐시에 저장되면 다음 필요시에도 브라우저에 있는 걸 재사용하기 때문에 경우에 따라 해당 자원이 변경되어도 변경된 자원을 참조할 수 없는 경우가 생길 수 있습니다.

## 6. 출처

- [쿠키와 세션 개념 - 라이언곰돌이푸](https://interconnection.tistory.com/74)
- [쿠키(Cookie), 세션(Session) 특징 및 차이 - 넌 잘하고 있어](https://hahahoho5915.tistory.com/32)
- [Web - 쿠키와 세션의 차이, 용도, 사용법(cookie,session) - 기본기를 쌓는 정아마추어 코딩블로그](https://jeong-pro.tistory.com/80#recentEntries)