# DiceSpell overrunEvent ↔ 첫 런 온보딩 계약 예시 패킷

## 문서 목적

이 문서는 이미 닫혀 있는 아래 문서 묶음에 대해 **프로그래머/UI/아트/QA가 같은 예시를 보고 구현·검수할 수 있게 만드는 canonical example packet**이다.

- `docs/dicespell_overrun_event_*`
- `docs/dicespell_first_run_onboarding_*`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- `docs/dicespell_overrun_onboarding_cutover_master_plan_kr.md`

지금 DiceSpell의 공백은 큰 방향이 아니라 **실제 payload / DOM / fixture가 어떤 모습이어야 하는지 한 장에서 바로 확인하기 어려운 점**이다.
문서마다 필드와 규칙은 충분하지만, 구현 직전에는 아래 질문이 다시 생긴다.

1. `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet`이 실제 snapshot에서 어떤 모양으로 들어가야 하는가?
2. UI는 그 payload를 화면 어디에서 어떤 밀도로 소비해야 하는가?
3. 아트는 어떤 branch끼리 토큰만 바꾸고 어떤 branch는 구조까지 달라져야 하는가?
4. QA는 S1~S12, 온보딩 9개 key를 어떤 예시 상태와 바로 대조해야 하는가?

이 문서는 새 설계를 추가하지 않는다.
대신 이미 확정된 문서들을 **예시 상태 / 예시 payload / 예시 DOM / 예시 fixture 기대값**으로 역정렬해, 다음 구현자가 다시 머릿속에서 샘플을 조합하지 않게 만든다.

---

## 이 문서가 메우는 마지막 실행 공백

### 현재 이미 있는 것
- handoff / implementation / screen / acceptance / cutsheet / source-of-truth / content deck / cutover master plan

### 아직 구현 직전까지 남는 작은 공백
- 문서마다 필드는 보이지만, **한 번에 복사 가능한 기준 예시 상태**가 부족하다.
- `overrunEvent`와 onboarding이 모두 `payload 소비자` 구조를 지향하지만, 엔진/렌더/QA가 참고할 **동일 샘플**이 흩어져 있다.
- UI/아트가 `branch 차이`를 읽을 때, 어떤 차이는 카피 차이인지, 어떤 차이는 배지/accent 차이인지, 어떤 차이는 backplate 토큰 차이인지를 다시 정리해야 한다.

### 이 문서의 한 줄 역할
> 실제 코드에 꽂기 직전, `이 상태라면 payload는 이렇게 생기고 화면은 이렇게 읽혀야 한다`를 한 번에 보여 주는 예시 패킷.

---

## owner별 사용법

### 프로그래머에게 넘길 것
- 아래 JSON 예시를 `snapshot` / `primer payload`의 shape sanity 기준으로 사용한다.
- 필드명은 그대로 복사할 필요가 없지만, **정보 밀도와 책임 분리**는 유지한다.
- render 함수는 아래 예시 payload를 **재판정하지 않고 소비**하는 쪽이 맞다.

### UI owner에게 넘길 것
- 아래 `surface별 시각 요소` 표를 Figma/HTML handoff 체크리스트로 사용한다.
- hero / inline / badge 밀도를 새로 발명하지 말고 branch별 차이만 반영한다.

### 아트 owner에게 넘길 것
- `artTokenSet`과 `accentKey`가 다르면 보통 같은 구조의 토큰 교체로 해결한다.
- 구조가 달라지는 지점은 `overrunEvent 중앙 카드` vs `onboarding primer layer`뿐이다.

### QA에게 넘길 것
- 아래 `예시 상태 ↔ smoke/acceptance 매핑표`를 fixture 이름 옆 참고 표로 사용한다.
- 문자열 완전일치보다 우선 보는 것은 `phase`, `contentKey`, `accentKey`, `visible density`, `CTA semantics`다.

---

# 1. 공통 canonical 원칙

## 1.1 payload 책임 분리

| 층위 | 소유자 | 해야 하는 일 | 하면 안 되는 일 |
| --- | --- | --- | --- |
| 엔진 snapshot / helper | 프로그래머 | `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, 최소 카피/CTA 필드 확정 | render 단계에서 다시 추론하게 비워 두기 |
| render surface | 프로그래머, UI | payload를 hero/inline/badge slot에 꽂기 | `reward.title` 같은 문자열만 보고 branch 재판정 |
| 아트 토큰 | UI, 아트 | accent/backplate/badge tone 분리 | branch마다 구조를 새로 만들기 |
| smoke / acceptance | QA | key / phase / fallback / density pass 확인 | 카피 일부 문구 차이만으로 branch 실패 처리 |

## 1.2 공통 최소 필드

모든 구현 예시는 아래 4개 축을 최우선으로 고정한다.

- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`

