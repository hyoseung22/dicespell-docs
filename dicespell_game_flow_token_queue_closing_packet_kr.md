# DiceSpell `Run Table` Token Queue Closing 패킷

## 3줄 요약
- 이 문서는 `atlas-route-node`, `battle-stakes-plaque`, `oath-banner` stage packet이 모두 생긴 뒤에도 마지막까지 남아 있던 **`좋아, 그러면 이 세 stage를 어떤 문장과 어떤 unlock 규칙으로 닫아야 half-green이나 total-green 오독이 안 생기지?`**를 고정하는 source-of-truth다.
- 핵심은 새 UI를 더 설계하는 것이 아니라, `candidate`, `green`, `queue closing`, `follow-up reopen`을 **서로 다른 층위의 signoff 문장**으로 잠가 tracker / run log / portal / manifest / git 기록이 같은 말을 하게 만드는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `무엇이 stage green이고`, `무엇이 queue closing이며`, `어떤 문장 전에는 Run Table 완료나 overrun/ending green을 쓰면 안 되는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`atlas -> battle -> oath` 3-stage 큐를 **stage green 3개 + queue closing 1개** 언어로 닫아, 마지막 refinement 기록이 감상형 총괄 green이나 과장된 완료 문장으로 흐르지 않게 만든다.

## 왜 이 문서가 필요한가
지금 Run Table token 축은 아래 문서까지 이미 닫혀 있다.

- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- 왜 atlas -> battle -> oath 순서인가
- 각 stage가 어디까지를 한 archive로 닫는가
- 어떤 evidence bundle이 있어야 `candidate`를 말할 수 있는가
- 각 stage의 DOM/QA hook과 1440p capture, starter asset sanity는 무엇인가

하지만 실제 마지막 운영 직전에는 여전히 아래 공백이 남아 있다.

1. stage packet은 각 stage의 `candidate`와 `green`까지는 말하지만, **세 stage가 모두 닫힌 뒤 queue closing을 정확히 어떤 문장으로 남길지**는 아직 한 장으로 잠겨 있지 않다.
2. PM/QA가 `oath-banner green`을 곧바로 `Run Table complete`, `ending green`, `token 전체 완료`처럼 크게 적으면 마지막 stage 경계가 바로 무너진다.
3. tracker / automation run log / portal / manifest / git 커밋 문장이 stage별로 조금씩 다르면, 구현자는 `candidate`, `green`, `queue closing`, `follow-up reopen`을 같은 뜻으로 오해하게 된다.
4. 프로그래머/UI/아트는 사실 stage green까지만 보면 되지만, PM/QA가 queue closing 전후 문장을 별도로 고정하지 않으면 후속 refinement나 re-open 이유가 다시 모호해진다.

즉 지금 필요한 것은 새 visual concept가 아니라, **세 stage green 뒤 마지막 운영 문장과 reopen 규칙을 file-level handoff로 다시 잠그는 queue closing packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `1. closing boundary`, `4. 기록 문장 계약` | `token refinement queue`, `oath banner stage packet`, `token evidence manifest` | 내 일은 stage green까지이며 queue closing 문장은 PM/QA unlock 뒤에만 열린다 |
| UI | `1. closing boundary`, `3. role-specific closing handoff` | `battle stage packet`, `oath stage packet`, `token closing packet` | overview/focus와 density note는 stage evidence이고, queue closing용 총괄 문장은 내가 먼저 쓰지 않는다 |
| 아트 | `1. closing boundary`, `3. role-specific closing handoff`, `6. 리스크 & 후속과제` | `token anchor packet`, `token evidence manifest` | starter sanity는 stage green 근거이고, queue closing은 새 art request나 full polish 완료를 뜻하지 않는다 |
| QA | `2. queue closing ladder`, `4. 기록 문장 계약`, `6. 리스크 & 후속과제` | `atlas candidate review`, `battle stage packet`, `oath stage packet` | `oath-banner green`과 `Run Table token queue closing`은 다른 문장이고, queue closing은 세 stage evidence alignment 뒤에만 쓴다 |
| PM/기획 | `0. first screen 결론`, `2. queue closing ladder`, `5. immediate next actions` | `token gap ledger`, `token source-of-truth map`, `token closing packet` | 마지막 stage green 뒤에도 한 번 더 queue closing 검토가 필요하며, 그 전에는 total-green 문장을 금지해야 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `atlas`, `battle`, `oath` 3개 stage가 다 문서로 닫힌 뒤 마지막 운영 문장은 무엇이어야 하지?
- `oath-banner green`과 `Run Table token queue closing`은 왜 다른가?
- 어떤 문장 전에는 `Run Table complete`, `ending green`, `token 전체 완료`를 쓰면 안 되지?

### 가장 짧은 답
- `atlas-route-node green`, `battle-stakes-plaque green`, `oath-banner green`은 각각 **stage green**이다.
- `Run Table token queue closing`은 **세 stage green과 closing note alignment를 다시 확인한 뒤**에만 쓰는 별도 운영 문장이다.
- queue closing은 `Run Table 전체 UI 완성`, `ending/overrun 전체 green`, `새 follow-up 없음`을 뜻하지 않는다.
- follow-up이 생기더라도 `queue closing 후 새 bounded packet 재개방`으로 기록해야 하며, 이전 green 문장을 덮어쓰면 안 된다.

### 한 줄 판정 규칙
`oath-banner green`은 마지막 stage 종료고, `Run Table token queue closing`은 **세 stage 기록 문장이 서로 같은 뜻을 말하는지 확인한 뒤 남기는 운영 종료선**이다.

---

## 1. closing boundary

## 3줄 요약
- queue closing은 새 네 번째 stage가 아니라, 세 stage green 뒤에만 열리는 운영 closing layer다.
- 이 closing은 새 화면 설계나 추가 polish가 아니라 `stage green 3개 + evidence archive 경로 + handoff line`이 같은 언어로 남았는지 확인하는 일이다.
- queue closing을 큰 총괄 green으로 오해하면, stage 경계와 후속 reopen discipline이 한 번에 무너진다.

### 한 줄 목표
queue closing을 `stage green 합본`이 아니라 **기록/인계 정렬 검수**로만 고정한다.

| 구분 | queue closing에 포함 | queue closing에서 금지 |
| --- | --- | --- |
| 운영 범위 | atlas/battle/oath green 문장 alignment, archive 경로 점검, handoff line 정렬, follow-up reopen 필요 여부 기록 | 새 markup/CSS edit, 새 token family 추가, ending/overrun boundary 재설계 |
| 문장 범위 | `Run Table token queue closing`, `follow-up reopen only by new bounded packet` | `Run Table complete`, `ending green`, `전 UI 완료`, `거의 최종본` |
| evidence 범위 | 각 stage manifest / handoff line / candidate-green 기록 / portal 링크 정렬 확인 | 새 capture 추가 요구, 뒤늦은 density pass 확장, stage 외 screenshot 수집 |
| owner 범위 | QA alignment review + PM closing 기록 | 프로그래머 단독 closing 선언, UI/아트 감상형 총평을 closing으로 대체 |

### queue closing이 필요한 이유
1. 마지막 stage가 `oath-banner`라서 특히 `ending/overrun 전체 green`으로 과장될 위험이 크다.
2. tracker / run log / portal / manifest / git 메시지가 다른 말을 하면 이후 reopen 시점에 무엇이 실제로 닫혔는지 다시 모호해진다.
3. 세 stage를 따로 닫아도 마지막 운영 문장이 없으면, 다음 자동화가 `새 문서가 더 필요한가?`, `이제 구현이 끝났나?`를 다시 추측하게 된다.

---

## 2. queue closing ladder

## 3줄 요약
- 마지막 운영 단계는 `oath green -> closing review -> queue closing -> follow-up reopen 여부 기록` 순서로만 열린다.
- QA는 `closing-ready candidate`까지만 올리고, PM이 마지막 queue closing을 기록한다.
- queue closing 뒤 follow-up이 생겨도 기존 green을 지우지 않고 새 bounded packet으로만 다시 연다.

### 한 줄 목표
마지막 운영 문장도 `제출`, `확인`, `기록`, `재개방` 네 층으로 분리한다.

| 단계 | 누가 먼저 적는가 | 무엇이 있어야 하는가 | 다음에 열리는 것 |
| --- | --- | --- | --- |
| `oath-banner green` | PM/기획 | QA의 `oath archive-ready candidate` + oath handoff line 정렬 | queue closing review |
| `queue-closing review-ready` | QA | atlas/battle/oath green 문장, manifest 경로, handoff line, portal 링크가 모두 존재 | PM closing 검토 |
| `Run Table token queue closing` | PM/기획 | QA review-ready + 금지 문장 미사용 + follow-up reopen 필요 여부 명시 | 후속 packet 보류 또는 bounded reopen |
| `follow-up reopen` | PM/기획 + stage owner | 새 bounded packet 이름, reopen 이유, 아직 안 여는 범위가 명시됨 | 새 stage/packet 착수 |

### queue closing-ready 최소선
1. `atlas-route-node green`, `battle-stakes-plaque green`, `oath-banner green`이 각각 stage명 기준으로 존재한다.
2. 세 stage manifest의 `handoff line`이 queue와 unlock 관계를 서로 어긋나지 않게 말한다.
3. portal/index/tracker/run log 링크가 세 stage source packet과 summary를 모두 가리킨다.
4. `Run Table token queue closing` 문장이 `ending green`, `overrun green`, `UI complete` 같은 큰 총괄 문장을 포함하지 않는다.
5. 후속 refine 필요 시 `reopen` 이유가 새 bounded packet 단위로만 적힌다.

---

## 3. role-specific closing handoff

### 프로그래머에게 넘길 것
- 프로그래머가 닫는 마지막 문장은 stage green 전까지다.
- queue closing을 위해 새 code/CSS edit를 뒤늦게 끼워 넣지 않는다.
- reopen이 필요하면 기존 stage를 다시 흐리게 적지 말고 새 bounded packet 이유만 남긴다.

### UI에게 넘길 것
- overview/focus/density note는 stage evidence다.
- queue closing은 `세 stage의 visual verdict가 제출됐다`는 운영 문장이지, `이제 모든 UI가 완성됐다`는 선언이 아니다.
- closing 직전에 새 density pass를 열지 않는다.

### 아트에게 넘길 것
- starter asset sanity는 stage green 근거다.
- queue closing 시점에 새 starter 범위를 키우거나 전체 shell 리뉴얼 backlog를 끼워 넣지 않는다.
- 필요하면 follow-up reopen 메모로만 남긴다.

### QA에게 넘길 것
- QA는 `queue-closing review-ready`까지만 올린다.
- `oath-banner green` 직후 바로 `Run Table complete`를 쓰지 않는다.
- 세 stage의 manifest / handoff line / green 문장이 같은 관계를 말하는지 먼저 본다.

### PM/기획에게 넘길 것
- PM은 마지막 closing 문장에서 `무엇이 닫혔는가`와 `무엇이 아직 안 닫혔는가`를 같이 적어야 한다.
- 허용 문장은 `Run Table token queue closing`뿐이며, 필요하면 뒤에 `follow-up reopen only by new bounded packet`을 붙인다.
- `ending green`, `overrun green`, `전체 UI 완료`는 계속 금지다.

---

## 4. 기록 문장 계약

## 3줄 요약
- 마지막 운영 공백은 기술이 아니라 wording drift다.
- tracker / run log / portal / manifest / git가 같은 문장을 써야 다음 자동화가 half-green을 완료처럼 오독하지 않는다.
- 아래 문장만 허용하고, 큰 총괄 표현은 계속 금지한다.

### 한 줄 목표
queue closing wording을 복붙 가능한 계약 문장으로 고정한다.

### stage green 허용 문장
- `atlas-route-node green, battle-stakes-plaque unlock`
- `battle-stakes-plaque green, oath-banner unlock`
- `oath-banner green, queue-closing review-only`

### queue closing 허용 문장
- `Run Table token queue closing: atlas-route-node / battle-stakes-plaque / oath-banner stage green과 handoff line, archive path, portal source link alignment 확인.`
- `Run Table token queue closing: follow-up reopen은 새 bounded packet이 있을 때만 연다.`

### follow-up reopen 허용 문장
- `Run Table token follow-up reopen: <packet-name> only, previous atlas/battle/oath green 유지.`
- `Run Table token follow-up reopen: queue closing 이후 새 bounded packet 기준으로만 재개방.`

### 금지 문장
- `Run Table complete`
- `ending green`
- `전 UI 완료`
- `토큰 작업 끝`
- `사실상 최종본`
- `oath까지 끝났으니 전체 종료`

### 기록 위치별 규칙
| 위치 | 허용 문장 | 금지 |
| --- | --- | --- |
| tracker | stage green 또는 queue closing 계약 문장 | 큰 총괄 green, vague progress line |
| automation run log | timestamped factual line + queue closing 계약 문장 | 이전 entry 재작성, 감상형 결론 |
| portal index/summary | source packet/summary 링크 + queue closing 한 줄 | final release 톤 문구 |
| manifest | stage handoff line + next allowed step | total-green 주장 |
| git commit | `docs: add run table token queue closing packet` 수준의 사실 문장 | `finalize UI`, `complete run table` |

---

## 5. immediate next actions

1. 실제 마지막 운영 검토 전에는 `token refinement queue -> oath stage packet -> 이 queue closing packet` 순서로 읽는다.
2. `atlas-route-node green`, `battle-stakes-plaque green`, `oath-banner green` 기록이 모두 stage명 기준으로 존재하는지 확인한다.
3. QA는 세 stage manifest / handoff line / portal 링크 alignment를 보고 `queue-closing review-ready`를 남긴다.
4. PM은 그 뒤에만 `Run Table token queue closing` 문장을 tracker / run log / portal에 같은 wording으로 남긴다.
5. 후속 refinement가 필요하면 기존 green을 지우지 말고, `follow-up reopen`을 새 bounded packet 이름으로만 기록한다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| `oath-banner green`이 곧바로 `Run Table complete`로 기록되는 위험 | 마지막 stage 경계와 후속 reopen discipline이 무너진다 | queue closing을 별도 운영 문장으로 분리하고 금지 문장을 명시 | oath green 기록 직후 |
| tracker / run log / portal / manifest가 서로 다른 closing 문장을 쓰는 위험 | 다음 자동화가 무엇이 실제로 닫혔는지 다시 추측해야 한다 | 위치별 허용 문장 계약을 file-level로 고정 | queue closing 기록 직전 |
| queue closing 시점에 새 capture / density pass / CSS edit를 끼워 넣는 위험 | closing review가 다시 stage 확장으로 변한다 | closing boundary에서 새 edit 금지를 다시 고정 | queue-closing review-ready 직전 |
| 후속 refinement 필요 시 기존 green을 덮어쓰는 위험 | stage archive history와 reopen 이유가 섞여 audit trail이 흐려진다 | `follow-up reopen`은 새 bounded packet으로만 기록한다고 못 박음 | 첫 reopen 제안 직전 |
| PM/QA가 queue closing과 release/finalize를 같은 뜻으로 쓰는 위험 | Run Table token 문서 축이 실제 제품 전체 green처럼 과장된다 | queue closing 문장에 `token queue`와 `follow-up reopen`만 허용 | portal/tracker 요약 갱신 직전 |

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 마지막 실제 공백은 **`oath가 마지막 stage라는 건 알겠는데, 그 다음 운영 문장을 어디까지를 queue closing으로 적고 어디서부터는 새 reopen packet으로 다시 열어야 하지?`**였다. 이 문서는 그 공백을 `closing boundary`, `queue closing ladder`, `기록 문장 계약`, `follow-up reopen rule`까지 한 장으로 다시 압축해, atlas/battle/oath 3-stage 문서 세트가 마지막 기록에서 총괄 green으로 부풀지 않게 만드는 최종 운영 handoff packet이다.
