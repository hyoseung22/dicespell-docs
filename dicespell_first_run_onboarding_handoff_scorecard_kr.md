# DiceSpell 첫 런 온보딩 핸드오프 스코어카드

## 3줄 요약
- 이 문서는 첫 런 온보딩 B-track을 실제로 닫을 때 구현자와 리뷰어가 같은 완료선으로 움직이도록 만드는 **fill-in-ready source handoff**다.
- 현재 DiceSpell에는 B-1~B-4 packet과 runtime stack까지 이미 충분하지만, 실제 착수 직전에는 여전히 `그래서 이번 커밋에 무엇을 붙이고 어떤 증거를 남기면 끝인가`가 surface마다 다시 흩어질 위험이 남아 있다.
- 목표는 `B-1 library`, `B-2 battle`, `B-3 reward/shop`, `B-4 ending`을 각각 **작업 체크리스트 + 증거 체크리스트 + tracker/run log 문장**으로 다시 압축해, 프로그래머/UI/아트/QA/PM이 같은 handoff 언어를 재사용하게 만드는 것이다.

## 한 줄 목표
온보딩 B-track의 4개 surface를 **복붙 가능한 실행/검수 스코어카드**로 다시 압축해, `primer 전체 붙이기` 같은 범위 팽창 없이 surface별 완료 문장과 evidence bundle을 고정한다.

## 왜 이 문서가 필요한가
지금 기준 문서는 이미 충분하다.

- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b2_battle_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md`
- `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`

문제는 방향 부족이 아니라 **실행 체크와 기록 체크의 재조합 비용**이다.

1. 구현자는 surface packet을 읽어도 `이번 턴에 어느 hook와 어떤 저장 플래그까지만 닫을지`를 다시 머릿속에서 합쳐야 한다.
2. QA는 acceptance matrix를 읽어도 `이번 커밋에서 꼭 확인해야 하는 trigger/evidence`를 surface별로 다시 축약해야 한다.
3. UI/아트는 화면과 토큰 기준을 알아도 `이번 턴에 지금 검수해야 하는 밀도 sanity`와 `아직 미뤄야 하는 polish`를 분리하지 못하면 범위가 쉽게 커진다.
4. PM/기획은 `온보딩 추가` 같은 뭉뚱그린 문장 대신 `어느 surface가 green인지`를 tracker/run log에 정확히 남겨야 한다.

즉 지금 필요한 것은 새 primer가 아니라, 이미 닫힌 B-track 문서를 **실행 체크리스트와 handoff 체크리스트**로 한 번 더 압축하는 일이다.

---

## 이 문서는 누가 무엇부터 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 쓸 부분 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. B-1`, `2. B-2`, `3. B-3`, `4. B-4` | `6. 복붙용 기록 문장` | 이번 턴 범위와 evidence bundle을 여러 문서 재조합 없이 바로 실행한다 |
| QA | `5. 공통 검수 체크`, `6. 복붙용 기록 문장` | `7. 제출 묶음 순서` | surface별 green/fail을 같은 번들로 기록한다 |
| UI | `2. B-2`, `3. B-3`, `4. B-4` | `5. 공통 검수 체크` | primer readable sanity만 닫고 unrelated polish를 넣지 않는다 |
| 아트 | `1. B-1`, `3. B-3`, `4. B-4` | `7. 제출 묶음 순서` | 새 대형 자산보다 token/accent/readability drift 체크가 우선임을 확인한다 |
| PM/기획 | `0. first screen 결론`, `6. 복붙용 기록 문장`, `8. 리스크 & 후속과제` | `9. 즉시 다음 액션` | B-1~B-4를 다른 완료 문장으로 기록한다 |

---

## 0. first screen 결론

