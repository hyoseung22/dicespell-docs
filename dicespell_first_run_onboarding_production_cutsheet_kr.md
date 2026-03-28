# DiceSpell 첫 런 온보딩 production cutsheet

## 빠른 안내

### 3줄 요약
- 이 문서는 첫 런 온보딩 문서 묶음을 실제 제작 handoff 단위로 압축한 **production cutsheet**다.
- 프로그래머·UI·아트·QA가 각각 어떤 산출물을 어떤 순서로 넘기면 되는지 첫 화면에서 확인할 수 있게 정리한다.
- 상세 방향 설명보다 **실제 전달물, 파일 경계, 완료선**을 빠르게 찾는 문서로 읽으면 된다.

**한 줄 목표:** 온보딩 작업을 owner별 산출물과 인수인계 순서로 바로 실행 가능한 제작 패킷으로 묶는다.

### 왜 이 문서가 필요한가
- handoff와 screen packet이 좋아도, 제작 단계에서는 `그래서 내가 지금 뭘 넘기면 끝인가`가 별도 문서로 보이는 편이 훨씬 빠르다.
- 이 문서는 포털/노션에서 훑어도 바로 handoff-ready 상태를 읽게 만드는 정리본이다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | 대상 파일, helper/UI 연결 산출물 | `integration workorder` |
| UI/아트 | 필요한 컴포넌트·자산 패키지 | `screen packet`, `content deck` |
| QA | 증거 묶음과 DoD | `acceptance matrix` |
| PM | 인수인계 순서와 범위 | `summary` |

---

## 문서 목적

이 문서는 이미 정리된 첫 런 온보딩 문서 묶음을, **실제 제작자가 바로 들고 들어가도 범위가 다시 커지지 않는 production handoff packet**으로 다시 묶는다.

기준 문서:

- `docs/dicespell_first_run_onboarding_handoff_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
- `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- `docs/dicespell_first_run_onboarding_source_of_truth_map_kr.md`

지금 온보딩 축은 이미 `무슨 surface가 필요한가`, `어디에 붙는가`, `어떤 상태를 저장하는가`, `언제 완료라고 말할 수 있는가`까지는 충분히 닫혀 있다.
하지만 실제 제작 착수 직전 기준으로는 아직 마지막 빈칸이 하나 남아 있다.

> 그래서 프로그래머 / UI / 아트 / QA가 **각자 무엇을 넘기면 이 bounded UX pass가 끝나는가?**

이 문서는 그 질문에만 답한다.
즉 새 기획서가 아니라, **실제 산출물 · 대상 파일 · owner별 종료 조건 · 인수인계 순서**를 한 장으로 고정하는 cutsheet다.

---

## 1. 왜 지금 이 문서가 필요한가

현재 tracker 기준으로 최우선 blocker는 여전히 `overrunEvent` Step 0~4 실제 코드 컷오버다.
다만 그 바로 다음 UX pass로 예약된 첫 런 온보딩도, 실제 작업자가 들어갈 때 아래 같은 재해석이 다시 생길 여지가 있다.

1. 프로그래머는 `meta.uiGuidance`, `getPrimerState()`, render slot, event hook을 어디까지 이번 pass 범위로 봐야 하는지 다시 조합하게 된다.
2. UI owner는 library/battle/reward/shop/ending primer가 각각 `hero-card`, `inline ribbon`, `badge-purpose line` 중 어느 밀도로 끝나는지 문서 여러 장을 왕복하며 다시 읽게 된다.
3. 아트 owner는 “새 자산이 정말 필요한가, 필요하다면 어디까지가 최소 패키지인가”를 다시 판단하게 된다.
4. QA는 acceptance 8종, recovery 6종이 있다는 사실은 알아도, 어떤 순서로 pass/fail을 끊고 어떤 캡처를 남겨야 하는지가 한 장에 모여 있지 않다.

즉 지금 부족한 것은 추가 아이디어가 아니라, **작업 시작 전에 바로 체크리스트처럼 펼칠 수 있는 production cutsheet**다.

