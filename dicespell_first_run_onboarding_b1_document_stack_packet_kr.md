# DiceSpell 첫 런 온보딩 `B-1 library` Document Stack Packet

## 3줄 요약
- 이 문서는 `B-1 library packet`, `live kickoff`, `archive bootstrap`, `artifact stub`, `manifest`, `semantic anchor`, `required artifacts`, `filled archive examples`, `handoff scorecard`, `review packet`까지 이미 충분한 상태에서도 끝까지 남아 있던 마지막 읽기 공백인 **`좋아, B-1 문서는 충분해. 그런데 implementer / UI / 아트 / QA / PM은 지금 baseline 설명, 실제 계약 문서, 빠른 요약, archive exemplar를 정확히 어느 문서에서 읽고 어느 문장을 복사 기준으로 삼아야 하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 primer를 더 설계하는 것이 아니라, B-1 문서 묶음을 **baseline / source packet / readable companion / execution archive / signoff doc** 5층으로 다시 압축해 `문서가 많아서 오히려 summary만 보고 착수하거나, exemplar를 source contract처럼 읽는` 마지막 운영 드리프트를 막는 것이다.
- 첫 화면만 읽어도 `누가 무엇부터 읽어야 하는지`, `무슨 문서는 참조용이고 무슨 문서는 실제 계약 문서인지`, `어떤 파일과 문장을 tracker/run log/git에 재사용해야 하는지`가 바로 보여야 한다.

## 한 줄 목표
`B-1 library` 문서 스택을 **읽기 위계 + 역할별 진입 순서 + stage archive 기준 문서**로 잠가, 첫 실전 handoff가 다시 문서 재큐레이션 작업으로 새지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 B-1 문서 스택은 실제로 꽤 완성돼 있다.

- 범위 계약: `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- 착수 계약: `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- archive 시작: `docs/dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md`
- starter text: `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- manifest 작성: `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- semantic locator: `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- required artifacts: `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- filled archive exemplar: `docs/dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md`
- owner handoff: `docs/dicespell_first_run_onboarding_b1_handoff_scorecard_kr.md`
- signoff/review: `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`

즉 아래 질문은 이미 대부분 닫혀 있다.

- B-1은 정확히 어디까지만 닫는가
- archive를 어떤 starter bundle로 시작하는가
- manifest와 stub 첫 줄은 무엇인가
- semantic anchor는 어디인가
- artifact 8종은 무엇이고 wording density는 어디까지인가
- review-ready / QA pass / PM green / B-2 unlock은 누가 언제 말하는가

하지만 실제 첫 live turn 직전에는 여전히 작은 운영 공백이 남는다.

1. 문서가 많아질수록 `무엇이 빠른 요약`이고 `무엇이 실제 계약 문서`인지가 흐려진다. summary만 보고 착수하면 boundary가 약해지고, exemplar만 보고 착수하면 현재 honest status보다 큰 문장이 먼저 나온다.
2. 프로그래머 / UI / 아트 / QA / PM은 같은 B-1을 보더라도 필요한 진입 문서가 다르다. 이 순서가 한 장으로 안 보이면 각자 다른 packet을 먼저 열고 다시 회의를 시작하게 된다.
3. archive exemplar와 handoff scorecard가 있어도 `baseline 설명`, `실행 packet`, `archive example`, `최종 signoff packet`을 같은 층위로 읽으면 review-ready와 green이 다시 섞인다.
4. 특히 포털/노션 붙여넣기 상황에서는 summary가 packet보다 먼저 눈에 띄기 쉬워, 문서가 충분한 상태에서도 오히려 stage boundary가 말로만 남을 위험이 있다.

즉 지금 필요한 것은 새 B-1 설계 문서가 아니라, **B-1 문서 묶음 자체를 source-of-truth stack으로 다시 정렬하는 packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 문서 스택 맵`, `3-1. programmer read order` | `B-1 library packet`, `live kickoff`, `manifest`, `semantic anchor` | summary보다 packet이 먼저고, exemplar는 현재 상태를 과장하지 않는 참고 bundle이다 |
| UI | `2. 문서 스택 맵`, `3-2. UI read order`, `4. archive 기준 문서` | `filled archive examples`, `handoff scorecard`, `review packet` | capture/hierarchy note는 packet 계약을 따른 뒤 exemplar 문장 밀도로 맞춘다 |
| 아트 | `2. 문서 스택 맵`, `3-3. art read order`, `5. role-specific handoff points` | `artifact stub`, `filled archive examples`, `handoff scorecard` | art sanity는 source packet과 exemplar를 같이 보되, summary만으로 충분성 판단을 내리지 않는다 |
| QA | `0. first screen 결론`, `3-4. QA read order`, `6. signoff boundary` | `required artifacts`, `handoff scorecard`, `review packet` | candidate/pass/green은 각각 다른 문서 층위에 묶여 있고, exemplar는 wording sanity 기준이지 signoff 대체 문서가 아니다 |
| PM/기획 | `0. first screen 결론`, `2. 문서 스택 맵`, `3-5. PM read order` | `runtime stack`, `B-1 review packet`, `handoff scorecard` | summary는 빠른 보기이고, 실제 green/unlock 판단은 source packet + review packet + scorecard를 같이 보고 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `B-1 library`를 실제로 열 때 무엇이 baseline 설명이고 무엇이 source contract인가?
- `packet`, `summary`, `example archive`, `scorecard`, `review`는 어떤 순서로 읽어야 하나?
- tracker/run log/git 문장은 어느 문서 층위에서 가져와야 과장이 없나?

