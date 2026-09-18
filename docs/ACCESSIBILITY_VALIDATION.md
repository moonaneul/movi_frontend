# MOVI Accessibility Validation

이 문서는 MOVI Frontend의 핵심 금융 흐름을 실제로 접근성 관점에서 검증하기 위한 실행 기록입니다.

아직 수행하지 않은 항목은 PASS로 표시하지 않습니다.

---

## 0. Test Environment

실제 테스트를 시작하기 전에 아래 정보를 먼저 기록합니다.

| 항목 | 값 |
|---|---|
| Test date | TODO |
| Frontend commit | TODO |
| Backend commit | TODO |
| Browser | TODO |
| OS | TODO |
| Viewport | TODO |
| Zoom | 100% / 200% |
| Screen reader | 없음 / VoiceOver / TalkBack |
| API mode | Mock / Backend |

---

## 1. Keyboard-only Validation

### 목표

마우스를 사용하지 않고 `Tab`, `Shift+Tab`, `Enter`, `Space`, 화살표 키만으로 핵심 흐름을 완료할 수 있는지 확인합니다.

테스트 중에는 마우스를 사용하지 않습니다.

---

## 2. Scenario A — PIN Login

경로:

```text
/login
→ PIN으로 로그인
→ /login/pin
→ 전화번호 입력
→ PIN 입력
→ 로그인
```

### 확인 항목

| Step | Action | Expected | Result | Note |
|---|---|---|---|---|
| A-1 | `/login`에서 Tab 이동 | 처음 화면 링크 → 로그인 방법 → 회원가입/PIN 로그인 순으로 자연스럽게 이동 | PASS | 사용자 직접 확인 |
| A-2 | PIN으로 로그인에 focus 후 Enter | `/login/pin`으로 이동 | TODO | |
| A-3 | Tab 이동 | 전화번호 입력 → PIN 입력 → 로그인 버튼 순서 | TODO | |
| A-4 | 전화번호 없이 제출 | 전화번호 입력칸으로 focus 이동 | PASS | 사용자 직접 확인 |
| A-5 | 잘못된 PIN 형식 제출 | PIN 입력칸으로 focus 이동 | PASS | 사용자 직접 확인 |
| A-6 | 일반 오류 발생 시 | 오류 요약 영역으로 focus 이동 | TODO | |
| A-7 | Shift+Tab | 역방향 focus 이동이 끊기지 않음 | TODO | |

### 코드상 기대되는 동작

현재 `/login/pin`은 잘못된 전화번호나 PIN이 있을 때 해당 input으로 focus를 돌리고, 기타 오류는 `role="alert"` 영역으로 focus를 이동하도록 구현되어 있습니다.

실제 브라우저에서 이 동작이 그대로 재현되는지 확인합니다.

---

## 3. Scenario B — Balance Inquiry

경로:

```text
/accounts
→ /balance
→ 계좌 선택
→ 잔액 확인
```

### 확인 항목

| Step | Action | Expected | Result | Note |
|---|---|---|---|---|
| B-1 | `/balance` 진입 | 뒤로가기 링크부터 focus 가능 | PASS* | 잔액조회 keyboard-only 흐름 정상으로 사용자 확인 |
| B-2 | Tab 이동 | 계좌 select → 잔액 확인 버튼 순으로 이동 | PASS* | 잔액조회 keyboard-only 흐름 정상으로 사용자 확인 |
| B-3 | select에서 화살표 키 사용 | 다른 계좌를 선택할 수 있음 | PASS* | 잔액조회 keyboard-only 흐름 정상으로 사용자 확인 |
| B-4 | Enter/Space로 잔액 조회 실행 | 마우스 없이 실행 가능 | PASS* | 잔액조회 keyboard-only 흐름 정상으로 사용자 확인 |
| B-5 | API 오류 발생 | 오류 안내 영역 또는 재시도 버튼으로 이동 가능 | TODO | |
| B-6 | 잔액 결과 표시 후 Tab | 결과 다시 듣기 등 이후 조작 요소 접근 가능 | TODO | |

