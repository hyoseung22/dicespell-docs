# DiceSpell 첫 런 온보딩 B-2 Battle Primer 컷오버 패킷

## 빠른 안내

### 3줄 요약
- 이 문서는 첫 런 온보딩의 두 번째 실제 구현 묶음인 **B-2 battle primer**를 commit-level handoff로 좁힌다.
- 프로그래머는 `renderBattle(summary)`와 cast / auto-pass action hook, 그리고 `seenBattlePrimer` / `seenFirstSpellHint` / `seenAutoPassHint` 저장 경계를 먼저 보면 된다.
- UI/아트/QA는 `hero-primer 1장 + 사건 기반 inline hint 2종 + 전투 인터랙션 비차단`만 기억하면 된다.

**한 줄 목표:** 첫 전투에서 플레이어가 `가능한 족보 확인 -> 이번 턴 주문 1회 -> 로그/잠금 결과 읽기`를 한 화면 흐름으로 이해하게 만들되, 전투 규칙이나 템포는 바꾸지 않는다.

### 왜 이 문서가 필요한가
- 온보딩 문서 묶음은 이미 방향, screen packet, workorder, acceptance, cutsheet, content deck까지 충분히 닫혀 있다.
- 하지만 실제 구현 직전 기준으로는 여전히 `좋아, 그럼 B-2 battle 첫 커밋에서는 hero-primer와 사건 기반 hint를 어디까지 묶고, 무엇을 일부러 하지 않지?`가 한 장으로 닫혀 있지 않다.
- 이 문서는 그 공백만 메운다. 즉 **B-2 battle primer를 실제 파일/이벤트 훅/검수/owner handoff 단위로 좁히는 실행 패킷**이다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | 2. 현재 runtime anchor, 5. B-2 범위, 6. 상태 구조 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| UI | 7. 화면 구조, 8. battle 밀도 규칙 | `docs/dicespell_first_run_onboarding_screen_packet_kr.md` |
| 아트 | 8. accent/token 최소 패키지 | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| QA | 9. acceptance / recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |

---

## 1. 이 문서가 다루는 범위

### 포함
- `battle.entry` hero-primer의 실제 컷오버 범위
- `battle.first-spell`, `battle.auto-pass` inline hint 2종의 사건 훅 경계
- `seenBattlePrimer`, `seenFirstSpellHint`, `seenAutoPassHint` 저장/재노출 규칙
- `renderBattle(summary)` 기준 삽입 위치와 밀도 제한
- 첫 커밋에서 필요한 최소 수동/자동 검수선
- 프로그래머/UI/아트/QA handoff 경계

### 제외
- reward / shop / ending primer 구현
- 전투 규칙 변경, 전투 밸런스 조정, 새 로그 시스템
- tooltip 군집, 도움말 모달, 상세 규칙 사전
- 상단 합계/붕괴/상태이상 전체 튜토리얼
- `overrunEvent` 본편 실구현

즉 이 문서는 `첫 전투 전체 개편`이 아니라 **B-2 battle primer 3면(hero 1 + inline 2)**만 닫는다.

---

## 2. 현재 runtime anchor

현재 코드 기준 B-2가 닿는 실제 anchor는 아래 넷이다.

| 파일 | 현재 anchor | 이번 단계 역할 |
| --- | --- | --- |
| `dice-spell-standalone/src/app.js` | `renderBattle(summary)` | hero-primer slot + inline hint slot 소비 |
| `dice-spell-standalone/src/app.js` | `data-action="select-hand"`, `data-action="roll-dice"`, `data-action="toggle-lock"` 흐름 주변 battle render | primer가 보여도 전투 조작 비차단 유지 |
| `dice-spell-standalone/src/app.js` | cast 성공 후 render 재진입 지점 | `battle.first-spell` 1회 노출 트리거 |
| `dice-spell-standalone/src/app.js` 또는 동등 action dispatcher | 주문 불가 자동 턴 종료 처리 지점 | `battle.auto-pass` 1회 노출 트리거 |

현재 `renderBattle(summary)`는 이미 아래 구조를 갖고 있다.

1. 상단 상태/로그 요약
2. `.battle-subcopy`
3. 주사위/리롤/패널 정보
4. hand grid / 액션 패널
5. 전투 로그 / 적 패널 / 보조 패널

즉 B-2의 진짜 목적은 새 전투 화면을 만드는 것이 아니라,
**이미 있는 battle surface 안에서 `무엇을 먼저 보면 되는가`를 한 번만 강하게 정렬하고, 사건 직후에는 짧은 inline hint만 남기는 것**이다.

---

