# DiceSpell `overrunEvent` 실구현 직전 런타임 패치 맵

## 3줄 요약
- 이 문서는 `overrunEvent` 문서 묶음을 **현재 실제 코드 함수 기준 patch surface**로 다시 번역한 프로그래머 중심 source-of-truth다.
- 핵심은 새 설계가 아니라, **지금 코드에서 무엇이 이미 live이고 무엇이 A-1~A-4 target과 어긋나는지**를 함수/파일 단위로 고정하는 것이다.
- 프로그래머는 `continueOverrun()`/`renderEnding()`/`renderCenter()`/action dispatcher/`smoke-test.mjs` 현재 baseline부터, UI/아트는 ending↔overrun 경계와 카드 밀도부터, QA는 immediate-apply baseline과 recovery 차이부터 읽으면 된다.

## 한 줄 목표
`overrunEvent` 실구현 직전, 구현자가 현재 live runtime을 다시 grep/추측하지 않고도 **어디를 어떻게 잘라야 하는지** 바로 판단하게 만든다.

## 왜 이 문서가 필요한가
이미 DiceSpell에는 `overrunEvent` 방향 문서, content deck, screen packet, acceptance, A-1~A-4 packet, runway packet까지 있다. 문제는 실구현 직전엔 여전히 아래 질문이 남는다는 점이다.

- 지금 live code에서 `continueOverrun()`는 정확히 어디까지 한 번에 하고 있지?
- `app.js`는 어떤 action에서 바로 그 함수로 들어가고 있지?
- `renderCenter()`는 아직 `overrunEvent`를 모르는데, 그 공백은 어느 단계에서 닫아야 하지?
- `smoke-test.mjs`는 아직 어떤 assertion으로 immediate-apply baseline을 고정하고 있지?
- A-1~A-4 문서는 충분한데, **현재 코드 anchor**가 빠져 있어 implementer가 결국 소스를 다시 캐야 하지 않나?

이 문서는 그 마지막 빈칸만 메운다. 새 기능을 제안하지 않고, 이미 닫힌 문서 계약을 **현재 runtime anchor map**으로 다시 묶는다.

---

# 누가 어디부터 읽어야 하나

## 프로그래머
1. 이 문서의 `1. 현재 live entry map`
2. `2. A-step별 실제 패치 표`
3. `3. 함수별 current vs target`
4. `docs/dicespell_overrun_cutover_runway_packet_kr.md`
5. `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md` ~ `docs/dicespell_overrun_a4_resolve_packet_kr.md`

## UI 오너
1. 이 문서의 `4. UI surface 경계`
2. `docs/dicespell_ending_overrun_boundary_packet_kr.md`
3. `docs/dicespell_overrun_a3_ui_packet_kr.md`
4. `docs/dicespell_overrun_event_content_deck_kr.md`

## 아트 오너
1. 이 문서의 `4. UI surface 경계`
2. `docs/dicespell_overrun_event_screen_packet_kr.md`
3. `docs/dicespell_overrun_a3_ui_packet_kr.md`

