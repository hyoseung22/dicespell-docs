# DiceSpell 첫 런 온보딩 content deck

## 문서 목적

`docs/dicespell_first_run_onboarding_handoff_kr.md`, `docs/dicespell_first_run_onboarding_screen_packet_kr.md`, `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`, `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`, `docs/dicespell_first_run_onboarding_source_of_truth_map_kr.md`, `docs/dicespell_first_run_onboarding_production_cutsheet_kr.md`는 이미 **surface / 상태 / 화면 위치 / owner handoff / acceptance / 복구선**을 충분히 고정했다.

하지만 실제 구현 직전 기준으로는 마지막 작은 공백이 하나 남아 있다.

> library / battle / reward / shop / ending primer가 필요하다는 건 알겠는데, 그래서 **정확히 어떤 content key / 카피 / 배지 / accent / 아트 토큰 / QA 기대값**을 어디에 쓰는가?

이 문서는 그 빈칸만 메운다.

즉 이 문서는 새 시스템 제안이 아니라, 첫 런 온보딩을 실제로 붙일 때 프로그래머/UI/아트/카피/QA가 다시 문자열과 분기 기준을 조합하지 않도록 만드는 **exact content source-of-truth deck**이다.

---

## 1. 왜 지금 이 문서가 필요한가

현재 온보딩 문서 묶음은 아래를 이미 충분히 닫았다.

- `도서관 책 선택 primer` / `첫 전투 primer` / `첫 reward·shop primer` / `첫 ending·overrun primer` 4개 surface 방향
- `renderGrimoireShelf()` / `renderBattle()` / `renderReward()` / `renderShop()` / `renderEnding()` 기준 주입 위치
- `meta.uiGuidance` 저장 위치와 `getPrimerState()` helper shape
- acceptance 8종 / recovery 6종 / owner별 DoD / 인수인계 순서

하지만 실제 구현 직전에는 아래가 아직 사람 손으로 다시 조합될 위험이 있다.

1. `library` primer가 `초반 운영을 먼저 읽어라`는 건 알겠는데, exact title/body/CTA와 highlight key가 문서 한 장으로 보이지 않는다.
2. `battle` primer는 진입 / 첫 주문 / 자동 종료 3갈래인데, 각 branch가 어떤 tone과 accent를 써야 하는지 아직 암묵적이다.
3. `reward`와 `shop`은 목적 차이를 말해야 하지만, 배지 이름과 카피 길이가 고정돼 있지 않으면 둘이 다시 비슷한 문장으로 흐를 수 있다.
4. `ending`은 패배 / 승리 / 오버런 CTA 주변 카피까지 갈라야 하는데, exact content 계약이 없으면 본문과 CTA가 서로 같은 말을 반복할 수 있다.
5. QA는 `primer가 보였다` 수준은 확인할 수 있어도, 정확히 어떤 content key와 text token이 떠야 pass인지가 흐리다.

즉 지금 필요한 것은 새로운 handoff 문서가 아니라, **온보딩 branching을 한 번 더 고정하는 제작용 기준표**다.

---

## 2. 이번 문서의 한 줄 결론

첫 런 온보딩은 render 함수가 임기응변으로 문장을 조립하는 레이어가 아니다.

> 엔진/앱이 이미 알고 있는 `현재 surface`, `현재 사건(event)`, `현재 결과(outcome)`를 기준으로 **고정된 onboarding content key 세트**를 내려 주고, UI는 그것을 그대로 배치만 해야 한다.

따라서 다음 원칙을 고정한다.

1. `render*()` 함수는 primer 문구를 조건문으로 직접 조립하지 않는다.
2. 최소 `contentKey`와 `accentKey`, 가능하면 `badgeLabel`까지 helper payload에 포함한다.
3. 텍스트/배지/accent/아트 토큰은 `contentKey`를 source-of-truth로 삼는다.
4. surface 설명과 CTA 설명은 분리하되, 같은 의미를 두 번 반복하지 않는다.

---

## 3. content source-of-truth 구조

## 3.1 권장 payload 추가 필드

