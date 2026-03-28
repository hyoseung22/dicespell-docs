# DiceSpell 첫 런 온보딩 screen packet

## 빠른 안내

### 3줄 요약
- 이 문서는 온보딩 primer를 **실제 화면 어디에 어떤 밀도로 꽂을지** 정하는 screen-level source of truth다.
- 프로그래머는 render surface와 주입 조건을, UI는 hero/inline/badge 밀도를, 아트는 강조 토큰과 최소 자산 범위를 먼저 보면 된다.
- 한 화면 한 메시지 원칙을 지키는 것이 핵심이므로, 새 설명을 늘리기보다 **현재 화면의 첫 스크린을 더 빨리 읽히게 만드는지**를 기준으로 본다.

**한 줄 목표:** `renderGrimoireShelf() / renderBattle() / renderReward() / renderShop() / renderEnding()` 기준으로 primer 주입 위치와 읽힘 우선순위를 흔들리지 않게 고정한다.

### 왜 이 문서가 필요한가
- handoff 문서가 방향을 정했다면, 이 문서는 실제 화면 레벨에서 `무엇이 먼저 보이고 무엇은 뒤로 미뤄지는지`를 정한다.
- 즉 구현 직전의 `어디에 붙이지?`와 UI 리뷰 단계의 `지금 너무 많나?`를 동시에 줄이기 위한 문서다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | surface별 주입 위치, 조건, helper 연결점 | `integration workorder` |
| UI | hero/inline/badge 위계, 화면별 메시지 밀도 | `content deck` |
| 아트 | badge/accent/art token이 필요한 화면 | `production cutsheet` |
| QA | surface별 보여야 할 것 / 보이면 안 되는 것 | `acceptance matrix` |

---

## 문서 목적

이 문서는 `docs/dicespell_first_run_onboarding_handoff_kr.md`가 이미 고정한 **4개 primer surface의 방향**을, 실제 구현자가 `app.js`의 어느 화면에 어떤 밀도로 꽂아야 하는지까지 내려서 정리한 **화면/카피/상태 handoff packet**이다.

즉 handoff 문서가 `왜 이 온보딩이 필요한가`와 `무엇을 일부러 하지 않을 것인가`를 고정했다면, 이 문서는 그 다음 질문에 답한다.

> 지금 코드 기준으로 어떤 render surface에 무엇을 붙이면, 프로그래머/UI/아트/QA가 같은 화면을 보고 바로 작업할 수 있는가?

이번 패킷은 거대한 튜토리얼 설계가 아니라, **현재 빌드에 이미 존재하는 화면 구조를 최대한 재사용하는 bounded screen spec**이다.

---

## 1. 현재 코드 기준 source-of-truth

이 문서는 아래 실제 render surface를 기준으로 한다.

- `renderGrimoireShelf()`
  - `dice-spell-standalone/src/app.js`
  - 책 선택 화면의 `.section-head`, `.body-copy`, `.grimoire-grid`, `.grimoire-card`
- `renderBattle(summary)`
  - `.battle-subcopy`, `.battle-action-panel`, `.battle-target-panel`, `.battle-hand-section`, 로그/타깃/주사위 인터랙션
- `renderReward(summary)`
  - 보상 화면의 `.section-head`, `.reward-block`, `.reward-grid`
- `renderShop()`
  - 상점 화면의 `.section-head`, `.body-copy`, `.shop-grid`
- `renderEnding(summary)`
  - 엔딩 화면의 `.ending-card`, `.meta-retirement-card`, `.hero-actions`, `오버런 계속` CTA

즉 이번 온보딩 surface는 새 phase를 만들지 않는다. `battle`, `reward`, `shop`, `ending`, `library` 안에 **조건부 primer block**을 얹는 방식으로만 해결한다.

---

## 2. 현재 빌드 기준 남아 있는 실제 모호점

`docs/dicespell_first_run_onboarding_handoff_kr.md`는 이미 방향을 닫았지만, 실제 구현 직전 기준으로는 아래가 아직 화면 레벨에서 모호하다.

1. **책 선택**
   - 현재 `renderGrimoireShelf()`의 `body-copy`는 방향은 맞지만, 초보자에게 `무엇부터 읽으라`는 명령 순서가 없다.
   - `playstyle / openingPlan / caution / signatureRule / completionBonus`가 같은 밀도로 노출돼 있어, primer가 별도 카드인지 헤더 콜아웃인지 owner마다 해석이 갈릴 수 있다.

