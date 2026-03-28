# DiceSpell `Run Table` Battle Stakes Plaque Required Artifacts 패킷

## 3줄 요약
- 이 문서는 `battle-stakes-plaque stage packet`까지로도 남아 있던 마지막 실무 공백인 **`좋아, battle 두 번째 stage가 archive-ready candidate라고 말하려면 정확히 어떤 artifact가 어떤 밀도로 들어 있어야 하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 전투 화면을 더 설계하는 것이 아니라, battle stage evidence bundle을 **파일 이름, 작성 책임, 필수 문장, pass/fail 기준**까지 다시 좁혀 프로그래머 / UI / 아트 / QA / PM이 같은 제출물로 말하게 만드는 것이다.
- 첫 화면만 읽어도 `무엇이 있어야 QA가 battle candidate를 쓸 수 있는지`, `무엇이 비어 있으면 아직 bootstrap/in-progress인지`, `왜 oath unlock 전 마지막 점검이 artifact alignment인지`가 바로 보여야 한다.

## 한 줄 목표
`battle-stakes-plaque` 두 번째 stage를 **required artifacts 8종 + artifact별 owner sentence + archive-ready 판정선 1개**로 잠가, atlas green 다음 실제 refinement 턴의 제출물 언어를 감상형 note가 아니라 handoff-ready bundle로 고정한다.

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
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- 둘째 stage가 왜 battle인지
- stage boundary가 어디까지인지
- `bootstrap-ready -> archive-in-progress -> archive-ready candidate -> battle green` 순서와 ownership은 무엇인지
- oath를 왜 아직 열면 안 되는지

하지만 실제 battle 턴 직전에는 여전히 다음 공백이 남아 있다.

1. `required artifacts` 목록은 stage packet에도 있지만, **각 artifact를 어느 owner가 어떤 문장 밀도로 채워야 하는지**가 한 장으로 다시 보이지 않는다.
2. `capture가 2장 필요하다`는 사실만으로는 충분하지 않다. QA/PM은 그 캡처가 무엇을 증명해야 하는지, 어떤 실패면 다시 in-progress로 내려야 하는지까지 같은 표로 보고 싶어진다.
3. 프로그래머 / UI / 아트가 note를 남길 때 wording이 조금씩 달라지면 `battle archive-ready candidate`가 있어도 oath unlock 직전 다시 설명 회의를 해야 한다.
4. `manifest.md`가 있어도 `hook-manifest`, `dom-sanity`, `density-review`, `starter-asset-list`, `handoff-line`이 서로 다른 단계 언어를 쓰면 battle green부터 `stakes only`가 아니라 `Battle Table 거의 완료` 같은 half-closure가 생긴다.

즉 지금 필요한 것은 새 디자인안이 아니라, **battle 두 번째 stage의 artifact bundle을 field-level handoff 계약으로 다시 압축한 required artifacts packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. artifact contract table`, `3. note 작성 규칙` | `battle stakes stage packet`, `token evidence manifest`, `token archive bootstrap` | hook/diff note는 generic battle 메모가 아니라 battle stage vocabulary로 써야 한다 |
| UI | `2. artifact contract table`, `4. capture verdict rule`, `6. role-specific handoff points` | `token closing packet`, `battle stage summary` | overview/focus 캡처는 예쁜 전투 장면이 아니라 current/next/grimoire first-glance를 증명해야 한다 |
| 아트 | `2. artifact contract table`, `5. starter asset sanity rule` | `token anchor packet`, `token evidence manifest` | starter note는 combat shell 제안이 아니라 Stakes Plaque Starter 충분성/금지 범위를 말해야 한다 |
| QA | `2. artifact contract table`, `7. archive-ready candidate rule`, `8. 리스크 & 후속과제` | `token refinement queue`, `battle stakes stage packet` | artifact 8종이 같은 battle stage 문장을 쓸 때만 candidate를 올린다 |
| PM/기획 | `0. first screen 결론`, `7. archive-ready candidate rule`, `9. immediate next actions` | `token gap ledger`, `token source-of-truth map summary` | battle green은 artifact completeness를 보고 쓰는 문장이지 `전투 UI 거의 완료`가 아니다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `battle-stakes-plaque archive-ready candidate`를 말하려면 어떤 artifact가 반드시 있어야 하지?
- `어떤 note/capture는 아직 초안이고 어떤 순간부터 handoff-ready라고 말할 수 있지?`
- `oath unlock 전 마지막으로 봐야 하는 제출물은 무엇이지?`

### 가장 짧은 답
- battle 두 번째 stage는 **artifact 8종이 같은 stage vocabulary를 쓸 때만** candidate로 본다.
- capture 2장, note 4장, check 1장, manifest 1장이 다 필요하다.
- 각 artifact는 `battle-stakes-plaque`, `current/next/grimoire`, `stakes first-glance`, `starter`, `candidate/green 전 단계` 언어를 유지해야 한다.
- 그중 하나라도 generic summary나 atlas/oath 언어로 흐르면 아직 `archive-in-progress`다.

