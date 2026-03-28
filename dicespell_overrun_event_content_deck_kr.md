# DiceSpell `overrunEvent` content deck

## 문서 목적

`docs/dicespell_overrun_event_handoff_kr.md`, `docs/dicespell_overrun_event_implementation_packet_kr.md`, `docs/dicespell_overrun_event_screen_packet_kr.md`, `docs/dicespell_overrun_event_production_cutsheet_kr.md`, `docs/dicespell_overrun_event_acceptance_matrix_kr.md`, `docs/dicespell_overrun_event_integration_workorder_kr.md`, `docs/dicespell_overrun_event_source_of_truth_map_kr.md`, `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`는 이미 **phase / 상태 / 화면 / 생산 handoff / 복구 / 컷오버 순서**를 충분히 고정했다.

하지만 실제 구현 직전 기준으로는 마지막 작은 공백이 하나 남아 있다.

> `themeTrophy`, `enemyTrophy`, `none`을 구분한다는 건 알겠는데, 그래서 **정확히 어떤 content key / 카피 / 배지 / accent / 자산 토큰 / QA 기준**을 어디에 쓰는가?

이 문서는 그 빈칸만 메운다.

즉 이 문서는 새 시스템 제안이 아니라, `overrunEvent`를 실제로 붙일 때 프로그래머/UI/아트/카피/QA가 다시 문자열과 분기 기준을 조합하지 않도록 만드는 **exact content source-of-truth deck**이다.

---

## 1. 왜 지금 이 문서가 필요한가

현재 문서 묶음은 아래를 이미 충분히 닫았다.

- `ending -> overrunEvent -> 다음 세그먼트` phase 방향
- CTA 시점 적용
- `themeTrophy / enemyTrophy / none` 3상태 구분
- save/load fallback
- 중앙 카드 1장 레이아웃
- owner별 deliverable과 DoD

하지만 실제 구현 직전에는 아래가 아직 사람 손으로 다시 조합될 위험이 있다.

1. `themeTrophy` 안에서도 슬라임/고블린/리치가 어떤 header/flavor/accent를 공유하고 무엇을 개별화하는지 문서 한 장으로 보이지 않는다.
2. `enemyTrophy` 안에서도 `기록 골렘`과 `붕락 정령`이 title은 reward card에 이미 있지만, 헤더/flavor/보조 accent를 어디까지 갈라야 하는지 아직 암묵적이다.
3. 프로그래머가 `reward.title`만 읽어 카피를 역추론하면, 나중에 문구 변경 때 UI/QA가 같이 흔들린다.
4. 아트 owner는 `배지 3종`과 `accent 4종`은 알지만, 어느 sourceKey에서 어떤 조합을 써야 하는지 token 단위로 못 박혀 있지 않다.
5. QA는 `themeTrophy로 보인다`는 정도는 확인할 수 있어도, 정확히 어떤 text token과 accent key가 떠야 pass인지가 흐리다.

즉 지금 필요한 것은 새로운 phase 문서가 아니라, **content branching을 한 번 더 고정하는 제작용 기준표**다.

---

## 2. 이번 문서의 한 줄 결론

`overrunEvent`는 reward card title/description을 다시 해석하는 화면이 아니다.

> 엔진이 이미 알고 있는 `선택 출처(source)`와 `현재 책/적 맥락(context)`를 기준으로, **고정된 content key 세트**를 내려 주고 UI는 그것을 그대로 배치만 해야 한다.

따라서 다음 원칙을 고정한다.

1. `renderOverrunEvent()`는 `reward.title`을 읽어 문맥을 추론하지 않는다.
2. `sourceType`만으로 끝내지 않고, 최소 `contentKey`를 함께 받는다.
3. 텍스트/배지/accent/자산 토큰은 `contentKey`를 source-of-truth로 삼는다.
4. reward card 내부 title/description은 현재 보상 정의를 재사용하되, 헤더/flavor/CTA는 이 문서 기준으로 고정한다.

---

