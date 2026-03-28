# DiceSpell 첫 런 온보딩 런타임 스택 패킷

- 요약 1: `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending` 문서는 이미 각각 handoff-ready하지만, 실제 구현 턴에서는 여전히 `어느 커밋까지를 한 턴으로 닫고 무엇을 같이 제출해야 하는가`가 남아 있다.
- 요약 2: 이 문서는 온보딩 B-track을 **4커밋 런타임 스택**으로 다시 압축해, 프로그래머/UI/아트/QA/PM이 `surface별 bounded commit`과 `제출 순서`를 같은 문장으로 공유하게 만든다.
- 요약 3: 첫 화면만 읽어도 `A-track green 이후 누구가 무엇부터 읽고, 어떤 surface를 절대 합치지 말아야 하는지`가 바로 보이게 구성했다.

**한 줄 목표:** A-track 종료 직후 온보딩 B-track이 다시 `튜토리얼 전체 붙이기`로 부풀지 않도록, B-1~B-4를 실제 구현/리뷰/기록 기준의 4커밋 스택으로 고정한다.

## 왜 이 문서가 필요한가

기존 문서 상태는 충분히 강하다.

- 방향: `docs/dicespell_first_run_onboarding_handoff_kr.md`
- 화면: `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- 통합 훅: `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
- 검수선: `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- 콘텐츠: `docs/dicespell_first_run_onboarding_content_deck_kr.md`
- surface별 첫 커밋 경계: `B-1`~`B-4` packet/summary
- cross-doc 진입점: `docs/dicespell_overrun_onboarding_entrypoint_matrix_kr.md`

하지만 실제 실행 직전에는 아직 한 단계가 비어 있었다.

> 좋다. 각 surface packet은 있다. 그런데 실제 사람들은 **이번 턴에 어느 surface까지만 닫고**, **무슨 evidence를 남기며**, **tracker/run log에는 어떤 문장을 남겨야 하지?**

이 문서는 그 공백만 다룬다. 새 primer 카피나 새 메카닉을 추가하지 않는다. 이미 닫힌 B-1~B-4 packet을 `실행 스택`으로 재배치하는 문서다.

---

## 누가 무엇을 읽어야 하나

| 역할 | 먼저 읽을 것 | 바로 이어서 읽을 것 | 이번 문서에서 집중할 섹션 |
| --- | --- | --- | --- |
| 프로그래머 | 본 문서 `4. 커밋 스택` | 각 B-1~B-4 packet | 파일 경계 / 비변경선 / handoff 문장 |
| UI 오너 | 본 문서 `5. 역할별 handoff` | screen packet / content deck | surface별 밀도 / 동시 착수 금지선 |
| 아트 오너 | 본 문서 `5. 역할별 handoff` | content deck / boundary packet | art token 세트 / 최소 자산 패키지 |
| QA | 본 문서 `6. evidence bundle` | acceptance matrix / B-1~B-4 packet | commit별 acceptance / recovery 증거 |
| PM/기획 | 본 문서 `3. 실행 원칙` | summary 문서 | 순서 고정 / 완료 문장 / 범위 팽창 금지 |

---

## 실행 원칙

### 1) A-track green 전에는 열지 않는다

- `overrunEvent` A-track Commit 2(`scene/resolve closing`)가 green으로 닫히기 전에는 온보딩 B-track을 열지 않는다.
- 이유: `ending` surface는 `overrunEvent`와 공유 경계가 있으므로, A-track이 닫히기 전에 B-4를 먼저 붙이면 결과/장면 책임이 다시 섞인다.

### 2) B-track은 4커밋 스택으로만 실행한다

- Commit B-1 = library primer
- Commit B-2 = battle primer
- Commit B-3 = reward/shop primer
- Commit B-4 = ending primer

한 커밋에서 두 surface 이상을 같이 닫지 않는다. 특히 아래는 금지다.

- `library + battle` 동시 커밋
- `reward + shop + ending` 한 번에 묶기
- `ending` primer와 `overrunEvent` flavor 문구를 같은 커밋에서 조정하기
- primer 구현과 unrelated UX polish, 새 메카닉, 새 reward 규칙을 같이 묶기

### 3) surface마다 종료 문장이 달라야 한다

B-track의 위험은 기능이 아니라 기록이다. `온보딩 추가` 한 문장으로 기록하면 어떤 surface가 닫혔는지 흐려진다.

따라서 tracker/run log에는 반드시 surface별 종료 문장을 남긴다.

| 커밋 | tracker/run log 권장 문장 |
| --- | --- |
| B-1 | `library primer handoff를 닫고 seenBookSelectPrimer 기준 첫 진입 guidance만 고정했다.` |
| B-2 | `battle primer handoff를 닫고 entry/first-spell/auto-pass guidance를 전투 조작 비차단 상태로 고정했다.` |
| B-3 | `reward/shop primer handoff를 닫고 전투 보상 vs 상점 목적 분리를 primer layer에서 고정했다.` |
| B-4 | `ending primer handoff를 닫고 결과/CTA 의미만 남기며 overrunEvent flavor 비침범 경계를 유지했다.` |

---

## 4커밋 런타임 스택

## Commit B-1 — Library Primer Floor

### 3-line summary
- 대상: `renderGrimoireShelf()` + `meta.uiGuidance.seenBookSelectPrimer`
- 목표: 첫 책 선택 순간에만 hero-primer 1장을 보여 주고, 이후엔 재노출 제어만 남긴다.
- 하지 않을 것: battle/reward/shop/ending primer를 같은 커밋에서 건드리지 않는다.

**한 줄 목표:** 책 선택 surface를 `첫 진입 guidance layer`로 닫되, 선택 자체는 끝까지 비차단으로 유지한다.

### 왜 중요하나
- 첫 런 UX의 첫인상을 결정한다.
- 여기서 과하게 많은 primer를 밀어 넣으면 이후 surface들의 정보 밀도가 모두 흔들린다.

### 코어 구조
- source-of-truth: `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- 구현 범위: `seenBookSelectPrimer`, `library.book-select`, `openingPlan` / `caution` highlight
- 비변경선: battle primer, reward/shop primer, ending primer, overrun 경계 카피

