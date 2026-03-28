# DiceSpell `overrunEvent` 두 번째 런타임 슬라이스 패킷

## 3줄 요약
- 이 문서는 `first runtime slice` 이후 남는 실제 구현 공백인 **A-3 중앙 카드 UI + A-4 confirm resolve**를 한 번 더 bounded commit으로 압축한 source handoff다.
- 목표는 프로그래머/UI/아트/QA가 `장면 카드 읽힘 + confirm 1회 적용 + full smoke migration`을 **한 closing slice**로 같은 문장으로 넘기게 만드는 것이다.
- 첫 슬라이스가 `들어가기`와 `저장/복구 바닥`을 닫았다면, 두 번째 슬라이스는 `보여 주기`와 `실제로 넘기기`를 닫아 `overrunEvent` 축을 handoff-ready가 아니라 runtime-ready 상태로 끝내는 단계다.

## 한 줄 목표
`overrunEvent`의 남은 실제 구현을 **A-3 UI shell + A-4 resolve + review evidence + closing handoff** 한 묶음으로 고정해, 마지막 컷오버가 다시 half-cutover나 역할별 해석 차이로 흐르지 않게 만든다.

## 왜 이 문서가 필요한가
현재 DiceSpell에는 이미 아래 문서가 있다.

- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_first_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_overrun_a4_resolve_packet_kr.md`

문제는 첫 슬라이스가 닫힌 뒤에도 실제 구현 직전 아래 혼선이 남는다는 점이다.

1. 프로그래머는 A-3와 A-4를 각각 읽을 수는 있지만, **어디까지를 같은 closing commit으로 묶어야 하는지** 다시 머릿속에서 재조합해야 한다.
2. UI/아트는 A-3에서 shell/tone을 닫고 A-4에서 confirm transition을 닫아야 한다는 건 알지만, **payload freeze 이후 실제 참여 범위**를 한 장으로 보기 어렵다.
3. QA는 A3-S1~S5와 A4-S4~S12를 모두 봐야 하지만, **이번 턴의 closing evidence bundle**이 무엇인지 따로 다시 엮어야 한다.
4. PM/기획은 `첫 슬라이스 이후 남은 마지막 구현 턴`을 한 문장으로 요약하기 어렵다.

즉 지금 필요한 것은 새 설계가 아니라, **`overrunEvent`를 실제로 끝내는 두 번째 런타임 슬라이스 자체를 한 장으로 닫는 일**이다.

---

## 이 문서를 누가 먼저 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 읽을 문서 | 이번 턴 목표 |
| --- | --- | --- | --- |
| 프로그래머 | `2. 이번 슬라이스 범위`, `3. 파일별 closing 묶음`, `4. 구현 순서` | `dicespell_overrun_a3_ui_packet_kr.md`, `dicespell_overrun_a4_resolve_packet_kr.md` | 단일 장면 카드 + confirm resolve + old immediate baseline 제거를 한 commit 묶음으로 닫기 |
| QA | `5. evidence bundle`, `6. 리뷰 통과선` | `dicespell_overrun_event_smoke_migration_packet_kr.md`, `dicespell_overrun_first_runtime_review_packet_kr.md` | A3-S1~S5 + A4-S4~S12를 closing smoke 세트로 검수 |
| UI | `7. UI handoff` | `dicespell_overrun_a3_ui_packet_kr.md`, `dicespell_ending_overrun_boundary_packet_kr.md` | overrunEvent가 ending/보상 화면이 아니라 경계 장면으로 읽히게 닫기 |
| 아트 | `8. 아트 handoff` | `dicespell_overrun_event_screen_packet_kr.md`, `dicespell_overrun_event_content_deck_kr.md` | badge/accent/token이 theme/enemy/none 3축에서 drift 없이 마감되게 확인 |
| PM/기획 | `9. 한 줄 closing handoff`, `10. 즉시 다음 액션` | `dicespell_overrun_first_runtime_delivery_summary_kr.md`, `dicespell_overrun_onboarding_entrypoint_matrix_summary_kr.md` | `overrunEvent`가 어디까지 닫혔는지 과장 없이 기록 |

---

## 2. 이번 슬라이스 범위

### 3줄 요약
- 이번 closing slice는 `A-3 중앙 카드 UI`와 `A-4 confirm resolve`를 **같은 종료선**으로 묶는다.
- 첫 런타임 슬라이스(A-1/A-2)가 이미 green이라는 전제 위에서만 열린다.
- 종료선은 `보여 주기 + 눌렀을 때 정확히 한 번 적용되기 + full smoke migration 완료`다.

### 한 줄 목표
이번 턴을 `overrunEvent` 축의 마지막 bounded runtime commit`으로만 닫는다.