## 3. content source-of-truth 구조

## 3.1 권장 payload 추가 필드

기존 문서의 최소 payload 위에 아래 필드를 추가하는 것을 권장한다.

```json
{
  "phase": "overrunEvent",
  "overrunEvent": {
    "sourceType": "themeTrophy | enemyTrophy | none",
    "contentKey": "theme.slime | theme.goblin | theme.lich | enemy.420016 | enemy.420017 | fallback.none",
    "rewardId": "string | null",
    "rewardTitle": "string",
    "rewardDescription": "string",
    "rewardTag": "string",
    "headerTitle": "string",
    "flavorText": "string",
    "confirmLabel": "string",
    "eyebrow": "Overrun Transition",
    "badgeLabel": "string",
    "accentKey": "slime | goblin | lich | golem | collapse | none",
    "artTokenSet": "theme-slime | theme-goblin | theme-lich | enemy-golem | enemy-collapse | neutral-none",
    "footnote": "확인 후 다음 세그먼트 첫 페이지로 이동합니다.",
    "sourceTemplateId": "420016 | 420017 | null",
    "sourceTheme": "slime | goblin | lich | null"
  }
}
```

## 3.2 왜 `contentKey`가 필요한가

`sourceType=themeTrophy`만으로는 슬라임/고블린/리치를 구분할 수 없다.
`sourceType=enemyTrophy`만으로는 `기록 골렘`과 `붕락 정령`의 문맥 차이를 보존하기 어렵다.

따라서 최소 아래 6개 key를 고정한다.

- `theme.slime`
- `theme.goblin`
- `theme.lich`
- `enemy.420016`
- `enemy.420017`
- `fallback.none`

이 6개가 이번 단계의 유일한 content branch다.

---

## 4. branch 결정 규칙

## 4.1 엔진 결정 순서

1. `selectedRewardId` lookup 성공
2. 선택 option이 책 테마 전리품이면 `contentKey = theme.<currentTheme>`
3. 선택 option이 적 전리품이면 `contentKey = enemy.<templateId>`
4. lookup 실패 / selection 없음 / snapshot 복구 실패면 `contentKey = fallback.none`

## 4.2 절대 하지 말아야 할 것

- `reward.title.includes('점액')` 같은 문자열 추론
- UI에서 `rewardDescription`을 보고 `contentKey` 재구성
- `rewardTag`를 보고 `theme/enemy`를 다시 판정

`contentKey`는 엔진 snapshot 생성 시점에만 정한다.

---

## 5. exact content matrix

## 5.1 공통 고정값

| 필드 | 값 |
| --- | --- |
| `eyebrow` | `Overrun Transition` |
| `footnote` | `확인 후 다음 세그먼트 첫 페이지로 이동합니다.` |
| CTA 수 | 1개 |
| 카드 수 | 1장 |
| reward card body | 현재 reward card display-only 재사용 |

## 5.2 theme branch

### `theme.slime`

| 필드 | 값 |
| --- | --- |
| `sourceType` | `themeTrophy` |
| `badgeLabel` | `책 전리품` |
| `headerTitle` | `다음 권의 첫 장을 차분히 펼친다` |
| `flavorText` | `점액 잔향을 가라앉혀 다음 장의 첫 충격을 안정적으로 받는다.` |
| `confirmLabel` | `이 전리품을 챙기고 다음 권으로` |
| `accentKey` | `slime` |
| `artTokenSet` | `theme-slime` |
| `headerTone` | `안정 / 정리 / 완충` |

### `theme.goblin`

| 필드 | 값 |
| --- | --- |
| `sourceType` | `themeTrophy` |
| `badgeLabel` | `책 전리품` |
| `headerTitle` | `다음 권의 첫 장에 약탈의 탄력을 싣는다` |
| `flavorText` | `방금 건진 전리품을 곧바로 다음 전투 화력과 경제 템포로 환전한다.` |
| `confirmLabel` | `이 전리품을 챙기고 다음 권으로` |
| `accentKey` | `goblin` |
| `artTokenSet` | `theme-goblin` |
| `headerTone` | `노획 / 가속 / 황동` |

