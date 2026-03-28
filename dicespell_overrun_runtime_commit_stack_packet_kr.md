# DiceSpell `overrunEvent` 런타임 커밋 스택 패킷

## 3줄 요약
- 이 문서는 `first runtime slice`와 `second runtime slice`를 **실제 2커밋 스택**으로 묶어 보는 source handoff다.
- 목표는 프로그래머/UI/아트/QA/PM이 `이번엔 1차 커밋에서 어디까지 닫고, 2차 커밋에서 무엇으로 마감하는가`를 더 이상 delivery/review/slice 문서 여러 장에서 다시 합치지 않게 만드는 것이다.
- 즉 이 문서는 새 설계 문서가 아니라, 이미 닫힌 A-track 문서를 **실행 가능한 commit stack**으로 재압축하는 운영용 source-of-truth다.

## 한 줄 목표
`overrunEvent` A-track을 **Commit 1 = enter/snapshot floor**, **Commit 2 = scene/resolve closing**의 두 단계 runtime stack으로 고정해 half-cutover와 역할별 해석 차이를 줄인다.

## 왜 이 문서가 필요한가
지금 DiceSpell에는 이미 아래 문서가 있다.

- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_first_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`
- `docs/dicespell_overrun_a2_snapshot_packet_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_overrun_a4_resolve_packet_kr.md`

문제는 문서가 부족해서가 아니라, 실제 구현 직전에는 오히려 아래 운영 공백이 남는다는 점이다.

1. 프로그래머는 `first runtime delivery`와 `second runtime slice`를 따로 읽을 수는 있지만, **둘을 실제 어떤 2커밋 스택으로 묶어야 하는지** 다시 머릿속에서 조합해야 한다.
2. UI/아트는 `A-3`와 `A-4`의 closing scope를 이해해도, **Commit 1에서는 무엇을 일부러 하지 말아야 하는지** 한 장으로 보기 어렵다.
3. QA는 `첫 슬라이스 evidence`와 `closing slice evidence`를 다 알고 있어도, **review 순서와 fail gate를 어떤 스택 기준으로 남길지** 다시 엮어야 한다.
4. PM/기획은 `다음 구현자는 정확히 두 번의 bounded commit으로 끝낸다`는 문장을 tracker/run log/리뷰 코멘트에서 일관되게 쓰기 어렵다.

즉 지금 필요한 것은 새 phase 정의가 아니라, **이미 닫힌 문서를 실제 runtime commit stack으로 다시 압축하는 일**이다.

---

## 누가 먼저 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 읽을 문서 | 이번 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `2. 커밋 스택 개요`, `3. Commit 1`, `4. Commit 2` | `first runtime delivery packet`, `second runtime slice packet` | `overrunEvent`는 2커밋으로 닫고, 중간 상태와 최종 상태를 같은 용어로 남긴다 |
| UI | `4. Commit 2`, `6. 역할별 handoff` | `a3_ui_packet`, `ending_overrun_boundary_packet` | Commit 1에서는 shell을 건드리지 않고, Commit 2에서만 장면 카드 집중도를 닫는다 |
| 아트 | `4. Commit 2`, `6. 역할별 handoff` | `event_screen_packet`, `event_content_deck` | token/accent drift 없이 closing visual을 Commit 2에만 마감한다 |
| QA | `3. Commit 1`, `4. Commit 2`, `5. evidence bundle` | `first runtime review packet`, `smoke migration packet` | 첫 커밋은 non-change line, 두 번째 커밋은 full closing line으로 검수한다 |
| PM/기획 | `7. 리스크`, `8. 즉시 다음 액션` | `delivery summary`, `second runtime slice summary` | 다음 implementer에게 `두 커밋만 허용`이라는 문장을 그대로 넘긴다 |

---

## 2. 커밋 스택 개요

### 3줄 요약
- 이 스택은 `A-1/A-2`와 `A-3/A-4`를 **2개의 서로 다른 종료선**으로 나눈다.
- Commit 1은 `들어가기 + 저장/복구 바닥`이고, Commit 2는 `보여 주기 + 실제 적용`이다.
- 둘을 섞으면 half-cutover가 생기고, 둘을 너무 잘게 쪼개도 검수선이 무너진다.

### 한 줄 목표
`overrunEvent`를 **2번의 bounded runtime commit**으로 끝내도록 강제한다.

| 구분 | Commit 1: First Runtime Floor | Commit 2: Closing Slice |
| --- | --- | --- |
| 단계 묶음 | `A-1 enter 분리 + A-2 canonical snapshot floor` | `A-3 scene card UI + A-4 confirm resolve` |
| 핵심 결과 | `phase='overrunEvent'` 진입, canonical field set 저장, normalize/recovery smoke green | 단일 장면 카드 표시, confirm 1회 적용, old immediate baseline 제거 |
| 반드시 아직 하지 말 것 | reward apply, `currentPage` 전진, `pagePlan` 변경, `segmentTrophyTemplateIds` cleanup, onboarding surface 수정 | onboarding B-track 착수, 전역 카드 시스템 리디자인, 새 reward 타입 추가 |
| 리뷰 질문 | `무엇이 바뀌었고 무엇이 아직 안 바뀌었는가?` | `이제 장면/적용/다음 세그먼트가 정확히 한 번 성립하는가?` |
| 완료 문장 | `이번 커밋은 enter+snapshot floor까지만 닫았다.` | `이번 커밋은 scene+resolve closing까지 닫았다.` |

### 하드 게이트
- Commit 2는 **Commit 1 green 이후에만** 열린다.
- Commit 1 evidence가 없으면 Commit 2 smoke green도 runtime-ready로 취급하지 않는다.
- onboarding B-track은 **Commit 2 pass 이후에만** 열린다.

---

## 3. Commit 1 — First Runtime Floor

### 3줄 요약
- Commit 1의 역할은 `ending -> overrunEvent` 진입과 canonical snapshot floor를 닫는 것이다.
- 이 단계에서는 일부러 reward를 적용하지 않고, page 관련 상태도 아직 바꾸지 않는다.
- 즉 Commit 1은 `중간 상태를 안전하게 저장/복구할 수 있는가`만 검수한다.

### 한 줄 목표
`continueOverrun()`를 즉시 적용 함수에서 `overrunEvent` 진입 wrapper로 바꾸고, recovery 가능한 canonical snapshot을 남긴다.

### 파일 경계
| 파일 | Commit 1에서 닫을 것 | Commit 1에서 금지 |
| --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `continueOverrun()` enter wrapper화, canonical snapshot/build helper, normalize/recovery floor | reward apply, resolve cleanup |
| `dice-spell-standalone/src/app.js` | `phase='overrunEvent'` 분기 진입 최소 wiring | 장면 카드 shell/tone 완성, confirm resolve UX |
| `dice-spell-standalone/scripts/smoke-test.mjs` | enter/recovery/non-change smoke | full closing smoke, old immediate baseline 완전 제거 |

### canonical field set
Commit 1에서 snapshot이 직접 들고 있어야 하는 필드:

- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `flavorText`
- `confirmLabel`
- `resolved`

### Commit 1 pass 조건
- `phase='overrunEvent'`로 들어간다.
- 위 canonical field set이 normalize 가능한 형태로 저장된다.
- invalid/missing/legacy 상태가 `none` fallback 또는 안전한 recovery로 수렴한다.
- reward는 **아직 적용되지 않는다**.
- `currentPage`, `pagePlan`, `segmentTrophyTemplateIds`는 **아직 바뀌지 않는다**.

### Commit 1 fail 조건
- UI가 title/sourceType 문자열로 branch를 다시 추론한다.
- enter 직후 reward가 이미 적용된다.
- reload 시 snapshot 표정이 바뀐다.
- smoke가 green이어도 non-change line이 깨진다.

---

## 4. Commit 2 — Closing Slice

### 3줄 요약
- Commit 2의 역할은 Commit 1이 만든 canonical snapshot을 **display-only로 소비**해 장면 카드 UI와 confirm resolve를 닫는 것이다.
- 이 단계에서만 reward 1회 적용, `resolved` reload guard, old immediate baseline 제거가 열린다.
- 즉 Commit 2는 `중간 상태`가 아니라 `실제 플레이 가능한 최종 overrunEvent`를 닫는 단계다.

### 한 줄 목표
`renderOverrunEvent(summary)` + `confirm-overrun-event` + `resolveOverrunEvent(state)`를 한 closing commit으로 묶는다.

### 파일 경계
| 파일 | Commit 2에서 닫을 것 | Commit 2에서 금지 |
| --- | --- | --- |
| `dice-spell-standalone/src/app.js` | `renderOverrunEvent(summary)`, `confirm-overrun-event`, stale ending/CTA 제거 | onboarding primer 주입 |
| `dice-spell-standalone/src/styles.css` | `.overrun-event-*` shell, focus card, `none` density, ending과 다른 spacing/tone | 전역 스타일 체계 갈아엎기 |
| `dice-spell-standalone/src/game-engine.js` | `resolveOverrunEvent(state)` 또는 동등 함수, reward 1회 적용, cleanup, `resolved` guard | 새 reward 타입, unrelated refactor |
| `dice-spell-standalone/scripts/smoke-test.mjs` | A3/A4 closing smoke, old immediate baseline 제거 | unrelated smoke 확장 |

### Commit 2 pass 조건
- `themeTrophy` / `enemyTrophy` / `none` 3축이 `badge → header → flavor → reward summary → CTA → footnote` 구조로 읽힌다.
- confirm CTA에서만 reward가 정확히 1회 적용된다.
- `none`은 무보상 진행이지만 정상 branch로 유지된다.
- invalid/missing/broken/reload/resolved가 모두 safe recovery 또는 no-op로 런을 살린다.
- old immediate baseline이 제거된다.

### Commit 2 fail 조건
- snapshot field 대신 render가 branch를 다시 추론한다.
- confirm 전 적용 또는 confirm 후 중복 적용이 있다.
- `none`이 placeholder처럼 비어 있다.
- ending과 `overrunEvent`가 다시 같은 결과표처럼 읽힌다.
- Commit 2에 onboarding이나 새 메카닉이 섞인다.

---

## 5. 스택 기준 evidence bundle

### 한 줄 목표
리뷰와 tracker/run log가 같은 증거 묶음을 기준으로 두 커밋을 구분하게 만든다.

| 커밋 | 필수 evidence | 왜 필요한가 |
| --- | --- | --- |
| Commit 1 | enter/recovery smoke, before/after 상태 diff, non-change line 메모, 한 줄 handoff 문장 | `무엇이 아직 안 바뀌었는가`를 증거로 남기기 위해 |
| Commit 2 | A3/A4 smoke, confirm 전/후 diff, theme/enemy/none UI evidence, closing handoff 문장 | `이제 진짜 runtime-ready인가`를 증거로 남기기 위해 |

### 권장 첨부 순서
1. smoke 결과
2. 상태 diff 메모
3. UI evidence 또는 DOM evidence
4. 한 줄 handoff 문장

### tracker / run log 권장 문장
- Commit 1: `이번 커밋은 overrunEvent의 enter 분리와 canonical snapshot floor만 닫았고, reward apply와 page advance는 아직 열지 않았다.`
- Commit 2: `이번 커밋은 overrunEvent의 scene card와 confirm resolve를 닫아, reward 1회 적용·none 무보상 진행·resolved reload guard·old immediate baseline 제거까지 runtime closing을 마무리했다.`

---

## 6. 역할별 handoff 포인트

### 프로그래머
- Commit 1에서는 `상태 전이의 바닥`만 닫는다.
- Commit 2에서는 `장면 소비 + 적용 시점`만 닫는다.
- 두 커밋 모두 onboarding/state/meta 확장과 섞지 않는다.

### UI
- Commit 1에서는 시각 polish 욕심을 내지 않는다.
- Commit 2에서만 ending과 다른 shell/focus card/tone을 닫는다.
- `none`도 정상 장면 branch처럼 보여야 한다.

### 아트
- Commit 2에서 `artTokenSet`, `accentKey`, `badgeLabel` drift 여부만 확인한다.
- 새 대형 자산보다 shell/tone 정합성을 우선한다.
- ending flavor는 강화하지 않는다.

### QA
- Commit 1은 `non-change line 검수`, Commit 2는 `closing line 검수`로 분리한다.
- 리뷰 코멘트에서 두 커밋의 pass 기준을 같은 문단에 섞지 않는다.
- old immediate baseline 제거 여부를 Commit 2 pass의 일부로 본다.

### PM/기획
- Commit 1을 `아직 중간 상태`로, Commit 2를 `runtime closing`으로 기록한다.
- onboarding B-track은 Commit 2 pass 전까지 열지 않는다고 명시한다.
- `문서가 많다`가 아니라 `두 커밋으로 끝낸다`는 문장으로 handoff한다.

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| Commit 1과 Commit 2의 검수 문장이 섞이는 위험 | 중간 상태가 완성 상태처럼 기록될 수 있음 | tracker/run log용 한 줄 문장을 커밋별로 분리 | 첫 코드 커밋 리뷰 시 |
| Commit 2에 onboarding 또는 새 메카닉이 같이 들어오는 위험 | closing slice 범위 팽창, smoke 해석 붕괴 | 커밋 스택 표에 금지 범위를 명시 | closing commit 시작 전 |
| Commit 1에서 non-change line이 흐려지는 위험 | half-cutover가 pass처럼 보일 수 있음 | state matrix/review packet 기반 evidence 요구 | Commit 1 QA 시 |
| `none` branch가 Commit 2에서 약해지는 위험 | recovery 신뢰와 UX 읽힘 저하 | closing pass 조건에 3축 density 유지 포함 | Commit 2 UI/QA 시 |

---

## 8. 즉시 다음 액션

1. implementer는 `runway -> runtime patch map -> first runtime delivery packet -> second runtime slice packet -> 이 문서` 순서로 범위를 다시 확인한다.
2. 첫 실제 코드 턴은 Commit 1만 닫고, run log에 Commit 1용 handoff 문장을 그대로 남긴다.
3. Commit 1 review가 green이면 Commit 2에서만 scene/resolve closing을 연다.
4. Commit 2 pass 이후에만 onboarding B-track을 `B-1 -> B-2 -> B-3 -> B-4` 순서로 연다.
5. fail이면 새 문서를 추가하지 말고 해당 커밋 범위 안에서만 되돌린다.

---

## 한 줄 결론
이 문서는 이미 충분한 `overrunEvent` 문서 묶음을 **실제 2커밋 runtime stack**으로 다시 압축해, 다음 구현자가 `이번엔 어디서 멈추고 무엇으로 끝내는가`를 감이 아니라 같은 handoff 문장으로 실행하게 만드는 source packet이다.
