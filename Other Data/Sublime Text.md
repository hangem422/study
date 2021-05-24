# Sublime Text

서브라임 텍스트는 가볍고 확장성이 좋은 편집기로서 많은 개발자들이 사용하고 있는 툴입니다. 기본적으로 하드코딩을 염두에 두고 개발되었는데 여러가지 플러그인들이 붙으면서 그 편의성이 날로 좋아지고 있습니다.

## 1. 어디로든 이동

단축키 `Ctrl + P`를 사용해 아래의 동작들을 실행할 수 있습니다.

- 몇글자의 축약된 키 입력만으로도 파일을 찾아 오픈하고 파일의 내용을 들여다 볼 수 있습니다.
- `@심볼명`과 같이 입려함으로써, 함수등의 특정 심볼 정의로 이동합니다.
- `#단어`와 같이 입력함으로써, 특정 단어로 이동합니다.
- `:줄번호`를 입력함으로써, 해당 라인으로 이동합니다.

## 2. 명령어 모음판

단축기 `Ctrl + Shift + P`를 사용해 여러 동작들을 실행할 수 있다. 메뉴들을 찾아가 실행하거나 단축키를 외우는 것 대신에 몇몇 글자를 입력해서 원하는 명령어를 찾는 방식이다.

## 3. 분할 편집

원하는 만큼의 행과 열을 나눌 수 있고, 하나의 파일에서 두개의 위치를 편집할 수도 있습니다. 다중 모니터를 사용한다면 서브라임 텍스트를 여러개 실행시켜 각각을 편집할 수도 있습니다.

- **View/Layout** 메뉴에서 분할 옵션들을 살펴보니다.
- **File/New View**를 사용하여 새로운 뷰를 엽니다.

## 4. 사용자 정의

단축키, 메뉴들, 스니펫, 매크로 등을 서브라임 텍스트는 간단한 JSON 파일로 사용자 정의 할 수 있습니다. 프로젝트 단위와 파일 단위 설정을 지정할 수 있는 이 시스템은 유연성이 좋습니다.

## 5. 복수 선택

### 5.1 단어 복수 선택

- 원하는 단어를 마우스로 드래그한 후, `Ctrl + D`를 이용해 같은 단어를 복수 선택후 수정할 수 있습니다.
- `Ctrl + U`를 이용해 선택 취소합니다.
- `Ctrl + D`를 이용해서 선택하고 `Ctrl + K`를 이용해 취소하고 `Ctrl + D`를 이용해 다음 단어를 선택할 수 있습니다.
- `Ctrl + D`를 이용해 단어를 선택하고 `Alt + F3`으로 단어를 현재 문서에서 전체 선택할 수 있습니다.

### 5.2 행의 복수 선택

- `Ctrl + L`을 이용하여 커서가 위치한 행을 선택합니다. 반복 사용이 가능합니다.
- 원하는 지역을 마우스로 드래그한 후, `Ctrl + Shift + L`를 이용해 여러개의 행을 동시에 수정할 수 있습니다.

### 5.3 마우스 복수 선택

`Shift + 마우스 클릭`으로 세로행으로 선택합니다.

### 5.4 기타

`Shift + Ctrl + M`으로 브라켓 내의 범위 선택합니다.

## 6. 집중모드

단축키 `Shift + F11`을 사용함으로써, 집중모드에 들어갑니다. 모든 UI나 메뉴들이 제거되고 코딩에 더욱 집중할 수 있는 환경을 조성해줍니다. 전체 모드보다 좀 더 심플해 진다고 생각하면 됩니다.

## 7. Instant Project Switch

서브라임 텍스트의 프로젝트는 수정 및 저장되지 않은 파일을 포함하여 작업 공간의 전체 내용을 캠쳐합니다. 그래서 저장하지 않고 종료를 하더라도 다음에 서브라임 텍스트가 다시 실행되면 그 전 내용을 복원해 놓습니다.

## 8. 출처

- [서브라임 텍스트(Sublime Text) 소개 및 설치 - WEBDIR](https://webdir.tistory.com/394)