### 가장 짧은 답
- **packet이 계약 문서**, **summary는 빠른 읽기 문서**, **example archive는 wording density reference**, **scorecard/review는 handoff/signoff 문서**다.
- 착수는 `library packet -> live kickoff -> archive bootstrap -> stub -> manifest -> semantic anchor` 순서로 한다.
- archive 채움은 `required artifacts -> filled archive examples -> handoff scorecard` 순서로 한다.
- 최종 기록은 `review packet`과 `handoff scorecard` 문장을 그대로 재사용한다. exemplar 문장을 바로 green 문장으로 올리면 안 된다.

### 한 줄 판정 규칙
좋은 B-1 문서 스택 사용은 `문서를 많이 읽었다`가 아니라 **`현재 상태에 필요한 문서 층위만 읽고, 더 높은 단계 문장을 조기 사용하지 않았다`**로 판단한다.

---

## 1. core structure

## 3줄 요약
- B-1 문서 스택은 크게 `baseline`, `source packet`, `readable companion`, `archive evidence`, `signoff` 다섯 층으로 읽는다.
- 층위가 섞이는 순간 가장 먼저 무너지는 것은 `library-only boundary`와 `review-ready / QA pass / PM green` 구분이다.
- 따라서 문서 수가 많을수록 더 많은 요약이 아니라, 어떤 문서를 언제 열지에 대한 정렬표가 먼저 필요하다.

### 한 줄 목표
B-1 문서 묶음을 `문서 종류별 책임`으로 다시 잠근다.

### 문서 층위 정의
| 층위 | 의미 | 여기에 속하는 B-1 문서 | 잘못 읽었을 때 생기는 문제 |
| --- | --- | --- | --- |
| baseline | 지금 프로젝트의 전체 실행 큐 설명 | `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`, `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md` | B-1을 전체 온보딩 맥락 없이 단일 예쁘장한 task처럼 읽는다 |
| source packet | 실제 stage 계약 | `B-1 library`, `live kickoff`, `archive bootstrap`, `artifact stub`, `manifest`, `semantic anchor`, `required artifacts`, `review` | summary/예시만 보고 boundary나 gate를 임의 해석한다 |
| readable companion | 빠른 이해/공유용 | 각 `*_summary_kr.md` | summary를 계약 문서처럼 써서 누락된 gate를 놓친다 |
| archive evidence | 실제 제출물 예시/템플릿 | `filled archive examples`, `docs/artifacts/examples/b1-library/`, `docs/artifacts/stubs/` | 현재 honest status보다 더 큰 wording을 조기 차용한다 |
| signoff | owner handoff / final gate | `B-1 handoff scorecard`, `B-1 review packet` | review-ready, QA pass, PM green을 같은 단계로 쓴다 |

