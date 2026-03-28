# DiceSpell `Run Table` Token Manifest Fill 패킷

## 3줄 요약
- 이 문서는 `archive bootstrap`, `artifact stub`, `atlas live kickoff`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도 마지막으로 남아 있던 작은 실전 공백, 즉 **`좋아, stage archive는 만들었고 starter file도 열었어. 그런데 manifest 각 칸은 정확히 어떤 문장으로 채워야 하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 토큰이나 새 stage를 더 설계하는 것이 아니라, `manifest.md`의 `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line`을 **stage 언어 그대로 재사용 가능한 field-level 계약**으로 압축하는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `무엇을 쓸까`보다 `무엇을 아직 쓰면 안 되는가`, `어느 문장이 candidate이고 어느 문장이 green인지`, `battle/oath unlock 문장을 언제부터 적어도 되는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`manifest.md`를 단순 체크리스트가 아니라 **stage 경계 / 증거 상태 / 다음 unlock을 한 줄씩 잠그는 실행 계약서**로 만들어, Run Table token refinement가 generic placeholder나 큰 총괄 문장으로 다시 흐르지 않게 만든다.

## 왜 이 문서가 필요한가
지금 Run Table token 축은 아래 문서까지 이미 충분하다.

- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`

즉 아래는 이미 정리돼 있다.

1. 지금 atlas만 live-ready이고 battle/oath는 unlock 전까지 locked라는 점
2. stage archive를 어디에 만들고 어떤 starter file을 먼저 복사해야 하는지
3. `hook-manifest`, `dom-sanity`, `density-review`, `starter-asset-list`, `handoff-line`의 첫 줄을 어떤 honest stub로 시작해야 하는지
4. required artifacts 8종이 어떤 질문을 증명해야 candidate/green review로 넘어갈 수 있는지

하지만 실제 첫 live turn 직전에는 여전히 마지막 작은 공백이 남는다.

1. `manifest.md`는 거의 모든 packet이 참조하지만, 각 필드에 **정확히 어떤 문장을 쓰고 어떤 문장은 금지인지**는 아직 여러 문서에 흩어져 있다.
2. implementer는 `status`를 `bootstrap-ready`로 적어야 할지, 바로 `archive-ready candidate`로 적어도 되는지, PM은 `battle unlock`을 언제부터 `next allowed step`에 써도 되는지 다시 판단해야 한다.
3. QA/PM이 같은 stage를 봐도 `owner summary`는 atlas-only인데 `handoff line`은 `Run Table 진행`처럼 커지면, 문서가 충분해도 기록은 다시 큰 초록불로 흐른다.
4. starter manifest 템플릿이 있어도 field-level 계약이 없으면, 첫 실전 archive는 결국 `TODO`, `정리 중`, `거의 완료` 같은 generic placeholder로 채워진다.

즉 지금 필요한 것은 새 설계가 아니라, **manifest의 칸 하나하나를 stage contract 언어로 채우는 방법 자체를 고정하는 문서**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. field contract`, `3. stage별 작성 규칙` | `artifact stub packet`, `atlas live kickoff packet` | manifest는 코드 edit 이후 정리문서가 아니라, edit 전 stage 경계를 먼저 잠그는 파일이다 |
| UI | `2. field contract`, `4. wording examples`, `5. role-specific handoff` | `atlas/battle/oath required artifacts packet` | `owner summary`와 `boundary`는 visual verdict를 넓히는 칸이 아니라 이번 surface 범위를 줄이는 칸이다 |
| 아트 | `2. field contract`, `3. stage별 작성 규칙`, `5. role-specific handoff` | `artifact stub packet`, `starter-asset-list` stub | manifest에서도 아트는 stage-specific starter sanity만 말하고 공통 리뉴얼은 적지 않는다 |
| QA | `2. field contract`, `4. wording examples`, `6. candidate/green gate` | `candidate review packet`, `queue readiness board` | candidate/green 문장은 `status`와 `handoff line`에 따로 적히며, 같은 칸에서 섞이면 fail이다 |
| PM/기획 | `0. first screen 결론`, `2. field contract`, `6. candidate/green gate`, `7. 리스크 & 후속과제` | `queue readiness board`, `refinement queue packet` | manifest는 진행 요약이 아니라 unlock discipline을 기록하는 문서다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `manifest.md`의 각 칸에는 정확히 어떤 문장을 써야 하지?
- `candidate-ready`, `green`, `unlock`, `비범위`를 한 파일 안에서 어떻게 섞지 않고 적지?
- 무엇을 적으면 아직 honest manifest이고, 무엇을 적는 순간 stage boundary가 무너지지?