### 한 줄 판정 규칙
battle 두 번째 stage의 closing은 **캡처 개수**가 아니라 **artifact 간 문장 정렬까지 포함한 bundle completeness**로 판단한다.

---

## 1. core structure

## 3줄 요약
- battle required artifacts는 `상태`, `hook`, `검수`, `시각 증거`, `아트 sanity`, `handoff 문장` 6축으로 나뉜다.
- 각 artifact는 `무엇을 증명하는가`, `누가 채우는가`, `반드시 들어가야 하는 핵심 문장`, `무엇을 쓰면 안 되는가`가 따로 있다.
- 이 문서의 역할은 stage packet의 `무엇을 닫을까`를 실제 제출물 수준의 `어떻게 써서 넘길까`로 바꾸는 것이다.

### 한 줄 목표
artifact를 `파일 존재 여부`가 아니라 `같은 battle stage 문장을 쓰는 제출물`로 만든다.

### battle stage canonical artifact set
- `manifest.md`
- `notes/hook-manifest.md`
- `checks/dom-sanity.md`
- `captures/battle-1440-overview.png`
- `captures/battle-1440-stakes-focus.png`
- `notes/density-review.md`
- `assets/starter-asset-list.md`
- `notes/handoff-line.md`

---

## 2. artifact contract table

## 3줄 요약
- 아래 표는 battle 두 번째 stage에서 무엇을 제출해야 하는지보다, **각 파일이 무엇을 증명해야 하는지**를 먼저 고정한다.
- 프로그래머 / UI / 아트 / QA / PM은 이 표를 기준으로 artifact를 나눠 채우되, 최종 candidate는 bundle completeness로만 판단한다.
- 파일 하나가 좋아도 다른 파일이 generic wording이면 battle green 후보가 아니다.

### 한 줄 목표
각 artifact를 `owner별 메모`가 아니라 `같은 stage 계약의 다른 면`으로 읽게 만든다.

| 파일 | 역할 | 주요 작성자 | 반드시 들어가야 하는 핵심 문장/내용 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- | --- |
| `manifest.md` | stage 상태/경계/다음 unlock 기준 | 프로그래머 + PM | `현재 stage는 battle-stakes-plaque`, `이번 stage는 current / next / grimoire stakes 읽힘까지만 닫음`, `next allowed step: oath-banner unlock only after battle green` | `Battle Table 진행`, `oath도 병행`, `전투 UI 대체로 완료` |
| `notes/hook-manifest.md` | battle root/slot hook vocabulary 고정 | 프로그래머 | `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"`, hook별 실제 DOM 위치와 책임 | 액션 패널 감상, atlas/oath hook 계획 |
| `checks/dom-sanity.md` | hook 실제 장착 여부와 누락 여부 확인 | 프로그래머 + QA | root/slot hook 존재 여부, 누락 없음/미결 note, battle-only 범위 확인 | `UI 느낌 좋음`, `전투 전체 polish 필요` |
| `captures/battle-1440-overview.png` | stakes first-glance 증명 | UI | current stakes가 next/grimoire 및 action panel보다 먼저 읽히는 화면 | generic combat screenshot, atlas/oath naming |
| `captures/battle-1440-stakes-focus.png` | slot hierarchy 집중 증명 | UI | current / next / grimoire 관계와 stakes plaque 집중 읽힘 | dice tray, spell list, controls 중심 샷 |
| `notes/density-review.md` | overview/focus에 대한 짧은 verdict | UI + QA | `유지 / 약함 / 과함` verdict, 왜 그런지 2~4줄, battle-only fix point | 장문 전투 미감 회고, 전체 HUD 재설계 제안 |
| `assets/starter-asset-list.md` | Stakes Plaque Starter 충분성/금지 범위 | 아트 | starter asset 구성, 충분/약함 판단, 이번 stage에서 아직 안 하는 자산 | combat shell 전체 리뉴얼 발주, ending/oath 공용 frame 제안 |
| `notes/handoff-line.md` | candidate/green 직전 복붙 문장 초안 | QA + PM | `battle-stakes-plaque archive-ready candidate` 또는 `battle-stakes-plaque green` 문장 초안, oath unlock 조건 | `전투 UI 거의 완료`, `Run Table green`, 큰 총괄 문장 |

---

## 3. note 작성 규칙

## 3줄 요약
- battle 두 번째 stage note는 다 짧아야 한다. 길게 쓰는 순간 stage boundary보다 감상과 설계 재논의가 커진다.
- 공통 원칙은 `이번 stage가 무엇을 증명했는지 1줄`, `무엇은 아직 금지인지 1줄`, `다음 unlock 조건 1줄`이다.
- note마다 어휘가 달라지면 candidate는 보류한다.

