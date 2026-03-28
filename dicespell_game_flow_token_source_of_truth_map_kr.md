# DiceSpell `Run Table` Token Source-of-Truth Map

## 3줄 요약
- 이 문서는 `surface handoff packet` → `token anchor packet` → `token delivery workboard` → `token closing packet` → `token evidence manifest packet`까지 닫힌 뒤에도 남아 있던 마지막 읽기 병목인 **지금 코드 / 목표 토큰 계약 / 제출 증거를 어느 문서에서 읽어야 하는가**를 고정한다.
- 읽는 대상은 프로그래머, UI 오너, 아트 오너, QA/PM이다. 새 화면 제안이 아니라 `Run Atlas / Battle Table / Overrun Oath` 3면의 **baseline-vs-target 문서 스택**을 source-of-truth로 잠그는 문서다.
- source-of-truth는 이 문서, 빠른 브라우징용 companion은 `docs/dicespell_game_flow_token_source_of_truth_map_summary_kr.md`, 선행 계약은 `docs/dicespell_game_flow_token_evidence_manifest_kr.md`, `docs/dicespell_game_flow_token_closing_packet_kr.md`, `docs/dicespell_game_flow_token_delivery_workboard_kr.md`, `docs/dicespell_game_flow_token_anchor_packet_kr.md`다.

## 한 줄 목표
`route node / stakes plaque / oath banner` 후속 refinement가 다시 `문서 여러 장을 왔다 갔다 하며 감으로 범위를 넓히는 일`로 흐르지 않도록, **질문별로 어느 문서가 baseline이고 어느 문서가 target이며 어느 파일이 증거인지**를 한 장에 고정한다.

## 왜 이 문서가 필요한가
지금 Run Table 문서 묶음은 이미 충분히 강하다.

- `surface handoff`는 5개 surface family의 역할을 잠갔다.
- `token anchor`는 핵심 토큰 이름과 DOM/QA hook을 잠갔다.
- `delivery workboard`는 누가 어디부터 착수해야 하는지 잠갔다.
- `closing packet`은 언제 닫혔다고 말할 수 있는지 잠갔다.
- `evidence manifest`는 무엇을 어떤 파일명으로 제출해야 하는지 잠갔다.

하지만 실제 구현/리뷰 직전에는 아직 아래 공백이 남아 있다.

- 프로그래머는 `좋아, 그럼 지금 코드 baseline은 어디서 확인하고 target hook 계약은 어디서 읽지?`를 다시 조합해야 한다.
- UI 오너는 `prototype board`, `closing summary`, `evidence manifest`를 오가며 **무엇이 look reference이고 무엇이 delivery contract인지**를 다시 구분해야 한다.
- 아트 오너는 starter asset 범위를 이미 받았어도, **지금 열어도 되는 문서와 아직 보면 안 되는 문서**가 한 장에 모여 있지 않다.
- QA/PM은 리뷰 문장을 쓸 수 있지만, **baseline 설명 문서와 target 계약 문서와 제출 증거 문서가 같은 층위로 소비되면** `거의 완료`가 다시 생긴다.

즉 지금 남은 병목은 `무엇을 설계할까`가 아니라 **`어느 문서가 현재 상태를 설명하고, 어느 문서가 다음 handoff를 계약하며, 어느 경로가 실제 제출물인가`**다. 이 문서는 그 읽기 순서를 고정한다.

---

## 먼저 무엇을 읽어야 하는가

| 역할 | 먼저 볼 문서 | 그 다음 | 아직 열지 말 문서 | 이 문서에서 특히 볼 섹션 |
| --- | --- | --- | --- | --- |
| 프로그래머 | 이 문서 | 2, 3, 4 → 해당 source packet | summary만 보고 범위 선언하기 | 2, 4, 6, 8 |
| UI 오너 | summary | 이 문서 → prototype board → closing/evidence | code grep으로 역할 재정의하기 | 3, 5, 7, 8 |
| 아트 오너 | summary | 이 문서 → anchor/closing/evidence | 큰 배경 발주 문맥으로 확대하기 | 3, 5, 7 |
| QA / PM | summary | 이 문서 → closing/evidence | baseline 설명만으로 pass/fail 정하기 | 4, 6, 8, 9 |