---

## 2. B-1 document stack map

## 3줄 요약
- 아래 표는 문서 이름을 다시 나열하려는 것이 아니라, `언제 여는가`, `무엇을 복사해도 되는가`, `무엇을 아직 복사하면 안 되는가`를 한 번에 보여 주려는 표다.
- implementer는 보통 이 표 하나만으로도 `오늘은 어디까지 읽고 멈춰야 하는가`를 판단할 수 있어야 한다.
- 특히 `filled archive examples`와 `handoff scorecard`는 source packet을 대체하지 않고, 이미 잠긴 범위를 실행과 기록으로 옮길 때만 쓴다.

### 한 줄 목표
문서별 역할과 열기 순서를 stage 실행 순서와 같은 언어로 정렬한다.

| 문서 | 층위 | 언제 연다 | 여기서 복사해도 되는 것 | 여기서 복사하면 안 되는 것 |
| --- | --- | --- | --- | --- |
| `dicespell_overrun_onboarding_cutover_readiness_board_kr.md` | baseline | B-1 착수 전 | 현재 열려 있는 단계가 B-1인지 여부 | B-1 내부 artifact wording |
| `dicespell_first_run_onboarding_runtime_stack_packet_kr.md` | baseline | B-1 착수 전 | B-1이 B-track 첫 commit이라는 큐 | review-ready / green 세부 문장 |
| `dicespell_first_run_onboarding_b1_library_packet_kr.md` | source packet | 가장 먼저 | stage 범위, 금지 범위, acceptance 축 | archive wording 예시 |
| `dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md` | source packet | archive 열기 직전 | kickoff 5단계 순서 | review-ready/green line |
| `dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md` | source packet | stage 폴더 만들 때 | starter bundle 구조, 경로 | exemplar 문장 자체 |
| `dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md` | source packet | stub 파일 채울 때 | 첫 줄, 금지 문장 | 최종 verdict 문장 |
| `dicespell_first_run_onboarding_b1_manifest_packet_kr.md` | source packet | manifest 작성 때 | status/owner summary/boundary/next allowed step/handoff line 구조 | green 선행 문장 |
| `dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md` | source packet | 실제 코드/DOM 다시 찾을 때 | locator 5축 | 구현 완료처럼 읽히는 상태 설명 |
| `dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md` | source packet | archive 채움 시작 시 | artifact 8종 정의, 필수 질문 | exemplar 수준 wording |
| `dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md` | archive evidence | artifact 정의 뒤 | honest wording density, file-level bundle 감 | 현재 상태보다 높은 status |
| `dicespell_first_run_onboarding_b1_handoff_scorecard_kr.md` | signoff | artifact 정렬 뒤 | owner 제출 순서, 복붙 문장 | baseline 큐 대체 |
| `dicespell_first_run_onboarding_b1_review_packet_kr.md` | signoff | review-ready 직전/직후 | review-ready -> QA pass -> PM green 순서 | archive bootstrap 규칙 대체 |

---

## 3. 역할별 읽기 순서

## 3-1. programmer read order

### 3줄 요약
- 프로그래머는 packet을 먼저 읽고, exemplar는 나중에 wording sanity 확인용으로만 본다.
- B-1에서 프로그래머가 가장 먼저 잠가야 하는 것은 `range`와 `locator`이지, 예쁜 evidence 문장이 아니다.
- 가장 흔한 실수는 `filled archive examples`를 먼저 보고 manifest/status를 과장하는 것이다.

### 한 줄 목표
프로그래머는 `범위 -> kickoff -> manifest/stub -> semantic anchor -> artifacts -> exemplar` 순서로만 읽는다.

