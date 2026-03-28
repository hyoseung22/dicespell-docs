# DiceSpell `overrunEvent` Commit 1 floor 수술 시트 요약

## 3줄 요약
- 이 문서는 `overrunEvent` 첫 실구현 커밋에서 **정확히 어디를 자르고 어디는 아직 건드리면 안 되는지**를 빠르게 읽기 위한 companion view다.
- 핵심은 `continueOverrun()`를 즉시 적용 함수에서 `enter-only floor`로 줄이고, 같은 커밋에서 `renderCenter()` explicit branch와 floor smoke까지 같이 닫는 것이다.
- 프로그래머는 line anchor와 제거 목록을, UI/아트는 대기선을, QA/PM은 floor evidence 묶음을 먼저 보면 된다.

**한 줄 목표:** Commit 1을 `line-anchored enter floor surgery`로 다시 압축해, 더 이상 `어디를 먼저 잘라야 하지?`를 감으로 정하지 않게 만든다.

---

## first screen

### 지금 가장 중요한 결론
- `game-engine.js 2978-3009`의 `continueOverrun()`가 Commit 1의 핵심 절개 지점이다.
- 이 커밋에서 허용되는 변화는 `phase='overrunEvent'` 진입 + canonical snapshot + recovery + explicit branch + floor smoke뿐이다.
- reward apply / `currentPage` 증가 / `pagePlan` 확장 / `segmentTrophyTemplateIds` 초기화 / `maybeEnterNextPage()`는 전부 아직 금지다.

### 누가 무엇부터 읽어야 하나
| 역할 | 먼저 볼 것 | 바로 얻어야 하는 결론 |
| --- | --- | --- |
| 프로그래머 | line anchor + 수술 순서 | 첫 커밋은 enter-only floor다 |
| UI | 대기선 | Commit 1은 shell 마감 턴이 아니다 |
| 아트 | 대기선 | `artTokenSet` freeze 전까지 token sanity만 본다 |
| QA | evidence bundle | `S1/S2/S3/S7~S11`만 Commit 1 green이다 |
| PM | 완료 문장 | `runtime-ready`가 아니라 `floor green`이다 |

---

## Commit 1에서 직접 자를 anchor

| 파일 | 현재 anchor | Commit 1에서 해야 할 일 |
| --- | --- | --- |
| `src/game-engine.js` | `2978-3009` | `continueOverrun()`를 reward/apply/progression 없는 enter wrapper로 축소 |
| `src/app.js` | `2296-2298` | `continue-overrun`을 enter action 의미로만 유지 |
| `src/app.js` | `1995-2005` | `renderCenter()`에 `overrunEvent` explicit branch 추가 |
| `scripts/smoke-test.mjs` | `1294-1409` | old immediate assertions를 floor assertions로 교체 |

---

## Commit 1 수술 순서
1. `buildCanonicalOverrunEventSnapshot()` / `buildNoneOverrunEventSnapshot()` / `normalizeOverrunEventState()` 계열 helper를 만든다.
2. `continueOverrun()`는 `state.overrunEvent` 생성 + `phase='overrunEvent'` 진입까지만 남긴다.
3. `renderCenter()`가 `overrunEvent`를 library default로 흘리지 않게 explicit branch를 둔다.
4. smoke를 `S1/S2/S3/S7/S8/S9/S10/S11` 기준 floor smoke로 갈아탄다.

---

## 이번 커밋에서 지우거나 남겨야 할 것

### 제거/축소
- `applyEffectBundle(...)`
- `state.overrunLevel += 1`
- `state.segmentTrophyTemplateIds = []`
- `state.pagePlan.push(...)`
- `state.currentPage += 1`
- `state.phase = 'library'`
- `maybeEnterNextPage(state)`

### 보존
- ending 선택 저장(`selectedRewardId`) 자체
- canonical snapshot 입력값 읽기
- `none` fallback 경로
- 최소 explicit render branch

---

## UI/아트 대기선
- Commit 1은 장면 카드 완성 턴이 아니다.
- UI는 `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved`가 엔진 snapshot에 직접 들어온 뒤에만 A-3 shell closing을 시작한다.
- 아트는 새 대형 자산보다 `themeTrophy` / `enemyTrophy` / `none` token identity가 payload에 직접 고정됐는지부터 본다.

---

## QA quick check
- `phase='overrunEvent'`
- canonical field set 존재
- reward 미적용
- `currentPage` / `pagePlan.length` / `segmentTrophyTemplateIds` 미변경
- `renderCenter()` library fall-through 아님
- `S1/S2/S3/S7/S8/S9/S10/S11`만 green

### 실패 신호
- reward가 이미 적용됨
- progression 값이 움직임
- snapshot이 partial임
- branch가 title/sourceType 재추론에 의존함

---

## 바로 다음 액션
1. `runtime patch map -> first runtime slice -> state matrix -> fixture ledger -> 이 요약` 순서로 Commit 1 범위를 다시 잠근다.
2. `game-engine.js + app.js + smoke-test.mjs`를 같은 커밋 경계로 묶는다.
3. 기록은 `Commit 1 floor green` 문장만 쓴다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 첫 코드 커밋을 `continueOverrun()` 절개 + explicit branch + floor smoke`라는 실제 수술 순서로 빠르게 읽게 만드는 companion view다.