### 역할별 handoff 포인트
- 프로그래머: seen flag 저장/복구 + shelf hero-primer 주입
- UI 오너: card hierarchy, highlight weight, 읽힘 밀도 검수
- 아트 오너: `library.book-select` 토큰 기준 최소 배너/아이콘 패키지
- QA: 첫 진입 노출 / 재진입 미노출 / 선택 비차단 / 저장 복구 확인

### 리스크 & 후속과제
- risk: primer가 책 카드 자체보다 더 큰 시각 우선순위를 먹는 drift
- follow-up: B-2에서 battle primer가 library 카피를 다시 반복하지 않게 `책 선택 -> 첫 주문` 정보 차이를 유지

### immediate next actions
1. `B-1 packet` 기준 구현 범위를 1 commit으로 제한한다.
2. evidence bundle을 남긴다.
3. tracker/run log에는 B-1 전용 종료 문장만 남긴다.

---

## Commit B-2 — Battle Primer Slice

### 3-line summary
- 대상: `renderBattle(summary)` + 첫 주문 성공 후 render 재진입 + auto-pass 처리 훅
- 목표: `battle.entry`, `battle.first-spell`, `battle.auto-pass` 3면을 전투 조작 비차단 상태로 붙인다.
- 하지 않을 것: reward/shop/ending primer와 묶지 않는다.

**한 줄 목표:** 플레이어가 첫 전투에서 `무엇을 해야 하는지`와 `왜 자동 종료되었는지`를 같은 battle surface 안에서 읽게 만든다.

### 왜 중요하나
- 전투는 DiceSpell의 핵심 감정선이다.
- 여기서 primer가 늦거나 과하면 첫 주문의 리듬을 망친다.

### 코어 구조
- source-of-truth: `docs/dicespell_first_run_onboarding_b2_battle_packet_kr.md`
- 구현 범위: `seenBattlePrimer`, `seenFirstSpellHint`, `seenAutoPassHint`
- 비변경선: reward/shop 목적 문구, ending CTA 설명, overrun flavor