### `theme.lich`

| 필드 | 값 |
| --- | --- |
| `sourceType` | `themeTrophy` |
| `badgeLabel` | `책 전리품` |
| `headerTitle` | `다음 권의 첫 장에 금서의 잔향을 옮겨 적는다` |
| `flavorText` | `금서의 결을 챙겨 다음 장의 첫 주문을 더 빠르고 더 위험하게 연다.` |
| `confirmLabel` | `이 전리품을 챙기고 다음 권으로` |
| `accentKey` | `lich` |
| `artTokenSet` | `theme-lich` |
| `headerTone` | `필사 / 금서 / 속성 기대` |

## 5.3 enemy branch

### `enemy.420016` (`기록 골렘`)

| 필드 | 값 |
| --- | --- |
| `sourceType` | `enemyTrophy` |
| `badgeLabel` | `적 전리품` |
| `headerTitle` | `방금 넘긴 방벽의 잔해를 챙긴다` |
| `flavorText` | `골렘의 균열 조각을 다음 권 첫 전열 준비물로 바꿔 들고 간다.` |
| `confirmLabel` | `이 잔해를 들고 다음 권으로` |
| `accentKey` | `golem` |
| `artTokenSet` | `enemy-golem` |
| `headerTone` | `방벽 / 균열 / 전열 준비` |
| `sourceTemplateId` | `420016` |

### `enemy.420017` (`붕락 정령`)

| 필드 | 값 |
| --- | --- |
| `sourceType` | `enemyTrophy` |
| `badgeLabel` | `적 전리품` |
| `headerTitle` | `방금 넘긴 붕락의 잔해를 챙긴다` |
| `flavorText` | `정령의 응축핵을 다음 권 첫 템포를 뒤집는 연료로 바꿔 들고 간다.` |
| `confirmLabel` | `이 잔해를 들고 다음 권으로` |
| `accentKey` | `collapse` |
| `artTokenSet` | `enemy-collapse` |
| `headerTone` | `붕락 / 폭풍핵 / 템포 역전` |
| `sourceTemplateId` | `420017` |

## 5.4 fallback branch

### `fallback.none`

| 필드 | 값 |
| --- | --- |
| `sourceType` | `none` |
| `badgeLabel` | `빈손` |
| `headerTitle` | `가볍게 다음 권의 첫 장을 연다` |
| `flavorText` | `이번에는 아무 잔해도 들지 않은 채, 가벼운 손으로 다음 장을 펼친다.` |
| `confirmLabel` | `가볍게 다음 권을 연다` |
| `accentKey` | `none` |
| `artTokenSet` | `neutral-none` |
| `headerTone` | `중성 / 여백 / 비전리품` |

---

## 6. reward card와 헤더의 책임 분리

## 6.1 reward card가 말하는 것

reward card body는 계속 현재 보상 정의를 그대로 재사용한다.

예:
- `점액 응고편` / `농축 점액 응고편`
- `약탈 장부` / `대담한 약탈 장부`
- `금서 필사편` / `금서 잔향 필사편`
- `균열 석판 조각` / `균열 석판 조각+`
- `붕락 응축핵` / `붕락 응축핵+`

즉 수치와 실제 bundle 설명은 reward card가 담당한다.

## 6.2 헤더가 말하는 것

헤더는 reward 수치를 반복하지 않는다.
헤더는 아래만 말한다.

1. 지금은 세그먼트 경계다.
2. 들고 가는 대상의 성격은 무엇인가.
3. 이 행동이 다음 권 첫 장과 어떤 문맥으로 이어지는가.

따라서 아래는 금지한다.

- 헤더에서 `Shield +14`, `MP +3` 같은 수치 반복
- 헤더에서 `무작위 족보 1개 강화` 같은 bundle 나열
- 헤더가 reward card title을 그대로 한 번 더 복창

