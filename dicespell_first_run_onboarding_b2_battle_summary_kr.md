# DiceSpell 첫 런 온보딩 B-2 Battle Primer 요약

## 3줄 요약
- B-2는 온보딩 전체가 아니라 **첫 전투 primer 3면(hero 1 + inline 2)**을 실제 구현 가능한 commit 범위로 줄인 문서다.
- 핵심은 전투 규칙을 바꾸는 게 아니라 `renderBattle()` 안에서 `가능한 족보 -> 주문 1회 -> 로그/잠금 결과 읽기` 순서를 먼저 보이게 만드는 것이다.
- 저장 구조는 `seenBattlePrimer`, `seenFirstSpellHint`, `seenAutoPassHint` 3필드면 충분하고, primer가 보여도 전투 조작은 절대 막히면 안 된다.

**한 줄 목표:** 첫 전투 1~2턴 안에 플레이어가 `이번 턴에 무엇을 보고 무엇을 확인해야 하는가`를 감으로 읽게 만든다.

## 왜 이 문서가 필요한가
- 기존 온보딩 문서는 방향과 화면 위치, exact content를 충분히 닫았지만, 실제 구현 직전 `B-2 battle 첫 커밋에서 어디까지가 범위인가`는 별도로 좁혀 둘 필요가 있었다.
- 이 요약 문서는 프로그래머/UI/아트/QA가 B-2 범위를 3~5분 안에 맞춰 읽도록 만든 readable companion이다.

## 이 문서를 먼저 봐야 하는 사람
- **프로그래머:** `renderBattle(summary)`와 cast / auto-pass 훅만 우선 건드릴 계획일 때
- **UI/아트:** primer를 `새 전투 화면`이 아니라 `compact hero-primer 1장 + inline hint 2종`으로 제한해야 할 때
- **QA:** 첫 전투 진입, 첫 주문, 첫 자동 종료의 1회 노출과 비차단성만 빠르게 검수해야 할 때

---

## 이번 B-2에서 실제로 닫는 것

### 1) 화면
- `.battle-subcopy` 아래에 full-width compact hero-primer 1장 추가
- 첫 주문 직후 `battle.first-spell` inline hint 1종
- 첫 auto-pass 직후 `battle.auto-pass` inline hint 1종

### 2) 상태
- `meta.uiGuidance.seenBattlePrimer`
- `meta.uiGuidance.seenFirstSpellHint`
- `meta.uiGuidance.seenAutoPassHint`
- 저장 누락 시 허용 범위는 `primer/hint 재노출`까지
- 전투 진행과 입력은 어떤 경우에도 막지 않음

### 3) exact content
- `battle.entry`
  - title: `이번 턴은 한 번의 주문으로 끝납니다`
  - line 1: `먼저 가능한 족보를 보고, 이번 턴에 쓸 주문 하나를 고르세요.`
  - line 2: `결과는 로그와 잠금 표시에서 바로 확인할 수 있습니다.`
- `battle.first-spell`
  - badge: `첫 주문`
  - line 1: `방금 쓴 족보는 이번 페이지 동안 잠길 수 있습니다.`
  - line 2: `피해, 상태이상, 잠금 결과는 로그에서 바로 읽을 수 있습니다.`
- `battle.auto-pass`
  - badge: `자동 종료`
  - line 1: `지금은 쓸 수 있는 주문이 없어 몬스터 턴으로 넘어갔습니다.`
  - line 2: `다음 턴에는 가능한 족보부터 다시 확인하세요.`

---

## 역할별 handoff

### 프로그래머
- B-2에서는 battle surface와 사건 hook만 건드린다.
- reward/shop/ending primer를 같이 열지 않는다.
- 전투 규칙/수치/로그 시스템 자체는 바꾸지 않는다.

### UI
- hero-primer는 hand grid를 밀어내지 않는 compact card여야 한다.
- inline hint는 hero-primer보다 가벼워야 한다.
- warning modal, floating tooltip, 중앙 토스트는 금지다.

### 아트
- `primer-battle-entry` 1종, `primer-battle-inline` 1종이면 충분하다.
- 상태이상별 상세 튜토리얼 아이콘 체계는 만들지 않는다.

### QA
- 첫 battle 진입 hero-primer 1회
- 첫 주문 inline hint 1회
- 첫 auto-pass inline hint 1회
- primer가 있어도 roll / lock / select-hand / target 선택 가능
- hero-primer와 inline hint 과밀 중첩 없음

---

## 완료선

B-2는 아래가 모두 참이면 닫힌다.

1. 첫 battle 진입에서 `battle.entry` hero-primer가 보인다.
2. 첫 주문 후 `battle.first-spell`이 1회만 보인다.
3. 첫 auto-pass 후 `battle.auto-pass`가 1회만 보인다.
4. primer/hint가 있어도 전투 조작이 막히지 않는다.
5. 이 커밋 안에 reward/shop/ending 온보딩이 섞이지 않는다.

---

## 리스크
- 가장 큰 리스크는 `이왕 battle 여는 김에 reward도 같이` 식으로 범위가 다시 커지는 것.
- 두 번째 리스크는 hero-primer와 inline hint가 같은 무게로 겹쳐 전투 화면이 과밀해지는 것.
- 세 번째 리스크는 cast/auto-pass 훅이 느슨해 힌트가 두 번 뜨거나 잘못된 시점에 뜨는 것이다.

## 바로 다음 행동
- overrun A-track 실구현이 아직 최우선이면 B-2는 handoff-ready 상태로만 유지한다.
- 온보딩 B-track이 실제로 열리면, B-1 library 다음 커밋은 이 문서를 기준으로 battle primer 3면만 먼저 붙인다.
- 그 다음 commit-level 문서는 `B-3 reward/shop packet`으로 분리한다.
