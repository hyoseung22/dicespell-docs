# DiceSpell `Run Table` Atlas Route Node Required Artifacts 패킷

## 3줄 요약
- 이 문서는 `atlas-route-node stage packet`까지로도 남아 있던 마지막 실무 공백인 **`좋아, atlas 첫 stage가 archive-ready candidate라고 말하려면 정확히 어떤 artifact가 어떤 밀도로 들어 있어야 하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 토큰을 더 설계하는 것이 아니라, 첫 atlas 턴의 evidence bundle을 **파일 이름, 작성 책임, 필수 문장, pass/fail 기준**까지 다시 좁혀 프로그래머 / UI / 아트 / QA / PM이 같은 제출물로 말하게 만드는 것이다.
- 첫 화면만 읽어도 `무엇이 있어야 QA가 candidate를 쓸 수 있는지`, `무엇이 비어 있으면 아직 bootstrap/in-progress인지`, `battle unlock 전에 어떤 note와 capture가 반드시 모여야 하는지`가 바로 보여야 한다.

## 한 줄 목표
`atlas-route-node` 첫 stage를 **required artifacts 8종 + artifact별 owner sentence + archive-ready 판정선 1개**로 잠가, 첫 실제 refinement 턴의 제출물 언어를 감상형 note가 아니라 handoff-ready bundle로 고정한다.

## 왜 이 문서가 필요한가
지금 Run Table 축은 아래 문서까지 이미 닫혀 있다.

- `docs/dicespell_game_flow_surface_handoff_packet_kr.md`
- `docs/dicespell_game_flow_token_anchor_packet_kr.md`
- `docs/dicespell_game_flow_token_delivery_workboard_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- 첫 stage는 왜 atlas인가
- stage boundary는 어디까지인가
- `bootstrap-ready -> archive-in-progress -> archive-ready candidate -> atlas green` 순서와 ownership은 무엇인가
- battle / oath를 왜 아직 열면 안 되는가

하지만 실제 atlas 첫 턴 직전에는 여전히 다음 공백이 남아 있다.

1. `required artifacts` 목록은 stage packet에도 있지만, **각 artifact를 어느 owner가 어떤 문장 밀도로 채워야 하는지**가 한 장으로 다시 보이지 않는다.
2. `capture가 2장 필요하다`는 사실만으로는 충분하지 않다. QA/PM은 그 캡처가 무엇을 증명해야 하는지, 어떤 실패면 다시 in-progress로 내려야 하는지까지 같은 표로 보고 싶어진다.
3. 프로그래머 / UI / 아트가 note를 남길 때 wording이 조금씩 달라지면 `archive-ready candidate`가 있어도 battle unlock 직전 다시 설명 회의를 해야 한다.
4. `manifest.md`가 있어도 `hook-manifest`, `dom-sanity`, `density-review`, `starter-asset-list`, `handoff-line`이 서로 다른 단계 언어를 쓰면 첫 atlas green부터 half-closure가 생긴다.

