# DiceSpell `overrunEvent` Commit 2 closing 수술 시트 요약

## 3줄 요약
- 이 문서는 `overrunEvent` 두 번째 실구현 커밋에서 **정확히 어디를 closing으로 잘라야 하고 무엇은 아직 섞으면 안 되는지**를 빠르게 읽기 위한 companion view다.
- 핵심은 `renderCenter()`/`renderOverrunEvent(summary)`의 scene card closing, `confirm-overrun-event`의 resolve 분리, old immediate smoke 제거를 한 커밋 경계로 묶는 것이다.
- 프로그래머는 line anchor와 closing 순서를, UI/아트는 장면 카드 마감선과 비혼입선을, QA/PM은 `S4/S5/S6/S10/S12` evidence 묶음을 먼저 보면 된다.

**한 줄 목표:** Commit 2를 `line-anchored closing surgery`로 다시 압축해, 더 이상 `좋아, 그럼 어디서부터 runtime-ready라고 말하지?`를 감으로 정하지 않게 만든다.

---

## first screen

### 지금 가장 중요한 결론
- `src/game-engine.js 2978-3009`의 `continueOverrun()`는 Commit 2에서도 핵심 절개 지점이다. 다만 이번엔 `enter`와 `resolve`를 분리해야 한다.
- `src/app.js 1991-2005`, `1978-1984`, `2296-2298`는 `overrunEvent`를 ending과 다른 장면 카드 + confirm action으로 읽히게 만드는 필수 UI anchor다.
- 이 커밋에서 허용되는 변화는 `scene card + confirm resolve + old baseline 제거`뿐이며, onboarding B-track은 여전히 금지다.

### 누가 무엇부터 읽어야 하나
| 역할 | 먼저 볼 것 | 바로 얻어야 하는 결론 |
| --- | --- | --- |
| 프로그래머 | line anchor + 수술 순서 | 두 번째 커밋은 runtime-ready closing만 닫는다 |
| UI | closing anchor | ending과 다른 장면 카드 위계만 닫는다 |
| 아트 | closing anchor | token/accent drift 없는 3축 장면성만 마감한다 |
| QA | evidence bundle | `S4/S5/S6/S10/S12`만 Commit 2 green이다 |
| PM | 완료 문장 | `runtime-ready closing green` 뒤에만 B-1을 연다 |

---

## Commit 2에서 직접 자를 anchor

| 파일 | 현재 anchor | Commit 2에서 해야 할 일 |
| --- | --- | --- |
| `src/game-engine.js` | `2978-3009` | `continueOverrun()` monolith를 enter/resolve 경계로 분리 |
| `src/app.js` | `1991-2005` | `renderCenter()`에 `overrunEvent` explicit branch 추가 |
| `src/app.js` | `1978-1984` | ending은 결과/CTA 의미만 남기고 장면은 `overrunEvent`로 넘기기 |
| `src/app.js` | `2296-2298` | `continue-overrun`과 `confirm-overrun-event` action 의미 분리 |
| `scripts/smoke-test.mjs` | `1294-1409` | old immediate assertions를 `S4/S5/S6/S10/S12` closing smoke로 교체 |

---

## Commit 2 수술 순서
1. Commit 1 floor가 이미 닫혔는지 먼저 확인한다.
2. `renderCenter()`가 `overrunEvent`를 library default로 흘리지 않게 explicit branch를 둔다.
3. `renderOverrunEvent(summary)`가 `badge -> header -> flavor -> reward summary -> CTA -> footnote` 6요소를 display-only로 렌더하게 한다.
4. `confirm-overrun-event`와 `resolveOverrunEvent(state)` 또는 동등 함수를 분리해 reward 1회 적용 / progression / cleanup을 confirm 시점에만 수행하게 만든다.
5. smoke를 `S4/S5/S6/S10/S12` closing 세트로 갈아타고 old immediate baseline을 제거한다.

---

## 이번 커밋에서 지우거나 남겨야 할 것

### 제거/축소
- `continueOverrun()` 안의 즉시 reward apply/progression monolith
- ending 안에서 장면을 같이 소비하는 흐름
- old immediate-apply smoke assertion
- onboarding helper/state/UI 혼입

### 보존
- canonical snapshot field set 8개
- `selectedRewardId` 입력값 자체
- ending의 결과/CTA 의미
- `none` fallback branch와 `resolved` guard

---

## UI/아트 closing line
- Commit 2는 UI/아트가 실제 closing에 들어가는 첫 턴이다.
- 하지만 payload를 다시 만들면 안 된다. `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved`를 display-only로 소비해야 한다.
- `themeTrophy` / `enemyTrophy` / `none`는 같은 카드 구조 안에서 tone만 달라져야 하며, `none`도 placeholder처럼 보이면 실패다.
- 이번 턴에는 primer helper, 다른 surface 수정, 전역 card system 리뉴얼을 열지 않는다.

---

## QA quick check
- confirm 전 reward 미적용
- confirm 후 reward 1회 적용
- `currentPage` / `pagePlan.length` / `overrunLevel` / `segmentTrophyTemplateIds`는 confirm 시점에만 변함
- `S4/S5/S6/S10/S12`만 green
- ending 대비 shell 차이와 CTA/footnote 존재
- old immediate baseline 제거 완료

### 실패 신호
- confirm 전에 reward가 적용됨
- reload/reclick로 reward가 두 번 적용됨
- `none` branch가 비어 보임
- `renderCenter()`가 `overrunEvent`를 몰라 library로 떨어짐
- onboarding 상태/도우미/UI가 같이 들어옴

---

## 바로 다음 액션
1. `cutover gate -> second runtime slice -> second runtime review -> fixture ledger -> 이 요약` 순서로 Commit 2 범위를 다시 잠근다.
2. `game-engine.js + app.js + styles.css + smoke-test.mjs`를 같은 커밋 경계로 묶는다.
3. 기록은 `Commit 2 runtime-ready closing green` 문장만 쓴다.
4. 그 뒤에만 `B-1 library packet`을 연다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 두 번째 코드 커밋을 `scene card + confirm resolve + old baseline 제거`라는 실제 closing 수술 순서로 빠르게 읽게 만드는 companion view다.
