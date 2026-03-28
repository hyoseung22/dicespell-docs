# DiceSpell `Run Table` Token Queue Readiness Board

## 3줄 요약
- 이 문서는 `atlas-route-node`, `battle-stakes-plaque`, `oath-banner`, `queue closing` 문서가 이미 충분한 지금도 끝까지 남아 있던 **`그래서 지금 누가 무엇을 시작해도 되고, 무엇은 아직 기다려야 하지?`**를 한 화면으로 고정하는 source-of-truth다.
- 핵심은 새 stage를 더 설계하는 것이 아니라, `start-ready`, `candidate-ready`, `green-ready`, `queue-closing-ready`, `follow-up reopen`을 **실제 착수/대기/인계 판단 언어**로 다시 잠가 프로그래머 / UI / 아트 / QA / PM이 같은 stop line을 보게 만드는 것이다.
- 첫 화면만 읽어도 `atlas는 지금 바로 refinement를 열 수 있는가`, `battle과 oath는 왜 아직 대기인가`, `queue closing은 왜 stage green 뒤에만 열리는가`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
Run Table token 축을 `문서 세트`가 아니라 **오너별 시작 가능 / 대기 / 제출 / 기록 보드**로 재배열해, 다음 refinement가 다시 큰 UI polish나 vague green으로 번지지 않게 만든다.

## 왜 이 문서가 필요한가
지금 Run Table token 축은 이미 아래 문서까지 충분히 닫혀 있다.

- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- atlas / battle / oath 각 stage를 어디까지 한 묶음으로 닫는가
- candidate와 green을 어떤 review 순서로 분리하는가
- stage archive에 어떤 artifact 8종이 들어가야 하는가
- 마지막 queue closing 문장을 무엇으로 남겨야 하는가

하지만 실제 handoff 직전에는 아직 아래 공백이 남아 있다.

1. 문서는 충분한데도 implementer는 `그래서 지금 당장 열 수 있는 stage가 atlas뿐인가?`를 다시 여러 packet에서 조합해 읽어야 한다.
2. UI/아트는 `지금 들어가도 되는가`와 `아직 대기 검토만 해야 하는가`를 stage별로 같은 표에서 보지 못하면, atlas green 전 battle/oath용 density pass를 미리 열기 쉽다.
3. QA/PM은 `artifact packet이 있다`, `candidate review packet이 있다`, `queue closing packet이 있다`를 곧바로 `진행 가능` 또는 `거의 완료`로 과장하기 쉽다.
4. 결국 다음 refinement가 실제 archive evidence보다 `문서가 충분하니 이제 그냥 조금씩 다 만져 보자`로 부풀 수 있다.

