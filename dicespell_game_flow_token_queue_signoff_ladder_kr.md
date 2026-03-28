# DiceSpell `Run Table` Token Queue Signoff Ladder

## 3줄 요약
- 이 문서는 `atlas-route-node`, `battle-stakes-plaque`, `oath-banner`, `queue closing` 문서가 이미 충분한 상태에서도 마지막까지 남아 있던 **`좋아, 그러면 정확히 누가 review-ready를 올리고, 누가 green을 선언하고, 누가 follow-up reopen을 열지?`**를 역할별 signoff 사다리로 다시 고정한다.
- 핵심은 새 stage를 더 설계하는 것이 아니라, `stage green`, `queue-closing review-ready`, `Run Table token queue closing`, `follow-up reopen`의 **기록 주체와 unlock 권한**을 분리해 PM/QA/프로그래머/UI/아트가 같은 승인 언어를 쓰게 만드는 것이다.
- 첫 화면만 읽어도 `oath-banner green` 뒤에 누가 멈춰 세울 수 있는지, 어떤 증거가 있어야 queue closing으로 넘어갈 수 있는지, 무엇이 아직 금지인지가 바로 보여야 한다.

## 한 줄 목표
`atlas -> battle -> oath -> queue closing` 마지막 운영선을 **제출자 / 동반 검토자 / 마지막 기록자 / 다음 unlock 조건**까지 file-level signoff ladder로 잠가, half-green과 total-green이 다시 섞이지 않게 만든다.

## 왜 이 문서가 필요한가
지금 Run Table token 축은 아래 문서까지 이미 충분하다.

- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_handoff_scorecard_kr.md`
- `docs/dicespell_game_flow_token_battle_handoff_scorecard_kr.md`
- `docs/dicespell_game_flow_token_oath_handoff_scorecard_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- atlas / battle / oath stage를 어떤 artifact 8종과 어떤 wording 밀도로 닫는가
- `candidate`와 `green`을 어떤 순서로 나누는가
- queue closing을 어떤 운영 문장으로 남기는가
- follow-up reopen을 큰 총괄 문장 대신 새 bounded packet으로만 기록해야 한다는 점

하지만 실제 handoff 직전에는 아직 아래 공백이 남아 있었다.

1. `queue-closing review-ready`는 누가 올리고 누가 stop을 걸 수 있는지가 문서 묶음 전체를 읽어야만 드러난다.
2. PM/QA가 같은 문장을 서로 다른 의미로 쓰면 `oath-banner green`과 `Run Table token queue closing`이 다시 같은 초록불처럼 소비된다.
3. 프로그래머/UI/아트는 stage evidence를 냈는데도 최종 운영선에서 누가 마지막 unlock 권한을 갖는지 모르면 review-ready와 green 사이에 불필요한 드리프트가 생긴다.
4. follow-up이 필요할 때 기존 green을 유지한 채 새 packet만 여는 discipline이 ownership까지 같이 잠겨 있지 않으면, `closing 취소`처럼 보이는 기록이 생긴다.

