# DiceSpell `Run Table` Atlas Route Node 첫 스테이지 패킷

## 3줄 요약
- 이 문서는 `token refinement queue`, `archive bootstrap`, `gap ledger`까지 이미 충분한 상태에서도 마지막으로 남아 있던 **`좋아, 그럼 첫 실제 refinement 턴에서 atlas-route-node는 정확히 어디까지를 한 stage로 닫지?`**를 고정하는 source-of-truth다.
- 핵심은 새 토큰을 더 설계하는 것이 아니라, `Run Atlas` 첫 stage를 **`route node hook 잠금 + threshold first-glance 검수 + starter asset sanity + atlas archive-ready candidate`**까지만 좁혀 battle/oath drift를 막는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `이번 턴에 atlas에서 무엇을 해야 하는지`, `아직 무엇을 하면 안 되는지`, `어떤 evidence가 모여야 battle이 열리는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`atlas-route-node`를 **stage 1개 = archive 1개 = green 문장 1개** 원칙으로 닫아, Run Table token refinement의 첫 실전 턴을 감상형 polish가 아니라 review 가능한 handoff stage로 고정한다.

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

즉 `무엇을 닫아야 하는가`, `어떤 archive를 먼저 열어야 하는가`, `atlas -> battle -> oath` 순서와 `bootstrap-ready -> archive-ready candidate -> stage green` ownership은 이미 정리돼 있다.

하지만 실제 첫 atlas 턴 직전에는 여전히 아래 공백이 남아 있다.

1. 큐는 고정됐지만, **atlas stage 안에서 정확히 어느 hook / capture / sanity / handoff line까지만 닫고 멈출지**는 여전히 사람이 다시 조합해야 한다.
2. `route node`와 `threshold`는 정의돼 있어도, 구현자가 `preview/log도 같이 만질까`, `battle stakes까지 같이 다듬을까`로 범위를 다시 넓히기 쉽다.
3. QA/PM은 `bootstrap-ready`와 `archive-ready candidate`를 구분할 규칙이 있어도, 실제 atlas green 전까지 무엇이 꼭 들어 있어야 하는지 한 장으로 다시 보고 싶어진다.
4. 아트/UI는 starter asset sanity와 1440p density 검수가 필요하지만, 이 둘을 `큰 리디자인` 없이 어디까지면 충분한지 첫 stage 언어로 다시 잠글 필요가 있다.

즉 지금 필요한 것은 새 디자인안이 아니라, **첫 atlas stage를 실제 실행 단위로 다시 압축한 stage packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `1. stage boundary`, `2. 구현 범위`, `4. evidence bundle` | `token source-of-truth map`, `token archive bootstrap`, `token refinement queue` | 이번 턴은 atlas route node hook/threshold closure만 닫고 battle/oath는 일부러 건드리지 않는다 |
| UI | `1. stage boundary`, `3. UI/아트 검수선`, `4. evidence bundle` | `token closing packet`, `surface handoff summary` | atlas는 `route board first-glance`만 닫는 stage이지 전체 Run Table density pass가 아니다 |
| 아트 | `3. UI/아트 검수선`, `5. role-specific handoff` | `token anchor packet`, `token evidence manifest` | `Route Node Starter` sanity만 보면 되고 지도 전체 리뉴얼은 아직 금지다 |
| QA | `4. evidence bundle`, `6. signoff rule`, `7. 리스크 & 후속과제` | `token evidence manifest`, `token refinement queue` | `archive-ready candidate`는 atlas required artifacts가 채워졌을 때만 쓴다 |
| PM/기획 | `0. first screen 결론`, `6. signoff rule`, `8. immediate next actions` | `token gap ledger`, `token refinement queue summary` | atlas green 전에는 battle/oath를 열지 않고, green 문장도 atlas stage명으로만 남긴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `첫 실제 refinement 턴에서 atlas-route-node는 정확히 어디까지를 한 stage로 닫지?`
- `무엇이 들어와야 archive-ready candidate라고 말할 수 있지?`
- `battle / oath는 이번 턴에 왜 아직 열면 안 되지?`

### 가장 짧은 답
- 이번 stage는 `Run Atlas route node`만 닫는다.
- 범위는 `surface root + state/threshold hook + 1440 overview/focus + starter asset sanity + atlas handoff line 초안`까지다.
- preview/log 대확장, battle stakes polish, oath banner tone pass는 아직 금지다.
- `atlas-route-node archive-ready candidate`는 QA가 atlas required artifacts를 모두 본 뒤에만 쓴다.

### 한 줄 판정 규칙
이번 atlas 턴은 **`route node가 board 첫 시선에서 읽히는지`를 닫는 stage**이지, Run Table 전체를 예쁘게 만드는 턴이 아니다.

---

## 1. stage boundary

## 3줄 요약
- atlas 첫 stage는 `Run Atlas` 전체가 아니라 `route node + threshold 읽힘`을 닫는 bounded stage다.
- 이 stage의 성공은 새 layout 발명이 아니라, `route node state`와 `next threshold`가 같은 문장으로 markup/evidence/review에 남는지로 판단한다.
- battle/oath로 drift하지 않는 것이 atlas green의 일부다.

### 한 줄 목표
atlas stage를 `route board first-glance closure`까지만 좁혀 다음 stage unlock 전까지 흔들리지 않게 만든다.

| 구분 | 이번 stage에 포함 | 이번 stage에서 금지 |
| --- | --- | --- |
| surface | `Run Atlas` route board, node, threshold 읽힘 | battle header/action panel, oath banner/result/carry |
| markup 계약 | `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold` | `data-stakes-slot`, `data-oath-role`, `data-overrun-cta` |
| UI 범위 | board first-glance, current/next threshold focus, node 밀도 검수 | preview/log panel 재설계, Run Table 전체 density pass |
| 아트 범위 | `Route Node Starter` sanity note | 지도 배경 전체 리뉴얼, Run Table 공통 frame 재설계 |
| 기록 문장 | `atlas-route-node bootstrap-ready`, `atlas-route-node archive-ready candidate`, `atlas-route-node green` | `Run Table 진행`, `token 거의 완료`, `battle도 같이`, `oath도 같이` |

### 이번 stage를 atlas-only로 고정하는 이유
1. queue packet이 고정한 첫 stage가 atlas이므로, 실제 첫 live turn도 atlas만 닫혀야 queue가 의미를 가진다.
2. `route node`는 battle/oath보다 경계 민감도가 낮아 archive discipline을 가장 안전하게 시험할 수 있다.
3. atlas green 전 battle/oath를 같이 만지면 `stage 1개 = archive 1개 = green 문장 1개` 원칙이 첫 턴부터 무너진다.

---

## 2. 구현 범위

## 3줄 요약
- 프로그래머 범위는 새 render family 발명보다 atlas live markup을 `route node state`와 `threshold` 언어로 잠그는 일이다.
- 이 stage에서는 helper vocabulary drift를 줄이고 evidence-friendly hook 이름을 남기는 것이 우선이다.
- route board 바깥 설명 panel까지 같은 턴에 크게 재설계하면 범위를 넘는다.

### 한 줄 목표
`renderRouteBoard()`와 연결된 atlas live markup을 review 가능한 hook vocabulary로 잠근다.

| 항목 | 현재 baseline | 이번 stage target | 이번 stage에서 아직 안 함 |
| --- | --- | --- | --- |
| surface root | atlas board root는 있으나 surface hook이 generic하게 읽힐 수 있음 | `data-run-surface="atlas"` | Run Curation/Choose Your Tome root 재정렬 |
| node state | current/past/shop/boss가 class/branch에 흩어져 있을 수 있음 | `data-route-node-state`로 검수 가능한 state 노출 | route tooltip/popup 계층 재설계 |
| threshold | next hint가 텍스트 문맥 의존도가 큼 | `data-route-threshold`와 대응 review note | threshold 계산 로직 확장 |
| helper vocabulary | route board와 side summary가 다른 단어를 쓸 위험 | 같은 route vocabulary로 정렬 | route copy 대량 재작성 |

### 프로그래머 handoff line
- 이번 atlas stage는 `DOM hook 잠금 + threshold 읽힘 고정`까지만 닫는다.
- `battle-stakes-plaque` / `oath-banner`용 hook은 이번 커밋에 넣지 않는다.
- helper를 새 tree로 갈아엎기보다, 지금 있는 route board가 review 가능한 단어를 쓰게 만드는 쪽이 우선이다.

---

## 3. UI / 아트 검수선

## 3줄 요약
- UI와 아트의 목표는 `Run Atlas가 핀 보드처럼 읽히는가`를 닫는 것이지, 전체 미감 리뉴얼이 아니다.
- overview/focus 두 캡처와 starter sanity note만으로도 이번 stage는 충분히 검수 가능해야 한다.
- atlas는 preview/log가 아니라 route node와 next threshold가 먼저 보여야 green 후보가 된다.

### 한 줄 목표
atlas를 `board first-glance` 기준으로 닫고, 나머지 surface polish는 일부러 뒤로 미룬다.

| 항목 | 이번 stage 완료선 | 아직 금지 |
| --- | --- | --- |
| overview capture | `captures/atlas-1440-overview.png`에서 current node와 next threshold가 먼저 읽힌다 | preview/log column 전체 개편 |
| focus capture | `captures/atlas-1440-threshold-focus.png`에서 threshold 집중점이 route board 언어와 같은 말로 읽힌다 | route node hover/animation 확장 |
| density note | `notes/density-review.md`에 `유지 / 약함 / 과함` 수준의 짧은 atlas verdict가 있다 | 감상형 장문 리뷰 |
| starter asset sanity | `assets/starter-asset-list.md`에 `Route Node Starter` sanity와 아직 금지 자산이 함께 적혀 있다 | 새 배경/새 공통 card family 발주 |

### UI / 아트 owner에게 넘길 것
- atlas stage는 `route node > threshold > 보조 설명` 순서를 닫는 turn이다.
- 캡처 이름은 반드시 atlas stage 이름을 말해야 한다.
- starter asset sanity는 `충분 / 약함 / 과함`을 판단하는 note면 충분하다. 큰 리뉴얼 제안은 이번 stage 범위 밖이다.

---

## 4. evidence bundle

## 3줄 요약
- atlas green은 느낌이 아니라 bundle로 판단한다.
- required artifacts가 stage archive 안에 같은 이름으로 모여야 QA가 `archive-ready candidate`를 쓸 수 있다.
- evidence가 stage 밖 임시 폴더에 흩어지면 이번 stage는 닫히지 않은 것으로 본다.

### 한 줄 목표
atlas를 `말`이 아니라 `같은 경로의 evidence bundle`로 닫는다.

### 필수 archive 경로
- `docs/artifacts/run-table-token-delivery/atlas-route-node/manifest.md`
- `docs/artifacts/run-table-token-delivery/atlas-route-node/captures/atlas-1440-overview.png`
- `docs/artifacts/run-table-token-delivery/atlas-route-node/captures/atlas-1440-threshold-focus.png`
- `docs/artifacts/run-table-token-delivery/atlas-route-node/notes/hook-manifest.md`
- `docs/artifacts/run-table-token-delivery/atlas-route-node/notes/density-review.md`
- `docs/artifacts/run-table-token-delivery/atlas-route-node/notes/handoff-line.md`
- `docs/artifacts/run-table-token-delivery/atlas-route-node/checks/dom-sanity.md`
- `docs/artifacts/run-table-token-delivery/atlas-route-node/assets/starter-asset-list.md`

### atlas stage required artifacts

| 파일 | 무엇을 증명하는가 | 누가 주로 채우는가 |
| --- | --- | --- |
| `manifest.md` | stage 상태, required artifacts, next allowed step, handoff line 초안 | 프로그래머 + PM |
| `notes/hook-manifest.md` | atlas root/state/threshold hook이 실제 markup에서 무엇인지 | 프로그래머 |
| `checks/dom-sanity.md` | root/state/threshold hook이 누락 없이 박혔는지 | 프로그래머 + QA |
| `captures/atlas-1440-overview.png` | route board first-glance | UI |
| `captures/atlas-1440-threshold-focus.png` | threshold focus와 next 읽힘 | UI |
| `notes/density-review.md` | `유지 / 약함 / 과함` verdict | UI + QA |
| `assets/starter-asset-list.md` | `Route Node Starter` sanity와 아직 금지 범위 | 아트 |
| `notes/handoff-line.md` | atlas stage 복붙용 green/candidate 문장 초안 | QA + PM |

### archive-ready candidate 최소선
1. atlas stage archive가 생성돼 있다.
2. 위 required artifacts가 모두 존재한다.
3. `hook-manifest.md`와 `dom-sanity.md`가 같은 hook vocabulary를 쓴다.
4. overview/focus 캡처 파일명이 atlas stage 목적을 그대로 말한다.
5. `manifest.md`의 `next allowed step`이 battle unlock 전제와 일치한다.

---

## 5. role-specific handoff points

### 프로그래머에게 넘길 것
- `Run Atlas`에서 닫아야 하는 것은 `route node state`와 `threshold`의 hook language다.
- `battle-stakes-plaque` / `oath-banner` 관련 hook을 같은 턴에 추가하지 않는다.
- atlas stage의 성공은 helper 대수술이 아니라 review 가능한 root/state/threshold anchor를 남기는 데 있다.

### UI에게 넘길 것
- 이번 턴 캡처는 atlas 두 장이면 충분하다.
- overview는 `current node + next threshold`, focus는 `threshold 읽힘`만 증명하면 된다.
- route board보다 preview/log가 먼저 보이면 fail note를 남기고 green 문장은 미룬다.

### 아트에게 넘길 것
- `Route Node Starter` sanity만 닫는다.
- atlas를 위해 새 배경/새 공통 frame을 열지 않는다.
- starter note에는 `이번 stage에 아직 안 하는 것`도 같이 적는다.

### QA에게 넘길 것
- `archive-ready candidate`는 required artifacts가 채워졌을 때만 쓴다.
- `bootstrap-ready`와 `archive-ready candidate`를 같은 review 문장으로 합치지 않는다.
- battle/oath 증거가 섞이면 atlas green이 아니라 atlas fail note를 남긴다.

### PM/기획에게 넘길 것
- atlas green은 `Run Table green`이 아니다.
- atlas green 뒤에만 battle unlock을 쓴다.
- 기록 문장은 반드시 stage명 기준으로 남긴다.

---

## 6. signoff rule

## 3줄 요약
- atlas stage도 `준비`, `진행`, `후보`, `green`이 다른 층위다.
- 같은 사람이 시작과 최종 green을 모두 쓰면 stage 경계가 다시 흐려진다.
- battle unlock은 atlas green 이후에만 열린다.

### 한 줄 목표
atlas stage의 signoff를 `제출`, `확인`, `기록`으로 분리한다.

| 단계 | 누가 먼저 적는가 | 무엇이 있어야 하는가 | 다음에 열리는 것 |
| --- | --- | --- | --- |
| `bootstrap-ready` | 프로그래머 | stage 폴더 + manifest + starter file set | atlas live edit 시작 |
| `archive-in-progress` | 프로그래머 / UI | 첫 hook note 또는 첫 capture 존재 | 중간 sanity review |
| `archive-ready candidate` | QA | atlas required artifacts 충족 | PM green 검토 |
| `atlas-route-node green` | PM/기획 | QA candidate + handoff line 합의 | `battle-stakes-plaque` unlock |

### 금지 문장
- `Run Table 거의 완료`
- `token refinement 진행중`
- `battle도 같이 시작`
- `atlas 사실상 green`

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| atlas stage가 다시 `route board + preview/log + battle preview` 묶음으로 커지는 위험 | 첫 stage부터 surface boundary가 흐려져 queue packet이 무력화된다 | 이번 packet에서 atlas 포함/금지 범위를 다시 표로 고정 | 다음 atlas first edit 직전 |
| required artifacts가 stage archive 밖 임시 경로에 흩어지는 위험 | QA candidate가 감각 의존형 판단으로 돌아간다 | atlas archive 경로와 required artifacts를 file-level로 못 박음 | 첫 capture drop 직전 |
| UI/아트가 starter sanity를 큰 리디자인 제안으로 확장하는 위험 | atlas green이 불필요하게 늦어지고 battle unlock이 밀린다 | 이번 stage는 `Route Node Starter` sanity만 본다고 고정 | atlas density review 직전 |
| PM/QA가 atlas green을 `Run Table green`처럼 크게 기록하는 위험 | battle/oath가 너무 빨리 열리고 tracker/run log 문장이 흐려진다 | signoff rule과 금지 문장을 stage명 기준으로 고정 | atlas green 기록 직전 |

---

## 8. immediate next actions

1. 실제 첫 refinement 턴에서는 `token source-of-truth map -> token gap ledger -> token archive bootstrap -> token refinement queue -> 이 atlas stage packet` 순서로 읽는다.
2. `docs/artifacts/run-table-token-delivery/atlas-route-node/` starter bundle부터 만든다.
3. 프로그래머는 `notes/hook-manifest.md`, `checks/dom-sanity.md`를 먼저 채운 뒤 live atlas edit에 들어간다.
4. UI는 `atlas-1440-overview.png`, `atlas-1440-threshold-focus.png` 두 장만 우선 예약/생성한다.
5. 아트는 `assets/starter-asset-list.md`에 `Route Node Starter` sanity와 아직 금지 자산을 함께 적는다.
6. QA가 `atlas-route-node archive-ready candidate`를 쓰기 전에는 battle/oath를 열지 않는다.
7. PM은 atlas green과 battle unlock을 같은 stage명 기준으로만 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 남은 가장 작은데도 실제로는 가장 중요한 공백은 **`atlas가 첫 stage라는 건 알겠는데, 그 첫 stage를 정확히 어디까지를 한 archive와 한 green 문장으로 닫을 것인가`**였다. 이 문서는 그 공백을 `stage boundary`, `hook/threshold 구현 범위`, `overview/focus/starter sanity evidence bundle`, `signoff rule`, `battle unlock 조건`까지 한 장으로 다시 압축해, 다음 refinement가 다시 큰 UI polish나 세 surface 동시 수정으로 흐르지 않게 만드는 atlas-first stage packet이다.