즉 지금 필요한 것은 새 토큰 이름이 아니라, **문서 readiness와 실제 execution readiness를 분리해서 보여 주는 readiness board**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. stage readiness 표`, `3-1. 프로그래머 handoff` | `token source-of-truth map`, `archive bootstrap`, `atlas stage/required artifacts/candidate review` | 지금 열 수 있는 live refinement는 atlas 하나뿐이고, battle/oath는 unlock 전까지 코드/markup 범위를 넓히면 안 된다 |
| UI | `1. first screen 판정`, `2. stage readiness 표`, `3-2. UI handoff` | `token anchor packet`, `token delivery workboard`, `atlas candidate review` | 지금 필요한 것은 atlas stage evidence용 density review이며 battle/oath visual pass는 아직 대기다 |
| 아트 | `2. stage readiness 표`, `3-3. 아트 handoff`, `5. 리스크 & 후속과제` | `token anchor packet`, `token evidence manifest` | starter asset sanity는 stage별로 하나씩만 닫고, queue closing 전 전체 polish를 열면 안 된다 |
| QA | `2. stage readiness 표`, `3-4. QA handoff`, `4. 기록 문장 규칙` | `atlas candidate review`, `battle candidate review`, `oath candidate review`, `queue closing packet` | stage별 `candidate-ready`와 `green-ready`와 `queue-closing-ready`를 다른 증거로 기록해야 한다 |
| PM/기획 | `1. first screen 판정`, `2. stage readiness 표`, `4. 기록 문장 규칙`, `5. 리스크 & 후속과제` | `token refinement queue`, `token queue closing packet`, `token gap ledger` | 지금은 atlas stage만 열려 있고, battle/oath/queue closing은 unlock 조건 전까지 기록만 준비해야 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- 지금 당장 refinement를 열 수 있는 stage는 무엇인가?
- `문서가 준비됨`과 `실제 stage를 열어도 됨`은 왜 다른가?
- queue closing은 왜 `oath-banner green` 뒤에만 열리는가?

### 가장 짧은 답
- **지금 실제로 열 수 있는 stage**: `atlas-route-node`
- **atlas green 뒤에만 열 수 있는 stage**: `battle-stakes-plaque`
- **battle green 뒤에만 열 수 있는 stage**: `oath-banner`
- **oath green 뒤에만 열 수 있는 운영 layer**: `queue-closing review-ready -> Run Table token queue closing`
- `required artifacts packet`이나 `candidate review packet`이 존재한다고 해서 stage가 자동으로 열리는 것은 아니다.

### 한 줄 판정 규칙
Run Table token 축은 지금 **`atlas only live-ready, battle/oath locked, queue closing review-only`** 상태로 읽어야 한다.

---

## 1. first screen 판정

## 3줄 요약
- Run Table token 문서는 이미 많지만, 실제 실행 순서는 여전히 `atlas -> battle -> oath -> queue closing` 하나뿐이다.
- 따라서 readiness board의 역할은 문서 목록을 더 늘리는 것이 아니라, `지금 열 수 있는 단계`와 `아직 문장만 준비해야 하는 단계`를 분리하는 데 있다.
- 이 분리가 없으면 문서 완성도가 높을수록 오히려 refinement 범위가 쉽게 부푼다.

### 한 줄 목표
`문서가 있음`과 `지금 착수 가능`을 다른 층위로 고정한다.

| 질문 | 짧은 판정 | 이유 |
| --- | --- | --- |
| atlas는 지금 열 수 있는가? | 예 | queue / archive bootstrap / stage packet / required artifacts / candidate review가 모두 닫혀 있어 첫 bounded refinement를 열 조건이 갖춰져 있다 |
| battle은 지금 열 수 있는가? | 아니오 | battle 문서는 준비됐지만 unlock 조건은 여전히 `atlas-route-node green`이다 |
| oath는 지금 열 수 있는가? | 아니오 | oath 문서는 준비됐지만 unlock 조건은 여전히 `battle-stakes-plaque green`이다 |
| queue closing은 지금 열 수 있는가? | 아니오 | queue closing은 stage green 3개와 handoff line alignment 뒤의 운영 문장이다 |
| 지금 해도 되는 일은 무엇인가? | atlas stage archive 생성 + artifact alignment + candidate/green review 준비 | stage 범위를 넓히지 않고도 실질 evidence를 남길 수 있는 유일한 시작점이기 때문이다 |

---

## 2. stage readiness 보드

## 3줄 요약
- 아래 표는 atlas / battle / oath / queue closing을 `문서 준비도`가 아니라 `실제 시작 가능 여부`로 다시 읽게 만든다.
- 목적은 다음 구현/문서화 턴이 `이제 battle도 조금`, `oath도 문서 있으니 같이` 같은 확장을 못 하게 하는 것이다.
- 프로그래머, UI, 아트, QA, PM 모두 이 표의 단계 언어를 그대로 복붙해서 써야 한다.

### 한 줄 목표
각 stage를 `ready / locked / review-only` 상태와 증거 기준으로 고정한다.

| 단계 | 지금 상태 | 시작 조건 | 이번 단계 핵심 산출물 | 아직 금지 | 다음 단계로 넘기는 증거 |
| --- | --- | --- | --- | --- | --- |
| `atlas-route-node` | **즉시 시작 가능** | `source-of-truth map -> gap ledger -> archive bootstrap -> refinement queue -> atlas stage/required artifacts/candidate review` 읽기 완료 | atlas stage archive, artifact 8종, first-glance verdict, starter sanity verdict, `atlas archive-ready candidate` | battle/oath 선행 열기, route node 외 preview/log polish 확장, 큰 total-green 문장 | `atlas archive-ready candidate` + `atlas-route-node green, battle-stakes-plaque unlock` |
| `battle-stakes-plaque` | **atlas green 뒤 시작** | atlas green 문장과 archive path 정렬 확인 | battle stage archive, artifact 8종, stakes verdict, starter sanity, `battle archive-ready candidate` | oath 선행 열기, action panel/HUD 전체 확장, `Battle Table green` 같은 큰 문장 | `battle archive-ready candidate` + `battle-stakes-plaque green, oath-banner unlock` |
| `oath-banner` | **battle green 뒤 시작** | battle green 문장과 archive path 정렬 확인 | oath stage archive, artifact 8종, banner-first verdict, starter sanity, `oath archive-ready candidate` | ending/overrun 전체 polish, queue closing 선행 기록, `Run Table complete` | `oath archive-ready candidate` + `oath-banner green, queue-closing review-only` |
| `queue closing` | **oath green 뒤 review-only** | atlas/battle/oath green 3개 + handoff line + archive path + portal link alignment | `queue-closing review-ready`와 `Run Table token queue closing` 운영 문장 | 새 markup/CSS edit, 새 capture 요구, 기존 green 덮어쓰기 | `Run Table token queue closing` + 필요 시 새 bounded `follow-up reopen` 이름 |

### 단계별 한 줄 해석
- **atlas**: 지금 유일하게 live-ready한 첫 refinement stage
- **battle**: 문서는 준비됐지만 atlas green 전엔 locked
- **oath**: 문서는 준비됐지만 battle green 전엔 locked
- **queue closing**: stage가 아니라 review-only 운영 layer

---

## 3. 역할별 handoff 포인트

## 3-1. 프로그래머에게 넘길 것

## 3줄 요약
- 프로그래머는 지금 `무슨 surface를 더 건드리지?`가 아니라 `어느 stage 하나만 열어도 되는가`를 판단해야 한다.
- 현재 실제로 열 수 있는 live edit 범위는 atlas stage 하나뿐이다.
- battle/oath는 문서가 있어도 unlock 전에는 참고 문서이지 실행 범위가 아니다.

**한 줄 목표:** 프로그래머는 atlas green 전까지 atlas만 닫고, unlock 전 stage를 미리 열지 않는다.

| 단계 | 열어도 되는 것 | 이번 턴 핵심 작업 | 절대 섞지 말 것 |
| --- | --- | --- | --- |
| atlas | atlas stage archive, route node hook evidence, threshold verdict 메모, candidate/green line 준비 | `bootstrap-ready -> archive-ready candidate -> atlas green` 한 흐름만 닫기 | battle stakes markup, oath banner 정리, queue closing 문장 선기록 |
| battle | atlas green 뒤에만 | current/next/grimoire stakes hierarchy를 한 stage로 잠그기 | oath/ending 확장 |
| oath | battle green 뒤에만 | banner/result/carry hook과 review line만 닫기 | queue closing이나 ending total-green 선기록 |
| queue closing | 코드 작업 아님 | stage 기록 정렬 확인만 지원 | 새 edit 추가 |

### 프로그래머 제출물
1. 이번 턴 stage 이름 1개
2. archive path 1개
3. candidate 또는 green 문장 1개
4. stage 바깥을 열지 않았다는 non-change 메모 1개

---

## 3-2. UI에게 넘길 것

## 3줄 요약
- UI의 기본 역할은 지금 `전체 polish`가 아니라 stage별 first-glance를 닫는 것이다.
- atlas green 전에는 battle/oath visual pass를 먼저 열면 안 된다.
- queue closing은 visual finishing 턴이 아니라 visual evidence alignment 확인 턴이다.

**한 줄 목표:** UI는 atlas/battle/oath를 한 번에 다듬지 말고, 현재 unlock된 stage 하나의 hierarchy만 닫는다.

| 단계 | UI 역할 | 이번 턴 필수 확인 | 금지 |
| --- | --- | --- | --- |
| atlas | 실제 closing | route node threshold first-glance, overview/focus capture, density note | battle/oath 캡처 동시 생성 |
| battle | atlas green 뒤 실제 closing | current/next/grimoire stakes hierarchy, plaque starter sanity | action panel/HUD 전체 재정렬 |
| oath | battle green 뒤 실제 closing | banner/result/carry hierarchy, neutral ending helper와 구분 | ending/overrun 전체 shell 합치기 |
| queue closing | review-only | 세 stage capture/handoff line wording alignment 확인 | 새 visual polish 요구 |

### UI 제출물
1. unlock된 stage 캡처 세트
2. hierarchy verdict 1개
3. starter sanity 메모 1개
4. 아직 안 여는 surface 메모 1개

---

## 3-3. 아트에게 넘길 것

## 3줄 요약
- 아트의 기본 임무는 대형 신작이 아니라 starter asset sanity와 token 차이 유지다.
- 특히 queue closing 전에 `이왕이면 전체 톤도 같이`가 열리면 stage 경계가 즉시 무너진다.
- 지금 필요한 것은 atlas/battle/oath 각각의 bounded sanity다.

**한 줄 목표:** 아트는 unlock된 stage 한 면의 starter sanity만 닫고, 전체 polish를 열지 않는다.

| 단계 | 아트 기본 역할 | 이번 턴 필요한 것 | 이번 턴 필요 없는 것 |
| --- | --- | --- | --- |
| atlas | sanity closing | route node starter asset list, token contrast 메모 | battle/oath 동시 자산 패키지 |
| battle | atlas green 뒤 sanity closing | stakes plaque starter asset list | 전투 전체 신규 아트 팩 |
| oath | battle green 뒤 sanity closing | oath banner starter asset list | ending/overrun 확장 아트 |
| queue closing | review-only | 세 stage 자산 제출물 alignment 확인 | 새 자산 요청 |

### 아트 제출물
1. stage별 starter asset sanity 메모
2. token/accent drift 없음 확인 1줄
3. follow-up reopen 필요 시 이유 1줄

---

## 3-4. QA에게 넘길 것

## 3줄 요약
- QA는 지금부터 `문서가 있음`을 pass로 기록하면 안 된다.
- atlas/battle/oath/queue closing은 각각 다른 evidence 묶음을 가진다.
- stage 이름이 흐려지면 가장 먼저 audit trail이 무너진다.

**한 줄 목표:** QA는 `candidate-ready`, `green-ready`, `queue-closing-ready`를 다른 증거로 분리 기록한다.

| 단계 | 필수 evidence | 같이 남길 질문 | fail로 돌려야 하는 경우 |
| --- | --- | --- | --- |
| atlas | archive 8종 + overview/focus + density review + handoff line | route node threshold가 정말 atlas-only로 읽히는가 | battle/oath unlock 전 증거가 같이 섞였을 때 |
| battle | archive 8종 + stakes verdict + handoff line | current/next/grimoire hierarchy가 battle-only로 읽히는가 | action panel/HUD 전체 확장이 같이 들어왔을 때 |
| oath | archive 8종 + banner-first verdict + handoff line | banner/result/carry가 ending total-green 없이 읽히는가 | queue closing이나 ending green이 먼저 기록됐을 때 |
| queue closing | atlas/battle/oath green 3개 + manifest 경로 + portal/source link alignment | 같은 문장이 각 위치에서 같은 뜻을 말하는가 | 큰 총괄 green 문장이 섞였을 때 |

---

## 3-5. PM/기획에게 넘길 것

## 3줄 요약
- PM/기획의 가장 중요한 역할은 지금부터 stage unlock discipline을 기록 문장으로 지키는 것이다.
- 문서가 충분해도 기록이 커지면 결국 실행 범위가 같이 부푼다.
- 지금 남길 수 있는 문장은 항상 현재 unlock된 stage 하나 또는 queue closing review-only 문장뿐이다.

**한 줄 목표:** PM은 `무엇이 열렸는가`보다 `무엇이 아직 잠겨 있는가`를 함께 적는다.

### PM 체크포인트
- atlas green 전에는 battle/oath를 `다음`으로만 적고 `진행`으로 적지 않는다.
- battle green 전에는 oath를 `문서 준비 완료` 이상으로 적지 않는다.
- oath green 전에는 queue closing을 기록하지 않는다.
- queue closing 뒤 follow-up이 생겨도 기존 green을 지우지 않고 새 bounded packet 이름으로만 reopen을 연다.

---

## 4. 기록 문장 규칙

## 3줄 요약
- 가장 큰 실행 리스크는 wording drift다.
- tracker / run log / portal / manifest / git가 각기 다른 단계를 같은 초록불처럼 쓰면 stage boundary가 즉시 무너진다.
- readiness board는 단계별 허용 문장을 복붙 가능한 수준으로 고정해야 한다.

### 한 줄 목표
단계별로 `문장 1개 + evidence 1묶음 + 다음 unlock 1개`만 남긴다.

### 허용 문장
- `atlas archive-ready candidate`
- `atlas-route-node green, battle-stakes-plaque unlock`
- `battle archive-ready candidate`
- `battle-stakes-plaque green, oath-banner unlock`
- `oath archive-ready candidate`
- `oath-banner green, queue-closing review-only`
- `queue-closing review-ready`
- `Run Table token queue closing`
- `Run Table token follow-up reopen: <bounded-packet-name> only`

### 금지 문장
- `Run Table complete`
- `atlas/battle/oath 거의 다 됨`
- `전 UI 완료`
- `ending green`
- `overrun green`
- `토큰 작업 마감`
- `사실상 최종본`

### 위치별 규칙
| 위치 | 허용 문장 | 금지 |
| --- | --- | --- |
| tracker | unlock된 stage 1개 또는 queue closing 1개 | 여러 stage 동시 green, total-green |
| automation run log | timestamped append entry + factual handoff line | 이전 entry 재작성, 감상형 총평 |
| portal index/summary | source packet/summary 링크 + readiness 한 줄 | final release 톤 요약 |
| manifest | stage handoff line + next allowed step | 다른 stage unlock 선기록 |
| git commit | `docs: add run table token readiness board` 수준 사실 문장 | `finalize run table`, `complete token work` |

---

## 5. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| 문서가 충분한 상태에서 atlas/battle/oath가 모두 `실행 가능`처럼 소비되는 위험 | 다음 refinement가 stage discipline 대신 큰 polish로 부풀 수 있다 | readiness board로 `atlas only live-ready`, `battle/oath locked`, `queue closing review-only` 상태를 한 표로 고정했다 | 다음 Run Table 실제 refinement 착수 직전 |
| UI/아트가 atlas green 전 battle/oath density pass를 같이 여는 위험 | evidence archive가 다시 stage별이 아니라 감상형 묶음이 된다 | 역할별 표에 `unlock된 stage 하나만 닫기` 원칙과 금지 범위를 다시 명시했다 | atlas first-stage archive 생성 직전 |
| QA/PM이 `candidate-ready`, `green-ready`, `queue-closing-ready`를 같은 초록불로 쓰는 위험 | tracker/run log/portal에서 stage boundary가 무너진다 | readiness board에 단계별 허용 문장과 위치별 기록 규칙을 추가했다 | 다음 handoff line/portal wording 갱신 직전 |
| queue closing이 새 polish 요청 창구처럼 오해되는 위험 | closing review가 다시 구현 확장 턴으로 바뀐다 | queue closing을 review-only layer로 못 박고 새 edit를 금지했다 | queue-closing review-ready 직전 |
| follow-up이 생겼을 때 기존 green을 덮어쓰는 위험 | audit trail과 reopen reason이 섞인다 | `follow-up reopen`은 새 bounded packet 이름으로만 열도록 다시 고정했다 | 첫 reopen 제안 직전 |

---

## 6. 즉시 다음 액션

1. 다음 Run Table 작업자는 먼저 `token source-of-truth map -> token gap ledger -> token archive bootstrap packet -> token refinement queue packet -> 이 readiness board` 순서로 읽는다.
2. 이번 턴에 실제로 열 수 있는 범위는 `atlas-route-node` 하나뿐이라는 사실을 먼저 선언한다.
3. atlas stage archive를 생성하고 `artifact 8종 -> first-glance verdict -> starter sanity -> atlas archive-ready candidate` 순서로 evidence를 채운다.
4. QA는 그 뒤에만 `atlas-route-node green, battle-stakes-plaque unlock` 검토로 넘어간다.
5. battle/oath/queue closing은 이번 턴에 코드나 기록을 먼저 열지 말고, unlock 조건과 금지 범위만 공유한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 마지막 실무 공백은 **`문서는 충분한데, 그래서 지금 실제로 열 수 있는 stage는 무엇이고 누가 아직 기다려야 하지?`**였다. 이 문서는 그 공백을 `atlas only live-ready`, `battle/oath locked`, `queue closing review-only`, `역할별 제출물`, `기록 문장 규칙`, `follow-up reopen discipline`까지 한 장으로 다시 압축해, 다음 refinement가 stage archive closing 대신 큰 UI polish나 vague green으로 새지 않게 만드는 readiness board다.