```json
{
  "uiGuidancePrimer": {
    "surface": "library | battle | reward | shop | ending",
    "slot": "hero | inline | badge | cta",
    "contentKey": "library.book-select | battle.entry | battle.first-spell | battle.auto-pass | reward.first | shop.first | ending.defeat | ending.victory | ending.overrun-cta",
    "eyebrow": "string | null",
    "title": "string | null",
    "lines": ["string"],
    "badgeLabel": "string | null",
    "confirmLabel": "string | null",
    "accentKey": "library-gold | battle-blue | battle-amber | reward-warm | shop-cool | ending-ash | ending-victory | ending-overrun",
    "artTokenSet": "primer-library | primer-battle-entry | primer-battle-inline | primer-reward | primer-shop | primer-ending-defeat | primer-ending-victory | primer-ending-overrun",
    "highlightKeys": ["openingPlan", "caution"],
    "iconKey": "book | dice | lock | reward | shop | archive | overrun"
  }
}
```

## 3.2 왜 `contentKey`가 필요한가

`surface=ending`만으로는 패배 / 승리 / 오버런 CTA를 구분할 수 없다.
`surface=battle`만으로는 진입 / 첫 주문 / 자동 종료를 구분하기 어렵다.

따라서 최소 아래 9개 key를 고정한다.

- `library.book-select`
- `battle.entry`
- `battle.first-spell`
- `battle.auto-pass`
- `reward.first`
- `shop.first`
- `ending.defeat`
- `ending.victory`
- `ending.overrun-cta`

이 9개가 이번 단계의 유일한 onboarding content branch다.

---

## 4. branch 결정 규칙

## 4.1 helper 결정 순서

1. 현재 phase/surface 판정
2. `meta.uiGuidance` 기준 `seen` 여부 판정
3. battle면 사건 기반 분기 우선
   - 첫 주문 직후면 `battle.first-spell`
   - 자동 종료 직후면 `battle.auto-pass`
   - 둘 다 아니고 첫 전투 진입이면 `battle.entry`
4. ending이면 결과 기반 분기 우선
   - 패배면 `ending.defeat`
   - 승리 본문이면 `ending.victory`
   - 오버런 계속 CTA 주변 보조 문구면 `ending.overrun-cta`
5. reward/shop/library는 해당 첫 진입 1회만 고정 key 사용

## 4.2 절대 하지 말아야 할 것

- render 함수 안에서 `if (isFirstRun) text = '...'` 식으로 문구 직접 조립
- `body-copy` 기존 문장을 재가공해 primer 문구로 재사용
- `reward`와 `shop`의 목적 차이를 같은 badge 문구로 얼버무리기
- `ending` 본문과 CTA 근처에 같은 의미의 문장을 중복 배치

`contentKey`는 helper 계산 시점에만 정한다.

---

## 5. exact content matrix

## 5.1 공통 고정값

| 필드 | 값 |
| --- | --- |
| 최대 메시지 수 | hero는 title + body 2줄, inline은 최대 2문장, badge/cta는 1문장 |
| CTA 수 | hero slot은 1개, inline/badge/cta slot은 별도 CTA 없음 |
| 문체 | 짧고 지시적이되 과장 금지 |
| 금지 톤 | `이게 정답입니다`, `이건 쉬운 책입니다`, `반드시 이렇게 하세요` |
| 공통 목표 | 시스템을 다 설명하지 않고 지금 읽어야 할 순서만 고정 |

## 5.2 library branch

### `library.book-select`

| 필드 | 값 |
| --- | --- |
| `surface` | `library` |
| `slot` | `hero` |
| `eyebrow` | `첫 런 가이드` |
| `title` | `첫 책은 이렇게 읽으면 됩니다` |
| `lines[0]` | `먼저 ‘초반 운영’을 보고, 바로 아래 ‘주의점’까지만 읽어도 시작하기 충분합니다.` |
| `lines[1]` | `고유 규칙과 완주 보너스는 책 감이 온 뒤 읽어도 늦지 않습니다.` |
| `confirmLabel` | `알겠음` |
| `badgeLabel` | `null` |
| `accentKey` | `library-gold` |
| `artTokenSet` | `primer-library` |
| `highlightKeys` | `openingPlan`, `caution` |
| `iconKey` | `book` |
| `tone` | `읽는 순서 / 초반 운영 / 과밀 정보 정리` |