즉 지금 필요한 것은 새 디자인안이 아니라, **atlas 첫 stage의 artifact bundle을 field-level handoff 계약으로 다시 압축한 required artifacts packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. artifact contract table`, `3. note 작성 규칙` | `atlas route node stage packet`, `token evidence manifest`, `token archive bootstrap` | hook/diff note는 generic DOM 메모가 아니라 atlas stage vocabulary로 써야 한다 |
| UI | `2. artifact contract table`, `4. capture verdict rule`, `6. role-specific handoff points` | `token closing packet`, `surface handoff summary` | overview/focus 캡처는 예쁜 장면이 아니라 current node와 threshold 읽힘을 증명해야 한다 |
| 아트 | `2. artifact contract table`, `5. starter asset sanity rule` | `token anchor packet`, `token evidence manifest` | starter note는 key art 제안이 아니라 route node starter 충분성/금지 범위를 말해야 한다 |
| QA | `2. artifact contract table`, `7. archive-ready candidate rule`, `8. 리스크 & 후속과제` | `token refinement queue`, `atlas route node stage packet` | artifact 8종이 같은 atlas stage 문장을 쓸 때만 candidate를 올린다 |
| PM/기획 | `0. first screen 결론`, `7. archive-ready candidate rule`, `9. immediate next actions` | `token gap ledger`, `token source-of-truth map summary` | atlas green은 artifact completeness를 보고 쓰는 문장이지 분위기상 거의 됐다는 말이 아니다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `atlas-route-node archive-ready candidate`를 말하려면 어떤 artifact가 반드시 있어야 하지?
- `어떤 note/capture는 아직 초안이고 어떤 순간부터 handoff-ready라고 말할 수 있지?`
- `battle unlock 전 마지막으로 봐야 하는 제출물은 무엇이지?`

### 가장 짧은 답
- atlas 첫 stage는 **artifact 8종이 같은 stage vocabulary를 쓸 때만** candidate로 본다.
- capture 2장, note 4장, check 1장, manifest 1장이 다 필요하다.
- 각 artifact는 `atlas-route-node`, `route node`, `threshold`, `starter`, `candidate/green 전 단계` 언어를 유지해야 한다.
- 그중 하나라도 generic summary나 다른 surface 언어로 흐르면 아직 `archive-in-progress`다.

### 한 줄 판정 규칙
atlas 첫 stage의 closing은 **캡처 개수**가 아니라 **artifact 간 문장 정렬**까지 포함한 bundle completeness**로 판단한다.

---

## 1. core structure

## 3줄 요약
- atlas required artifacts는 `상태`, `hook`, `검수`, `시각 증거`, `아트 sanity`, `handoff 문장` 6축으로 나뉜다.
- 각 artifact는 `무엇을 증명하는가`, `누가 채우는가`, `반드시 들어가야 하는 핵심 문장`, `무엇을 쓰면 안 되는가`가 따로 있다.
- 이 문서의 역할은 stage packet의 `무엇을 닫을까`를 실제 제출물 수준의 `어떻게 써서 넘길까`로 바꾸는 것이다.

### 한 줄 목표
artifact를 `파일 존재 여부`가 아니라 `같은 atlas stage 문장을 쓰는 제출물`로 만든다.

### atlas stage canonical artifact set
- `manifest.md`
- `notes/hook-manifest.md`
- `checks/dom-sanity.md`
- `captures/atlas-1440-overview.png`
- `captures/atlas-1440-threshold-focus.png`
- `notes/density-review.md`
- `assets/starter-asset-list.md`
- `notes/handoff-line.md`

---

## 2. artifact contract table

## 3줄 요약
- 아래 표는 atlas 첫 stage에서 무엇을 제출해야 하는지보다, **각 파일이 무엇을 증명해야 하는지**를 먼저 고정한다.
- 프로그래머 / UI / 아트 / QA / PM은 이 표를 기준으로 artifact를 나눠 채우되, 최종 candidate는 bundle completeness로만 판단한다.
- 파일 하나가 좋아도 다른 파일이 generic wording이면 atlas green 후보가 아니다.

### 한 줄 목표
각 artifact를 `owner별 메모`가 아니라 `같은 stage 계약의 다른 면`으로 읽게 만든다.

| 파일 | 역할 | 주요 작성자 | 반드시 들어가야 하는 핵심 문장/내용 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- | --- |
| `manifest.md` | stage 상태/경계/다음 unlock 기준 | 프로그래머 + PM | `현재 stage는 atlas-route-node`, `이번 stage는 route node hook + threshold 읽힘까지만 닫음`, `next allowed step: battle-stakes-plaque unlock only after atlas green` | `Run Table 진행`, `battle도 병행`, `대체로 완료` |
| `notes/hook-manifest.md` | atlas root/state/threshold hook vocabulary 고정 | 프로그래머 | `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold`, hook별 실제 DOM 위치와 책임 | CSS 감상, battle/oath hook 계획 |
| `checks/dom-sanity.md` | hook 실제 장착 여부와 누락 여부 확인 | 프로그래머 + QA | root/state/threshold hook 존재 여부, 누락 없음/미결 note, atlas-only 범위 확인 | `UI 느낌 좋음`, `전체 polish 필요` |
| `captures/atlas-1440-overview.png` | board first-glance 증명 | UI | current node와 next threshold가 첫 시선에 읽히는 화면 | preview/log 중심 장면, generic screenshot naming |
| `captures/atlas-1440-threshold-focus.png` | threshold 읽힘 집중 증명 | UI | threshold 문맥과 route node 관계가 한 장에서 읽히는 화면 | battle stakes나 oath carry가 강조된 장면 |
| `notes/density-review.md` | overview/focus에 대한 짧은 verdict | UI + QA | `유지 / 약함 / 과함` verdict, 왜 그런지 2~4줄, atlas-only fix point | 장문 디자인 회고, 전체 Run Table 재설계 제안 |
| `assets/starter-asset-list.md` | Route Node Starter 충분성/금지 범위 | 아트 | starter asset 구성, 충분/약함 판단, 이번 stage에서 아직 안 하는 자산 | 지도 전체 리뉴얼 발주, 공통 frame 재설계 |
| `notes/handoff-line.md` | candidate/green 직전 복붙 문장 초안 | QA + PM | `atlas-route-node archive-ready candidate` 또는 `atlas-route-node green` 문장 초안, battle unlock 조건 | `거의 완료`, `다음은 자유롭게`, 큰 총괄 문장 |

---

## 3. note 작성 규칙

## 3줄 요약
- atlas 첫 stage note는 다 짧아야 한다. 길게 쓰는 순간 stage boundary보다 감상과 설계 재논의가 커진다.
- 공통 원칙은 `이번 stage가 무엇을 증명했는지 1줄`, `무엇은 아직 금지인지 1줄`, `다음 unlock 조건 1줄`이다.
- note마다 어휘가 달라지면 candidate는 보류한다.

### 한 줄 목표
모든 note가 `atlas-route-node` 같은 문장 규칙을 공유하게 만든다.

### note 공통 문장 규칙
1. 첫 줄에는 stage명을 넣는다. 예: `atlas-route-node stage에서는 ...`
2. 둘째 줄에는 이번 stage 포함 범위를 넣는다. 예: `route node state / threshold 읽힘까지만 닫았다.`
3. 셋째 줄에는 비범위/금지 범위를 넣는다. 예: `battle stakes / oath banner / preview-log 확장은 이번 stage 바깥이다.`
4. 마지막 줄에는 next allowed step 또는 candidate 조건을 넣는다.

### 금지되는 note 습관
- `대충 괜찮음`
- `거의 다 됨`
- `추후 전체 polish`
- `battle도 크게 문제 없음`
- `Run Table 전반적으로 안정적`

### 권장되는 짧은 문장 패턴
- `이번 stage는 atlas-route-node route state와 threshold 읽힘까지만 닫는다.`
- `현재 artifact는 atlas candidate 검토용이며 battle/oath unlock 근거로만 사용한다.`
- `preview/log 대개편과 지도 전체 리뉴얼은 이번 stage 비범위다.`

---

## 4. capture verdict rule

## 3줄 요약
- overview/focus 캡처는 시각 참고자료가 아니라 `atlas first-glance pass/fail` 증거다.
- 두 장 모두 있어야 하고, 각각 다른 질문에 답해야 한다.
- 한 장이라도 generic glamour shot이면 다시 in-progress다.

### 한 줄 목표
atlas 캡처를 `예쁜 샷`이 아니라 `다른 질문을 답하는 2장 세트`로 고정한다.

| 캡처 | 반드시 답해야 하는 질문 | pass 조건 | fail 예시 |
| --- | --- | --- | --- |
| `atlas-1440-overview.png` | `Run Atlas가 첫 시선에 핀 보드처럼 읽히는가?` | current node와 next threshold가 보조 패널보다 먼저 인지된다 | preview/log 또는 주변 설명이 먼저 튄다 |
| `atlas-1440-threshold-focus.png` | `threshold가 route node와 같은 언어로 읽히는가?` | threshold 문맥과 다음 노드 의미가 한 장에서 연결된다 | threshold가 별도 잡문처럼 보이거나 route node와 분리돼 보인다 |

### 캡처 메모 규칙
- density-review에는 각 캡처를 1~2줄씩 직접 참조한다.
- handoff-line에는 캡처 파일명을 직접 언급하지 않아도 되지만, candidate 근거는 overview/focus 두 장 세트로 적는다.
- 캡처 파일명은 atlas stage명을 포함한 canonical naming만 쓴다.

---

## 5. starter asset sanity rule

## 3줄 요약
- `starter-asset-list.md`는 아트 backlog 문서가 아니라 atlas 첫 stage의 **작은 충분성 증명**이다.
- 이 note는 무엇이 모자란지보다, 지금 stage에서 어디까지면 충분한지와 무엇을 일부러 안 하는지를 함께 적어야 한다.
- 큰 리뉴얼 제안은 이 파일에 적지 않는다.

### 한 줄 목표
`Route Node Starter`를 `지금 stage를 닫게 해 주는 최소 차이`로만 본다.

### 반드시 들어가야 하는 항목
- 현재 stage에서 확인한 starter asset 범위
- `충분 / 약함 / 과함` 중 하나의 verdict
- `이번 stage에서 아직 하지 않음` 항목 1~3개
- atlas와 다른 surface가 섞이지 않게 만드는 포인트 1줄

### 금지 항목
- `지도 전체 배경 재작업 필요`
- `Run Table 공통 frame 통합 필요`
- `battle/oath도 같은 asset family로 가자`
- 대형 발주 일정이나 새 key art 제안

---

## 6. role-specific handoff points

### 프로그래머에게 넘길 것
- atlas required artifacts의 중심은 `hook-manifest`와 `dom-sanity`다.
- 프로그래머 note가 battle/oath 계획까지 넘기기 시작하면 artifact bundle이 흐려진다.
- 첫 줄은 항상 atlas stage boundary부터 적는다.

### UI에게 넘길 것
- overview/focus는 두 장 모두 필요하다.
- UI note는 `어디가 더 예쁜가`가 아니라 `무엇이 먼저 읽히는가`를 판정한다.
- density-review는 4줄 내로 끝나는 편이 좋다.

### 아트에게 넘길 것
- `starter-asset-list.md`는 지금 발주할 큰 작업이 아니라 지금 stage를 닫게 해 주는 작은 판단 note다.
- atlas stage와 무관한 공통 style 제안은 다음 stage/후속 backlog로 미룬다.

### QA에게 넘길 것
- artifact 8종이 모두 있어도 wording drift가 있으면 candidate를 미룬다.
- `archive-ready candidate`는 file existence 체크가 아니라 stage contract alignment 판정이다.

### PM/기획에게 넘길 것
- green 기록 전 마지막 확인 포인트는 artifact completeness와 next allowed step 정렬이다.
- atlas green은 `battle unlock 가능`만 의미한다. `Run Table 전체 green`이 아니다.

---

## 7. archive-ready candidate rule

## 3줄 요약
- QA가 candidate를 쓰는 순간은 단순히 파일이 다 생겼을 때가 아니다.
- bundle 안의 note와 capture와 manifest가 모두 같은 stage boundary와 같은 unlock 문장을 말할 때만 candidate다.
- 그 전까지는 bootstrap-ready 또는 in-progress다.

### 한 줄 목표
candidate 판정을 file checklist가 아니라 `artifact alignment` 규칙으로 고정한다.

### candidate로 올리기 위한 필수 조건
1. artifact 8종이 atlas stage archive 안에 모두 존재한다.
2. `manifest.md`, `hook-manifest.md`, `dom-sanity.md`, `handoff-line.md`가 모두 `atlas-route-node` vocabulary를 쓴다.
3. `manifest.md`의 `next allowed step`이 `battle-stakes-plaque unlock only after atlas green`과 같은 뜻을 가진다.
4. `density-review.md`가 overview/focus 둘 다를 근거로 verdict를 남긴다.
5. `starter-asset-list.md`가 `이번 stage에서 아직 안 하는 것`을 분명히 적는다.

### candidate에서 다시 내려야 하는 경우
- capture는 있는데 note가 generic 표현만 쓴다.
- hook-manifest와 dom-sanity가 다른 hook 이름을 쓴다.
- handoff-line이 `Run Table green` 같은 큰 문장을 쓴다.
- starter note가 battle/oath 자산까지 같이 다룬다.

---

## 8. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| artifact는 다 생겼지만 note wording이 서로 달라 atlas green 직전 다시 해석 회의가 생기는 위험 | 첫 atlas stage가 archive는 있는데 handoff-ready는 아닌 상태로 남는다 | 이번 packet에서 artifact별 핵심 문장과 금지 문장을 file-level로 고정 | 첫 atlas candidate review 직전 |
| overview/focus 캡처가 존재만 하고 무엇을 증명하는지 적히지 않는 위험 | QA가 예쁜 샷과 first-glance 증거를 구분하기 어려워진다 | capture verdict rule로 캡처별 질문과 fail 예시를 분리 | 첫 density-review 작성 직전 |
| starter asset note가 backlog 확장 문서처럼 커지는 위험 | atlas green이 불필요하게 늦어지고 battle unlock이 밀린다 | starter asset sanity rule에서 큰 리뉴얼 문장을 금지 | 아트 note 작성 직전 |
| handoff-line이 `atlas green`을 `Run Table green`처럼 부풀리는 위험 | battle/oath가 너무 빨리 열리고 기록 문장이 흐려진다 | candidate/green 문장 용도를 atlas stage unlock 문장으로만 고정 | PM green 기록 직전 |

---

## 9. immediate next actions

1. 다음 atlas refinement 턴에서는 `token source-of-truth map -> token gap ledger -> token archive bootstrap -> token refinement queue -> atlas route node stage packet -> 이 required artifacts packet` 순서로 읽는다.
2. atlas stage archive를 먼저 만든다.
3. 프로그래머는 `manifest.md`와 `hook-manifest.md` 첫 줄을 atlas stage boundary 문장으로 먼저 채운다.
4. UI는 overview/focus 캡처를 만들고 `density-review.md`에 각 캡처 verdict를 1~2줄씩 남긴다.
5. 아트는 `starter-asset-list.md`에 `Route Node Starter` 충분성/비범위를 같이 적는다.
6. QA는 artifact 8종의 wording alignment를 확인한 뒤에만 `archive-ready candidate`를 적는다.
7. PM은 atlas green과 battle unlock을 같은 stage명 문장으로만 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`

---

## 한 장 결론
지금 atlas 첫 stage의 마지막 공백은 `무엇을 닫지?`가 아니라 **`좋아, 그걸 닫았다고 말하려면 정확히 어떤 artifact가 어떤 문장으로 같은 archive 안에 있어야 하지?`**였다. 이 문서는 그 공백을 artifact 8종의 역할, owner sentence, capture verdict, starter sanity, candidate rule까지 한 장으로 다시 압축해, 다음 atlas 턴이 단순 캡처 수집이나 감상형 note가 아니라 실제 battle unlock 직전 handoff bundle을 만드는 턴으로 읽히게 한다.