| 범주 | 이번 슬라이스에 포함 | 이번 슬라이스에서 제외 |
| --- | --- | --- |
| 엔진 | `resolveOverrunEvent(state)` 또는 동등 함수, reward 1회 적용, `resolved` guard, invalid/missing -> `none` fallback, cleanup | 새 reward 종류, overrun 외 phase 리팩터링 |
| 앱 | `renderOverrunEvent(summary)` 최종 shell, `confirm-overrun-event` action wiring, stale ending/overrun card 제거 | onboarding primer 주입, 다른 surface UI 정리 |
| 스타일 | overrun 전용 shell/tone/focus card/fallback density 마감 | 전용 풀아트 시스템, 전역 테마 리디자인 |
| 검증 | A3-S1~S5 + A4-S4~S12 + recovery, old immediate baseline 제거 증거 | 새 밸런스 시뮬레이션, unrelated smoke 확장 |

### 이번 슬라이스의 hard gate
- A-1/A-2 first runtime slice가 이미 pass 상태여야 한다.
- `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved`가 canonical payload로 저장되어 있어야 한다.
- 리뷰 문장에서 `reward 미적용 유지`였던 첫 슬라이스와 충돌하지 않아야 한다. 즉 **이번 슬라이스부터만** reward apply가 열린다.

---

## 3. 파일별 closing 묶음

### 한 줄 목표
마지막 컷오버를 누가 어떤 파일 묶음으로 닫아야 하는지 다시 흔들리지 않게 만든다.

| 파일 | 이번 턴 필수 변경 | 넘기면 안 되는 변경 | handoff 메모 |
| --- | --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `resolveOverrunEvent(state)` 또는 동등 함수, reward 1회 적용, `resolved` guard, cleanup | 새 meta 설계, onboarding state, unrelated refactor | 이번 closing slice의 중심 파일 |
| `dice-spell-standalone/src/app.js` | `renderCenter()`의 `overrunEvent` 최종 분기, `renderOverrunEvent(summary)`, `confirm-overrun-event` wiring | primer helper, library/battle/reward/shop surface 변경 | UI shell + action wiring만 담당 |
| `dice-spell-standalone/src/styles.css` | `.overrun-event-*` shell/tone/focus/fallback density 마감 | 전역 카드 체계 갈아엎기 | ending과 다른 장면 집중도만 닫기 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | A3-S1~S5, A4-S4~S12, old immediate baseline 제거 증거 | unrelated smoke 대량 추가 | closing proof 파일 |

### 권장 커밋 경계
- **한 커밋**: `game-engine.js` + `app.js` + `styles.css` + `smoke-test.mjs`
- **한 줄 커밋 의미**: `overrunEvent closing slice: scene card + confirm resolve + full smoke`
- **이 커밋에 섞지 말 것**: onboarding B-1~B-4, 보상 종류 추가, meta 저장 구조 확장, global style cleanup

---

## 4. 실제 구현 순서

### 한 줄 목표
A-3와 A-4를 따로 놀지 않게, 마지막 closing slice를 실제 코드 순서로 일치시킨다.

1. `docs/dicespell_overrun_a3_ui_packet_kr.md`로 카드 위계와 ending 경계를 다시 선언한다.
2. `docs/dicespell_overrun_a4_resolve_packet_kr.md`로 confirm 이후 상태 diff와 recovery 규칙을 다시 선언한다.
3. `app.js`에서 `renderCenter()`의 `overrunEvent` 분기와 `renderOverrunEvent(summary)`를 최종 shell 기준으로 고정한다.
4. `styles.css`에서 `overrun-event-card`, `focus-card`, tone/token class, `none` neutral density를 마감한다.
5. `game-engine.js`에서 `resolveOverrunEvent(state)` 또는 동등 함수로 reward 1회 적용 / `none` 무보상 진행 / invalid fallback / `resolved` guard / cleanup을 묶는다.
6. `app.js`에서 `confirm-overrun-event` action을 wiring하고 stale ending/overrun UI가 남지 않게 한다.
7. `smoke-test.mjs`에서 A3-S1~S5, A4-S4~S12, old immediate baseline 제거 증거를 같은 턴에 고정한다.
8. closing evidence bundle과 한 줄 closing handoff 문장을 tracker/run log에 남긴다.

