# Sudoku 프로젝트 가이드

## 키보드 단축키는 `e.code` 사용

`keydown` 핸들러에서 글자/숫자 키를 매칭할 때는 `e.key`가 아닌 **`e.code`** 를 쓴다.

- 이유: `e.key`는 IME가 한국어 입력 상태일 때 `'ㅡ'`, `'Process'` 등 엉뚱한 값으로 들어와서 매칭 실패. 유저가 한/영 전환을 잊은 채로 단축키를 누르면 동작 안 함.
- `e.code`는 키의 **물리 위치**라 레이아웃·IME 무관하게 일관됨.

### 매핑 예시

| 단축키 | 사용할 식별자 |
|---|---|
| 알파벳 | `e.code === 'KeyM'`, `'KeyZ'` |
| 상단 숫자열 | `e.code === 'Digit1'` ~ `'Digit9'` |
| 숫자 패드 | `e.code === 'Numpad1'` ~ `'Numpad9'` |
| 화살표 | `e.code === 'ArrowUp'` 등 (key/code 동일) |
| 백스페이스/딜리트 | `e.code === 'Backspace'` / `'Delete'` |

숫자는 `/^(Digit|Numpad)([1-9])$/.test(e.code)` 패턴으로 한 번에 처리하면 상단 숫자열과 숫자 패드 둘 다 커버.

## 구조

- 단일 `index.html` 파일에 HTML/CSS/JS 모두. 빌드 단계 없음, 외부 의존성 없음.
- 상태는 두 곳에 동시 저장:
  - `localStorage['sudoku.state']` — 같은 브라우저 새로고침 복원용
  - URL 해시 `#v1.<base64>` — 기기 간 공유 및 이어서 하기

URL 인코딩 포맷을 바꿀 때는 `decodeState`에서 길이 가드(`if (buf.length >= p + N)`)를 둬서 구버전 URL도 깨지지 않게 유지.