### 이 문서를 왜 열어야 하나
- **프로그래머**: 이번 커밋에서 실제로 건드려도 되는 hook/flag/render 범위를 바로 확인하려고 연다.
- **UI/아트**: 지금 턴이 primer readable sanity만 보는 턴인지, 아직 polish를 하면 안 되는 턴인지 확인하려고 연다.
- **QA/PM**: 이번 턴이 library/battle/reward-shop/ending 중 어떤 surface green인지 다른 문장으로 기록하려고 연다.

### 한 줄 판정 규칙
- `B-1`은 **첫 책 선택 hero-primer와 seen 제어**가 닫히면 끝이다.
- `B-2`는 **entry / first-spell / auto-pass primer와 비차단성**이 닫혀야 끝이다.
- `B-3`는 **reward vs shop 목적 분리**가 primer layer에서 닫혀야 끝이다.
- `B-4`는 **ending 결과/CTA 의미만 남기고 overrun flavor 비침범 경계**가 유지돼야 끝이다.
- `B-4`가 green이 되기 전에는 `온보딩 전체 polish`나 `overrunEvent flavor 보강`을 새 목표로 열지 않는다.

---

## 1. B-1 스코어카드 — Library Primer Floor

## 3줄 요약
- B-1은 `renderGrimoireShelf()`에서 첫 책 선택 순간의 hero-primer 1장과 `seenBookSelectPrimer` 저장/복구를 닫는 턴이다.
- 이 단계에서는 선택 자체를 방해하지 않는 것이 중요하고, battle/reward/shop/ending primer는 일부러 열지 않는다.
- 즉 B-1의 성공 기준은 `설명 카드가 추가되었다`가 아니라 `첫 진입 guidance가 비차단 상태로 고정되었다`다.

### 한 줄 목표
책 선택 surface를 `첫 진입 guidance layer`로 닫되, 책 카드 조작과 이후 surface까지는 절대 건드리지 않는다.

### 프로그래머 작업 체크

| 체크 | 기대값 | 파일 / surface |
| --- | --- | --- |
| B1-1 | `seenBookSelectPrimer`가 `meta.uiGuidance`에 저장/복구된다 | `src/game-engine.js` 또는 동등 storage layer |
| B1-2 | `renderGrimoireShelf()`가 첫 진입에만 hero-primer를 주입한다 | `src/app.js` |
| B1-3 | primer가 책 선택 CTA를 가리거나 막지 않는다 | `src/app.js`, `src/styles.css` |
| B1-4 | `openingPlan` / `caution` highlight가 primer와 충돌하지 않는다 | `src/app.js` |
| B1-5 | reload/revisit 시 재노출 제어가 동일하게 유지된다 | `scripts/smoke-test.mjs` 또는 focused verification |

### B-1에서 일부러 하지 말 것

| 금지 | 이유 |
| --- | --- |
| battle primer 추가 | B-1 완료선이 흐려진다 |
| library 카드 레이아웃 전면 리뉴얼 | primer floor와 UI 리디자인이 섞인다 |
| 신규 책 소개 문구 확장 | source-of-truth 범위를 넘어 tone drift가 커진다 |
| ending/overrun CTA 카피 조정 | shared boundary와 unrelated surface가 섞인다 |

### B-1 제출 증거

| 증거 | 최소 내용 |
| --- | --- |
| 화면 증거 | 첫 진입 primer 1개, 재진입 미노출 1개 |
| 상태 diff 메모 | `seenBookSelectPrimer=true` 또는 동등 상태 저장 |
| 검증 | 선택 비차단, reload 유지, primer 미중첩 |
| 코드 diff 메모 | shelf hero-primer + seen 저장/복구만 포함 |
| 한 줄 handoff | `library primer handoff를 닫고 seenBookSelectPrimer 기준 첫 진입 guidance만 고정했다.` |

### B-1 fail 신호
- primer가 책 선택 버튼/카드를 가린다.
- 재진입마다 hero-primer가 반복 노출된다.
- battle primer까지 같은 커밋에 섞였다.
- storage가 없어 reload 후 표정이 달라진다.

