# 0. Information

## 0.1 도서 정보

- **제목:** 모던 자바스크립트 Deep Dive - 자바스크립트의 기본 개념과 동작 원리
- **저자:** 이웅모 지음
- **출판사:** 위키북스

## 0.2 서문

### 자바스크립트의 태생적 특징

자바스크립트는 웹페이지의 단순한 보조 기능을 처리하기 위한 제한적인 용도를 목적으로 탄생했습니다. 그렇기에 자바스크립트는 되도록 손이 덜 가게끔 친절하게 설계되어 있습니다. 이러한 자바스크립트의 특징은 편리한 경우도 있지만 내부 동작을 이해하기 어렵게 만들기도 합니다. 하지만 자바스크립트는 명실상부한 범용 애플리케이션 개발 언어로 성장했습니다. 따라서 자바스크립트를 학습하는 방식도 이에 걸맞게 변화해야 한다고 믿습니다.

### 기본 개념과 동작 원리 이해의 중요성

프로그래머는 자신이 구현한 코드가 컴퓨터 내부에서 어떻게 동작할 것인지 예측 가능해야 하며, 이를 명확히 설명할 수 있어야 합니다. 그러자면 프로그래밍 언어의 기본 개념과 동작 원리를 정확히 이해하는 것이 중요합니다.

- 문맥에 맞는 정확한 용어를 사용하면 오해를 불러일으키지 않는 명확한 의사소통이 가능해집니다.
- 프레임워크나 다른 언어를 학습하는 데도 도움이 됩니다.
- 자신이 작성하는 코드의 동작을 예측할 수 있습니다.
- 에러가 발생하는 원인을 이해하고 디버깅이 가능합니다.

### 학습 방법

> 빨리 가는 유일한 방법은 제대로 가는 것이다. - Robert C. Martin

1. 기본 개념과 동작 원리를 이해하려고 노력해야 합니다. 이때 학습 중인 키워드 외에 아직 학습하지 않은 개념에 대해서는 일단 기술 부채로 쌓아두고 진행합니다.
2. 코드 구현 능력을 갖추기 위한 연습이 필요합니다. 기본 개념과 동작 원리를 이해해야 하는 이유는 결국 코드를 구현하기 위해서입니다.
3. 프로젝트를 통해 쌓은 경험은 성장에 좋은 밑거름이 됩니다.
4. 추가로 학습이 필요한 사항에 대해서는 첫 사이클, 즉 기본 개념과 동작 원리 이해로 돌아가 학습을 시작합니다.

## 0.3 목차

1. **프로그래밍**
   1. 프로그래밍이랑?
   2. 프로그래밍 언어
   3. 구뭉과 의미
2. **자바스크립트란?**
   1. 자바스크립트의 탄생
   2. 자바스크립트의 표준화
   3. 자바스크립트의 성장의 역사
3. **자바스크립트 개발 환경**
   1. 자바스크립트 실행 환경
   2. 웹 브라우저
   3. Node.js
   4. 비주얼 스튜디오 코드
4. **변수**
   1. 변수란 무엇인가? 왜 필요한가?
   2. 식별자
   3. 변수 선언
   4. 변수 선언의 실행 시점과 변수 호이스팅
   5. 값의 할당
   6. 값의 재할당
   7. 식별자 네이밍 규칙
5. **표현식과 문**
   1. 값
   2. 리터럴
   3. 표현식
   4. 문
   5. 세미콜론과 세미콜론 자동 삽입 기능
   6. 표현식인 문과 표현식이 아닌 문
6. **데이터 타입**
   1. 숫자 타입
   2. 문자열 타입
   3. 템플릿 리터럴
   4. 불리언 타입
   5. undefined 타입
   6. null 타입
   7. 심벌 타입
   8. 객체 타입
   9. 데이터 타입의 필요성
   10. 동적 타이핑
7. **연산자**
   1. 산술 연산자
   2. 할당 연산자
   3. 비교 연산자
   4. 삼항 조건 연산자
   5. 논리 연산자
   6. 쉼표 연산자
   7. 그룹 연산자
   8. typeof 연산자
   9. 지수 연산자
   10. 그 외의 연산자
   11. 연산자의 부수 효과
   12. 연산자 우선순위
   13. 연산자 결합 순위
8. **제어문**
   1. 블록문
   2. 조건문
   3. 반복문
   4. break 문
   5. continue 문