## 5.3 battle branches

### `battle.entry`

| 필드 | 값 |
| --- | --- |
| `surface` | `battle` |
| `slot` | `hero` |
| `eyebrow` | `첫 전투 가이드` |
| `title` | `이번 턴은 한 번의 주문으로 끝납니다` |
| `lines[0]` | `먼저 가능한 족보를 보고, 이번 턴에 쓸 주문 하나를 고르세요.` |
| `lines[1]` | `결과는 로그와 잠금 표시에서 바로 확인할 수 있습니다.` |
| `confirmLabel` | `전투 시작` |
| `badgeLabel` | `null` |
| `accentKey` | `battle-blue` |
| `artTokenSet` | `primer-battle-entry` |
| `iconKey` | `dice` |
| `tone` | `행동 순서 / 주문 1회 / 결과 읽기` |

### `battle.first-spell`

| 필드 | 값 |
| --- | --- |
| `surface` | `battle` |
| `slot` | `inline` |
| `eyebrow` | `null` |
| `title` | `null` |
| `lines[0]` | `방금 쓴 족보는 이번 페이지 동안 잠길 수 있습니다.` |
| `lines[1]` | `피해, 상태이상, 잠금 결과는 로그에서 바로 읽을 수 있습니다.` |
| `confirmLabel` | `null` |
| `badgeLabel` | `첫 주문` |
| `accentKey` | `battle-amber` |
| `artTokenSet` | `primer-battle-inline` |
| `iconKey` | `lock` |
| `tone` | `사건 직후 / 잠금 / 결과 해석` |

### `battle.auto-pass`

| 필드 | 값 |
| --- | --- |
| `surface` | `battle` |
| `slot` | `inline` |
| `eyebrow` | `null` |
| `title` | `null` |
| `lines[0]` | `지금은 쓸 수 있는 주문이 없어 몬스터 턴으로 넘어갔습니다.` |
| `lines[1]` | `다음 턴에는 가능한 족보부터 다시 확인하세요.` |
| `confirmLabel` | `null` |
| `badgeLabel` | `자동 종료` |
| `accentKey` | `battle-amber` |
| `artTokenSet` | `primer-battle-inline` |
| `iconKey` | `dice` |
| `tone` | `자동 진행 / 원인 설명 / 복구 가능성` |

## 5.4 reward / shop branches

### `reward.first`

| 필드 | 값 |
| --- | --- |
| `surface` | `reward` |
| `slot` | `badge` |
| `eyebrow` | `null` |
| `title` | `null` |
| `lines[0]` | `보상은 다음 1~2전투를 버티고 밀어 주는 즉시 선택입니다.` |
| `confirmLabel` | `null` |
| `badgeLabel` | `첫 보상` |
| `accentKey` | `reward-warm` |
| `artTokenSet` | `primer-reward` |
| `iconKey` | `reward` |
| `tone` | `즉시 효익 / 다음 전투 / 단기 전술` |

### `shop.first`

| 필드 | 값 |
| --- | --- |
| `surface` | `shop` |
| `slot` | `badge` |
| `eyebrow` | `null` |
| `title` | `null` |
| `lines[0]` | `상점은 이번 런의 장기 방향을 굳히는 투자 공간입니다.` |
| `confirmLabel` | `null` |
| `badgeLabel` | `첫 상점` |
| `accentKey` | `shop-cool` |
| `artTokenSet` | `primer-shop` |
| `iconKey` | `shop` |
| `tone` | `장기 투자 / 런 방향 / 보상과 역할 분리` |

## 5.5 ending branches

### `ending.defeat`

| 필드 | 값 |
| --- | --- |
| `surface` | `ending` |
| `slot` | `badge` |
| `eyebrow` | `null` |
| `title` | `null` |
| `lines[0]` | `런은 끝나도 이번에 남긴 페이지 기록과 서고 진척은 다음 선택에 남습니다.` |
| `confirmLabel` | `null` |
| `badgeLabel` | `첫 결과 정산` |
| `accentKey` | `ending-ash` |
| `artTokenSet` | `primer-ending-defeat` |
| `iconKey` | `archive` |
| `tone` | `패배 후 남는 것 / 정산 / 재도전 부담 완화` |

