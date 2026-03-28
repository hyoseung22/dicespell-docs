# DiceSpell 첫 런 온보딩 오너 워크보드

## 3줄 요약
- 이 문서는 첫 런 온보딩 B-track을 실제로 닫을 때 **프로그래머 / UI / 아트 / QA / PM이 이번 턴에 무엇을 넘기고 무엇은 아직 넘기지 말아야 하는지**를 역할별 작업 보드로 다시 압축한 source handoff다.
- 현재 DiceSpell에는 B-1~B-4 packet, runtime stack, handoff scorecard까지 이미 충분하지만, 실제 착수 직전에는 여전히 `좋아, 그래서 이번 surface 커밋에서 각 오너가 실제로 뭘 제출하면 되는가`가 문서 사이에 흩어져 보일 수 있다.
- 목표는 B-1/B-2/B-3/B-4를 역할별 산출물·입력 의존성·검수 증거·handoff 문장까지 한 장으로 고정해, 구현 착수와 리뷰 착수를 같은 오너 언어로 맞추는 것이다.

## 한 줄 목표
`B-1 library`, `B-2 battle`, `B-3 reward/shop`, `B-4 ending`을 **오너별 작업 묶음**으로 재배열해, 프로그래머/UI/아트/QA/PM이 같은 순서로 handoff를 주고받게 만든다.

## 왜 이 문서가 필요한가
지금 기준 온보딩 문서 축은 이미 충분하다.

- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b2_battle_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`

문제는 문서 부족이 아니라, 실제 착수 직전의 **오너별 전달 형태**가 다시 퍼질 수 있다는 점이다.

1. 프로그래머는 범위를 알아도 `이번 턴에 UI/아트/QA/PM에게 무엇을 넘기면 되는지`를 다시 조합하게 된다.
2. UI는 primer 밀도를 알아도 `이번 턴이 실제 화면 마감 턴인지, 아니면 hook/상태 검수 턴인지`가 흐려지면 너무 일찍 polish를 확정할 수 있다.
3. 아트는 `accentKey` / `artTokenSet` 계약을 알아도 `이번 턴 제출물`이 새 자산인지, 토큰 매칭 sanity인지 헷갈릴 수 있다.
4. QA/PM은 acceptance와 handoff 문장을 알아도 `이번 커밋이 어느 오너 handoff까지 닫힌 것인지`를 짧게 기록하기 어렵다.

즉 지금 필요한 것은 새 primer가 아니라, 이미 닫힌 문서를 **오너별 workboard와 handoff queue**로 다시 압축하는 일이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. B-1`, `2. B-2`, `3. B-3`, `4. B-4` | `runtime stack`, `handoff scorecard`, 각 surface packet | 이번 턴 코드 범위와 오너 handoff 시점을 동시에 고정한다 |
| UI | `2. B-2`, `3. B-3`, `4. B-4`, `5. surface handoff 규칙` | `screen packet`, `content deck`, `boundary packet` | primer readable sanity를 닫는 턴과 아직 기다려야 하는 턴을 구분한다 |
| 아트 | `1. B-1`, `3. B-3`, `4. B-4`, `5. surface handoff 규칙` | `content deck`, `production cutsheet`, `boundary packet` | 새 대형 자산보다 token/accent drift 검수가 우선임을 확인한다 |
| QA | `1. B-1`, `2. B-2`, `3. B-3`, `4. B-4`, `6. evidence bundle` | `acceptance matrix`, `handoff scorecard` | surface별 pass/fail을 같은 owner bundle로 기록한다 |
| PM/기획 | `0. first screen 결론`, `7. 리스크 & 후속과제`, `8. 즉시 다음 액션` | `runtime stack`, `handoff scorecard` | B-1/B-2/B-3/B-4의 handoff 완료선을 다른 문장으로 남긴다 |

---

## 0. first screen 결론

