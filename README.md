# MOVI Frontend

MOVI의 Next.js Frontend 저장소입니다.

사용자가 계좌 조회와 송금 과정을 확인하고, 음성을 사용할 수 없는 상황에서도 키보드·터치로 주요 금융 흐름을 이용할 수 있도록 구성했습니다.

전체 프로젝트 설명:
- [MOVI Overview](https://github.com/moonaneul/movi-overview)

---

## 주요 화면

- 로그인 / PIN 로그인
- 연결 계좌
- 잔액 조회
- 직접 송금
- 송금 review
- 거래 결과
- 음성 입력 / 결과 안내

주요 경로:

```text
/login
/login/pin
/accounts
/balance
/transfer
/transfer/review
/transfer/result
```

---

## Direct Transfer Flow

```text
출금 계좌 선택
→ 등록된 받는 사람 선택
→ 금액 입력
→ Backend review
→ 거래 내용 확인
→ 사용자 명시적 확인
→ 거래 실행 / FDS
→ 결과 표시
```

송금 입력 화면에서 바로 거래를 실행하지 않고, Backend가 확인한 거래 정보를 review 화면에서 다시 보여줍니다.

---

## Voice-assisted Flow

```text
음성 입력
→ Frontend가 Backend로 전달
→ Backend가 Voice AI 호출
→ Backend 검증
→ 재질문 또는 거래 내용 확인
→ 사용자 확인
→ 결과 표시 / TTS
```

Frontend는 Voice AI / FDS 서버를 직접 호출하지 않습니다.

---

## Accessibility

음성을 유일한 입력 방식으로 두지 않았습니다.

- 키보드·터치 대체 조작
- focus-visible
- form label 연결
- 오류 `role="alert"`
- 오류 발생 시 focus 이동
- `aria-pressed` 선택 상태
- 결과를 색상만으로 구분하지 않음
- 텍스트 기반 거래 결과 제공

### Manual validation

2026-09-18 기준:

- Keyboard-only
  - 로그인 Tab 순서 정상
  - 전화번호 오류 시 해당 입력칸으로 focus 이동
  - PIN 오류 시 PIN 입력칸으로 focus 이동
  - 잔액조회 핵심 흐름 정상
  - 송금 수취인 선택 정상
- 200% browser zoom
  - Login / Accounts / Balance / Transfer / Review / Result에서 주요 UI 문제를 발견하지 못함

아직 수행하지 않은 항목:
- VoiceOver
- TalkBack
- 실제 시각장애 사용자 usability test

자세한 기록:
- [Accessibility Validation](./docs/ACCESSIBILITY_VALIDATION.md)

---

## Error & Recovery UX

주요 오류 상황에서는 사용자가 다음 행동을 알 수 있도록 안내합니다.

- 로그인 오류 → 관련 input 또는 오류 영역
- 계좌 / 잔액 조회 실패 → 오류 안내 + 재시도
- 송금 입력 오류 → 오류 영역
- 거래 결과 불확실 → 기존 요청 상태 확인
- 완료 / 차단 / 실패 상태를 텍스트로 구분

---

## Tech Stack

- Next.js
- React
- TypeScript
- Tailwind CSS
- Zustand
- Axios

---

## Local Run

```bash
npm ci
cp .env.example .env.local
npm run dev
```

기본 환경 변수:

```env
NEXT_PUBLIC_API_URL=http://localhost:8080
NEXT_PUBLIC_USE_MOCK=false
```

---

## Validation Commands

```bash
npm test
npm run typecheck
npm run lint
npm run build
```

---

## Backend Contract

- [Integration Spec](https://github.com/moonaneul/movi_backend/blob/main/docs/integration-spec.md)
- [AI API Contract](https://github.com/moonaneul/movi_backend/blob/main/docs/ai-api-contract.md)