---

## 7. art / token mapping

## 7.1 배지 토큰

| `badgeLabel` | 토큰 |
| --- | --- |
| `책 전리품` | `overrun_badge_theme` |
| `적 전리품` | `overrun_badge_enemy` |
| `빈손` | `overrun_badge_none` |

## 7.2 accent key → 자산 토큰

| `accentKey` | 권장 색/무드 | `artTokenSet` |
| --- | --- | --- |
| `slime` | 녹청, 안정, 완충 | `theme-slime` |
| `goblin` | 황동, 금화, 노획 | `theme-goblin` |
| `lich` | 청보라, 금서, 잔향 | `theme-lich` |
| `golem` | 석판, 균열, 방벽 | `enemy-golem` |
| `collapse` | 자홍/청전기, 붕락, 폭풍핵 | `enemy-collapse` |
| `none` | 잿빛, 저채도, 여백 | `neutral-none` |

## 7.3 최소 자산 조합

이번 단계에서 실제 아트가 준비해야 하는 최소 조합은 아래다.

1. 공용 헤더 패널 1종
2. 배지 3종
3. focus backplate 1세트
4. accent rule 6종

즉 기존 문서의 `accent 4종`을 실제 구현 기준으로는 `6종`으로 세분한다.
이유는 `enemyTrophy` 내부에서도 `기록 골렘`과 `붕락 정령`이 시각적으로 같아 보이면 적 전리품 branch의 기억점이 약해지기 때문이다.

단, 이 세분은 **레이아웃 분기 증가**가 아니라 accent/texture token 분리 수준으로만 다룬다.

---

## 8. 프로그래머 handoff

## 8.1 snapshot 생성 시 최소 보장값

프로그래머는 아래 값을 snapshot 생성부에서 모두 채운다.

- `contentKey`
- `badgeLabel`
- `headerTitle`
- `flavorText`
- `confirmLabel`
- `accentKey`
- `artTokenSet`
- `footnote`
- `sourceTheme`
- `sourceTemplateId`

UI는 이 값이 비었을 때 최대한 `fallback.none`으로 강등하고, 다시 계산하지 않는다.

## 8.2 권장 helper shape

```js
const OVERRUN_EVENT_CONTENT = {
  "theme.slime": { ... },
  "theme.goblin": { ... },
  "theme.lich": { ... },
  "enemy.420016": { ... },
  "enemy.420017": { ... },
  "fallback.none": { ... },
};
```

권장 흐름:

1. `determineOverrunEventContentKey(state, option)`
2. `buildOverrunEventContent(contentKey)`
3. `createOverrunEventSnapshot(...)`

## 8.3 절대 하지 말아야 할 구현

- `switch (reward.title)`로 분기
- UI render 단계에서 `accentKey`를 재구성
- `none` 상태에서만 별도 DOM 구조 사용

---

## 9. UI handoff

## 9.1 상태별 고정 시각 차이

| branch | 배지 | accent | 추가 모티프 |
| --- | --- | --- | --- |
| `theme.slime` | 책 전리품 | slime | 없음 |
| `theme.goblin` | 책 전리품 | goblin | 없음 |
| `theme.lich` | 책 전리품 | lich | 없음 |
| `enemy.420016` | 적 전리품 | golem | 석판/균열 보조 texture 허용 |
| `enemy.420017` | 적 전리품 | collapse | 전하/붕락 보조 glow 허용 |
| `fallback.none` | 빈손 | none | 없음 |

## 9.2 UI가 반복하면 안 되는 것

- reward card title을 헤더에 다시 쓰기
- flavorText 아래에 reward description을 또 한 번 복사
- 적 전리품 둘을 똑같은 accent로 처리

---

## 10. 카피 handoff

## 10.1 tone 원칙

- 세그먼트 경계 장면을 먼저 말한다.
- 카드 안의 수치를 헤더에서 반복하지 않는다.
- `넘어간다`, `챙긴다`, `옮겨 적는다`, `가라앉힌다`, `환전한다` 같은 동사로 전환감을 만든다.

