# DiceSpell `overrunEvent` Commit 1 floor 수술 시트

## 3줄 요약
- 이 문서는 `overrunEvent` A-track의 실제 첫 코드 컷오버를 **지금 코드 기준 line-anchored surgery sheet**로 다시 압축한 source handoff다.
- 기존 `runtime patch map`, `state matrix`, `fixture ledger`, `gap ledger`가 범위와 증거를 충분히 닫아 둔 상태에서, 아직 남아 있던 마지막 구현 병목은 `좋아, 그래서 Commit 1에서 정확히 어느 줄을 어떻게 잘라야 하지?`였다.
- 목표는 `continueOverrun()` 즉시 적용 체인을 `enter-only floor`로 줄이는 첫 수술을 프로그래머 / UI / 아트 / QA / PM이 같은 anchor, 같은 비변경선, 같은 제출 묶음으로 읽게 만드는 것이다.

## 한 줄 목표
현재 live baseline의 `ending -> continueOverrun() -> 즉시 reward apply -> 세그먼트 전진` 체인을 **Commit 1 floor 수술 순서**로 재배열해, 구현자가 더 이상 문서 여러 장과 소스를 동시에 재해석하지 않게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell 문서 세트는 이미 강하다.

- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_runtime_fixture_ledger_kr.md`
- `docs/dicespell_overrun_onboarding_gap_ledger_kr.md`

문제는 설계 공백이 아니라 **첫 실제 코드 커밋의 절개 지점**이다.

1. `game-engine.js`의 `continueOverrun()`는 아직 reward 적용, 세그먼트 확장, phase 전환, 다음 페이지 진입을 한 함수에 같이 들고 있다.
2. `app.js`는 `continue-overrun` 액션을 그대로 그 함수에 꽂고 있고, `renderCenter()`는 아직 `overrunEvent` branch를 모른다.
3. `smoke-test.mjs`는 여전히 old immediate-apply baseline을 기대한다.
4. 문서는 충분하지만, implementer는 결국 `정확히 어디를 먼저 잘라야 안전한가`를 다시 grep하게 된다.

즉 지금 필요한 것은 새 방향 문서가 아니라, **현재 line anchor + 절개 순서 + 비변경선 + 제출 증거**를 같은 화면에 묶은 수술 시트다.

---

## 이 문서를 누가 먼저 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 얻어야 하는 결론 |
| --- | --- | --- |
| 프로그래머 | `1. live 절개 anchor`, `2. Commit 1 수술 순서`, `3. 제거/보존 체크` | 첫 커밋은 `enter-only floor`까지만 닫고 progression은 열지 않는다 |
| UI | `4. UI/아트 대기선` | Commit 1은 shell 마감 턴이 아니라 branch payload floor를 받는 턴이다 |
| 아트 | `4. UI/아트 대기선` | `artTokenSet` freeze 전까지는 대형 자산보다 token sanity만 본다 |
| QA | `5. QA evidence bundle` | `S1/S2/S3/S7~S11`만 Commit 1에 속하고, old immediate assertions는 남겨 두면 안 된다 |
| PM/기획 | `0. first screen 결론`, `6. 리스크 & 후속과제` | 이번 단계는 runtime-ready가 아니라 floor green이다 |

---

## 0. first screen 결론

### 왜 이 문서를 열어야 하나
- **프로그래머**: `continueOverrun()`를 어디서 자르고 어디는 아직 건드리지 말아야 하는지 바로 확인하려고 연다.
- **UI/아트**: Commit 1이 실제 장면 완성 턴이 아니라 payload floor를 넘겨받는 턴인지 확인하려고 연다.
- **QA/PM**: `floor green`을 무엇으로 증명하고, 무엇이 아직 열리지 않았는지 같은 문장으로 기록하려고 연다.

### 가장 짧은 답
- Commit 1은 `phase='overrunEvent'` 진입 + canonical snapshot floor + recovery + non-change line까지만 닫는다.
- reward apply / `currentPage` 증가 / `pagePlan` 확장 / `segmentTrophyTemplateIds` 초기화 / `maybeEnterNextPage()` 재진입은 **모두 금지**다.
- `renderCenter()`에는 최소한 explicit `overrunEvent` branch가 필요하지만, Commit 1에서 장면 카드 완성까지 가면 범위가 부푼다.

---

## 1. live 절개 anchor

## 3줄 요약
- 아래 anchor는 지금 실제 코드에서 Commit 1 수술이 걸리는 지점이다.
- 이 문서는 추상 함수명만 적지 않고, 현재 live baseline이 어느 라인대에서 immediate-apply를 수행하는지까지 같이 고정한다.
- line number는 현재 2026-03-18 05:30 KST 기준 workspace 상태를 기준으로 한다.

**한 줄 목표:** implementer가 다시 grep하지 않고도 `어디서 잘라야 하는지`를 바로 볼 수 있게 만든다.

| 파일 | 현재 anchor | 현재 의미 | Commit 1에서 해야 할 일 |
| --- | --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `2978-3009` | `continueOverrun()`가 reward apply → `overrunLevel += 1` → `segmentTrophyTemplateIds=[]` → `pagePlan.push(...)` → `currentPage += 1` → `phase='library'` → `maybeEnterNextPage()`까지 즉시 수행 | **즉시 적용 체인을 잘라** `phase='overrunEvent'` 진입 + canonical snapshot 생성까지만 남긴다 |
| `dice-spell-standalone/src/app.js` | `2296-2298` | `case "continue-overrun"`이 즉시 `continueOverrun(appState.session)` 호출 | action 의미는 유지하되 Commit 1에서는 `enter action`으로만 쓰이게 한다 |
| `dice-spell-standalone/src/app.js` | `1995-2005` | `renderCenter()`가 `battle/shop/reward/ending`만 분기 | 최소한 `overrunEvent` explicit branch 또는 동등 fall-safe surface를 추가한다 |
| `dice-spell-standalone/src/app.js` | `1903-1979` 인근 | `renderEnding(summary)`가 아직 overrun draft와 CTA 문맥을 ending 안에서 크게 소비 | Commit 1에서는 카피 대수술 대신 `ending`과 `overrunEvent`가 곧 분리된다는 전제만 깨지지 않게 유지 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | `1294-1409` | `continueOverrun()` 직후 reward 적용/세그먼트 전진/전리품 이력 초기화를 기대 | Commit 1 smoke는 old immediate assertions를 floor assertions로 치환해야 한다 |

### 절개 해석
- `game-engine.js 2978-3009`가 핵심 병목이다.
- `app.js 2296-2298`과 `1995-2005`는 엔진 절개가 화면/세이브에서 조용히 새지 않게 막는 최소 보강 지점이다.
- `smoke-test.mjs 1294-1409`를 같이 안 자르면 Commit 1은 구현돼도 old baseline 때문에 곧바로 실패처럼 보인다.

---

## 2. Commit 1 수술 순서

## 3줄 요약
- Commit 1은 리팩터링이 아니라 **수술 순서 고정**이 중요하다.
- 잘못된 순서는 `phase만 바뀌고 old progression이 남는 half-cutover` 또는 `snapshot 없이 UI placeholder로 때우는 drift`를 만든다.
- 따라서 아래 순서는 구현 순서이자 리뷰 순서다.

**한 줄 목표:** `enter wrapper -> canonical snapshot -> explicit branch -> floor smoke` 순서만 허용한다.

### Step 0. canonical helper 자리부터 만든다
추천 위치는 `chooseEndingOverrunReward()` 근처 또는 `continueOverrun()` 바로 위다.

필수 helper 책임:
- `buildCanonicalOverrunEventSnapshot(state, selectedReward)` 또는 동등 helper
- `buildNoneOverrunEventSnapshot(state)` 또는 동등 helper
- `normalizeOverrunEventState(state)` 또는 동등 helper

필수 field set:
- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `flavorText`
- `confirmLabel`
- `resolved`

### Step 1. `continueOverrun()`를 enter-only wrapper로 줄인다
Commit 1 후 `continueOverrun()`의 허용 책임은 아래뿐이다.

- ending phase guard
- selected reward lookup 입력값 읽기
- canonical snapshot 또는 `none` fallback snapshot 생성
- `state.overrunEvent = ...`
- `state.phase = 'overrunEvent'`
- recovery/normalize 수렴
- 최소 로그 또는 동등 상태 기록

Commit 1에서 **절대 남기면 안 되는 줄**:
- `applyEffectBundle(...)`
- `state.overrunLevel += 1`
- `state.segmentTrophyTemplateIds = []`
- `state.pagePlan.push(...)`
- `state.currentPage += 1`
- `state.phase = 'library'`
- `maybeEnterNextPage(state)`

### Step 2. `renderCenter()`에 explicit branch를 추가한다
Commit 1 UI는 완성형 카드가 아니라도 괜찮다. 하지만 아래는 필요하다.

- `phase='overrunEvent'`일 때 library default로 떨어지지 않음
- `state.overrunEvent` canonical payload를 읽을 수 있는 explicit branch 존재
- branch 판정이 `reward.title` / `sourceType` 재추론에 기대지 않음

Commit 1에서 **일부러 하지 말 것**:
- ending shell 대개편
- `themeTrophy` / `enemyTrophy` / `none` full visual polish
- confirm CTA 실제 resolve wiring

### Step 3. old smoke를 floor smoke로 치환한다
Commit 1 smoke는 아래 증거를 남겨야 한다.

- `S1/S2/S3`: enter path 3종
- `S7/S8/S9`: invalid/missing/broken recovery 3종
- `S10`: resolved/no-op floor guard 바닥
- `S11`: missing copy reinjection 또는 동등 copy survival check

Commit 1 smoke에서 확인할 것:
- reward 미적용
- `currentPage` 미전진
- `pagePlan.length` 미확장
- `segmentTrophyTemplateIds` 미초기화
- `phase='overrunEvent'`
- canonical field set 존재

---

## 3. 제거/보존 체크

## 한 줄 목표
`무엇을 새로 추가하는가`보다 `무엇을 이번 커밋에서 지우거나 그대로 둬야 하는가`를 고정한다.

| 구분 | Commit 1에서 제거/축소 | Commit 1에서 보존 | 이유 |
| --- | --- | --- | --- |
| 엔진 progression | immediate apply / page advance / segment cleanup / auto-enter | ending 선택 저장, canonical snapshot 입력값 읽기 | floor와 closing을 분리하기 위해 |
| render routing | library default fall-through | explicit `overrunEvent` branch | save/load와 render drift 방지 |
| overrun draft | ending 내 선택 저장만 | `selectedRewardId` 자체는 입력값으로 유지 | selection과 resolve 책임 분리 |
| smoke baseline | immediate-apply assertion | enter/recovery/non-change line assertion | Commit 1이 old baseline에 묶이지 않게 |
| UI scope | ending 재디자인, full scene polish | 최소 fall-safe surface, payload survival | Commit 1 범위 팽창 방지 |

### Commit 1 완료 문장
> 이번 커밋은 `continueOverrun()` 즉시 적용 체인을 `overrunEvent` enter floor로 줄이고, canonical snapshot/recovery/non-change line까지만 닫았다.

---

## 4. UI/아트 대기선

## 3줄 요약
- Commit 1은 UI/아트가 장면 마감을 하는 턴이 아니다.
- 대신 이 단계에서 받아야 하는 것은 `payload가 언제부터 신뢰 가능한가`에 대한 정확한 floor다.
- 이 대기선을 안 지키면 A-3에서 shell이 아니라 branch 판정까지 UI가 떠안게 된다.

**한 줄 목표:** UI/아트가 너무 일찍 closing 감각으로 들어가지 않게 만든다.

### UI 오너에게 넘길 것
- `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved`가 엔진 snapshot에 직접 들어온다.
- Commit 1 surface는 placeholder여도 되지만 **payload identity는 placeholder면 안 된다.**
- `none` branch도 빈 상태가 아니라 정상 snapshot으로 살아 있어야 한다.

### 아트 오너에게 넘길 것
- Commit 1에서는 새 대형 삽화보다 `artTokenSet` freeze 여부가 우선이다.
- `themeTrophy` / `enemyTrophy` / `none`의 토큰 정체성이 payload에 직접 들어와야 한다.
- ending 화면 자산과 overrunEvent 장면 자산을 아직 합치지 않는다.

---

## 5. QA evidence bundle

## 한 줄 목표
Commit 1 green을 old smoke 통과나 `화면이 보인다`로 기록하지 않게 만든다.

### Commit 1 필수 evidence
1. `S1/S2/S3/S7/S8/S9/S10/S11` 결과
2. 상태 diff 메모
   - `phase='overrunEvent'`
   - `state.overrunEvent` canonical field set 존재
   - reward 미적용
   - `currentPage` / `pagePlan.length` / `segmentTrophyTemplateIds` 미변경
3. render fallback 방지 메모
   - library default fall-through 아님
   - branch 재추론 아님
4. handoff 문장
   - `이번 턴은 Commit 1 floor green이며 progression은 아직 열지 않았다.`

### Commit 1 fail 신호
- `continueOverrun()` 직후 reward가 이미 적용된다.
- `currentPage` 또는 `pagePlan`이 움직인다.
- `segmentTrophyTemplateIds`가 비워진다.
- `state.overrunEvent`가 partial이다.
- `renderCenter()`가 `overrunEvent`를 몰라 library로 떨어진다.
- smoke 이름은 바뀌었지만 실제론 old immediate assertions를 그대로 재사용한다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 문서의 대응 | 다음 후속 |
| --- | --- | --- | --- |
| `continueOverrun()` 절개와 smoke 절개가 따로 움직이는 위험 | Commit 1이 구현돼도 검증이 old baseline에 묶인다 | line anchor와 Step 0~3를 한 장으로 고정 | 실제 Commit 1 구현 턴에서 코드/테스트를 같은 커밋으로 묶는다 |
| `overrunEvent` explicit branch 없이 phase만 추가하는 위험 | save/load 시 조용한 library fall-through 발생 | `app.js 1995-2005` anchor를 Commit 1 필수 surface로 명시 | A-1 리뷰에서 branch 누락 여부를 별도 체크한다 |
| UI가 payload floor 전에 shell 마감으로 들어가는 위험 | branch identity drift, title/sourceType 재추론 재발 | UI/아트 대기선과 금지 패턴을 명시 | Commit 2 착수 전 payload field set 8개 존재를 다시 확인한다 |
| `none` fallback을 보조 케이스로 미루는 위험 | invalid/missing 저장 상태가 곧바로 crash나 빈 화면이 된다 | `buildNoneOverrunEventSnapshot()`와 recovery smoke를 Commit 1 필수로 고정 | A-2 이전에도 `none` branch를 same-priority로 검수한다 |

---

## 7. immediate next actions
1. 다음 구현자는 `runtime patch map -> first runtime slice -> state matrix -> fixture ledger -> 이 surgery sheet` 순서로 Commit 1 범위를 다시 잠근다.
2. 실제 코드 변경은 `game-engine.js`, `app.js`, `smoke-test.mjs` 세 파일을 같은 커밋 경계로 묶는다.
3. tracker/run log에는 `Commit 1 floor` 문장만 남기고 `runtime-ready` 표현은 금지한다.
4. Commit 1 green 뒤에만 A-3/A-4 closing packet으로 넘어간다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 첫 실구현 커밋을 `line-anchored enter floor surgery`로 다시 고정해, 프로그래머/UI/아트/QA/PM이 같은 절개 지점과 같은 비변경선으로 Commit 1을 닫게 만드는 source handoff다.