### 추천 읽기 순서
1. `readiness board`
2. `runtime stack`
3. `B-1 library packet`
4. `B-1 live kickoff`
5. `B-1 archive bootstrap`
6. `B-1 artifact stub`
7. `B-1 manifest`
8. `B-1 semantic anchor`
9. `B-1 required artifacts`
10. `B-1 filled archive examples`
11. `B-1 handoff scorecard`
12. `B-1 review packet`

### 프로그래머가 가져갈 결론
- exemplar는 `좋은 문장 밀도`를 보여 주지만, 현재 bundle 상태보다 앞선 `status`를 선복사하면 안 된다.
- scorecard와 review는 구현 바닥을 잠근 뒤에만 연다.

## 3-2. UI read order

### 3줄 요약
- UI는 source packet 없이 exemplar만 보면 capture를 예쁜 샷으로 오독하기 쉽다.
- UI evidence는 어디까지나 `first-open / return-state` 질문에 답하는 단계 제출물이다.
- hierarchy note도 summary가 아니라 packet과 exemplar를 같이 보고 써야 한다.

### 한 줄 목표
UI는 `required artifacts -> filled examples -> handoff scorecard`를 축으로 읽는다.

### 추천 읽기 순서
1. `B-1 library summary`
2. `B-1 required artifacts packet`
3. `B-1 filled archive examples`
4. `B-1 handoff scorecard`
5. `B-1 review packet`

### UI가 가져갈 결론
- capture caption은 exemplar처럼 짧아도 좋지만 반드시 질문/판정이 들어가야 한다.
- review-ready와 green 문장은 UI가 먼저 쓰지 않는다.

## 3-3. art read order

### 3줄 요약
- 아트는 이번 stage에서 큰 신규 자산 제안보다 `primer-library 충분성` sanity를 읽어야 한다.
- summary만 보면 감도 중심으로 판단하기 쉽고, packet만 보면 wording density가 빠질 수 있다.
- 따라서 아트는 packet과 exemplar를 같이 본다.

### 한 줄 목표
아트는 `stub/source contract`와 `filled archive example`을 같이 읽어 작은 충분성 언어를 유지한다.

### 추천 읽기 순서
1. `B-1 artifact stub packet`
2. `B-1 required artifacts packet`
3. `B-1 filled archive examples`
4. `B-1 handoff scorecard`

### 아트가 가져갈 결론
- art sanity note는 backlog나 발주 문서가 아니라 이번 stage closing evidence다.
- `추천 책 연출`, `새 key art`, `shelf 리디자인`은 아직 summary에서 보여도 source contract 바깥이다.

## 3-4. QA read order

### 3줄 요약
- QA는 가장 쉽게 `exemplar 문장`과 `pass/green 문장`을 같은 층위로 읽을 수 있다.
- 하지만 실제 검수는 artifacts completeness와 wording alignment를 본 뒤, review-ready / QA pass / PM green을 분리해야 한다.
- 따라서 QA는 signoff 문서를 source packet보다 늦게가 아니라, artifact 정의와 같이 읽어야 한다.

### 한 줄 목표
QA는 `artifacts -> exemplar -> scorecard -> review` 순으로 읽고 candidate/pass/green을 분리한다.

### 추천 읽기 순서
1. `B-1 required artifacts packet`
2. `B-1 filled archive examples`
3. `B-1 handoff scorecard`
4. `B-1 review packet`

### QA가 가져갈 결론
- exemplar는 `pass 기준의 어조`를 알려 주지만, green 권한까지 대체하지 않는다.
- `QA pass`는 QA line이고, `B-1 library green`은 PM line이다.

## 3-5. PM read order

### 3줄 요약
- PM은 보통 summary를 먼저 보게 되지만, green/unlock 판단은 summary가 아니라 source packet과 signoff 문서를 기준으로 해야 한다.
- B-1은 특히 문서가 충분해서 `이 정도면 거의 green` 착시가 쉽게 생긴다.
- PM은 문서 수를 줄여 읽는 대신, 층위를 구분해 읽어야 한다.

