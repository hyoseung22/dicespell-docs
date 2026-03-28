# DiceSpell `Run Table` Atlas Route Node semantic anchor summary

## 3줄 요약
- 이 문서는 `atlas-route-node` 첫 live edit 직전 마지막 작은 공백이던 `정확히 어느 함수 / DOM / capture 질문을 같은 책임으로 다시 찾아야 하지?`를 3~5분 안에 읽게 만드는 readable companion이다.
- 핵심은 새 route node 설계를 더하는 것이 아니라, `renderRouteBoard()` / `run-side-panel` / `run-route-node` / `renderRunPressureCards(summary)` / `renderUpperSectionPanel(summary)` / forecast·preview·log zone을 **atlas semantic zone**으로 다시 묶는 것이다.
- 목표는 implementer / UI / QA / PM이 line drift가 있어도 `root -> route node -> threshold -> boundary -> capture question` 순서로 같은 stage 언어를 재사용하게 만드는 것이다.

**한 줄 목표:** atlas 첫 live turn에서 semantic anchor 재확인을 감각적인 grep 대신 `atlas-only locator checklist`로 고정한다.

---

## first screen

### 이 문서가 답하는 질문
- atlas 첫 edit 전에 무엇을 semantic anchor라고 부르지?
- line number가 조금 밀려도 무엇까지만 찾고 멈춰야 하지?
- 왜 archive / file map가 있어도 semantic anchor packet이 하나 더 필요하지?

### 가장 짧은 답
- atlas semantic anchor는 `surface root`, `route node state`, `threshold pressure`, `forecast boundary`, `overview / threshold-focus evidence` 다섯 묶음이다.
- 프로그래머는 `renderRouteBoard()`, `run-side-panel`, `renderRunPressureCards(summary)`, `renderUpperSectionPanel(summary)` 주변까지만 다시 찾는다.
- UI / QA는 `Run Atlas -> 페이지 보드 -> route board -> forecast -> pressure -> preview/log` 위계와 `thresholdLabel / pointsLeft / progressLabel` 문맥만 본다.
- battle/oath surface, preview/log 재배치, upper bonus overlay 확장은 여전히 atlas 바깥이다.

---

## 왜 이 문서가 필요한가
- `atlas live kickoff`는 semantic anchor 재확인을 요구하지만, 무엇이 atlas semantic anchor인지 stage 언어로 다시 한 장에 고정하진 않았다.
- `atlas archive file map`은 어느 경로에 쓸지 잠갔지만, 실제 live edit 직전에 어떤 locator를 다시 찾아야 하는지는 따로 묶여 있지 않았다.
- 그래서 실제 턴에서는 route node를 보다가 preview/log, threshold pressure를 보다가 upper bonus draft, atlas를 보다가 battle/oath까지 같이 열 위험이 남아 있었다.

즉 이 summary는 **locator를 찾는 기준을 다시 좁혀 atlas가 atlas-only로 남게 만드는 요약 보드**다.

---

## core checklist

| anchor family | 다시 찾을 locator | 이번 stage 질문 | 이번 stage에서 열면 안 되는 것 |
| --- | --- | --- | --- |
| Surface root | `run-side-panel`, `Run Atlas`, `renderRouteBoard()` | atlas stage가 실제로 어디서 시작하지? | battle/oath root 선추가 |
| Route node | `.run-route-board`, `.run-route-node`, `pageTypeMeta(page.type)` | current / past / shop / boss 읽힘이 어디서 잠기지? | preview list를 route node 대체 증거로 쓰는 것 |
| Threshold pressure | `renderRunPressureCards(summary)`, `renderUpperSectionPanel(summary)`, `thresholdLabel`, `pointsLeft`, `progressLabel` | threshold가 atlas 문맥으로 어떻게 읽히지? | upper bonus overlay, battle draft 확장 |
| Forecast boundary | `.run-side-summary`, `.forecast-item`, `.run-peek-panel`, `.log-panel` | forecast는 보조선이고 preview/log는 비범위라는 경계를 어떻게 적지? | preview/log 재배치 |
| Evidence | `atlas-1440-overview.png`, `atlas-1440-threshold-focus.png`, `density-review.md` | 어떤 두 질문으로 candidate-ready를 증명하지? | battle/oath capture 혼입 |

---

## role-specific handoff points

### 프로그래머
- note는 `root / route node / threshold / boundary / non-change` 다섯 줄이면 충분하다.
- `renderRouteBoard()`와 threshold pressure zone까지만 찾고 멈춘다.
- preview/log helper 정리나 battle/oath surface 예열은 금지다.

### UI
- `Run Atlas -> 페이지 보드 -> route board -> forecast -> pressure -> preview/log` 위계를 먼저 적는다.
- 판단 기준은 예쁨이 아니라 route node first-glance와 threshold focus다.
- preview/log polish는 이번 stage 바깥이다.

### 아트
- Route Node Starter / route node tone / threshold contrast까지만 본다.
- token sanity는 `충분 / 약함 / 과함` verdict로 끝내는 편이 맞다.
- battle/oath tone 정렬 논의는 다음 단계다.

### QA
- overview / threshold-focus 캡처는 둘 다 필요하다.
- acceptance note에는 `atlas-only boundary 유지` 문장이 빠지면 안 된다.
- generic glamour shot은 review-ready 근거가 아니다.

### PM
- semantic anchor 재확인은 kickoff layer다.
- green / battle unlock은 여전히 evidence bundle 뒤에만 쓴다.
- `Run Table 진행`, `atlas도 하고 battle도 준비` 같은 큰 문장은 계속 금지다.

---

## stop / go

### GO
- programmer note가 atlas root / route node / threshold pressure만 다룬다.
- UI / QA가 overview / threshold-focus 두 질문을 atlas-only로 적는다.
- PM boundary note가 `battle-stakes-plaque unlock 대기`를 유지한다.

### STOP
- preview/log 재배치가 route node edit보다 먼저 열린다.
- threshold note가 upper bonus overlay나 battle draft까지 커진다.
- battle/oath locator까지 같이 찾기 시작한다.
- capture가 atlas semantic question 없이 generic shot이 된다.

---

## immediate next actions
1. `source-of-truth map -> gap ledger -> atlas live kickoff -> atlas archive file map -> atlas semantic anchor packet` 순서로 읽는다.
2. programmer는 `hook-manifest.md`에 다섯 줄 anchor를 먼저 적는다.
3. UI는 `density-review.md`에 overview / threshold-focus 질문을 먼저 적는다.
4. 그 뒤에만 atlas-only edit를 시작한다.
5. `archive-ready candidate / green / battle unlock`은 여전히 evidence bundle로만 닫는다.