### 가장 짧은 답
- `status`는 현재 단계만 적는다.
- `owner summary`는 이번 stage에서 실제로 여는 surface와 evidence 목적만 적는다.
- `boundary / non-change`는 아직 열지 않는 surface와 unlock 전 금지 범위를 적는다.
- `next allowed step`은 **지금 당장 하는 일**이 아니라 **이번 stage green 뒤에만 허용되는 다음 단계**를 적는다.
- `handoff line`은 QA candidate 또는 PM green 문장을 stage명 그대로 적고, `Run Table 진행`, `거의 완료` 같은 총괄 문장은 쓰지 않는다.

### 한 줄 판정 규칙
좋은 manifest는 `무엇을 했다`보다 **`이번 stage가 무엇만 닫고 무엇은 아직 안 열었는가`**를 먼저 말한다.

---

## 1. core structure

## 3줄 요약
- Run Table token manifest는 다섯 칸만 제대로 채워도 stage 경계를 거의 다 잠글 수 있다.
- 중요한 것은 길이가 아니라, 각 칸이 서로 다른 책임을 말하게 만드는 것이다.
- 아래 다섯 칸이 같은 단계 언어를 쓸 때만 manifest를 honest manifest로 본다.

### 한 줄 목표
`status / owner summary / boundary / next allowed step / handoff line`을 서로 다른 질문에 답하는 칸으로 분리한다.

### canonical fields
1. `status`
2. `owner summary`
3. `boundary / non-change`
4. `next allowed step`
5. `handoff line`

---

## 2. field contract

## 3줄 요약
- 아래 표는 각 필드가 무엇을 말해야 하는지와, 무엇을 말하면 안 되는지를 file-level로 고정한다.
- 핵심은 `manifest.md` 한 파일 안에서 stage progress와 next unlock과 큰 총괄 문장을 섞지 않게 만드는 것이다.
- candidate/green review는 이 field alignment가 맞을 때만 의미가 생긴다.

### 한 줄 목표
manifest 칸 하나하나를 stage execution vocabulary로 잠근다.

| 필드 | 이 칸이 답해야 하는 질문 | 반드시 들어가야 하는 내용 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- |
| `status` | 지금 이 archive는 어느 단계인가? | `bootstrap-ready`, `archive-in-progress`, `archive-ready candidate`, `green` 중 현재 단계 1개 | `거의 완료`, `진행 중이지만 사실상 green`, 여러 단계 동시 표기 |
| `owner summary` | 이번 stage에서 누가 무엇을 닫고 있는가? | 현재 stage명, 이번 surface 범위, evidence 목적 | battle/oath/queue closing 병행, 큰 총괄 작업 설명 |
| `boundary / non-change` | 이번 stage에서 일부러 안 여는 것은 무엇인가? | locked stage, 비범위 surface, unlock 전 금지 edit | `추후 전체 polish`, vague backlog, 애매한 미래 계획 |
| `next allowed step` | 이번 stage green 뒤에만 무엇이 열리는가? | 바로 다음 stage 1개 또는 review-only 단계 1개 | 현재 stage 내부 TODO, `필요하면 battle도`, 여러 다음 단계 동시 표기 |
| `handoff line` | QA/PM이 복붙할 공식 stage 문장은 무엇인가? | `atlas-route-node archive-ready candidate`, `atlas-route-node green, battle-stakes-plaque unlock` 같은 canonical sentence | `Run Table 진행`, `전체 거의 완료`, 다른 stage를 한 줄에 섞는 문장 |

### field alignment 규칙
- `status=bootstrap-ready`이면 `handoff line`은 green 문장이 아니라 초안 또는 금지 문장 메모여야 한다.
- `status=archive-ready candidate`이면 `next allowed step`은 여전히 다음 stage unlock 조건을 말해야지, 지금 stage 내부 추가 작업을 말하면 안 된다.
- `status=green`이면 `handoff line`과 `next allowed step`이 같은 unlock chain을 말해야 한다.

---

## 3. stage별 작성 규칙

## 3줄 요약
- atlas/battle/oath는 같은 필드를 쓰지만, 각 칸에서 강조해야 하는 surface가 다르다.
- 따라서 stage별로 문장 뼈대를 고정해 두지 않으면, manifest는 다시 generic 진행 메모가 된다.
- 아래는 각 stage에서 복붙해도 되는 수준의 작성 규칙이다.

