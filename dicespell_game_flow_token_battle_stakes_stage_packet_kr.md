# DiceSpell `Run Table` Battle Stakes Plaque 첫 스테이지 패킷

## 3줄 요약
- 이 문서는 `atlas-route-node green`까지 정리된 상태에서도 다음 실제 refinement 턴 직전 마지막으로 남아 있던 **`좋아, 이제 battle-stakes-plaque는 정확히 어디까지를 한 stage로 닫지?`**를 고정하는 source-of-truth다.
- 핵심은 새 전투 화면을 다시 설계하는 것이 아니라, `Battle Table` 첫 stage를 **`current / next / grimoire slot hook 잠금 + stakes first-glance 검수 + starter asset sanity + battle archive-ready candidate`**까지만 좁혀 atlas 재개방과 oath drift를 막는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `atlas green 뒤 어떤 조건으로 battle을 열 수 있는지`, `이번 턴에 battle에서 무엇을 해야 하는지`, `아직 무엇을 하면 안 되는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`battle-stakes-plaque`를 **stage 1개 = archive 1개 = green 문장 1개** 원칙으로 닫아, atlas 다음 실제 Run Table refinement 턴을 다시 큰 전투 UI polish가 아니라 review 가능한 handoff stage로 고정한다.

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
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- 왜 atlas가 첫 stage였는가
- atlas green 뒤에만 battle이 열려야 하는가
- `bootstrap-ready -> archive-in-progress -> archive-ready candidate -> stage green` ownership은 무엇인가
- battle도 `current / next / grimoire` slot language로 닫아야 한다는 큰 방향은 무엇인가

하지만 실제 `battle-stakes-plaque` 착수 직전에는 여전히 아래 공백이 남아 있다.

1. queue는 battle이 다음이라고 말하지만, **battle stage 안에서 정확히 어느 slot hook / capture / sanity / handoff line까지만 닫고 멈출지**는 여전히 사람이 다시 조합해야 한다.
2. atlas green 뒤라 implementer가 `그럼 battle action panel도 같이`, `reward/shop side family도 같이`처럼 범위를 다시 키우기 쉽다.
3. UI/아트는 `current / next / grimoire` hierarchy와 `Stakes Plaque Starter` sanity만 보면 되는데, 이 둘이 `전투 전체 polish`와 다르다는 점을 첫 battle stage 언어로 다시 잠글 필요가 있다.
4. QA/PM은 atlas green 이후 battle을 열 수는 있지만, **어떤 evidence가 있어야 `battle archive-ready candidate`와 `battle green, oath-banner unlock`을 쓸 수 있는지**를 한 장으로 다시 보고 싶어진다.

즉 지금 필요한 것은 새 visual concept가 아니라, **atlas green 다음 실제 battle 턴을 실행 단위로 다시 압축한 battle stage packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `1. stage boundary`, `2. 구현 범위`, `4. evidence bundle` | `token source-of-truth map`, `token refinement queue`, `atlas candidate review packet` | 이번 턴은 battle stakes slot language만 닫고 atlas 재개방이나 oath/ending 경계 논의는 일부러 건드리지 않는다 |
| UI | `1. stage boundary`, `3. UI/아트 검수선`, `4. evidence bundle` | `token closing packet`, `surface handoff summary` | battle은 `current > next > grimoire` first-glance를 닫는 stage이지 action panel/HUD 전체 density pass가 아니다 |
| 아트 | `3. UI/아트 검수선`, `5. role-specific handoff` | `token anchor packet`, `token evidence manifest` | `Stakes Plaque Starter` sanity만 보면 되고 battle HUD 전체 리뉴얼은 아직 금지다 |
| QA | `4. evidence bundle`, `6. signoff rule`, `7. 리스크 & 후속과제` | `token evidence manifest`, `token refinement queue` | `battle archive-ready candidate`는 battle required bundle이 채워졌을 때만 쓰고 oath는 아직 unlock 전이다 |
| PM/기획 | `0. first screen 결론`, `6. signoff rule`, `8. immediate next actions` | `token gap ledger`, `token refinement queue summary` | battle green 전에는 oath를 열지 않고, green 문장도 `battle-stakes-plaque` stage명으로만 남긴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `atlas-route-node green` 뒤 다음 실제 refinement 턴에서 `battle-stakes-plaque`는 정확히 어디까지를 한 stage로 닫지?
- 무엇이 들어와야 `battle archive-ready candidate`라고 말할 수 있지?
- 왜 action panel / oath banner / reward-shop family는 이번 턴에 아직 열면 안 되지?

### 가장 짧은 답
- 이번 stage는 `Battle Table`의 stakes plaque만 닫는다.
- 범위는 `surface root + current/next/grimoire slot hook + 1440 overview/focus + starter asset sanity + battle handoff line 초안`까지다.
- action panel 대개편, atlas route node 재보정, oath banner tone pass, reward/shop/ending family 재논의는 아직 금지다.
- `battle-stakes-plaque archive-ready candidate`는 QA가 battle required bundle을 모두 본 뒤에만 쓴다.

### 한 줄 판정 규칙
이번 battle 턴은 **`current / next / grimoire stakes hierarchy가 첫 시선에 읽히는지`**를 닫는 stage이지, 전투 화면 전체를 예쁘게 만드는 턴이 아니다.

---

## 1. stage boundary

## 3줄 요약
- battle 첫 stage는 `Battle Table` 전체가 아니라 `stakes plaque`의 slot hierarchy를 닫는 bounded stage다.
- 이 stage의 성공은 새 combat shell 발명이 아니라, `current / next / grimoire`가 같은 문장으로 markup / evidence / review에 남는지로 판단한다.
- atlas를 다시 열지 않고 oath를 아직 열지 않는 것이 battle green의 일부다.

### 한 줄 목표
battle stage를 `stakes first-glance closure`까지만 좁혀 다음 oath unlock 전까지 흔들리지 않게 만든다.

| 구분 | 이번 stage에 포함 | 이번 stage에서 금지 |
| --- | --- | --- |
| surface | `Battle Table`의 stakes plaque, current/next/grimoire 읽힘 | action panel 전체, reward/shop side family, oath banner/result/carry |
| markup 계약 | `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"` | `data-route-node-state`, `data-route-threshold`, `data-oath-role`, `data-overrun-cta` |
| UI 범위 | stakes first-glance, slot hierarchy, 1440 overview/focus 검수 | battle HUD 전체 density pass, combat log/controls 재설계 |
| 아트 범위 | `Stakes Plaque Starter` sanity note | battle 배경/프레임 전체 리뉴얼, oath/ending side shell 확장 |
| 기록 문장 | `battle-stakes-plaque bootstrap-ready`, `battle-stakes-plaque archive-ready candidate`, `battle-stakes-plaque green` | `Battle Table 거의 완료`, `oath도 같이`, `Run Table 전반 green` |

