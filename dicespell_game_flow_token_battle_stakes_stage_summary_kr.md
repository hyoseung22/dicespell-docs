# DiceSpell `Run Table` Battle Stakes Plaque 첫 스테이지 요약

## 3줄 요약
- 이 문서는 `atlas-route-node green` 뒤 다음 실제 refinement 턴을 **`current / next / grimoire slot hook 잠금 + stakes first-glance 검수 + starter asset sanity + battle archive-ready candidate`**까지만 좁히는 readable companion이다.
- 핵심은 새 전투 화면을 더 설계하는 것이 아니라, `oath-banner`를 아직 열지 않은 채 battle stage 하나만 한 archive와 한 green 문장으로 닫게 만드는 것이다.
- PM / 기획 / UI / 아트 / QA가 3~5분 안에 `왜 battle이 두 번째 stage인지`, `무엇이 이번 턴 완료선인지`, `어떤 문장 전에는 oath를 열면 안 되는지`를 바로 이해해야 한다.

## 한 줄 목표
atlas 다음 refinement 턴을 `battle stakes only`로 고정해, queue와 ownership 문서가 실제 두 번째 stage 실행으로 이어지게 만든다.

## 왜 이 문서를 먼저 보나
atlas green 뒤 실제 battle 턴 직전엔 보통 이렇게 흐르기 쉽다.

- `battle이니까 action panel도 같이 보면 되지 않을까?`
- `overview 캡처만 있으면 거의 된 거 아닌가?`
- `starter asset sanity가 부족하니 이번에 combat shell 전체 얘기도 같이 열까?`

이 요약 문서는 그 drift를 막기 위해, battle stage를 **무엇을 하는 턴인지보다 무엇을 아직 하지 않는 턴인지** 기준으로 먼저 읽히게 만든다.

---

## 이번 stage의 가장 짧은 정의

### 이번에 닫는 것
- `Battle Table` root / `current | next | grimoire` slot hook
- `battle-1440-overview.png`
- `battle-1440-stakes-focus.png`
- `Stakes Plaque Starter` sanity note
- battle stage archive와 handoff line 초안

### 이번에 아직 안 닫는 것
- action panel / log / controls 전체 개편
- atlas route node 재보정
- oath banner / result / carry hierarchy
- reward/shop/ending side family 재논의
- Run Table 전체 green 문장

### 한 줄 판정
battle stage는 `current > next > grimoire stakes hierarchy가 먼저 읽히는가`를 닫는 턴이지, 전투 화면 전체를 다듬는 턴이 아니다.

---

## 누가 무엇을 보면 되나

| 역할 | 이번 문서에서 바로 가져갈 결론 |
| --- | --- |
| 프로그래머 | battle live edit은 `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"`까지만 잠근다 |
| UI | `overview`와 `stakes-focus` 두 캡처만으로 first-glance verdict를 남긴다 |
| 아트 | `Stakes Plaque Starter` sanity만 본다. battle HUD 전체 리뉴얼은 아직 금지다 |
| QA | required artifacts가 stage archive 안에 다 모였을 때만 `battle archive-ready candidate`를 쓴다 |
| PM/기획 | `battle-stakes-plaque green` 전에는 oath unlock을 쓰지 않는다 |

---

## 이번 stage evidence 묶음
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/manifest.md`
- `captures/battle-1440-overview.png`
- `captures/battle-1440-stakes-focus.png`
- `notes/hook-manifest.md`
- `notes/density-review.md`
- `notes/handoff-line.md`
- `checks/dom-sanity.md`
- `assets/starter-asset-list.md`

이 묶음이 stage archive 안에 같은 이름으로 있어야 QA가 후보 green을 말할 수 있다.

---

## stage signoff 한눈표

| 단계 | 누가 적나 | 의미 |
| --- | --- | --- |
| `bootstrap-ready` | 프로그래머 | battle 폴더 + manifest + starter file set이 생겼다 |
| `archive-in-progress` | 프로그래머 / UI | 첫 hook note 또는 첫 capture가 들어왔다 |
| `archive-ready candidate` | QA | battle required artifacts가 다 채워졌다 |
| `battle-stakes-plaque green` | PM/기획 | QA 확인까지 끝나 oath를 열 수 있다 |

---

## 리스크 & 후속과제
- battle stage가 다시 `stakes + action panel + log + rewards`로 커지면 둘째 큐부터 무너진다.
- overview/focus 캡처가 battle stage archive 밖 임시 경로에 있으면 green 후보로 보지 않는다.
- starter asset sanity를 combat shell 전체 리디자인 제안으로 확장하면 battle green이 불필요하게 늦어진다.
- `battle green`을 `Battle Table green`이나 `Run Table green`처럼 기록하면 oath unlock 규칙이 무너진다.

---

## immediate next actions
1. `token source-of-truth map -> token refinement queue -> atlas candidate review packet -> battle stage packet` 순서로 읽는다.
2. atlas green evidence를 먼저 확인한다.
3. battle stage archive를 먼저 만든다.
4. hook note / DOM sanity / overview/focus capture / starter asset sanity를 채운다.
5. QA가 `battle archive-ready candidate`를 적기 전에는 oath를 열지 않는다.
6. PM은 `battle-stakes-plaque green, oath-banner unlock` 문장만 남기고 그 다음에만 oath unlock을 연다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **`battle이 두 번째 stage라는 사실`을 `battle stakes만 닫는 실제 실행 규칙`으로 바꾸는 것**이다. 다음 Run Table refinement 턴은 이 stage packet 기준으로 `current / next / grimoire` 읽힘만 닫고, oath는 아직 대기 상태로 남겨야 한다.
