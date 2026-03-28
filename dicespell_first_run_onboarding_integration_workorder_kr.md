# DiceSpell 첫 런 온보딩 통합 작업지시서

## 빠른 안내

### 3줄 요약
- 이 문서는 온보딩 문서 묶음을 실제 구현 순서로 재배열한 **컷오버 작업지시서**다.
- 프로그래머는 `상태 저장 위치 → helper → surface 주입 순서`를 먼저 보고, QA는 recovery와 owner DoD를 함께 보면 된다.
- 읽을 때 기준은 `한 번에 전부 붙이기`가 아니라, **library → battle → reward/shop → ending** 순으로 좁혀서 닫히는가다.

**한 줄 목표:** 온보딩을 추상 문구가 아니라 실제 파일·함수·상태 기준의 작업 순서로 바꾼다.

### 왜 이 문서가 필요한가
- handoff와 screen packet만으로도 방향은 충분하지만, 구현 슬롯에서는 `무엇부터 잘라 붙이는가`가 다시 병목이 된다.
- 이 문서는 그 병목을 줄이기 위해 owner별 handoff와 DoD를 한 장에서 이어 읽게 만든다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | Step 0~4, 상태 소유권, helper shape | `acceptance matrix` |
| UI | 각 step에서 필요한 화면 변경만 추려보기 | `screen packet` |
| QA | acceptance / recovery 증거 묶음 | `acceptance matrix` |
| PM | 컷오버 순서와 범위 관리 | `summary`, `production cutsheet` |

---

## 문서 목적

이 문서는 이미 작성된 아래 2개 문서를, **실제 구현 슬롯에서 바로 사용할 수 있는 온보딩 컷오버 작업지시서**로 다시 묶는 최종 보조 문서다.

- `docs/dicespell_first_run_onboarding_handoff_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`

지금까지의 문서들은 방향, surface, 카피 톤, 화면 밀도를 충분히 고정했다.  
하지만 실제 구현 직전 기준으로는 아직 한 가지가 화면 여러 곳에 흩어져 있었다.

> 현재 `app.js` 구조 위에서 `도서관 → 전투 → reward/shop → ending` primer를 어떤 순서로 끼우고, 어떤 상태가 언제 source of truth가 되며, owner별 납품물은 무엇이 완료선인가?

이번 문서는 그 마지막 공백만 메운다.  
즉 새 기획서가 아니라, **현재 코드베이스 기준 통합 순서 · 상태 소유권 · surface별 주입 순서 · owner별 실제 납품 묶음**을 한 장으로 고정하는 작업지시서다.

---

## 1. 왜 이 문서가 지금 필요한가

현재 `overrunEvent` 축은 handoff/spec/screen/cutsheet/acceptance/workorder/source-of-truth/smoke migration까지 이미 구현 착수 가능한 수준까지 닫혀 있다. 즉 그쪽의 남은 blocker는 문서가 아니라 실제 코드 컷오버다.

반면 첫 런 온보딩은 `무슨 메시지를 보여 줄까`와 `어느 화면에 붙일까`는 정리됐지만, 실제 구현 직전 기준으로는 아래 네 가지가 아직 owner마다 다르게 해석될 수 있다.

1. `renderGrimoireShelf()` / `renderBattle()` / `renderReward()` / `renderShop()` / `renderEnding()`에 **어떤 순서로** 들어가야 레이아웃 리스크가 가장 낮은가
2. primer 표시 여부가 `session` 소유인지 `meta` 소유인지, 혹은 임시 UI state인지가 아직 코드 컷오버 순서로 고정되지 않았다
3. 첫 전투 후속 힌트(`첫 주문`, `자동 종료`)처럼 **행동 직후 1회 노출되는 surface**는 render만으로 닫히지 않아, 어떤 event hook에 얹을지가 아직 느슨하다
4. `overrunEvent` 구현 전후로 ending primer 문맥이 유지되려면, ending primer가 어디까지 말하고 어디서 멈추는지 컷오버 순서까지 같이 고정돼야 한다

즉 지금 남은 병목은 아이디어가 아니라 **실제 구현 순서와 상태 책임 분리**다.

---