### 이 문서를 왜 열어야 하나
- **프로그래머**: 이번 커밋이 실제로 누구에게 어떤 입력/산출물을 넘겨야 끝나는지 바로 확인하려고 연다.
- **UI/아트**: 지금 턴이 아직 대기/검수 턴인지, 실제로 primer readable sanity를 닫는 턴인지 확인하려고 연다.
- **QA/PM**: 이번 커밋이 `첫 진입 guidance handoff`, `전투 trigger handoff`, `reward/shop 목적 분리 handoff`, `ending boundary handoff` 중 어느 것인지 다른 문장으로 기록하려고 연다.

### 한 줄 판정 규칙
- B-1은 **첫 책 선택 hero-primer와 seen 제어가 닫혀 다음 오너가 같은 library primer 상태를 볼 수 있으면** 끝이다.
- B-2는 **entry / first-spell / auto-pass trigger와 비차단성이 닫혀 battle 오너들이 같은 trigger 언어를 볼 수 있으면** 끝이다.
- B-3는 **reward vs shop 목적 분리가 primer layer에서 닫혀 UI/아트/QA가 같은 분기 의미를 읽을 수 있으면** 끝이다.
- B-4는 **ending 결과/CTA 의미만 남기고 overrunEvent flavor 비침범 경계가 유지돼 모든 오너가 같은 종료 문장을 남길 수 있으면** 끝이다.
- B-4가 green이 되기 전에는 `온보딩 전체 polish`나 `overrunEvent flavor 보강`을 새 목표로 열지 않는다.

---

## 1. B-1 오너 워크보드 — library primer floor

## 3줄 요약
- B-1은 `renderGrimoireShelf()`에서 첫 책 선택 순간의 hero-primer 1장과 `seenBookSelectPrimer` 저장/복구를 닫는 턴이다.
- 이 단계에서 UI/아트는 shelf 전체를 새로 꾸미는 것이 아니라, **primer가 책 카드보다 앞서지 않는지**를 확인하는 검수 역할이 우선이다.
- 즉 B-1의 성공 기준은 `설명 카드가 생겼다`가 아니라 `첫 진입 guidance를 같은 언어로 handoff할 수 있다`다.

### 한 줄 목표
책 선택 surface를 `첫 진입 guidance layer`로 닫아, 다음 오너가 같은 library primer 상태와 같은 종료 문장을 공유하게 만든다.

### B-1 오너별 작업 표

| 역할 | 이번 턴 입력 | 이번 턴 산출물 | 이번 턴 금지 | handoff 문장 |
| --- | --- | --- | --- | --- |
| 프로그래머 | B-1 packet, runtime stack, handoff scorecard | `seenBookSelectPrimer` 저장/복구, shelf hero-primer 주입, primer 비차단 상태 | battle/reward/shop/ending primer 선반영, shelf 전면 리뉴얼 | `library primer floor를 넘겼다.` |
| UI | screen packet, B-1 packet | hero-primer vs 책 카드 hierarchy 메모, highlight weight sanity | battle hint 스타일 선행 확정, shelf 대규모 레이아웃 재설계 | `UI는 library primer readable sanity만 닫았다.` |
| 아트 | content deck의 `library.book-select`, production cutsheet | 최소 token/accent sanity 메모, 아이콘/배너 필요 여부 체크 | 신규 대형 key art 제작, reward/shop 토큰 선작업 | `아트는 library token 계약만 잠갔다.` |
| QA | acceptance matrix, B-1 packet | 첫 진입 노출 / 재진입 미노출 / 선택 비차단 / reload 복구 판정 | B-2 trigger까지 green 선언 | `QA는 library floor green만 기록했다.` |
| PM/기획 | handoff scorecard, runtime stack | B-1 완료 문장, 다음은 B-2만 허용한다는 기록 | `온보딩 추가` 같은 뭉뚱그린 완료 보고 | `이번 턴은 library primer green이다.` |

### B-1 프로그래머 제출 체크
- `src/game-engine.js` 또는 동등 storage layer
  - `meta.uiGuidance.seenBookSelectPrimer`가 저장/복구된다.
- `src/app.js`
  - `renderGrimoireShelf()`가 첫 진입에만 hero-primer를 주입한다.
  - `openingPlan` / `caution` highlight와 primer가 서로 가리지 않는다.