---

## 2. B-2 스코어카드 — Battle Primer Slice

## 3줄 요약
- B-2는 `battle.entry`, `battle.first-spell`, `battle.auto-pass` 3면의 primer와 재노출 제어를 묶어 닫는 턴이다.
- 이 단계의 핵심은 전투 조작을 막지 않으면서 `무엇을 해야 하는지`와 `왜 자동 종료되었는지`를 같은 battle surface 안에서 읽히게 만드는 것이다.
- 즉 B-2의 성공 기준은 힌트 개수보다 `trigger 분리`와 `비차단성`이다.

### 한 줄 목표
entry / first-spell / auto-pass 3개 primer를 battle surface 안에 닫되, reward/shop/ending 설명은 아직 열지 않는다.

### 프로그래머 작업 체크

| 체크 | 기대값 | 파일 / surface |
| --- | --- | --- |
| B2-1 | `seenBattlePrimer`, `seenFirstSpellHint`, `seenAutoPassHint`가 저장/복구된다 | storage layer |
| B2-2 | 전투 첫 진입 시 `battle.entry` primer가 1회 노출된다 | `src/app.js` |
| B2-3 | 첫 주문 성공 후 `battle.first-spell` inline hint가 트리거된다 | `src/app.js` |
| B2-4 | 주문 불가 auto-pass 시 `battle.auto-pass` hint가 뜬다 | `src/app.js`, `src/game-engine.js` |
| B2-5 | 힌트 2개 이상이 같은 frame에서 과밀 중첩되지 않는다 | `src/app.js`, `src/styles.css` |

### B-2에서 일부러 하지 말 것

| 금지 | 이유 |
| --- | --- |
| reward/shop primer 추가 | B-2 검수선이 흐려진다 |
| 전투 로그/버튼 카피 전면 교체 | primer hook 검증과 unrelated UX가 섞인다 |
| 새 전투 규칙/메카닉 추가 | source handoff가 구현 범위를 벗어난다 |
| ending CTA 설명 선반영 | B-4 경계가 흐려진다 |

### B-2 제출 증거

| 증거 | 최소 내용 |
| --- | --- |
| 화면 증거 | entry / first-spell / auto-pass 각 1개 이상 |
| 상태 diff 메모 | 3개 seen flag 저장 또는 동등 재노출 제어 |
| 검증 | 전투 조작 비차단, reload 유지, 힌트 중첩 방지 |
| 코드 diff 메모 | battle primer hook 3종만 포함 |
| 한 줄 handoff | `battle primer handoff를 닫고 entry/first-spell/auto-pass guidance를 전투 조작 비차단 상태로 고정했다.` |

### B-2 fail 신호
- first-spell과 auto-pass 힌트가 같은 타이밍에 겹친다.
- primer가 roll/lock/cast 조작을 막는다.
- reward/shop 문구까지 같은 커밋에 섞였다.
- reload 뒤 동일 trigger가 반복 폭주한다.

---

## 3. B-3 스코어카드 — Reward / Shop Split Slice

## 3줄 요약
- B-3는 `renderReward(summary)`와 `renderShop()`에서 reward vs shop 목적 분리를 primer layer로 닫는 턴이다.
- 핵심은 화면을 더 화려하게 만드는 것이 아니라, `전투 보상은 지금 살리는 선택`, `상점은 다음 전투를 준비하는 선택`을 즉시 읽히게 하는 것이다.
- 즉 B-3의 성공 기준은 새 규칙이 아니라 **목적 분리의 가독성**이다.

### 한 줄 목표
reward와 shop의 목적 차이를 primer layer에서 고정하되, 실제 보상 규칙/가격/표는 건드리지 않는다.

### 프로그래머 / UI 작업 체크