2. **첫 전투**
   - 현재 `battle-subcopy`와 `battle-action-panel` 문구는 이미 좋지만, 첫 런 전용 우선순위(`가능한 족보 -> 주문 1회 -> 자동 종료`)가 surface별로 분리돼 있지 않다.
   - 첫 주문 후/첫 자동 종료 후 후속 힌트를 어디에 얹을지 아직 고정되지 않았다.

3. **reward / shop**
   - 둘 다 `body-copy`가 있지만, 첫 진입일 때만 더 강하게 읽혀야 하는 purpose line과 평시 문구가 아직 분리되지 않았다.
   - 즉 `항상 있는 설명`과 `첫 런 primer`가 같은 요소인지 별도 badge인지 명확하지 않다.

4. **ending / overrun**
   - 현재 `renderEnding()` 본문은 승리/패배/오버런 의미를 한 단락에 같이 품고 있어, 초보자에게는 `무엇이 남는가`와 `이 버튼이 무엇을 하는가`가 살짝 섞여 읽힌다.
   - 향후 `overrunEvent` 도입 후에도 문맥이 겹치지 않는 primer 위치를 지금 미리 고정할 필요가 있다.

즉 이번 패킷의 역할은 `온보딩을 더 추가 설명하는 것`이 아니라, **각 surface에서 primer가 정확히 어디에 얹히고 무엇을 말해야 하는지**를 닫는 것이다.

---

## 3. 공통 UI 원칙

### 3.1 primer 컴포넌트 계층

이번 온보딩은 아래 세 밀도만 사용한다.

1. `hero-primer`
   - surface 진입 직후 가장 먼저 읽어야 하는 1문장 + 2~3줄 보조 문구
   - 화면 전체를 막는 modal 금지
   - 기존 `.hero-card`, `.stage-card`, `.section-head` 바로 아래에 붙는 compact card 형태

2. `inline-primer`
   - 로그 상단, 헤더 서브카피, CTA 근처에 붙는 1문장 힌트
   - 상태 변화 직후 1회만 뜨는 후속 설명용

3. `badge-primer`
   - reward/shop/ending에서 목적 차이를 빠르게 읽히게 하는 소형 배지
   - 항상 보이는 헤더 문구를 first-run 상태에서만 강조할 때 사용

### 3.2 공통 시각 규칙

- primer는 기존 카드와 구분되되, 새 phase처럼 보이면 안 된다.
- 배경은 기존 `stage-card`보다 한 단계 얕은 tone.
- border/accent만 다르게 주고 full-screen dimmer는 쓰지 않는다.
- 본문 길이는 기본 1문장, 최대 2문장.
- 한 surface에서 동시에 primer 2개 이상이 강하게 경쟁하면 안 된다.

### 3.3 인터랙션 규칙

- 기본 액션은 `알겠음` 1개면 충분하다.
- `이번 런에서는 숨기기` 또는 `다시 보지 않기`는 설정/도움말 reopen과 분리된 저장값으로 보지 말고, 우선 같은 `seen` 플래그로 처리해도 된다.
- primer를 닫지 않아도 핵심 버튼 클릭이 막히면 안 된다.

---

## 4. surface별 화면 spec

## 4.1 도서관 책 선택 primer

### 대상 surface

- 함수: `renderGrimoireShelf()`
- 위치 기준:
  - 1순위: `.section-head` 아래, 기존 `.body-copy` 위
  - 2순위: `.body-copy`를 primer tone으로 대체

### 목표

플레이어가 책 카드의 모든 필드를 다 읽으려 하지 말고, **`초반 운영`과 `주의점`부터 읽는 순서**를 배우게 만든다.

### 권장 배치

```text
[section-head]
[hero-primer: 첫 책은 운영 축/주의점만 먼저 읽어도 충분]
[existing body-copy or thinner helper line]
[legacy loadout]
[grimoire grid]
```

### hero-primer 카피 계약