### 한 줄 목표
stage가 달라도 manifest 필드는 같은 구조, 다른 surface 언어로 채운다.

### 3-1. atlas-route-node
- `owner summary`는 `atlas route node hook + threshold 읽힘 + atlas archive evidence`만 말한다.
- `boundary / non-change`에는 `battle/oath locked`, `preview/log 대확장 금지`, `queue closing 비범위`를 적는다.
- `next allowed step`은 `battle-stakes-plaque unlock only after atlas green` 계열만 허용한다.

### 3-2. battle-stakes-plaque
- `owner summary`는 `current/next/grimoire stakes hierarchy + battle stage evidence`만 말한다.
- `boundary / non-change`에는 `oath locked`, `HUD 전체 개편 금지`, `queue closing 비범위`를 적는다.
- `next allowed step`은 `oath-banner unlock only after battle green` 계열만 허용한다.

### 3-3. oath-banner
- `owner summary`는 `banner/result/carry hierarchy + oath stage evidence`만 말한다.
- `boundary / non-change`에는 `queue closing review-only until oath green`, `ending total polish 금지`를 적는다.
- `next allowed step`은 `queue-closing review-only` 계열만 허용한다.

---

## 4. wording examples

## 3줄 요약
- 아래 예시는 스타일 참고가 아니라, 실제 실전에서 거의 그대로 재사용해도 되는 honest wording 기준이다.
- 좋은 예시는 늘 stage명, 이번 범위, 비범위, next unlock이 함께 보인다.
- 나쁜 예시는 항상 커다란 총괄 문장이나 vague progress 문장으로 흐른다.

### 한 줄 목표
좋은 manifest 문장을 복붙 가능한 수준으로 고정한다.

### `status`
- 좋은 예시: `bootstrap-ready`
- 좋은 예시: `archive-in-progress`
- 좋은 예시: `archive-ready candidate`
- 좋은 예시: `green`
- 나쁜 예시: `거의 완료`, `close to green`, `진행 중(사실상 끝)`

### `owner summary`
- 좋은 예시: `이번 stage는 atlas-route-node route state와 threshold 읽힘 evidence만 닫는다.`
- 좋은 예시: `이번 stage는 battle-stakes-plaque current/next/grimoire hierarchy와 stage archive evidence만 다룬다.`
- 나쁜 예시: `Run Table 토큰 전반을 정리 중이다.`
- 나쁜 예시: `atlas를 하면서 battle도 같이 보고 있다.`

### `boundary / non-change`
- 좋은 예시: `battle-stakes-plaque, oath-banner, queue closing은 아직 열지 않는다.`
- 좋은 예시: `HUD 전체 재배치와 preview/log 확장은 이번 stage 비범위다.`
- 나쁜 예시: `추후 더 손볼 예정.`
- 나쁜 예시: `필요하면 다음에 battle도 함께 수정.`

### `next allowed step`
- 좋은 예시: `battle-stakes-plaque unlock only after atlas-route-node green.`
- 좋은 예시: `oath-banner unlock only after battle-stakes-plaque green.`
- 좋은 예시: `queue-closing review-only after oath-banner green.`
- 나쁜 예시: `다음엔 battle도 가능할 듯.`
- 나쁜 예시: `추가 polish 또는 다음 stage.`

### `handoff line`
- 좋은 예시: `atlas-route-node archive-ready candidate: route node hook / threshold first-glance / Route Node Starter sanity bundle aligned.`
- 좋은 예시: `atlas-route-node green, battle-stakes-plaque unlock: atlas first-stage evidence bundle aligned.`
- 나쁜 예시: `Run Table 진행 중.`
- 나쁜 예시: `토큰 리파인 거의 완료.`

---

## 5. role-specific handoff points

### 프로그래머
- `owner summary`와 `boundary / non-change`는 diff 설명보다 먼저 채운다.
- `next allowed step`에는 현재 stage 내부 TODO를 적지 않는다.
- manifest가 atlas-only인데 code diff가 battle/oath까지 가면 manifest가 아니라 실행이 fail이다.

### UI
- manifest는 visual 회고 문서가 아니다.
- `owner summary`는 이번 stage 캡처가 무엇을 증명하는지와 연결돼야 한다.
- `boundary / non-change`에 `전체 visual polish` 같은 표현이 들어가면 stage가 커진다.

