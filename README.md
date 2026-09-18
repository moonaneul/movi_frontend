# MOVI Frontend

MOVI의 **Next.js Frontend 저장소**입니다.

MOVI는 시각 중심 금융 UI 이용에 어려움이 있는 사용자를 고려해,  
음성으로 계좌 조회와 송금 과정을 보조하는 금융 서비스 프로토타입입니다.

이 Frontend의 핵심 역할은 **사용자가 거래 내용을 직접 확인하고, 음성을 사용할 수 없는 상황에서도 키보드·터치로 주요 금융 흐름을 완료할 수 있게 하는 것**입니다.

> 전체 프로젝트 설명과 개인 기여 범위는 [MOVI Overview](https://github.com/moonaneul/movi-overview)를 기준으로 합니다.  
> 이 저장소는 팀 Frontend 전체 코드를 포함하며, 저장소 전체를 문하늘 개인 단독 구현으로 표현하지 않습니다.

---

## 1. Frontend Responsibility

Frontend는 다음 역할을 담당합니다.

- 로그인 / PIN 로그인 화면
- 연결 계좌 조회 및 관리
- 잔액 조회
- 등록 수취인 기반 직접 송금
- 송금 review / explicit confirmation
- 거래 결과 표시
- 음성 입력 및 결과 안내 UI
- Backend API contract에 맞춘 상태 표현
- 오류 안내와 재시도 흐름
- 키보드·터치 기반 대체 조작
- 화면 텍스트와 기기 TTS를 통한 결과 안내

Frontend는 AI나 FDS 결과만으로 금융 상태를 확정하지 않습니다.

> **Frontend는 사용자의 입력과 확인을 담당하고, 금융 검증과 실행의 최종 책임은 Backend에 둡니다.**

---

## 2. Core Flow

### Direct transfer

```text
출금 계좌 선택
→ 등록된 받는 사람 선택
→ 금액 입력
→ Backend review
→ 검증된 거래 내용 확인
→ 사용자 명시적 확인
→ 거래 실행 / FDS
→ 결과 표시
```

송금 입력 화면에서 바로 거래를 실행하지 않고,  
Backend가 확인한 수취인·금액·출금 계좌를 review 화면에서 다시 제시한 뒤 사용자의 명시적 확인을 받습니다.

### Voice-assisted flow

```text
음성 입력
→ Frontend가 Backend로 전달
→ Backend가 Voice AI 호출
→ Backend 검증
→ 재질문 또는 거래 내용 확인
→ 사용자 확인
→ 결과 표시 / TTS
```

Frontend가 Voice AI / FDS 서버를 직접 호출하지 않습니다.

---

## 3. Accessibility Principles

MOVI에서는 음성을 유일한 입력 방식으로 두지 않았습니다.

- 음성 기능에 키보드·터치 대안 제공
- 음성 인식만으로 송금 완료 금지
- 수취인·금액·출금 계좌를 화면에서 다시 확인
- 주요 선택 버튼에 focus style 제공
- 오류 발생 시 `role="alert"` 또는 focus 이동 사용
- 선택 상태에 `aria-pressed` 사용
- form label과 input 연결
- 결과를 색상만으로 구분하지 않고 텍스트/기호 함께 사용
- 금융 결과를 화면 텍스트로도 제공

### Manual accessibility validation

2026-09-18 기준 수동 검증 결과:

- **Keyboard-only**
  - 로그인 Tab 순서 정상
  - 전화번호 오류 시 전화번호 입력칸으로 focus 이동
  - PIN 오류 시 PIN 입력칸으로 focus 이동
  - 잔액조회 핵심 흐름 정상
  - 송금 수취인 선택 정상
- **200% browser zoom**
  - Login / Accounts / Balance / Transfer / Review / Result 화면에서 핵심 UI 잘림·겹침 문제를 발견하지 못함

아직 수행하지 않은 항목:

- VoiceOver
- TalkBack
- 실제 시각장애 사용자 usability test

자세한 기록:
- [Accessibility Validation](./docs/ACCESSIBILITY_VALIDATION.md)

> 현재 결과를 “WCAG 준수” 또는 “시각장애 사용자 검증 완료”로 표현하지 않습니다.

---

## 4. Error & Recovery UX

현재 주요 금융 화면은 오류가 발생했을 때 다음 행동을 사용자에게 전달하도록 구성되어 있습니다.

예:

- 로그인 오류 → 관련 input 또는 오류 summary로 focus 이동
- 계좌/잔액 조회 실패 → 오류 안내 + 재시도
- 송금 입력 오류 → 오류 영역으로 focus 이동
- 송금 실행 결과가 불확실한 경우 → 새 송금을 반복하지 말고 기존 요청 상태를 확인하도록 안내
- 완료 / 차단 / 실패 상태를 텍스트로 구분

특히 송금에서는 **응답을 못 받았다 = 거래가 실행되지 않았다**고 가정하지 않습니다.

---

## 5. Frontend Structure

```text
src/
├── app/
│   ├── login/
│   ├── accounts/
│   ├── balance/
│   ├── transactions/
│   └── transfer/
├── components/
│   ├── common/
│   └── domain/
├── services/
├── store/
└── types/
```

주요 화면:

- `/login`
- `/login/pin`
- `/accounts`
- `/balance`
- `/transfer`
- `/transfer/review`
- `/transfer/result`

---

## 6. Tech Stack

- Next.js App Router
- React
- TypeScript
- Tailwind CSS
- Zustand
- Axios
- Node.js 22.6+

---

## 7. Local Run

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

Backend 없이 화면 단위 확인이 필요한 경우에만 Mock 모드를 사용합니다.

---

## 8. Validation Commands

```bash
npm test
npm run typecheck
npm run lint
npm run build
```

현재 자동 테스트는 API contract, 인증 상태, 계좌/잔액, 송금, 음성 contract, 오류 recovery 등의 단위 검증을 포함합니다.

정적 검사와 자동 테스트 통과만으로 실제 접근성 또는 실제 금융 연동 완료를 의미하지 않습니다.

---

## 9. Frontend–Backend Contract

Frontend는 임의의 금융 상태나 AI 결과를 자체적으로 확정하지 않고 Backend contract를 기준으로 화면을 구성합니다.

관련 문서:

- [Backend Integration Spec](https://github.com/moonaneul/movi_backend/blob/main/docs/integration-spec.md)
- [Backend AI API Contract](https://github.com/moonaneul/movi_backend/blob/main/docs/ai-api-contract.md)

대표 통합 기록:

- [Frontend PR #14 — Backend-to-Frontend Contract Audit](https://github.com/movi-ai-challenge/movi_frontend/pull/14)
- [Frontend PR #28 — Direct Transfer API Flow](https://github.com/movi-ai-challenge/movi_frontend/pull/28)

---

## 10. Portfolio Context

문하늘의 검증된 개인 기여는 다음 문서에서 별도로 정리합니다.

- [MOVI Overview](https://github.com/moonaneul/movi-overview)
- [My Contribution](https://github.com/moonaneul/movi-overview/blob/master/docs/contribution.md)
- [System Architecture](https://github.com/moonaneul/movi-overview/blob/master/docs/architecture.md)
- [Validation](https://github.com/moonaneul/movi-overview/blob/master/docs/validation.md)
- [Limitations & Evidence Boundaries](https://github.com/moonaneul/movi-overview/blob/master/docs/limitations.md)

구현 과정에는 Claude / Codex 등 AI 코딩 도구가 활용되었습니다.  
개인 기여는 코드 라인 수가 아니라 **문제 정의, 통합 계약, 서비스 흐름, 구현 검증 범위**를 기준으로 설명합니다.

---

## 11. Current Boundary

현재 다음을 완료된 사실로 주장하지 않습니다.

- 실제 시각장애 사용자 대상 사용성 검증
- VoiceOver / TalkBack 전체 흐름 검증
- 실제 금융기관 OpenBanking production 운영
- 실제 SMS production 운영
- Frontend 전체를 문하늘 개인 단독 구현