- eyebrow: `첫 런 가이드`
- title: `첫 책은 이렇게 읽으면 됩니다`
- body line 1: `먼저 ‘초반 운영’을 보고, 바로 아래 ‘주의점’까지만 읽어도 시작하기 충분합니다.`
- body line 2: `고유 규칙과 완주 보너스는 책 감이 온 뒤 읽어도 늦지 않습니다.`
- confirm CTA: `알겠음`

### 카드 내부 highlight 규칙

- `openingPlan` 행에 얇은 accent outline
- `caution` 행에 보조 accent outline
- `completionBonus`는 강조 금지
- `추천 시작` 배지를 붙이더라도 1~2권까지만, `강한 책` 서열처럼 보이면 안 됨

### 프로그래머 handoff

- 신규 상태: `uiGuidance.seenBookSelectPrimer`
- render hook:
  - `showBookSelectPrimer = !seenBookSelectPrimer && isFirstRunLikeContext`
- 닫기 전에도 책 선택 버튼은 활성 유지
- primer highlight용 class는 카드 데이터 확장 없이, 기존 `.grimoire-identity-item` 라벨 텍스트 또는 인덱스 기준으로 처리

### UI handoff

- primer 높이는 96~140px 범위의 compact card 우선
- 그리드 첫 줄을 아래로 과도하게 밀지 않음
- 1280px 기준 `section-head + primer + body-copy`가 한 스크롤 안에 보여야 함

### 아트 handoff

- `library` 계열 gold/brown 톤 재사용
- 신규 리본은 `첫 런 가이드` 1종이면 충분
- 책별 개별 배너 금지

---

## 4.2 첫 전투 primer

### 대상 surface

- 함수: `renderBattle(summary)`
- 위치 기준:
  - 진입 primer: `.battle-subcopy` 위 또는 아래
  - 첫 주문 힌트: `.battle-hand-section` 상단 또는 최근 로그 위
  - 자동 종료 힌트: `.battle-action-panel` 하단 또는 `.battle-subcopy` 대체 line

### 목표

첫 전투에서 아래 3가지만 확실히 읽히게 한다.

1. 가능한 족보를 먼저 본다
2. 주문은 턴당 1회다
3. 주문 뒤 결과는 로그/잠금에서 읽는다

### A. 전투 진입 hero-primer

#### 배치

```text
[battle-header]
[battle-subcopy]
[hero-primer]
[battle-board]
```

또는

```text
[battle-header]
[hero-primer]
[battle-subcopy]
[battle-board]
```

첫 구현은 layout risk가 더 낮은 **`battle-subcopy` 바로 아래 full-width compact card**를 권장한다.

#### 카피 계약

- eyebrow: `첫 전투 가이드`
- title: `이번 턴은 한 번의 주문으로 끝납니다`
- bullet 1: `먼저 가능한 족보를 보고`
- bullet 2: `이번 턴에 쓸 주문 하나를 고른 뒤`
- bullet 3: `결과는 로그와 잠금 표시에서 확인하세요`
- CTA: `전투 시작`

### B. 첫 주문 직후 inline-primer

#### 배치

- `.battle-hand-section` 헤더 아래 1문장 inline ribbon
- 로그가 있다면 가장 최신 로그 위 고정도 허용

#### 카피 계약

- `방금 쓴 족보는 이번 페이지 동안 잠길 수 있습니다.`
- `피해, 상태이상, 잠금 결과는 로그에서 바로 읽을 수 있습니다.`

둘을 한 줄/두 줄로 합쳐도 되지만 2문장 초과 금지.

### C. 첫 자동 종료 inline-primer

#### 트리거

- 현재 턴에서 `쓸 수 있는 주문이 없어 자동으로 몬스터 턴 전환`
- `MP 부족` 또는 `가용 족보 없음` 원인이 보일 때만

#### 카피 계약

- `지금은 쓸 수 있는 주문이 없어 몬스터 턴으로 넘어갔습니다.`
- 추가 문장은 붙이지 않거나, 붙여도 `다음 턴에는 가능한 족보부터 다시 확인하세요.` 정도로 제한

### 프로그래머 handoff

- 신규 상태
  - `uiGuidance.seenBattlePrimer`
  - `uiGuidance.seenFirstSpellHint`
  - `uiGuidance.seenAutoPassHint`
