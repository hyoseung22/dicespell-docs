# DiceSpell `Run Table` Oath Banner Document Stack Packet

## 3줄 요약
- 이 문서는 `oath live kickoff`, `semantic anchor`, `archive file map`, `filled archive examples`, `handoff scorecard`, `required artifacts`, `candidate review`, `queue closing`까지 이미 충분한 상태에서도 끝까지 남아 있던 마지막 읽기 공백인 **`좋아, oath 문서는 충분해. 그런데 프로그래머 / UI / 아트 / QA / PM은 지금 baseline 설명, 실제 계약 문서, 빠른 요약, exemplar, signoff 문장을 정확히 어느 문서에서 읽고 어느 문장을 복사 기준으로 삼아야 하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 ending 설계를 더 쓰는 것이 아니라, oath 문서 묶음을 **baseline / source packet / readable companion / archive evidence / signoff** 5층으로 다시 압축해 `문서가 많아서 오히려 summary나 exemplar가 source contract처럼 읽히는` 마지막 운영 드리프트를 막는 것이다.
- 첫 화면만 읽어도 `이 문서는 왜 있는가`, `누가 무엇부터 읽어야 하는가`, `어느 문서는 참조용이고 어느 문서는 실제 계약 문서인가`, `어떤 문장을 tracker/run log/git에 재사용해야 하는가`가 바로 보여야 한다.

## 한 줄 목표
`oath-banner` 문서 스택을 **읽기 위계 + 역할별 진입 순서 + signoff 문장 기준**으로 잠가, 마지막 stage handoff가 다시 `문서 재큐레이션` 작업으로 새지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 Oath 축은 실제로 꽤 완성돼 있다.

- baseline / queue: `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`, `docs/dicespell_game_flow_token_gap_ledger_kr.md`, `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- stage 범위: `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- 착수 순서: `docs/dicespell_game_flow_token_oath_live_kickoff_packet_kr.md`
- semantic locator: `docs/dicespell_game_flow_token_oath_semantic_anchor_packet_kr.md`
- file routing: `docs/dicespell_game_flow_token_oath_archive_file_map_packet_kr.md`
- filled archive exemplar: `docs/dicespell_game_flow_token_oath_filled_archive_examples_kr.md`
- owner handoff: `docs/dicespell_game_flow_token_oath_handoff_scorecard_kr.md`
- exact artifact 계약: `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- review / candidate / green: `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- queue closing 경계: `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`

즉 아래 질문은 이미 대부분 닫혀 있다.

- oath final stage는 정확히 어디까지를 닫는가
- archive를 어떤 경로와 starter 구조로 열어야 하는가
- semantic locator와 file routing은 어디까지가 canonical path인가
- artifact 8종은 무엇이며 어느 문장 밀도까지 채워야 honest candidate인가
- 프로그래머 / UI / 아트 / QA / PM은 어떤 순서로 제출하고 어디서 candidate와 green을 나누는가
- queue closing은 왜 oath green 뒤 review-only로만 열리는가

하지만 실제 첫 live turn 직전에는 여전히 작은 운영 공백이 남는다.

1. 문서가 많아질수록 `무엇이 빠른 요약`이고 `무엇이 실제 계약 문서`인지가 흐려진다. summary만 보고 착수하면 boundary가 약해지고, exemplar만 보고 착수하면 현재 honest status보다 큰 문장이 먼저 나온다.
2. 프로그래머 / UI / 아트 / QA / PM은 같은 Oath를 보더라도 필요한 진입 문서가 다르다. 이 순서가 한 장으로 안 보이면 각자 다른 packet을 먼저 열고 다시 정렬 회의를 시작하게 된다.
3. archive exemplar와 handoff scorecard가 있어도 `baseline 설명`, `실행 packet`, `archive example`, `signoff packet`을 같은 층위로 읽으면 `archive-in-progress`, `candidate`, `green`, `queue closing review-only unlock`이 다시 섞인다.
4. 특히 포털/노션 붙여넣기 상황에서는 summary가 packet보다 먼저 눈에 띄기 쉬워, 문서가 충분한 상태에서도 오히려 stage boundary가 말로만 남을 위험이 있다.

즉 지금 필요한 것은 새 Oath 설계 문서가 아니라, **Oath 문서 묶음 자체를 source-of-truth stack으로 다시 정렬하는 packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 문서 스택 맵`, `3-1. programmer read order` | `oath banner stage`, `oath live kickoff`, `oath semantic anchor`, `oath archive file map` | summary보다 packet이 먼저고, exemplar는 현재 상태를 과장하지 않는 reference bundle이다 |
| UI | `2. 문서 스택 맵`, `3-2. UI read order`, `4. archive 기준 문서` | `oath required artifacts`, `oath filled archive examples`, `oath handoff scorecard` | capture/hierarchy note는 packet 계약을 따른 뒤 exemplar 문장 밀도로 맞춘다 |
| 아트 | `2. 문서 스택 맵`, `3-3. art read order`, `5. role-specific handoff points` | `oath required artifacts`, `oath filled archive examples`, `oath handoff scorecard` | starter sanity는 source packet과 exemplar를 같이 보되, summary만으로 충분성 판단을 내리지 않는다 |
| QA | `0. first screen 결론`, `3-4. QA read order`, `6. signoff boundary` | `oath required artifacts`, `oath handoff scorecard`, `oath candidate review`, `queue closing packet` | candidate/pass/green/unlock은 각각 다른 문서 층위에 묶여 있고, exemplar는 wording sanity 기준이지 signoff 대체 문서가 아니다 |
| PM/기획 | `0. first screen 결론`, `2. 문서 스택 맵`, `3-5. PM read order` | `queue readiness`, `oath handoff scorecard`, `oath candidate review`, `queue closing packet` | summary는 빠른 보기이고, 실제 green/unlock 판단은 source packet + scorecard + review packet을 같이 보고 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `oath-banner`를 실제로 열 때 무엇이 baseline 설명이고 무엇이 source contract인가?
- `packet`, `summary`, `example archive`, `scorecard`, `review`, `queue closing`은 어떤 순서로 읽어야 하나?
- tracker/run log/git 문장은 어느 문서 층위에서 가져와야 과장이 없나?

