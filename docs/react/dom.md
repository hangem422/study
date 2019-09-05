# DOM에 접근하기

DOM에 직접적인 접근을 해야 할 때는 다음과 같습니다.

- input / textarea 등에 포커스를 해야 할 떄
- 특정 DOM의 크기를 가져와야 할 때
- 특정 DOM에서 스크롤 위치를 가져오거나 설정해야 할 때
- 외부 라이브러리를 사용할 때

## 1. Ref 사용하기

`DOM`에 `ref` 속성을 설정합니다. 값은 `ref`를 파라미터로 가져와 컴포넌트의 맴버 변수로 설정하는 함수를 넣어줍니다.

```JSX
<div ref={ref => { this.mydiv = ref }}></div>
```

## 2. 출처

- [VELOPERT.LOG](https://velopert.com/1148)