### 이번 stage를 battle-only로 고정하는 이유
1. queue packet이 atlas 다음 stage를 battle로 고정했으므로, 실제 두 번째 live turn도 battle만 닫혀야 queue가 의미를 가진다.
2. `stakes plaque`는 oath보다 경계 민감도가 낮고, atlas보다 전투 정보 hierarchy가 복잡해 첫 후속 stage로 딱 맞다.
3. battle green 전에 oath를 같이 만지면 `stage 1개 = archive 1개 = green 문장 1개` 원칙이 둘째 턴에서 바로 무너진다.

---

## 2. 구현 범위

## 3줄 요약
- 프로그래머 범위는 battle live markup을 `current / next / grimoire` slot language로 잠그는 일이다.
- 이 stage에서는 stakes plaque vocabulary drift를 줄이고 evidence-friendly hook 이름을 남기는 것이 우선이다.
- action panel이나 보조 컬럼까지 같은 턴에 크게 재설계하면 범위를 넘는다.

### 한 줄 목표
`renderBattle(summary)`와 연결된 live stakes markup을 review 가능한 slot vocabulary로 잠근다.

| 항목 | 현재 baseline | 이번 stage target | 이번 stage에서 아직 안 함 |
| --- | --- | --- | --- |
| surface root | battle surface는 있으나 stakes가 generic card grid처럼 읽힐 수 있음 | `data-run-surface="battle-table"` | atlas/oath root 재정렬 |
| slot state | 현재 card 강조가 `.current` 중심이라 `next / grimoire` 역할이 약할 수 있음 | `data-stakes-slot="current|next|grimoire"`로 검수 가능한 slot 노출 | turn-by-turn HUD 재배치 |
| stakes hierarchy | stakes 카드가 정보 카드 3장처럼 읽힐 위험 | `current > next > grimoire` hierarchy를 한 문장으로 정렬 | battle action panel, log, controls 확장 |
| helper vocabulary | battle stakes와 주변 설명이 다른 말을 쓸 위험 | 같은 stakes vocabulary로 정렬 | battle copy 대량 재작성 |

