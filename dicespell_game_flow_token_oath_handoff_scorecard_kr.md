# DiceSpell `Run Table` Oath Banner Handoff Scorecard

## 3줄 요약
- 이 문서는 `oath live kickoff`, `semantic anchor`, `archive file map`, `filled archive examples`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도 끝까지 남아 있던 마지막 운영 공백인 **`좋아, 이제 oath archive를 실제로 열면 프로그래머 / UI / 아트 / QA / PM은 어떤 순서로 무엇을 제출하고, 어디서 archive-in-progress / archive-ready candidate / green / queue-closing unlock을 나누지?`**를 고정하는 source-of-truth다.
- 핵심은 새 ending UI를 더 설계하는 것이 아니라, oath 마지막 live turn의 handoff를 **역할별 제출 체크리스트 + 단계별 제출 순서 + 복붙용 기록 문장**으로 압축해 `artifact는 모였지만 owner 순서와 초록불 문장이 흐린 상태`와 `진짜 candidate/green 직전 상태`를 같은 뜻으로 쓰지 못하게 만드는 것이다.
- 첫 화면만 읽어도 `누가 지금 움직여도 되는지`, `어떤 파일이 비어 있으면 아직 candidate가 아닌지`, `어떤 문장을 tracker/run log/git에 그대로 남겨야 하는지`가 바로 보여야 한다.

## 한 줄 목표
`oath-banner` 마지막 live turn을 **owner별 제출물 8종 + 단계별 handoff gate 4개 + 복붙 문장 4개**로 잠가, 마지막 stage archive가 문서 세트가 아니라 실제 handoff-ready work package로 읽히게 만든다.

## 왜 이 문서가 필요한가
지금 Run Table oath 축은 아래 문서까지 이미 닫혀 있다.

- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_semantic_anchor_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_archive_file_map_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_filled_archive_examples_kr.md`
- `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- oath final stage는 어디까지를 닫는가
- archive는 어떤 폴더/manifest/stub로 시작하는가
- semantic locator와 file routing은 어디까지가 canonical path인가
- required artifacts 8종은 무엇이며 candidate review는 어떤 순서로 진행되는가

하지만 실제 마지막 live refinement 직전에는 여전히 아래 공백이 남아 있다.

1. `무엇을 제출해야 하는가`와 `누가 언제 제출하는가`는 다르다. 문서가 충분해도 owner handoff 순서가 한 장으로 안 보이면 implementer/UI/아트/QA/PM은 다시 각자 다른 타이밍에 기록을 남긴다.
2. filled archive examples가 있어도 tracker/run log/git에는 여전히 `Overrun Oath 진행`, `ending은 거의 끝`, `queue closing도 곧` 같은 큰 문장이 먼저 들어갈 수 있다.
3. QA/PM은 `archive-ready candidate`와 `oath-banner green`을 나누는 review 순서를 알고 있어도, 실제 제출물이 어떤 순서로 모여야 안전한지 한 장으로 다시 보고 싶어진다.
4. UI/아트는 capture와 starter sanity를 내면 된다는 걸 알아도, 프로그래머 hook/check note보다 먼저 올려도 되는지, candidate 전 필수 선행물이 있는지 불명확할 수 있다.