## QA 오너
1. 이 문서의 `5. smoke/current baseline 차이`
2. `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
3. `docs/dicespell_overrun_a4_resolve_packet_kr.md`

---

# 1. 현재 live entry map

## 3줄 요약
- 현재 live runtime은 `app.js` action dispatcher가 `continue-overrun`을 바로 `continueOverrun(appState.session)`로 연결하고, 엔진은 그 안에서 **선택 보상 적용 + 오버런 진입 + 페이지 전진**을 한 번에 처리한다.
- `renderEnding()`은 여전히 `오버런 전리품 보관함`과 `오버런 계속` 버튼을 같은 ending surface 안에 두고 있고, `renderCenter()`는 아직 `overrunEvent` phase 분기가 없다.
- `smoke-test.mjs`도 현재는 `continueOverrun()` 직후 `battle` phase 진입, reward 즉시 적용, `segmentTrophyTemplateIds` 초기화까지 한 번에 기대하고 있다.

## 한 줄 목표
실구현 전, 현재 live runtime이 정확히 어떤 경로로 굴러가는지 먼저 고정한다.

| 파일 | 현재 live anchor | 현재 동작 | target cutover와의 차이 |
| --- | --- | --- | --- |
| `dice-spell-standalone/src/app.js` | action dispatcher `case "continue-overrun"` | 버튼 클릭 시 즉시 `continueOverrun(appState.session)` 호출 | 중간 `overrunEvent` phase가 아직 없음 |
| `dice-spell-standalone/src/app.js` | `renderEnding(summary)` | ending 안에서 `오버런 전리품 보관함` + `오버런 계속` CTA를 함께 렌더 | ending과 overrunEvent surface 분리가 아직 없음 |
| `dice-spell-standalone/src/app.js` | `renderCenter(summary)` | `battle/shop/reward/ending`만 분기 | `overrunEvent` branch 미구현 |
| `dice-spell-standalone/src/game-engine.js` | `chooseEndingOverrunReward(state, rewardId)` | selectedRewardId만 저장 | snapshot/canonical field set 없음 |
| `dice-spell-standalone/src/game-engine.js` | `continueOverrun(state)` | 선택 reward bundle 적용 → `overrunLevel += 1` → `segmentTrophyTemplateIds=[]` → `pagePlan` 확장 → `currentPage += 1` → `phase='library'` → `maybeEnterNextPage()` | A-1~A-4 전체가 한 함수에 압축돼 있음 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | overrun 관련 smoke 블록 | `continueOverrun()` 직후 reward 적용/다음 세그먼트 진입을 기대 | enter/snapshot/UI/resolve 분리 smoke로 아직 안 갈라져 있음 |

## 왜 이 표가 중요하나
지금 `overrunEvent` 문서가 충분해도, 실제 구현자는 결국 현재 코드가 어떤 상태인지 알아야 한다. 이 표는 그 출발점이다. 즉 A-1~A-4는 빈 설계 위에 얹는 게 아니라, **이 현재 live chain을 commit 단위로 분해하는 작업**이다.

---

# 2. A-step별 실제 패치 표

## 3줄 요약
- A-1은 `continueOverrun()`에서 immediate-apply와 page progression을 떼어 `enter`만 남기는 단계다.
- A-2는 엔진이 `contentKey` 중심 snapshot을 직접 저장/복구하게 만드는 단계다.
- A-3는 `renderCenter()`/`styles.css`가 `overrunEvent`를 독립 장면 카드로 읽히게 만들고, A-4는 CTA resolve와 old baseline 제거까지 닫는 단계다.

## 한 줄 목표
문서상의 A-1~A-4를 **현재 파일/함수 경계**에 정확히 다시 매핑한다.

| 단계 | 필수 파일 | 현재 anchor | 이번 단계에서 바뀌는 것 | 이번 단계에서 일부러 안 하는 것 |
| --- | --- | --- | --- | --- |
| A-1 | `src/game-engine.js`, `scripts/smoke-test.mjs`, 최소 `src/app.js` | `continueOverrun()` / `continue-overrun` action / overrun smoke | `continueOverrun()`를 `overrunEvent` enter wrapper로 축소, fallback snapshot 생성, phase 진입 smoke 추가 | reward apply, page progression, full UI |
| A-2 | `src/game-engine.js`, `scripts/smoke-test.mjs`, 필요 시 `src/app.js` | A-1에서 만든 `state.overrunEvent` | `contentKey`/`badgeLabel`/`accentKey`/`artTokenSet`/`headerTitle`/`flavorText`/`confirmLabel` canonicalization, normalize/recovery | shell styling, confirm resolve |
| A-3 | `src/app.js`, `src/styles.css`, sanity smoke | `renderCenter()` / `renderEnding()` / 새 `renderOverrunEvent(summary)` | ending과 다른 중앙 카드 1장 shell, badge→header→flavor→reward summary→CTA→footnote 위계 | 엔진 resolve 로직 |
| A-4 | `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs` | `confirm-overrun-event` wiring + old `continueOverrun()` baseline | CTA 시점 1회 적용, `resolved` guard, `none` 진행, full smoke migration | 온보딩 primer 구현 |

---

# 3. 함수별 current vs target

## 3줄 요약
- 핵심 함수는 `chooseEndingOverrunReward()`, `continueOverrun()`, `renderEnding()`, `renderCenter()`, action dispatcher, 그리고 smoke overrun assertion 블록이다.
- 특히 `continueOverrun()`는 지금 사실상 A-1~A-4 전부를 동시에 하는 함수라, 구현 범위를 부풀리는 주범이기도 하다.
- target은 새 거대한 리팩터링이 아니라, 각 함수가 맡아야 할 책임을 A-step별로 다시 배치하는 것이다.

## 한 줄 목표
구현자가 가장 먼저 건드릴 함수마다 `지금 무엇을 하고 있는지 / target에서 무엇을 맡아야 하는지`를 고정한다.

### 3-1. `chooseEndingOverrunReward(state, rewardId)`
**현재**
- `phase === 'ending'`
- `ending.overrunDraft.selectedRewardId`가 비어 있을 때만 선택 저장
- option lookup 실패 시 그냥 return

**target 이후 역할**
- 여전히 ending 단계의 draft 선택 저장만 맡는다.
- snapshot canonicalization이나 effect apply는 맡지 않는다.
- invalid 선택은 enter 단계나 normalize 단계에서 `none` fallback으로 흡수한다.

**프로그래머 handoff 포인트**
- 이 함수는 overrunEvent phase 생성기가 아니다.
- 여기서 `contentKey`나 snapshot 필드를 미리 만들기 시작하면 책임이 섞인다.

### 3-2. `continueOverrun(state)`
**현재**
- `selectedOverrunReward` lookup
- `applyEffectBundle(...)`
- `state.overrunLevel += 1`
- `state.segmentTrophyTemplateIds = []`
- `pagePlan` push
- `currentPage += 1`
- `phase = 'library'`
- `reward/shop/battle/ending = null`
- `maybeEnterNextPage(state)`

**target 이후 역할**
- A-1: `overrunEvent` snapshot 생성 + `phase='overrunEvent'` 진입까지만 담당
- A-2: canonical snapshot 또는 normalize helper를 소비할 진입점 유지
- A-4: 최종적으로는 `resolveOverrunEvent(state)` 또는 동등 함수가 실제 apply/progression을 담당하고, `continueOverrun()`는 enter semantics만 맡거나 제거된다.

**금지 패턴**
- A-1에서 page progression까지 같이 남기는 것
- A-2에서 `continueOverrun()` 안에 UI용 field set을 임시로 만들었다가 app.js가 다시 손보는 것
- A-4 전까지 old immediate baseline과 new phase baseline을 반쯤 섞는 것

### 3-3. `renderEnding(summary)`
**현재**
- ending hero card 안에 `오버런 전리품 보관함` 카드와 `오버런 계속` CTA를 함께 렌더
- body copy도 `오버런으로 이어갈 수 있다`는 의미를 이미 ending에서 많이 소비

**target 이후 역할**
- ending은 결과/정산/버튼 의미까지만 담당
- `themeTrophy` / `enemyTrophy` / `none` flavor는 overrunEvent에서 소비
- `오버런 계속`은 즉시 적용이 아니라 **다음 장면 진입 CTA**로 읽혀야 함

**UI handoff 포인트**
- ending에서 flavor를 더 얹으면 boundary packet 위반이다.
- overrunEvent가 생긴 뒤 ending 카피는 오히려 더 절제돼야 한다.

### 3-4. `renderCenter(summary)`
**현재**
- `battle/shop/reward/ending`만 알고 있음
- default는 library

**target 이후 역할**
- A-1에서는 최소 placeholder라도 `phase='overrunEvent'`를 죽지 않게 처리
- A-3에서는 `renderOverrunEvent(summary)` 독립 분기 추가
- ending과 다른 shell/tone을 강제하는 1차 entry

**프로그래머/UI handoff 포인트**
- `overrunEvent`가 default로 library에 떨어지면 save/load나 render 복구가 조용히 틀어진다.
- A-1 이후엔 최소한 explicit branch가 필요하다.

### 3-5. action dispatcher (`case "continue-overrun"`)
**현재**
- 버튼 클릭 시 바로 `continueOverrun(appState.session)`
- 오디오도 confirm 계열로만 처리

**target 이후 역할**
- A-1~A-3까지는 여전히 enter action으로 유지 가능
- A-4에서 `confirm-overrun-event`가 추가되면 `continue-overrun`과 `confirm-overrun-event` 의미가 갈라짐

**QA handoff 포인트**
- `continue-overrun`은 ending CTA, `confirm-overrun-event`는 장면 카드 CTA라는 두 단계로 나뉘어야 한다.

### 3-6. `smoke-test.mjs` overrun assertions
**현재**
- `continueOverrun()` 직후 reward 적용, 다음 세그먼트 진입, `battle` phase 진입까지 한 번에 기대
- `segmentTrophyTemplateIds` 초기화도 같은 타이밍에 기대

**target 이후 역할**
- A-1: enter만 검증
- A-2: canonical snapshot/normalize/recovery 검증
- A-3: sanity/UI shell 읽힘 검증
- A-4: resolve 1회 적용 + reload guard + old baseline 제거 검증

**QA/프로그래머 handoff 포인트**
- 기존 smoke를 바로 지우지 말고, 단계별 새 fixture로 갈아타야 drift 원인을 분리할 수 있다.

---

# 4. UI surface 경계

## 3줄 요약
- 현재는 ending 화면이 `오버런 보관함`과 `오버런 계속` 의미를 대부분 먹고 있고, `overrunEvent`는 아직 화면에 존재하지 않는다.
- target에서는 ending은 결과 해석, overrunEvent는 장면/flavor/confirm, onboarding은 guidance layer로 층위가 갈라져야 한다.
- 특히 `none` branch는 빈 상태가 아니라 정상 장면으로 읽혀야 한다.

## 한 줄 목표
실구현 중 가장 쉽게 무너지는 shared surface 경계를 다시 고정한다.

| surface | 현재 live 상태 | target 역할 | 금지 패턴 |
| --- | --- | --- | --- |
| ending | 결과 + 정산 + 전리품 보관함 + 오버런 CTA | 결과/정산/버튼 의미 | flavor까지 ending이 먹는 것 |
| overrunEvent | 아직 없음 | 선택한 전리품을 들고 다음 권으로 넘어가는 장면 카드 | reward 선택 화면처럼 보이는 것 |
| onboarding | 아직 문서만 있음 | 흐름을 막지 않는 guidance layer | overrunEvent만큼 큰 phase로 키우는 것 |
| none branch | 현재는 ending 내 선택 없음 허용 정도 | neutral tone의 정상 장면 | 빈 박스/placeholder/오류 상태처럼 보이게 하는 것 |

---

# 5. smoke/current baseline 차이

## 3줄 요약
- 지금 smoke의 핵심 가정은 `continueOverrun()`가 곧바로 다음 세그먼트를 시작한다는 것이다.
- cutover 뒤 smoke의 핵심 가정은 `enter -> snapshot canonicalization -> UI -> confirm resolve`가 단계적으로 분리된다는 것이다.
- 따라서 다음 구현자가 가장 먼저 조심해야 할 건 기능보다도 **검증 기준을 옛 baseline으로 계속 읽는 실수**다.

## 한 줄 목표
현재 smoke와 target smoke가 어디서 갈라지는지 한눈에 보이게 한다.

| 항목 | 현재 smoke 기대 | target smoke 기대 |
| --- | --- | --- |
| `continueOverrun()` 직후 | reward 적용 완료 | `phase='overrunEvent'`, reward 미적용 |
| `currentPage` | 즉시 증가 | A-4 confirm 전까지 유지 |
| `pagePlan.length` | 즉시 증가 | A-4 confirm 전까지 유지 |
| `segmentTrophyTemplateIds` | 즉시 초기화 | A-4 resolve 시점에 초기화 |
| branch identity | reward 결과만 간접 확인 | `contentKey`/`badgeLabel`/`accentKey`/`artTokenSet` 직접 확인 |
| reload guard | 없음 | `resolved` 기준 재실행 금지 |

## QA 즉시 체크리스트
1. A-1 직후에도 reward가 이미 적용됐다면 실패다.
2. A-2에서 title/description string만 맞고 `contentKey`가 없으면 실패다.
3. A-3에서 카드가 보이더라도 ending과 같은 shell이면 실패다.
4. A-4에서 한 번 더 눌러 같은 reward가 다시 들어가면 실패다.

---

# 6. 역할별 handoff 포인트

## 프로그래머
- 현재 코드의 진짜 분해점은 `continueOverrun()`다. 이 함수를 기준으로 enter/apply/progression을 분리한다.
- `chooseEndingOverrunReward()`는 selection 저장까지만 둔다.
- `renderCenter()` explicit branch 추가 없이 엔진만 바꾸면 phase drift를 놓치기 쉽다.

## UI 오너
- 지금 ending이 과하게 많은 말을 하고 있다는 사실 자체가 출발점이다.
- `overrunEvent`는 새 선택 화면이 아니라 **선택한 것을 들고 넘어가는 장면 카드**여야 한다.
- `none`은 빈손 장면이지 빈 화면이 아니다.

## 아트 오너
- 새 장면은 대형 삽화보다도 tone shell과 branch 토큰이 중요하다.
- `neutral none`이 오류처럼 보이지 않게 만드는 것이 실제로 중요하다.

## QA 오너
- 현재 smoke는 old baseline을 고정하고 있으므로, 새 smoke가 붙기 전까지는 옛 assertion에 속기 쉽다.
- phase 분리 여부, reward 1회 적용, reload guard, invalid/missing -> none fallback을 우선 확인한다.

---

# 7. 리스크 & 후속과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| 구현자가 A-1~A-4 문서만 읽고 현재 live code anchor를 다시 추측하는 위험 | 실제 함수 분리 지점이 흔들리고 commit 범위가 부풂 | 이 문서를 현재 runtime anchor 문서로 먼저 읽음 |
| `continueOverrun()`를 한 번에 다 뜯어내려는 위험 | half-cutover, smoke drift | A-step별 책임 재배치 유지 |
| ending 카피가 그대로 남아 overrunEvent 의미를 미리 소비하는 위험 | 장면 카드 존재 이유 약화 | boundary packet 기준으로 ending 문구 절제 |
| old smoke baseline을 늦게 걷는 위험 | 구현은 바뀌었는데 검증은 옛 기준 | 단계별 smoke migration 유지 |

## immediate next actions
1. 다음 구현 턴은 이 문서를 열고 **지금 건드릴 current live anchor가 무엇인지** 먼저 선언한다.
2. A-1 커밋에서는 `continueOverrun()`/action dispatcher/enter smoke만 닫고, page progression은 그대로 옮기지 않는다.
3. A-2부터는 `contentKey`와 normalize를 엔진이 직접 들도록 만들고, render 추론 코드는 금지한다.

---

## 한 줄 결론
이제 `overrunEvent`의 남은 공백은 새 설계가 아니라, **현재 live runtime을 어디서 어떻게 잘라 target cutover에 붙일지에 대한 마지막 코드 앵커 정렬**이다. 이 문서는 그 정렬을 위한 source handoff다.