즉 카피가 일부 다듬어져도, 이 4개가 같다면 같은 branch로 본다.

---

# 2. overrunEvent canonical 예시

## 2.1 상태 A — 책 테마 전리품 branch (`themeTrophy`)

### 사용 맥락
- 승리 엔딩에서 `고블린` 책 계열 전리품을 고르고 `오버런 계속` 진입
- `ending`은 결과/버튼 의미만 보여 주고, 실제 장면/맛은 `overrunEvent`에서 소비

### 엔진 snapshot 예시

```json
{
  "phase": "overrunEvent",
  "overrunEvent": {
    "resolved": false,
    "sourceType": "themeTrophy",
    "selectedRewardId": "reward_theme_goblin_cache",
    "contentKey": "theme.goblin.cache",
    "badgeLabel": "책 테마 전리품",
    "accentKey": "goblin-gold",
    "artTokenSet": "goblin-cache",
    "headerTitle": "약탈 꾸러미를 다음 권으로 넘긴다",
    "flavorText": "고블린 순찰지에서 챙긴 노획품이 다음 권 첫 장의 속도를 당겨 준다.",
    "confirmLabel": "전리품을 챙기고 다음 권으로",
    "rewardTag": "강화/경제",
    "rewardTitle": "긁힌 노획 주머니",
    "rewardDescription": "Gold +35, 무작위 족보 1개 강화",
    "footnote": "효과는 확인 버튼 시점에 적용된다."
  }
}
```

### UI가 화면에서 읽히게 해야 할 것
- 중앙 카드 1장
- 상단 badge: `책 테마 전리품`
- 본문 헤더: 다음 권으로 이어지는 장면성 헤더
- 하단 reward card: 실제 효과 요약
- CTA 1개: 즉시 적용이 아니라 **다음 권 진입 확인** 의미

### 아트 owner 메모
- 구조는 다른 `themeTrophy`와 동일
- 바뀌는 것은 `accentKey=goblin-gold`, `artTokenSet=goblin-cache`
- hero illustration이 없어도 badge/backplate/coin-like accent만으로 읽혀야 함

### QA pass 포인트
- `contentKey=theme.goblin.cache`
- `badgeLabel=책 테마 전리품`
- CTA가 `선택 확정`이 아니라 `다음 권 진입` 의미
- reward card가 실제 효과를 다시 보여 줌

---

## 2.2 상태 B — 적 전리품 branch (`enemyTrophy`)

### 사용 맥락
- 세그먼트 동안 `기록 골렘` 처치 후 엔딩 보관함에서 관련 전리품 선택

### 엔진 snapshot 예시

```json
{
  "phase": "overrunEvent",
  "overrunEvent": {
    "resolved": false,
    "sourceType": "enemyTrophy",
    "selectedRewardId": "reward_enemy_golem_fragment",
    "contentKey": "enemy.golem.fragment",
    "badgeLabel": "적 처치 전리품",
    "accentKey": "golem-stone",
    "artTokenSet": "golem-fragment",
    "headerTitle": "균열 석판 조각이 새 장의 초반 템포를 받친다",
    "flavorText": "방금 쓰러뜨린 골렘의 파편이 다음 전투에서 방벽을 먼저 벗길 준비물로 남는다.",
    "confirmLabel": "석판 조각을 챙기고 전진",
    "rewardTag": "방벽/대지",
    "rewardTitle": "균열 석판 조각",
    "rewardDescription": "Shield +12, 무작위 족보 1개 강화, 대지 속성 1회 주입",
    "footnote": "효과는 다음 세그먼트 진입 확인 시 적용된다."
  }
}
```

### UI가 화면에서 읽히게 해야 할 것
- `적 처치 전리품` 배지가 책 테마 전리품과 구분되어야 함
- 중앙 flavor는 `방금 쓰러뜨린 위협의 잔향` 쪽이어야 함
- reward card는 기존 reward 스타일을 display-only로 재사용 가능

### 아트 owner 메모
- `themeTrophy`와 카드 구조는 같아도 토큰 질감은 stone/shard 계열이어야 함
- 골렘과 붕락 정령은 같은 `enemyTrophy` 그룹이지만 accent tone을 같이 쓰지 않는다