### 추가 확인

- focus ring이 배경과 충분히 구분되는지
- disabled 상태가 focus되지 않는지
- 계좌 정보가 시각적 순서와 keyboard 순서에서 어긋나지 않는지

---

## 4. Scenario C — Direct Transfer

경로:

```text
/transfer
→ 출금 계좌 선택
→ 받는 사람 선택
→ 금액 입력
→ 송금 정보 검토
→ /transfer/review
→ 실행
→ /transfer/result
```

### 입력 화면

| Step | Action | Expected | Result | Note |
|---|---|---|---|---|
| C-1 | Tab으로 출금 계좌 버튼 이동 | 각 계좌 버튼에 focus 가능 | TODO | |
| C-2 | Space/Enter로 계좌 선택 | `aria-pressed` 상태가 변경됨 | TODO | |
| C-3 | 받는 사람 버튼 이동 | 각 수취인 버튼에 focus 가능 | PASS | 사용자 직접 확인 |
| C-4 | Space/Enter로 수취인 선택 | 선택 상태가 시각적으로/상태값으로 반영 | PASS | 사용자 직접 확인 |
| C-5 | 금액 input 이동 | label과 연결된 input에 focus | TODO | |
| C-6 | 금액 빠르게 추가 버튼 | 마우스 없이 +1만/+5만/+10만 사용 가능 | TODO | |
| C-7 | 수취인 미선택 상태 제출 | 오류 alert로 focus 이동 | TODO | |
| C-8 | 잘못된 금액 제출 | 오류 alert로 focus 이동 | TODO | |
| C-9 | 정상 제출 | `/transfer/review`로 이동 | TODO | |

### Review 화면

| Step | Action | Expected | Result | Note |
|---|---|---|---|---|
| C-10 | review 진입 | 수정 링크부터 focus 가능 | TODO | |
| C-11 | Tab 이동 | 송금 실행 버튼과 취소 링크 모두 접근 가능 | TODO | |
| C-12 | 실행 버튼 Enter | 송금 실행/상태 확인 시작 | TODO | |
| C-13 | 실행 오류 | 오류 alert로 focus 이동 | TODO | |
| C-14 | 실행 후 재시도 | 동일 요청 key를 사용하고 중복 송금하지 않음 | TODO | |

### Result 화면

| Step | Action | Expected | Result | Note |
|---|---|---|---|---|
| C-15 | 결과 화면 진입 | 상태 제목과 결과 문구가 즉시 이해 가능 | TODO | |
| C-16 | Tab 이동 | 결과 다시 듣기 버튼 접근 가능 | TODO | |
| C-17 | 차단/완료 상태 | 색상 외 제목/기호/텍스트로도 상태가 구분됨 | TODO | |
| C-18 | 다음 행동 버튼 | 새 송금/거래내역 등 이동 가능 | TODO | |

---

## 5. Scenario D — Error Recovery

다음 오류는 별도로 재현해 봅니다.

- 등록된 받는 사람 로딩 실패
- 잔액 조회 실패
- 음성 분석 실패
- FDS HIGH
- FDS timeout
- Network timeout
- 세션 만료
- 잘못된 금액
- 중복 요청

### 확인 항목

| Test | Expected | Result | Note |
|---|---|---|---|
| 오류 원인이 텍스트로 표시됨 | YES | TODO | |
| 색상만으로 오류를 구분하지 않음 | YES | TODO | |
| 오류 발생 후 focus 위치가 예측 가능함 | YES | TODO | |
| 다음 행동이 명확함 | YES | TODO | |
| 중복 요청이 중복 이체로 이어지지 않음 | YES | 기존 자동 테스트 있음 | |
| FDS 실패 시 거래가 실행되지 않음 | YES | 기존 Backend 검증 있음 | |

---

## 6. 200% Zoom / Reflow

브라우저 zoom을 200%로 설정하고 동일한 핵심 흐름을 반복합니다.