---

## 2. 이번 bounded pass의 한 줄 정의

> 첫 런 플레이어가 `도서관`, `첫 전투`, `첫 reward/shop`, `첫 ending`에서 각각 한 번씩만 필요한 primer를 보고, 그 primer가 진행을 막지 않으며, 저장 누락 시에도 최악의 결과가 `재노출`로만 남는 상태를 닫는다.

이 범위를 벗어나는 것은 이번 cutsheet의 out-of-scope다.

### 이번 pass에 포함되는 것

- `meta.uiGuidance` 또는 동등 구조의 seen 저장
- `getPrimerState()` 또는 동등 helper 기반 primer payload 계산
- `renderGrimoireShelf()` / `renderBattle()` / `renderReward()` / `renderShop()` / `renderEnding()` primer slot
- 전투의 `첫 주문` / `자동 종료` 사건 기반 힌트 hook
- reward vs shop 목적 구분용 시각 계층
- ending primer의 `무엇이 남는가 / 버튼 의미` 보조 문구

### 이번 pass에 포함되지 않는 것

- 별도 튜토리얼 모드
- 고정 시나리오 전투
- 대형 help modal / 백과사전 UI
- 새 메타 보상 / 튜토리얼 전용 화폐
- `overrunEvent` 장면 자체 구현
- 모바일 전용 재설계
- 대형 일러스트 / 컷신 파이프라인

---

## 3. 현재 코드 기준 병목 요약

### 병목 A. primer source of truth 부재
- 현재 `app.js` / `meta-progression.js`에는 첫 런 primer용 장기 seen 저장 구조가 없다.
- 결과적으로 surface마다 `지금 보여 줄지`를 제각각 계산하게 될 위험이 있다.

### 병목 B. render slot은 있지만 primer slot 계약이 없다
- `renderGrimoireShelf()`, `renderBattle()`, `renderReward()`, `renderShop()`, `renderEnding()`에는 본문용 slot이 이미 있지만, primer용 slot이라는 계약은 아직 코드에 없다.

### 병목 C. 전투 후속 힌트는 render만으로 닫히지 않는다
- `첫 주문`과 `자동 종료` 힌트는 사건 기반이라 cast/auto-pass hook을 건드려야 한다.

### 병목 D. reward / shop / ending은 카피보다 위계가 더 중요하다
- reward와 shop은 `문장 하나`보다도, 서로 다른 공간처럼 읽히는 시각 위계가 더 중요하다.
- ending은 `overrunEvent`와 문맥 경계를 잘못 잡으면 곧바로 역할 충돌이 난다.

즉 이번 pass는 텍스트 추가 작업이 아니라, **상태 · render slot · 사건 hook · 위계 분리**를 같이 닫아야 한다.

---

## 4. owner별 산출물 컷시트

## 4.1 프로그래머

### 대상 파일

- `dice-spell-standalone/src/app.js`
- `dice-spell-standalone/src/meta-progression.js`
- 필요 시 `dice-spell-standalone/src/styles.css`
- `dice-spell-standalone/scripts/smoke-test.mjs`

### 필수 산출물

1. `meta.uiGuidance` 또는 동등 구조 기본값/정규화
2. `getPrimerState(summary, appState)` 또는 동등 helper
3. 아래 render surface primer slot
   - `renderGrimoireShelf()`
   - `renderBattle(summary)`
   - `renderReward(summary)`
   - `renderShop()`
   - `renderEnding(summary)`
4. 사건 기반 seen 갱신 hook
   - 첫 주문 성공 직후
   - 자동 종료 직후
5. primer 비노출 상태에서도 기존 render와 진행이 그대로 유지되는 fallback
6. smoke 고정
   - 첫 진입 primer
   - dismiss 후 재진입 비노출
   - 첫 주문 힌트
   - 자동 종료 힌트
   - reward / shop purpose line 분리
   - 패배 / 승리 / 오버런 ending primer
   - `uiGuidance` 누락 fallback

### DoD