### `ending.victory`

| 필드 | 값 |
| --- | --- |
| `surface` | `ending` |
| `slot` | `badge` |
| `eyebrow` | `null` |
| `title` | `null` |
| `lines[0]` | `책을 끝내면 완주 보너스를 챙긴 뒤, 이어서 다음 권으로 넘어갈지 고를 수 있습니다.` |
| `confirmLabel` | `null` |
| `badgeLabel` | `첫 완주` |
| `accentKey` | `ending-victory` |
| `artTokenSet` | `primer-ending-victory` |
| `iconKey` | `archive` |
| `tone` | `완주 / 보너스 / 다음 선택` |

### `ending.overrun-cta`

| 필드 | 값 |
| --- | --- |
| `surface` | `ending` |
| `slot` | `cta` |
| `eyebrow` | `null` |
| `title` | `null` |
| `lines[0]` | `이 버튼은 정산을 끝내고, 지금 챙긴 보너스를 들고 다음 권으로 이어가는 선택입니다.` |
| `confirmLabel` | `null` |
| `badgeLabel` | `오버런 계속` |
| `accentKey` | `ending-overrun` |
| `artTokenSet` | `primer-ending-overrun` |
| `iconKey` | `overrun` |
| `tone` | `CTA 의미 / 이어가기 / 버튼 해석 고정` |

---

## 6. surface 본문과 primer의 책임 분리

## 6.1 primer가 말하는 것

primer는 아래만 말한다.

1. 지금 이 surface에서 무엇을 먼저 읽어야 하는가
2. 지금 이 surface가 reward인지 투자 공간인지 정산인지 어떻게 해석해야 하는가
3. 지금 버튼/행동이 무엇을 의미하는가

## 6.2 기존 본문이 말하는 것

기존 body-copy / reward card / shop card / ending 카드 본문은 계속 현재 시스템 설명과 실제 선택지 내용을 담당한다.

즉 아래는 금지한다.

- primer에서 카드 효과 수치 반복
- primer에서 reward/shop 카드 텍스트를 그대로 복창
- ending primer에서 `overrunEvent` 장면 flavor까지 미리 설명
- battle primer에서 상태이상 백과사전처럼 설명 확장

---

## 7. art / token mapping

## 7.1 배지 토큰

| `badgeLabel` | 토큰 |
| --- | --- |
| `첫 보상` | `primer_badge_reward_first` |
| `첫 상점` | `primer_badge_shop_first` |
| `첫 결과 정산` | `primer_badge_ending_defeat` |
| `첫 완주` | `primer_badge_ending_victory` |
| `오버런 계속` | `primer_badge_overrun_cta` |
| `첫 주문` | `primer_badge_first_spell` |
| `자동 종료` | `primer_badge_auto_pass` |

## 7.2 accent key 정의

| `accentKey` | 의미 | 권장 톤 |
| --- | --- | --- |
| `library-gold` | 읽기 우선순위 / 도서관 | gold / brown |
| `battle-blue` | 전투 진입 안내 | desaturated blue |
| `battle-amber` | 사건 기반 전투 힌트 | amber / brass |
| `reward-warm` | 즉시 선택 | warm gold / ember |
| `shop-cool` | 장기 투자 | teal / cool steel |
| `ending-ash` | 패배 정산 | ash / muted bronze |
| `ending-victory` | 완주 정산 | gold / ivory |
| `ending-overrun` | 이어가기 CTA 설명 | violet / overrun glow |

## 7.3 icon key 정의

| `iconKey` | 의미 |
| --- | --- |
| `book` | 책 선택 / 읽기 순서 |
| `dice` | 전투 시작 / 자동 진행 |
| `lock` | 첫 주문 후 잠금 안내 |
| `reward` | 보상 |
| `shop` | 상점 |
| `archive` | 기록 / 정산 |
| `overrun` | 이어가기 / 다음 권 진입 |

---

## 8. owner별 handoff 메모

## 8.1 프로그래머