### QA pass 포인트
- `contentKey=enemy.golem.fragment`
- theme branch와 같은 DOM 구조 유지
- 배지/토큰/헤더 정서만 enemy branch로 바뀜

---

## 2.3 상태 C — 선택 없음 / fallback branch (`none`)

### 사용 맥락
- 선택 없이 `오버런 계속`
- invalid rewardId 복구
- snapshot 누락에서 safe fallback 생성

### 엔진 snapshot 예시

```json
{
  "phase": "overrunEvent",
  "overrunEvent": {
    "resolved": false,
    "sourceType": "none",
    "selectedRewardId": null,
    "contentKey": "fallback.none",
    "badgeLabel": "준비 정리",
    "accentKey": "neutral-ash",
    "artTokenSet": "travel-pack",
    "headerTitle": "가방을 정리하고 다음 권으로 넘어간다",
    "flavorText": "특별히 챙긴 전리품은 없지만, 런의 템포는 끊기지 않는다.",
    "confirmLabel": "정리하고 다음 권으로",
    "rewardTag": "선택 없음",
    "rewardTitle": "가벼운 짐",
    "rewardDescription": "추가 효과 없음",
    "footnote": "선택을 건너뛰어도 오버런은 계속된다."
  }
}
```

### UI가 화면에서 읽히게 해야 할 것
- `none`도 빈 화면이 아니라 **정상적인 1장 카드**여야 함
- CTA, badge, footnote가 사라지지 않아야 함
- reward card는 placeholder라도 밀도를 유지해야 함

### 아트 owner 메모
- neutral branch는 새 구조가 아니라 tone만 중립화
- over-dramatic art 금지. travel-pack / ash / plain seal 정도면 충분

### QA pass 포인트
- `contentKey=fallback.none`
- invalid reward / missing snapshot / explicit skip이 모두 같은 safe density로 수렴 가능
- 진행이 막히지 않음

---

## 2.4 overrunEvent surface별 필수 요소

| slot | 필수 여부 | 설명 |
| --- | --- | --- |
| badge | 필수 | `책 테마 전리품` / `적 처치 전리품` / `준비 정리` |
| hero header | 필수 | 장면/감정선 담당 |
| flavor body | 필수 | 다음 권으로 넘어가는 맥락 |
| reward card | 필수 | 실제 효과 display-only 요약 |
| CTA 1개 | 필수 | confirm action |
| footnote 1줄 | 필수 | `효과는 확인 시점 적용` 또는 skip 의미 |

---

# 3. 첫 런 온보딩 canonical 예시

## 3.1 상태 D — library primer

### helper payload 예시

```json
{
  "visible": true,
  "surface": "library",
  "contentKey": "library.book-select",
  "badgeLabel": "첫 선택 가이드",
  "accentKey": "library-ink",
  "artTokenSet": "shelf-marker",
  "line": "책은 이번 런의 플레이 성격을 정한다.",
  "body": "처음엔 추천 운영과 주의점을 먼저 읽고, 마음에 드는 책 한 권만 고르면 된다.",
  "ctaLine": "지금은 완벽한 선택보다 읽히는 책 하나를 고르는 것이 우선이다."
}
```

### UI가 화면에서 읽히게 해야 할 것
- 책 카드 본체를 가리지 않는 hero/inline primer
- 책 선택이라는 본체보다 primer가 더 커지면 안 됨
- `추천 운영 / 주의점` 읽기 유도 톤 유지

### 아트 owner 메모
- 책 선택 primer는 scene card가 아니라 shelf 위 marker 느낌

### QA pass 포인트
- 첫 진입에만 보임
- 책 선택 자체를 막지 않음
- `contentKey=library.book-select`

---

## 3.2 상태 E — battle entry primer

### helper payload 예시

```json
{
  "visible": true,
  "surface": "battle",
  "contentKey": "battle.entry",
  "badgeLabel": "첫 전투 가이드",
  "accentKey": "battle-bronze",
  "artTokenSet": "battle-ribbon",
  "line": "한 턴에는 주문을 한 번만 쓴다.",
  "body": "Roll과 Lock으로 원하는 족보를 만든 뒤, 이번 턴의 주문 한 번을 고르면 된다.",
  "ctaLine": "지금은 큰 콤보보다 한 번의 정확한 선택을 익히는 편이 좋다."
}
```

### UI가 화면에서 읽히게 해야 할 것
- 전투 메인 패널보다 작지만 첫 행동 전에 읽히는 위치
- `한 턴 = 한 주문` 핵심만 말함
- 리롤/잠금/턴 종료 전체를 다 설명하려 하지 않음