- `봤는가`와 `지금 어떻게 보이는가`를 같은 객체에 섞지 않았다.
- primer가 없는 상태에서도 기존 화면이 깨지지 않는다.
- primer 저장 누락은 크래시나 진행 차단이 아니라 `재노출` 수준으로만 남는다.
- battle primer는 action order를 바꾸지 않는다.
- ending primer는 `overrunEvent` 장면 설명을 먹지 않는다.

### 넘기면 안 되는 것

- 큰 modal 추가
- primer 표시를 위해 핵심 action flow 변경
- 새 데이터 필드 강제 추가
- `overrunEvent` flavor까지 ending primer에 미리 넣기

---

## 4.2 UI owner

### 대상 surface

- Library primer
- Battle hero-primer / inline-primer 2종
- Reward purpose primer
- Shop purpose primer
- Ending compact primer / CTA subcopy

### 필수 산출물

1. **Library:** compact hero-primer 1종 + `openingPlan` / `caution` highlight rule
2. **Battle:**
   - 진입 hero-primer 1종
   - 첫 주문 inline ribbon 1종
   - 자동 종료 inline ribbon 1종
3. **Reward / Shop:** 서로 다른 목적을 읽히게 하는 accent + badge + line 구조 2종
4. **Ending:**
   - 패배/승리/오버런 공용 compact line rule
   - `오버런 계속` 주변 CTA subcopy rule

### DoD

- primer는 설명 레이어이지 새로운 주인공 카드가 아니다.
- Library/Battle은 hero-card급이지만 화면 핵심 인터랙션을 가리지 않는다.
- Reward/Shop/Ending은 badge/line급으로 충분하고, 과도한 card 확장을 만들지 않는다.
- reward와 shop은 같은 문장을 색만 다르게 바꾼 수준이 아니라, 목적이 다른 공간처럼 읽힌다.
- ending primer는 정산 card와 CTA보다 낮은 위계다.

### 금지 패턴

- fullscreen modal
- primer 때문에 첫 screen fold가 지나치게 길어지는 레이아웃
- reward와 shop의 동일한 accent 재사용
- 승리/패배/오버런을 모두 hero-card로 올리는 과도한 강조

---

## 4.3 아트 owner

### 최소 패키지 원칙

이번 pass는 신규 시스템용 대형 아트가 아니라, **공용 primer kit** 정도면 충분하다.

### 필수 산출물

1. Library 공용 backplate/ribbon 1종
2. Battle hero-primer 공용 glyph 3종
   - 주사위
   - 주문 1회
   - 잠금/로그
3. Reward accent 1종
4. Shop accent 1종
5. Ending 상태 badge 3종
   - 남는 진척
   - 완주
   - 오버런

### DoD

- 기존 DiceSpell 톤(금빛/서고/마도서 계열)을 유지한다.
- surface별 차이는 분명하지만, 새 시스템처럼 튀지 않는다.
- Library/Battle/Reward/Shop/Ending이 하나의 primer 계열 UI로 묶여 보인다.

### 범위 밖

- 책별 전용 primer 삽화
- 컷신용 아트
- `overrunEvent` 전용 카드 일러스트

---

## 4.4 QA owner

### 필수 검수 묶음

#### Acceptance 8종
1. 도서관 hero-primer
2. 첫 전투 hero-primer
3. 첫 주문 inline-primer
4. 자동 종료 inline-primer
5. 첫 reward purpose primer
6. 첫 shop purpose primer
7. 패배 ending primer
8. 승리 / 오버런 ending primer

#### Recovery 6종
1. 오래된 meta에 `uiGuidance` 없음
2. primer 일부 플래그 누락
3. battle hook가 한 프레임 늦거나 누락
4. reward / shop phase 건너뛰기
5. ending primer와 `overrunEvent` 경계 변화
6. primer DOM 누락 시 fallback 문구 유지

### 필요 증거

- smoke pass 기록
- 최소 1회 수동 캡처
  - library
  - battle 진입
  - first spell
  - auto-pass
  - reward
  - shop
  - defeat ending
  - victory/overrun ending