9. **타입 변환관 단축 평가**
   1. 타입 변환이란?
   2. 암묵적 타입 변환
   3. 명시적 타입 변화
   4. 단축 평가
10. **객체 리터럴**
    1. 객체란?
    2. 객체 리터럴에 의한 객체 생성
    3. 프로퍼티
    4. 메서드
    5. 프로퍼티 접근
    6. 프로퍼티 값 갱신
    7. 프로퍼티 동적 생성
    8. 프로퍼티 삭제
    9. ES6에서 추가된 객체 리터럴의 확장 기능
11. **원시 값과 객체의 비교**
    1. 원시 값
    2. 객체
12. **함수**
    1. 함수란?
    2. 함수를 사용하는 이유
    3. 함수 리터럴
    4. 함수 정의
    5. 함수 호출
    6. 참조에 의한 전달과 외부 상태의 변경
    7. 다양한 함수의 형태
13. **스코프**
    1. 스코프란?
    2. 스코프의 종류
    3. 스코프 체인
    4. 함수 래밸 스코프
    5. 렉시컬 스코프
14. **전역 변수의 문제점**
    1. 변수의 생명 주기
    2. 전역 변수의 문제점
    3. 전역 변수의 사용을 억제하는 방법
15. **let, const 키워드와 블록 레벨 스코프**
    1. var 키워드로 선언한 변수의 문제점
    2. let 키워드
    3. const 키워드
    4. var vs. let vs. const
16. **프로퍼티 어트리뷰트**
    1. 내부 슬롯과 내부 메서드
    2. 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체
    3. 데이터 프로퍼티와 접근자 프로퍼티
    4. 프로퍼티 정의
    5. 객체 변경 방지
17. **생성자 함수에 의한 객체 생성**
    1. Object 생성자 함수
    2. 생성자 함수
18. **함수와 일급 객체**
    1. 일급 객체
    2. 함수 객체의 프로퍼티
19. **프로토타입**
    1. 객체지향 프로그래밍
    2. 상속과 프로토타입
    3. 프로토타입 객체
    4. 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입
    5. 프로토타입의 생성 시점
    6. 객체 생성 방식과 프로토타입의 결정
    7. 프로토타입 체인
    8. 오버라이딩과 프로퍼티 섀도잉
    9. 프로토타입 교체
    10. instatnceof 연산자
    11. 직접 상속
    12. 정적 프로퍼티/메서드
    13. 프로퍼티 존재 확인
    14. 프로퍼티 열거
20. **strict mode**
    1. strict mode란?
    2. strict mode의 적용
    3. 전역에 strict mode를 적용하는 것은 피하자
    4. 함수 단위로 strict mode를 적용하는 것도 피하자
    5. strict mode가 발생시키는 에러
    6. strict mode 적용에 의한 변화
21. **빌트인 객체**
    1. 자바스크립트 객체의 분류
    2. 표준 빌트인 객체
    3. 원시값과 래퍼 객체
    4. 전역 객체
22. **this**
    1. this 키워드
    2. 함수 호출 방식과 this 바인딩
23. **실행 컨텍스트**
    1. 소스코드의 타입
    2. 소스코드의 평가와 실행
    3. 실행 컨텍스트의 역할
    4. 실행 컨텍스트 스택
    5. 렉시컬 환경
    6. 실행 컨텍스트의 생성과 식별자 검색 과정
    7. 실행 컨텍스트와 블록 레벨 스코프
24. **클로저**
    1. 렉시컬 스코프
    2. 함수 객체의 내부 슬롯 \[\[Environment]]
    3. 클로저와 렉시컬 환경
    4. 클로저의 활용
    5. 캡슐화와 정보 은닉
    6. 자주 발생하는 실수
25. **클래스**
    1. 클래스는 프로토타입의 문법적 설탕인가?
    2. 클래스 정의
    3. 클래스 호이스팅
    4. 인스턴스 생성
    5. 메서드
    6. 클래스의 인스턴스 생성 과정
    7. 프로퍼티
    8. 상속에 의한 클래스 확장
26. **ES6 함수의 추가 기능**
    1. 함수의 구분
    2. 매서드
    3. 화살표 함수
    4. Rest 파라미터
    5. 매개변수 기본값
27. **배열**
    1. 배열이란?
    2. 자바스크립트 배열은 배열이 아니다
    3. length 프로퍼티와 희소 배열
    4. 배열 생성
    5. 배열 요소의 참조
    6. 배열 요소의 추가와 갱신
    7. 배열 요소의 삭제
    8. 배열 메서드
    9. 배열 고차 함수