### 가장 짧은 답
- **packet이 계약 문서**, **summary는 빠른 읽기 문서**, **example archive는 wording density reference**, **scorecard/review/queue closing은 signoff 문서**다.
- 착수는 `oath banner stage -> oath live kickoff -> oath semantic anchor -> oath archive file map` 순서로 한다.
- archive 채움은 `required artifacts -> filled archive examples -> handoff scorecard` 순서로 한다.
- 최종 기록은 `candidate review`와 `queue closing packet` 문장을 그대로 재사용한다. exemplar 문장을 바로 green 문장으로 올리면 안 된다.

### 한 줄 판정 규칙
좋은 Oath 문서 스택 사용은 `문서를 많이 읽었다`가 아니라 **`현재 단계에 필요한 문서 층위만 읽고, 더 높은 단계 문장을 조기 사용하지 않았다`**로 판단한다.

---

## 1. core structure

## 3줄 요약
- Oath 문서 스택은 크게 `baseline`, `source packet`, `readable companion`, `archive evidence`, `signoff` 다섯 층으로 읽는다.
- 층위가 섞이는 순간 가장 먼저 무너지는 것은 `oath-only boundary`와 `candidate / green / queue closing review-only unlock` 구분이다.
- 따라서 문서 수가 많을수록 더 많은 요약이 아니라, 어떤 문서를 언제 열지에 대한 정렬표가 먼저 필요하다.

### 한 줄 목표
Oath 문서 묶음을 `문서 종류별 책임`으로 다시 잠근다.

### 문서 층위 정의
| 층위 | 의미 | 여기에 속하는 Oath 문서 | 잘못 읽었을 때 생기는 문제 |
| --- | --- | --- | --- |
| baseline | 지금 어떤 stage가 열려 있는지, 전체 queue가 어떻게 잠겨 있는지 설명 | `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`, `docs/dicespell_game_flow_token_gap_ledger_kr.md`, `docs/dicespell_game_flow_token_queue_readiness_board_kr.md` | Oath를 전체 ending polish나 queue closing 준비와 같은 뜻으로 오독한다 |
| source packet | 실제 stage 계약 | `oath banner stage`, `oath live kickoff`, `oath semantic anchor`, `oath archive file map`, `oath required artifacts`, `oath candidate review`, `queue closing` | summary/예시만 보고 boundary나 gate를 임의 해석한다 |
| readable companion | 빠른 이해/공유용 | 각 `*_summary_kr.md` | summary를 계약 문서처럼 써서 queue closing·green gate를 놓친다 |
| archive evidence | 실제 제출물 예시/참조 | `oath filled archive examples`, `docs/artifacts/run-table-token-delivery/examples/oath-banner/` | 현재 honest status보다 더 큰 wording을 조기 차용한다 |
| signoff | owner handoff / final gate | `oath handoff scorecard`, `oath candidate review`, `queue closing packet` | `candidate`, `green`, `queue closing review-only unlock`을 같은 단계로 쓴다 |