확인 항목:

- 주요 텍스트가 겹치지 않음
- 버튼 문구가 잘리지 않음
- 확인해야 할 거래 정보가 사라지지 않음
- 가로 스크롤 때문에 핵심 기능을 놓치지 않음
- modal/alert가 화면 밖으로 밀리지 않음
- focus ring이 화면 밖에 숨지 않음

결과는 아래에 기록합니다.

| Screen | Result | Issue | Fix |
|---|---|---|---|
| Login | PASS | 문제 없음 | - |
| Accounts | PASS | 문제 없음 | - |
| Balance | PASS | 문제 없음 | - |
| Transfer input | PASS | 문제 없음 | - |
| Transfer review | PASS | 문제 없음 | - |
| Transfer result | PASS | 문제 없음 | - |

---

## 7. Screen Reader Validation

### VoiceOver

환경:
- Device: TODO
- OS: TODO
- Browser: TODO

검증 항목:

- 화면 제목이 자연스럽게 읽히는가
- form label이 input과 연결되어 있는가
- `aria-pressed` 선택 상태가 전달되는가
- 오류 `role="alert"`가 읽히는가
- 송금 review에서 받는 사람 / 금액 / 출금 계좌 순서가 이해되는가
- 완료/차단 상태가 색상 없이도 읽히는가

### TalkBack

환경:
- Device: TODO
- OS: TODO
- Browser: TODO

동일 항목을 반복합니다.

---

## 8. Issue Recording Rule

문제를 발견하면 다음 형식으로 기록합니다.

```text
Issue ID:
Screen:
Environment:
Steps to reproduce:
Expected:
Actual:
Impact:
Fix:
Retest:
```

예:

```text
Issue ID: A11Y-001
Screen: /transfer/review
Environment: Chrome / Windows / Keyboard-only
Steps to reproduce: 송금 실행 중 오류 발생
Expected: 오류 안내문으로 focus 이동
Actual: focus가 body로 이동
Impact: 화면을 보지 않는 사용자가 오류 발생 위치를 알기 어려움
Fix: role=alert 영역에 tabIndex=-1 + focus 이동
Retest: PASS
```

---

## 8.1 Manual Test Log — 2026-09-18

사용자가 실제 브라우저에서 keyboard-only 검증을 수행해 다음 결과를 확인했습니다.

- Login
  - Tab 순서 정상
  - PIN 형식 오류 시 PIN 입력칸으로 focus 이동
  - 전화번호 오류 시 전화번호 입력칸으로 focus 이동
- Balance inquiry
  - 핵심 keyboard-only 흐름 정상
- Transfer
  - 받는 사람 선택 정상

> `PASS*`는 사용자가 해당 화면의 핵심 흐름을 정상으로 확인했지만, 세부 하위 항목별 독립 재현 기록은 아직 남기지 않은 항목입니다.

### 200% Zoom / Reflow

사용자가 브라우저를 200% 확대해 다음 화면을 직접 확인했습니다.

- Login — 정상
- Accounts — 정상
- Balance — 정상
- Transfer input — 정상
- Transfer review — 정상
- Transfer result — 정상

이번 수동 확인에서는 텍스트 겹침, 핵심 버튼 잘림, 핵심 기능을 놓치게 하는 가로 스크롤 등의 문제를 발견하지 못했습니다.

> VoiceOver/TalkBack과 실제 시각장애 사용자 usability test는 아직 수행하지 않았습니다.

---

## 9. Portfolio Rule

접근성 검증이 끝나기 전에는 다음 표현을 사용하지 않습니다.

- “접근성 검증 완료”
- “시각장애 사용자 사용성 검증 완료”
- “WCAG 준수”
- “VoiceOver / TalkBack 지원 검증 완료”

실제 검증이 끝난 뒤에는 PASS 개수보다

> **문제 발견 → 수정 → 재검증**

과정을 중심으로 포트폴리오에 반영합니다.