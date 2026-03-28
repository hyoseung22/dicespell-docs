# DiceSpell `overrunEvent` / 첫 런 온보딩 구현 갭 레저

## 3줄 요약
- 이 문서는 지금 DiceSpell 문서 세트가 충분히 강한 상태에서, **실제로 아직 남아 있는 구현 갭이 무엇인지**를 프로그래머 / UI / 아트 / QA / PM 기준으로 다시 압축한 source-of-truth다.
- 핵심은 새 설계를 더하는 것이 아니라, `현재 코드 baseline`과 `이미 닫힌 handoff 문서` 사이의 마지막 차이를 **커밋 단위 + 오너 단위 + 증거 단위**로 고정하는 것이다.
- 첫 화면만 읽어도 `무엇은 더 이상 문서 blocker가 아니고`, `무엇이 아직 코드 blocker인지`, `누가 어느 단계에서 실제로 받아 가야 하는지`가 바로 보여야 한다.

## 한 줄 목표
`문서 공백`과 `실구현 공백`을 분리해, 다음 구현/리뷰 턴이 더 이상 방향 문서를 늘리지 않고도 **A-track Commit 1/2 -> B-track B-1~B-4**를 같은 갭 언어로 닫게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell의 가장 큰 착시는 `문서가 부족하다`가 아니라 `문서가 충분한데도 아직 뭐가 안 끝났는지 한 화면에 안 보인다`는 점이다.

이미 닫힌 것들:
- `overrunEvent` 방향 / 화면 / acceptance / content deck / A-1~A-4 packet
- `runtime patch map`, `commit stack`, `handoff scorecard`, `fixture ledger`, `cutover gate`, `execution reporting`
- 온보딩 `handoff -> screen -> integration -> acceptance -> content deck -> B-1~B-4 packet -> runtime stack -> handoff scorecard -> owner workboard`

하지만 실제 구현 직전에는 여전히 아래가 다시 섞이기 쉽다.

1. `현재 코드가 아직 즉시 적용 baseline인데, 문서는 이미 runtime-ready처럼 충분해서` 실제 남은 갭이 작아 보이지 않는다.
2. 프로그래머는 `무엇을 구현할지`보다 `무엇이 아직 구현되지 않았는지`를 owner별로 다시 정리해야 한다.
3. UI/아트는 `문서상 화면 정의`와 `현재 live surface` 사이의 마지막 차이를 commit boundary로 받지 않으면 너무 일찍 closing 감각으로 들어가기 쉽다.
4. QA/PM은 `문서 handoff green`과 `실제 runtime green`을 같은 말로 기록해 half-cutover를 과장할 위험이 있다.

