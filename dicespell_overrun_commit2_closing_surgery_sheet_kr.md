# DiceSpell `overrunEvent` Commit 2 closing 수술 시트

## 3줄 요약
- 이 문서는 `overrunEvent` A-track의 실제 두 번째 코드 컷오버를 **지금 코드 기준 line-anchored closing surgery sheet**로 다시 압축한 source handoff다.
- 기존 `second runtime slice`, `second runtime review`, `runtime fixture ledger`, `cutover gate`, `gap ledger`가 Commit 2의 범위와 종료선을 충분히 닫아 둔 상태에서, 아직 남아 있던 마지막 구현 병목은 `좋아, 그래서 Commit 2에서는 정확히 어느 줄을 어떻게 closing으로 바꿔야 하지?`였다.
- 목표는 프로그래머 / UI / 아트 / QA / PM이 같은 line anchor, 같은 closing 순서, 같은 비혼입선, 같은 제출 증거로 `scene card + confirm resolve + old baseline 제거`를 한 커밋 경계로 읽게 만드는 것이다.

## 한 줄 목표
현재 live baseline의 `ending -> continueOverrun() -> 즉시 reward apply -> 세그먼트 전진` 체인을 **Commit 2 closing 수술 순서**로 재배열해, 구현자가 더 이상 문서 여러 장과 소스를 동시에 재해석하지 않게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell 문서 세트는 이미 충분히 강하다.

- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_runtime_fixture_ledger_kr.md`
- `docs/dicespell_overrun_cutover_gate_packet_kr.md`
- `docs/dicespell_overrun_onboarding_gap_ledger_kr.md`

문제는 설계 공백이 아니라 **두 번째 실제 코드 커밋의 closing 절개 지점**이다.

1. `game-engine.js`의 `continueOverrun()`는 아직 reward 적용, 세그먼트 확장, 다음 페이지 진입을 한 함수에 그대로 들고 있어 Commit 2에서 `resolve`와 `enter`가 분리된 shape로 닫혀 있지 않다.
2. `app.js`는 `continue-overrun` 액션을 여전히 즉시 progression 함수로 꽂고 있고, `renderCenter()`는 아직 `overrunEvent` branch를 모르기 때문에 장면 카드 closing anchor가 비어 있다.
3. `smoke-test.mjs`는 여전히 old immediate-apply baseline을 기대하고 있어, Commit 2에서 닫아야 할 `S4/S5/S6/S10/S12` closing 증거가 실제 코드 anchor와 함께 묶여 있지 않다.
4. 문서는 충분하지만 implementer는 결국 `정확히 어디를 closing용으로 잘라야 안전한가`를 다시 grep하게 된다.

즉 지금 필요한 것은 새 방향 문서가 아니라, **현재 line anchor + closing 순서 + 혼입 금지선 + 제출 증거**를 같은 화면에 묶은 수술 시트다.

---

## 이 문서를 누가 먼저 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 얻어야 하는 결론 |
| --- | --- | --- |
| 프로그래머 | `1. live closing anchor`, `2. Commit 2 수술 순서`, `3. 제거/보존 체크` | 두 번째 커밋은 `scene card + confirm resolve + old baseline 제거`만 닫고 onboarding은 열지 않는다 |
| UI | `4. UI closing anchor` | Commit 2는 첫 shell 마감 턴이지만 branch 재추론/전역 리디자인 턴은 아니다 |
| 아트 | `4. UI closing anchor` | `artTokenSet`/`accentKey` drift 없이 theme/enemy/none 3축 장면성만 닫는다 |
| QA | `5. QA evidence bundle` | `S4/S5/S6/S10/S12`만 Commit 2 closing 증거이며 old immediate assertions는 남겨 두면 안 된다 |
| PM/기획 | `0. first screen 결론`, `6. 리스크 & 후속과제` | 이번 단계는 `runtime-ready closing green`이며 B-track은 그 뒤에만 열린다 |

---

## 0. first screen 결론

### 왜 이 문서를 열어야 하나
- **프로그래머**: Commit 2에서 `continueOverrun()`를 어디서 분리하고 어떤 새 `resolve` 경계를 만들어야 하는지 바로 확인하려고 연다.
- **UI/아트**: `renderCenter()`와 `renderOverrunEvent(summary)`가 어디서 닫혀야 ending과 다른 장면 카드로 읽히는지 확인하려고 연다.
- **QA/PM**: `runtime-ready closing green`을 무엇으로 증명하고, 무엇이 아직 금지인지 같은 문장으로 기록하려고 연다.

### 가장 짧은 답
- Commit 2는 `renderCenter()` explicit `overrunEvent` branch + `renderOverrunEvent(summary)` shell + `confirm-overrun-event` action + `resolveOverrunEvent(state)` 또는 동등 함수 + old immediate smoke 제거까지만 닫는다.
- reward apply / `overrunLevel` 증가 / `pagePlan` 확장 / `currentPage` 증가 / `segmentTrophyTemplateIds` 초기화는 **confirm 시점에서만** 일어나야 한다.
- B-track primer helper, `meta.uiGuidance`, 다른 surface polishing, 새 reward 타입은 **전부 금지**다.

---

## 1. live closing anchor

## 3줄 요약
- 아래 anchor는 지금 실제 코드에서 Commit 2 closing 수술이 걸리는 지점이다.
- 이 문서는 추상 함수명만 적지 않고, 현재 live baseline이 어느 라인대에서 old immediate-apply를 수행하는지까지 같이 고정한다.
- line number는 현재 2026-03-18 06:00 KST 기준 workspace 상태를 기준으로 한다.

**한 줄 목표:** implementer가 다시 grep하지 않고도 `어디서 closing으로 잘라야 하는지`를 바로 볼 수 있게 만든다.

| 파일 | 현재 anchor | 현재 의미 | Commit 2에서 해야 할 일 |
| --- | --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `2978-3009` | `continueOverrun()`가 아직 reward apply → `overrunLevel += 1` → `segmentTrophyTemplateIds=[]` → `pagePlan.push(...)` → `currentPage += 1` → `phase='library'` → `maybeEnterNextPage()`까지 즉시 수행 | **즉시 적용 체인을 closing용 함수 경계로 분리**해 `enter`와 `confirm resolve` 책임을 갈라 놓는다 |
| `dice-spell-standalone/src/app.js` | `1991-2005` | `renderCenter()`가 `battle/shop/reward/ending`만 분기 | explicit `overrunEvent` branch와 `renderOverrunEvent(summary)` 연결을 추가한다 |
| `dice-spell-standalone/src/app.js` | `1978-1984` | `renderEnding(summary)` 안에서 `continue-overrun` CTA가 아직 즉시 progression 문맥으로 읽힌다 | ending은 결과/CTA 의미까지만 남기고 실제 장면은 `overrunEvent` branch로 넘긴다 |
| `dice-spell-standalone/src/app.js` | `2296-2298` | `continue-overrun` 액션이 여전히 `continueOverrun(appState.session)`를 직접 호출 | Commit 2에서는 `enter action`과 `confirm action`을 분리한다 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | `1294-1409` | `continueOverrun()` 직후 reward 적용/세그먼트 전진/전리품 이력 초기화를 기대 | old immediate assertions를 제거하고 `S4/S5/S6/S10/S12` closing smoke로 갈아탄다 |

### 절개 해석
- `game-engine.js 2978-3009`는 여전히 Commit 2의 핵심 병목이다. 다만 이번에는 `무엇을 지울까`보다 `무엇을 `resolve`로 옮길까`가 중요하다.
- `app.js 1991-2005`, `1978-1984`, `2296-2298`은 엔진 closing이 실제 UI/행동 semantics로 읽히게 만드는 최소 surface다.
- `smoke-test.mjs 1294-1409`를 같이 안 자르면 Commit 2는 구현돼도 여전히 old baseline 때문에 closing green처럼 기록될 수 없다.

---

## 2. Commit 2 수술 순서

## 3줄 요약
- Commit 2는 리팩터링이 아니라 **closing 순서 고정**이 중요하다.
- 잘못된 순서는 `UI만 있는데 resolve가 없는 half-closing` 또는 `resolve는 됐는데 still ending/result screen처럼 보이는 drift`를 만든다.
- 따라서 아래 순서는 구현 순서이자 리뷰 순서다.

**한 줄 목표:** `explicit branch -> scene card shell -> confirm resolve -> closing smoke` 순서만 허용한다.

### Step 0. Commit 1 floor를 전제로 건다
Commit 2 시작 전 아래가 이미 참이어야 한다.

- canonical field set 존재: `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved`
- `phase='overrunEvent'` 또는 동등 enter floor로 안전하게 진입 가능
- reward 미적용 / `currentPage` 미전진 / `pagePlan` 미확장 / `segmentTrophyTemplateIds` 미초기화 상태가 먼저 닫혀 있음

### Step 1. `renderCenter()`에 explicit branch를 닫는다
Commit 2 UI의 최소 책임은 아래다.

- `phase='overrunEvent'`일 때 library default로 떨어지지 않음
- `renderOverrunEvent(summary)` 또는 동등 함수가 canonical payload를 display-only로 소비함
- branch 판정이 `reward.title` / `sourceType` / body-copy 문자열 재추론에 기대지 않음

### Step 2. `renderOverrunEvent(summary)` 장면 카드를 닫는다
필수 위계:
- `badge`
- `header`
- `flavor`
- `reward summary`
- `CTA`
- `footnote`

필수 규칙:
- theme/enemy/none 3축 공통 density 유지
- ending shell과 다른 focus card / tone / spacing 사용
- `none` branch도 placeholder가 아니라 정상 장면 branch로 보여야 함

### Step 3. `confirm-overrun-event` action과 `resolve` 경계를 분리한다
Commit 2 후 허용 책임은 아래뿐이다.

- `continue-overrun`는 enter-only action으로 유지하거나 동등 enter action으로 수렴
- `confirm-overrun-event`가 실제 reward apply / progression / cleanup을 담당
- `resolveOverrunEvent(state)` 또는 동등 함수가 아래를 한 번만 수행
  - reward 1회 적용
  - `none` 무보상 진행
  - `state.overrunLevel += 1`
  - `state.segmentTrophyTemplateIds = []`
  - `state.pagePlan.push(...)`
  - `state.currentPage += 1`
  - `state.phase = 'library'` 이후 다음 phase 재진입
  - `resolved` guard/no-op recovery

### Step 4. old smoke를 closing smoke로 치환한다
Commit 2 smoke는 아래 증거를 남겨야 한다.

- `S4/S5/S6`: theme/enemy/none confirm resolve 3종
- `S10`: resolved/reclick/reload no-op guard
- `S12`: theme/enemy/none visual density + ending 대비 shell sanity

Commit 2 smoke에서 확인할 것:
- confirm 전 reward 미적용
- confirm 후 reward 정확히 1회 적용
- `currentPage` / `pagePlan.length` / `overrunLevel` / `segmentTrophyTemplateIds`가 confirm 시점에만 바뀜
- old immediate-apply assertion 제거
- `renderCenter()` explicit branch가 library fall-through를 만들지 않음

---

## 3. 제거/보존 체크

## 한 줄 목표
`무엇을 새로 추가하는가`보다 `무엇을 이번 커밋에서 더 이상 남기면 안 되는가`와 `무엇을 아직 섞지 말아야 하는가`를 고정한다.

| 구분 | Commit 2에서 제거/축소 | Commit 2에서 보존 | 이유 |
| --- | --- | --- | --- |
| 엔진 progression | `continueOverrun()` 내부 즉시 apply/progression monolith | canonical snapshot payload, enter floor, `selectedRewardId` 입력값 | enter와 resolve 책임을 분리하기 위해 |
| render routing | library default fall-through, ending 내 장면 소비 | explicit `overrunEvent` branch, ending의 결과/CTA 의미 | ending/overrun 경계를 runtime으로 닫기 위해 |
| CTA wiring | `continue-overrun` = 즉시 progression 의미 | `continue-overrun` enter 의미 + `confirm-overrun-event` resolve 의미 | 한 버튼에 두 책임이 남지 않게 하기 위해 |
| smoke baseline | `continueOverrun()` 직후 apply 기대 | confirm 전/후 diff + closing fixtures | Commit 2를 old baseline에서 분리하기 위해 |
| 범위 | onboarding primer helper/state/UI, 새 reward 타입, 전역 테마 리뉴얼 | overrun scene/resolve closing | Commit 2 범위 팽창 방지 |

### Commit 2 완료 문장
> 이번 커밋은 `overrunEvent`의 장면 카드와 confirm resolve를 닫아, reward 1회 적용·`none` 무보상 진행·`resolved` guard·old immediate baseline 제거까지 포함한 runtime closing을 마무리했다.

---

## 4. UI closing anchor

## 3줄 요약
- Commit 2는 UI/아트가 실제로 closing에 들어가는 첫 턴이다.
- 하지만 이 단계의 핵심은 새 시각 시스템이 아니라, canonical payload를 **다시 만들지 않고 읽히게 닫는 것**이다.
- 이 선을 넘으면 A-track closing에 B-track 또는 전역 리디자인이 섞인다.

**한 줄 목표:** UI/아트가 장면 마감은 하되 branch 재조합과 범위 팽창은 하지 않게 만든다.

### UI 오너에게 넘길 것
- `renderOverrunEvent(summary)`는 snapshot field를 display-only로 소비한다.
- 장면 카드는 `badge → header → flavor → reward summary → CTA → footnote` 6요소로 고정한다.
- `themeTrophy` / `enemyTrophy` / `none`는 같은 구조 안에서 tone만 달라지고, ending보다 한 단계 장면적으로 읽혀야 한다.
- confirm 직후 stale CTA/old ending card 잔상은 남으면 안 된다.

### UI 오너가 이번 턴에 하지 않을 것
- primer helper 주입
- library/battle/reward/shop/ending surface 수정
- 전역 card system / spacing / token rework

### 아트 오너에게 넘길 것
- Commit 2에서는 새 대형 삽화보다 `artTokenSet`/`accentKey`/`badgeLabel` drift 제거가 우선이다.
- `none` branch는 neutral tone이지만 빈 상태나 오류 카드처럼 보이면 안 된다.
- ending 자산 강화는 이번 턴 범위가 아니다. overrunEvent 장면성만 닫는다.

---

## 5. QA evidence bundle

## 한 줄 목표
Commit 2 green을 `화면이 보인다`나 `버튼이 눌린다` 수준으로 기록하지 않게 만든다.

### Commit 2 필수 evidence
1. `S4/S5/S6/S10/S12` 결과
2. 상태 diff 메모
   - confirm 전 `resolved=false`
   - confirm 후 reward 정확히 1회 적용
   - `currentPage` / `pagePlan.length` / `overrunLevel` / `segmentTrophyTemplateIds`가 confirm 시점에만 변경
3. UI/DOM evidence
   - theme/enemy/none 3축 카드 위계
   - ending 대비 shell 차이
   - CTA/footnote 존재
4. handoff 문장
   - `이번 턴은 Commit 2 runtime-ready closing green이며, 다음은 B-1 library primer만 연다.`

### Commit 2 fail 신호
- confirm 전에 reward가 이미 적용된다.
- reload/reclick로 reward가 두 번 적용된다.
- `none` branch가 placeholder처럼 비거나 진행 의미가 없다.
- `renderCenter()`가 `overrunEvent`를 몰라 library로 떨어진다.
- smoke 이름은 바뀌었지만 실제론 old immediate assertions를 그대로 재사용한다.
- onboarding state/helper/UI가 같이 들어온다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 문서의 대응 | 다음 후속 |
| --- | --- | --- | --- |
| `continueOverrun()` 절개와 `resolve` 절개가 따로 움직이는 위험 | enter/closing 책임이 다시 섞여 half-cutover가 남는다 | line anchor와 Step 0~4를 한 장으로 고정 | 실제 Commit 2 구현 턴에서 엔진/앱/스타일/스모크를 같은 커밋으로 묶는다 |
| `renderOverrunEvent(summary)` 없이 phase만 추가하는 위험 | overrunEvent가 여전히 ending/result screen처럼 읽힌다 | `app.js 1991-2005`/`1978-1984` anchor를 Commit 2 필수 surface로 명시 | Commit 2 리뷰에서 ending-vs-overrun 경계를 별도 체크한다 |
| UI가 payload floor를 넘어 content를 다시 조합하는 위험 | badge/accent/header drift, summary/body 중복 | UI closing anchor와 금지 패턴을 명시 | Commit 2 착수 전 canonical field set 8개 존재를 다시 확인한다 |
| old immediate smoke 일부가 남는 위험 | closing green 뒤에도 중복 적용/회귀 해석이 남는다 | `smoke-test.mjs 1294-1409` anchor와 `S4/S5/S6/S10/S12`를 함께 고정 | Commit 2 구현 직후 old baseline grep과 smoke 결과를 같이 확인한다 |
| Commit 2에 B-track 온보딩이 섞이는 위험 | 범위 팽창, 검수선 붕괴 | 제거/보존 체크에 onboarding 비혼입선을 명시 | Commit 2 green 뒤에만 B-1 packet을 연다 |

---

## 7. immediate next actions
1. 다음 구현자는 `cutover gate -> second runtime slice -> second runtime review -> runtime fixture ledger -> 이 surgery sheet` 순서로 Commit 2 범위를 다시 잠근다.
2. 실제 코드 변경은 `game-engine.js`, `app.js`, `styles.css`, `smoke-test.mjs` 네 파일을 같은 커밋 경계로 묶는다.
3. tracker/run log에는 `Commit 2 runtime-ready closing green` 문장만 남기고 `오버런 전체 green` 같은 큰 표현은 금지한다.
4. Commit 2 green 뒤에만 `first_run_onboarding_runtime_stack_packet -> handoff_scorecard -> owner_workboard -> B-1 library packet` 순서로 B-track을 연다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 두 번째 실구현 커밋을 `line-anchored closing surgery`로 다시 고정해, 프로그래머/UI/아트/QA/PM이 같은 절개 지점과 같은 비혼입선으로 Commit 2를 닫게 만드는 source handoff다.