## 2. 현재 코드 기준 실제 병목

현재 실제 코드 기준 핵심 병목은 아래 5개다.

### 2.1 primer용 source of truth가 아직 없다

현재 `dice-spell-standalone/src/app.js`는 `appState.meta`, `appState.session`, 로컬 UI state를 이미 나눠 쓰고 있지만, 첫 런 온보딩용 `uiGuidance`는 아직 존재하지 않는다.

즉 지금은 다음 질문에 대한 단일 답이 없다.

- 이 primer를 이미 봤는가
- 이번 프로필에서 다시 보여 줄 필요가 있는가
- 저장이 누락돼도 진행을 막지 않는가

### 2.2 render surface는 분명하지만 primer 주입 point가 아직 코드 계약이 아니다

현재 각 render surface의 핵심 slot은 이미 있다.

- `renderGrimoireShelf()` → `.section-head`, `.body-copy`, `.grimoire-grid`
- `renderBattle(summary)` → `.battle-subcopy`, `.battle-action-panel`, `.battle-hand-section`
- `renderReward(summary)` → `.section-head`, `.reward-block`
- `renderShop()` → `.section-head`, `.body-copy`, `.shop-grid`
- `renderEnding(summary)` → `.ending-card .body-copy`, `.hero-actions`

하지만 아직 이 slot들이 `primer용 insert point`라는 구현 계약은 아니다.  
즉 구현자가 바로 붙잡으면 `body-copy를 대체할지`, `추가 line을 얹을지`, `compact card를 새로 넣을지`를 다시 판단하게 된다.

### 2.3 전투 primer는 정적 render만으로는 닫히지 않는다

첫 전투는 아래 세 종류가 섞여 있다.

1. 진입 시 1회 노출되는 hero-primer
2. 첫 주문 사용 직후 1회 노출되는 inline-primer
3. 첫 자동 종료 직후 1회 노출되는 inline-primer

즉 `renderBattle()`만 수정해서는 부족하고, 기존 액션 처리 흐름에서 **캐스트 성공 후**와 **자동 종료 발생 후**에 primer 상태를 밀어 넣는 event hook이 필요하다.

### 2.4 reward와 shop은 목적 문구는 있지만 first-run 강조 레이어가 없다

현재 `renderReward()`는 헤더만 있고 purpose line이 없고, `renderShop()`은 body-copy가 있지만 first-run 강조와 평시 문구가 구분되지 않는다.  
즉 지금 구조만 두면 `항상 있는 설명`과 `첫 런 primer`가 같은 레벨로 섞여 읽힌다.

### 2.5 ending primer는 `overrunEvent`와 역할 경계가 아직 코드 순서로 고정되지 않았다

현재 `renderEnding(summary)`의 본문은 승리/패배/오버런 문맥을 한 단락에 같이 담고 있다.  
이 상태에서 온보딩 primer를 덧붙일 때 역할 경계를 잘못 잡으면 아래가 다시 섞일 수 있다.

- ending primer가 말해야 할 것: `무엇이 남는가`, `버튼이 무엇을 뜻하는가`
- `overrunEvent`가 말해야 할 것: `장면 전환`, `다음 세그먼트 직전 감정선`, `선택 감각`

즉 ending primer 구현은 `overrunEvent` 이전에도 자연스럽고, 이후에도 밀도를 낮춰 재사용 가능해야 한다.

---

## 3. 이번 bounded 구현의 최종 목표

이번 구현이 끝났다고 말하려면 아래 문장이 코드/화면/QA 모두에서 참이어야 한다.

> 첫 런 플레이어는 `도서관`, `첫 전투`, `첫 reward/shop`, `첫 ending`에서 각각 한 번씩만 primer를 보고, 그 primer는 핵심 인터랙션을 가리지 않으며, 저장이 누락돼도 진행은 절대 막히지 않는다.

이 문장을 만족하지 못하면 이번 bounded 온보딩 pass는 아직 닫히지 않은 상태다.

---

## 4. source of truth 소유권 매트릭스

이번 구현에서 가장 중요한 것은 `primer를 보여 줄지 말지`를 화면마다 제각각 판정하지 않는 것이다.