- `contentKey`는 `getPrimerState()` 또는 동등 helper가 결정한다.
- render 함수는 `contentKey`와 payload를 소비만 한다.
- `badgeLabel` / `accentKey` / `artTokenSet`는 UI가 재판정하지 않게 payload에 포함하는 편이 안전하다.
- `ending.victory`와 `ending.overrun-cta`는 한 surface 안에 같이 존재해도 의미가 겹치지 않도록 slot을 분리한다.

## 8.2 UI

- `library`와 `battle.entry`만 hero-card 계열을 쓴다.
- `battle.first-spell`, `battle.auto-pass`, `reward.first`, `shop.first`, `ending.*`는 inline/badge 밀도만 유지한다.
- `reward.first`와 `shop.first`는 배지 구조는 비슷해도 accent와 위치 체감이 달라야 한다.
- `ending.overrun-cta`는 버튼을 덮는 카드가 아니라 버튼 근처 1줄 보조 문구여야 한다.

## 8.3 아트

- 신규 대형 일러스트는 필요 없다.
- 최소 패키지는 배지 토큰 7종, icon key 7종, accent palette 8종이면 충분하다.
- `ending-overrun`만 살짝 이질적인 glow를 허용하되, 별도 이벤트 화면처럼 과장하지 않는다.

## 8.4 QA

- pass 기준은 `보였다`가 아니라 `정확한 contentKey가 정확한 slot에 정확한 카피로 떴는가`다.
- `reward.first`와 `shop.first`가 서로 다른 목적 문장으로 읽히는지 반드시 구분해 본다.
- `ending.victory`와 `ending.overrun-cta`가 같은 문장을 반복하지 않는지 확인한다.
- `battle.first-spell`과 `battle.auto-pass`는 사건 기반 1회 노출이어야 하며, battle entry hero-primer와 동시에 과밀하게 겹치면 실패다.

---

## 9. QA expected values

| 시나리오 | 기대 `contentKey` | 기대 slot | 핵심 pass 기준 |
| --- | --- | --- | --- |
| 새 프로필 첫 도서관 진입 | `library.book-select` | `hero` | `초반 운영` / `주의점` 읽기 순서와 `알겠음` CTA 표시 |
| 새 프로필 첫 전투 진입 | `battle.entry` | `hero` | `주문 1회` title + 행동 순서 2줄 |
| 첫 주문 성공 직후 | `battle.first-spell` | `inline` | `첫 주문` 배지 + 잠금/로그 2문장 |
| 첫 자동 종료 직후 | `battle.auto-pass` | `inline` | `자동 종료` 배지 + 원인 설명 1~2문장 |
| 첫 보상 진입 | `reward.first` | `badge` | `첫 보상` 배지 + 즉시 선택 문장 |
| 첫 상점 진입 | `shop.first` | `badge` | `첫 상점` 배지 + 장기 투자 문장 |
| 첫 패배 엔딩 | `ending.defeat` | `badge` | `첫 결과 정산` 배지 + 서고 기록 유지 문장 |
| 첫 승리 엔딩 본문 | `ending.victory` | `badge` | `첫 완주` 배지 + 완주 보너스/다음 권 선택 문장 |
| 첫 승리 엔딩 CTA 근처 | `ending.overrun-cta` | `cta` | `오버런 계속` 주변 1줄 보조 문구 |

---

## 10. 다음 구현 슬롯 메모

다음 UX pass는 더 이상 온보딩 문구를 새로 쓰는 일이 아니라, 이 문서의 9개 `contentKey`를 `meta.uiGuidance` + `getPrimerState()` + 각 `render*()` slot에 그대로 꽂는 작업이어야 한다.

실제 구현 순서는 기존 `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`의 Step 0~4를 그대로 따른다.

1. `meta.uiGuidance`와 helper를 먼저 고정
2. `library.book-select` + `battle.entry`부터 붙임
3. 사건 기반 `battle.first-spell` / `battle.auto-pass` 추가
4. `reward.first` / `shop.first` 추가
5. `ending.defeat` / `ending.victory` / `ending.overrun-cta`를 마지막에 붙여 `overrunEvent`와 문맥 충돌 없는지 검수

즉 이번 문서의 역할은 `무슨 말을 할까`가 아니라, **다음 구현자가 더 이상 문자열 / 배지 / accent / 토큰을 다시 추론하지 않게 만드는 것**이다.