---

## 1. 범위 선언

### 이번 문서가 다루는 것
- `Run Atlas` / `Battle Table` / `Overrun Oath` 3면의 baseline 문서와 target 문서 역할 분리
- `dice-spell-standalone/src/app.js`, `src/styles.css`, `docs/prototypes/dicespell_game_flow_redesign_board.html` 사이 읽기 순서
- source packet vs readable summary vs evidence archive 역할 정렬
- owner별 `지금 봐야 하는 문서`, `아직 대기해야 하는 문서`, `review 때 필요한 문서`

### 이번 문서가 의도적으로 다루지 않는 것
- 새 토큰 설계
- 새 phase 정의
- `overrunEvent` / 온보딩 문서 묶음 대체
- 실제 capture/artifact를 지금 생성하는 일
- 큰 리팩터링 범위 정의

### 이번 단계의 핵심 판단
이제 Run Table의 제일 큰 리스크는 `계약이 없다`가 아니라 **계약이 많아져서 baseline 설명, target packet, summary, evidence 문서가 같은 층위로 소비되는 것**이다. 따라서 다음 가치가 큰 문서는 새 디자인안이 아니라 **질문별 source-of-truth 정렬표**다.

---

## 2. Core structure: 문서를 4층으로 나눠 읽는다

| 층위 | 역할 | 무엇이 여기에 속하는가 | 잘못 읽을 때 생기는 문제 |
| --- | --- | --- | --- |
| Layer A. `Current baseline` | 지금 live 코드가 어디에 있는지 설명 | `dice-spell-standalone/src/app.js`, `dice-spell-standalone/src/styles.css` | target packet을 보지 않고 감으로 수정하거나, 반대로 현재 구현을 과소/과대 해석한다 |
| Layer B. `Target contract` | 다음 refinement에서 무엇을 잠가야 하는지 계약 | `surface handoff`, `token anchor`, `delivery workboard`, `closing packet`, `evidence manifest`, 이 문서 | summary만으로 착수하거나 evidence만 보고 구현 범위를 넓힌다 |
| Layer C. `Readable companion` | non-programmer가 3~5분 안에 이해하도록 돕는 문서 | 각 `*_summary_kr.md`, prototype board | summary가 source packet처럼 소비돼 종료선이 약해진다 |
| Layer D. `Evidence archive` | 실제 closing 증거가 들어갈 경로 | `docs/artifacts/run-table-token-delivery/<stage>/...` | 문장은 있는데 파일이 흩어져 있어 다시 DM/임시 폴더로 흐른다 |

### 핵심 원칙
- `현재 코드 설명`과 `다음 target 계약`은 같은 문서가 아니다.
- `summary`는 source packet의 대체물이 아니다.
- `prototype board`는 참조판이지 closing 증거가 아니다.
- `archive path`는 구현 문서가 아니라 제출 문서다.

---

## 3. 질문별 source-of-truth 정렬표

