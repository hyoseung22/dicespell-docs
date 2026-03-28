# DiceSpell `overrunEvent` 첫 런타임 슬라이스 리뷰 요약

## 3줄 요약
- 이 문서는 `first runtime slice` 커밋을 리뷰할 때 **무엇을 pass로 보고 무엇을 아직 안 된 것으로 남겨야 하는지**를 빠르게 읽게 만드는 companion이다.
- 핵심은 `enter + canonical snapshot + recovery smoke`까지만 닫혔는지, 그리고 reward apply / 페이지 전진 / UI polish / resolve가 아직 열리지 않았는지를 함께 확인하는 것이다.
- 즉 이 문서는 `좋아, 이 커밋이 어디까지 끝난 건가?`를 3~5분 안에 답하게 만든다.

## 한 줄 목표
첫 runtime slice 커밋을 **과장 없이 설명하고 검수하는 짧은 리뷰 기준선**을 제공한다.

---

## 이 문서가 필요한 이유
문서는 이미 충분하다.

- `runway`
- `runtime patch map`
- `first runtime slice packet`
- `state matrix`
- `A-1 / A-2 packet`

문제는 리뷰 순간이다.
그때는 `지금 이 커밋이 정말 A-1/A-2까지만 닫혔는가?`를 한 문장으로 말할 수 있어야 한다.

---

## 이번 커밋을 뭐라고 말하면 맞는가
아래 문장을 그대로 써도 된다.

> 이번 커밋은 `ending -> overrunEvent` 진입과 canonical snapshot 저장/복구까지만 닫혔고, reward apply / `currentPage` 증가 / `pagePlan` 확장 / `segmentTrophyTemplateIds` 초기화 / 중앙 카드 UI polish / confirm resolve는 아직 열지 않았다.

이 문장이 중요한 이유는 두 가지다.

1. **무엇이 끝났는지**를 말한다.
2. **무엇이 아직 안 끝났는지**도 같이 말한다.

---

## 리뷰에서 꼭 봐야 하는 것

### 1) 코드 diff
기대되는 파일은 보통 이 셋이다.

- `src/game-engine.js`
- `src/app.js`
- `scripts/smoke-test.mjs`

정상적인 diff는 보통 아래 모양이다.

- `continueOverrun()`가 즉시 적용 체인에서 enter wrapper 쪽으로 줄어든다.
- canonical snapshot helper가 생긴다.
- normalize helper가 생긴다.
- `phase='overrunEvent'` 최소 branch가 생긴다.
- enter / canonical / recovery smoke가 추가된다.

### 2) state diff
이번 커밋에서 **반드시 바뀌어야 하는 것**

- `phase='overrunEvent'`
- canonical snapshot 존재
- `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved=false`

이번 커밋에서 **아직 바뀌면 안 되는 것**

- reward effect 적용
- `currentPage` 증가
- `pagePlan` 확장
- `segmentTrophyTemplateIds` 초기화
- `resolved=true`

### 3) smoke 결과
happy path만 보면 안 된다.
최소 아래는 같이 있어야 한다.

- enter
- none/skip
- canonical field set
- legacy recovery
- missing recovery
- invalid recovery

---

## 이 커밋에서 fail로 봐야 하는 것

- `phase='overrunEvent'`만 바뀌고 snapshot이 partial인 경우
- title/sourceType로 badge/accent/header를 다시 계산하는 경우
- reward가 이미 적용된 경우
- `currentPage`나 `pagePlan`이 움직인 경우
- `styles.css`나 중앙 카드 위계가 크게 들어와 A-3 범위가 섞인 경우
- confirm resolve까지 같이 들어와 A-4 범위가 섞인 경우

즉, **좋아 보이는 것**보다 **범위를 지켰는가**가 더 중요하다.

---

## 역할별로 무엇을 확인하면 되나

### 프로그래머
- enter와 snapshot/normalize가 분리됐는가
- progression이 아직 안 움직였는가
- recovery가 happy path 부록이 아니라 같은 커밋 범위에 있는가

### QA
- smoke가 green인가
- legacy/missing/invalid/none이 다 있나
- state matrix 비변경선이 실제로 지켜졌나

### UI
- 이제 `contentKey`/`accentKey`/`artTokenSet`을 믿어도 되는가
- 아직 A-3 shell을 열지 않았는가

### 아트
- `artTokenSet`이 freeze됐는가
- `none`이 빈 상태가 아니라 정상 branch로 남아 있는가

### PM/기획
- 이 커밋을 `enter+snapshot까지만 닫힌 커밋`으로 설명할 수 있는가
- 다음 액션이 A-3 UI인지 A-4 resolve인지 bounded하게 이어지는가

---

## pass 다음에 바로 할 일

### pass면
- tracker/run log에 `enter + canonical snapshot + reward 미적용 유지`를 같은 문장으로 기록
- 다음 슬롯에서 `A-3 UI` 또는 `A-4 resolve` 중 하나만 bounded하게 연다

### fail이면
- 새 문서를 더 쓰지 않는다
- first runtime slice 범위로 다시 되돌려 `무엇이 먼저 섞였는지`부터 복구한다

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_first_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_overrun_a4_resolve_packet_kr.md`
