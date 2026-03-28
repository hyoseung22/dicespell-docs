# DiceSpell 첫 런 온보딩 핸드오프 스코어카드 요약

- 요약 1: 이 문서는 온보딩 B-track을 실제로 열 때 `B-1 library`, `B-2 battle`, `B-3 reward/shop`, `B-4 ending`을 어떤 체크와 어떤 완료 문장으로 닫아야 하는지 빠르게 읽는 readable companion이다.
- 요약 2: 핵심은 새 primer를 더 만드는 게 아니라, 이미 handoff-ready한 surface packet들을 `실행 체크리스트 + evidence bundle + tracker/run log 문장`으로 다시 압축하는 것이다.
- 요약 3: 첫 화면만 읽어도 프로그래머/UI/아트/QA/PM이 `이번 턴에 어느 surface만 닫고 무엇은 아직 건드리지 말아야 하는지`를 바로 파악할 수 있게 구성했다.

**한 줄 목표:** 온보딩 B-track을 `surface 1개 = commit 1개`의 실행/검수 스코어카드로 고정해 `온보딩 전체 붙이기` 같은 범위 팽창을 막는다.

## 누가 어디를 읽으면 되나

| 역할 | 먼저 볼 부분 | 바로 가져갈 결론 |
| --- | --- | --- |
| 프로그래머 | `B-1~B-4 체크라인` | surface별 hook/flag/render 범위를 섞지 않는다 |
| UI | `B-2~B-4 비변경선` | readable sanity만 닫고 전역 리디자인은 미룬다 |
| 아트 | `B-1/B-3/B-4 체크라인` | 새 대형 자산보다 token/accent drift 체크가 우선이다 |
| QA | `공통 evidence bundle` | surface별 green/fail을 같은 번들로 기록한다 |
| PM/기획 | `복붙용 기록 문장` | `온보딩 추가`가 아니라 닫힌 surface 이름으로 기록한다 |

## 왜 필요한가

runtime stack packet이 `B-1 -> B-2 -> B-3 -> B-4` 순서를 고정했다면, 이번 문서는 그 다음 단계인 `각 surface를 정확히 무엇으로 green 처리할 것인가`를 고정한다.

비어 있던 공백은 이거였다.

> 좋다. 온보딩을 4커밋으로 여는 건 정해졌다. 그런데 이번 턴 구현자와 리뷰어는 정확히 어떤 체크를 닫고 어떤 문장을 tracker/run log에 남기지?

이 문서는 그 질문에만 답한다.

---

## B-1 — Library Primer Floor

### 3줄 요약
- 대상: `renderGrimoireShelf()` + `seenBookSelectPrimer`
- 목표: 첫 책 선택 순간의 hero-primer 1장과 seen 저장/복구만 닫는다.
- 금지: battle/reward/shop/ending primer를 같이 열지 않는다.

**한 줄 목표:** library surface를 첫 진입 guidance layer로 닫되, 책 선택은 끝까지 비차단으로 유지한다.

### 역할별 handoff 포인트
- 프로그래머: shelf hero-primer 주입 + seen 저장/복구
- UI: card hierarchy가 책 선택보다 커지지 않게 검수
- 아트: `library.book-select` token sanity만 확인
- QA: 첫 진입 노출 / 재진입 미노출 / 선택 비차단 검증
- PM: `library primer handoff를 닫고 seenBookSelectPrimer 기준 첫 진입 guidance만 고정했다.` 문장을 기록

### 최소 evidence
- 첫 진입 캡처
- 재진입 미노출 캡처
- seen 상태 diff 또는 동등 증거
- focused verification 결과

### 리스크 & 후속과제
- primer가 책 카드보다 더 큰 시각 우선순위를 먹는 drift
- 다음 B-2에서 library 카피를 되풀이하지 않도록 정보 차이를 유지해야 함

---

## B-2 — Battle Primer Slice

### 3줄 요약
- 대상: `battle.entry`, `battle.first-spell`, `battle.auto-pass`
- 목표: 전투 조작을 막지 않으면서 `무엇을 해야 하는지`와 `왜 자동 종료되었는지`를 battle surface 안에서 읽히게 만든다.
- 금지: reward/shop/ending 설명과 섞지 않는다.

**한 줄 목표:** entry / first-spell / auto-pass 3개 primer를 battle surface 안에 닫고 trigger 중첩은 막는다.

### 역할별 handoff 포인트
- 프로그래머: 3개 hook와 seen 저장/복구
- UI: hero vs inline hint 우선순위와 버튼 가림 여부 검수
- 아트: battle token 최소 세트 sanity 확인
- QA: 첫 진입/첫 주문/auto-pass 각 1회 이상 증거 확보
- PM: `battle primer handoff를 닫고 entry/first-spell/auto-pass guidance를 전투 조작 비차단 상태로 고정했다.` 문장을 기록