| 층위 | source of truth | 역할 | 이 층위에서 하면 안 되는 것 |
| --- | --- | --- | --- |
| 메타 저장 | `meta.uiGuidance` 또는 동등 구조 | `이 primer를 이미 봤는가`의 장기 기록 | primer DOM payload 직접 소유 |
| 세션/런타임 | `session` + 일시적 UI state | 현재 phase, 첫 주문/자동 종료 같은 이번 런 사건 | 장기 seen 플래그 직접 영속화 없이 남발 |
| render helper | `getPrimerState(summary, appState)` 또는 동등 함수 | 현재 surface에 어떤 primer를 어떤 밀도로 보여 줄지 계산 | localStorage 직접 읽기/쓰기 |
| render surface | `renderGrimoireShelf()` 등 | helper가 준 payload를 slot에 출력 | 자체 규칙으로 primer 조건 재판정 |
| action handler | cast / auto-pass / continue-reward / leave-shop / restart-library 등 | 사건 발생 시 seen 플래그 갱신 및 후속 힌트 트리거 | 마크업 문자열 직접 조립 |

핵심은 아래 두 줄이다.

- `meta.uiGuidance`는 **seen 기록용 source of truth**다.
- primer payload는 **현재 화면 표현용 source of truth**다.

즉 `봤는가`와 `지금 어떻게 보일 것인가`를 같은 함수/같은 객체에 섞지 않는다.

---

## 5. 권장 primer 상태 구조

### 5.1 최소 저장 구조

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

원칙:

- 기본 위치는 `meta` 쪽이 가장 자연스럽다
- 저장 누락 시에는 `힌트가 한 번 더 보임` 정도만 허용하고, 세이브 손상으로 취급하지 않는다
- 첫 구현에서는 별도 `dismissedThisRun`까지 만들지 않아도 된다. `seen` 플래그만으로도 bounded pass는 닫힌다

### 5.2 권장 helper shape

```js
getPrimerState(summary, appState) => {
  library: { visible, eyebrow, title, lines, highlightKeys },
  battle: {
    heroVisible,
    heroLines,
    firstSpellHintVisible,
    firstSpellHintLines,
    autoPassHintVisible,
    autoPassHintLines
  },
  reward: { visible, badge, line },
  shop: { visible, badge, line },
  ending: { badge, line, ctaLine }
}
```

원칙:

- render 함수는 가급적 `payload 소비자`로만 남긴다
- helper가 길어지더라도 조건식은 그쪽에 모으는 편이 안전하다
- `null` / `visible:false` 반환을 기본 패턴으로 통일한다

---

## 6. 컷오버 구현 순서

이번 구현은 아래 순서로 자르는 것이 가장 안전하다.

## Step 0. 상태 저장 위치부터 먼저 고정한다

### 해야 할 일

- `meta` 정규화 경로에 `uiGuidance` 기본값을 추가한다
- `readMetaProgress()` / `persistMetaProgress()`가 새 구조를 자연스럽게 통과시키는지 확인한다
- `getPrimerState()` 또는 동등 helper의 도입 위치를 먼저 고정한다

### 이 단계의 완료선

- primer가 어디에서 `봤음/안 봄`을 판정하는지가 더 이상 surface별로 갈리지 않는다
- 저장 누락이 나도 meta reset이 아니라 `재노출` 수준으로만 해석된다

### 프로그래머 handoff

- `dice-spell-standalone/src/meta-progression.js`에 기본값/정규화 추가
- `dice-spell-standalone/src/app.js`에 helper 진입점 추가
- owner 문서에는 `meta.uiGuidance`가 장기 기록 source of truth라는 점을 명시

---

## Step 1. 도서관 + 전투 진입 primer만 먼저 닫는다

가장 큰 초반 해석 비용은 `책 선택`과 `첫 전투 진입`이다. 따라서 reward/shop/ending보다 이 둘을 먼저 붙인다.

### Step 1-A. `renderGrimoireShelf()`

#### 해야 할 일

