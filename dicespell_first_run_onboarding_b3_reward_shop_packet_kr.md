# DiceSpell 첫 런 온보딩 B-3 Reward / Shop Primer 컷오버 패킷

## 빠른 안내

### 3줄 요약
- 이 문서는 첫 런 온보딩의 세 번째 실제 구현 묶음인 **B-3 reward / shop primer**를 commit-level handoff로 좁힌다.
- 프로그래머는 `renderReward(summary)` / `renderShop()`와 `seenRewardPrimer` / `seenShopPrimer` 저장 경계, boss draft 공존 규칙, CTA 비차단 조건을 먼저 보면 된다.
- UI/아트/QA는 `reward는 즉시 선택`, `shop은 장기 투자`, `둘 다 얇은 badge-primer 1장`만 기억하면 된다.

**한 줄 목표:** 첫 reward와 첫 shop에서 플레이어가 `지금은 다음 1~2전투를 버티기 위한 즉시 선택인지`, `이번 런의 방향을 굳히는 투자 공간인지`를 한 화면 안에서 헷갈리지 않게 읽도록 만들되, 카드 그리드와 CTA의 우선순위는 건드리지 않는다.

### 왜 이 문서가 필요한가
- 온보딩 문서 묶음은 이미 방향, screen packet, workorder, acceptance, cutsheet, content deck까지 충분히 닫혀 있다.
- B-1 library와 B-2 battle도 실제 첫 두 커밋 경계까지 내려왔지만, 그 다음 surface인 reward/shop은 여전히 `둘 다 짧은 primer면 같이 붙이면 되지 않나?`라는 식으로 가장 쉽게 범위가 부풀 수 있는 구간이다.
- 이 문서는 그 공백만 메운다. 즉 **B-3 reward/shop primer를 실제 파일/화면 밀도/검수/owner handoff 단위로 좁히는 실행 패킷**이다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | 2. 현재 runtime anchor, 5. B-3 범위, 6. 상태 구조 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| UI | 7. 화면 구조, 8. reward-vs-shop 차등 규칙 | `docs/dicespell_first_run_onboarding_screen_packet_kr.md` |
| 아트 | 8. accent/token 최소 패키지 | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| QA | 9. acceptance / recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |

---

## 1. 이 문서가 다루는 범위

### 포함
- `reward.first` badge-primer의 실제 컷오버 범위
- `shop.first` badge-primer의 실제 컷오버 범위
- `seenRewardPrimer`, `seenShopPrimer` 저장/재노출 규칙
- `renderReward(summary)` / `renderShop()` 기준 삽입 위치와 밀도 제한
- reward와 shop이 **같은 문장 / 같은 톤 / 같은 위치**로 보이지 않게 만드는 최소 차등 규칙
- boss draft 공존, CTA 비차단, primer 누락 fallback을 포함한 최소 검수선
- 프로그래머/UI/아트/QA handoff 경계

### 제외
- ending primer 구현
- reward taxonomy 전체 튜토리얼, shop 가격 공식 설명, 아티팩트 상세 사전
- reward / shop 카드 데이터 구조 재설계
- 구매/선택 로직, 골드 계산, 보상 적용 규칙 변경
- `overrunEvent` 본편 실구현

즉 이 문서는 `reward/shop UX 전체 개편`이 아니라 **B-3 reward 1면 + shop 1면 badge-primer**만 닫는다.

---

## 2. 현재 runtime anchor

현재 코드 기준 B-3가 닿는 실제 anchor는 아래 넷이다.

| 파일 | 현재 anchor | 이번 단계 역할 |
| --- | --- | --- |
| `dice-spell-standalone/src/app.js` | `renderReward(summary)` | reward primer slot 소비 + reward block / boss draft 공존 |
| `dice-spell-standalone/src/app.js` | `renderShop()` | shop primer slot 소비 + shop grid / CTA 공존 |
| `dice-spell-standalone/src/app.js` | reward 상단 `section-head`, `reward-block`, boss artifact draft 구조 | primer가 선택지를 가리지 않게 밀도 고정 |
| `dice-spell-standalone/src/app.js` | shop 상단 `section-head`, `.body-copy`, `shop-grid` 구조 | primer가 `다음 페이지로` CTA보다 더 강해지지 않게 위계 고정 |