- primer는 `battle` phase state를 바꾸지 않는 pure UI layer여야 함
- 첫 주문 감지는 기존 cast action success 후크 사용
- 자동 종료 감지는 이미 있는 battle result/log phase 변화에 얹되, 실패해도 진행 막힘이 없어야 함

### UI handoff

- primer가 dice, targetable monster, hand button을 가리면 실패
- `.battle-subcopy`와 hero-primer가 동시에 길어지지 않게 subcopy는 1문장 유지
- inline-primer는 max 2줄, opacity 높은 ribbon 형태 권장

### 아트 handoff

- 공용 glyph 3종이면 충분
  - `주사위`
  - `주문 1회`
  - `잠금`
- 몬스터/배경 신규 일러스트는 범위 밖

---

## 4.3 첫 reward primer

### 대상 surface

- 함수: `renderReward(summary)`
- 위치 기준:
  - `.section-head` 아래
  - `일반 보상 1개 선택` 타이틀 위

### 목표

reward를 `지금 전투 직후 템포를 바로 밀어 주는 선택`으로 읽히게 만든다.

### 배치

```text
[section-head]
[badge-primer + purpose line]
[reward-block]
```

### 카피 계약

- badge: `첫 보상`
- line: `보상은 다음 1~2전투를 버티고 밀어 주는 즉시 선택입니다.`

### 시각 규칙

- full card보다 얇은 accent strip 권장
- 카드 그리드보다 먼저 눈에 들어오되, 선택 버튼보다 강하면 안 됨

### 프로그래머 handoff

- 신규 상태: `uiGuidance.seenRewardPrimer`
- `showRewardPrimer = !seenRewardPrimer && firstRewardEntry`
- 평시에는 같은 위치에 일반 purpose line을 얇게 남겨도 됨

### UI handoff

- `첫 보상` 배지 + 1문장만 허용
- boss artifact draft가 같이 열리는 경우에도 primer는 reward block 상단 1회만 노출

### 아트 handoff

- `즉시` 성격을 읽히는 warm accent 1종
- 기존 reward pill 체계와 충돌 금지

---

## 4.4 첫 shop primer

### 대상 surface

- 함수: `renderShop()`
- 위치 기준:
  - `.section-head` 아래, 기존 `.body-copy`와 같은 레이어

### 목표

shop을 `이번 런의 장기 방향을 굳히는 투자 공간`으로 reward와 분리해 읽히게 만든다.

### 카피 계약

- badge: `첫 상점`
- line: `상점은 이번 런의 장기 방향을 굳히는 투자 공간입니다.`

### 시각 규칙

- reward와 다른 accent 색 사용
- `구매 즉시 효과` 설명은 body-copy 또는 하위 문구에 남겨도 되지만, primer 본문은 장기 투자 문맥이 먼저여야 함

### 프로그래머 handoff

- 신규 상태: `uiGuidance.seenShopPrimer`
- render는 `renderShop()` 내부의 body-copy 위/대체 라인으로 해결
- 별도 modal/open state 금지

### UI handoff

- `다음 페이지로` 버튼보다 더 시선을 뺏지 않음
- shop grid 첫 줄을 밀어도 1카드 높이 이상 이동 금지

### 아트 handoff

- `장기`, `투자` 성격을 읽히는 cool accent 1종
- reward primer와 색만 달라도 충분

---

## 4.5 첫 ending / overrun primer

### 대상 surface

- 함수: `renderEnding(summary)`
- 위치 기준:
  - 본문 첫 `body-copy` 대체 또는 그 위 compact primer line
  - `오버런 계속` 버튼 바로 위 inline-primer

### 목표

엔딩 화면에서 아래 두 질문에 즉시 답한다.

1. 이번 런에서 무엇이 남는가
2. 지금 누르는 버튼이 무엇을 뜻하는가

### A. 패배 ending primer

#### 카피 계약

- badge: `첫 결과 정산`
- line: `런은 끝나도, 이번에 전진한 페이지만큼 서고 기록은 남습니다.`

#### 배치

- `.ending-card` 본문 최상단 compact primer line
- `런 정산` 카드보다 먼저 보이게 함

### B. 승리 ending primer

#### 카피 계약

- badge: `첫 클리어`
- line: `책을 봉인하면 완주 보너스를 챙기고, 원하면 다음 세그먼트로 이어갈 수 있습니다.`

### C. 오버런 CTA inline-primer

