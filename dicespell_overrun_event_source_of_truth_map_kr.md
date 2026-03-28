# DiceSpell `overrunEvent` source-of-truth 정렬표

## 빠른 안내

### 3줄 요약
- 이 문서는 `overrunEvent` 관련 문서를 `현재 런타임 설명`과 `다음 컷오버 목표`로 나눠 읽게 만드는 정렬표다.
- 프로그래머는 current/target 구분을 먼저, QA와 PM은 질문별 source-of-truth 열을 먼저 보면 된다.
- 핵심은 문서를 많이 읽는 것이 아니라, **지금 답을 어디서 가져와야 하는지 즉시 찾는 것**이다.

**한 줄 목표:** `overrunEvent` 문서 묶음에서 질문별 기준 문서를 빠르게 찾게 만든다.

### 왜 이 문서가 필요한가
- readability는 문장 다듬기만이 아니라, source-of-truth가 빨리 보이는 구조도 포함한다.
- 이 문서는 포털/노션에서 index처럼 쓰이면서 drift를 줄이는 역할을 맡는다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | current vs target 정렬표 | `integration workorder`, `implementation packet` |
| QA | 질문별 source-of-truth 열 | `acceptance matrix` |
| PM | owner별 읽기 순서 | `summary` |
| UI/아트 | target 문서 열 | `screen packet`, `content deck` |

---

## 문서 목적

`overrunEvent` 관련 handoff/spec/screen/cutsheet/acceptance/integration workorder는 이미 충분히 production-ready하다.
그런데 실제 구현자가 착수할 때 여전히 한 번 더 멈칫할 수 있는 지점이 남아 있었다.

> 현재 저장소의 `역기획서`와 `코드 구조 정리`는 아직 **현재 런타임(즉시 적용 버전)** 을 설명하고 있고, 새 `overrunEvent` 문서 묶음은 **다음 컷오버 목표(확인 후 적용 버전)** 를 설명한다.

이 둘은 서로 모순이라기보다 **시점이 다른 문서**인데, 그 구분이 한 장으로 고정돼 있지 않으면 구현자가 다시 아래 질문을 문서 여러 장에서 짜맞추게 된다.

- 지금 실제 코드가 무엇을 하고 있나?
- 다음 구현이 무엇을 바꿔야 하나?
- 어느 문서를 `현재 상태 설명서`로 읽고, 어느 문서를 `목표 계약서`로 읽어야 하나?
- 프로그래머/UI/아트/QA는 각각 어느 문장을 기준으로 납품을 끝냈다고 말해야 하나?

이 문서는 그 마지막 충돌만 정리하는 **baseline 정렬표**다.
새 기능을 더 설계하는 문서가 아니라, 이미 있는 문서 묶음이 서로 다른 시점을 다루고 있다는 사실을 명시하고, 역할별로 무엇을 source of truth로 삼아야 하는지 고정한다.

---

## 1. 왜 지금 이 문서가 필요한가

현재 저장소에는 아래 두 종류의 문서가 공존한다.

### A. 현재 런타임 설명 문서

- `docs/dicespell_current_build_reverse_spec_kr.md`
- `docs/dicespell_current_build_code_map_kr.md`

이 문서들은 **지금 실제 코드가 어떻게 동작하는가**를 설명한다.
즉 `continueOverrun()`가 아직 선택 보상을 즉시 적용하고, `phase` 체계에 `overrunEvent`가 없으며, 엔딩 화면이 사실상 마지막 선택 화면이라는 현재 상태를 기술한다.

### B. 다음 컷오버 목표 문서

- `docs/dicespell_overrun_event_handoff_kr.md`
- `docs/dicespell_overrun_event_implementation_packet_kr.md`
- `docs/dicespell_overrun_event_screen_packet_kr.md`
- `docs/dicespell_overrun_event_production_cutsheet_kr.md`
- `docs/dicespell_overrun_event_acceptance_matrix_kr.md`
- `docs/dicespell_overrun_event_integration_workorder_kr.md`

이 문서들은 **다음 구현이 끝났을 때 무엇이 참이어야 하는가**를 설명한다.
즉 `continue-overrun -> overrunEvent 진입 -> confirm 시점 resolve -> recovery/fallback` 구조를 목표 계약으로 고정한다.

문제는 둘 다 맞지만, **같은 질문에 답하는 문서가 아니었다**는 점이다.
이번 문서는 그 구분을 강하게 못 박는다.

---

## 2. 한 줄 원칙