- `.section-head` 아래, 기존 `.body-copy` 위에 compact `hero-primer` slot 추가
- primer가 보일 때에도 기존 `.body-copy`는 완전히 지우지 말고 얇은 보조 문구로 유지
- `openingPlan`, `caution` highlight class를 넣을 수 있게 카드 내부 target class를 추가

#### 완료선

- 첫 프로필에서는 `초반 운영 -> 주의점` 읽기 순서가 hero-primer 1장으로 먼저 읽힌다
- 책 선택 버튼은 primer와 무관하게 항상 클릭 가능하다
- 특정 책 tier ranking처럼 보이는 새 뱃지를 남발하지 않는다

#### UI/아트 handoff

- `library` 톤의 compact hero-primer 1종
- outline/highlight는 `openingPlan`, `caution` 두 field까지만

### Step 1-B. `renderBattle(summary)` 진입 hero-primer

#### 해야 할 일

- `.battle-subcopy` 바로 아래 full-width compact card slot 추가
- 현재 `battle-subcopy` 길이는 유지하되, hero-primer와 경쟁하지 않게 1문장 이상 늘리지 않는다

#### 완료선

- 첫 전투 진입 시 `가능한 족보 → 주문 1회 → 로그 확인` 3축이 한 카드에 읽힌다
- 주사위, 타깃 카드, hand grid를 물리적으로 가리지 않는다

#### UI/아트 handoff

- battle hero-primer는 floating tooltip 금지
- full-width compact card + 공용 glyph 3종(`주사위`, `주문 1회`, `잠금`)으로 충분

---

## Step 2. 전투 후속 힌트(`첫 주문`, `자동 종료`)를 event hook으로 닫는다

이 단계는 render만으로는 닫히지 않는다.

### Step 2-A. 첫 주문 힌트

#### 해야 할 일

- cast 성공 시점 후크에 `seenFirstSpellHint` 갱신 로직 추가
- `.battle-hand-section` 헤더 아래 또는 최신 로그 위에 inline-primer 1줄 삽입

#### 완료선

- 첫 주문 직후 `잠금`, `로그에서 결과 읽기`가 1회만 표시된다
- 주문 처리 순서나 대상 선택 흐름은 전혀 바뀌지 않는다

### Step 2-B. 자동 종료 힌트

#### 해야 할 일

- `쓸 수 있는 주문이 없어 몬스터 턴으로 넘어감` 사건 직후 `seenAutoPassHint` 갱신
- `.battle-action-panel` 하단 또는 `.battle-subcopy` 대체 line에 inline-primer 삽입

#### 완료선

- 첫 자동 종료 후 원인을 읽게 하지만, 별도 modal 없이 흐름이 이어진다
- MP 부족/가용 족보 없음 문맥이 힌트에 직접 들어간다

### 프로그래머 handoff

- event hook은 기존 액션 처리 성공 경로를 재사용하고, primer 때문에 state machine을 분기시키지 않는다
- 후속 힌트는 순수 UI 보조 상태여야 하며 `battle` phase를 추가로 만들지 않는다

### QA handoff

- 첫 전투 hero-primer, 첫 주문 힌트, 자동 종료 힌트가 각각 `1회만` 뜨는지 분리 체크
- primer가 없는 상태에서도 battle flow 회귀가 없는지 확인

---

## Step 3. reward / shop 목적 문구를 같은 payload 체계로 붙인다

### Step 3-A. `renderReward(summary)`

#### 해야 할 일

- `.section-head` 아래, `reward-block` 위에 `badge-primer + purpose line` slot 추가
- 평시에도 얇은 purpose line을 남길지, first-run만 보여 줄지는 동일 helper에서 관리

#### 완료선

- 첫 reward에서 `즉시 선택` 문맥이 카드 그리드보다 먼저 읽힌다
- boss artifact draft가 같이 떠도 primer는 reward block 상단 1회만 유지된다

### Step 3-B. `renderShop()`

#### 해야 할 일

- `.section-head` 아래 또는 기존 `.body-copy` 대체 line에 `badge-primer + purpose line` 추가
- reward와 accent, badge 라벨을 분리해 둘을 같은 문장처럼 읽히지 않게 한다

#### 완료선