## 3. 왜 B-2가 별도 packet이어야 하는가

현재 온보딩 문서는 `library -> battle -> reward/shop -> ending` 전체 순서를 이미 고정했다.
그런데 실제 구현 착수 관점에서 battle은 library보다 훨씬 사고가 나기 쉽다.

1. `renderBattle()`만 바꾸면 끝나지 않고 **cast 성공** / **auto-pass 발생** 훅까지 닿는다.
2. hero-primer와 inline hint가 동시에 과밀하게 겹치면 실제 전투 집중을 해친다.
3. 첫 전투 안내인데도 `룰 전체 설명`로 비대해지기 쉬워 범위가 쉽게 커진다.
4. 전투 surface는 dice, target, hand grid, 로그가 이미 빽빽해서 primer 밀도 제한이 library보다 중요하다.

즉 B-2는 단순한 카피 배치가 아니라,
**전투 surface 밀도 규칙 + 사건 기반 1회 힌트 + 비차단 인터랙션을 같이 시험하는 첫 전투 commit**이다.

---

## 4. canonical source 묶음

| 질문 | source-of-truth |
| --- | --- |
| battle primer의 존재 이유와 범위 | `docs/dicespell_first_run_onboarding_handoff_kr.md` |
| 실제 삽입 위치와 화면 밀도 | `docs/dicespell_first_run_onboarding_screen_packet_kr.md` |
| 상태 저장 위치와 event hook 순서 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| exact 카피/배지/accent/token | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| 완료선과 recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |
| owner별 DoD | `docs/dicespell_first_run_onboarding_production_cutsheet_kr.md` |
| canonical payload 예시 | `docs/dicespell_overrun_onboarding_contract_examples_kr.md` |

이 B-2 packet은 위 문서를 대체하지 않는다.
대신 **B-2 battle 첫 커밋에 필요한 부분만 추려 commit boundary로 다시 묶는다.**

---

## 5. B-2의 한 줄 정의

> 첫 전투에서 `한 턴 = 한 주문`과 `결과는 로그/잠금에서 읽는다`를 hero-primer 1장으로 먼저 고정하고, 첫 주문/첫 자동 종료 직후에는 inline hint 2종으로만 후속 해석을 보조한다.

이 정의에서 벗어나면 B-2가 아니라 다른 작업이다.

---

## 6. 상태 구조와 helper 계약

## 6.1 최소 저장 필드

B-2에서 필요한 장기 저장 필드는 아래 셋이면 충분하다.

```json
{
  "uiGuidance": {
    "seenBattlePrimer": true,
    "seenFirstSpellHint": true,
    "seenAutoPassHint": true
  }
}
```

### 규칙
- 기본 위치는 `appState.meta` 하위다.
- 저장 누락 시 해석은 `hint가 한 번 더 뜰 수 있음`까지다.
- 세이브가 망가졌다고 취급하거나 전투 진행을 막아선 안 된다.

## 6.2 B-2 전용 helper 최소 shape

```js
getBattlePrimerState(summary, appState) => {
  hero: {
    visible: boolean,
    contentKey: "battle.entry",
    eyebrow: "첫 전투 가이드",
    title: "이번 턴은 한 번의 주문으로 끝납니다",
    lines: [
      "먼저 가능한 족보를 보고, 이번 턴에 쓸 주문 하나를 고르세요.",
      "결과는 로그와 잠금 표시에서 바로 확인할 수 있습니다."
    ],
    confirmLabel: "전투 시작",
    accentKey: "battle-blue",
    artTokenSet: "primer-battle-entry"
  },
  firstSpellHint: {
    visible: boolean,
    contentKey: "battle.first-spell",
    badgeLabel: "첫 주문",
    lines: [
      "방금 쓴 족보는 이번 페이지 동안 잠길 수 있습니다.",
      "피해, 상태이상, 잠금 결과는 로그에서 바로 읽을 수 있습니다."
    ],
    accentKey: "battle-amber",
    artTokenSet: "primer-battle-inline"
  },
  autoPassHint: {
    visible: boolean,
    contentKey: "battle.auto-pass",
    badgeLabel: "자동 종료",
    lines: [
      "지금은 쓸 수 있는 주문이 없어 몬스터 턴으로 넘어갔습니다.",
      "다음 턴에는 가능한 족보부터 다시 확인하세요."
    ],
    accentKey: "battle-amber",
    artTokenSet: "primer-battle-inline"
  }
}
```