## 10.2 금지 표현

- `보상이 적용됩니다`
- `선택한 아이템을 확인하세요`
- `다음 단계로 진행`
- `시스템상 전리품이 준비되었습니다`
- `현재 효과는 다음과 같습니다`를 헤더에 쓰는 것

## 10.3 향후 수정 우선순위

문구를 바꿔야 하면 아래 순서로만 바꾼다.

1. 이 문서의 content matrix
2. 구현부의 content map
3. QA snapshot / fixture 기대값

UI 템플릿을 먼저 바꾸지 않는다.

---

## 11. QA acceptance addendum

## 11.1 sourceKey별 pass 기준

| 케이스 | 반드시 보여야 하는 값 |
| --- | --- |
| `theme.slime` | `badgeLabel=책 전리품`, `accentKey=slime`, `headerTitle=다음 권의 첫 장을 차분히 펼친다` |
| `theme.goblin` | `badgeLabel=책 전리품`, `accentKey=goblin`, `headerTitle=다음 권의 첫 장에 약탈의 탄력을 싣는다` |
| `theme.lich` | `badgeLabel=책 전리품`, `accentKey=lich`, `headerTitle=다음 권의 첫 장에 금서의 잔향을 옮겨 적는다` |
| `enemy.420016` | `badgeLabel=적 전리품`, `accentKey=golem`, `headerTitle=방금 넘긴 방벽의 잔해를 챙긴다` |
| `enemy.420017` | `badgeLabel=적 전리품`, `accentKey=collapse`, `headerTitle=방금 넘긴 붕락의 잔해를 챙긴다` |
| `fallback.none` | `badgeLabel=빈손`, `accentKey=none`, `headerTitle=가볍게 다음 권의 첫 장을 연다` |

## 11.2 실패 조건

- `themeTrophy` 세 branch가 같은 header/flavor로만 보임
- `enemy.420016` / `enemy.420017`이 accent나 flavor에서 구분되지 않음
- `fallback.none`이 배지 없이 빈 화면처럼 보임
- reward card title 변경이 헤더 branch를 깨뜨림

---

## 12. 리스크 & 후속 과제

### 리스크 1. implementation/screen 문서와 content 문서가 이중 source-of-truth가 될 위험

- 영향: 어디 문구를 먼저 고쳐야 하는지 다시 헷갈릴 수 있다.
- 대응: phase/레이아웃/acceptance는 기존 문서 묶음이 source-of-truth, exact content branching은 이 문서가 source-of-truth라고 역할을 분리한다.

### 리스크 2. enemy accent 세분이 범위를 키울 위험

- 영향: `enemyTrophy` 하나로도 충분한데 자산 분기가 과해질 수 있다.
- 대응: 레이아웃은 그대로 두고, accent/texture token만 분리한다. 새 배지나 새 CTA를 늘리지 않는다.

### 리스크 3. reward title과 header가 서로 경쟁할 위험

- 영향: 카드도 읽어야 하고 헤더도 읽어야 해서 화면이 과밀해질 수 있다.
- 대응: header는 장면 문맥, reward card는 실제 수치 설명이라는 책임 분리를 유지한다.

### 후속 과제

1. 다음 구현 슬롯은 이 문서의 `contentKey`를 snapshot 생성부에 추가한다.
2. UI는 `accentKey` / `artTokenSet` 기준으로 class와 asset만 매핑한다.
3. smoke migration fixture(S1~S12)에 `contentKey` 기대값까지 추가하면, 이후 카피 drift를 가장 싸게 막을 수 있다.

---

## 13. 한 줄 결론

`overrunEvent`의 남은 마지막 공백은 phase가 아니라 **exact content contract**였다.
이 문서는 `무슨 장면인가`를 넘어서, **정확히 어떤 branch가 어떤 문구/배지/accent/token으로 렌더되어야 하는가**를 못 박는 최종 content deck이다.
