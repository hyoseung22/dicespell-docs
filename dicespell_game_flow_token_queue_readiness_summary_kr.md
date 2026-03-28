# DiceSpell `Run Table` Token Queue Readiness Board 요약

## 3줄 요약
- 문서는 충분하지만 실제 착수 가능 여부는 여전히 `atlas -> battle -> oath -> queue closing` 한 줄 큐다.
- 이 문서는 `atlas만 지금 live-ready`, `battle/oath는 locked`, `queue closing은 review-only`라는 실행 상태를 한 화면에서 읽게 만든다.
- 프로그래머 / UI / 아트 / QA / PM 모두 `문서 준비`와 `지금 착수 가능`을 같은 뜻으로 쓰지 않게 만드는 것이 목적이다.

## 한 줄 목표
Run Table token 축을 `문서 묶음`이 아니라 **시작 가능 / 대기 / 제출 / 기록 판단 보드**로 읽게 만든다.

## 왜 지금 중요한가
- atlas/battle/oath stage packet, required artifacts, candidate review, queue closing packet까지는 이미 있다.
- 그런데 이 상태에서도 실제 handoff 직전엔 `그래서 지금 누가 무엇을 시작해도 되지?`가 다시 흔들린다.
- readiness board가 없으면 atlas green 전 battle/oath visual pass나 큰 총괄 green 문장이 다시 끼어들기 쉽다.

## first screen 판정
| 질문 | 답 |
| --- | --- |
| 지금 실제로 열 수 있는 stage | `atlas-route-node` |
| atlas green 뒤에 열리는 stage | `battle-stakes-plaque` |
| battle green 뒤에 열리는 stage | `oath-banner` |
| oath green 뒤에만 열리는 운영 layer | `queue-closing review-ready -> Run Table token queue closing` |

## stage readiness 한눈표
| 단계 | 상태 | 지금 해야 할 것 | 아직 금지 |
| --- | --- | --- | --- |
| atlas | 즉시 시작 가능 | stage archive + artifact 8종 + candidate/green review 준비 | battle/oath 선행 오픈 |
| battle | atlas green 뒤 시작 | stakes stage archive + verdict + candidate/green | oath 선행 오픈 |
| oath | battle green 뒤 시작 | banner stage archive + verdict + candidate/green | queue closing 선행 기록 |
| queue closing | oath green 뒤 review-only | stage green 3개, handoff line, archive path 정렬 확인 | 새 markup/CSS edit |

## 역할별 handoff 핵심
### 프로그래머
- 지금 실제로 열 수 있는 live refinement는 atlas 하나뿐이다.
- unlock 전 battle/oath는 참고 문서이지 실행 범위가 아니다.

### UI
- 현재 필요한 건 atlas first-glance와 density evidence다.
- atlas green 전 battle/oath visual pass를 미리 열지 않는다.

### 아트
- starter asset sanity는 unlock된 stage 하나씩만 닫는다.
- queue closing 전에 전체 polish를 열지 않는다.

### QA
- `candidate-ready`, `green-ready`, `queue-closing-ready`를 서로 다른 증거로 기록한다.
- stage 이름이 흐려지면 바로 fail로 되돌린다.

### PM/기획
- 지금 남길 수 있는 문장은 현재 unlock된 stage 하나 또는 queue closing 하나뿐이다.
- `Run Table complete`, `ending green`, `전 UI 완료` 같은 큰 문장은 계속 금지다.

## 허용 문장
- `atlas archive-ready candidate`
- `atlas-route-node green, battle-stakes-plaque unlock`
- `battle archive-ready candidate`
- `battle-stakes-plaque green, oath-banner unlock`
- `oath archive-ready candidate`
- `oath-banner green, queue-closing review-only`
- `queue-closing review-ready`
- `Run Table token queue closing`
- `Run Table token follow-up reopen: <bounded-packet-name> only`

## 금지 문장
- `Run Table complete`
- `ending green`
- `overrun green`
- `토큰 작업 마감`
- `사실상 최종본`

## 즉시 다음 액션
1. `token source-of-truth map -> gap ledger -> archive bootstrap -> refinement queue -> readiness board` 순서로 읽는다.
2. 이번 턴 범위를 `atlas-route-node` 하나로 먼저 선언한다.
3. atlas stage archive를 채우고 `artifact 8종 -> first-glance verdict -> starter sanity -> atlas archive-ready candidate` 순서로 evidence를 남긴다.
4. battle/oath/queue closing은 unlock 조건이 오기 전까지 기록만 준비한다.

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`