### 프로그래머 handoff line
- 이번 battle stage는 `slot hook 잠금 + stakes first-glance 고정`까지만 닫는다.
- `oath-banner`용 hook이나 ending/reward family 언어는 이번 커밋에 넣지 않는다.
- helper를 새 tree로 갈아엎기보다, 지금 있는 battle stakes가 review 가능한 `current / next / grimoire` 단어를 쓰게 만드는 쪽이 우선이다.

---

## 3. UI / 아트 검수선

## 3줄 요약
- UI와 아트의 목표는 `Battle Table`이 stakes plaque 중심으로 먼저 읽히는가를 닫는 것이지, 전체 전투 미감 리뉴얼이 아니다.
- overview/focus 두 캡처와 starter sanity note만으로도 이번 stage는 충분히 검수 가능해야 한다.
- battle은 action panel보다 `current > next > grimoire` stakes hierarchy가 먼저 보여야 green 후보가 된다.

### 한 줄 목표
battle을 `stakes first-glance` 기준으로 닫고, 나머지 전투 polish는 일부러 뒤로 미룬다.

| 항목 | 이번 stage 완료선 | 아직 금지 |
| --- | --- | --- |
| overview capture | `captures/battle-1440-overview.png`에서 `current > next > grimoire`가 다른 전투 카드보다 먼저 읽힌다 | action panel / log / rewards까지 한 화면에서 같이 닫기 |
| focus capture | `captures/battle-1440-stakes-focus.png`에서 stakes plaque hierarchy와 slot 이름이 한 장에서 읽힌다 | spell list, dice tray, 하단 controls 재설계 |
| density note | `notes/density-review.md`에 `유지 / 약함 / 과함` 수준의 짧은 battle verdict가 있다 | 감상형 장문 회고, 전체 combat shell 토론 |
| starter asset sanity | `assets/starter-asset-list.md`에 `Stakes Plaque Starter` sanity와 아직 금지 자산이 함께 적혀 있다 | battle HUD 전체 리뉴얼, oath/ending shared frame 제안 |

### UI / 아트 owner에게 넘길 것
- battle stage는 `current > next > grimoire > 나머지 보조 정보` 순서를 닫는 turn이다.
- 캡처 이름은 반드시 battle stage 이름을 말해야 한다.
- starter asset sanity는 `충분 / 약함 / 과함`을 판단하는 note면 충분하다. 큰 combat shell 리뉴얼 제안은 이번 stage 범위 밖이다.

---

## 4. evidence bundle

## 3줄 요약
- battle green은 느낌이 아니라 bundle로 판단한다.
- required artifacts가 stage archive 안에 같은 이름으로 모여야 QA가 `archive-ready candidate`를 쓸 수 있다.
- evidence가 stage 밖 임시 폴더에 흩어지면 이번 stage는 닫히지 않은 것으로 본다.

