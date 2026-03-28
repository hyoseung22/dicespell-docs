# DiceSpell `Run Table` Token Source-of-Truth Map Summary

## 3줄 요약
- 이 문서는 Run Table token 문서 묶음이 충분해진 뒤 남아 있던 마지막 읽기 병목, 즉 **지금 코드 / 다음 계약 / 빠른 요약 / 실제 제출 경로를 어디서 구분해 읽을지**를 짧게 정리한 companion이다.
- 먼저 이 요약으로 전체 판단을 잡고, 실제 착수/리뷰/기록은 반드시 `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`와 `closing/evidence` source packet 기준으로 진행한다.
- 대상은 프로그래머, UI 오너, 아트 오너, QA/PM이며, 새 디자인 제안이 아니라 **문서 읽기 위계**를 고정하는 문서다.

## 한 줄 목표
Run Table 후속 refinement가 다시 `문서 여러 장을 왔다 갔다 하며 감으로 범위를 정하는 일`로 흐르지 않게, **질문별 source-of-truth를 3~5분 안에 고르게 한다.**

## 왜 이 문서가 필요한가
Run Table 축은 이미 다음까지 닫혀 있다.

- surface family 역할
- token / DOM / QA anchor
- owner별 delivery order
- closing language
- evidence archive / 파일명 규칙

문제는 이제 `문서가 없어서`가 아니라 **문서가 충분해서 baseline 설명, target 계약, summary, evidence 문서가 같은 층위로 읽히기 쉽다**는 점이다. 이 요약은 그 혼선을 줄이는 빠른 안내판이다.

## 무엇을 어디서 읽는가

| 질문 | 먼저 볼 문서 | 메모 |
| --- | --- | --- |
| 지금 live 코드는 어디 있지? | `dice-spell-standalone/src/app.js`, `src/styles.css` + source-of-truth map 본문 | baseline 문서다 |
| 다음에 무엇을 잠가야 하지? | `token anchor packet`, `delivery workboard`, `closing packet` | target 계약 문서다 |
| 어떤 파일로 제출하지? | `token evidence manifest packet` | archive/파일명 규칙 문서다 |
| 요약만 빨리 보고 싶다 | 각 `*_summary_kr.md` | companion이지 계약 문서는 아니다 |
| 시각 톤을 빠르게 확인하고 싶다 | prototype board | ref이지 closing 기준은 아니다 |

## Surface별 핵심 결론

### Run Atlas
- baseline: `renderRouteBoard()`와 `.run-route-board/.run-route-node`
- target: `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold`
- evidence path: `docs/artifacts/run-table-token-delivery/atlas-route-node/`

### Battle Table
- baseline: `renderBattle(summary)`와 `.battle-stakes-grid/.battle-stakes-card`
- target: `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"`
- evidence path: `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/`

### Overrun Oath
- baseline: `renderEnding(summary)`와 `.ending-banner/.ending-banner-pill`
- target: `data-run-surface="overrun-oath"`, `data-oath-role`, `data-overrun-cta`
- evidence path: `docs/artifacts/run-table-token-delivery/oath-banner/`

## 역할별 읽기 순서

| 역할 | 읽기 순서 |
| --- | --- |
| 프로그래머 | source-of-truth map → token anchor → delivery workboard → closing → evidence |
| UI 오너 | summary → source-of-truth map → prototype board → closing → evidence |
| 아트 오너 | summary → source-of-truth map → token anchor/closing → evidence |
| QA/PM | summary → source-of-truth map → closing → evidence → `handoff-line.md` |

## 꼭 기억할 금지선
- summary는 source packet 대체물이 아니다.
- prototype board는 final contract가 아니다.
- evidence manifest는 구현 순서 문서가 아니다.
- capture-only 제출은 closing이 아니다.
- tracker/run log 문장은 `closing packet`과 stage별 `handoff-line.md`에서만 복붙한다.

## Immediate next actions
1. Run Table 문서 스택을 `surface handoff -> token anchor -> delivery workboard -> closing -> evidence manifest -> source-of-truth map`으로 고정한다.
2. 착수 전에는 baseline-vs-target을 먼저 분리한다.
3. 리뷰 전에는 closing 기준만 본다.
4. 제출 전에는 evidence path와 파일명만 본다.
5. 기록 전에는 `handoff-line.md`만 쓴다.

## 한 장 결론
Run Table의 남은 공백은 디자인이 아니라 **문서 위계**다. 이 요약은 `지금 코드 설명`, `다음 계약`, `빠른 브라우징`, `실제 제출 경로`를 다시 섞지 않게 만드는 첫 화면용 companion이다.