| 체크 | 기대값 | 파일 / surface |
| --- | --- | --- |
| B3-1 | `seenRewardPrimer`, `seenShopPrimer` 저장/복구가 동작한다 | storage layer |
| B3-2 | reward 첫 노출에서 즉시 적용/회복/강화 성격이 primer로 읽힌다 | `src/app.js` |
| B3-3 | shop 첫 노출에서 장기 빌드/투자 성격이 primer로 읽힌다 | `src/app.js` |
| B3-4 | boss draft 또는 fallback 흐름과 primer가 충돌하지 않는다 | `src/app.js`, `src/game-engine.js` |
| B3-5 | reward와 shop의 accent/tone이 서로 다른 목적을 유지한다 | `src/styles.css`, content token layer |

### B-3에서 일부러 하지 말 것

| 금지 | 이유 |
| --- | --- |
| 새 reward table/가격 조정 | primer와 밸런스 조정이 섞인다 |
| ending/overrun 카피 수정 | B-4/shared boundary가 흐려진다 |
| shop UI 전면 리디자인 | 목적 분리보다 시각 개편이 앞선다 |
| battle primer 재서술 | 같은 학습 문구가 중복된다 |

### B-3 제출 증거

| 증거 | 최소 내용 |
| --- | --- |
| 화면 증거 | reward primer 1개, shop primer 1개, accent 분리 캡처 |
| 상태 diff 메모 | reward/shop seen flag 저장 또는 동등 제어 |
| 검증 | CTA 비차단, boss draft 공존 또는 fallback 유지 |
| 코드 diff 메모 | reward/shop primer와 목적 분리 hierarchy만 포함 |
| 한 줄 handoff | `reward/shop primer handoff를 닫고 전투 보상 vs 상점 목적 분리를 primer layer에서 고정했다.` |

### B-3 fail 신호
- reward와 shop이 같은 tone으로 읽힌다.
- primer가 실제 reward 규칙 변경까지 끌고 들어갔다.
- boss draft 동선이 primer 때문에 밀리거나 깨진다.
- ending 결과 설명이 같은 커밋에 섞인다.

---

## 4. B-4 스코어카드 — Ending Primer Closing

## 3줄 요약
- B-4는 `renderEnding(summary)`와 `오버런 계속` CTA 주변의 낮은 밀도 primer를 닫는 턴이다.
- 이 단계의 핵심은 결과 해석과 CTA 의미만 남기고, 장면/flavor는 끝까지 `overrunEvent`에 남겨 두는 것이다.
- 즉 B-4의 성공 기준은 `친절해 보임`이 아니라 `ending과 overrunEvent 책임이 섞이지 않음`이다.

### 한 줄 목표
ending surface를 결과/정산/CTA 의미까지만 설명하는 closing commit으로 닫고, overrun flavor는 끝까지 비침범 상태로 유지한다.

### 프로그래머 / UI / 아트 작업 체크

| 체크 | 기대값 | 파일 / surface |
| --- | --- | --- |
| B4-1 | `seenDefeatPrimer`, `seenVictoryPrimer`, `seenOverrunCtaPrimer`가 저장/복구된다 | storage layer |
| B4-2 | defeat / victory / overrun CTA 분기가 primer와 함께 정상 노출된다 | `src/app.js` |
| B4-3 | ending primer가 결과/버튼 의미만 설명하고 flavor를 재서술하지 않는다 | `src/app.js`, content layer |
| B4-4 | `themeTrophy` / `enemyTrophy` / `none` flavor는 `overrunEvent`에 남고 ending에는 끌려오지 않는다 | boundary surface |
| B4-5 | CTA 비차단 / reload 복구 / overrun 진입 후 flavor 비중복이 유지된다 | `scripts/smoke-test.mjs` 또는 focused verification |

### B-4에서 일부러 하지 말 것

