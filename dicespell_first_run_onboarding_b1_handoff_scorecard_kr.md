# DiceSpell 첫 런 온보딩 `B-1 library` Handoff Scorecard

## 3줄 요약
- 이 문서는 `B-1 live kickoff`, `archive bootstrap`, `artifact stub`, `manifest`, `semantic anchor`, `required artifacts`, `filled archive examples`, `review packet`까지 이미 충분한 상태에서도 끝까지 남아 있던 마지막 운영 공백인 **`좋아, 이제 B-1 archive를 실제로 열면 프로그래머 / UI / 아트 / QA / PM은 어떤 순서로 무엇을 제출하고, 어디서 archive-in-progress / review-ready / QA pass / PM green / B-2 unlock을 나누지?`**를 고정하는 source-of-truth다.
- 핵심은 새 primer를 더 설계하는 것이 아니라, B-1 첫 실전 턴의 handoff를 **역할별 제출 체크리스트 + 단계별 제출 순서 + 복붙용 기록 문장**으로 압축해 `artifact는 다 있는데 제출 순서와 green 언어가 흐린 상태`를 더 이상 초록불처럼 쓰지 못하게 만드는 것이다.
- 첫 화면만 읽어도 `누가 지금 움직여도 되는지`, `어떤 note/capture가 비어 있으면 아직 review-ready가 아닌지`, `어떤 문장을 tracker/run log/git에 그대로 남겨야 하는지`가 바로 보여야 한다.

## 한 줄 목표
`B-1 library` 첫 실전 archive를 **owner별 제출물 8종 + handoff gate 5개 + 복붙 문장 5개**로 잠가, library stage가 문서 세트가 아니라 실제 handoff-ready work package로 읽히게 만든다.

---

## 왜 이 문서가 필요한가
지금 B-1 문서 스택은 이미 두껍다.

- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`

즉 아래 질문은 이미 닫혀 있다.

- B-1은 정확히 어느 범위까지만 닫는가
- archive를 어떤 폴더와 starter bundle로 시작하는가
- starter stub과 manifest 첫 줄은 무엇으로 시작해야 하는가
- semantic locator는 무엇이며 required artifacts 8종은 무엇인가
- review-ready / QA pass / PM green / B-2 unlock은 어떤 순서로 말해야 하는가

하지만 실제 첫 live archive 직전에는 여전히 작은 운영 공백이 남는다.

1. `무엇을 제출해야 하는가`와 `누가 언제 제출하는가`는 다르다. required artifacts 8종이 정의돼 있어도 owner handoff 순서가 한 장으로 안 보이면 implementer/UI/아트/QA/PM은 다시 각자 다른 타이밍에 기록을 남긴다.
2. filled archive exemplar가 있어도 tracker/run log/git에는 여전히 `온보딩 진행`, `library 거의 완료`, `battle도 곧` 같은 큰 문장이 먼저 들어갈 수 있다.
3. QA/PM은 review packet을 알고 있어도, 실제 제출물이 어떤 순서로 모여야 안전한지와 어느 시점부터 `review-ready`를 말해도 되는지를 한 장으로 다시 보고 싶어진다.
4. UI/아트는 capture와 sanity note를 내면 된다는 걸 알아도, 프로그래머 state diff / boundary note보다 먼저 올려도 되는지, 아니면 review-ready 전 필수 선행물이 있는지 불명확할 수 있다.

즉 지금 필요한 것은 새 B-1 방향 문서가 아니라, **B-1 첫 stage를 실제 제출/검수/기록 순서로 다시 압축한 handoff scorecard**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 단계별 제출 순서`, `3-1. programmer scorecard` | `B-1 live kickoff`, `manifest`, `semantic anchor`, `required artifacts` | manifest + state diff + boundary note를 먼저 잠그기 전에는 UI/아트 evidence가 와도 아직 review-ready가 아니다 |
| UI | `2. 단계별 제출 순서`, `3-2. UI scorecard`, `4. 기록 문장` | `filled archive examples`, `review packet` | first-open / return-state capture는 stage 중반 제출물이지 시작 신호도 green 문장도 아니다 |
| 아트 | `2. 단계별 제출 순서`, `3-3. art scorecard`, `5. 리스크 & 후속과제` | `artifact stub`, `filled archive examples` | art sanity는 `primer-library` 충분성 verdict이며 추천 책 연출이나 새 key art 요청은 아직 금지다 |
| QA | `2. 단계별 제출 순서`, `3-4. QA scorecard`, `4. 기록 문장` | `required artifacts`, `review packet`, `signoff ladder` | review-ready와 QA pass는 다르고, QA는 artifact 8종 정렬 확인 뒤에만 pass를 쓴다 |
| PM/기획 | `0. first screen 결론`, `2. 단계별 제출 순서`, `3-5. PM scorecard`, `5. 리스크 & 후속과제` | `cutover readiness board`, `execution reporting packet` | PM green은 마지막 기록 문장이고, 그 전에는 B-2 unlock을 쓰지 않는다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `B-1 library archive를 실제로 열면 누가 어떤 순서로 제출하지?`
- `archive-in-progress / review-ready / QA pass / PM green / B-2 unlock은 어디서 갈리지?`
- `tracker/run log/git에는 어떤 문장을 그대로 남겨야 하지?`