### 한 줄 목표
battle을 `말`이 아니라 `같은 경로의 evidence bundle`로 닫는다.

### 필수 archive 경로
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/manifest.md`
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/captures/battle-1440-overview.png`
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/captures/battle-1440-stakes-focus.png`
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/notes/hook-manifest.md`
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/notes/density-review.md`
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/notes/handoff-line.md`
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/checks/dom-sanity.md`
- `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/assets/starter-asset-list.md`

### battle stage required artifacts

| 파일 | 무엇을 증명하는가 | 누가 주로 채우는가 |
| --- | --- | --- |
| `manifest.md` | stage 상태, required artifacts, next allowed step, handoff line 초안 | 프로그래머 + PM |
| `notes/hook-manifest.md` | battle root/current/next/grimoire hook이 실제 markup에서 무엇인지 | 프로그래머 |
| `checks/dom-sanity.md` | root/current/next/grimoire hook이 누락 없이 박혔는지 | 프로그래머 + QA |
| `captures/battle-1440-overview.png` | stakes first-glance와 slot hierarchy | UI |
| `captures/battle-1440-stakes-focus.png` | current / next / grimoire 집중 읽힘 | UI |
| `notes/density-review.md` | `유지 / 약함 / 과함` verdict | UI + QA |
| `assets/starter-asset-list.md` | `Stakes Plaque Starter` sanity와 아직 금지 범위 | 아트 |
| `notes/handoff-line.md` | battle stage 복붙용 green/candidate 문장 초안 | QA + PM |

### archive-ready candidate 최소선
1. battle stage archive가 생성돼 있다.
2. 위 required artifacts가 모두 존재한다.
3. `hook-manifest.md`와 `dom-sanity.md`가 같은 slot vocabulary를 쓴다.
4. overview/focus 캡처 파일명이 battle stage 목적을 그대로 말한다.
5. `manifest.md`의 `next allowed step`이 `oath-banner unlock only after battle green`과 일치한다.

---

## 5. role-specific handoff points

### 프로그래머에게 넘길 것
- `Battle Table`에서 닫아야 하는 것은 `current / next / grimoire` stakes slot language다.
- atlas hook 재수정이나 `oath-banner` 관련 hook을 같은 턴에 넣지 않는다.
- battle stage의 성공은 helper 대수술이 아니라 review 가능한 slot anchor를 남기는 데 있다.

### UI에게 넘길 것
- 이번 턴 캡처는 battle 두 장이면 충분하다.
- overview는 `current > next > grimoire`, focus는 `stakes hierarchy와 slot 이름`만 증명하면 된다.
- stakes보다 action panel이나 하단 controls가 먼저 보이면 fail note를 남기고 green 문장은 미룬다.

### 아트에게 넘길 것
- `Stakes Plaque Starter` sanity만 닫는다.
- battle을 위해 새 battle shell이나 oath/ending 공용 frame을 열지 않는다.
- starter note에는 `이번 stage에 아직 안 하는 것`도 같이 적는다.

### QA에게 넘길 것
- `battle archive-ready candidate`는 required artifacts가 채워졌을 때만 쓴다.
- `bootstrap-ready`와 `archive-ready candidate`를 같은 review 문장으로 합치지 않는다.
- atlas hook 회귀나 oath 증거가 섞이면 battle green이 아니라 battle fail note를 남긴다.

### PM/기획에게 넘길 것
- battle green은 `Run Table green`이 아니다.
- battle green 뒤에만 oath unlock을 쓴다.
- 기록 문장은 반드시 stage명 기준으로 남긴다.

---

## 6. signoff rule

## 3줄 요약
- battle stage도 `준비`, `진행`, `후보`, `green`이 다른 층위다.
- 같은 사람이 시작과 최종 green을 모두 쓰면 stage 경계가 다시 흐려진다.
- oath unlock은 battle green 이후에만 열린다.

### 한 줄 목표
battle stage의 signoff를 `제출`, `확인`, `기록`으로 분리한다.

| 단계 | 누가 먼저 적는가 | 무엇이 있어야 하는가 | 다음에 열리는 것 |
| --- | --- | --- | --- |
| `bootstrap-ready` | 프로그래머 | stage 폴더 + manifest + starter file set | battle live edit 시작 |
| `archive-in-progress` | 프로그래머 / UI | 첫 hook note 또는 첫 capture 존재 | 중간 sanity review |
| `archive-ready candidate` | QA | battle required artifacts 충족 | PM green 검토 |
| `battle-stakes-plaque green` | PM/기획 | QA candidate + handoff line 합의 | `oath-banner` unlock |

### 금지 문장
- `Battle Table 거의 완료`
- `전투 UI도 사실상 닫힘`
- `oath도 같이 시작`
- `battle 사실상 green`

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| battle stage가 다시 `stakes + action panel + log + rewards` 묶음으로 커지는 위험 | 둘째 stage부터 boundary가 흐려져 queue packet이 무력화된다 | 이번 packet에서 battle 포함/금지 범위를 다시 표로 고정 | 다음 battle first edit 직전 |
| required artifacts가 battle stage archive 밖 임시 경로에 흩어지는 위험 | QA candidate가 감각 의존형 판단으로 돌아간다 | battle archive 경로와 required artifacts를 file-level로 못 박음 | 첫 battle capture drop 직전 |
| UI/아트가 starter sanity를 combat shell 전체 리디자인 제안으로 확장하는 위험 | battle green이 불필요하게 늦어지고 oath unlock이 밀린다 | 이번 stage는 `Stakes Plaque Starter` sanity만 본다고 고정 | battle density review 직전 |
| PM/QA가 battle green을 `Battle Table green`이나 `Run Table green`처럼 크게 기록하는 위험 | oath가 너무 빨리 열리고 tracker/run log 문장이 흐려진다 | signoff rule과 금지 문장을 stage명 기준으로 고정 | battle green 기록 직전 |
| atlas review 문장을 battle에도 그대로 복붙해 `candidate = review pass`, `green = oath unlock 기록`이 다시 흐려지는 위험 | atlas와 battle stage 문장이 섞여 stage archive 의미가 약해진다 | battle 전용 candidate/green stage명을 따로 고정 | battle handoff-line 초안 작성 직전 |

---

## 8. immediate next actions

1. 실제 다음 refinement 턴에서는 `token source-of-truth map -> token gap ledger -> token refinement queue -> atlas candidate review packet -> 이 battle stage packet` 순서로 읽는다.
2. `atlas-route-node green` evidence가 먼저 있는지 확인한다.
3. `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/` starter bundle부터 만든다.
4. 프로그래머는 `notes/hook-manifest.md`, `checks/dom-sanity.md`를 먼저 채운 뒤 live battle stakes edit에 들어간다.
5. UI는 `battle-1440-overview.png`, `battle-1440-stakes-focus.png` 두 장만 우선 예약/생성한다.
6. 아트는 `assets/starter-asset-list.md`에 `Stakes Plaque Starter` sanity와 아직 금지 자산을 함께 적는다.
7. QA가 `battle-stakes-plaque archive-ready candidate`를 쓰기 전에는 oath를 열지 않는다.
8. PM은 `battle-stakes-plaque green, oath-banner unlock` 문장만 남기고 그 다음에만 oath unlock을 연다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 다음 가장 가치 큰 공백은 **`atlas 다음 stage가 battle이라는 건 알겠는데, 그 battle stage를 정확히 어디까지를 한 archive와 한 green 문장으로 닫을 것인가`**였다. 이 문서는 그 공백을 `stage boundary`, `current/next/grimoire 구현 범위`, `overview/focus/starter sanity evidence bundle`, `signoff rule`, `oath unlock 조건`까지 한 장으로 다시 압축해, 다음 refinement가 다시 큰 전투 UI polish나 atlas/oath 동시 수정으로 흐르지 않게 만드는 battle-first stage packet이다.
