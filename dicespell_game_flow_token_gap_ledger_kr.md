# DiceSpell `Run Table` Token 구현 갭 레저

## 3줄 요약
- 이 문서는 `surface handoff -> token anchor -> delivery workboard -> closing packet -> evidence manifest -> source-of-truth map`까지 충분히 쌓인 뒤에도 남아 있는 **실제 구현 갭**을 `Run Atlas / Battle Table / Overrun Oath` 3면 기준으로 다시 압축한다.
- 핵심은 새 토큰을 더 설계하는 것이 아니라, `문서 blocker`와 `실구현 blocker`를 분리해 프로그래머 / UI / 아트 / QA / PM이 지금 무엇을 닫아야 하는지 같은 표로 보게 만드는 것이다.
- 첫 화면만 읽어도 `무엇은 이미 handoff-ready이고`, `무엇이 아직 markup/CSS/review/archive evidence에서 비어 있는지`, `누가 어떤 증거를 남겨야 closing이라고 말할 수 있는지`가 바로 보여야 한다.

## 한 줄 목표
`route node / stakes plaque / oath banner` 후속 refinement를 더 이상 `문서가 많으니 거의 끝났다`나 `뭘 먼저 닫지?` 상태로 두지 않고, **surface별 남은 code/UI/art/review gap**으로 다시 좁힌다.

## 왜 이 문서가 필요한가
지금 Run Table 문서 세트는 충분히 강하다.

이미 닫힌 것:
- `docs/dicespell_game_flow_surface_handoff_packet_kr.md`
- `docs/dicespell_game_flow_token_anchor_packet_kr.md`
- `docs/dicespell_game_flow_token_delivery_workboard_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`

하지만 실제 제작 직전에는 여전히 아래가 다시 섞이기 쉽다.

1. 프로그래머는 `hook 이름은 알겠는데 live markup/CSS에서 무엇이 아직 안 잠겼지?`를 다시 코드와 packet 사이에서 조합해야 한다.
2. UI 오너는 `first-glance hierarchy`와 `surface별 density pass/fail`이 문서엔 있는데, 실제 closing 전에 **무엇이 아직 빈칸인지**를 한 표로 보기 어렵다.
3. 아트 오너는 starter asset 범위를 받았어도, **이번 턴에 진짜 필요한 것이 token sanity인지 큰 자산인지**를 다시 추론하기 쉽다.
4. QA/PM은 source-of-truth map 덕분에 `어디서 읽지`는 정리됐지만, **실제로 아직 안 끝난 것**이 `DOM hook`, `density`, `archive evidence` 중 어디인지 한 화면에 보이지 않으면 다시 큰 green 문장으로 흐른다.

