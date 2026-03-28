# DiceSpell `overrunEvent` 컷오버 실행 런웨이 패킷

## 3줄 요약
- 이 문서는 이미 닫힌 `A-1~A-4` 문서를 **실제 구현 턴에서 바로 집행하는 마지막 실행 런웨이**로 다시 묶는다.
- 핵심은 새 설계가 아니라, **이번 커밋에서 누가 무엇을 어디까지 닫고 어떤 증거를 남기면 끝인가**를 한 장으로 고정하는 것이다.
- 프로그래머는 `A-1 → A-2 → A-3 → A-4` 실행 순서와 파일 경계를, UI/아트는 화면 읽힘과 경계 유지 포인트를, QA는 slice별 종료 증거를 먼저 보면 된다.

## 한 줄 목표
`overrunEvent` 문서 묶음을 실제 코드 컷오버용 **실행 체크리스트 + owner별 handoff + 증거 기준**으로 번역한다.

## 왜 이 문서를 먼저 봐야 하는가
지금 DiceSpell의 `overrunEvent` 축은 문서 부족 상태가 아니다. 오히려 반대다. 방향 문서, screen packet, content deck, delivery packet, A-1~A-4 commit-level packet까지 이미 충분하다. 문제는 실제 구현 직전엔 아래 질문이 다시 살아난다는 점이다.

- `이번 턴은 정확히 어느 packet까지 닫는 게 맞지?`
- `어떤 파일을 같이 묶고 어떤 파일은 다음 커밋으로 미뤄야 하지?`
- `프로그래머/UI/아트/QA가 같은 완료선을 어떻게 확인하지?`
- `문서가 많아서 오히려 범위가 다시 부풀지는 않나?`

이 문서는 그 마지막 운영 공백만 다룬다. 즉 새 기획을 추가하지 않고, 이미 닫힌 문서들을 실제 구현 가능한 **런웨이 순서**로 압축한다.

---

# 1. 누가 어디부터 읽어야 하나

## 프로그래머
1. 이 문서의 `2. 실행 순서`와 `3. 파일 경계`
2. `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`
3. `docs/dicespell_overrun_a2_snapshot_packet_kr.md`
4. `docs/dicespell_overrun_a3_ui_packet_kr.md`
5. `docs/dicespell_overrun_a4_resolve_packet_kr.md`
6. `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`

## UI 오너
1. 이 문서의 `4. 화면 읽힘 유지 규칙`
2. `docs/dicespell_overrun_a3_ui_packet_kr.md`
3. `docs/dicespell_ending_overrun_boundary_packet_kr.md`
4. `docs/dicespell_overrun_event_content_deck_kr.md`

## 아트 오너
1. 이 문서의 `4. 화면 읽힘 유지 규칙`
2. `docs/dicespell_overrun_event_screen_packet_kr.md`
3. `docs/dicespell_overrun_a3_ui_packet_kr.md`
4. `docs/dicespell_overrun_event_content_deck_kr.md`