---

## 2. Oath document stack map

## 3줄 요약
- 아래 표는 문서 이름을 다시 나열하려는 것이 아니라, `언제 여는가`, `무엇을 복사해도 되는가`, `무엇을 아직 복사하면 안 되는가`를 한 번에 보여 주려는 표다.
- implementer는 보통 이 표 하나만으로도 `오늘은 어디까지 읽고 멈춰야 하는가`를 판단할 수 있어야 한다.
- 특히 `filled archive examples`, `handoff scorecard`, `candidate review`, `queue closing`은 source packet을 대체하지 않고, 이미 잠긴 범위를 실행과 기록으로 옮길 때만 쓴다.

### 한 줄 목표
문서별 역할과 열기 순서를 stage 실행 순서와 같은 언어로 정렬한다.

| 문서 | 층위 | 언제 연다 | 여기서 복사해도 되는 것 | 여기서 복사하면 안 되는 것 |
| --- | --- | --- | --- | --- |
| `dicespell_game_flow_token_source_of_truth_map_kr.md` | baseline | Oath 착수 전 | baseline-vs-target 분리 원칙 | Oath green 문장 |
| `dicespell_game_flow_token_gap_ledger_kr.md` | baseline | Oath 착수 전 | 아직 안 닫힌 live gap과 owner | exemplar wording |
| `dicespell_game_flow_token_queue_readiness_board_kr.md` | baseline | Oath 착수 전 | `oath live-ready`, `queue closing review-only` gate | `green`/`unlock` 복붙 문장 |
| `dicespell_game_flow_token_oath_banner_stage_packet_kr.md` | source packet | 가장 먼저 | stage 범위, 금지 범위 | archive wording 예시 |
| `dicespell_game_flow_token_oath_live_kickoff_packet_kr.md` | source packet | archive 열기 직전 | kickoff 5단계 순서 | candidate/green/queue closing line |
| `dicespell_game_flow_token_oath_semantic_anchor_packet_kr.md` | source packet | 실제 코드/DOM 다시 찾을 때 | locator 5축 | 구현 완료처럼 읽히는 상태 설명 |
| `dicespell_game_flow_token_oath_archive_file_map_packet_kr.md` | source packet | stage 폴더 만들 때 | live root, reference-vs-live path | exemplar 문장 자체 |
| `dicespell_game_flow_token_oath_required_artifacts_packet_kr.md` | source packet | archive 채움 시작 시 | artifact 8종 정의, 필수 질문 | exemplar 수준 wording |
| `dicespell_game_flow_token_oath_filled_archive_examples_kr.md` | archive evidence | artifact 정의 뒤 | honest wording density, file-level bundle 감 | 현재 상태보다 높은 status |
| `dicespell_game_flow_token_oath_handoff_scorecard_kr.md` | signoff | artifact 정렬 뒤 | owner 제출 순서, 복붙 문장 | baseline 큐 대체 |
| `dicespell_game_flow_token_oath_candidate_review_packet_kr.md` | signoff | review-ready 직전/직후 | candidate -> green ladder | archive bootstrap 규칙 |
| `dicespell_game_flow_token_queue_closing_packet_kr.md` | signoff | oath green 뒤 다음 단계 판단 시 | queue closing review-only unlock 문장 | oath stage source contract 대체 |

---

## 3. 역할별 읽기 순서

## 3-1. programmer read order