28. **Number**
    1. Number 생성자 함수
    2. Number 프로퍼티
    3. Number 메서드
29. **Math**
    1. Math 프로퍼티
    2. Math 메서드
30. **Date**
    1. Date 생성자 함수
    2. Date 메서드
    3. Date를 활용한 시계 예제
31. **RegExp**
    1. 정규 표현식이란?
    2. 정규 표현식의 생성
    3. RegExp 메서드
    4. 플래그
    5. 패턴
    6. 자주 사용하는 정규표현식
32. **String**
    1. String 생성자 함수
    2. length 프로퍼티
    3. String 메서드
33. **7번째 데이터 타입 Symbol**
    1. 심벌이란?
    2. 심벌 값의 생성
    3. 심벌과 상수
    4. 심벌과 프로퍼티 키
    5. 심벌과 프로퍼티 은닉
    6. 심벌과 표준 빌트인 객체 확장
    7. Well-known Symbol
34. **이터러블**
    1. 이터레이션 프로토콜
    2. 빌트인 이터러블
    3. for...of 문
    4. 이터러블과 유사 배열 객체
    5. 이터레이션 프로토콜의 필요성
    6. 사용자 정의 이터러블
35. **스프레드 문법**
    1. 함수 호출문의 인수 목록에서 사용하는 경우
    2. 배열 리터럴 내부에서 사용하는 경우
    3. 객체 리터럴 내부에서 사용하는 경우
36. **디스트럭처링 할당**
    1. 배열 디스트럭처링 할당
    2. 객체 디스트럭처링 할당
37. **Set과 Map**
    1. Set
    2. Map
38. **브라우저의 렌더링 과정**
    1. 요청과 응답
    2. HTTP 1.1과 HTTP 2.0
    3. HTML 파싱과 DOM 생성
    4. CSS 파싱과 CSSOM 생성
    5. 렌더 트리 생성
    6. 자바스크립트 파싱과 실행
    7. 리플로우와 리페인트
    8. 자바스크립트 파싱에 의한 HTML 파싱 중단
    9. script 태그의 async/defer 어트리뷰트
39. **DOM**
    1. 노드
    2. 요소 노드 취득
    3. 노드 탐색
    4. 노드 정보 취득
    5. 요소 노드의 텍스트 조작
    6. DOM 조작
    7. 어트리뷰트
    8. 스타일
    9. DOM 표준
40. **이벤트**
    1. 이벤트 드리븐 프로그래밍
    2. 이벤트 타입
    3. 이벤트 핸들러 등록
    4. 이벤트 핸들러 제거
    5. 이벤트 객체
    6. 이벤트 전파
    7. 이벤트 위임
    8. DOM 요소의 기본 동작의 조작
    9. 이벤트 핸들러 내부 this
    10. 이벤트 핸들러에 인수 전달
    11. 커스텀 이벤트
41. **타이머**
    1. 호출 스케줄링
    2. 타이머 함수
    3. 디바운스와 스로틀
42. **비동기 프로그래밍**
    1. 동기 처리와 비동기 시작
    2. 이벤트 루프와 테스크 큐
43. **Ajax**
    1. Ajax란?
    2. JSON
    3. XMLHttpRequest
44. **REST API**
    1. REST API의 구성
    2. REST API 설계 원칙
    3. JSON Server를 이용한 REST API 실습
45. **프로미스**
    1. 비동기 처리를 위한 콜백 패턴의 단점
    2. 프로미스의 생성
    3. 프로미스의 후속 처리 메서드
    4. 프로미스의 에러 처리
    5. 프로미스 체이닝
    6. 프로미스의 정적 메서드
    7. 마이크로태스크 큐
    8. fetch
46. **제너레이터와 async/await**
    1. 제너레이터란?
    2. 제너레이터 함수의 정의
    3. 제너레이터 객체
    4. 제너레이터의 일시 중지와 재개
    5. 제너레이터의 활용
    6. async/await
47. **에러 치리**
    1. 에러 처리의 필요성
    2. try...catch...finally 문
    3. Error 객체
    4. throw 문
    5. 에러의 전파
48. **모듈**
    1. 모듈의 일반적 의미
    2. 자바스크립트와 모듈
    3. ES6 모듈(ESM)
49. **Babel과 Webpack을 이용한 ES6+/ES.NEXT 개발 환경 구축**
    1. Babel
    2. Webpack