### 역할별 handoff 포인트
- 프로그래머: entry/inline hint 3종의 event hook와 재노출 제어
- UI 오너: hero vs inline hint 우선순위, 버튼 가림 여부, 로그와의 간섭 확인
- 아트 오너: battle token 세트 최소 아이콘/상태 배지
- QA: 첫 진입/첫 주문/자동 종료 각 트리거 증거, 조작 비차단, reload 복구 확인

### 리스크 & 후속과제
- risk: first-spell 힌트와 auto-pass 힌트가 같은 타이밍에 겹쳐 battle 밀도가 무너지는 문제
- follow-up: B-3에서는 `전투가 끝난 뒤 보상과 상점의 목적이 달라진다`는 메시지만 추가하고, battle 카피를 다시 반복하지 않는다.

### immediate next actions
1. `B-2 packet` 기준으로 battle primer만 닫는다.
2. B-2 acceptance/recovery evidence를 surface 단위로 남긴다.
3. B-3를 열기 전 tracker/run log에 B-2 전용 문장을 남긴다.

---

## Commit B-3 — Reward / Shop Split Slice

### 3-line summary
- 대상: `renderReward(summary)` / `renderShop()`
- 목표: reward와 shop의 목적 차이를 primer layer에서 분리 고정한다.
- 하지 않을 것: ending primer, overrun CTA, new reward rules 추가.

**한 줄 목표:** `전투 보상은 지금 살리는 선택`, `상점은 다음 전투를 준비하는 선택`이라는 차이를 같은 런에서 즉시 읽히게 만든다.

### 왜 중요하나
- reward와 shop은 UI 모양은 가까워도 역할은 다르다.
- 이 차이를 primer가 흐리게 만들면 mid-run 판단이 다시 설명문처럼 느껴진다.

### 코어 구조
- source-of-truth: `docs/dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md`
- 구현 범위: `seenRewardPrimer`, `seenShopPrimer`, boss draft 공존, CTA 비차단
- 비변경선: 새 reward table, 상점 가격, ending/overrun 경계

### 역할별 handoff 포인트
- 프로그래머: reward/shop 각 1면 primer + seen flag 저장/복구
- UI 오너: accent 분리, CTA hierarchy, reward-vs-shop 본문과 primer 중복 제거
- 아트 오너: reward/shop token 대비 세트
- QA: reward 첫 노출 / shop 첫 노출 / boss draft 공존 / CTA 비차단 검증

### 리스크 & 후속과제
- risk: reward primer와 shop primer가 같은 tone으로 보여 목적 분리가 다시 흐려지는 문제
- follow-up: B-4는 `결과 해석 + CTA 의미`만 붙이고, reward/shop 학습 문구를 ending에서 되풀이하지 않는다.

### immediate next actions
1. `B-3 packet` 기준으로 reward/shop primer만 닫는다.
2. accent/카피 drift 증거를 남긴다.
3. tracker/run log에는 reward/shop 목적 분리 문장만 남긴다.

---

## Commit B-4 — Ending Primer Closing

### 3-line summary
- 대상: `renderEnding(summary)` + `오버런 계속` CTA 주변 primer layer
- 목표: 결과 해석과 CTA 의미만 남기고, 장면/flavor는 끝까지 `overrunEvent`로 넘긴다.
- 하지 않을 것: overrunEvent flavor, 새 ending polish 범위 확장, 엔딩 화면 대개편.

**한 줄 목표:** ending surface를 `결과/정산/버튼 의미`까지만 설명하는 낮은 밀도의 primer commit으로 닫는다.

### 왜 중요하나
- ending은 onboarding과 overrunEvent가 가장 쉽게 충돌하는 surface다.
- 여기서 경계가 흐리면 A-track과 B-track이 다시 섞인다.