### 3줄 요약
- 프로그래머는 packet을 먼저 읽고, exemplar는 나중에 wording sanity 확인용으로만 본다.
- Oath에서 프로그래머가 가장 먼저 잠가야 하는 것은 `range`와 `locator`이지, 예쁜 evidence 문장이 아니다.
- 가장 흔한 실수는 `filled archive examples`를 먼저 보고 manifest/status를 과장하는 것이다.

### 한 줄 목표
프로그래머는 `범위 -> kickoff -> anchor/file map -> artifacts -> exemplar -> scorecard/review` 순서로만 읽는다.

### 추천 읽기 순서
1. `source-of-truth map`
2. `gap ledger`
3. `queue readiness board`
4. `oath banner stage packet`
5. `oath live kickoff`
6. `oath semantic anchor`
7. `oath archive file map`
8. `oath required artifacts`
9. `oath filled archive examples`
10. `oath handoff scorecard`
11. `oath candidate review`
12. `queue closing packet`

### 프로그래머가 가져갈 결론
- exemplar는 `좋은 문장 밀도`를 보여 주지만, 현재 bundle 상태보다 앞선 `status`를 선복사하면 안 된다.
- scorecard/review/queue closing은 구현 바닥 제출을 잠근 뒤에만 연다.

## 3-2. UI read order

### 3줄 요약
- UI는 source packet 없이 exemplar만 보면 capture를 예쁜 ending 샷으로 오독하기 쉽다.
- UI evidence는 어디까지나 `overview / banner-focus` 질문에 답하는 stage 제출물이다.
- hierarchy note도 summary가 아니라 packet과 exemplar를 같이 보고 써야 한다.

### 한 줄 목표
UI는 `required artifacts -> filled examples -> handoff scorecard`를 축으로 읽는다.

### 추천 읽기 순서
1. `oath banner stage summary`
2. `oath required artifacts packet`
3. `oath filled archive examples`
4. `oath handoff scorecard`
5. `oath candidate review`

### UI가 가져갈 결론
- capture caption은 exemplar처럼 짧아도 좋지만 반드시 질문/판정이 들어가야 한다.
- review-ready와 green 문장은 UI가 먼저 쓰지 않는다.

## 3-3. art read order

### 3줄 요약
- 아트는 이번 stage에서 큰 신규 자산 제안보다 `Oath Banner Starter 충분성` sanity를 읽어야 한다.
- summary만 보면 감도 중심으로 판단하기 쉽고, packet만 보면 wording density가 빠질 수 있다.
- 따라서 아트는 packet과 exemplar를 같이 본다.

### 한 줄 목표
아트는 `source contract`와 `filled archive example`을 같이 읽어 작은 충분성 언어를 유지한다.

### 추천 읽기 순서
1. `oath required artifacts packet`
2. `oath filled archive examples`
3. `oath handoff scorecard`
4. `oath candidate review`

### 아트가 가져갈 결론
- art sanity note는 backlog나 발주 문서가 아니라 이번 stage closing evidence다.
- `ending shell 리뉴얼`, `reward/shop side family 재정리`, `queue closing 공용 shell`은 아직 source contract 바깥이다.

## 3-4. QA read order

### 3줄 요약
- QA는 가장 쉽게 `exemplar 문장`과 `candidate/green` 문장을 같은 층위로 읽을 수 있다.
- 하지만 실제 검수는 artifacts completeness와 wording alignment를 본 뒤, candidate / green / queue closing unlock을 분리해야 한다.
- 따라서 QA는 signoff 문서를 artifact 정의와 같이 읽어야 한다.

### 한 줄 목표
QA는 `artifacts -> exemplar -> scorecard -> review -> queue closing` 순으로 읽고 candidate/green/unlock을 분리한다.

### 추천 읽기 순서
1. `oath required artifacts packet`
2. `oath filled archive examples`
3. `oath handoff scorecard`
4. `oath candidate review`
5. `queue closing packet`

### QA가 가져갈 결론
- exemplar는 `candidate 어조`를 알려 주지만, green 권한까지 대체하지 않는다.
- `oath-banner archive-ready candidate`는 QA line이고, `oath-banner green`과 `queue closing review-only unlock`은 PM/queue line이다.

## 3-5. PM read order