### 규칙
- 첫 구현에서는 공용 `getPrimerState()` 안의 battle branch여도 좋고, battle 전용 helper여도 좋다.
- render는 이 payload를 소비만 하고, 문자열을 재조합하지 않는다.
- B-2에서는 `variant`, `priority`, `queue`, `dismissedThisRun` 같은 필드를 늘리지 않는다.
- `battle.first-spell`과 `battle.auto-pass`가 같은 렌더에서 동시에 보이는 상태는 허용하지 않는다.

---

## 7. 화면 구조 계약

## 7.1 hero-primer 삽입 위치

권장 DOM 순서는 아래다.

```text
[battle heading / top status]
[battle-subcopy]
[hero-primer]
[action panel / hand grid / dice area]
[log / side panels]
```

### 허용 대안
- `.battle-subcopy` 바로 아래 full-width compact card로 삽입
- hero-primer를 상단 패널 하단에 붙이되 hand grid 첫 줄을 스크롤 밖으로 밀지 않음

### 금지
- hero-primer를 modal로 띄우기
- hero-primer 때문에 주사위/족보 카드가 첫 스크린 밖으로 밀리기
- hero-primer 안에 상단 합계/붕괴/상태이상 장문 설명을 덧붙이기

## 7.2 inline hint 삽입 위치

B-2에서 inline hint는 아래 둘 중 하나에만 붙인다.

1. battle log 상단 보조 줄
2. hand / action panel 바로 위의 얇은 inline strip

### 허용 방식
- badge + 1~2문장 strip
- battle-amber accent 1종 재사용
- 기존 log 흐름을 덮지 않는 single-line or two-line density

### 금지 방식
- hero-primer와 같은 크기의 두 번째 카드
- auto-pass 직후 전투 인터랙션을 막는 confirm 버튼
- 첫 주문 힌트를 reward toast처럼 화면 중앙에 띄우기

## 7.3 battle surface 정보 우선순위

B-2에서 battle primer가 말할 수 있는 것은 아래 셋뿐이다.

1. 가능한 족보를 본다
2. 이번 턴 주문은 한 번이다
3. 결과는 로그/잠금에서 읽는다

### 일부러 하지 않을 것
- 상단 합계 시스템 전체 설명
- 붕괴의 장기 설계 철학 설명
- 상태이상 상성 도감
- 적 AI 패턴 튜토리얼

---

## 8. role-specific handoff

## 8.1 프로그래머

### deliverable
- `meta.uiGuidance.seenBattlePrimer` / `seenFirstSpellHint` / `seenAutoPassHint` 정규화
- `renderBattle(summary)` hero-primer slot 추가
- cast 성공 후 `battle.first-spell` 1회 노출 트리거 연결
- auto-pass 발생 후 `battle.auto-pass` 1회 노출 트리거 연결
- primer와 무관하게 roll / lock / select-hand / target 선택 가능 유지

### 일부러 하지 않을 것
- B-2에서 reward/shop/ending primer hook 열기
- 전투 규칙/잠금/MP 계산 로직 변경
- 새 battle tutorial modal이나 queue 시스템 도입

### 완료선
- 신규/기존 메타 상태 모두 battle 진입이 깨지지 않는다.
- 첫 전투에서는 hero-primer가 1회 뜨고, 첫 주문/첫 auto-pass는 각각 1회만 보인다.
- primer가 떠 있어도 주사위, 타깃, hand 카드 클릭은 그대로 동작한다.

## 8.2 UI

### deliverable
- full-width compact hero-primer 1종
- inline hint strip 1종(`battle.first-spell`, `battle.auto-pass` 공용 shell)
- battle-blue / battle-amber accent 2종

### 일부러 하지 않을 것
- 새 전투 shell 제작
- floating tooltip 군집
- battle panel 전체 재레이아웃

### 완료선
- 1280px 기준 첫 스크린에서 `.battle-subcopy`, hero-primer, hand grid 첫 줄이 함께 읽힌다.
- inline hint는 로그/패널을 보조할 정도로만 보이고, hero-primer와 같은 무게를 갖지 않는다.

## 8.3 아트

### deliverable
- `primer-battle-entry` 토큰 세트 1종
- `primer-battle-inline` 토큰 세트 1종
- `첫 전투 가이드`, `첫 주문`, `자동 종료` 배지 3종 또는 동등 토큰 가이드

### 일부러 하지 않을 것
- 상태이상별 개별 아이콘 세트 제작
- 적 종류별 전투 가이드 배너 제작
- 전투 튜토리얼 전용 일러스트 확장

### 완료선
- hero-primer는 안내로 읽히고, alert/warning 패널처럼 과하게 위협적으로 읽히지 않는다.
- inline hint는 사건 후속 읽힘 보조로만 작동하고 전투 실패 경고창처럼 보이지 않는다.