### 2.1 현재 동작을 묻는 문장에는 현재 런타임 문서를 본다

- 지금 `continueOverrun()`가 무엇을 하는가?
- 지금 `phase` 종류가 무엇인가?
- 지금 save 데이터는 어떤 필드를 가지는가?

이 질문은 `reverse spec` / `code map` / 실제 코드가 답한다.

### 2.2 다음 구현 완료선을 묻는 문장에는 `overrunEvent` 문서 묶음을 본다

- 다음 구현에서 `continueOverrun()`는 무엇이 되어야 하는가?
- `overrunEvent` snapshot은 무엇을 담아야 하는가?
- 어떤 recovery smoke까지 닫혀야 완료인가?

이 질문은 `handoff` / `implementation packet` / `screen packet` / `cutsheet` / `acceptance matrix` / `integration workorder`가 답한다.

### 2.3 둘이 충돌하면, 그것은 문서 오류가 아니라 `현재`와 `목표`의 구분 문제로 먼저 해석한다

즉 아래 문장은 동시에 참일 수 있다.

- `reverse spec`: 현재는 엔딩에서 고른 보관함 전리품이 다음 오버런 진입 직전에 즉시 적용된다.
- `integration workorder`: 다음 컷오버 후에는 `continue-overrun` 직후 즉시 적용되면 안 되고, `overrunEvent` confirm에서만 1회 적용돼야 한다.

이번 문서의 역할은 바로 이 문장을 **모순이 아니라 시점 차이**로 읽게 만드는 것이다.

---

## 3. 질문별 source-of-truth 표

| 질문 | 먼저 볼 문서 | 왜 그 문서인가 | 다른 문서가 하는 역할 |
| --- | --- | --- | --- |
| 지금 실제 코드에서 `continueOverrun()`는 무엇을 하나 | `docs/dicespell_current_build_code_map_kr.md` + 실제 `src/game-engine.js` | 현재 실행 경로 설명 | `integration workorder`는 다음에 어떻게 자를지 설명 |
| 지금 세이브/상태 스키마에 `overrunEvent`가 있나 | `docs/dicespell_current_build_reverse_spec_kr.md` | 현재 저장 상태 기준 | `handoff`는 목표 스키마 제안 |
| 다음 컷오버에서 상태 책임을 어떻게 나누나 | `docs/dicespell_overrun_event_integration_workorder_kr.md` | source-of-truth 분리와 순서가 가장 구체적 | `reverse spec`은 현재 상태 설명 |
| UI가 어떤 화면을 그려야 하나 | `docs/dicespell_overrun_event_screen_packet_kr.md` | 화면/카피/배지/CTA 계약이 가장 자세함 | 현재 `renderEnding()` 설명은 구버전 문맥 |
| 프로그래머가 어떤 함수/액션/복구를 닫아야 하나 | `docs/dicespell_overrun_event_implementation_packet_kr.md` + `docs/dicespell_overrun_event_integration_workorder_kr.md` | patch surface + 컷오버 순서 | `cutsheet`는 owner별 묶음 정리 |
| QA가 언제 완료라고 말할 수 있나 | `docs/dicespell_overrun_event_acceptance_matrix_kr.md` | acceptance/recovery 종료선 명시 | `production cutsheet`는 역할별 납품 형식 |
| 역할별로 무엇을 넘기면 되는가 | `docs/dicespell_overrun_event_production_cutsheet_kr.md` | owner별 산출물/DoD가 가장 명확 | `screen packet`은 UI/아트 세부 계약 |

---

## 4. 현재 상태 vs 목표 상태 정렬표

| 항목 | 현재 런타임 기준 | 목표 컷오버 기준 | 구현자가 읽는 법 |
| --- | --- | --- | --- |
| `continueOverrun()` 의미 | 선택 보상 적용 + 오버런 준비 + 다음 페이지 진입까지 한 번에 처리 | `overrunEvent` 진입 wrapper | 현재 코드를 이해할 때는 왼쪽, 수정 목표는 오른쪽 |
| 오버런 보상 적용 시점 | `continue-overrun` 직후 | `confirm-overrun-event` 클릭 시점 | 둘을 섞지 말 것 |
| `phase` 체계 | `library / battle / shop / reward / ending` | 여기에 `overrunEvent` 추가 | reverse spec은 현재, handoff는 목표 |
| 엔딩 화면 문맥 | 선택 + 진행 | 선택 완료 후 이벤트 진입 직전 화면 | UI owner는 screen packet 기준으로 이동 |
| save/load 복구선 | 현재 엔딩 기준 복구 | `overrunEvent` snapshot/none fallback/resolved guard까지 포함 | QA는 acceptance matrix를 기준으로 확장 |
| smoke 기준 | 즉시 적용 구조 확인 | enter / confirm / recovery 분리 | 기존 smoke를 단순 보존하지 않음 |