#### 카피 계약

- `오버런 계속`은 새 런 시작이 아니라, 지금 만든 흐름을 들고 다음 세그먼트로 넘어가는 선택입니다.

#### 배치

```text
[overrun draft]
[inline-primer near CTA]
[hero-actions]
```

### `overrunEvent` 대응 규칙

- `overrunEvent` 구현 전:
  - 위 문장을 그대로 사용 가능
- `overrunEvent` 구현 후:
  - same location, but density를 더 낮춰 `다음 세그먼트로 넘어가기 전 짧은 준비 장면이 이어집니다.` 수준으로 줄여도 됨
- 즉 엔딩 primer는 장면 연출을 설명하지 않고, **행동 의미만** 다뤄야 한다

### 프로그래머 handoff

- 신규 상태
  - `uiGuidance.seenDefeatEndingPrimer`
  - `uiGuidance.seenVictoryEndingPrimer`
  - `uiGuidance.seenOverrunPrimer`
- 분기 기준
  - 패배 첫 진입
  - 승리 첫 진입
  - `ending.victory && ending.canOverrun` 첫 진입
- `overrunEvent` 전후 모두에서 카피가 성립하도록 CTA 보조 문구를 별도 함수로 분리하는 편이 안전

### UI handoff

- primer가 `런 정산`, `완주 보너스`, `오버런 전리품 보관함` 카드와 같은 위계를 차지하면 안 됨
- CTA 주위 문구는 버튼 클릭을 막지 않는 하단/상단 1줄 서브카피로 제한

### 아트 handoff

- 상태 배지 3종이면 충분
  - `남는 진척`
  - `완주`
  - `오버런`
- ending 메인 일러스트 추가는 범위 밖

---

## 5. surface 간 정보 우선순위 매트릭스

| Surface | 지금 먼저 읽힐 것 | 뒤로 미룰 것 | 강한 primer 형태 |
| --- | --- | --- | --- |
| 도서관 | `초반 운영`, `주의점` | `완주 보너스` 상세 | hero-primer |
| 전투 진입 | `가능한 족보`, `주문 1회` | `붕괴`, `상단 합계` 전체 설명 | hero-primer |
| 첫 주문 후 | `잠금`, `로그` | 상태이상 세부 예외 | inline-primer |
| reward | `즉시 선택` | 세부 보상 taxonomy | badge-primer |
| shop | `장기 투자` | 경제 최적화 설명 | badge-primer |
| ending 패배 | `서고 기록이 남음` | 메타 시스템 전체 설명 | badge/inline |
| ending 승리/오버런 | `다음 세그먼트로 이어짐` | 오버런 설계 철학 | inline-primer |

---

## 6. 상태 저장 / DOM payload 제안

### 6.1 최소 상태 저장

```json
{
  "uiGuidance": {
    "seenBookSelectPrimer": true,
    "seenBattlePrimer": true,
    "seenFirstSpellHint": true,
    "seenAutoPassHint": true,
    "seenRewardPrimer": true,
    "seenShopPrimer": true,
    "seenDefeatEndingPrimer": true,
    "seenVictoryEndingPrimer": true,
    "seenOverrunPrimer": true
  }
}
```

### 6.2 렌더 helper 권장 shape

```js
getPrimerState(summary, session) => {
  library: { visible, variant, title, lines, targetSelectors },
  battle: { heroVisible, heroLines, firstSpellHint, autoPassHint },
  reward: { visible, badge, line },
  shop: { visible, badge, line },
  ending: { badge, line, ctaLine }
}
```

원칙:

- primer payload는 view model로 만든 뒤 render 함수에 흘려보내는 편이 안전
- primer가 없으면 `null` 또는 `visible:false`만 반환
- render 함수 안에서 상태 판정 로직이 길게 늘어나지 않게 한다

---

## 7. QA acceptance 초안

### 7.1 필수 확인

1. 새 프로필/초기 메타에서 도서관 primer 1회 노출
2. 첫 전투 진입 시 hero-primer 노출
3. 첫 주문 후 잠금/로그 inline-primer 노출
4. 첫 자동 종료 후 auto-pass hint 노출
5. 첫 reward에서 `즉시 선택` purpose line 노출
6. 첫 shop에서 `장기 투자` purpose line 노출
7. 첫 패배 ending에서 `서고 기록이 남음` 문맥 노출
8. 첫 승리/오버런 ending에서 `이어 간다` 문맥 노출
9. 같은 primer가 같은 프로필에서 반복 스팸되지 않음
10. primer 누락/플래그 초기화가 발생해도 진행은 정상 유지