즉 지금 필요한 것은 새 stage 문서가 아니라, **oath 마지막 stage를 실제 제출/검수/기록 순서로 다시 압축한 handoff scorecard**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 단계별 제출 순서`, `3-1. 프로그래머 scorecard` | `oath live kickoff`, `oath semantic anchor`, `oath archive file map` | manifest와 hook/check note를 먼저 잠그기 전에는 UI/아트 evidence가 와도 candidate를 올리지 않는다 |
| UI | `2. 단계별 제출 순서`, `3-2. UI scorecard`, `4. 기록 문장` | `oath required artifacts`, `oath candidate review` | overview/banner-focus capture는 stage 중반 제출물이지 시작 신호가 아니다 |
| 아트 | `2. 단계별 제출 순서`, `3-3. 아트 scorecard`, `5. 리스크 & 후속과제` | `oath required artifacts`, `oath filled archive examples` | starter sanity는 oath-only 범위를 지키는 제출물이며 ending shell/reward-shop/queue closing 확장 제안은 아직 금지다 |
| QA | `2. 단계별 제출 순서`, `3-4. QA scorecard`, `4. 기록 문장` | `oath candidate review`, `queue closing packet` | candidate는 제출물 정렬 확인 뒤에만 쓰고, green은 PM 마지막 기록 전에는 쓰지 않는다 |
| PM/기획 | `0. first screen 결론`, `2. 단계별 제출 순서`, `3-5. PM scorecard`, `5. 리스크 & 후속과제` | `queue closing packet`, `oath candidate review` | oath green은 마지막 기록 문장이지 분위기상 거의 됐다는 표현이 아니다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `oath-banner archive를 실제로 열면 누가 어떤 순서로 제출하지?`
- `archive-in-progress / archive-ready candidate / green / queue closing review-only unlock은 어디서 갈리지?`
- `tracker/run log/git에는 어떤 문장을 그대로 남겨야 하지?`

### 가장 짧은 답
- oath 마지막 stage는 **프로그래머 바닥 제출 -> UI/아트 evidence 제출 -> QA candidate review -> PM green 기록** 순서로만 닫는다.
- `manifest + hook/check note`가 먼저 잠기기 전에는 overview/banner-focus capture가 있어도 아직 stage는 `archive-in-progress`다.
- `oath-banner archive-ready candidate`는 QA만 기록한다.
- `oath-banner green, Run Table token queue closing review-only unlock`은 PM 마지막 기록 전에는 누구도 쓰지 않는다.

### 한 줄 판정 규칙
oath 마지막 stage의 handoff는 **필수 artifact 존재 여부**가 아니라 **제출 순서와 기록 문장이 같은 stage ladder를 따르는가**로 판단한다.

---

## 1. core structure

## 3줄 요약
- 이 scorecard는 `시작 바닥`, `시각 증거`, `candidate review`, `green 기록` 4단계로만 본다.
- 각 단계에는 owner, 선행 제출물, 완료 문장, 아직 쓰면 안 되는 문장이 따로 있다.
- 목적은 oath archive를 `자료가 모이는 폴더`가 아니라 `같은 문장으로 닫히는 handoff flow`로 만드는 것이다.

### 한 줄 목표
`oath-banner` 제출 순서를 한 장으로 고정해, 누가 무엇을 내고 어디서 멈춰야 하는지 다시 회의하지 않게 만든다.

### oath handoff ladder
| 단계 | 상태명 | 누가 먼저 움직이나 | 이 단계에서 반드시 잠겨야 하는 것 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- | --- |
| 1 | `archive-in-progress` 바닥 | 프로그래머 | `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md`, oath-only boundary/stub | `candidate`, `green`, `queue closing unlock` |
| 2 | `archive-in-progress` evidence | UI + 아트 + 프로그래머 | overview/banner-focus capture, density verdict, starter sanity, oath-only boundary 유지 | `archive-ready`, `green`, `queue closing` |
| 3 | `archive-ready candidate` | QA | artifact 8종 정렬 확인, wording drift 없음, queue closing 미개방 확인 | `green`, `queue closing unlock 확정` |
| 4 | `oath-banner green` | PM | candidate evidence 수용, `Run Table token queue closing review-only unlock` 문장 1회 기록 | `Overrun Oath 진행`, `ending 거의 완료`, `Run Table complete` |

---

## 2. 단계별 제출 순서

## 3줄 요약
- 아래 순서는 실제 제출 타이밍을 고정하는 운영 규칙이다.
- owner가 자기 제출물을 더 잘 만들었더라도 선행 단계가 비어 있으면 candidate/green으로 올리지 않는다.
- stage를 빨리 닫는 가장 쉬운 방법은 더 많이 만드는 것이 아니라, **순서를 안 어기는 것**이다.

### 한 줄 목표
`무엇을 내야 하지`보다 먼저 `무엇을 먼저 내야 하지`를 고정한다.

| 순서 | 단계 | 주 담당 | 선행 조건 | 제출물 | 통과 기준 | 이 단계에서 금지 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | archive 바닥 생성 | 프로그래머 | `queue readiness = oath live-ready`와 battle green 전제 확인 | stage 폴더, `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md`, stub 초안 | manifest의 `status/owner summary/boundary/next allowed step/handoff line`이 oath-only language로 채워짐 | capture 먼저 생성, queue closing 먼저 기록 |
| 2 | hook/check 바닥 제출 | 프로그래머 | 순서 1 완료 | `hook-manifest`, `dom-sanity`, manifest status 갱신 | `data-run-surface="overrun-oath"`, `[data-oath-role="banner|result|carry"]`, `[data-overrun-cta]` 기준 문장이 note/check에 잠김 | ending helper 전체 감상, queue closing 문구 예고 |
| 3 | visual evidence 제출 | UI | 순서 2 완료 | `captures/oath-1440-overview.png`, `captures/oath-1440-banner-focus.png`, `notes/density-review.md` | main banner first-glance와 banner/result/carry hierarchy verdict가 note/capture에 일치 | glamour shot, reward/shop side family 강조 |
| 4 | starter sanity 제출 | 아트 | 순서 2 완료 | `assets/starter-asset-list.md` | `Oath Banner Starter` 충분/약함/과함과 금지 범위가 적힘 | ending shell 전체 리뉴얼 요구, queue closing 공용 shell 발주 |
| 5 | candidate review | QA | 순서 1~4 완료 | `notes/handoff-line.md`, candidate verdict, 누락/금지 여부 메모 | artifact 8종이 모두 oath stage vocabulary를 공유 | green 확정, queue closing unlock 확정 |
| 6 | final green 기록 | PM | QA candidate 통과 | tracker/run log/git용 green 문장 | `oath-banner green`, `Run Table token queue closing review-only unlock` 1회 기록 | `Overrun Oath 진행`, `대체로 완료`, `Run Table 종료` |

### 꼭 기억할 비대칭 규칙
1. UI/아트 제출은 프로그래머 hook/check 바닥보다 먼저 오지 않는다.
2. QA는 evidence를 검수하지만 `green`을 기록하지 않는다.
3. PM은 `candidate`를 다시 쓰지 않고 마지막 `green/unlock`만 기록한다.
4. queue closing은 oath green 전까지 문서상 존재해도 실행상 잠겨 있다.

---

## 3. 역할별 handoff scorecard

## 3-1. 프로그래머 scorecard

### 3줄 요약
- 프로그래머는 oath stage의 첫 문장을 잠그는 역할이다.
- 이 단계가 흐리면 뒤의 capture/note는 전부 예쁜 증거일 뿐 stage contract가 아니다.
- 프로그래머 제출이 먼저 닫혀야 다른 owner 제출물도 oath language를 공유할 수 있다.

### 한 줄 목표
manifest와 hook/check note를 먼저 잠가 oath-only 바닥을 만든다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| P-1 | `manifest.md` | `status=archive-in-progress`가 honest state로 적힘 | `green`, `queue closing unlock` 선기록 |
| P-2 | `notes/hook-manifest.md` | root/role/CTA hook location과 responsibility가 적힘 | ending helper/reward-shop/queue closing 메모 |
| P-3 | `checks/dom-sanity.md` | oath 범위 내 hook 존재/누락 여부가 적힘 | 전체 ending 품평 |
| P-4 | boundary note | `이번 stage는 banner / result / carry까지만 닫음` 문장이 명시됨 | ending shell / queue closing 확장 계획 |

### 프로그래머 복붙용 handoff line
- `oath-banner stage는 manifest + hook/check 바닥을 먼저 잠갔고, ending helper/reward-shop/queue closing은 아직 열지 않았다.`

## 3-2. UI scorecard

### 3줄 요약
- UI는 oath stage를 예쁘게 만드는 사람이 아니라 first-glance를 증명하는 사람이다.
- capture 2장과 density note 1개가 같은 oath sentence를 쓰는지가 핵심이다.
- visual evidence는 candidate 직전 제출물이지만, green 문장을 대신하지 않는다.

### 한 줄 목표
overview/focus capture와 density note를 oath-only verdict로 맞춘다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| U-1 | `captures/oath-1440-overview.png` | main banner가 first-glance로 읽힘 | CTA/side card 중심 샷 |
| U-2 | `captures/oath-1440-banner-focus.png` | banner / result / carry 관계가 같은 장면에서 읽힘 | reward/shop strip 중심 샷 |
| U-3 | `notes/density-review.md` | `유지/약함/과함` verdict + oath-only fix point 2~4줄 | 장문 회고, ending 전체 리디자인 제안 |

### UI 복붙용 handoff line
- `oath overview/banner-focus 증거는 banner / result / carry 읽힘까지만 다루며, ending helper/reward-shop/queue closing polish는 아직 범위 밖이다.`

## 3-3. 아트 scorecard

### 3줄 요약
- 아트는 이번 stage에서 새 큰 패키지를 여는 사람이 아니다.
- starter sanity note는 오히려 `무엇을 아직 안 하는지`를 같이 적어야 좋은 제출물이다.
- oath green은 멋진 key art가 아니라 starter 범위가 충분한지로 닫힌다.

### 한 줄 목표
`Oath Banner Starter` 충분성/금지 범위를 짧은 sanity note로 잠근다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| A-1 | `assets/starter-asset-list.md` | 현재 starter 구성, 충분/약함/과함 판단, oath stage에서 아직 안 하는 자산이 함께 적힘 | ending shell 전체 재설계, queue closing 공용 shell 리뉴얼 |

### 아트 복붙용 handoff line
- `이번 oath stage의 art sanity는 Oath Banner Starter 범위만 판단하며, ending shell / reward-shop / queue closing 공용 자산 확장은 아직 열지 않는다.`

## 3-4. QA scorecard

### 3줄 요약
- QA는 candidate를 선언하지만 green을 대신하지 않는다.
- QA의 핵심 일은 artifact completeness보다 wording alignment와 unlock discipline을 확인하는 것이다.
- candidate는 pass/fail이고, green은 최종 기록이다.

### 한 줄 목표
artifact 8종과 기록 문장이 같은 oath ladder를 쓰는지 검수한다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| Q-1 | artifact completeness check | required artifacts 8종 존재 | 일부 누락인데 `대체로 완료` 기록 |
| Q-2 | wording alignment check | `oath-banner`, `banner / result / carry`, `starter`, `candidate 전 단계`, `queue closing after green` 언어 정렬 | generic `Overrun Oath 진행` |
| Q-3 | candidate line | `oath-banner archive-ready candidate`와 queue closing 미개방 확인 | `green`, `unlock 완료` |

### QA 복붙용 handoff line
- `oath-banner artifact bundle은 candidate review 기준을 충족했지만, green/unlock 기록은 PM 최종 단계 전까지 보류한다.`

## 3-5. PM scorecard

### 3줄 요약
- PM은 마지막으로 stage를 닫는 사람이지 중간 검수 문장을 반복하는 사람이 아니다.
- PM green은 단 한 번만 쓰여야 하고, 그 문장이 queue closing unlock 문장까지 함께 잠근다.
- 큰 총괄 문장은 stage language를 무너뜨리므로 금지한다.

### 한 줄 목표
oath green과 queue closing review-only unlock을 하나의 마지막 기록 문장으로만 남긴다.

| 체크 | 제출물 | 완료 조건 | 금지 |
| --- | --- | --- | --- |
| M-1 | final tracker/run log sentence | `oath-banner green`, `Run Table token queue closing review-only unlock`이 1회 기록됨 | `Overrun Oath 진행`, `대체로 완료`, `Run Table complete` |
| M-2 | next-step guard | 다음 단계가 queue closing review-only로만 열리고 새 ending polish는 여전히 대기 | `queue closing + follow-up reopen 병행`, `후속 stage 자유 진행` |

### PM 복붙용 handoff line
- `oath-banner green. 다음 허용 단계는 Run Table token queue closing review-only unlock뿐이며, 새 ending polish reopen은 여전히 잠겨 있다.`

---

## 4. tracker / run log / git 기록 문장

## 3줄 요약
- 기록 문장은 stage를 설명하는 문장이지 기분을 설명하는 문장이 아니다.
- 같은 archive에서도 owner마다 다른 단계 문장을 남겨야 half-green을 막을 수 있다.
- 아래 복붙 문장은 짧지만 단계/책임/다음 unlock을 동시에 담아야 한다.

### 한 줄 목표
기록 문장을 owner별로 분리해 oath stage ladder를 문서 밖에서도 유지한다.

| 상황 | 권장 문장 | 아직 쓰면 안 되는 것 |
| --- | --- | --- |
| kickoff 직후 | `oath-banner archive를 열고 manifest + hook/check 바닥을 먼저 잠갔다.` | `Overrun Oath 진행 중`, `거의 시작됨` |
| evidence 수집 중 | `oath-banner archive-in-progress: banner / result / carry evidence를 oath-only 범위로 모으는 중이다.` | `candidate 직전`, `green 준비 완료` |
| QA candidate | `oath-banner archive-ready candidate: artifact 8종과 oath-only wording 정렬이 확인됐다.` | `oath green`, `queue closing unlock 완료` |
| PM final green | `oath-banner green. Run Table token queue closing review-only unlock만 다음 허용 단계다.` | `Run Table green`, `완료`, `후속도 같이 가능` |

---

## 5. 리스크 & 후속과제

## 3줄 요약
- 현재 가장 큰 리스크는 문서가 부족한 것이 아니라, 제출 순서와 기록 문장이 다시 섞이는 것이다.
- 특히 manifest/hook/check 바닥보다 capture가 먼저 올라오거나, QA candidate와 PM green이 같은 초록불로 기록되면 oath-only discipline이 무너진다.
- 다음 문서 작업이 있다면 새 stage를 더 쓰는 것보다 실제 archive scorecard 재사용을 더 쉽게 만드는 방향이어야 한다.

### 한 줄 목표
남은 oath handoff 리스크를 `누가 먼저 무엇을 적는가` 기준으로 다시 잠근다.

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| manifest/hook/check 바닥보다 capture가 먼저 올라와 archive가 다시 시각 감상 메모처럼 읽히는 위험 | candidate/green이 evidence 순서가 아니라 분위기로 올라간다 | scorecard에 `프로그래머 바닥 -> UI/아트 -> QA -> PM` 순서를 명시 | 첫 oath live archive 생성 직전 |
| QA candidate와 PM green이 같은 초록불 문장으로 기록되는 위험 | queue closing unlock이 조기 개방되거나 stage discipline이 무너진다 | owner별 복붙 문장과 금지 문장을 scorecard 안에 같이 고정 | 첫 oath candidate review / PM green 기록 직전 |
| starter sanity note가 ending shell/reward-shop/queue closing까지 같이 열어 oath stage를 다시 부풀리는 위험 | stage boundary가 약해지고 oath green이 늦어진다 | 아트 scorecard에 small-sanity language와 금지 범위를 같이 고정 | 첫 starter sanity note 작성 직전 |
| summary/exemplar만 읽고 source packet의 금지 범위를 생략하는 위험 | archive가 예시 폴더 복사본처럼 보이고 actual handoff discipline이 약해진다 | scorecard에서 바로 이어서 읽을 문서를 역할별로 명시 | 첫 owner handoff 킥오프 직전 |

---

## 6. immediate next actions

1. 다음 oath 실제 refinement는 `source-of-truth map -> gap ledger -> queue readiness board -> oath live kickoff -> oath semantic anchor -> oath archive file map -> oath filled archive examples -> oath handoff scorecard -> oath required artifacts -> oath candidate review -> queue closing` 순서로 읽는다.
2. 실제 live archive는 반드시 `docs/artifacts/run-table-token-delivery/oath-banner/` 하나만 제출 경로로 사용하고, `examples/oath-banner/`는 reference layer로만 둔다.
3. 첫 live turn에서도 `manifest / hook-manifest / dom-sanity`를 먼저 채운 뒤에만 `overview/banner-focus capture + density`, `starter sanity`, `candidate`, `green`으로 넘어간다.
4. ending helper, reward/shop side family, queue closing은 oath green 전까지 계속 잠근다.

## 한 장 결론
Run Table oath 축의 남은 마지막 공백은 `무슨 artifact가 필요한가`만이 아니라 **`그 artifact를 누가 어떤 순서로 제출하고 어떤 문장으로 닫아야 honest oath green이 되는가`**다. 이 문서는 그 답을 owner scorecard와 기록 문장까지 포함한 oath-only handoff packet으로 고정한다.