현재 `renderReward(summary)`는 이미 아래 구조를 갖고 있다.

1. 상단 `section-head` + `다음 페이지로` CTA
2. `reward-block`
3. 일반 보상 grid
4. 필요 시 boss artifact draft

현재 `renderShop()`는 이미 아래 구조를 갖고 있다.

1. 상단 `section-head` + `다음 페이지로` CTA
2. `body-copy`
3. `shop-grid`

즉 B-3의 진짜 목적은 새 reward/shop 화면을 만드는 것이 아니라,
**이미 있는 두 surface 안에서 `reward는 즉시 선택`, `shop은 장기 투자`라는 역할 차이만 짧게 한 번 고정하는 것**이다.

---

## 3. 왜 B-3가 별도 packet이어야 하는가

현재 온보딩 문서는 `library -> battle -> reward/shop -> ending` 전체 순서를 이미 고정했다.
그런데 실제 구현 착수 관점에서 reward/shop은 battle보다 단순해 보이기 때문에 오히려 위험하다.

1. 둘 다 `얇은 strip 하나면 되겠지`라고 생각하는 순간 같은 문장/같은 accent/같은 위치로 수렴하기 쉽다.
2. reward는 boss draft와 함께 열릴 수 있어 primer가 `선택지 위의 노이즈`가 되기 쉽다.
3. shop은 기존 `.body-copy`가 이미 설명을 하고 있어서 새 primer가 body-copy와 중복되기 쉽다.
4. `reward도 shop도 결국 뭔가 고르는 화면`이라는 이유로 한 커밋 안에 ending까지 같이 붙이기 쉬운 단계다.

즉 B-3는 단순한 카피 배치가 아니라,
**reward-vs-shop 역할 분리 + 얇은 badge-primer 밀도 + CTA/그리드 비차단을 같이 시험하는 첫 보상/상점 commit**이다.

---

## 4. canonical source 묶음

| 질문 | source-of-truth |
| --- | --- |
| reward/shop primer의 존재 이유와 범위 | `docs/dicespell_first_run_onboarding_handoff_kr.md` |
| 실제 삽입 위치와 화면 밀도 | `docs/dicespell_first_run_onboarding_screen_packet_kr.md` |
| 상태 저장 위치와 cutover 순서 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| exact 카피/배지/accent/token | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| 완료선과 recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |
| owner별 DoD | `docs/dicespell_first_run_onboarding_production_cutsheet_kr.md` |
| canonical payload 예시 | `docs/dicespell_overrun_onboarding_contract_examples_kr.md` |
| ending과의 책임 경계 | `docs/dicespell_ending_overrun_boundary_packet_kr.md` |

이 B-3 packet은 위 문서를 대체하지 않는다.
대신 **B-3 reward/shop 첫 커밋에 필요한 부분만 추려 commit boundary로 다시 묶는다.**

---

## 5. B-3의 한 줄 정의

> 첫 reward에서는 `이 선택은 다음 1~2전투를 밀어 주는 즉시 선택`임을 얇은 warm badge-primer 1장으로 먼저 고정하고, 첫 shop에서는 `이 화면은 이번 런의 장기 방향을 굳히는 투자 공간`임을 cool badge-primer 1장으로만 분리해 읽히게 만든다.

이 정의에서 벗어나면 B-3가 아니라 다른 작업이다.

---

## 6. 상태 구조와 helper 계약

## 6.1 최소 저장 필드

B-3에서 필요한 장기 저장 필드는 아래 둘이면 충분하다.

```json
{
  "uiGuidance": {
    "seenRewardPrimer": true,
    "seenShopPrimer": true
  }
}
```