### 7.2 실패 조건

- primer가 버튼/카드 클릭을 물리적으로 가림
- primer 문구가 `overrunEvent` 도입 후 엔딩 CTA와 의미 충돌
- reward/shop 둘 다 같은 목적 문구처럼 읽힘
- 책 선택 primer가 특정 책 tier ranking처럼 오해되게 보임

---

## 8. owner별 실제 납품물

### 프로그래머

- `getPrimerState()` 또는 동등한 helper
- 각 render surface 주입 포인트
- `seen` 플래그 저장/복구
- primer 닫기 액션 또는 자동 dismiss 규칙
- 최소 smoke/render 검증

### UI

- 5개 배치 시안
  - library hero-primer
  - battle hero-primer
  - battle inline-primer 2종
  - reward/shop badge-primer
  - ending inline/badge-primer
- desktop 1280 기준 spacing 규칙
- interaction overlap 금지선

### 아트

- primer 공통 backplate 1세트
- badge icon 세트 6종
  - 첫 런
  - 주문 1회
  - 즉시
  - 장기
  - 남는 진척
  - 오버런
- reward/shop accent strip 2종

### QA

- first-run profile checklist
- primer 재노출/비노출 regression checklist
- `overrunEvent` 미구현/구현 이후 두 버전 카피 적합성 점검표

---

## 9. 리스크 & 후속 과제

### 리스크 1. primer가 body-copy를 대체하면서 기존 화면 정보가 오히려 흐려질 위험

- 영향: 첫 런은 쉬워져도 기존 카드 설명 밀도가 깨질 수 있다.
- 대응: primer는 기존 `body-copy`를 완전히 지우기보다, first-run에서만 `위에 짧은 primer + 아래 얇은 기존 문구` 구조를 우선 검토한다.

### 리스크 2. 전투 primer가 로그/핸드/타깃 패널과 경쟁할 위험

- 영향: 가장 중요한 전투 인터랙션을 primer가 가리면 온보딩이 오히려 방해가 된다.
- 대응: 첫 구현은 `battle-subcopy` 바로 아래 full-width compact card + 후속 inline ribbon만 허용하고, floating tooltip형은 범위 밖으로 둔다.

### 리스크 3. reward/shop primer가 결국 같은 문장처럼 읽힐 위험

- 영향: `즉시`와 `장기` 구분이 시각적으로 분리되지 않으면 첫 런 학습 효과가 약하다.
- 대응: 문장뿐 아니라 accent 색, badge 라벨, placement를 분리한다.

### 리스크 4. ending primer가 `overrunEvent`와 역할이 겹칠 위험

- 영향: 엔딩에서 이미 장면을 다 설명해 버리면 경계 이벤트의 존재감이 줄어든다.
- 대응: ending primer는 `행동 의미`만, `overrunEvent`는 `장면 연출과 선택 감각`만 담당하게 유지한다.

### 다음 후속 과제

1. 다음 UX pass는 이 문서 기준으로 **Step 1: 도서관 + 첫 전투 primer**만 먼저 붙여도 된다.
2. 이후 reward/shop, ending은 같은 payload/helper 구조를 재사용해 얹는다.
3. 실제 구현 전에는 `docs/dicespell_first_run_onboarding_handoff_kr.md`와 이 screen packet을 한 owner가 같이 검토해, 방향 문서와 화면 문서가 어긋나지 않는지 최종 확인한다.

---

## 10. 최종 결론

온보딩의 다음 병목은 더 이상 `무슨 메시지를 보여 줄 것인가`가 아니다.

이제 남은 병목은 **그 메시지를 현재 화면 위 어디에, 어느 밀도로, 어떤 owner 경계로 얹을 것인가**다.

이 screen packet이 닫아 주는 것은 바로 그 마지막 해석 차이다. 이제 프로그래머는 render hook을, UI는 배치를, 아트는 badge/backplate를, QA는 노출 조건을 같은 기준으로 바로 잡을 수 있다.