즉 지금 필요한 것은 새 설계가 아니라, **남은 구현 갭의 위치를 surface별 / 오너별 / 증거별로 보이는 ledger**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 결론`, `2. 구현 갭 한눈표`, `3. surface별 code gap` | `token anchor packet`, `delivery workboard`, `source-of-truth map` | 지금 남은 일은 새 token naming이 아니라 live function/markup gap closure임을 확인한다 |
| UI | `2. 구현 갭 한눈표`, `4. surface별 UI gap`, `6. 리스크 & 후속과제` | `closing packet`, `prototype board` | summary/reference가 아니라 density/closing gap만 닫으면 된다는 결론을 얻는다 |
| 아트 | `2. 구현 갭 한눈표`, `5. art starter gap` | `token anchor packet`, `closing packet`, `evidence manifest` | 이번 턴의 아트 일은 대형 신작이 아니라 starter token sanity임을 확인한다 |
| QA | `2. 구현 갭 한눈표`, `6. 리스크 & 후속과제`, `7. 즉시 다음 액션` | `closing packet`, `evidence manifest`, `source-of-truth map` | pass/fail은 source packet 유무가 아니라 hook/density/archive evidence 유무로 나뉜다는 결론을 얻는다 |
| PM/기획 | `1. first screen 결론`, `2. 구현 갭 한눈표`, `6. 리스크 & 후속과제` | `delivery workboard`, `closing packet`, `source-of-truth map` | 다음 refinement를 큰 UI polish나 새 화면 제안으로 넓히면 안 된다는 결론을 얻는다 |

---

## 1. first screen 결론

### 왜 이 문서를 열어야 하나
- **프로그래머**: `어떤 문서를 읽지?` 다음 질문인 `좋아, 그래서 지금 코드에서 무엇이 아직 비어 있지?`를 확인하려고 연다.
- **UI/아트**: 이번 턴이 `새로운 미감 확장`이 아니라 `surface 차이를 닫는 closing turn`인지 확인하려고 연다.
- **QA/PM**: `문서가 충분함`과 `토큰 refinement가 실제로 닫힘`을 다른 문장으로 기록하려고 연다.

### 가장 짧은 답
- Run Table 토큰 축은 **문서 blocker가 거의 없다.**
- 남은 것은 `renderRouteBoard()` / `renderBattle()` / `renderEnding()`와 대응 CSS, 1440p review, starter asset sanity, stage archive evidence를 실제로 닫는 일이다.
- 따라서 다음 refinement의 기본 질문은 `무슨 packet을 더 쓰지?`가 아니라 **`route node / stakes plaque / oath banner 중 무엇이 아직 live evidence로 안 닫혔지?`**다.

### 한 줄 판정 규칙
- 문서가 이미 있는 문제는 blocker가 아니다.
- markup/CSS/hierarchy/archive evidence가 아직 없는 문제만 blocker다.
- `atlas`, `battle`, `oath`는 각각 다른 갭이다.

---

## 2. 구현 갭 한눈표

| surface | 현재 baseline | 이미 문서로 닫힌 것 | 아직 남은 실제 갭 | 누구 손에서 닫히나 | 닫힘 증거 |
| --- | --- | --- | --- | --- | --- |
| `Run Atlas` / route node | `renderRouteBoard()`와 `run-route-node` class 조합은 있으나 state가 live class에 기대고 있음 | surface handoff, token anchor, delivery workboard, closing packet, evidence manifest, source-of-truth map | `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold` 기준 hook lock + next threshold first-glance 확인 + route starter sanity + atlas stage archive | 프로그래머 + UI + 아트 + QA/PM | DOM hook 목록, 1440p atlas capture, starter asset note, `atlas-route-node` archive |
| `Battle Table` / stakes plaque | `battle-stakes-grid`와 3카드는 있으나 slot contract와 density evidence가 아직 느슨함 | surface handoff, token anchor, delivery workboard, closing packet, evidence manifest, source-of-truth map | `data-run-surface="battle-table"`, `data-stakes-slot` 기준 slot lock + action panel 비침범 hierarchy + plaque starter sanity + battle stage archive | 프로그래머 + UI + 아트 + QA/PM | DOM hook 목록, 1440p battle capture, starter asset note, `battle-stakes-plaque` archive |
| `Overrun Oath` / oath banner | `ending-banner`와 2컬럼은 있으나 `banner/result/carry` 역할이 owner evidence까지 안 잠김 | surface handoff, token anchor, delivery workboard, closing packet, evidence manifest, source-of-truth map | `data-run-surface="overrun-oath"`, `data-oath-role`, `data-overrun-cta` hook lock + banner-first hierarchy + starter banner/seal sanity + oath stage archive | 프로그래머 + UI + 아트 + QA/PM | DOM hook 목록, 1440p oath capture, starter asset note, `oath-banner` archive |

### 한 줄 해석
- `현재 방향이 없다`가 아니라 **`역할 이름은 있는데 실제 evidence가 아직 비어 있다`**가 정확한 상태다.
- 세 surface를 한 번에 닫으려는 순간 다시 generic polish가 된다.

---

## 3. Surface별 code gap

## 3줄 요약
- 프로그래머 갭은 새 render family를 발명하는 일이 아니라, 현재 live render가 이미 가진 역할을 **hook 이름과 review 가능한 state 이름**으로 고정하는 일이다.
- 가장 큰 위험은 class/style 조합을 곧 state contract처럼 여기고 archive-ready evidence를 미루는 것이다.
- 이번 턴의 구현 언어는 `대형 리팩터링`이 아니라 `surface root`, `state/slot/role hook`, `drift 없는 helper vocabulary`다.

### 한 줄 목표
`live class 조합`을 `surface 목적과 같은 이름을 쓰는 markup contract`로 바꾼다.

### 3-1. `Run Atlas` / route node code gap

| 항목 | 현재 baseline | target 계약 | 실제 남은 갭 |
| --- | --- | --- | --- |
| surface root | route board root는 있으나 surface 식별이 generic | `data-run-surface="atlas"` | atlas를 다른 sidebar family와 분리하는 root hook 추가 |
| state name | `current / past / shop / boss`가 class/branch에 흩어짐 | `data-route-node-state` | QA와 UI가 같은 state 단어를 markup에서 직접 읽게 만들기 |
| threshold | next hint가 panel text에 기대는 비중이 큼 | `data-route-threshold` | route node와 right panel이 같은 threshold vocabulary를 쓰게 맞추기 |
| helper language | route board와 next summary가 다른 표현으로 drift할 여지 | 같은 helper 결과에서 파생 | `shop/boss/none` threshold naming 일관화 |

### 3-2. `Battle Table` / stakes plaque code gap

| 항목 | 현재 baseline | target 계약 | 실제 남은 갭 |
| --- | --- | --- | --- |
| surface root | battle screen root는 있으나 token surface와 직접 연결 안 됨 | `data-run-surface="battle-table"` | stakes review를 battle-table surface 기준으로 분리 |
| slot identity | 3카드는 있으나 `.current` 중심 읽힘이 강함 | `data-stakes-slot="current|next|grimoire"` | card 순서가 아닌 slot 역할을 markup 수준에서 고정 |
| hint sync | route hint / next summary / grimoire axis가 drift 가능 | slot language와 동일 어휘 | `current > next > grimoire`를 같은 payload/wording으로 정렬 |
| review anchor | DOM evidence 없이 캡처 감상으로만 끝날 위험 | root + slot hook 목록 | battle density review를 hook 기반 closing으로 연결 |

### 3-3. `Overrun Oath` / oath banner code gap

| 항목 | 현재 baseline | target 계약 | 실제 남은 갭 |
| --- | --- | --- | --- |
| surface root | ending root는 있으나 overrun oath family로 직접 이름 붙지 않음 | `data-run-surface="overrun-oath"` | reward/shop/ending generic family drift 방지 |
| role split | banner / result / carry가 시각적으로만 느슨하게 암시됨 | `data-oath-role="banner|result|carry"` | banner-first hierarchy를 markup evidence로 고정 |
| CTA context | continue CTA가 장면 역할과 별도 contract 없음 | `data-overrun-cta` | CTA drift와 carry language drift를 같은 anchor로 확인 |
| boundary | side column이 reward/shop side card family로 역류할 여지 | oath role contract | ending 결과 shell과 carry-forward shell의 역할 분리 유지 |

### 프로그래머에게 넘길 것
- 이번 턴은 `새 토큰 설계`가 아니라 `지금 있는 surface 목적을 hook vocabulary로 잠그는 턴`이다.
- helper를 새 object tree로 크게 갈아엎기보다, current baseline을 같은 단어로 다시 읽히게 만드는 쪽이 우선이다.
- stage archive evidence 없이 `거의 끝`으로 기록하지 않는다.

---

## 4. Surface별 UI gap

## 3줄 요약
- UI 갭은 무드보드 부족이 아니라 `first-glance hierarchy`가 아직 closing evidence까지 닫히지 않았다는 점이다.
- atlas/battle/oath는 각각 `지도 핀`, `판 압력 plaque`, `결과/carry banner`로 읽혀야 하며, 이 차이가 줄어들수록 Run Table은 다시 generic card refresh처럼 보인다.
- 이번 턴 UI의 일은 새 레이아웃 발명보다 **무엇이 먼저 보여야 하는가를 짧게 닫는 것**이다.

### 한 줄 목표
세 surface가 서로 다른 첫 시선을 유지한다.

| surface | 먼저 보여야 하는 것 | 흔한 실패 | 아직 남은 UI 갭 |
| --- | --- | --- | --- |
| Atlas | current node와 next threshold | preview/log가 route board보다 먼저 튐 | route board가 `보드의 핀`처럼 읽히는지 1440p review evidence 필요 |
| Battle | current stakes plaque | stakes가 subtitle처럼 약하거나 action panel을 침범 | `current > next > grimoire` 위계를 capture + 짧은 review note로 고정할 필요 |
| Oath | main banner | side card가 메인처럼 강해져 reward/shop 연장처럼 보임 | banner/result/carry 2~3층 읽힘을 capture와 review note로 닫을 필요 |

### UI 오너에게 넘길 것
- prototype board는 여전히 reference다. closing 기준은 `first-glance hierarchy`와 archive evidence다.
- review 문장은 길게 쓰기보다 `유지 / 약함 / 과함` 수준으로 짧게 남기는 편이 drift를 줄인다.
- 세 surface를 같은 border/glow family로 맞추는 순간 token 목적이 사라진다.

---

## 5. Art starter gap

## 3줄 요약
- 아트 갭은 새 대형 비주얼이 없어서가 아니라, **starter asset이 실제로 어디까지면 충분한지**가 closing evidence와 함께 잠기지 않았다는 점이다.
- 이번 턴 기본값은 `frame / badge / seal / accent` 중심의 작은 starter sanity다.
- 큰 배경이나 전체 shell 재설계는 이 단계의 blocker가 아니다.

### 한 줄 목표
starter asset을 `surface 차이를 오래 버티게 만드는 최소 패키지`로만 닫는다.

| surface | 이미 정해진 starter 범위 | 아직 남은 실제 갭 | 이번 턴에 필요 없는 것 |
| --- | --- | --- | --- |
| Atlas | `Route Node Starter` | current/past/shop/boss 차이를 evidence에 남길 asset sanity note | 지도 배경 전체 리뉴얼 |
| Battle | `Stakes Plaque Starter` | current/next/grimoire 3프레임이 plaque로 읽히는지 짧은 sanity note | 전투 전체 HUD 리페인트 |
| Oath | `Oath Banner Starter` | main banner + seal pill + side accent가 reward/shop과 섞이지 않는지 sanity note | ending 배경 일러스트 확장 |

### 아트 오너에게 넘길 것
- 이번 턴에 필요한 것은 `더 멋진 그림`보다 `surface family가 다시 섞이지 않게 만드는 starter 차이`다.
- archive에 남길 것은 최종 key art가 아니라 starter asset list와 sanity note다.
- Oath는 특히 reward/shop family 역류를 막는 쪽이 우선이다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| 문서가 충분해져 `Run Table token 축은 거의 끝났다`는 착시가 생기는 위험 | 구현자가 hook/density/archive evidence를 생략한 채 generic polish로 넘어갈 수 있음 | 이번 ledger로 `문서 완료`와 `실구현 완료`를 분리하고 surface별 남은 gap을 다시 표로 고정 | 다음 token refinement 착수 직전 |
| atlas/battle/oath를 한 번에 닫으려다 surface별 gap이 다시 큰 UI pass로 합쳐지는 위험 | route/stakes/oath가 generic card family로 다시 평준화될 수 있음 | surface별 code/UI/art/evidence gap을 분리해 각 stage archive와 대응시키는 방식으로 정리 | implementation 범위 선언 직전 |
| source-of-truth map 덕분에 `어디서 읽지`는 해결됐지만 `무엇이 아직 비었지`가 tracker/run log에서 다시 흐려지는 위험 | QA/PM이 큰 green 문장을 남기며 half-closure를 완료처럼 기록할 수 있음 | gap 언어를 `DOM hook / 1440p capture / starter sanity / archive evidence` 4축으로 재정리 | tracker/run log 기록 직전 |
| starter asset이 작은 범위임에도 UI/아트가 큰 자산 발주나 배경 논의로 확장하는 위험 | bounded refinement가 다시 backlog 확장으로 돌아감 | art gap을 starter sanity note 중심으로만 정의하고 큰 자산은 의도적으로 이번 범위 밖으로 남김 | art review 직전 |

---

## 7. 즉시 다음 액션

1. 다음 refinement는 먼저 `token source-of-truth map -> 이 gap ledger -> token delivery workboard` 순서로 읽고, **무엇이 이미 정해졌는지보다 무엇이 아직 비어 있는지**부터 확인한다.
2. 프로그래머는 atlas/battle/oath 중 한 surface씩 `root hook -> state/slot/role hook -> helper vocabulary` 순서로 범위를 선언한다.
3. UI는 같은 surface에 대해 1440p capture 1장과 짧은 hierarchy note 1개만 남긴다.
4. 아트는 대응 starter asset sanity note만 닫고 큰 자산 논의는 열지 않는다.
5. QA/PM은 `token refinement 진행` 대신 `어느 surface gap이 닫혔는가`를 stage 이름과 evidence 묶음으로 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_delivery_workboard_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_anchor_packet_kr.md`
- `docs/prototypes/dicespell_game_flow_redesign_board.html`

---

## 한 장 결론
지금 Run Table 축의 남은 공백은 `무슨 토큰을 더 설계하지?`가 아니다. 이제 남은 공백은 **`좋아, 문서와 이름은 충분한데 route node / stakes plaque / oath banner 중 무엇이 아직 실제 markup, 1440p review, starter sanity, archive evidence에서 안 닫혔지?`**다. 이 문서는 그 마지막 구현 공백을 surface별 갭 언어로 다시 보이게 만들어, 다음 refinement가 다시 generic UI polish나 큰 green 문장으로 흐르지 않게 하는 bounded gap ledger다.