### QA pass 포인트
- 첫 전투 진입 시만 보임
- 주문 사용 이후 같은 밀도로 재노출되지 않음

---

## 3.3 상태 F — first spell primer

### helper payload 예시

```json
{
  "visible": true,
  "surface": "battle",
  "contentKey": "battle.first-spell",
  "badgeLabel": "첫 주문 힌트",
  "accentKey": "battle-gold",
  "artTokenSet": "spell-ping",
  "line": "주문을 쓰면 그 족보는 이번 페이지 동안 잠긴다.",
  "body": "지금은 가장 센 주문보다, 다음 턴 선택지까지 남기는 주문이 더 좋을 수 있다.",
  "ctaLine": "한 번의 주문이 끝나면 바로 몬스터 턴으로 넘어간다."
}
```

### QA pass 포인트
- 첫 주문 직전 또는 직후 1회만 보여 줌
- `entry` primer와 중복으로 둘 다 큰 hero block이 되지 않음

---

## 3.4 상태 G — reward / shop primer

### reward helper payload 예시

```json
{
  "visible": true,
  "surface": "reward",
  "contentKey": "reward.first",
  "badgeLabel": "보상 읽기",
  "accentKey": "reward-green",
  "artTokenSet": "reward-seal",
  "line": "전투 보상은 다음 1~2전투를 버티는 선택이다.",
  "body": "회복, 즉시 강화, 템포 보정처럼 지금 당장 도움이 되는 카드를 우선 읽는다.",
  "ctaLine": "지금 화면은 장기 투자보다 바로 다음 전투 준비에 가깝다."
}
```

### shop helper payload 예시

```json
{
  "visible": true,
  "surface": "shop",
  "contentKey": "shop.first",
  "badgeLabel": "상점 읽기",
  "accentKey": "shop-purple",
  "artTokenSet": "shop-ledger",
  "line": "상점은 런 전체 방향을 굳히는 투자 공간이다.",
  "body": "당장 급하지 않더라도, 지금 런의 운영 축을 오래 밀어 줄 유물을 먼저 본다.",
  "ctaLine": "보상과 상점은 같은 선택지가 아니다."
}
```

### UI/아트 메모
- reward와 shop은 구조를 재사용하되 accent가 같아 보이면 안 됨
- reward는 immediate, shop은 investment 느낌이 읽혀야 함

### QA pass 포인트
- reward / shop 카피 목적이 섞이지 않음
- `reward.first`와 `shop.first`는 같은 컴포넌트를 써도 서로 다른 accentKey를 가짐

---

## 3.5 상태 H — ending primer

### victory helper payload 예시

```json
{
  "visible": true,
  "surface": "ending",
  "contentKey": "ending.victory",
  "badgeLabel": "결과 읽기",
  "accentKey": "ending-gold",
  "artTokenSet": "ending-seal",
  "line": "이 화면은 이번 책의 결과와 다음 선택 의미를 정리해 준다.",
  "body": "여기서는 무엇을 얻었는지와 버튼이 무엇을 뜻하는지만 읽으면 된다.",
  "ctaLine": "다음 권으로 넘어가는 장면과 flavor는 이어지는 화면에서 맡는다."
}
```

### overrun CTA helper payload 예시

```json
{
  "visible": true,
  "surface": "ending",
  "contentKey": "ending.overrun-cta",
  "badgeLabel": "다음 단계 안내",
  "accentKey": "ending-blue",
  "artTokenSet": "overrun-arrow",
  "line": "오버런 계속은 이번 책 다음 세그먼트로 이어진다는 뜻이다.",
  "body": "전리품을 실제로 들고 넘어가는 장면과 맛은 다음 화면에서 확인한다.",
  "ctaLine": "이 화면은 장면 설명이 아니라 버튼 의미 설명까지만 맡는다."
}
```

### 가장 중요한 경계
- ending primer는 절대 slime/goblin/lich/golem/collapse/none flavor를 먹지 않는다.
- hero-card처럼 커지지 않는다.
- `overrunEvent`의 장면성을 미리 소비하지 않는다.

---

# 4. 예시 상태 ↔ smoke / acceptance 매핑표

## 4.1 overrunEvent

| 예시 상태 | smoke / acceptance 대응 | 확인할 핵심 |
| --- | --- | --- |
| 상태 A theme.goblin.cache | S1, S3, A1, A2 | theme branch key / CTA / reward card |
| 상태 B enemy.golem.fragment | S2, A2 | enemy branch key / 배지 / stone accent |
| 상태 C fallback.none | S4, S8, S9, R1, R2 | none fallback / missing snapshot recovery / invalid reward recovery |
| resolved reload | S10, R3 | confirm 1회성 유지 |
| copy 누락 복구 | S11, R5, R6 | 기본 카피 재주입 / density 유지 |
| visual density sanity | S12 | 카드 1장 + CTA 1개 + footnote 1줄 |