### 한 줄 목표
모든 note가 `battle-stakes-plaque` 같은 문장 규칙을 공유하게 만든다.

### note 공통 문장 규칙
1. 첫 줄에는 stage명을 넣는다. 예: `battle-stakes-plaque stage에서는 ...`
2. 둘째 줄에는 이번 stage 포함 범위를 넣는다. 예: `current / next / grimoire slot과 stakes 읽힘까지만 닫았다.`
3. 셋째 줄에는 비범위/금지 범위를 넣는다. 예: `action panel, atlas 재보정, oath banner 확장은 이번 stage 바깥이다.`
4. 마지막 줄에는 next allowed step 또는 candidate 조건을 넣는다.

### 금지되는 note 습관
- `대충 괜찮음`
- `전투는 거의 다 됨`
- `추후 전체 polish`
- `oath도 같이 크게 문제 없음`
- `Run Table 전반적으로 안정적`

### 권장되는 짧은 문장 패턴
- `이번 stage는 battle-stakes-plaque current / next / grimoire slot 읽힘까지만 닫는다.`
- `현재 artifact는 battle candidate 검토용이며 oath unlock 근거로만 사용한다.`
- `action panel 개편과 ending/oath family 조정은 이번 stage 비범위다.`

---

## 4. capture verdict rule

## 3줄 요약
- overview/focus 캡처는 시각 참고자료가 아니라 `battle stakes first-glance pass/fail` 증거다.
- 두 장 모두 있어야 하고, 각각 다른 질문에 답해야 한다.
- 한 장이라도 generic glamour shot이면 다시 in-progress다.

### 한 줄 목표
battle 캡처를 `예쁜 샷`이 아니라 `다른 질문을 답하는 2장 세트`로 고정한다.

| 캡처 | 반드시 답해야 하는 질문 | pass 조건 | fail 예시 |
| --- | --- | --- | --- |
| `battle-1440-overview.png` | `Battle Table이 첫 시선에 stakes plaque 중심으로 읽히는가?` | current stakes가 next/grimoire 및 action panel보다 먼저 인지된다 | action panel, 로그, dice tray가 먼저 튄다 |
| `battle-1440-stakes-focus.png` | `current / next / grimoire slot 관계가 같은 언어로 읽히는가?` | 세 slot과 stakes plaque hierarchy가 한 장에서 연결된다 | current만 강조되고 next/grimoire가 보조 카드처럼 흐려진다 |

### 캡처 메모 규칙
- density-review에는 각 캡처를 1~2줄씩 직접 참조한다.
- handoff-line에는 캡처 파일명을 직접 언급하지 않아도 되지만, candidate 근거는 overview/focus 두 장 세트로 적는다.
- 캡처 파일명은 battle stage명을 포함한 canonical naming만 쓴다.

---

## 5. starter asset sanity rule

## 3줄 요약
- `starter-asset-list.md`는 아트 backlog 문서가 아니라 battle 두 번째 stage의 **작은 충분성 증명**이다.
- 이 note는 무엇이 모자란지보다, 지금 stage에서 어디까지면 충분한지와 무엇을 일부러 안 하는지를 함께 적어야 한다.
- 큰 리뉴얼 제안은 이 파일에 적지 않는다.

### 한 줄 목표
`Stakes Plaque Starter`를 `지금 stage를 닫게 해 주는 최소 차이`로만 본다.

### 반드시 들어가야 하는 항목
- 현재 stage에서 확인한 starter asset 범위
- `충분 / 약함 / 과함` 중 하나의 verdict
- `이번 stage에서 아직 하지 않음` 항목 1~3개
- battle과 다른 surface가 섞이지 않게 만드는 포인트 1줄

### 금지 항목
- `전투 전체 HUD 재작업 필요`
- `Run Table 공통 frame 통합 필요`
- `atlas/oath도 같은 asset family로 가자`
- 대형 발주 일정이나 새 key art 제안

---

## 6. role-specific handoff points

### 프로그래머에게 넘길 것
- battle required artifacts의 중심은 `hook-manifest`와 `dom-sanity`다.
- 프로그래머 note가 oath나 전체 Battle Table 계획까지 넘기기 시작하면 artifact bundle이 흐려진다.
- 첫 줄은 항상 battle stage boundary부터 적는다.

### UI에게 넘길 것
- overview/focus는 두 장 모두 필요하다.
- UI note는 `어디가 더 예쁜가`가 아니라 `무엇이 먼저 읽히는가`를 판정한다.
- density-review는 4줄 내로 끝나는 편이 좋다.