### 한 줄 목표
PM은 `summary -> source packet -> signoff` 순서로 읽되 final green은 마지막에만 쓴다.

### 추천 읽기 순서
1. `B-1 library summary`
2. `B-1 live kickoff summary`
3. `B-1 library packet`
4. `B-1 handoff scorecard`
5. `B-1 review packet`

### PM이 가져갈 결론
- `summary`는 방향 확인용이고, `green / B-2 unlock`은 scorecard + review packet 문장을 그대로 재사용해야 한다.
- exemplar 문장이 좋다고 해서 현재 bundle이 green인 것은 아니다.

---

## 4. archive 기준 문서

## 3줄 요약
- B-1 archive를 실제로 열 때는 source packet과 example archive를 함께 봐야 하지만, 둘의 역할은 다르다.
- source packet은 `무엇이 있어야 하는지`를, example archive는 `어느 톤과 밀도로 채우는지`를 알려 준다.
- 둘을 합치되, source packet을 example로 대체하거나 example을 source packet처럼 쓰면 안 된다.

### 한 줄 목표
archive 작성 시 `계약 문서`와 `예시 문서`의 역할을 분리한다.

| 상황 | 먼저 보는 문서 | 그 다음 보는 문서 | 이유 |
| --- | --- | --- | --- |
| stage 폴더 생성 | `archive bootstrap packet` | `filled archive examples` | 예시보다 먼저 구조와 경로를 잠근다 |
| manifest 첫 줄 작성 | `manifest packet` | `stage_manifest_b1_library_example.md` | 현재 honest status를 먼저 결정하고 예시 문장 밀도를 맞춘다 |
| stub 첫 줄 작성 | `artifact stub packet` | `docs/artifacts/stubs/` starter kit | template를 먼저 고정한 뒤 stage-specific wording을 맞춘다 |
| capture caption 작성 | `required artifacts packet` | `filled archive examples`의 capture 예시 | 질문/판정 구조를 먼저 보고 문장 톤을 맞춘다 |
| final handoff line 작성 | `handoff scorecard` | `review packet` | 기록 권한과 단계명을 먼저 잠근다 |

---

## 5. role-specific handoff points

## 3줄 요약
- 이 문서의 목적은 읽기 순서 정리지만, 결국 읽기 순서는 handoff 품질로 이어져야 한다.
- 아래 항목은 각 owner가 어디서부터는 자기 문서가 아니라고 멈춰야 하는지를 다시 잠근다.
- B-1은 문서가 많아서 무엇을 더 읽느냐보다, 어디서 멈추느냐가 더 중요하다.

### 한 줄 목표
owner별로 `내 문서`와 `참조 문서`의 경계를 다시 잠근다.

- **프로그래머에게 넘길 것**
  - source packet이 우선이다.
  - filled archive examples는 현재 status를 과장하지 않는 범위에서만 참조한다.
  - review packet 문장은 구현 바닥 제출 전에 쓰지 않는다.
- **UI에게 넘길 것**
  - required artifacts와 exemplar를 같이 본다.
  - capture는 signoff 문장 대체물이 아니다.
  - `battle/reward/shop/ending` 캡처는 B-1 evidence에 섞지 않는다.
- **아트에게 넘길 것**
  - art sanity는 작은 충분성 판단이다.
  - summary의 맥락 문장을 새 자산 요구로 확대하지 않는다.
  - exemplar note보다 더 큰 연출 제안은 별도 reopen 전까지 남기지 않는다.
- **QA에게 넘길 것**
  - artifacts completeness와 wording alignment를 먼저 본다.
  - `review-ready`, `QA pass`, `PM green`은 서로 다른 단계명이다.
  - example archive는 pass 어조 참고용이고, stage 판정 권한은 scorecard/review packet에 있다.
- **PM에게 넘길 것**
  - summary는 빠른 확인, green은 source packet + signoff 문서 기준이다.
  - `온보딩 진행`, `library 거의 완료` 같은 큰 문장은 여전히 금지다.
  - B-2 unlock은 B-1 green 뒤 마지막 문장으로만 남긴다.