### 구현 중 자기 점검 질문
- 지금 render가 `title`/`sourceType` 문자열을 다시 읽어 branch를 추론하는가? → **되면 안 된다**
- 지금 confirm 전에 reward가 이미 적용됐는가? → **되면 안 된다**
- 지금 confirm 후 같은 CTA를 reload/reclick로 다시 눌러도 effect가 들어가는가? → **되면 안 된다**
- 지금 `none` branch가 placeholder처럼 비어 있거나 진행이 막히는가? → **되면 안 된다**
- 지금 smoke가 old immediate baseline을 여전히 pass로 취급하는가? → **되면 안 된다**

---

## 5. evidence bundle

### 3줄 요약
- 이번 턴은 `장면이 보인다`만으로 닫힌 게 아니다.
- `UI shell evidence + resolve state diff + smoke green + 한 줄 결론`이 같이 있어야 closing slice가 handoff-ready다.
- 이 네 가지가 함께 있어야 `overrunEvent`가 문서 단계가 아니라 실제 runtime 단계에서 닫혔다고 말할 수 있다.

### 한 줄 목표
리뷰와 다음 onboarding 착수가 같은 closing evidence bundle을 보게 만든다.

| 증거 | 필수 내용 | owner |
| --- | --- | --- |
| UI 캡처/DOM evidence | theme/enemy/none 3축 카드 위계, ending 대비 shell 차이, CTA/footnote 존재 | UI + QA |
| 상태 diff 메모 | confirm 전/후 `resolved`, reward 1회 적용, `currentPage`/`pagePlan`/`overrunLevel` 전진, cleanup 완료 | 프로그래머 + QA |
| smoke 결과 | A3-S1~S5, A4-S4~S12, old immediate baseline 제거 | QA |
| 한 줄 closing 결론 | `이번 커밋으로 overrunEvent의 장면 카드와 confirm resolve를 닫고 onboarding 전까지 필요한 runtime baseline을 끝냈다.` | PM/기획 |

### 권장 첨부 순서
1. smoke 결과
2. confirm 전/후 state diff 메모
3. 3축 UI 캡처 또는 동등 evidence
4. 한 줄 closing 결론

---

## 6. 리뷰 통과선

### 한 줄 목표
이번 closing slice가 pass인지 fail인지를 누구나 같은 문장으로 말하게 만든다.

### pass 조건
- `overrunEvent`가 ending과 다른 단일 장면 카드로 읽힌다.
- `themeTrophy`, `enemyTrophy`, `none` 3축 모두 badge/header/flavor/summary/CTA/footnote 6요소가 유지된다.
- confirm CTA에서만 reward가 정확히 한 번 적용된다.
- invalid/missing/broken/reload/resolved 케이스가 모두 `none` fallback 또는 safe no-op로 런을 살린다.
- old immediate baseline이 제거되고 A3/A4 smoke가 새 기준선이 된다.

### fail 조건
- smoke가 green이어도 reload/reclick로 reward가 두 번 적용된다.
- UI가 snapshot field 대신 문자열 재추론으로 badge/accent/header를 다시 만든다.
- `none` branch에서 CTA semantics나 density가 무너진다.
- ending과 `overrunEvent`가 여전히 같은 결과표/보상 선택 화면처럼 읽힌다.
- onboarding 구현이 같이 들어와 closing slice 범위가 부풀었다.

---

## 7. UI handoff

### 한 줄 목표
UI는 이번 턴에 `마지막 장면 읽힘`을 닫고, onboarding이 아니라 overrun runtime closing에만 집중한다.

### 이번 턴에 UI가 닫아야 할 것
- `badge → header → flavor → reward summary → CTA → footnote` 6요소 위계
- theme/enemy/none 3축 공통 density
- ending과 다른 shell/focus card/spacing/tone
- confirm 직후 stale card/CTA 잔상 제거