## 8.4 QA

### deliverable
- 첫 battle 진입 hero-primer 1회 노출 확인
- primer 표시 상태에서도 roll / select-hand / cast 진행 가능 확인
- 첫 주문 힌트 1회 노출 확인
- 첫 auto-pass 힌트 1회 노출 확인
- hero-primer와 inline hint가 같은 프레임에 과밀하게 중첩되지 않는지 확인
- 메타 저장 누락/초기화 시 recovery 확인

---

## 9. acceptance / recovery 요약

## 9.1 B2-A acceptance

| ID | 확인 항목 | pass 기준 |
| --- | --- | --- |
| B2-A1 | 첫 battle 진입 hero-primer | `battle.entry` hero-primer가 1회 보이고 전투 조작을 가리지 않음 |
| B2-A2 | hero density | `.battle-subcopy`, hero-primer, hand grid 첫 줄이 첫 스크린에서 함께 읽힘 |
| B2-A3 | 첫 주문 inline hint | 첫 cast 성공 직후 `battle.first-spell`이 1회만 보임 |
| B2-A4 | auto-pass inline hint | 자동 종료 직후 `battle.auto-pass`가 1회만 보임 |
| B2-A5 | interaction safety | primer 유무와 무관하게 roll / lock / target / select-hand가 계속 동작 |
| B2-A6 | no overlap | hero-primer와 inline hint가 같은 강조 밀도로 동시에 겹치지 않음 |
| B2-A7 | no rulescope drift | battle primer가 상단 합계/붕괴/속성 장문 설명으로 비대해지지 않음 |

## 9.2 recovery

| ID | 상황 | 기대 결과 |
| --- | --- | --- |
| B2-R1 | `uiGuidance` 누락 | primer가 다시 뜰 수는 있어도 전투 진행은 정상 |
| B2-R2 | 첫 주문 힌트 저장 누락 | `battle.first-spell` 재노출까지만 허용, 에러/soft-lock 없음 |
| B2-R3 | auto-pass 힌트 저장 누락 | `battle.auto-pass` 재노출까지만 허용, 턴 진행 정상 |
| B2-R4 | battle 진입 후 저장/재로드 | hero-primer 재노출 가능, 단 조작 막힘/중복 UI 폭주 없음 |

---

## 10. 리스크 & 후속과제

### 리스크 1. hero-primer와 inline hint를 한 커밋에 과도하게 섞어 전투 화면이 과밀해질 위험
- 영향: 첫 전투 읽힘을 돕기보다 실제 hand grid와 로그를 가릴 수 있다.
- 대응: hero는 1장, inline은 1줄 strip로만 제한하고, 같은 시점 중첩을 금지한다.

### 리스크 2. cast/auto-pass 훅이 느슨해 힌트가 두 번 뜨거나 엉뚱한 시점에 뜰 위험
- 영향: 온보딩이 설명이 아니라 노이즈가 된다.
- 대응: `seenFirstSpellHint`, `seenAutoPassHint`를 분리하고, 사건 직후 1회만 소비하는 쪽으로 고정한다.

### 리스크 3. battle primer가 전투 규칙 개편처럼 오해되어 범위가 커질 위험
- 영향: B-2가 UI 안내가 아니라 전투 시스템 수정 슬롯으로 부풀 수 있다.
- 대응: B-2는 설명 레이어만 다루고, 전투 규칙/수치/로그 시스템은 범위 밖으로 유지한다.

### 바로 다음 handoff
- A-track(`overrunEvent` A-1~A-4) 실구현이 아직 열려 있으면 B-2는 handoff-ready 상태로만 유지한다.
- 온보딩 B-track이 실제로 열릴 때는 B-1 library primer 다음 커밋으로 이 문서를 기준 삼는다.
- B-2 이후의 다음 문서는 필요 시 `B-3 reward/shop packet`으로 분리한다. ending은 끝까지 `ending ↔ overrunEvent` 경계 패킷을 따른다.

---

## 11. immediate next actions

1. tracker/blueprint에는 온보딩 문서 축의 새 공백이 `B-2 battle commit boundary`였고, 이번 문서로 그 공백이 닫혔음을 기록한다.
2. run log에는 이번 슬롯이 문서화 슬롯이었고, `renderBattle()` / cast / auto-pass 기준 B-2 handoff를 생산했다는 사실만 안전하게 append한다.
3. 다음 구현자는 A-track이 닫힌 뒤 B-track을 열 때, B-1 다음 커밋을 이 문서 기준으로 `battle.entry -> battle.first-spell -> battle.auto-pass` bounded surface로 연다.