### 아트에게 넘길 것
- `starter-asset-list.md`는 지금 발주할 큰 작업이 아니라 지금 stage를 닫게 해 주는 작은 판단 note다.
- battle stage와 무관한 combat shell 리뉴얼 제안은 다음 stage/후속 backlog로 미룬다.

### QA에게 넘길 것
- artifact 8종이 모두 있어도 wording drift가 있으면 candidate를 미룬다.
- `archive-ready candidate`는 file existence 체크가 아니라 stage contract alignment 판정이다.

### PM/기획에게 넘길 것
- green 기록 전 마지막 확인 포인트는 artifact completeness와 next allowed step 정렬이다.
- battle green은 `oath unlock 가능`만 의미한다. `전투 UI 전체 green`이 아니다.

---

## 7. archive-ready candidate rule

## 3줄 요약
- QA가 candidate를 쓰는 순간은 단순히 파일이 다 생겼을 때가 아니다.
- bundle 안의 note와 capture와 manifest가 모두 같은 stage boundary와 같은 unlock 문장을 말할 때만 candidate다.
- 그 전까지는 bootstrap-ready 또는 in-progress다.

### 한 줄 목표
candidate 판정을 file checklist가 아니라 `artifact alignment` 규칙으로 고정한다.

### candidate로 올리기 위한 필수 조건
1. artifact 8종이 battle stage archive 안에 모두 존재한다.
2. `manifest.md`, `hook-manifest.md`, `dom-sanity.md`, `handoff-line.md`가 모두 `battle-stakes-plaque` vocabulary를 쓴다.
3. `manifest.md`의 `next allowed step`이 `oath-banner unlock only after battle green`과 같은 뜻을 가진다.
4. `density-review.md`가 overview/focus 둘 다를 근거로 verdict를 남긴다.
5. `starter-asset-list.md`가 `이번 stage에서 아직 안 하는 것`을 분명히 적는다.

### candidate에서 다시 내려야 하는 경우
- capture는 있는데 note가 generic 표현만 쓴다.
- hook-manifest와 dom-sanity가 다른 hook 이름을 쓴다.
- handoff-line이 `Battle Table green`이나 `Run Table green` 같은 큰 문장을 쓴다.

---

## 8. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| battle required artifacts가 없어 overview/focus 캡처와 slot hook note가 서로 다른 문장으로 흩어지는 위험 | QA/PM이 `battle archive-ready candidate`를 감으로 쓰거나, action panel/HUD polish를 같은 stage 안쪽처럼 오독할 수 있다 | 이번 packet으로 battle artifact 8종의 역할, 필수 문장, 금지 문장, candidate 최소선을 다시 고정 | 다음 battle-stakes-plaque archive 작성 직전 |
| starter asset note가 stakes plaque sanity가 아니라 combat shell 전체 backlog로 커지는 위험 | battle green이 불필요하게 늦어지고 `stakes only` stage boundary가 흐려진다 | `Stakes Plaque Starter`를 충분성 + 비범위 선언 note로만 정의 | 다음 starter-asset note 작성 직전 |
| handoff-line이 `oath unlock` 대신 `전투 UI 거의 완료` 같은 큰 문장을 쓰는 위험 | stage 1개 = archive 1개 = green 문장 1개 원칙이 둘째 stage에서 무너진다 | handoff-line contract와 candidate rule을 분리해 `battle-stakes-plaque` stage 언어만 허용 | 다음 QA candidate 기록 직전 |

---

## 9. immediate next actions

1. 다음 battle refinement 직전에는 `token refinement queue -> battle stakes stage packet -> 이 required artifacts packet` 순서로 읽는다.
2. 프로그래머는 `manifest.md`, `hook-manifest.md`, `dom-sanity.md` 세 파일부터 `battle-stakes-plaque` vocabulary로 맞춘다.
3. UI는 overview/focus 두 장과 density-review를 같은 질문으로 묶는다.
4. 아트는 `Stakes Plaque Starter` 충분성과 비범위 선언만 짧게 적는다.
5. QA는 artifact 8종의 wording alignment가 맞을 때만 `battle archive-ready candidate`를 기록하고, PM은 그 뒤에만 `battle-stakes-plaque green, oath-banner unlock`을 적는다.

---

## 한 장 결론
지금 battle 두 번째 stage의 마지막 공백은 `무엇을 더 설계하지?`가 아니다. 이제 남은 공백은 **`좋아, stakes stage evidence가 모였다고 치자. 그런데 어떤 artifact가 어떤 문장으로 정렬돼야 QA가 candidate를 쓰고 PM이 oath unlock을 열지?`**다. 이 문서는 그 마지막 제출물 공백을 field-level 계약으로 다시 고정해, battle 턴이 다시 전투 전체 polish나 큰 green 문장으로 흐르지 않게 만드는 required artifacts packet이다.