### 가장 짧은 답
- B-1 첫 stage는 **프로그래머 바닥 제출 -> UI/아트 evidence 제출 -> QA pass -> PM green 기록 -> B-2 unlock 기록** 순서로만 닫는다.
- `manifest + state diff + boundary note`가 먼저 잠기기 전에는 first-open/return-state capture가 있어도 아직 stage는 `archive-in-progress`다.
- `B-1 review-ready`와 `B-1 QA pass`는 QA가 분리 기록한다.
- `B-1 library green. 다음 허용 단계는 B-2 battle뿐이다.`는 PM 마지막 기록 전에는 누구도 쓰지 않는다.

### 한 줄 판정 규칙
B-1 첫 stage의 handoff는 **artifact 존재 여부**가 아니라 **제출 순서와 기록 문장이 같은 B-1 ladder를 따르는가**로 판단한다.

---

## 1. core structure

## 3줄 요약
- 이 scorecard는 `archive 바닥`, `시각/토큰 evidence`, `review-ready`, `QA pass`, `PM green + B-2 unlock` 5단계로만 본다.
- 각 단계에는 owner, 선행 제출물, 완료 문장, 아직 쓰면 안 되는 문장이 따로 있다.
- 목적은 B-1 archive를 `자료가 모이는 폴더`가 아니라 `같은 문장으로 닫히는 handoff flow`로 만드는 것이다.

### 한 줄 목표
`B-1 library` 제출 순서를 한 장으로 고정해, 누가 무엇을 내고 어디서 멈춰야 하는지 다시 회의하지 않게 만든다.

### B-1 handoff ladder
| 단계 | 상태명 | 누가 먼저 움직이나 | 이 단계에서 반드시 잠겨야 하는 것 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- | --- |
| 1 | `archive-in-progress` | 프로그래머 | `manifest.md`, `logs/acceptance.md`, `notes/programmer-state-diff.md`, `notes/pm-boundary.md`, stub/금지 범위 | `review-ready`, `QA pass`, `green`, `B-2 unlock` |
| 2 | `archive-in-progress` | UI + 아트 + 프로그래머 | first-open / return-state capture, `ui-hierarchy note`, `art-token-sanity note`, semantic anchor 재확인 | `review-ready`, `green` |
| 3 | `review-ready` | 프로그래머 + QA | artifact 8종 존재, wording alignment, 다른 primer flag 미오픈 확인 | `QA pass`, `green`, `B-2 unlock` |
| 4 | `B-1 QA pass` | QA | B1-A1~A5 + recovery 3종 + CTA 비차단 + library-only boundary | `green`, `battle unlock 완료` |
| 5 | `B-1 library green` | PM | review-ready/QA pass evidence 수용, B-2 battle만 next step 기록 | `온보딩 진행`, `B-2도 사실상 ready`, `reward/shop도 같이` |

---

## 2. 단계별 제출 순서

## 3줄 요약
- 아래 순서는 실제 제출 타이밍을 고정하는 운영 규칙이다.
- owner가 자기 제출물을 더 잘 만들었더라도 선행 단계가 비어 있으면 review-ready/green으로 올리지 않는다.
- stage를 빨리 닫는 가장 쉬운 방법은 더 많이 만드는 것이 아니라, **순서를 안 어기는 것**이다.

### 한 줄 목표
`무엇을 내야 하지`보다 먼저 `무엇을 먼저 내야 하지`를 고정한다.