- 첫 shop에서 `장기 투자` 문맥이 분명히 읽힌다
- `다음 페이지로` 버튼보다 시선을 더 강하게 빼앗지 않는다

### UI/아트 handoff

- reward: warm accent
- shop: cool accent
- 같은 컴포넌트이되 색/배지 라벨/placement로 목적 차이를 읽히게 만든다

---

## Step 4. ending primer를 `overrunEvent`와 충돌 없는 밀도로 닫는다

### 해야 할 일

- `renderEnding(summary)` 본문 최상단에 compact badge/inline primer line slot 추가
- `오버런 계속` CTA 근처에는 별도 `ctaLine` slot 추가
- 승리/패배/오버런 가능 여부별 카피 분기를 helper로 분리

### 분기 원칙

#### 패배

- `런은 끝나도, 이번에 전진한 페이지만큼 서고 기록은 남습니다.`

#### 승리

- `책을 봉인하면 완주 보너스를 챙기고, 원하면 다음 세그먼트로 이어갈 수 있습니다.`

#### 오버런 CTA

- `오버런 계속`은 새 런 시작이 아니라, 지금 만든 흐름을 들고 다음 세그먼트로 넘어가는 선택입니다.

### 이 단계에서 하면 안 되는 것

- ending primer가 `overrunEvent`의 장면 설명을 대신 말하는 것
- 엔딩 본문과 CTA 보조 문구가 같은 의미를 두 번 길게 반복하는 것

### 완료선

- 패배/승리/오버런 문맥이 각각 분리돼 읽힌다
- `overrunEvent`가 아직 없을 때도 자연스럽고, 나중에 붙어도 `행동 의미`만 남겨 재사용 가능하다

---

## 7. owner별 실제 납품물

## 7.1 프로그래머

### 반드시 넘겨야 하는 것

1. `uiGuidance` 기본값/정규화
2. `getPrimerState()` 또는 동등 helper
3. 5개 render surface 주입 포인트
4. 첫 주문/자동 종료 event hook
5. primer dismiss 또는 1회 seen 처리
6. primer 비노출 시 기존 플로우 회귀 없음

### 대상 파일

- `dice-spell-standalone/src/meta-progression.js`
- `dice-spell-standalone/src/app.js`
- 필요 시 primer 관련 공통 class를 위한 `dice-spell-standalone/src/styles.css`

### DoD

- primer가 없는 상태에서 기존 phase/UI가 그대로 작동한다
- primer가 떠도 버튼/핵심 카드 클릭을 막지 않는다
- meta 저장 누락 시 진행이 멈추지 않는다

## 7.2 UI 오너

### 반드시 넘겨야 하는 것

1. library hero-primer 배치안
2. battle hero-primer 배치안
3. battle inline-primer 2종 배치안
4. reward/shop badge-primer 배치안
5. ending badge/cta-line 배치안

### DoD

- 1280 기준 핵심 인터랙션이 primer에 가려지지 않는다
- reward와 shop의 목적 문구가 다른 공간처럼 읽힌다
- ending primer가 `런 정산`, `완주 보너스`, `오버런 전리품 보관함`보다 더 큰 카드로 보이지 않는다

## 7.3 아트 오너

### 반드시 넘겨야 하는 것

1. primer 공통 backplate 1세트
2. badge icon 6종
   - 첫 런
   - 주문 1회
   - 즉시
   - 장기
   - 남는 진척
   - 오버런
3. reward/shop accent strip 2종

### DoD

- 새 자산이 없어도 텍스트 기반으로 먼저 구현 가능하다
- 아트가 들어와도 새 phase처럼 과장되지 않고 기존 카드 체계 안에 묻어난다

## 7.4 QA

### 반드시 넘겨야 하는 것

1. first-run profile checklist
2. primer 재노출/비노출 regression checklist
3. `overrunEvent` 미구현 / 구현 이후 두 버전 모두에서 ending 카피 적합성 점검표
4. 저장 누락/초기화 상황 재노출 점검표

### DoD

- primer는 의도한 surface에서만 뜬다
- 같은 primer가 같은 프로필에서 반복 스팸되지 않는다
- `clear save`나 meta 초기화 상황에서도 앱이 죽지 않는다