### 아트
- `owner summary`와 `boundary / non-change`는 starter asset sanity 범위를 잠그는 칸이다.
- 이 칸에서 공통 frame 리뉴얼이나 대형 배경 작업을 꺼내면 manifest honesty가 깨진다.

### QA
- candidate 판정 전 manifest 다섯 칸이 같은 stage명을 쓰는지 먼저 본다.
- `status=archive-ready candidate`인데 `handoff line`이 green 문장이면 fail이다.
- `boundary / non-change`가 비어 있으면 fail에 가깝다.

### PM/기획
- green 기록 전 `next allowed step`과 `handoff line`이 같은 unlock chain을 말하는지 확인한다.
- manifest는 대시보드 요약이 아니라 unlock discipline 문서다.

---

## 6. candidate / green gate

## 3줄 요약
- manifest field가 맞아도 그것만으로 candidate/green은 아니다.
- 하지만 이 alignment가 어긋나면 required artifacts가 모두 있어도 candidate/green을 쓰면 안 된다.
- 즉 manifest field alignment는 candidate/green의 최소 게이트다.

### 한 줄 목표
candidate/green을 artifact completeness + manifest honesty의 교집합으로 고정한다.

### candidate 최소 게이트
1. `status=archive-ready candidate`
2. `owner summary`가 현재 stage와 evidence 목적만 말함
3. `boundary / non-change`에 locked stage와 비범위가 적힘
4. `next allowed step`이 단일 unlock chain만 말함
5. `handoff line`이 candidate 문장임

### green 최소 게이트
1. `status=green`
2. QA candidate 기록이 선행됨
3. `handoff line`이 green + unlock 문장임
4. `next allowed step`이 실제 unlock stage와 일치함
5. 이전 locked stage가 manifest에 남아 있던 채로 green이 기록되지 않음

### fail 예시
- `status=green`인데 `battle/oath locked`를 그대로 남겨 놓고 green을 씀
- `status=archive-ready candidate`인데 `handoff line`이 `battle unlock`까지 같이 말함
- `next allowed step`이 `battle 또는 queue closing`처럼 두 갈래로 적힘

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| starter manifest가 있어도 field-level 계약이 없어 generic progress 메모로 흐르는 위험 | archive가 생겨도 stage boundary와 unlock discipline이 기록에서 무너짐 | 이번 packet에서 5개 canonical field의 질문/허용 문장/금지 문장을 file-level로 고정 | 다음 atlas 첫 live archive 작성 직전 |
| candidate와 green 문장이 `status` / `handoff line`에서 섞이는 위험 | QA/PM이 서로 다른 초록불을 같은 의미로 기록하게 됨 | candidate/green gate를 별도 표로 분리 | atlas candidate review 직전 |
| `next allowed step`에 vague future plan이 들어가는 위험 | battle/oath/queue closing unlock discipline이 약해짐 | 단일 unlock chain만 허용한다고 명시 | 각 stage green 기록 직전 |
| manifest가 대시보드 요약처럼 길어지는 위험 | honest stub와 required artifacts가 있어도 실제 구현 범위가 다시 커짐 | 각 필드를 1~2문장 stage contract로 제한 | 다음 battle/oath manifest 작성 직전 |

---

## 8. immediate next actions

1. 다음 Run Table 작업자는 `source-of-truth map -> gap ledger -> archive bootstrap -> artifact stub packet -> atlas live kickoff packet -> 이 manifest fill packet` 순서로 읽고 atlas archive를 연다.
2. `templates/stage_manifest_*.md`를 복사한 뒤 `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line`부터 먼저 채운다.
3. `status`를 honest current stage로만 적고, candidate/green은 QA/PM signoff 뒤에만 올린다.
4. `next allowed step`에는 현재 stage 내부 TODO가 아니라 다음 unlock chain만 적는다.
5. manifest가 채워진 뒤에만 required artifacts note와 capture를 stage archive에 drop한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 마지막 작은 실행 공백은 **`manifest는 만들었는데, 그 다섯 칸을 정확히 어떤 문장으로 채워야 stage boundary와 unlock discipline이 같이 살아남지?`**였다. 이 문서는 그 공백을 `status / owner summary / boundary / next allowed step / handoff line`의 field-level 계약으로 다시 압축해, 다음 atlas/battle/oath stage archive가 generic progress 메모가 아니라 실제 handoff-ready execution contract로 시작하게 만든다.