---

## 5. 역할별 읽기 우선순위

## 5.1 프로그래머 오너

### 먼저 볼 것

1. `docs/dicespell_overrun_event_integration_workorder_kr.md`
2. `docs/dicespell_overrun_event_implementation_packet_kr.md`
3. 실제 `dice-spell-standalone/src/game-engine.js`
4. 실제 `dice-spell-standalone/src/app.js`
5. `docs/dicespell_current_build_code_map_kr.md`

### 프로그래머에게 중요한 해석

- `current_build_*` 문서는 **현재 어떤 함수가 어디 있는지** 찾는 데 쓰고,
- `overrunEvent_*` 문서는 **그 함수를 어떻게 잘라야 하는지** 결정하는 데 쓴다.

즉 프로그래머는 `reverse spec`을 목표 계약서로 읽으면 안 된다.
그 문서는 어디를 뜯어야 하는지 알려 주는 baseline이다.

### 프로그래머 handoff 메모

- `continueOverrun()`의 현재 책임 범위는 baseline 문서가 맞다.
- 그러나 납품 완료선은 `enter wrapper + snapshot/resolve/normalize 분리 + confirm action + recovery smoke`다.
- `ending.overrunDraft`를 최종 source of truth로 오래 유지하면 안 된다.
- 화면에서 reward를 다시 lookup하는 편법은 금지다.

---

## 5.2 UI 오너

### 먼저 볼 것

1. `docs/dicespell_overrun_event_screen_packet_kr.md`
2. `docs/dicespell_overrun_event_production_cutsheet_kr.md`
3. 현재 `src/app.js`의 `renderEnding(summary)`

### UI 오너에게 중요한 해석

현재 `renderEnding()`와 reverse spec에 적힌 카피는 **현행 엔딩 UI 설명**일 뿐, 목표 화면 계약이 아니다.
따라서 UI owner는 `현재 앱이 왜 이렇게 보이는지`를 이해할 때만 이를 참고하고, 실제 납품은 반드시 screen packet 기준으로 맞춘다.

### UI handoff 메모

- `ending reward grid`와 `overrunEvent`는 같은 계층감이면 안 된다.
- `themeTrophy / enemyTrophy / none`는 카피와 배지와 카드 밀도에서 모두 달라야 한다.
- `none`도 임시 경고문이 아니라 정상 장면처럼 보이게 유지한다.

---

## 5.3 아트 오너

### 먼저 볼 것

1. `docs/dicespell_overrun_event_screen_packet_kr.md`
2. `docs/dicespell_overrun_event_production_cutsheet_kr.md`

### 아트 오너에게 중요한 해석

아트는 현재 엔딩 카드 외형을 그대로 polish하는 작업이 아니다.
이번 컷오버의 목표는 **선택 화면과 이벤트 확인 화면의 계층 분리**다.
따라서 헤더 패널, 배지 3종, focus backplate, accent 4종은 `overrunEvent`를 별도 장면으로 읽히게 만드는 방향으로 넘겨야 한다.

### 아트 handoff 메모

- `none` 상태도 빈 화면처럼 보이면 안 된다.
- 텍스트를 읽지 않아도 `theme / enemy / none` 구분이 남아야 한다.
- reward-card display-only 재사용은 가능하지만, `선택 UI`처럼 보이면 실패다.

---

## 5.4 QA 오너

### 먼저 볼 것

1. `docs/dicespell_overrun_event_acceptance_matrix_kr.md`
2. `docs/dicespell_overrun_event_production_cutsheet_kr.md`
3. 현재 smoke와 실제 런타임

### QA 오너에게 중요한 해석

현재 런타임이 즉시 적용이라고 해서, 그것을 회귀 기준선으로 고정하면 안 된다.
이번 변경의 테스트 목적은 **옛 동작을 지키는 것**이 아니라 **새 계약으로 안전하게 갈아타는 것**이다.

### QA handoff 메모