### 이번 턴에 UI가 하지 않을 것
- primer helper 주입
- library/battle/reward/shop/ending surface 수정
- 전역 카드 시스템 재정렬

### UI 완료 정의
- `overrunEvent`를 처음 보는 사람이 `이건 결과표`나 `보상 선택 화면`이 아니라 `다음 권으로 넘어가기 직전 장면`이라고 읽을 수 있어야 한다.

---

## 8. 아트 handoff

### 한 줄 목표
아트는 이번 턴에 token freeze 이후 실제 visual close를 확인하되, 새 자산 범위를 overrun shell 안으로만 제한한다.

### 이번 턴에 아트가 확인할 것
- `artTokenSet`이 theme/enemy/none에서 drift 없이 shell에 반영되는가
- badge/accent/focus card가 ending보다 한 단계 장면적으로 보이는가
- `none` neutral shell이 placeholder가 아니라 정상 branch처럼 읽히는가
- confirm transition 중 tone/token flicker가 없는가

### 이번 턴에 아트가 하지 않을 것
- onboarding primer 자산 제작
- 전역 UI 테마 재정비
- ending 자체의 flavor 강화

### 아트 완료 정의
- theme/enemy/none 모두 같은 구조 안에서 tone만 달라지고, `none`도 빈손 서사 branch로 읽혀야 한다.

---

## 9. 한 줄 closing handoff 문장

### 한 줄 목표
tracker / run log / 리뷰 코멘트가 같은 closing 결론 문장을 쓰게 만든다.

> 이번 커밋으로 `overrunEvent`의 중앙 장면 카드와 confirm resolve를 닫아, reward 1회 적용·`none` 무보상 진행·`resolved` reload guard·old immediate baseline 제거까지 포함한 runtime closing slice를 마무리했다.

### 왜 이 문장이 중요한가
- 첫 슬라이스와 closing 슬라이스를 명확히 구분한다.
- `overrunEvent`가 이제 문서 handoff가 아니라 runtime baseline으로 닫혔다는 점을 과장 없이 말한다.
- 다음 implementer가 곧바로 onboarding B-track으로 넘어가도 되는 기준을 제공한다.

---

## 10. 즉시 다음 액션

1. 프로그래머는 이번 문서 기준으로 A-3/A-4 closing slice를 실제 코드로 닫는다.
2. QA는 A3-S1~S5 + A4-S4~S12 + old immediate baseline 제거를 같은 리뷰 코멘트에 묶는다.
3. tracker와 automation run log에는 위 한 줄 closing handoff 문장을 그대로 남긴다.
4. pass가 나면 다음 턴부터만 `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md` 기준 onboarding B-track을 연다.
5. fail이면 새 문서를 추가하지 말고 A-3/A-4 범위 안에서 되돌린다.

---

## 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| A-3와 A-4가 다시 두 커밋으로 어정쩡하게 쪼개지는 위험 | shell은 있는데 resolve가 비거나, resolve는 있는데 UI semantics가 흐림 | 두 번째 런타임 슬라이스를 closing commit으로 고정 | 실제 구현 슬롯 시작 전 |
| old immediate baseline 일부가 살아남는 위험 | 중복 적용 / smoke drift | A3/A4 smoke와 baseline 제거를 같은 evidence bundle로 묶음 | smoke 리뷰 시 |
| onboarding이 closing slice와 같이 열리는 위험 | 범위 팽창 / 검수선 붕괴 | B-track은 pass 후에만 연다는 gate 명시 | closing slice 리뷰 직후 |
| `none` branch가 정상 서사 상태로 닫히지 않는 위험 | fallback 신뢰 저하 | theme/enemy/none 공통 density와 confirm semantics를 pass 조건에 포함 | 수동 캡처/QA 확인 시 |

---

## 한 줄 결론
이 문서는 `overrunEvent`의 남은 실제 구현을 **A-3 UI shell + A-4 confirm resolve + full smoke migration** 한 묶음의 closing slice로 다시 압축해, 마지막 컷오버가 더 이상 문서 해석 문제가 아니라 실제 runtime 마감 문제로만 남게 만드는 source handoff다.