| 질문 | 먼저 볼 것 | 그 다음 볼 것 | 마지막 확인 | 이 질문에서 금지 |
| --- | --- | --- | --- | --- |
| 지금 route/stakes/oath live anchor가 어디 있지? | `dice-spell-standalone/src/app.js`, `src/styles.css` | 이 문서 4장 | `token anchor packet` | summary만 보고 line/semantic anchor를 추정하기 |
| 다음에 어떤 토큰/QA hook을 잠가야 하지? | `token anchor packet` | `delivery workboard` | `closing packet` | prototype board를 계약 문서처럼 읽기 |
| 누가 어떤 순서로 착수하지? | `delivery workboard` | 이 문서 6장 | `closing packet` | evidence manifest를 구현 순서 문서로 읽기 |
| 언제 닫혔다고 말하지? | `closing packet` | `evidence manifest` | 이 문서 8장 | 캡처 한 장만 보고 pass 판단하기 |
| 어떤 파일을 어떤 이름으로 남기지? | `evidence manifest` | `docs/artifacts/run-table-token-delivery/...` | `handoff-line.md` | summary에 파일명 규칙을 기대하기 |
| UI가 무엇을 먼저 보이게 해야 하지? | summary → prototype board | `token anchor packet`, `closing packet` | stage capture review | styles class만 보고 hierarchy를 판단하기 |
| starter asset은 어디까지가 이번 턴 범위지? | `token anchor packet` 5장 | `closing packet` 4장 | `evidence manifest` 3장 | 큰 배경/전체 shell 발주를 starter로 확대하기 |
| tracker/run log에는 무슨 문장을 남기지? | `closing packet` | `evidence manifest` | stage별 `handoff-line.md` | `거의`, `대체로`, `보기 좋음` 같은 감상형 문장 |

---

## 4. Surface별 baseline-vs-target 맵

## 4-1. `Run Atlas` / route node

### Current baseline
| 구분 | 현재 기준 |
| --- | --- |
| live function | `dice-spell-standalone/src/app.js:1512` `renderRouteBoard(pagePlan, currentPage, options = {})` |
| live markup | `.run-route-board`, `.run-route-node`, `meta.className`, `is-current`, `is-past` 조합 |
| live style | `dice-spell-standalone/src/styles.css:2396` `.run-route-board`, `:2402` `.run-route-node` |
| reference board | `docs/prototypes/dicespell_game_flow_redesign_board.html` |

### Target contract
- `token anchor packet`: `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold`
- `delivery workboard`: `renderRouteBoard()`와 `renderRightPanel()`을 같은 route vocabulary로 잠그기
- `closing packet`: `current -> next threshold -> route 흐름` 1440p 읽힘과 starter asset 종료선
- `evidence manifest`: `docs/artifacts/run-table-token-delivery/atlas-route-node/`

### 이 surface에서 흔한 오독
- `meta.className + is-current` 조합이 이미 state contract라고 착각하는 것
- `preview/log panel`을 같이 만지며 atlas token refinement 범위를 넓히는 것
- `boss/shop` 차이를 카피에만 맡기고 visual token을 후순위로 미루는 것

---

## 4-2. `Battle Table` / stakes plaque

### Current baseline
| 구분 | 현재 기준 |
| --- | --- |
| live function | `dice-spell-standalone/src/app.js:2140` `renderBattle(summary)` |
| live markup | `battle-stakes-grid`, `battle-stakes-card`, 첫 카드만 `.current` |
| live style | `dice-spell-standalone/src/styles.css:2336` `.battle-stakes-card`, `:2530` `.battle-stakes-grid` |
| reference board | `docs/prototypes/dicespell_game_flow_redesign_board.html` |

### Target contract
- `token anchor packet`: `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"`
- `delivery workboard`: `current / next / grimoire`를 slot language로 잠그기
- `closing packet`: stakes 3카드가 action panel을 가리지 않는 1440p hierarchy 고정
- `evidence manifest`: `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/`

### 이 surface에서 흔한 오독
- 현재 `.current` 클래스 하나만 있으니 이미 slot 구분이 충분하다고 보는 것
- stakes를 `정보 카드 3장`으로 읽어 header extension이라는 목적을 놓치는 것
- battle 조작 패널까지 같이 손봐 범위를 부풀리는 것

---

## 4-3. `Overrun Oath` / oath banner

### Current baseline
| 구분 | 현재 기준 |
| --- | --- |
| live function | `dice-spell-standalone/src/app.js:2435` `renderEnding(summary)` |
| live markup | `.ending-banner`, `.ending-banner-pill`, `.ending-main-column`, `.ending-side-column` |
| live style | `dice-spell-standalone/src/styles.css:2564` `.ending-banner`, `:2577` `.ending-banner-pill` |
| reference board | `docs/prototypes/dicespell_game_flow_redesign_board.html` |