### 규칙
- 기본 위치는 `appState.meta` 하위다.
- 저장 누락 시 해석은 `해당 primer가 한 번 더 뜰 수 있음`까지다.
- 세이브가 망가졌다고 취급하거나 reward/shop 진행을 막아선 안 된다.

## 6.2 B-3 전용 helper 최소 shape

```js
getRewardShopPrimerState(summary, appState) => {
  reward: {
    visible: boolean,
    contentKey: "reward.first",
    badgeLabel: "첫 보상",
    lines: [
      "보상은 다음 1~2전투를 버티고 밀어 주는 즉시 선택입니다."
    ],
    accentKey: "reward-warm",
    artTokenSet: "primer-reward"
  },
  shop: {
    visible: boolean,
    contentKey: "shop.first",
    badgeLabel: "첫 상점",
    lines: [
      "상점은 이번 런의 장기 방향을 굳히는 투자 공간입니다."
    ],
    accentKey: "shop-cool",
    artTokenSet: "primer-shop"
  }
}
```

### 규칙
- 첫 구현에서는 공용 `getPrimerState()` 안의 reward/shop branch여도 좋고, reward-shop 전용 helper여도 좋다.
- render는 이 payload를 소비만 하고, 문자열을 재조합하지 않는다.
- B-3에서는 `variant`, `priority`, `dismissedThisRun`, `queue`, `isTutorialStep` 같은 필드를 늘리지 않는다.
- reward와 shop primer는 같은 템플릿 컴포넌트를 재사용해도 되지만, **accent / badge / 배치 체감은 서로 달라야 한다.**

---

## 7. 화면 구조 계약

## 7.1 reward primer 삽입 위치

권장 DOM 순서는 아래다.

```text
[section-head + continue CTA]
[reward-primer]
[reward-block]
[boss artifact draft]
```

### 허용 대안
- `reward-block` 내부 맨 위가 아니라, block 바깥 상단 strip으로 삽입
- boss draft가 함께 열려도 primer는 reward-block 상단 1회만 유지

### 금지
- primer 때문에 reward 카드 첫 줄이 첫 스크린 밖으로 밀리기
- boss draft 위/아래에 reward primer를 중복 노출하기
- reward primer를 full hero-card로 키워 `일반 보상 1개 선택`보다 더 큰 시각 덩어리로 만들기

## 7.2 shop primer 삽입 위치

권장 DOM 순서는 아래다.

```text
[section-head + continue CTA]
[shop-primer or body-copy replacement]
[shop-grid]
```

### 허용 대안
- 기존 `.body-copy`를 thinner primer line으로 대체
- `.body-copy` 아래 1줄 badge-primer strip으로 삽입하되 CTA보다 위계가 낮음

### 금지
- shop primer가 `다음 페이지로` CTA보다 더 눈에 띄는 hero-card가 되기
- reward와 똑같은 위치/색/문장 구조를 그대로 복붙하기
- shop grid 첫 줄을 과도하게 아래로 밀기

## 7.3 reward-vs-shop 차등 규칙

B-3에서 두 primer가 반드시 다르게 읽혀야 하는 축은 아래 셋이다.

1. **목적 문장**
   - reward: 다음 1~2전투를 버티고 미는 즉시 선택
   - shop: 이번 런의 장기 방향을 굳히는 투자
2. **accent 체감**
   - reward: warm gold / ember 계열
   - shop: cool teal / steel 계열
3. **배치 체감**
   - reward: reward block 시작을 여는 strip
   - shop: 본문 설명을 정리하는 thinner badge-primer 또는 body-copy replacement

### 일부러 하지 않을 것
- reward와 shop에 같은 배지 문구 쓰기
- reward와 shop primer를 둘 다 full-width hero card로 키우기
- 카드/가격/유물 상세 설명을 primer 안으로 끌고 오기

---

## 8. role-specific handoff

## 8.1 프로그래머