- `src/styles.css`
  - primer가 책 카드, CTA, 강조 표식을 가리지 않는다.
- `scripts/smoke-test.mjs` 또는 focused verification
  - 첫 진입 / 재진입 / reload 복구가 같은 결과로 남는다.

### B-1 UI / 아트 체크
- UI는 아래만 확인한다.
  - primer가 책 카드보다 큰 시각 우선순위를 먹지 않는다.
  - 첫 화면은 `무슨 책을 고를지`를 더 선명하게 만들지, 선택을 멈추게 만들지 않는다.
- 아트는 아래만 체크한다.
  - `library.book-select` token/accent가 기존 shelf tone과 충돌하지 않는다.
  - 새 대형 자산 없이도 B-1 handoff는 성립한다.

### B-1 QA / PM 제출 묶음
1. 첫 진입 primer 화면
2. 재진입 미노출 화면
3. `seenBookSelectPrimer=true` 또는 동등 상태 메모
4. 선택 비차단 확인 메모
5. handoff 문장

### B-1 완료 문장
> 이번 커밋은 library primer의 첫 진입 guidance와 seen 제어만 닫아, 다음 오너가 같은 shelf 상태와 같은 종료 문장으로 B-2를 시작할 수 있게 만들었다.

---

## 2. B-2 오너 워크보드 — battle trigger handoff

## 3줄 요약
- B-2는 `battle.entry`, `battle.first-spell`, `battle.auto-pass` 3면의 primer와 재노출 제어를 닫는 전투 trigger 중심 턴이다.
- 이 단계에서 UI/아트는 전투 전체를 다시 디자인하는 것이 아니라, **trigger가 겹치지 않고 조작을 막지 않는지**를 검수하는 역할이 우선이다.
- 즉 B-2의 성공 기준은 `힌트가 많다`가 아니라 `무엇을 해야 하는지`와 `왜 자동 종료되었는지`가 같은 battle surface 안에서 handoff 가능해지는 것이다.

### 한 줄 목표
entry / first-spell / auto-pass trigger를 battle surface 안에 닫아, 다음 오너가 같은 전투 trigger 언어와 같은 종료 문장을 공유하게 만든다.

### B-2 오너별 작업 표

| 역할 | 이번 턴 입력 | 이번 턴 산출물 | 이번 턴 금지 | handoff 문장 |
| --- | --- | --- | --- | --- |
| 프로그래머 | B-2 packet, runtime stack, handoff scorecard | `seenBattlePrimer` / `seenFirstSpellHint` / `seenAutoPassHint` 저장/복구, entry/inline hint 트리거, 비차단 처리 | reward/shop/ending primer 선반영, 전투 메카닉 변경 | `battle primer trigger를 넘겼다.` |
| UI | screen packet, B-2 packet, content deck | hero vs inline hint 우선순위 메모, 과밀 중첩 방지 sanity | 로그/버튼 전면 리뉴얼, reward/shop 스타일 선확정 | `UI는 battle trigger readable sanity만 닫았다.` |
| 아트 | battle token 세트, content deck | battle accent/token drift 체크, 최소 배지/아이콘 가이드 | 새 연출 자산 묶음 추가 | `아트는 battle token sanity만 맞췄다.` |
| QA | acceptance matrix, B-2 packet | 첫 진입 / 첫 주문 / auto-pass trigger 증거, reload 복구 판정 | reward/shop 목적 분리까지 green 선언 | `QA는 battle trigger green만 기록했다.` |
| PM/기획 | handoff scorecard, runtime stack | B-2 완료 문장, 다음은 B-3만 허용한다는 기록 | `전투 튜토리얼 완료` 같은 포괄 보고 | `이번 턴은 battle primer green이다.` |

### B-2 프로그래머 제출 체크
- storage layer
  - `seenBattlePrimer`, `seenFirstSpellHint`, `seenAutoPassHint`가 저장/복구된다.
- `src/app.js`
  - 전투 첫 진입에 `battle.entry` primer가 1회 노출된다.
  - 첫 주문 성공 후 `battle.first-spell` inline hint가 뜬다.
  - auto-pass 시 `battle.auto-pass` hint가 뜬다.