### Target contract
- `token anchor packet`: `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta`
- `delivery workboard`: `banner / result / carry` 3층 역할을 owner 언어로 잠그기
- `closing packet`: main banner-first hierarchy와 starter asset 종료선 고정
- `evidence manifest`: `docs/artifacts/run-table-token-delivery/oath-banner/`

### 이 surface에서 흔한 오독
- `ending-banner`가 있으니 이미 banner 역할이 다 잠겼다고 보는 것
- ending side column을 reward/shop side card family의 연장선으로 읽는 것
- `overrunEvent` 경계 문서를 끌어와 Run Table token refinement 범위를 넓히는 것

---

## 5. Role-specific handoff points

## 5-1. 프로그래머에게 넘길 것

### 읽기 순서
1. 이 문서 2장과 4장으로 `current baseline`과 `target contract`를 분리한다.
2. `token anchor packet`에서 hook 이름을 본다.
3. `delivery workboard`에서 함수 범위를 본다.
4. `closing packet`으로 완료선만 확인한다.
5. 파일명/증거 경로가 필요할 때만 `evidence manifest`를 연다.

### 바로 써야 하는 문장
- `이번 refinement의 baseline은 app.js/styles.css live anchor이고, target은 token anchor/delivery/closing/evidence stack이다.`
- `summary나 prototype board는 범위 선언 근거가 아니라 companion/reference다.`

### 금지
- summary만 보고 hook naming을 정하기
- evidence manifest를 구현 순서 문서처럼 읽고 archive부터 기준으로 범위를 넓히기
- atlas/battle/oath 외 surface까지 같이 grep해 이번 턴 범위를 키우기

## 5-2. UI 오너에게 넘길 것

### 읽기 순서
1. summary로 3분 요약을 본다.
2. 이 문서 3장과 4장으로 각 surface의 current-vs-target 차이를 본다.
3. prototype board로 mood/reference를 확인한다.
4. `closing packet`으로 density 종료선을 확인한다.
5. `evidence manifest`로 capture 파일명만 확인한다.

### 바로 써야 하는 문장
- `prototype은 시각 참조이고, closing packet과 evidence manifest가 실제 제출 계약이다.`
- `overview/focus capture가 없으면 리뷰는 종료되지 않는다.`

### 금지
- prototype board를 final spec처럼 읽기
- capture naming 없이 `1.png`, `new.png`처럼 저장하기
- summary 문장만으로 density pass/fail을 내리기

## 5-3. 아트 오너에게 넘길 것

### 읽기 순서
1. summary
2. 이 문서 3장과 4장
3. `token anchor packet`의 starter asset 범위
4. `closing packet`의 starter asset 종료선
5. `evidence manifest`의 `assets/starter-asset-list.md`

### 바로 써야 하는 문장
- `이번 턴 제출물은 starter asset이고, 큰 배경/전체 shell은 아직 금지다.`
- `surface family를 섞지 않는 것이 예쁘게 만드는 것보다 먼저다.`

### 금지
- atlas/battle/oath 공용 하나의 frame family로 묶기
- reward/shop shell을 축소 복붙해 oath나 stakes를 닫았다고 보기
- evidence path 없이 아트만 DM/채팅 첨부로 넘기기

## 5-4. QA / PM에게 넘길 것

### 읽기 순서
1. summary
2. 이 문서 2장, 3장, 8장
3. `closing packet`
4. `evidence manifest`
5. stage별 `handoff-line.md`

### 바로 써야 하는 문장
- `baseline 설명 문서와 target 계약 문서를 구분해 읽고, pass/fail은 closing packet 기준으로만 남긴다.`
- `prototype board와 summary는 리뷰 진입 도우미이지 종료선 문서가 아니다.`