## 4.2 onboarding

| 예시 상태 | acceptance 대응 | 확인할 핵심 |
| --- | --- | --- |
| 상태 D library.book-select | O1, O2 | 첫 진입 노출 / 선택 방해 없음 |
| 상태 E battle.entry | O3 | 한 턴=한 주문 안내 |
| 상태 F battle.first-spell | O4 | 잠금/턴 종료 의미 분리 |
| 상태 G reward.first | O5 | 단기 보정 의미 |
| 상태 G shop.first | O6 | 장기 투자 의미 |
| 상태 H ending.victory | O7 | 결과/버튼 의미까지만 설명 |
| 상태 H ending.overrun-cta | O8 | overrun flavor 미소비 |

> 참고: `O1~O8`은 실제 파일의 acceptance ID와 1:1 고정명일 필요는 없지만, library / battle / reward / shop / ending의 exact key pass/fail을 분리해서 읽어야 한다.

---

# 5. 구현자에게 바로 넘길 체크리스트

## 5.1 프로그래머 체크리스트
- [ ] `overrunEvent` snapshot이 상태 A/B/C 수준의 밀도로 생성된다.
- [ ] onboarding helper payload가 상태 D~H 수준의 밀도로 계산된다.
- [ ] render는 payload 소비자로 남고 branch 재판정을 하지 않는다.
- [ ] `none` / invalid / missing snapshot에도 카드 밀도가 유지된다.
- [ ] ending primer가 overrun flavor를 먹지 않는다.

## 5.2 UI 체크리스트
- [ ] `overrunEvent`는 언제나 중앙 카드 1장 구조다.
- [ ] onboarding은 hero/inline/badge layer이지 scene card가 아니다.
- [ ] reward와 shop은 같은 컴포넌트를 써도 purpose tone이 다르게 읽힌다.
- [ ] ending primer는 얇고, overrunEvent가 장면을 먹는다.

## 5.3 아트 체크리스트
- [ ] `artTokenSet` 변경만으로 branch 차이 대부분을 해결한다.
- [ ] neutral/fallback도 빈 상태처럼 보이지 않는다.
- [ ] overrunEvent와 onboarding의 레이어 성격을 혼동하지 않는다.

## 5.4 QA 체크리스트
- [ ] key / phase / CTA semantics / density / fallback을 우선 검증한다.
- [ ] exact wording 일부보다 `contentKey`와 surface 역할이 우선이다.
- [ ] ending과 overrunEvent의 역할 침범이 없는지 separately 본다.

---

# 6. 리스크 & 후속 과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| 문서가 충분해도 샘플이 흩어져 구현자가 다시 payload를 조합할 위험 | 문서 해석 시간이 다시 길어진다 | 이 문서의 상태 A~H를 canonical example로 사용한다 |
| UI가 branch 차이를 과하게 구조 차이로 번역할 위험 | 구현량과 드리프트가 늘어난다 | 차이는 기본적으로 `contentKey` / `accentKey` / `artTokenSet` 교체로 해결한다 |
| QA가 카피 완전일치에만 매달릴 위험 | fallback/phase 버그를 놓친다 | key / CTA semantics / density를 먼저 본다 |
| ending primer가 overrun flavor를 다시 먹을 위험 | shared surface 경계가 무너진다 | 상태 H를 boundary packet의 예시 기준으로 고정한다 |

---

# 7. 한 줄 결론

> 지금 DiceSpell에 부족한 것은 새 방향이 아니라, **이미 닫힌 문서들을 같은 예시 상태로 보는 마지막 교정본**이다.
> 다음 구현자는 이 패킷의 상태 A~H를 기준으로 snapshot, helper payload, DOM, smoke 기대값을 같은 그림으로 맞추면 된다.

---

## 함께 읽을 문서
- `./dicespell_overrun_onboarding_cutover_master_plan_kr.md`
- `./dicespell_overrun_event_content_deck_kr.md`
- `./dicespell_overrun_event_smoke_migration_packet_kr.md`
- `./dicespell_ending_overrun_boundary_packet_kr.md`
- `./dicespell_first_run_onboarding_content_deck_kr.md`
- `./dicespell_first_run_onboarding_acceptance_matrix_kr.md`