### 최소 evidence
- entry / first-spell / auto-pass 각 캡처
- trigger별 seen 상태 diff
- 전투 조작 비차단 확인
- reload 유지 확인

### 리스크 & 후속과제
- first-spell과 auto-pass 힌트가 같은 타이밍에 겹치는 문제
- B-3에서 battle 카피를 반복하지 않도록 역할 차이를 유지해야 함

---

## B-3 — Reward / Shop Split Slice

### 3줄 요약
- 대상: `renderReward(summary)` / `renderShop()`
- 목표: reward와 shop의 목적 차이를 primer layer에서 고정한다.
- 금지: 보상 규칙/가격/ending 설명을 같이 바꾸지 않는다.

**한 줄 목표:** `전투 보상은 지금 살리는 선택`, `상점은 다음 전투를 준비하는 선택`이라는 차이를 즉시 읽히게 만든다.

### 역할별 handoff 포인트
- 프로그래머: reward/shop primer 각 1면 + seen 저장/복구
- UI: accent 분리와 CTA hierarchy 검수
- 아트: reward/shop tone 대비 sanity 확인
- QA: reward 첫 노출 / shop 첫 노출 / boss draft 공존 또는 fallback 검증
- PM: `reward/shop primer handoff를 닫고 전투 보상 vs 상점 목적 분리를 primer layer에서 고정했다.` 문장을 기록

### 최소 evidence
- reward primer 캡처
- shop primer 캡처
- accent 분리 캡처
- CTA 비차단 / boss draft 공존 증거

### 리스크 & 후속과제
- reward와 shop이 같은 tone으로 보여 목적 분리가 흐려지는 문제
- B-4에서 reward/shop 학습 문구를 ending에 되풀이하지 않아야 함

---

## B-4 — Ending Primer Closing

### 3줄 요약
- 대상: `renderEnding(summary)` + `오버런 계속` CTA primer
- 목표: 결과/CTA 의미만 남기고 장면/flavor는 끝까지 `overrunEvent`에 남긴다.
- 금지: ending과 overrunEvent의 경계를 다시 섞지 않는다.

**한 줄 목표:** ending surface를 낮은 밀도의 결과/CTA primer commit으로 닫고 overrun flavor 비침범 경계를 유지한다.

### 역할별 handoff 포인트
- 프로그래머: defeat/victory/overrun CTA primer 3면 저장/재노출 제어
- UI: 결과 정보와 CTA 문구 밀도 조절, overrunEvent와 shell 중복 방지
- 아트: neutral ending token sanity 확인
- QA: defeat/victory/CTA 각 분기 + overrun 진입 후 flavor 비중복 검증
- PM: `ending primer handoff를 닫고 결과/CTA 의미만 남기며 overrunEvent flavor 비침범 경계를 유지했다.` 문장을 기록

### 최소 evidence
- defeat / victory / overrun CTA 각 캡처
- ending vs overrunEvent 경계 캡처
- flavor 비중복 증거
- reload 복구 증거

### 리스크 & 후속과제
- ending primer가 overrunEvent flavor를 다시 먹는 drift
- B-4 green 전에는 `온보딩 전체 polish`를 새 목표로 열지 않아야 함

---

## 공통 evidence bundle

1. focused verification 또는 affected smoke
2. 상태 diff 메모
3. 화면 evidence
4. 코드 diff 요약
5. 한 줄 handoff 문장

### 공통 규칙
- `surface 1개 = commit 1개`
- 각 surface는 다음 surface만 연다
- `온보딩 추가` 같은 뭉뚱그린 기록 문장은 금지
- B-4는 반드시 `docs/dicespell_ending_overrun_boundary_packet_kr.md`와 함께 검수한다

---

## 복붙용 기록 문장

- B-1: `library primer handoff를 닫고 seenBookSelectPrimer 기준 첫 진입 guidance만 고정했다.`
- B-2: `battle primer handoff를 닫고 entry/first-spell/auto-pass guidance를 전투 조작 비차단 상태로 고정했다.`
- B-3: `reward/shop primer handoff를 닫고 전투 보상 vs 상점 목적 분리를 primer layer에서 고정했다.`
- B-4: `ending primer handoff를 닫고 결과/CTA 의미만 남기며 overrunEvent flavor 비침범 경계를 유지했다.`

---

## immediate next actions

1. A-track green 이후 B-track은 `B-1 -> B-2 -> B-3 -> B-4` 순서로만 연다.
2. 각 surface는 이 스코어카드의 evidence bundle과 완료 문장을 그대로 재사용한다.
3. fail이면 새 문서를 늘리지 말고 현재 surface 범위 안에서만 수정한다.
4. B-4 green 전에는 `온보딩 전체 polish`나 `ending/overrun 통합 정리`를 새 목표로 열지 않는다.