---

## 6. signoff boundary

## 3줄 요약
- B-1 문서 스택이 두꺼워질수록 가장 먼저 흐려지는 것은 signoff 층위다.
- exemplar, scorecard, review packet은 모두 마지막 단계 근처에서 쓰이지만 서로 역할이 다르다.
- 이 경계를 다시 한 장으로 고정해 둬야 review-ready와 green이 다시 한 줄로 압축되지 않는다.

### 한 줄 목표
`example`, `scorecard`, `review`의 signoff 역할을 분리한다.

| 문서 | 하는 일 | 하지 않는 일 |
| --- | --- | --- |
| `filled archive examples` | wording density 기준을 보여 준다 | review-ready / QA pass / green 권한을 주지 않는다 |
| `handoff scorecard` | owner 제출 순서와 복붙 문장을 준다 | artifact 구조를 새로 정의하지 않는다 |
| `review packet` | review-ready -> QA pass -> PM green ladder를 잠근다 | starter bundle 작성법을 다시 설명하지 않는다 |

### 복붙 원칙
1. manifest/status 문장은 `manifest packet` 기준으로 쓴다.
2. archive note/caption 톤은 `filled archive examples` 기준으로 맞춘다.
3. owner 제출 순서와 handoff line은 `handoff scorecard` 기준으로 쓴다.
4. review-ready / QA pass / PM green / B-2 unlock 최종 구분은 `review packet` 기준으로 쓴다.

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| B-1 문서가 충분해진 뒤 summary와 source packet, exemplar와 signoff 문서가 같은 층위로 읽히는 위험 | implementer/UI/아트/QA/PM이 각자 다른 문서에서 착수/기록 문장을 가져와 `library-only boundary`와 `green` 계층이 다시 흐려질 수 있다 | 이번 슬롯의 `docs/dicespell_first_run_onboarding_b1_document_stack_packet_kr.md` / `summary`로 B-1 문서를 baseline / source packet / readable companion / archive evidence / signoff 5층으로 다시 압축했다 | 다음 실제 B-1 live turn 직전 |
| filled archive examples가 있어도 implementer가 example을 source contract처럼 읽는 위험 | 현재 honest status보다 큰 `review-ready`, `green`, `B-2 unlock` 문장이 조기 사용될 수 있다 | `archive 기준 문서`와 `signoff boundary` 섹션으로 example / scorecard / review 역할을 분리 고정했다 | manifest/status 초안 작성 직전 |
| 포털/노션에서는 summary가 packet보다 먼저 보이는 위험 | 빠른 읽기 문서만 보고 착수/승인 판단을 내릴 수 있다 | 역할별 읽기 순서와 role-specific handoff point를 명시해 각 owner가 어떤 문서를 source로 삼아야 하는지 다시 잠갔다 | 포털/노션 handoff 직전 |

---

## 8. immediate next actions

1. 다음 B-1 실제 turn은 `readiness board -> runtime stack -> B-1 document stack packet -> B-1 library packet -> B-1 live kickoff -> B-1 archive bootstrap -> B-1 artifact stub -> B-1 manifest -> B-1 semantic anchor -> B-1 required artifacts -> B-1 filled archive examples -> B-1 handoff scorecard -> B-1 review packet` 순서로 읽는다.
2. 프로그래머는 source packet을 먼저 잠그고 exemplar는 wording sanity reference로만 쓴다.
3. UI/아트는 required artifacts와 filled archive examples를 같이 보되, scorecard 전에는 signoff 문장을 쓰지 않는다.
4. QA/PM은 candidate/pass/green을 여전히 분리 기록한다. example archive나 summary 문장을 final green line으로 직접 승격하지 않는다.
5. B-2 battle은 여전히 B-1 PM green 뒤에만 열린다. 이 문서는 읽기 위계를 잠그는 문서지, stage unlock을 당기는 문서가 아니다.