### deliverable
- `meta.uiGuidance.seenRewardPrimer` / `seenShopPrimer` 정규화
- `renderReward(summary)` primer slot 추가
- `renderShop()` primer slot 추가 또는 기존 `.body-copy`의 bounded replacement
- boss draft가 함께 있어도 reward primer는 1회만 보이는 guard
- primer와 무관하게 reward 선택 / boss artifact 선택 / 상점 구매 / 다음 페이지 CTA 정상 유지

### 일부러 하지 않을 것
- B-3에서 ending primer hook 열기
- reward/shop 선택 로직, 가격 계산, 보상 적용 규칙 변경
- primer 때문에 reward/shop body-copy 전체를 장문 재작성하기

### 완료선
- 첫 reward와 첫 shop에서 primer가 각각 1회만 뜬다.
- primer가 떠 있어도 카드 선택, 구매, `다음 페이지로` CTA가 그대로 동작한다.
- reward와 shop이 같은 payload 복붙처럼 읽히지 않는다.

## 8.2 UI

### deliverable
- reward용 warm compact strip 1종
- shop용 cool compact strip 1종 또는 thinner body-copy replacement 1종
- 1280px 기준 CTA와 카드 첫 줄이 첫 스크린 안에서 크게 밀리지 않는 배치
- boss draft 동시 노출 시에도 정보 우선순위가 무너지지 않는 spacing

### 일부러 하지 않을 것
- reward/shop 둘 다 같은 shell로 처리하기
- primer를 떠 있는 floating card로 만들어 클릭 영역을 덮기
- reward/shop primer를 정산 화면 수준의 큰 안내 카드로 키우기

### 완료선
- reward와 shop이 시각적으로 같은 공간처럼 보이지 않는다.
- 카드 선택지가 primer 아래에서 바로 이어져 읽힌다.
- CTA가 primer보다 행동 우선순위가 높게 유지된다.

## 8.3 아트

### deliverable
- `primer-reward`용 warm accent/token 1세트
- `primer-shop`용 cool accent/token 1세트
- 신규 아이콘은 기존 `reward` / `shop` 계열 재사용이면 충분

### 일부러 하지 않을 것
- reward/shop용 신규 대형 일러스트 제작
- rarity 카드 프레임과 primer 프레임을 섞어 오독 유발

### 완료선
- reward는 즉시 효익, shop은 장기 투자 톤이 시각적으로 분리된다.
- 카드 rarity 표현과 primer accent가 서로 경쟁하지 않는다.

## 8.4 QA

### 반드시 볼 것
- 신규 프로필 첫 reward 진입
- 신규 프로필 첫 shop 진입
- reward + boss draft 동시 노출
- `uiGuidance` 전체 누락 / 일부 키 누락 저장
- primer 비노출 상태에서 기존 선택/구매/이동 회귀 없음

### 완료선
- reward/shop primer가 각각 1회만 뜨고, 서로 다른 목적 문장으로 읽힌다.
- primer가 없어도 flow는 그대로 진행된다.
- boss draft나 shop grid가 primer 때문에 클릭/가독성 문제를 일으키지 않는다.

---

## 9. acceptance / recovery 압축판

## 9.1 B3-A1 ~ B3-A6 acceptance

| ID | 기준 | 통과 조건 |
| --- | --- | --- |
| B3-A1 | reward 첫 진입 | `첫 보상` badge + 즉시 선택 문장 1회 노출 |
| B3-A2 | shop 첫 진입 | `첫 상점` badge + 장기 투자 문장 1회 노출 |
| B3-A3 | reward CTA 비차단 | reward 선택 / `다음 페이지로` 동작 유지 |
| B3-A4 | boss draft 공존 | reward primer가 1회만 보이고 boss draft와 겹치지 않음 |
| B3-A5 | shop CTA 비차단 | 구매 / `다음 페이지로` 동작 유지 |
| B3-A6 | reward-vs-shop 차등 | 문장 / accent / 배치 체감 중 최소 2축 이상 분리 |

## 9.2 recovery