## QA 오너
1. 이 문서의 `5. 종료 증거와 검수 기준`
2. `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
3. `docs/dicespell_overrun_a4_resolve_packet_kr.md`
4. `docs/dicespell_overrun_onboarding_contract_examples_kr.md`

---

# 2. 실행 순서

## 3줄 요약
- `overrunEvent` 실구현은 **A-1 enter 분리 → A-2 snapshot/normalize → A-3 중앙 카드 UI → A-4 confirm resolve** 순서만 허용한다.
- 각 단계는 `문서 읽기 -> 파일 수정 -> 증거 고정`이 같은 slice 안에서 같이 닫혀야 한다.
- A-4가 닫히기 전에는 onboarding B-1~B-4를 열지 않는다.

## 한 줄 목표
`A-1~A-4`를 다시 합치거나 생략하지 말고, commit-level handoff 그대로 실행 순서로 고정한다.

## 왜 이 순서가 중요한가
`overrunEvent`는 장면 카드처럼 보이지만, 실제로는 **상태 전이 + 저장 복구 + UI 읽힘 + 1회 적용 보장**이 엮인 경계 시스템이다. 그래서 순서를 어기면 거의 항상 drift가 생긴다.

- A-1 없이 A-3로 가면 화면만 생기고 즉시 적용 baseline이 남는다.
- A-2 없이 A-3로 가면 save/load 뒤 branch identity가 흐려진다.
- A-3 없이 A-4로 가면 버튼은 작동해도 장면 카드 읽힘이 흔들린다.
- A-4 전에 onboarding을 열면 잘못된 baseline 위에 primer가 올라간다.

## A-1. 엔진 진입 분리
### 목표
`continueOverrun()`가 더 이상 즉시 보상 적용 함수가 아니라 `overrunEvent` 진입 wrapper로 읽히게 만든다.

### 이번 단계에서 닫아야 하는 것
- `phase = 'overrunEvent'` 상태 진입
- `none` fallback snapshot 생성
- confirm 이전 effect 미적용 보장
- A1-S1~S4 smoke 또는 동등 증거

### 이번 단계에서 일부러 하지 않는 것
- reward 1회 적용
- 중앙 카드 완성 UI
- full recovery migration

## A-2. snapshot / normalize
### 목표
render가 문자열을 다시 추론하지 않도록 canonical snapshot이 branch identity를 직접 들고 있게 만든다.

### 이번 단계에서 닫아야 하는 것
- `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet` 등 canonical field set
- `normalizeOverrunEventState()` 또는 동등 helper
- invalid/missing/legacy -> `none` fallback 수렴
- A2-S1~S6 recovery smoke 또는 동등 증거

### 이번 단계에서 일부러 하지 않는 것
- shell/styling 재설계
- CTA resolve 구현

## A-3. 중앙 카드 UI
### 목표
`overrunEvent`를 결과표가 아니라 다음 권 직전의 장면 카드로 읽히게 만든다.

### 이번 단계에서 닫아야 하는 것
- `badge → header → flavor → reward summary → CTA → footnote` 위계
- ending과 다른 shell/tone 경계
- snapshot display-only 소비
- `none` branch도 빈 상태가 아닌 정상 장면 밀도 유지
- A3-S1~S5 sanity 또는 동등 증거

### 이번 단계에서 일부러 하지 않는 것
- effect apply 로직 변경
- onboarding primer 주입

## A-4. confirm resolve + full smoke
### 목표
confirm CTA 시점에만 reward가 정확히 한 번 적용되고, 세그먼트가 다음 상태로 전진하게 만든다.

### 이번 단계에서 닫아야 하는 것
- `resolveOverrunEvent(state)` 또는 동등 함수
- `resolved` guard
- `none` 무보상 진행 보장
- invalid/missing -> `none` fallback
- old immediate baseline 제거
- A4-S4~S12 + recovery 세트 또는 동등 증거

### 이번 단계가 닫히기 전엔 하지 않는 것
- onboarding B-1~B-4 실구현
- 새 reward branch 추가
- unrelated UX polish

---

# 3. 파일 경계

## 3줄 요약
- `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs`가 실질적인 주 파일 경계다.
- `src/styles.css`는 A-3에서만 적극 수정하고, A-4에서는 flicker/stale-copy 보정 정도만 다룬다.
- 한 slice에서 파일을 많이 건드리는 것보다, **그 slice의 상태 전이와 검증을 같이 닫는 편**이 더 중요하다.

## 한 줄 목표
각 A-step이 어떤 파일을 실제로 건드려야 하고, 어디서 멈춰야 하는지 명확히 한다.

| 단계 | 필수 파일 | 선택 파일 | 이번 단계 핵심 |
| --- | --- | --- | --- |
| A-1 | `src/game-engine.js`, `scripts/smoke-test.mjs` | `src/app.js` placeholder | enter 분리 + 미적용 보장 |
| A-2 | `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs` | 없음 | canonical snapshot + normalize |
| A-3 | `src/app.js`, `src/styles.css` | `scripts/smoke-test.mjs` sanity | 중앙 카드 1장 UI |
| A-4 | `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs` | `src/styles.css` flicker fix | confirm resolve + full smoke |

## 파일별 역할 메모

### `src/game-engine.js`
- 상태 전이와 effect 적용의 단일 source여야 한다.
- app.js가 branch별 수치 해석을 직접 들고 가면 실패다.

### `src/app.js`
- snapshot을 display-only로 소비하고 action wiring을 담당한다.
- reward 계산/branch 판정까지 직접 맡지 않는다.

### `src/styles.css`
- A-3 카드 shell을 안정시키는 용도다.
- A-4에서는 UI 보정만 허용하고 구조 재설계는 금지한다.

### `scripts/smoke-test.mjs`
- 한 단계가 닫혔다고 말하려면 여기에 최소한 동등한 증거가 남아야 한다.
- enter smoke 없이 A-1 완료 선언 금지, recovery smoke 없이 A-4 완료 선언 금지.

---

# 4. 화면 읽힘 유지 규칙

## 3줄 요약
- `ending`은 결과/정산/CTA 의미만 말하고, `overrunEvent`는 flavor와 장면 전환 감정선을 맡는다.
- `overrunEvent`는 중앙 카드 1장이고, onboarding은 guidance layer다. 둘을 같은 UI 밀도로 만들면 안 된다.
- `none` branch도 placeholder가 아니라 정상 장면이어야 한다.

## 한 줄 목표
실구현 중에 가장 쉽게 무너지는 `읽힘의 경계`를 owner 공통 규칙으로 유지한다.

## 왜 이게 중요한가
실구현 단계에선 기능보다도 `이미 있는 컴포넌트를 재활용하고 싶다`는 유혹이 강하다. 그런데 `overrunEvent`는 reward card나 ending shell을 조금씩 빌려오더라도, 최종 읽힘은 분명히 달라야 한다.

### 유지해야 하는 경계
- `ending` = 결과 해석 / 정산 / CTA 의미
- `overrunEvent` = 다음 권 직전 장면 / flavor / reward summary / confirm CTA
- `onboarding` = 흐름을 막지 않는 hero/inline/badge guidance layer

### 금지 패턴
- ending 본문이 `themeTrophy` / `enemyTrophy` / `none` flavor를 미리 소비하는 것
- onboarding을 `overrunEvent`처럼 큰 카드 phase로 키우는 것
- `none`을 빈 상태, 임시 state, placeholder처럼 보이게 만드는 것
- reward card 구성요소를 그대로 붙여 `overrunEvent`가 선택 화면처럼 보이게 만드는 것

### UI/아트 handoff 핵심
- badge는 branch identity를, header/flavor는 장면 이유를, CTA는 다음 동작의 의미를 담당한다.
- accent/backplate는 정보 전달이 아니라 읽힘의 경계를 돕는 수준에서만 쓴다.
- `none` branch는 톤을 비우는 게 아니라, **위협은 없었지만 다음 권으로 넘어가는 장면**이라는 중립 밀도를 유지해야 한다.

---

# 5. 종료 증거와 검수 기준

## 3줄 요약
- 각 단계는 `작동했다`가 아니라 **문서가 말한 종료 조건이 증거로 남았는가**로 닫는다.
- QA는 문자열 미세 차이보다 `contentKey`, `CTA semantics`, `recovery`, `중복 적용 방지`를 먼저 본다.
- A-4 완료 기준은 결국 `장면 -> 적용 -> 다음 세그먼트`가 한 번만 성립하는가다.

## 한 줄 목표
프로그래머/UI/아트/QA가 같은 종료선을 보게 만든다.

## 단계별 최소 증거

| 단계 | 최소 증거 |
| --- | --- |
| A-1 | `phase='overrunEvent'` 진입 + confirm 전 effect 미적용 |
| A-2 | reload/invalid/missing 케이스가 canonical snapshot 또는 `none`으로 수렴 |
| A-3 | 중앙 카드 1장 캡처 + ending과 다른 shell 읽힘 |
| A-4 | reward 1회 적용, `resolved` 재실행 금지, S4~S12 + recovery |

## QA가 우선 확인할 것
1. confirm 전 effect가 들어가지 않았는가
2. confirm 후 같은 effect가 다시 들어가지 않는가
3. invalid/missing 상태도 런을 살리는가
4. `none` branch도 정상 진행하는가
5. ending과 overrunEvent가 같은 말을 하지 않는가

## owner별 완료선

### 프로그래머
- 동작 + smoke 증거까지 남겨야 완료다.
- 코드만 있고 검증 기준이 없으면 미완료다.

### UI
- 카드가 보이는 것보다 `의미가 올바르게 읽히는가`가 우선이다.
- ending/result screen과 혼동되면 미완료다.

### 아트
- 자산 양보다 branch별 tone 경계 유지가 우선이다.
- `none` neutral shell이 깨지면 미완료다.

### QA
- happy path만 통과해도 완료가 아니다.
- resolved reload guard, invalid/missing fallback, stale copy 제거까지 확인해야 완료다.

---

# 6. 리스크 & 후속 과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| 문서가 충분한데도 구현자가 이번 slice 범위를 다시 임의로 재조합할 위험 | A-step 경계 붕괴, commit 부풀기 | 이 문서를 `이번 턴 런웨이 선언` 문서로 사용 |
| A-3와 A-4를 한 번에 섞는 위험 | UI/상태/검증 drift | 카드 읽힘과 resolve를 따로 닫기 |
| `resolved` guard가 늦게 붙는 위험 | reload 후 dup reward | A-4 전용 종료 증거 필수화 |
| `none`이 계속 placeholder처럼 다뤄질 위험 | QA/UX 오판 | neutral branch를 정상 장면으로 강제 |
| A-4 전에 onboarding을 여는 위험 | 잘못된 baseline 위 primer 구현 | B-1~B-4는 A-4 뒤로 고정 |

## immediate next actions
1. 다음 실제 구현 턴은 이 문서를 열고 **이번 턴은 A-1만 닫는다 / A-2까지 닫는다**처럼 먼저 범위를 선언한다.
2. A-1~A-4는 절대 한 턴에 다 닫으려 하지 않는다. 최소 2개 이상의 bounded commit으로 나눈다.
3. tracker와 run log에는 단순히 `overrunEvent 구현`이 아니라 **이번 턴이 어느 runway step까지 닫혔는지**를 같은 용어로 기록한다.

---

## 한 줄 결론
이제 `overrunEvent` 문서 공백의 핵심은 설계 부족이 아니라 **마지막 실행 정렬 부족**이다. 이 문서는 그 정렬을 위한 마지막 source handoff다.