즉 지금 필요한 것은 새 stage packet이 아니라, **signoff ownership과 unlock authority를 한 장으로 다시 고정하는 ladder 문서**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. stage별 signoff ladder`, `4. 역할별 handoff` | `atlas/battle/oath handoff scorecard`, `queue closing packet` | 내 마지막 제출은 stage evidence까지이며 queue closing 선언권은 없다 |
| UI | `2. stage별 signoff ladder`, `4. 역할별 handoff`, `5. 금지 문장/금지 행동` | `oath handoff scorecard`, `queue readiness board` | visual sanity는 signoff 입력이지만 마지막 green 문장은 PM/QA 단계에서만 열린다 |
| 아트 | `2. stage별 signoff ladder`, `4. 역할별 handoff` | `token anchor packet`, `evidence manifest` | starter sanity 제출 뒤에는 follow-up reopen 이유만 남기고 closing 문장을 먼저 쓰지 않는다 |
| QA | `1. signoff boundary`, `2. stage별 signoff ladder`, `3. queue-closing signoff 계약` | `candidate review packet`, `queue closing packet` | QA는 review-ready / candidate / unlock 판단권은 있지만 PM green을 대신 쓰지 않는다 |
| PM/기획 | `0. first screen 결론`, `3. queue-closing signoff 계약`, `6. 리스크 & 후속과제` | `queue readiness board`, `queue closing packet`, `source-of-truth map` | 마지막 green과 queue closing, follow-up reopen은 서로 다른 ownership 문장으로 끝까지 분리해야 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `oath-banner green` 뒤에 정확히 누가 queue-closing review-ready를 올리는가?
- QA와 PM은 어디서 서로 멈춰 세울 수 있는가?
- follow-up reopen은 누가 어떤 조건으로만 열 수 있는가?

### 가장 짧은 답
- **프로그래머 / UI / 아트**는 stage evidence 제출자다.
- **QA**는 `candidate`, `review-ready`, `unlock 가능`을 판정하는 동반 검토자다.
- **PM/기획**은 `green`, `Run Table token queue closing`, `follow-up reopen`의 마지막 기록자다.
- `PM green 없음`, `queue-closing review-ready 없음`, `bounded packet 이름 없음` 상태에서는 다음 단계가 열리지 않는다.

### 한 줄 판정 규칙
Run Table token 축의 마지막 운영선은 **`제출자 != 동반 검토자 != 마지막 기록자`** 구조로 읽어야 한다.

---

## 1. signoff boundary

## 3줄 요약
- signoff ladder의 목적은 stage packet을 다시 쓰는 것이 아니라, 이미 있는 evidence bundle에 **누가 어떤 권한으로 서명하는지**를 분리하는 데 있다.
- `candidate`, `green`, `queue closing`, `reopen`은 모두 다른 승인 층위다.
- 이 경계가 흐려지면 가장 먼저 tracker / run log / portal에서 half-green이 complete처럼 보인다.

### 한 줄 목표
같은 evidence라도 역할마다 **할 수 있는 말의 최대치**를 따로 잠근다.

| 승인 층위 | 누가 먼저 적나 | 누가 확인하나 | 아직 못 쓰는 문장 |
| --- | --- | --- | --- |
| stage evidence drop | 프로그래머 / UI / 아트 | QA | `green`, `queue closing`, `complete` |
| `archive-ready candidate` | QA | PM/기획 | `queue closing`, `follow-up reopen` |
| `<stage> green` | PM/기획 | QA 검토 완료 전제 | `Run Table token queue closing`, `Run Table complete` |
| `queue-closing review-ready` | QA | PM/기획 | `Run Table token queue closing`, `follow-up reopen` |
| `Run Table token queue closing` | PM/기획 | QA review-ready 전제 | `Run Table complete`, `ending green` |
| `follow-up reopen` | PM/기획 + 해당 stage owner | QA/오너 메모 참조 | 기존 green 덮어쓰기 |

---

## 2. stage별 signoff ladder

## 3줄 요약
- atlas / battle / oath는 모두 같은 사다리를 쓰되 unlock 대상만 다르다.
- 마지막 queue closing도 같은 형태를 유지하되 `새 edit 없음`이 추가 규칙으로 붙는다.
- 아래 표를 그대로 tracker / run log / manifest handoff line의 ownership 기준으로 재사용해야 한다.

### 한 줄 목표
각 stage를 `제출 -> QA candidate -> PM green -> 다음 unlock` 순서로만 닫는다.

| 단계 | 1차 제출자 | 필수 동반 검토자 | 마지막 기록자 | 다음 unlock 조건 | 아직 금지 |
| --- | --- | --- | --- | --- | --- |
| `atlas-route-node` | 프로그래머 + UI + 아트 | QA | PM/기획 | `atlas archive-ready candidate` + archive path 정렬 후 `atlas-route-node green, battle-stakes-plaque unlock` | battle live edit, oath wording, queue closing 문장 |
| `battle-stakes-plaque` | 프로그래머 + UI + 아트 | QA | PM/기획 | `battle archive-ready candidate` + archive path 정렬 후 `battle-stakes-plaque green, oath-banner unlock` | oath live edit, queue closing 문장, total-green |
| `oath-banner` | 프로그래머 + UI + 아트 | QA | PM/기획 | `oath archive-ready candidate` + archive path 정렬 후 `oath-banner green, queue-closing review-only` | `Run Table token queue closing`, `ending green`, `전 UI 완료` |
| `queue-closing review` | QA | PM/기획 | PM/기획 | atlas/battle/oath green 3개 + manifest/handoff line/link alignment 후 `Run Table token queue closing` | 새 markup/CSS edit, 새 candidate, 새 capture 요구 |
| `follow-up reopen` | PM/기획 + 해당 stage owner | QA | PM/기획 | 새 bounded packet 이름 + reopen 이유 + non-change 범위 명시 | 기존 green 삭제, 큰 총괄 취소 문장 |

### 복붙용 signoff 문장
- `atlas-route-node green, battle-stakes-plaque unlock`
- `battle-stakes-plaque green, oath-banner unlock`
- `oath-banner green, queue-closing review-only`
- `Run Table token queue closing: atlas-route-node / battle-stakes-plaque / oath-banner stage green과 handoff line, archive path, portal source link alignment 확인.`
- `Run Table token follow-up reopen: <bounded-packet-name> only, previous atlas/battle/oath green 유지.`

---

## 3. queue-closing signoff 계약

## 3줄 요약
- queue closing은 `마지막 stage green`이 아니라 `마지막 운영 alignment 승인`이다.
- 그래서 QA의 `queue-closing review-ready`와 PM의 `Run Table token queue closing`은 반드시 분리 기록해야 한다.
- 여기서 가장 중요한 것은 새 화면이나 새 메모를 더하는 것이 아니라, 이미 있는 stage green 3개가 같은 뜻을 말하는지 확인하는 것이다.

### 한 줄 목표
queue closing을 **QA unlock + PM closing** 2단계로 못 박는다.

| 단계 | 필요한 입력 | 누가 멈춰 세울 수 있나 | 완료 문장 |
| --- | --- | --- | --- |
| `queue-closing review-ready` | atlas/battle/oath green 3개, 세 stage manifest `handoff line`, portal/index 링크, live archive path 정렬 | QA | `queue-closing review-ready` |
| `Run Table token queue closing` | QA review-ready + 금지 문장 미사용 + reopen 필요 여부 메모 | PM/기획 | `Run Table token queue closing: atlas-route-node / battle-stakes-plaque / oath-banner stage green과 handoff line, archive path, portal source link alignment 확인.` |
| `follow-up reopen` | 새 bounded packet 이름, 이유, 아직 금지 범위 | PM/기획 | `Run Table token follow-up reopen: <bounded-packet-name> only, previous atlas/battle/oath green 유지.` |

### queue closing에서 확인할 것
1. 세 stage green이 모두 stage명 기준으로 기록됐는가
2. `candidate`와 `green`이 같은 줄에서 섞이지 않았는가
3. `queue-closing review-ready`가 QA 문장으로 남아 있는가
4. PM closing 문장이 `Run Table complete`, `ending green`, `전 UI 완료`로 커지지 않았는가
5. follow-up이 필요해도 기존 green을 취소하지 않고 새 bounded packet으로만 reopening 하는가

---

## 4. 역할별 handoff 포인트

### 프로그래머에게 넘길 것
- 마지막 제출물은 여전히 stage archive와 hook/check/capture evidence다.
- `queue closing`을 이유로 새 code edit를 덧붙이지 않는다.
- reopen이 필요하면 stage green 취소가 아니라 새 bounded packet 제안만 남긴다.

### UI에게 넘길 것
- hierarchy verdict와 density note는 stage signoff 입력이다.
- queue closing은 visual total-green 선언이 아니다.
- `oath-banner green` 전에는 `Run Table token queue closing` 문장을 쓰지 않는다.

### 아트에게 넘길 것
- starter sanity와 asset note는 stage evidence다.
- queue closing에서 새 자산 범위를 키우지 않는다.
- follow-up이 필요하면 `새 자산 필요`가 아니라 `어떤 bounded packet에서 다시 열지`로 남긴다.

### QA에게 넘길 것
- QA는 `candidate`, `review-ready`, `unlock 가능`까지 올린다.
- PM green을 대신 쓰지 않는다.
- `queue-closing review-ready`는 세 stage alignment 검토가 끝난 뒤에만 올린다.

### PM/기획에게 넘길 것
- PM은 마지막 기록자다.
- `green`, `queue closing`, `follow-up reopen`은 모두 PM 문장으로 남긴다.
- 단, PM도 QA review-ready 없이 다음 문장을 먼저 쓰면 안 된다.

---

## 5. 금지 문장 / 금지 행동

## 3줄 요약
- 가장 흔한 실패는 `거의 끝`, `사실상 완료`, `oath도 끝났으니 closing도 같이` 같은 과장이다.
- ownership ladder는 이 과장을 막기 위해 존재한다.
- 특히 queue closing과 reopen은 wording drift가 가장 쉽게 나는 구간이다.

### 한 줄 목표
큰 초록불을 금지하고 **역할별 최대 허용 문장**만 남긴다.

### 금지 문장
- `Run Table complete`
- `ending green`
- `전 UI 완료`
- `토큰 작업 끝`
- `사실상 최종본`
- `oath까지 끝났으니 전체 종료`

### 금지 행동
- QA가 PM green을 대신 쓰기
- PM이 QA review-ready 없이 queue closing 먼저 쓰기
- queue closing review에서 새 capture/CSS edit 요구하기
- follow-up 필요를 기존 green 취소처럼 기록하기
- summary / exemplar / source packet을 같은 층위로 다시 섞기

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| QA `review-ready`와 PM `queue closing`이 다시 같은 초록불로 기록되는 위험 | `oath-banner green` 뒤 마지막 운영선이 과장돼 audit trail이 흐려진다 | signoff ladder로 역할별 최대 허용 문장과 마지막 기록 권한을 분리했다 | `oath-banner green` 직후 |
| follow-up reopen이 기존 green 취소처럼 적히는 위험 | 이전 stage evidence와 reopen 이유가 섞인다 | reopen은 새 bounded packet 이름으로만 열고 기존 green을 유지한다고 명시했다 | 첫 reopen 제안 직전 |
| 프로그래머/UI/아트가 queue closing을 이유로 새 edit를 끼워 넣는 위험 | closing review가 다시 stage 확장 턴으로 변한다 | queue-closing review는 alignment-only, 새 edit 없음 규칙을 다시 고정했다 | `queue-closing review-ready` 직전 |
| summary / exemplar / packet이 signoff ownership 없이 다시 같은 층위로 소비되는 위험 | 누가 stop line을 가진 문서인지 흐려진다 | source packet/scorecard/review/closing 문장을 ownership ladder와 함께 재정렬했다 | tracker/run log/portal wording 갱신 직전 |

---

## 7. immediate next actions

1. 다음 Run Table 작업자는 `queue readiness board -> queue closing packet -> 이 queue signoff ladder` 순서로 마지막 운영 ownership을 먼저 잠근다.
2. `oath-banner green` 뒤에는 QA가 먼저 `queue-closing review-ready`를 올리고, PM이 그 뒤에만 `Run Table token queue closing`을 기록한다.
3. follow-up이 필요하면 기존 green을 지우지 않고 새 bounded packet 이름으로만 `follow-up reopen`을 연다.
4. tracker / run log / portal / manifest / git에는 이 문서의 복붙 문장만 사용한다.
5. `Run Table complete`, `ending green`, `전 UI 완료` 같은 큰 문장은 계속 금지한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_handoff_scorecard_kr.md`
- `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 마지막 작은데 중요한 공백은 **`문서도 있고 closing 문장도 있는데, 그래서 누가 마지막으로 멈춰 세우고 누가 진짜 닫는가?`**였다. 이 문서는 그 공백을 `제출자 / 동반 검토자 / 마지막 기록자 / 다음 unlock 조건 / reopen 권한` 사다리로 다시 잠가, 마지막 운영선에서도 half-green이 total-green처럼 번지지 않게 만드는 queue signoff source-of-truth다.