- 기존 세이브 로드 + 새 프로필 시작 2축 확인

### DoD

- primer 보이기/숨기기보다, **진행이 막히지 않는가**를 먼저 본다.
- reward와 shop은 카피 차이뿐 아니라 공간 목적 차이가 읽히는지 본다.
- ending은 `무엇이 남는가 / 버튼 의미`까지만 말하는지 본다.
- 실패 케이스는 `보여야 하는데 안 보임`뿐 아니라 `보이면 안 되는 데 또 뜸`, `가리면 안 되는 걸 가림`도 같이 기록한다.

---

## 5. 인수인계 순서

### Packet A. 프로그래머 kickoff
1. `docs/dicespell_first_run_onboarding_source_of_truth_map_kr.md`
2. `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
3. `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`

### Packet B. UI / 아트 kickoff
1. `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
2. 이 cutsheet
3. 필요 시 `docs/dicespell_first_run_onboarding_handoff_kr.md`

### Packet C. QA kickoff
1. `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
2. 이 cutsheet
3. `docs/dicespell_first_run_onboarding_source_of_truth_map_kr.md`

원칙은 단순하다.
- 프로그래머는 `상태/순서/복구`부터 읽는다.
- UI/아트는 `화면 밀도/위계/최소 패키지`부터 읽는다.
- QA는 `완료선/복구선/질문별 source-of-truth`부터 읽는다.

---

## 6. 실제 구현 순서 제안

1. `meta.uiGuidance` 기본값과 helper를 먼저 닫는다.
2. Library + Battle hero-primer를 먼저 붙인다.
3. 첫 주문 / 자동 종료 event hook을 붙인다.
4. Reward / Shop purpose primer를 붙인다.
5. Ending primer와 CTA subcopy를 가장 마지막에 붙인다.
6. recovery 6종까지 smoke와 수동 검수로 닫는다.

이 순서를 바꾸면 ending 문맥이 먼저 커지거나, battle 사건 기반 힌트가 뒤늦게 붙어 문서 해석이 다시 흔들릴 가능성이 커진다.

---

## 7. 리스크 & 후속 handoff

### 리스크 1. primer가 커져서 실제 UX를 방해할 위험
- 영향: 첫 런 해석 비용을 낮추려다 오히려 화면 정보량이 늘어난다.
- 대응: Library/Battle만 hero-card급, 나머지는 badge/line급으로 제한한다.

### 리스크 2. reward / shop의 차이가 문장만 다른 수준으로 남을 위험
- 영향: 플레이어는 여전히 둘을 같은 선택 화면으로 읽는다.
- 대응: accent, 배치, 위계를 분리하고 QA에서도 카피 외 시각 차이를 별도 항목으로 본다.

### 리스크 3. ending primer가 `overrunEvent` 역할을 먹는 위험
- 영향: 이후 `overrunEvent` 구현 때 문맥이 중복되거나 더러워진다.
- 대응: ending은 끝까지 `무엇이 남는가 / 버튼 의미`만 말한다.

### 다음 handoff action

- `overrunEvent` 실제 코드 컷오버가 끝나면, 그 다음 UX pass kickoff 문서로 이 cutsheet를 함께 읽는다.
- 실제 primer 구현이 붙은 뒤에는 필요하면 후속으로 `onboarding smoke migration packet`을 만들 수 있지만, 현재 우선순위는 여기서 멈추고 코드 컷오버로 넘어가는 쪽이 맞다.

---

## 8. 최종 결론

첫 런 온보딩 축은 이제 방향, 화면, 통합 순서, acceptance, source-of-truth에 더해 **owner별 production cutsheet**까지 갖췄다.

즉 다음 UX pass는 더 이상

- 무엇을 만들지,
- 어떤 surface를 쓸지,
- 누가 무엇을 넘기면 끝인지

를 다시 토론하는 자리가 아니다.

이제 남은 것은 실제로 `도서관 + 첫 전투 primer`부터 붙이고, reward/shop/ending을 같은 payload 체계로 닫는 일이다.