- `src/styles.css`
  - 힌트 2개 이상이 같은 frame에서 과밀 중첩되지 않는다.
- verification
  - 전투 조작 비차단, reload/revisit 시 재노출 제어 유지.

### B-2 UI / 아트 체크
- UI는 아래만 확인한다.
  - hero-primer와 inline hint가 서로 다른 우선순위로 읽힌다.
  - battle 로그, 버튼, 타깃 UI를 가리지 않는다.
- 아트는 아래만 체크한다.
  - battle token/accent가 reward/shop primer와 같은 톤으로 미리 수렴하지 않는다.
  - 새 대형 자산 없이도 B-2 handoff는 성립한다.

### B-2 QA / PM 제출 묶음
1. entry primer 화면
2. first-spell hint 화면
3. auto-pass hint 화면
4. seen flag 상태 메모
5. 비차단 / reload 복구 메모
6. handoff 문장

### B-2 완료 문장
> 이번 커밋은 battle primer의 entry/first-spell/auto-pass trigger와 비차단성을 닫아, 다음 오너가 같은 전투 trigger 언어와 같은 종료 문장으로 B-3를 시작할 수 있게 만들었다.

---

## 3. B-3 오너 워크보드 — reward / shop intent handoff

## 3줄 요약
- B-3는 `reward.first`와 `shop.first`를 같은 작은 strip으로 뭉개지지 않게, **목적 분리 primer**로 닫는 턴이다.
- 이 단계에서 UI/아트는 reward와 shop이 둘 다 짧은 안내를 붙이더라도, 서로 다른 이유와 다른 표정으로 읽히게 만드는 검수 역할이 우선이다.
- 즉 B-3의 성공 기준은 `primer가 둘 다 있다`가 아니라 `reward는 전투 보상`, `shop은 자원 소비 판단`이라는 목적이 handoff 가능해지는 것이다.

### 한 줄 목표
reward와 shop의 primer를 같은 surface 문장으로 합치지 않고, 목적 분리와 CTA 비차단 상태를 오너별 closing handoff로 마무리한다.

### B-3 오너별 작업 표

| 역할 | 이번 턴 입력 | 이번 턴 산출물 | 이번 턴 금지 | handoff 문장 |
| --- | --- | --- | --- | --- |
| 프로그래머 | B-3 packet, handoff scorecard, content deck | `seenRewardPrimer` / `seenShopPrimer` 저장/복구, reward/shop primer 주입, boss draft 공존, CTA 비차단 | ending primer 선반영, reward/shop 규칙 변경 | `reward/shop primer intent를 넘겼다.` |
| UI | screen packet, B-3 packet, content deck | reward vs shop accent/배지/카피 hierarchy 메모, boss draft coexist sanity | 상점 규칙 설명 확장, ending CTA 스타일 선행 확정 | `UI는 reward/shop 목적 분리만 닫았다.` |
| 아트 | production cutsheet, content deck | reward token / shop token / neutral token 분리 sanity, 최소 accent drift 메모 | 새 상점 일러스트 묶음 추가 | `아트는 reward/shop token 경계만 맞췄다.` |
| QA | acceptance matrix, B-3 packet | reward 첫 노출 / shop 첫 노출 / boss draft 공존 / CTA 비차단 / reload 복구 판정 | ending boundary까지 green 선언 | `QA는 reward/shop intent green만 기록했다.` |
| PM/기획 | handoff scorecard, runtime stack | B-3 완료 문장, 다음은 B-4만 허용한다는 기록 | `보상 UX 정리` 같은 포괄 보고 | `이번 턴은 reward/shop primer green이다.` |

### B-3 프로그래머 제출 체크
- storage layer
  - `seenRewardPrimer`, `seenShopPrimer`가 저장/복구된다.
- `src/app.js`
  - reward surface와 shop surface에 각기 다른 primer가 1회 노출된다.
  - reward에서는 boss draft와 공존한다.
  - CTA와 카드 선택/구매를 막지 않는다.