### 3줄 요약
- PM은 보통 summary를 먼저 보게 되지만, green/unlock 판단은 summary가 아니라 source packet과 signoff 문서를 기준으로 해야 한다.
- Oath는 특히 문서가 충분해서 `이 정도면 거의 green` 착시가 쉽게 생긴다.
- PM은 문서 수를 줄여 읽는 대신, 층위를 구분해 읽어야 한다.

### 한 줄 목표
PM은 `summary -> source packet -> signoff` 순서로 읽되 final green은 마지막에만 쓴다.

### 추천 읽기 순서
1. `oath banner stage summary`
2. `oath live kickoff summary`
3. `oath banner stage packet`
4. `oath handoff scorecard`
5. `oath candidate review`
6. `queue closing packet`

### PM이 가져갈 결론
- `summary`는 방향 확인용이고, `green / queue closing review-only unlock`은 scorecard + candidate review + queue closing packet 문장을 그대로 재사용해야 한다.
- exemplar 문장이 좋다고 해서 현재 bundle이 green인 것은 아니다.

---

## 4. archive 기준 문서

## 3줄 요약
- Oath archive를 실제로 열 때는 source packet과 example archive를 함께 봐야 하지만, 둘의 역할은 다르다.
- source packet은 `무엇이 있어야 하는지`를, example archive는 `어느 톤과 밀도로 채우는지`를 알려 준다.
- 둘을 합치되, source packet을 example로 대체하거나 example을 source packet처럼 쓰면 안 된다.

### 한 줄 목표
archive 작성 시 `계약 문서`와 `예시 문서`의 역할을 분리한다.

| 상황 | 먼저 보는 문서 | 그 다음 보는 문서 | 이유 |
| --- | --- | --- | --- |
| stage 폴더 생성 | `oath archive file map packet` | `oath filled archive examples` | 예시보다 먼저 구조와 경로를 잠근다 |
| manifest/status 정렬 | `oath required artifacts packet` | `examples/oath-banner/manifest.md` | 현재 honest status를 먼저 결정하고 예시 문장 밀도를 맞춘다 |
| capture caption 작성 | `oath required artifacts packet` | `oath filled archive examples`의 capture 예시 | 질문/판정 구조를 먼저 보고 문장 톤을 맞춘다 |
| owner handoff line 작성 | `oath handoff scorecard` | `oath candidate review packet` | 기록 권한과 단계명을 먼저 잠근다 |
| queue closing 문장 준비 | `queue closing packet` | `oath candidate review packet` | oath green 뒤에만 열리는 다음 단계 언어를 분리한다 |

---

## 5. role-specific handoff points

## 3줄 요약
- 이 문서의 목적은 읽기 순서 정리지만, 결국 읽기 순서는 handoff 품질로 이어져야 한다.
- 아래 항목은 각 owner가 어디서부터는 자기 문서가 아니라고 멈춰야 하는지를 다시 잠근다.
- Oath는 문서가 많아서 무엇을 더 읽느냐보다, 어디서 멈추느냐가 더 중요하다.

### 한 줄 목표
owner별로 `내 문서`와 `참조 문서`의 경계를 다시 잠근다.

- **프로그래머에게 넘길 것**
  - source packet이 우선이다.
  - filled archive examples는 현재 status를 과장하지 않는 범위에서만 참조한다.
  - candidate review와 queue closing 문장은 구현 바닥 제출 전에 쓰지 않는다.
- **UI에게 넘길 것**
  - required artifacts와 exemplar를 같이 본다.
  - capture는 signoff 문장 대체물이 아니다.
  - ending helper, reward/shop side family, queue closing wording은 Oath evidence에 섞지 않는다.
- **아트에게 넘길 것**
  - art sanity는 작은 충분성 판단이다.
  - summary의 문맥 문장을 새 자산 요구로 확대하지 않는다.
  - exemplar note보다 더 큰 연출 제안은 별도 reopen 전까지 남기지 않는다.
- **QA에게 넘길 것**
  - artifacts completeness와 wording alignment를 먼저 본다.
  - `candidate`, `green`, `queue closing review-only unlock`은 서로 다른 단계명이다.
  - example archive는 candidate 어조 참고용이고, stage 판정 권한은 scorecard/review/queue closing packet에 있다.
- **PM에게 넘길 것**
  - summary는 빠른 확인, green은 source packet + signoff 문서 기준이다.
  - `Overrun Oath 진행`, `ending 거의 완료`, `Run Table complete` 같은 큰 문장은 여전히 금지다.
  - queue closing은 여전히 Oath green 뒤 review-only로만 열린다.