즉 지금 필요한 것은 새 설계가 아니라, **남은 구현 갭의 위치를 명시적으로 보이는 ledger**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 결론`, `2. 구현 갭 한눈표`, `3. A-track 갭`, `4. B-track 갭` | `runtime patch map`, `runtime commit stack`, `onboarding runtime stack` | 지금 남은 것은 새 설계가 아니라 file-bound code gap임을 확인한다 |
| UI | `2. 구현 갭 한눈표`, `3-3. UI/아트 갭`, `4-3. UI 갭` | `a3_ui_packet`, `ending_overrun_boundary_packet`, `B-1~B-4 packet` | Commit 1은 대기/동결 턴이고 Commit 2 이후에만 surface closing을 잡는다 |
| 아트 | `2. 구현 갭 한눈표`, `3-3. UI/아트 갭`, `4-4. 아트 갭` | `event_content_deck`, `first_run_onboarding_content_deck`, `event_screen_packet` | 새 대형 자산보다 token/accent drift와 최소 패키지가 우선임을 확인한다 |
| QA | `2. 구현 갭 한눈표`, `3-4. QA 갭`, `4-5. QA/PM 갭`, `6. 리스크 & 후속과제` | `fixture ledger`, `execution reporting packet`, `handoff scorecard` | 문서 green과 runtime green을 다른 evidence bundle로 남긴다 |
| PM/기획 | `1. first screen 결론`, `5. 문서 완료 vs 구현 완료 경계`, `6. 리스크 & 후속과제`, `7. 즉시 다음 액션` | `cutover gate packet`, `owner handoff packet`, `development tracker` | 이제 추가 설계보다 gap closure 기록과 bounded execution 관리가 우선임을 확인한다 |

---

## 1. first screen 결론

### 이 문서를 왜 열어야 하나
- **프로그래머**: 지금 남은 일이 `문서 재정리`인지 `코드 컷오버`인지 즉시 구분하려고 연다.
- **UI/아트**: 지금이 아직 payload baseline을 기다리는 턴인지, 실제 surface closing을 잡아도 되는 턴인지 확인하려고 연다.
- **QA/PM**: 문서 handoff-ready와 runtime-ready를 서로 다른 단계 언어로 남기기 위해 연다.

### 가장 짧은 답
- `overrunEvent`는 **문서 공백이 거의 없다.** 남은 것은 `src/game-engine.js`, `src/app.js`, `src/styles.css`, `scripts/smoke-test.mjs`에 걸친 Commit 1/2 코드 갭이다.
- 온보딩 B-track도 **surface 정의 공백이 거의 없다.** 남은 것은 A-track closing 이후 `surface 1개 = commit 1개` 원칙으로 primer를 붙이는 구현 갭이다.
- 따라서 다음 슬롯의 기본 질문은 `무슨 문서를 더 써야 하지?`가 아니라 `어느 갭을 누구 손으로 닫고, 어떤 증거를 남겨 닫힘을 증명하지?`다.

### 한 줄 판정 규칙
- 문서가 이미 있는 문제는 `설계 blocker`가 아니다.
- 코드와 smoke와 기록이 아직 없는 문제만 `실구현 blocker`다.
- `Commit 1 floor`, `Commit 2 closing`, `B-1`, `B-2`, `B-3`, `B-4`는 각각 다른 갭이다.

---

## 2. 구현 갭 한눈표

| 구간 | 현재 baseline | 이미 문서로 닫힌 것 | 아직 남은 실제 갭 | 누구 손에서 닫히나 | 닫힘 증거 |
| --- | --- | --- | --- | --- | --- |
| A-track Commit 1 | `continueOverrun()`가 즉시 적용/페이지 진행까지 한 번에 처리 | A-1/A-2 packet, first runtime slice/state/review/delivery, patch map, fixture ledger | `phase='overrunEvent'` 진입 wrapper + canonical snapshot floor + recovery + non-change line 유지 | 프로그래머 + QA + PM | `S1/S2/S3/S7/S8/S9/S10/S11`, 상태 diff, floor 문장 |
| A-track Commit 2 | `renderCenter()`에 독립 장면 없음, confirm resolve 없음, old immediate baseline 잔존 | A-3/A-4 packet, second runtime slice/review, commit stack, scorecard, owner workboard | scene card 표시 + confirm 1회 적용 + `resolved` guard + old baseline 제거 | 프로그래머 + UI + 아트 + QA + PM | `S4/S5/S6/S10/S12`, DOM/시각 evidence, closing 문장 |
| B-1 Library | primer 미구현 | B-1 packet, runtime stack, onboarding scorecard/workboard | `seenBookSelectPrimer` 저장/복구 + library hero-primer + 비차단 유지 | 프로그래머 + UI + QA | B1 acceptance/recovery + library 문장 |
| B-2 Battle | primer/hint 미구현 | B-2 packet, content deck, acceptance matrix | `battle.entry` / `battle.first-spell` / `battle.auto-pass` 주입 + seen flag 제어 | 프로그래머 + UI + QA | B2 acceptance/recovery + battle 문장 |
| B-3 Reward/Shop | primer 미구현 | B-3 packet, screen/content deck, scorecard | reward/shop 목적 분리 primer + boss draft 공존 + seen flag 제어 | 프로그래머 + UI + QA | B3 acceptance/recovery + reward/shop 문장 |
| B-4 Ending | primer 미구현, overrun 경계에 민감 | B-4 packet, boundary packet, content deck | 결과/CTA 의미 primer만 추가하고 overrun flavor 비침범 유지 | 프로그래머 + UI + 아트 + QA + PM | B4 acceptance/recovery + boundary evidence + ending 문장 |

### 한 줄 해석
- `A-track = 현재 코드와 target 문서 사이의 갭`
- `B-track = 문서로 닫힌 primer를 실제 surface에 심는 갭`
- 둘 사이를 섞는 순간 다시 blocker가 생긴다.

---

## 3. A-track 구현 갭 — `overrunEvent`

## 3-line summary
- A-track은 방향/카피/경계/검수선 부족이 아니라 **runtime baseline 전환**이 아직 안 끝난 상태다.
- 따라서 남은 갭은 설계 문서가 아니라 `현재 즉시 적용 체인`을 `중간 장면 + confirm resolve 체인`으로 분리하는 구현 갭이다.
- owner별로 보면 프로그래머는 상태/함수 갭, UI/아트는 장면/표정 갭, QA/PM은 evidence/기록 갭을 닫아야 한다.

### 한 줄 목표
`현재 즉시 적용 overrun baseline`을 `Commit 1 floor -> Commit 2 closing`의 두 단계 runtime으로 바꾼다.

### 왜 중요하나
`overrunEvent`는 이미 문서상으로는 handoff-ready다. 그런데 실제 코드는 아직 아니다. 이 차이를 모호하게 두면 다음 문제가 생긴다.

- Commit 1이 끝났는데 Commit 2처럼 기록된다.
- UI가 snapshot이 아니라 render 재추론 위에 shell을 얹는다.
- QA가 smoke 번호는 있지만 어떤 커밋에 속하는지 흐리게 기록한다.
- onboarding이 잘못된 baseline 위에서 시작된다.

### 3-1. 프로그래머 갭

| 구분 | 현재 코드 baseline | target 계약 | 실제 남은 갭 | handoff 메모 |
| --- | --- | --- | --- | --- |
| 엔진 진입 | `continueOverrun()` 안에서 reward apply -> page advance -> 다음 진입까지 연쇄 수행 | Commit 1에서는 `overrunEvent` 진입 wrapper까지만 허용 | 즉시 적용 체인을 enter 전용/resolve 전용 의미로 분리 | `game-engine.js`가 A-track 핵심 병목이다 |
| snapshot | selected reward identity만 느슨하게 남음 | canonical field set 직접 저장 | `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved`를 snapshot에 고정 | UI가 title 문자열로 branch 재추론하지 않게 해야 한다 |
| recovery | legacy/invalid/missing 복구 규칙이 target 기준으로 미완 | `none` fallback + safe recovery | normalize helper와 reload-safe convergence 구현 | Commit 1 smoke와 함께 닫아야 한다 |
| resolve | confirm 1회 적용/`resolved` guard/old baseline 제거 미완 | Commit 2에서만 reward 1회 적용 + page progression | resolve 함수 경계와 no-op 재실행 가드 구현 | Commit 1에 섞으면 안 된다 |

### 3-2. UI/아트 공통 갭

| 구분 | 문서 기준으로는 이미 정해진 것 | 아직 구현되지 않은 것 | 닫는 타이밍 | 금지 |
| --- | --- | --- | --- | --- |
| card hierarchy | badge -> header -> flavor -> reward summary -> CTA -> footnote 위계 | 실제 `renderCenter()` / `styles.css` 반영 | Commit 2 | Commit 1에서 shell 마감 |
| branch identity | `themeTrophy` / `enemyTrophy` / `none` 표정, accent, token | snapshot display-only 소비 UI | Commit 2 | render가 다시 content를 조합하는 것 |
| ending boundary | ending은 결과/정산/버튼 의미, overrunEvent는 장면/flavor | shared surface 경계 구현 | Commit 2, B-4 전 재확인 | ending이 overrun flavor를 먹는 것 |
| none branch density | neutral card / 오류 카드 아님 | 실제 시각 밀도와 footnote 톤 | Commit 2 | placeholder처럼 처리 |

### 3-3. QA 갭

| 질문 | 문서로는 닫혔는가 | 아직 남은 실제 일 |
| --- | --- | --- |
| `어떤 smoke가 Commit 1인가?` | 예 (`fixture ledger`, `scorecard`) | 실제 smoke 결과를 Commit 1 evidence로 남기기 |
| `무엇이 아직 바뀌면 안 되는가?` | 예 (`state matrix`, `review packet`) | non-change line을 실제 결과로 증명하기 |
| `runtime-ready는 언제인가?` | 예 (`second runtime review`, `cutover gate`) | Commit 2 green을 DOM/resolve evidence와 함께 남기기 |

### 3-4. PM/기획 갭

| 항목 | 문서상 상태 | 아직 남은 운영 갭 |
| --- | --- | --- |
| 단계 언어 | `floor` / `closing` / `B-1~B-4`까지 이미 고정 | 실제 tracker/run log/git에서 같은 단계 언어로만 기록 |
| source-of-truth | 문서 세트는 충분 | 어떤 문서가 설계 설명이고 어떤 것이 이제 단순 evidence gate인지 구분 유지 |
| 착수 판단 | `cutover gate`로 go/no-go 정의 완료 | stop 신호가 보일 때 기능 추가보다 기록 정정을 먼저 시키기 |

---

## 4. B-track 구현 갭 — 첫 런 온보딩

## 3-line summary
- B-track은 primer 방향/카피/surface 정의가 비어 있어서 막힌 것이 아니다.
- 남은 갭은 `A-track closing 이후` 각 surface를 실제 UI에 붙이고 seen flag와 비차단 UX를 지키는 런타임 갭이다.
- 따라서 온보딩의 핵심 위험은 새 기획 부족이 아니라 `여러 surface를 한 커밋에 섞는 것`이다.

### 한 줄 목표
온보딩을 `B-1 -> B-2 -> B-3 -> B-4`의 4개 surface gap으로만 열고, 각 surface를 별도 evidence로 닫는다.

### 왜 중요하나
온보딩은 지금 문서상으로는 거의 다 닫혔다. 하지만 실제 구현은 아직 0이다. 이 상태에서 가장 쉽게 생기는 오류는 다음 둘이다.

1. `문서가 충분하니 같이 붙이자`며 여러 surface를 동시에 구현한다.
2. `ending primer`를 붙이면서 `overrunEvent` flavor까지 같이 만진다.

이 둘이 생기면 B-track은 설계 부족이 아니라 실행 discipline 실패로 다시 blocker가 된다.

### 4-1. surface별 실제 갭

| surface | 문서상 이미 닫힌 것 | 아직 남은 실제 갭 | 가장 먼저 받을 사람 |
| --- | --- | --- | --- |
| B-1 library | hero-primer 위치, 카피, seen flag, acceptance | `renderGrimoireShelf()` 주입 + seen 저장/복구 + 비차단 선택 | 프로그래머 / UI / QA |
| B-2 battle | entry / first-spell / auto-pass 3면, hook, seen flag | `renderBattle()` 재진입 지점과 auto-pass 훅 연결 | 프로그래머 / UI / QA |
| B-3 reward/shop | reward/shop 목적 분리, boss draft 공존, seen flag | `renderReward()` / `renderShop()` 주입과 문구 밀도 조정 | 프로그래머 / UI / QA |
| B-4 ending | defeat/victory/overrun-cta primer, 경계 계약 | `renderEnding()` primer 주입 + boundary 유지 + overrun flavor 비침범 | 프로그래머 / UI / 아트 / QA / PM |

### 4-2. 프로그래머 갭
- A-track Commit 2 green 이전에는 B-track 코드를 열지 않는다.
- 각 surface는 helper/flag/render 주입을 포함해도 **한 surface = 한 commit**이다.
- `meta.uiGuidance` 구조는 이미 문서로 정해져 있으므로, 남은 일은 구현과 recovery다.

### 4-3. UI 갭
- 이미 필요한 카피와 hierarchy는 있다.
- 남은 일은 각 surface에서 primer가 본문을 삼키지 않도록 밀도를 닫는 것이다.
- 특히 B-4 ending은 `결과/CTA 의미`만 남겨야 하고, 장면 flavor는 끝까지 `overrunEvent`가 맡는다.

### 4-4. 아트 갭
- B-track의 첫 필요 자산은 대형 신작보다 `token / accent / badge`가 drift 없이 재사용 가능한 최소 패키지다.
- library/battle/reward/shop는 UI 보조 자산 중심이고, ending은 boundary-safe 톤 유지가 더 중요하다.
- overrun flavor 자산을 ending에 끌어오지 않는다.

### 4-5. QA/PM 갭
- B-track은 `온보딩 진행` 한 문장으로 기록하지 않는다.
- surface별 acceptance/recovery와 surface별 green 문장을 각각 남긴다.
- B-4를 닫기 전에는 `온보딩 전체 green` 같은 표현을 금지한다.

---

## 5. 문서 완료 vs 구현 완료 경계

## 3줄 요약
- 지금 DiceSpell에서 가장 중요한 구분은 `문서가 handoff-ready인가`와 `코드가 runtime-ready인가`를 분리하는 것이다.
- A-track과 B-track은 둘 다 문서상 handoff-ready에 가깝다.
- 하지만 runtime-ready는 아직 아니다.

### 한 줄 목표
문서 completeness를 구현 completeness로 오해하지 않게 만든다.

| 항목 | 문서 완료 상태 | 구현 완료 상태 | 아직 남은 갭 |
| --- | --- | --- | --- |
| `overrunEvent` | 매우 높음: packet/summary/stack/scorecard/workboard/gate/reporting까지 있음 | 낮음: 현재 live baseline은 여전히 immediate-apply 중심 | Commit 1/2 코드 + smoke + 기록 |
| 온보딩 B-track | 매우 높음: B-1~B-4 packet/summary/stack/scorecard/workboard까지 있음 | 낮음: primer 실제 주입은 아직 미구현 | B-1~B-4 surface별 구현 + acceptance + 기록 |
| tracker/run log 운영 | 높음: execution reporting packet으로 규칙 정리 | 중간: 실제 다음 구현 턴에서 처음 증명해야 함 | append-only 기록 discipline 실제 적용 |

### 역할별 handoff 포인트
- **프로그래머에게 넘길 것**: 이제 필요한 것은 새 설계가 아니라 각 커밋의 file-bound patch와 smoke evidence다.
- **UI 오너에게 넘길 것**: layout 아이디어보다 `어느 단계에서 shell을 닫아도 되는가`가 더 중요하다.
- **아트 오너에게 넘길 것**: 새 대형 자산 제안보다 token/accent drift 방지와 boundary-safe 패키지가 우선이다.
- **QA에게 넘길 것**: 문서 검토 완료가 아니라 단계별 green 증명이 우선이다.
- **PM에게 넘길 것**: 추가 문서 요청보다 stage language와 stop signal 통제가 우선이다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| 문서가 충분한 상태라 오히려 `아직 안 끝난 실제 구현 갭`이 가려지는 위험 | implementer가 추가 문서 작성과 실제 코드 컷오버 우선순위를 다시 헷갈릴 수 있음 | 이번 ledger로 `문서 blocker`와 `실구현 blocker`를 분리해 owner별 남은 갭만 다시 보이게 정리 | 다음 writer 구현 슬롯 착수 전 |
| A-track Commit 1/2와 B-track B-1~B-4가 `남은 일 목록`에서 다시 큰 덩어리로 합쳐지는 위험 | half-cutover가 재발하고 green 기록이 과장됨 | 갭을 commit/surface별로 분리하고 각 단계별 닫힘 증거를 다시 표로 고정 | tracker / run log 기록 직전 |
| UI/아트가 문서 풍부함 때문에 Commit 1 또는 B-1 이전에 closing 감각으로 들어가는 위험 | payload floor, boundary, non-change line보다 시각 마감이 앞서 drift 발생 | Commit 1은 payload 대기/동결 턴, Commit 2와 B-track만 closing 턴이라고 재명시 | Commit 1 착수 전, B-1 착수 전 |
| PM/QA가 `문서 handoff-ready`와 `runtime-ready`를 같은 green 문장으로 남기는 위험 | 다음 implementer가 실제 baseline을 잘못 읽고 잘못된 단계부터 착수 | `문서 완료 vs 구현 완료 경계` 섹션과 단계별 evidence bundle을 별도 고정 | 다음 기록/리뷰 사이클 |

---

## 7. 즉시 다음 액션

1. 다음 구현자는 먼저 `runtime patch map -> runtime commit stack -> execution reporting packet -> 이 gap ledger` 순서로 읽고, **추가 설계가 아니라 Commit 1 code gap**부터 닫는다.
2. Commit 1 green 전에는 UI/아트 closing과 온보딩 primer 구현을 열지 않는다.
3. Commit 2 green 뒤에만 `onboarding runtime stack -> onboarding handoff scorecard -> 이 gap ledger` 순서로 B-1을 연다.
4. 각 단계가 끝나면 `무엇을 새로 설계했는가`보다 `어떤 갭이 닫혔는가`를 tracker/run log에 남긴다.
5. fail이면 새 packet을 더 만들기보다, 현재 단계의 gap이 왜 안 닫혔는지 짧게 기록하고 같은 단계 안에서 되돌린다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- `docs/dicespell_current_build_reverse_spec_kr.md`
- `docs/dicespell_current_build_code_map_kr.md`