### 금지
- summary만으로 tracker/run log 문장을 쓰기
- capture-only 제출을 closing으로 간주하기
- stage archive 경로 없이 `토큰 정리됨` 같은 큰 문장을 남기기

---

## 6. 문서 스택별 사용 타이밍

| 타이밍 | 꼭 볼 문서 | 보조 문서 | 아직 열지 말 문서 |
| --- | --- | --- | --- |
| 착수 5분 전 | 이 문서, `token anchor packet`, `delivery workboard` | 해당 summary | evidence manifest를 구현 순서 문서처럼 상세히 파고들기 |
| 시각 리뷰 직전 | `closing packet` | prototype board, summary | unrelated surface packet |
| 제출물 정리 직전 | `evidence manifest` | 이 문서 8장 | 새 source packet 추가 작성 |
| tracker/run log 작성 직전 | `closing packet`, `evidence manifest`, stage `handoff-line.md` | summary | prototype board, baseline grep 메모 |

### 타이밍 규칙
- **착수 전**: baseline-vs-target을 분리한다.
- **리뷰 전**: closing 기준으로만 본다.
- **제출 전**: evidence path와 파일명만 본다.
- **기록 전**: `handoff-line.md`와 closing language만 복붙한다.

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 문서 기준 대응 |
| --- | --- | --- |
| baseline 설명 문서와 target packet이 같은 층위로 소비되면 implementer가 `지금 있는 class`를 계약으로 오인한다 | hook/state/role이 다시 약해지고 범위가 넓어진다 | 질문별 source-of-truth 표와 surface별 baseline-vs-target 맵을 먼저 고정한다 |
| summary가 source packet처럼 소비되면 QA/PM 기록이 다시 감상형으로 흐른다 | `거의 완료` 같은 큰 문장이 되살아난다 | summary는 companion, closing/evidence는 계약 문서라고 역할을 분리한다 |
| prototype board가 계약서처럼 읽히면 UI/아트가 token refinement 대신 큰 비주얼 논의로 퍼진다 | bounded refinement가 mood board 단계로 되돌아간다 | prototype은 ref, closing/evidence는 delivery contract라고 반복 고정한다 |
| evidence manifest가 있어도 읽기 순서가 없으면 archive를 먼저 만들다 범위가 커진다 | 제출물은 생겨도 구현 범위가 흔들린다 | 착수/리뷰/제출/기록 타이밍별 문서 사용 순서를 별도 표로 고정한다 |

---

## 8. Immediate next actions

1. `surface handoff -> token anchor -> delivery workboard -> closing packet -> evidence manifest -> source-of-truth map`을 Run Table 문서 스택으로 고정한다.
2. 프로그래머는 이 문서 4장의 baseline-vs-target 표를 기준으로 `renderRouteBoard()` / `renderBattle()` / `renderEnding()` 착수 범위를 먼저 선언한다.
3. UI 오너는 summary와 prototype board를 companion/reference로만 쓰고, 실제 제출 종료선은 closing/evidence 문서로만 기록한다.
4. 아트 오너는 starter asset 범위를 다시 넓히지 말고 stage별 `starter-asset-list.md` 기준으로만 제출한다.
5. QA/PM은 tracker/run log 문장을 stage별 `handoff-line.md`와 closing language에서만 복붙하고, summary 문장은 기록에 직접 쓰지 않는다.

---

## 9. 한 장 결론
지금 남은 공백은 더 이상 `무엇을 설계해야 하지?`가 아니다. 이제 남은 공백은 **`좋아, 문서는 충분한데 지금 코드 설명 / 다음 계약 / 빠른 요약 / 실제 제출 경로를 어디서 구분하지?`**다. 이 문서는 Run Table token refinement를 다시 감각과 기억에 기대지 않게, 질문별 source-of-truth와 surface별 baseline-vs-target, owner별 읽기 순서, 기록 직전 문서 사용 타이밍까지 한 장으로 잠그는 bounded source-of-truth map이다.