---

## 8. acceptance / recovery 체크리스트

### A. 필수 acceptance

1. 새 프로필에서 도서관 hero-primer 1회 노출
2. 첫 전투 진입 hero-primer 1회 노출
3. 첫 주문 후 inline-primer 1회 노출
4. 첫 자동 종료 후 inline-primer 1회 노출
5. 첫 reward에서 `즉시 선택` purpose line 노출
6. 첫 shop에서 `장기 투자` purpose line 노출
7. 첫 패배 ending에서 `서고 기록이 남음` 문맥 노출
8. 첫 승리/오버런 ending에서 `이어 간다` 문맥 노출
9. primer가 닫히거나 seen 처리된 후 같은 프로필에서 반복 스팸되지 않음
10. primer가 비활성화돼도 기존 플레이 흐름이 100% 유지됨

### B. 필수 recovery

1. `uiGuidance` 객체가 없는 오래된 meta 저장을 로드해도 기본값으로 복구됨
2. 특정 `seen*` 키만 누락돼도 전체 meta가 깨지지 않고 해당 primer만 재노출됨
3. reward/shop primer payload가 비어도 화면 본체는 정상 렌더됨
4. ending primer 분기 계산이 실패해도 CTA와 정산 카드는 정상 작동함
5. `overrunEvent` 구현 전후 어디서든 ending primer가 과잉 설명 없이 유지됨

---

## 9. 리스크 & 후속 과제

### 리스크 1. primer가 body-copy를 대체하면서 기존 정보 밀도를 무너뜨릴 위험

- 영향: 첫 런은 쉬워져도 원래 있던 책/상점 설명이 얕아질 수 있다.
- 대응: 첫 구현은 `primer 추가 + 기존 body-copy 축약 유지`를 기본으로 하고, 완전 대체는 마지막 폴리시에서만 검토한다.

### 리스크 2. 전투 primer가 battle HUD와 경쟁할 위험

- 영향: 가장 중요한 dice/hand/target 인터랙션이 가려지면 온보딩이 오히려 방해가 된다.
- 대응: floating 도움말 대신 `battle-subcopy` 아래 hero card + hand/log 인접 inline ribbon만 허용한다.

### 리스크 3. reward/shop 목적 문구가 카피만 다르고 체감상 같게 읽힐 위험

- 영향: 첫 런 학습 효과가 약하고 둘 다 그냥 `카드 고르는 화면`으로 남는다.
- 대응: 같은 문장 구조를 피하고 badge/accent/배치까지 분리한다.

### 리스크 4. ending primer가 `overrunEvent`의 존재감을 미리 소모할 위험

- 영향: 엔딩 화면에서 장면 의미를 다 설명해 버리면 경계 이벤트의 감정선이 약해진다.
- 대응: ending primer는 `무엇이 남는가`와 `버튼 의미`까지만 담당하고, 장면 연출 설명은 끝까지 `overrunEvent` 쪽에 남긴다.

### 다음 후속 과제

1. 다음 UX 구현 슬롯은 이 문서 기준으로 **Step 1(도서관 + 전투 진입)**만 먼저 붙여도 된다.
2. reward/shop/ending은 같은 helper/payload 구조를 재사용해 뒤이어 붙인다.
3. 실제 구현 후에는 onboarding handoff + screen packet + 본 작업지시서를 같은 owner가 함께 검토해, 방향/화면/컷오버가 다시 갈라지지 않는지 최종 확인한다.

---

## 10. 최종 결론

첫 런 온보딩의 남은 병목은 더 이상 `무슨 메시지를 보여 줄까`가 아니다.

이제 남은 병목은 **그 메시지를 어떤 상태 구조로, 어떤 render slot에, 어떤 순서로 끼워 넣어야 실제 구현/디자인/QA가 같은 완료선을 보게 되는가**다.

이 작업지시서는 그 마지막 해석 차이를 닫는다.  
즉 다음 UX pass는 더 이상 추상 온보딩 논의가 아니라, `도서관 -> 전투 -> reward/shop -> ending` 순서로 bounded primer layer를 실제 코드에 꽂는 작업으로 바로 전환할 수 있다.