- `continue-overrun -> 즉시 적용`은 이제 pass 조건이 아니라 구버전 baseline이다.
- pass 조건은 `enter -> confirm -> 1회 resolve -> fallback survivability`다.
- `resolved=true reload`, invalid reward, missing snapshot, `none`은 옵션이 아니라 필수 케이스다.

---

## 6. 이번 슬롯 기준 gap analysis

이번 시점의 최고가치 문서 공백은 새 기능 아이디어가 아니었다.
실제 공백은 아래 하나였다.

> `현재 상태 설명서`와 `다음 목표 계약서`가 모두 맞는데, 서로 다른 시점을 다룬다는 사실이 한 장으로 고정돼 있지 않았다.

이 공백이 남아 있으면 구현자는 다시 아래 실수를 하기 쉽다.

1. reverse spec의 현재 동작을 목표 사양으로 오해한다.
2. code map의 현재 함수 설명을 그대로 유지하려고 한다.
3. screen packet의 새 계층 설계를 기존 ending polish로 축소 해석한다.
4. QA가 기존 즉시 적용 smoke를 회귀 기준선으로 잘못 잡는다.

이번 문서는 그 해석 충돌을 막기 위한 정렬표다.
즉 구현 범위를 더 키우는 문서가 아니라, **이미 있는 문서들이 서로 다른 질문에 답한다는 사실을 고정하는 문서**다.

---

## 7. 지금 당장 넘겨야 할 owner별 납품 메모

### 프로그래머에게 넘길 것

- `current_build_code_map`은 현재 함수 위치와 현재 책임 확인용 baseline이다.
- 실제 구현 계약은 `implementation packet + integration workorder` 기준이다.
- `continueOverrun()` 즉시 적용 유지 시 이번 작업은 미완료다.

### UI 오너에게 넘길 것

- 현재 엔딩 카피는 구버전 문맥이다.
- 실제 납품은 `screen packet`의 중앙 카드 1장 + CTA 1개 + footnote 1줄 구조다.
- `선택 화면`과 `확인 화면`은 시각 계층이 분리돼야 한다.

### 아트 오너에게 넘길 것

- 헤더 패널 / 배지 3종 / focus backplate / accent 4종을 `overrunEvent` 독립 장면 기준으로 만든다.
- `none`도 정상 장면처럼 읽혀야 한다.

### QA에게 넘길 것

- 구버전 즉시 적용은 기준선이 아니라 교체 대상이다.
- acceptance 12종 + recovery 6종이 pass 조건이다.

---

## 8. 리스크 & 후속 과제

### 리스크 1. baseline 문서와 목표 문서를 다시 같은 층위로 읽을 위험

- 영향: 구현자가 현재 런타임 설명을 목표 사양으로 오독해 컷오버를 반쯤만 수행할 수 있다.
- 대응: 이번 문서에서 질문별 source-of-truth를 고정한다.

### 리스크 2. UI/QA가 기존 ending/즉시 적용 문맥을 회귀 기준선으로 붙잡을 위험

- 영향: 화면은 바뀌었는데 검수 기준은 구버전으로 남아 실제 완료선이 흔들린다.
- 대응: screen packet과 acceptance matrix를 역할별 최종 계약서로 못 박는다.

### 리스크 3. reverse spec/code map이 최신 목표를 다 설명해야 한다는 기대가 생길 위험

- 영향: 현재 상태 설명 문서에 목표 설계를 과도하게 섞어 문서 역할이 흐려진다.
- 대응: reverse spec/code map은 baseline 설명을 유지하되, 이 정렬표로 `현재 vs 목표` 해석 규칙을 분리한다.

### 후속 과제

이 문서 다음 우선순위는 다시 문서를 넓히는 일이 아니다.

1. `continueOverrun()` enter화
2. `overrunEvent` snapshot/resolve/normalize 실제 구현
3. `renderCenter()` / `confirm-overrun-event` 연결
4. acceptance 12종 + recovery 6종 smoke 고정

즉 이제 남은 일은 `문서를 더 만드는 것`이 아니라, 문서가 가리키는 실제 컷오버를 코드에 반영하는 것이다.

---

## 9. 한 줄 결론

이번에 정리한 공백은 `overrunEvent`의 새 아이디어가 아니라, **현재 런타임 설명 문서와 다음 목표 계약 문서를 어떤 질문에 어떻게 써야 하는가**였다. 이 문서는 그 해석 충돌을 막아, 프로그래머/UI/아트/QA가 다시 구버전 즉시 적용 문맥으로 되돌아가지 않게 만드는 source-of-truth 정렬표다.