| 순서 | 단계 | 주 담당 | 선행 조건 | 제출물 | 통과 기준 | 이 단계에서 금지 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | archive 바닥 생성 | 프로그래머 | `readiness board`에서 B-1이 아직 잠겨 있고 live packet을 참조 중임을 확인 | stage 폴더, `manifest.md`, `logs/acceptance.md`, `notes/programmer-state-diff.md`, `notes/pm-boundary.md`, stub 초안 | manifest의 `status/owner summary/boundary/next allowed step/handoff line`이 B-1 library language로 채워짐 | capture 먼저 생성, battle/reward/shop/ending note 작성 |
| 2 | state/boundary 바닥 제출 | 프로그래머 | 순서 1 완료 | `programmer-state-diff`, `pm-boundary`, semantic anchor 메모 | `seenBookSelectPrimer`, `renderGrimoireShelf`, `openingPlan`, `caution`, CTA path, other surfaces locked`가 note에 잠김 | `seenBattlePrimer` 등 후속 flag 예열, 공용 primer registry 확장 |
| 3 | visual evidence 제출 | UI | 순서 2 완료 | `captures/*first-open*`, `captures/*return-state*`, `ui-hierarchy note` | first-open / return-state 질문이 capture와 hierarchy note에 직접 적힘 | 예쁜 샷 중심 캡처, battle/reward/shop/ending 언급 |
| 4 | token sanity 제출 | 아트 | 순서 2 완료 | `art-token-sanity note` | `primer-library`가 안내 표식으로 읽히고 추천/정답 책처럼 읽히지 않음이 적힘 | 신규 key art 요구, library 전체 리디자인 요구 |
| 5 | review-ready 기록 | 프로그래머 + QA | 순서 1~4 완료 | review-ready line, artifact 8종 정렬 메모 | artifact 8종이 모두 B-1 vocabulary를 공유 | `QA pass`, `green`, `B-2 unlock` |
| 6 | QA pass 기록 | QA | 순서 5 완료 | acceptance pass line, recovery verdict, CTA 비차단 메모 | B1-A1~A5 + recovery 3종 + boundary 유지 확인 | `green`, `battle unlock 확정` |
| 7 | final green 기록 | PM | 순서 6 완료 | tracker/run log/git용 green line + next-step line | `B-1 library green`, `B-2 battle만 다음 허용 단계` 1회 기록 | `온보딩 green`, `B-2/B-3 같이`, `library/battle 초안 완료` |

### 꼭 기억할 비대칭 규칙
1. UI/아트 제출은 프로그래머 state/boundary 바닥보다 먼저 오지 않는다.
2. review-ready는 artifact 수집 완료 상태고, QA pass는 검수 통과 상태다.
3. QA는 evidence를 검수하지만 `green`을 기록하지 않는다.
4. PM은 마지막 `green/unlock`만 기록하고 review-ready / QA pass를 반복하지 않는다.
5. B-2 battle은 B-1 green 전까지 문서상 존재해도 실행상 잠겨 있다.

---

## 3. 역할별 handoff scorecard

## 3-1. 프로그래머 scorecard

### 3줄 요약
- 프로그래머는 B-1 stage의 첫 문장을 잠그는 역할이다.
- 이 단계가 흐리면 뒤의 capture/note는 전부 예쁜 증거일 뿐 stage contract가 아니다.
- 프로그래머 제출이 먼저 닫혀야 다른 owner 제출물도 같은 `library-only` 언어를 공유할 수 있다.

### 한 줄 목표
manifest와 state/boundary note를 먼저 잠가 `B-1 library` 바닥을 만든다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| P-1 | `manifest.md` | `status=archive-in-progress` 또는 `review-ready`가 honest state로 적힘 | `green`, `B-2 unlock` 선기록 |
| P-2 | `notes/programmer-state-diff.md` | `seenBookSelectPrimer`, hero-primer, openingPlan/caution, 미오픈 flag가 함께 적힘 | battle/reward/shop/ending primer 예열 |
| P-3 | semantic anchor 메모 | `readMetaProgress`, `persistMetaProgress`, `renderGrimoireShelf`, `openingPlan`, `caution`, `data-action="choose-grimoire"`가 적힘 | line drift를 이유로 stage 확대 |
| P-4 | `notes/pm-boundary.md` 초안 | `이번 stage는 B-1 library만 닫고 다른 surface는 열지 않음` 문장이 적힘 | `온보딩 전체 시작`, `battle도 곧` |

### 프로그래머 복붙용 handoff line
- `B-1 library stage는 manifest + state/boundary 바닥을 먼저 잠갔고, 아직 battle/reward/shop/ending primer는 열지 않았다.`

## 3-2. UI scorecard

### 3줄 요약
- UI는 B-1 stage를 예쁘게 만드는 사람이 아니라 first-open / return-state를 증명하는 사람이다.
- capture 2장과 hierarchy note 1개가 같은 B-1 sentence를 쓰는지가 핵심이다.
- visual evidence는 review-ready 직전 제출물이지만, green 문장을 대신하지 않는다.

### 한 줄 목표
first-open / return-state capture와 hierarchy note를 B-1 library verdict로 맞춘다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| U-1 | `captures/*b1-library-first-open*` | hero-primer, body-copy, CTA가 한 화면 안에서 읽힘 | `final shot`, `pretty screen` 같은 generic 메타 |
| U-2 | `captures/*b1-library-return-state*` | primer 재노출 없이 highlight/CTA만 남음 | battle/reward/shop surface 포함 |
| U-3 | `notes/ui-hierarchy.md` | `section-head -> hero-primer -> body-copy -> grid` 순서와 CTA 비차단 verdict가 적힘 | shelf 전체 리디자인 제안, 추천 책 강화 제안 |

### UI 복붙용 handoff line
- `B-1 visual evidence는 first-open / return-state 질문과 CTA 비차단성까지만 다루며, 다른 surface primer와 library 리디자인은 아직 범위 밖이다.`

## 3-3. 아트 scorecard

### 3줄 요약
- 아트는 이번 stage에서 새 큰 패키지를 여는 사람이 아니다.
- art sanity note는 오히려 `무엇을 아직 안 하는지`를 같이 적어야 좋은 제출물이다.
- B-1 green은 멋진 key art가 아니라 `primer-library`가 추천/정답처럼 읽히지 않는지로 닫힌다.

### 한 줄 목표
`primer-library` 충분성/금지 범위를 짧은 sanity note로 잠근다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| A-1 | `notes/art-token-sanity.md` | 현재 token/accent가 안내 표식인지, 과함/약함/충분함 verdict가 적힘 | 신규 key art 요구, shelf 전체 장식 발주 |
| A-2 | boundary line | 추천/정답 책 연출을 이번 stage green 조건으로 만들지 않음 | `추천 책 강조가 없으면 green 불가` |

### 아트 복붙용 handoff line
- `이번 B-1 stage의 art sanity는 primer-library 충분성만 판단하며, 추천 책 연출이나 대형 신규 자산 확장은 아직 열지 않는다.`

## 3-4. QA scorecard

### 3줄 요약
- QA는 review-ready와 pass를 분리 기록해야 한다.
- QA의 핵심 일은 artifact completeness보다 wording alignment와 boundary 유지, acceptance/recovery를 함께 확인하는 것이다.
- pass는 통과고, green은 최종 기록이다.

### 한 줄 목표
artifact 8종과 acceptance/recovery가 같은 B-1 ladder를 쓰는지 검수한다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| Q-1 | artifact completeness check | required artifacts 8종 존재 | 일부 누락인데 `대체로 완료` 기록 |
| Q-2 | wording alignment check | `B-1 library`, `seenBookSelectPrimer`, `openingPlan/caution`, `other surfaces locked` 언어 정렬 | generic `온보딩 진행` |
| Q-3 | QA pass line | B1-A1~A5 + recovery 3종 + CTA 비차단 + boundary 유지 | `green`, `B-2 unlock 완료` |

### QA 복붙용 handoff line
- `B-1 library artifact bundle은 QA pass 기준을 충족했지만, green/unlock 기록은 PM 최종 단계 전까지 보류한다.`

## 3-5. PM scorecard

### 3줄 요약
- PM은 마지막으로 stage를 닫는 사람이지 중간 검수 문장을 반복하는 사람이 아니다.
- PM green은 단 한 번만 쓰여야 하고, 그 문장이 다음 unlock 문장까지 함께 잠근다.
- 큰 총괄 문장은 stage language를 무너뜨리므로 금지한다.

### 한 줄 목표
B-1 green과 B-2 unlock을 마지막 기록 문장 두 줄로만 남긴다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| M-1 | final tracker/run log sentence | `B-1 library green`이 1회 기록됨 | `온보딩 green`, `library/battle 진행` |
| M-2 | next-step guard | 다음 단계가 B-2 battle로만 열리고 B-3/B-4는 여전히 대기 | `reward/shop도 같이`, `ending도 곧` |
| M-3 | archive path note | stage archive 경로와 handoff line이 함께 남음 | archive 없이 green 주장 |

### PM 복붙용 handoff line
- `B-1 library green. 다음 허용 단계는 B-2 battle archive 생성뿐이며, reward/shop/ending primer는 여전히 잠겨 있다.`

---

## 4. tracker / run log / git 기록 문장

## 3줄 요약
- 기록 문장은 stage를 설명하는 문장이지 기분을 설명하는 문장이 아니다.
- 같은 archive에서도 owner마다 다른 단계 문장을 남겨야 half-green을 막을 수 있다.
- 아래 복붙 문장은 짧지만 단계/책임/다음 unlock을 동시에 담아야 한다.

### 한 줄 목표
기록 문장을 owner별로 분리해 B-1 stage ladder를 문서 밖에서도 유지한다.

| 상황 | 권장 문장 | 아직 쓰면 안 되는 것 |
| --- | --- | --- |
| archive 바닥 생성 직후 | `B-1 library archive를 열고 manifest + state/boundary 바닥을 먼저 잠갔다.` | `B-1 진행 중`, `거의 시작됨` |
| visual/token evidence 수집 중 | `B-1 library archive-in-progress: first-open/return-state evidence와 hierarchy/token sanity를 library-only 범위로 모으는 중이다.` | `review-ready 직전`, `green 준비 완료` |
| review-ready | `B-1 review-ready: artifact 8종과 B-1 library wording alignment를 확인했고 battle/reward/shop/ending primer는 아직 열지 않았다.` | `QA pass`, `green`, `B-2 unlock` |
| QA pass | `B-1 QA pass: B1-A1~A5와 recovery 3종, CTA 비차단, library-only boundary 유지가 확인됐다.` | `B-1 green`, `battle unlock 완료` |
| PM final green | `B-1 library green. 다음 허용 단계는 B-2 battle archive 생성뿐이다.` | `온보딩 green`, `B-2/B-3 같이`, `reward/shop도 곧` |

---

## 5. 리스크 & 후속과제

## 3줄 요약
- 현재 가장 큰 리스크는 문서가 부족한 것이 아니라, 제출 순서와 기록 문장이 다시 섞이는 것이다.
- 특히 capture가 state/boundary note보다 먼저 올라오거나, QA pass와 PM green이 같은 초록불로 기록되면 B-1 library-only discipline이 무너진다.
- 다음 문서 작업이 있다면 새 B-1 방향 문서를 더 쓰는 것보다 실제 archive scorecard 재사용을 더 쉽게 만드는 방향이어야 한다.

### 한 줄 목표
`문서가 충분하다`와 `이제 green이다` 사이에 남아 있는 마지막 운영 드리프트를 줄인다.

| 리스크 | 왜 문제인가 | 이번 대응 | 다음 handoff |
| --- | --- | --- | --- |
| capture가 먼저 오고 state/boundary note가 늦게 오는 위험 | 예쁜 evidence는 있는데 stage contract가 비어 있어 review-ready가 흔들린다 | 프로그래머 바닥 제출을 1순위로 고정 | 다음 실제 B-1 archive 생성 직전 |
| review-ready / QA pass / PM green이 같은 초록불로 기록되는 위험 | B-2 unlock이 너무 빨리 열리고 half-cutover가 완료처럼 보인다 | 5단계 ladder와 owner별 복붙 문장 고정 | 다음 B-1 review-ready / QA pass / green 기록 직전 |
| art sanity가 추천/정답 책 연출 요구로 커지는 위험 | library-only stage가 다시 대형 visual polish로 부푼다 | art sanity를 `primer-library` 충분성 verdict로 제한 | 다음 B-1 UI/아트 sanity 검토 직전 |
| B-1 green 전에 B-2 언어가 run log에 섞이는 위험 | surface 1개 = commit 1개 discipline이 무너진다 | PM green 문장과 next-step 문장 분리 | 다음 tracker/run log 업데이트 직전 |

---

## 6. 즉시 다음 액션
1. 실제 B-1 archive를 열 때는 `filled archive examples`보다 먼저 이 scorecard를 열어 제출 순서를 고정한다.
2. 프로그래머는 `manifest + state diff + boundary note`를 먼저 채우고, 그 뒤에만 capture/hierarchy/token sanity를 받는다.
3. QA는 `review-ready`와 `QA pass`를 분리 기록하고, PM만 마지막 `green + B-2 unlock`을 기록한다.
4. tracker / automation run log / git에는 위 표의 복붙 문장만 사용하고, `온보딩 진행`, `거의 완료` 같은 큰 문장은 계속 금지한다.

---

## 원본 문서 연결
- readiness: `./dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- runtime stack: `./dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- B-1 live kickoff: `./dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- B-1 manifest: `./dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- B-1 semantic anchor: `./dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- B-1 required artifacts: `./dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- B-1 filled archive examples: `./dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md`
- B-1 review packet: `./dicespell_first_run_onboarding_b1_review_packet_kr.md`
- readable companion: `./dicespell_first_run_onboarding_b1_handoff_scorecard_summary_kr.md`