---

## 6. signoff boundary

## 3줄 요약
- Oath 문서 스택이 두꺼워질수록 가장 먼저 흐려지는 것은 signoff 층위다.
- exemplar, scorecard, review packet, queue closing packet은 모두 마지막 단계 근처에서 쓰이지만 서로 역할이 다르다.
- 이 경계를 다시 한 장으로 고정해 둬야 candidate/green/unlock이 다시 한 줄로 압축되지 않는다.

### 한 줄 목표
`example`, `scorecard`, `review`, `queue closing`의 signoff 역할을 분리한다.

| 문서 | 하는 일 | 하지 않는 일 |
| --- | --- | --- |
| `oath filled archive examples` | wording density 기준을 보여 준다 | candidate/green/unlock 권한을 주지 않는다 |
| `oath handoff scorecard` | owner 제출 순서와 복붙 문장을 준다 | artifact 구조를 새로 정의하지 않는다 |
| `oath candidate review packet` | `candidate -> green` ladder를 잠근다 | starter bundle 작성법을 다시 설명하지 않는다 |
| `queue closing packet` | oath green 뒤 `review-only unlock` 문장을 잠근다 | oath stage source contract를 대체하지 않는다 |

### 복붙 원칙
1. manifest/status 문장은 `required artifacts packet` 기준으로 쓴다.
2. archive note/caption 톤은 `filled archive examples` 기준으로 맞춘다.
3. owner 제출 순서와 handoff line은 `handoff scorecard` 기준으로 쓴다.
4. candidate/green 구분은 `candidate review packet` 기준으로 쓴다.
5. `Run Table token queue closing review-only unlock`은 `queue closing packet` 기준으로 oath green 뒤에만 쓴다.

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| Oath 문서가 충분해진 뒤 summary와 source packet, exemplar와 signoff 문서가 같은 층위로 읽히는 위험 | implementer/UI/아트/QA/PM이 각자 다른 문서에서 착수/기록 문장을 가져와 `oath-only boundary`와 `green` 계층이 다시 흐려질 수 있다 | 이번 슬롯의 `docs/dicespell_game_flow_token_oath_document_stack_packet_kr.md` / `summary`로 Oath 문서를 baseline / source packet / readable companion / archive evidence / signoff 5층으로 다시 압축했다 | 다음 실제 Oath live turn 직전 |
| filled archive examples가 있어도 implementer가 example을 source contract처럼 읽는 위험 | 현재 honest status보다 큰 `candidate`, `green`, `queue closing unlock` 문장이 조기 사용될 수 있다 | `archive 기준 문서`와 `signoff boundary` 섹션으로 example / scorecard / review / queue closing 역할을 분리 고정했다 | manifest/status 초안 작성 직전 |
| 포털/노션에서는 summary가 packet보다 먼저 보이는 위험 | 빠른 읽기 문서만 보고 착수/승인 판단을 내릴 수 있다 | 역할별 읽기 순서와 role-specific handoff point를 명시해 각 owner가 어떤 문서를 source로 삼아야 하는지 다시 잠갔다 | 포털/노션 handoff 직전 |

---

## 8. immediate next actions

1. 다음 Oath 실제 turn은 `source-of-truth map -> gap ledger -> queue readiness board -> oath document stack packet -> oath banner stage packet -> oath live kickoff -> oath semantic anchor -> oath archive file map -> oath required artifacts -> oath filled archive examples -> oath handoff scorecard -> oath candidate review -> queue closing packet` 순서로 읽는다.
2. 프로그래머는 source packet을 먼저 잠그고 exemplar는 wording sanity reference로만 쓴다.
3. UI/아트는 required artifacts와 filled archive examples를 같이 보되, scorecard 전에는 signoff 문장을 쓰지 않는다.
4. QA/PM은 candidate/green/unlock을 여전히 분리 기록한다. example archive나 summary 문장을 final green line으로 직접 승격하지 않는다.
5. queue closing은 여전히 Oath green 뒤 review-only로만 열린다. 이 문서는 읽기 위계를 잠그는 문서지, stage unlock을 당기는 문서가 아니다.