| 금지 | 이유 |
| --- | --- |
| overrunEvent flavor 선반영 | ending↔overrun 경계가 무너진다 |
| 엔딩 화면 대개편 | primer closing과 UI 리뉴얼이 섞인다 |
| 새 결과 규칙/보상 추가 | B-4 검수선이 흐려진다 |
| onboarding 전체 recap 모달 추가 | surface별 종료 문장이 사라진다 |

### B-4 제출 증거

| 증거 | 최소 내용 |
| --- | --- |
| 화면 증거 | defeat / victory / overrun CTA 각 1개 이상 |
| 경계 증거 | ending vs overrunEvent shell 차이, flavor 비중복 캡처 |
| 상태 diff 메모 | ending primer seen flag 저장 또는 동등 제어 |
| 검증 | CTA 비차단, reload 유지, overrun 진입 후 flavor 비침범 |
| 한 줄 handoff | `ending primer handoff를 닫고 결과/CTA 의미만 남기며 overrunEvent flavor 비침범 경계를 유지했다.` |

### B-4 fail 신호
- ending primer가 `themeTrophy` / `enemyTrophy` / `none` flavor를 다시 설명한다.
- overrun CTA 의미와 overrun scene flavor가 하나의 카드처럼 섞인다.
- defeat/victory/overrun 분기 중 하나라도 primer trigger가 빠진다.
- `온보딩 전체 polish`나 UI 대개편이 같은 커밋에 섞인다.

---

## 5. 공통 검수 체크

## 한 줄 목표
모든 역할이 `좋아 보인다`가 아니라 **같은 checkline**으로 pass/fail을 말하게 만든다.

| 공통 체크 | B-1 기대값 | B-2 기대값 | B-3 기대값 | B-4 기대값 |
| --- | --- | --- | --- | --- |
| 범위 고정 | library primer만 | battle primer만 | reward/shop primer만 | ending primer만 |
| 저장/복구 | first-entry seen 유지 | trigger별 seen 유지 | reward/shop seen 유지 | defeat/victory/CTA seen 유지 |
| 비차단성 | 책 선택 방해 없음 | 전투 조작 방해 없음 | reward/shop CTA 방해 없음 | ending CTA 방해 없음 |
| 비변경선 | battle 이후 surface 불개입 | reward/shop/ending 불개입 | 규칙/가격/ending 불개입 | overrun flavor 비침범 |
| 기록 문장 | library green | battle green | reward/shop green | ending green |
| 다음 단계 허용 | B-2만 연다 | B-3만 연다 | B-4만 연다 | B-track recap 또는 후속 QA만 연다 |

### 역할별 빠른 handoff
- **프로그래머**: surface별 hook와 seen flag만 닫고 다른 surface를 끌고 들어오지 않는다.
- **UI**: readable sanity만 닫고 전역 리디자인/카피 재서술을 섞지 않는다.
- **아트**: 새 대형 자산보다 token/accent/readability drift만 먼저 본다.
- **QA**: B-1~B-4 green 문장을 같은 코멘트로 뭉개지 않는다.
- **PM/기획**: `온보딩 추가`가 아니라 `어느 surface를 닫았는지`를 그대로 기록한다.

---

## 6. 복붙용 기록 문장

### B-1용 tracker / run log 문장
> 이번 커밋은 library primer handoff를 닫고 `seenBookSelectPrimer` 기준 첫 진입 guidance만 고정했다.

### B-2용 tracker / run log 문장
> 이번 커밋은 battle primer handoff를 닫고 entry / first-spell / auto-pass guidance를 전투 조작 비차단 상태로 고정했다.

### B-3용 tracker / run log 문장
> 이번 커밋은 reward/shop primer handoff를 닫고 전투 보상 vs 상점 목적 분리를 primer layer에서 고정했다.

### B-4용 tracker / run log 문장
> 이번 커밋은 ending primer handoff를 닫고 결과/CTA 의미만 남기며 `overrunEvent` flavor 비침범 경계를 유지했다.

