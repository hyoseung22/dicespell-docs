# DiceSpell `Run Table` Oath Banner Handoff Scorecard Summary

## 3줄 요약
- 이 문서는 `oath-banner` 마지막 live turn에서 누가 무엇을 언제 제출하고, 어디서 candidate와 green을 나누는지 빠르게 읽기 위한 readable companion이다.
- 핵심은 새 ending UI 설계가 아니라 `프로그래머 바닥 -> UI/아트 evidence -> QA candidate -> PM green` 순서를 고정하는 것이다.
- 첫 화면만 읽어도 `누가 지금 움직여도 되는지`, `어떤 문장을 아직 쓰면 안 되는지`, `queue closing이 언제 열리는지`가 보여야 한다.

## 한 줄 목표
`oath-banner` handoff를 **제출 순서 4단계 + 복붙 문장 4개**로 짧게 잠근다.

## 단계별 사다리
1. 프로그래머가 `manifest + hook/check` 바닥을 먼저 잠근다.
2. UI/아트가 `overview / banner-focus / density / starter sanity`를 올린다.
3. QA가 `oath-banner archive-ready candidate`를 기록한다.
4. PM이 `oath-banner green`과 `queue closing review-only unlock`을 마지막 한 줄로 남긴다.

## 역할별 핵심
| 역할 | 지금 해야 할 일 | 아직 하면 안 되는 일 |
| --- | --- | --- |
| 프로그래머 | oath-only hook/check 바닥 잠금 | ending helper/queue closing 확장 |
| UI | banner-first capture + density verdict | glamour shot, ending 총평 |
| 아트 | Oath Banner Starter sanity | ending shell 전체 리뉴얼 |
| QA | candidate 기록 | green 대신 쓰기 |
| PM | green + queue closing unlock 기록 | `Run Table complete` 같은 큰 총괄 문장 |

## 복붙 문장
- kickoff: `oath-banner archive를 열고 manifest + hook/check 바닥을 먼저 잠갔다.`
- in-progress: `oath-banner archive-in-progress: banner / result / carry evidence를 oath-only 범위로 모으는 중이다.`
- candidate: `oath-banner archive-ready candidate: artifact 8종과 oath-only wording 정렬이 확인됐다.`
- green: `oath-banner green. Run Table token queue closing review-only unlock만 다음 허용 단계다.`

## 금지 문장
- `Overrun Oath 진행 중`
- `ending은 거의 끝`
- `queue closing도 같이 가능`
- `Run Table complete`

## 바로 다음 액션
1. `oath filled archive examples`로 wording density를 먼저 맞춘다.
2. 이 scorecard 순서대로 실제 제출물을 정렬한다.
3. PM green 전에는 queue closing을 열지 않는다.

## 한 장 결론
oath 마지막 stage는 `자료가 많다`가 아니라 **`같은 순서와 같은 문장으로 닫힌다`**일 때만 green이다.