- `src/styles.css`
  - reward와 shop의 accent/배지/hierarchy가 동일한 strip으로 뭉개지지 않는다.
- verification
  - reward/shop 재진입 시 중복 노출되지 않고 reload 복구가 유지된다.

### B-3 UI / 아트 체크
- UI는 아래만 확인한다.
  - reward는 `이번 전투 보상`으로, shop은 `지금 자원을 써서 조정하는 곳`으로 읽힌다.
  - boss draft가 뜨는 상황에서도 primer가 카드 가독성을 밀어내지 않는다.
- 아트는 아래만 체크한다.
  - reward/shop/neutral token이 서로 같은 톤으로 수렴하지 않는다.
  - 새 대형 자산 없이도 B-3 handoff는 성립한다.

### B-3 QA / PM 제출 묶음
1. reward primer 화면
2. shop primer 화면
3. boss draft 공존 화면 또는 메모
4. seen flag 상태 메모
5. CTA 비차단 / reload 복구 메모
6. handoff 문장

### B-3 완료 문장
> 이번 커밋은 reward와 shop primer의 목적 분리와 비차단성을 닫아, 다음 오너가 같은 보상/상점 분기 의미와 같은 종료 문장으로 B-4를 시작할 수 있게 만들었다.

---

## 4. B-4 오너 워크보드 — ending boundary closing

## 3줄 요약
- B-4는 `ending.defeat`, `ending.victory`, `ending.overrun-cta` primer를 **결과/CTA 의미만 남기는 경계 마감 턴**으로 닫는다.
- 이 단계에서만 UI/아트가 ending readable sanity를 실제로 마감하고, QA/PM은 `ending은 결과/CTA 의미만`, `flavor는 overrunEvent에 남는다`는 closing 문장을 기록한다.
- 즉 B-4의 성공 기준은 `ending에 정보가 많다`가 아니라 `ending과 overrunEvent가 같은 surface 언어를 더 이상 공유하지 않는다`다.

### 한 줄 목표
ending primer를 결과/CTA 의미 layer로만 마무리해, `themeTrophy` / `enemyTrophy` / `none` flavor는 끝까지 overrunEvent에 남기는 closing handoff를 만든다.

### B-4 오너별 작업 표

| 역할 | 이번 턴 입력 | 이번 턴 산출물 | 이번 턴 금지 | handoff 문장 |
| --- | --- | --- | --- | --- |
| 프로그래머 | B-4 packet, handoff scorecard, boundary packet | `seenDefeatPrimer` / `seenVictoryPrimer` / `seenOverrunCtaPrimer` 저장/복구, defeat/victory/CTA primer, CTA 비차단, boundary 유지 | overrunEvent flavor 주입, 오버런 장면 로직 수정 | `ending primer boundary closing을 넘겼다.` |
| UI | boundary packet, screen packet, content deck | 결과 요약 vs CTA helper hierarchy, ending과 overrunEvent 분리 sanity | overrun scene shell 선행 수정, 전역 ending 리디자인 | `UI는 ending boundary를 닫았다.` |
| 아트 | content deck, boundary packet, production cutsheet | defeat/victory/CTA token/accent sanity, neutral overrun bridge 메모 | `themeTrophy` / `enemyTrophy` flavor 자산을 ending에 선반영 | `아트는 ending tone 경계만 맞췄다.` |
| QA | acceptance matrix, B-4 packet, boundary packet | defeat/victory/CTA 노출, CTA 비차단, overrun flavor 비침범, reload 복구 판정 | overrunEvent 장면 green까지 한 번에 선언 | `QA는 ending boundary green을 기록했다.` |
| PM/기획 | handoff scorecard, runtime stack, boundary packet | B-4 완료 문장, 다음은 `온보딩 전체 polish`가 아니라 실제 실행 증거 정리만 남았다는 기록 | ending과 overrunEvent를 같은 green 문장으로 묶기 | `이번 턴은 ending primer green이다.` |

### B-4 프로그래머 제출 체크
- storage layer
  - `seenDefeatPrimer`, `seenVictoryPrimer`, `seenOverrunCtaPrimer`가 저장/복구된다.