### 리뷰 코멘트 템플릿
```md
- 확인 범위: B-1 / B-2 / B-3 / B-4 중 하나만 검수
- 확인 증거: 화면 캡처, 상태 diff, focused verification 또는 smoke, 코드 diff, handoff 문장
- 판정: 현재 surface green이면 다음 surface만 열고, 다른 primer/polish 확장은 열지 않는다
```

---

## 7. 제출 묶음 순서

## 3줄 요약
- 구현이 끝나도 제출 묶음이 흐리면 tracker/run log가 다시 `온보딩 추가` 같은 과장 문장으로 돌아가기 쉽다.
- 따라서 증거는 항상 같은 순서로 남긴다.
- 이 순서를 지키면 runtime stack packet과 acceptance matrix를 실제 업무 기록으로 바로 옮길 수 있다.

### 권장 제출 순서
1. **focused verification 또는 affected smoke**
2. **상태 diff 메모**
3. **화면 evidence**
4. **코드 diff 요약**
5. **한 줄 handoff 문장**

### tracker / run log에 남길 때 필수 요소

| 항목 | B-1 | B-2 | B-3 | B-4 |
| --- | --- | --- | --- | --- |
| 작업 주제 | library primer floor | battle primer slice | reward/shop split slice | ending primer closing |
| 변경 요약 | hero-primer + seen 저장/복구 | entry/first-spell/auto-pass hint | reward vs shop 목적 분리 | 결과/CTA 의미 + boundary 유지 |
| 검증 | 선택 비차단 + 재진입 미노출 | 전투 비차단 + trigger 분리 | CTA 비차단 + 목적 분리 | boundary + flavor 비중복 |
| 다음 추천 | B-2만 연다 | B-3만 연다 | B-4만 연다 | recap 또는 후속 polish 후보 검토 |

---

## 8. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| B-1~B-4 기록 문장이 다시 `온보딩 추가` 한 문장으로 뭉개지는 위험 | surface별 종료선이 사라진다 | 복붙용 기록 문장과 제출 묶음 순서를 별도 고정 | 첫 실제 B-track 기록 시 |
| UI/아트가 B-2~B-4에서 polish 범위를 넓히는 위험 | primer sanity와 리디자인이 섞인다 | surface별 금지 목록과 handoff 문장을 명시 | 각 surface 착수 전 |
| ending primer가 overrun flavor를 다시 끌어오는 위험 | A-track/B-track boundary가 재오염된다 | B-4에 boundary check와 fail 신호를 포함 | B-4 리뷰 시 |
| reward/shop primer가 실제 규칙 조정과 섞이는 위험 | 밸런스 이슈와 primer 검증이 분리되지 않는다 | B-3 금지 범위에 규칙/가격 조정을 명시 | B-3 구현 전 |
| 다음 후속 작업 | A-track green 이후 B-track을 실제로 열 때 가장 먼저 재사용할 checkline이 필요했다 | 이 문서를 runtime stack packet 바로 다음 source handoff로 추가 | 첫 B-1 착수 전 |

---

## 9. 즉시 다음 액션

1. implementer는 `runtime stack packet -> B-1/B-2/B-3/B-4 packet -> acceptance matrix -> 이 문서` 순서로 실제 surface 범위를 다시 고정한다.
2. A-track이 green이면 B-track은 반드시 `B-1 -> B-2 -> B-3 -> B-4` 순서로만 연다.
3. 각 surface는 이 문서의 제출 묶음 순서와 handoff 문장을 그대로 재사용한다.
4. fail이면 새 primer 문서를 늘리지 말고, 현재 surface 범위 안에서만 원인을 수정한다.
5. B-4 green 전에는 `온보딩 전체 polish` 또는 `ending/overrun 통합 정리`를 새 목표로 열지 않는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b2_battle_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md`
- `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- `docs/dicespell_overrun_runtime_owner_workboard_kr.md`