| 상황 | 기대 동작 | 실패 시 fallback |
| --- | --- | --- |
| `uiGuidance` 전체 누락 | primer만 다시 뜰 수 있고 flow는 정상 진행 | `uiGuidance = {}` 수준 정규화 |
| `seenRewardPrimer`만 누락 | reward primer만 재노출, shop 상태는 유지 | 전체 primer 초기화 금지 |
| `seenShopPrimer`만 누락 | shop primer만 재노출, reward 상태는 유지 | 전체 primer 초기화 금지 |
| primer payload 비어 있음 | reward/shop 본체 렌더 정상 유지 | primer DOM 생략 |
| boss draft 동시 노출 | reward primer 1회만 유지 | boss draft 위/아래 중복 strip 금지 |
| 레이아웃 과밀 | compact variant 또는 body-copy replacement로 강등 | floating/fixed 배치 폐기 |

---

## 10. 금지 패턴

아래가 보이면 B-3가 아니라 다른 작업이다.

1. reward/shop primer를 한 커밋에서 ending primer까지 확장
2. reward/shop primer를 `룰 설명 카드`처럼 장문화
3. reward와 shop이 badge label만 다르고 같은 구조/색/위치로 보임
4. boss draft 때문에 reward primer를 두 번 렌더
5. primer 때문에 카드 선택이나 `다음 페이지로` CTA가 첫 스크린에서 밀려남
6. reward/shop primer 구현을 핑계로 선택/구매 로직까지 건드림

---

## 11. 왜 이 단계가 지금 가장 가치가 큰가

B-1과 B-2는 `무엇부터 읽을까`를 library와 battle에 고정했다.
그 다음 실제 런 템포에서 가장 자주 오해되는 구간은 reward와 shop이다.

- reward는 `지금 바로 버티기 위한 선택`
- shop은 `이번 런의 방향을 굳히는 투자`

이 차이가 흐리면 플레이어는 둘 다 그냥 `카드 고르는 화면`으로 읽고,
구현자도 둘을 같은 strip 하나로 때우기 쉽다.

즉 B-3의 목적은 새 기능 추가가 아니라,
**보상과 상점의 역할 분리를 플레이어 화면에서도 같은 밀도로 읽히게 만드는 handoff anchor**를 남기는 것이다.

---

## 12. 다음 액션

B-3이 닫힌 뒤에야 다음 선택지가 생긴다.

1. 여전히 overrun A-track 실구현이 최우선이면, B-3는 handoff-ready 상태로만 유지
2. 온보딩 B-track이 실제로 열리면, 순서는 반드시 `B-1 library -> B-2 battle -> B-3 reward/shop`
3. 그다음 문서는 필요 시 `B-4 ending primer packet`으로 분리하되, ending은 끝까지 `docs/dicespell_ending_overrun_boundary_packet_kr.md`를 따른다

즉 B-3 문서는 `온보딩 후반부 전부`를 여는 문서가 아니라,
**reward와 shop을 같은 작은 UI로 뭉개지지 않게 만드는 bounded anchor**다.

---

## 13. 리스크 & 후속과제

- 가장 큰 리스크는 `reward와 shop도 짧으니 같이 빨리 붙이자`가 아니라, **짧다고 생각해서 둘을 같은 구조로 붙이는 것**이다.
- 두 번째 리스크는 reward에서 boss draft가 함께 열릴 때 primer가 중복되거나, shop에서 body-copy와 primer가 같은 말을 반복해 정보 밀도가 늘어나는 것이다.
- 세 번째 리스크는 B-3을 열면서 ending primer까지 같이 붙여 `reward/shop/ending`이 다시 한 커밋으로 뭉치는 것이다.
- 따라서 B-3는 `reward.first` 1면 + `shop.first` 1면 + `seenRewardPrimer` / `seenShopPrimer` 2필드 + boss draft/CTA 비차단 검수선으로만 닫는다.
- 그것보다 커지면 B-3가 아니라 다른 작업이다.