### 코어 구조
- source-of-truth: `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md`
- 추가 참조: `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- 구현 범위: `seenDefeatPrimer`, `seenVictoryPrimer`, `seenOverrunCtaPrimer`
- 비변경선: themeTrophy/enemyTrophy/none flavor, overrunEvent 중앙 카드 tone, resolve 타이밍

### 역할별 handoff 포인트
- 프로그래머: ending primer 3면 저장/재노출/CTA 비차단 구현
- UI 오너: 결과 정보와 CTA 보조 문구의 밀도 조절, overrunEvent와 shell 중복 방지
- 아트 오너: ending primer용 최소 neutral token 세트
- QA: defeat/victory/overrun CTA 각 분기, overrunEvent 진입 후 flavor 비중복, reload 복구 검증

### 리스크 & 후속과제
- risk: ending primer가 overrunEvent flavor를 먹어 `다음 권 장면`이 다시 결과 화면처럼 읽히는 drift
- follow-up: B-4 green 이후에만 B-track 전체 QA recap 또는 polish 후보를 검토한다.

### immediate next actions
1. `B-4 packet`과 boundary packet을 같이 읽고 착수한다.
2. ending은 결과/CTA 의미만 남긴다.
3. flavor는 overrunEvent에 남겨 둔 상태로 종료 증거를 남긴다.

---

## 역할별 handoff 큐

| 역할 | B-1 | B-2 | B-3 | B-4 |
| --- | --- | --- | --- | --- |
| 프로그래머 | 필수 | 필수 | 필수 | 필수 |
| UI 오너 | 검수 우선 | 검수 우선 | 필수 | 필수 |
| 아트 오너 | 선택적 선행 | 선택적 선행 | 필수 | 필수 |
| QA | acceptance/recovery | acceptance/recovery | acceptance/recovery | acceptance/recovery + boundary |
| PM/기획 | 범위 승인 | 기록 문장 확인 | 목적 분리 확인 | ending-vs-overrun 경계 승인 |

### handoff 규칙
- B-1/B-2는 **프로그래머 + QA + PM** 중심으로 먼저 닫는다.
- B-3부터는 **UI/아트 관여도**가 올라간다.
- B-4는 `ending ↔ overrunEvent` 경계 때문에 **QA + PM + UI** 확인이 필수다.

---

## evidence bundle

| 커밋 | 최소 evidence |
| --- | --- |
| B-1 | 첫 진입 스크린샷, 재진입 미노출 증거, meta 저장 diff, tracker/run log 문장 |
| B-2 | entry/first-spell/auto-pass 각 1개 이상 증거, 조작 비차단 캡처, seen flag diff |
| B-3 | reward/shop primer 각각 캡처, accent 분리 캡처, boss draft 공존 또는 fallback 증거 |
| B-4 | defeat/victory/overrun CTA 각 캡처, ending-vs-overrun 경계 캡처, flavor 비중복 증거 |

### 제출 순서
1. affected smoke 또는 focused verification
2. 상태/저장 diff
3. 화면 evidence
4. 코드 diff
5. tracker/run log handoff 문장

---

## 금지 패턴

- `B-1~B-4`를 한 번에 `온보딩 적용`으로 기록하는 것
- `ending primer`에 `themeTrophy` / `enemyTrophy` / `none` flavor를 재서술하는 것
- `reward/shop` primer에서 실제 보상 규칙 변경까지 한 커밋에 섞는 것
- battle primer 중간에 unrelated combat UX polish를 끼워 넣는 것
- art token 부재를 이유로 프로그래머가 임시 텍스트 덩어리로 density를 올리는 것

---

## 리스크 & 후속과제

1. **범위 팽창 리스크**  
   B-track은 문서가 많아서 오히려 `이번에 그냥 온보딩 전체를 붙이자`는 유혹이 크다. 이 문서는 그 유혹을 막기 위한 stack packet이다.
2. **ending 경계 재오염 리스크**  
   B-4가 overrunEvent flavor를 다시 먹으면 A-track 문서가 닫아 둔 경계가 무너진다.
3. **기록 문장 혼합 리스크**  
   B-1~B-4를 같은 완료 문장으로 기록하면 surface별 종료선이 사라진다.
4. **다음 후속 작업**  
   A-track green 이후 이 stack packet 기준으로 B-1부터 순서대로 실행한다. B-4 green 전에는 `온보딩 전체 polish`를 새 목표로 열지 않는다.

---

## immediate next actions

1. tracker/blueprint/index에 이 stack packet을 source-of-truth로 추가한다.
2. A-track이 green이 되면 `B-1 -> B-2 -> B-3 -> B-4` 순서로만 온보딩을 연다.
3. 각 커밋은 surface별 종료 문장과 evidence bundle을 따로 남긴다.