- `src/app.js`
  - defeat/victory/overrun CTA primer가 각 surface에서 1회 노출된다.
  - CTA와 `오버런 전리품 보관함` 선택 흐름을 막지 않는다.
- `src/styles.css`
  - ending primer가 결과 정보와 CTA 의미를 설명하되 overrunEvent 장면 밀도를 먹지 않는다.
- verification
  - defeat/victory/CTA 각 노출, CTA 비차단, reload 복구, overrun flavor 비침범.

### B-4 UI / 아트 체크
- UI는 아래만 확인한다.
  - ending은 결과 해석과 CTA 의미까지만 말한다.
  - `themeTrophy` / `enemyTrophy` / `none` flavor는 ending body/helper에 들어오지 않는다.
- 아트는 아래만 체크한다.
  - ending tone은 overrunEvent scene tone과 구분된다.
  - overrun bridge는 `다음 장면을 기대시키는 정도`만 남고 flavor 자체는 선반영하지 않는다.

### B-4 QA / PM 제출 묶음
1. defeat primer 화면
2. victory primer 화면
3. overrun CTA primer 화면
4. boundary 유지 메모
5. CTA 비차단 / reload 복구 메모
6. handoff 문장

### B-4 완료 문장
> 이번 커밋은 ending primer의 결과/CTA 의미와 overrunEvent 비침범 경계를 닫아, 프로그래머/UI/아트/QA/PM이 같은 ending 종료선과 같은 closing 문장으로 온보딩 B-track을 마감할 수 있게 만들었다.

---

## 5. surface handoff 규칙

### 공통 원칙
1. `surface 1개 = commit 1개`를 깨지 않는다.
2. UI/아트는 해당 surface의 readable sanity까지만 닫고, 다음 surface tone을 선행 마감하지 않는다.
3. QA/PM은 `온보딩 추가` 같은 포괄 문장을 금지하고 surface명을 포함한 handoff 문장만 남긴다.

### surface별 handoff 큐
- B-1: 프로그래머 → QA → UI/아트 → PM
- B-2: 프로그래머 → UI → QA → PM
- B-3: 프로그래머 → UI/아트 → QA → PM
- B-4: 프로그래머 → UI ↔ 아트 → QA → PM

### 금지 패턴
- B-1에서 battle hint를 같이 닫기
- B-2에서 reward/shop primer를 같이 붙이기
- B-3에서 ending CTA 의미를 선행 설명하기
- B-4에서 overrunEvent flavor를 ending 본문/helper로 옮기기

---

## 6. 공통 evidence bundle

1. 해당 surface primer 화면 증거
2. seen flag 또는 동등 상태 저장 메모
3. 비차단 / reload 복구 메모
4. UI/아트 sanity 메모
5. 코드 diff 요약
6. surface 전용 handoff 문장

---

## 7. 리스크 & 후속과제

- B-1/B-2에서 UI/아트가 너무 일찍 밀도를 확정하면 primer floor와 후속 surface tone이 섞일 수 있다.
- B-3에서 reward와 shop을 같은 작은 안내 strip으로 처리하면 목적 분리 handoff가 무너질 수 있다.
- B-4에서 PM이 ending green과 overrunEvent green을 같은 문장으로 기록하면 boundary closing이 다시 흐려질 수 있다.
- 다음 실구현은 반드시 `runtime stack -> handoff scorecard -> owner workboard` 순서로 읽고, 각 surface를 별도 evidence bundle과 별도 handoff 문장으로만 닫는다.

---

## 8. 즉시 다음 액션

1. A-track green 전에는 이 문서를 실행 문서가 아니라 준비 문서로만 유지한다.
2. A-track green 뒤에는 `runtime stack -> handoff scorecard -> owner workboard` 순서로 B-1 오너 handoff를 먼저 연다.
3. B-1/B-2/B-3/B-4는 각각 별도 커밋, 별도 evidence bundle, 별도 tracker/run log 문장으로만 남긴다.
4. B-4 green 뒤에도 ending flavor 보강이 아니라 `boundary 유지 확인`만 추가 확인한다.
