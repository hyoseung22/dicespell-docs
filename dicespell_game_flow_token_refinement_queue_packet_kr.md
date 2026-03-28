# DiceSpell `Run Table` Token 리파인먼트 큐 패킷

## 3줄 요약
- 이 문서는 `token archive bootstrap`, `token delivery workboard`, `token closing`, `token evidence manifest`가 이미 충분한 상태에서도 남아 있던 마지막 운영 공백인 **`atlas / battle / oath 중 어느 stage archive부터 실제로 열고, bootstrap-ready와 archive-ready를 누가 어떤 문장으로 구분할 것인가`**를 고정하는 source-of-truth다.
- 핵심은 새 토큰을 더 설계하는 것이 아니라, `atlas-route-node → battle-stakes-plaque → oath-banner` 순서를 **stage queue + signoff ownership + next unlock rule**로 잠가 Run Table refinement가 다시 큰 UI polish나 감상형 closing으로 흐르지 않게 만드는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `지금 열 수 있는 stage`, `아직 대기해야 하는 stage`, `bootstrap-ready를 누가 기록하는지`, `archive-ready를 누가 next step으로 넘기는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
Run Table token refinement를 **`stage 1개 = archive 1개 = closing 문장 1개`** 원칙으로 다시 좁혀, atlas/battle/oath 3면이 같은 속도로 뭉개지지 않게 만든다.

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

즉 `무엇을 잠가야 하는가`, `무슨 evidence가 필요한가`, `어떤 archive를 먼저 만들어야 하는가`는 이미 정리돼 있다.

하지만 실제 refinement 직전 마지막 작은 공백은 여전히 남아 있다.

1. `좋아, 그런데 atlas / battle / oath 중 어느 stage archive부터 실제로 열지?`를 다시 사람이 감으로 정하면, 가장 쉬워 보이는 surface만 골라 닫거나 세 surface를 조금씩 같이 건드리기 쉽다.
2. `bootstrap-ready`와 `archive-ready`가 archive bootstrap packet에 정의돼 있어도, 실제로 **누가 그 상태 문장을 쓸 수 있는지**가 없으면 PM/QA가 너무 이르게 큰 green 문장을 남길 수 있다.
3. `delivery workboard`는 owner 순서를 말하지만, 실제 stage queue가 없으면 implementer는 `battle도 같이`, `oath도 같이`처럼 surface 범위를 다시 키우기 쉽다.
4. oath는 ending/reward/carry 경계가 가장 민감한데, atlas/battle보다 먼저 열리면 Run Table refinement가 다시 `경계 재설계`처럼 커질 위험이 있다.

즉 지금 필요한 것은 새 디자인안이 아니라, **어떤 stage부터 열고, 누가 어떤 상태 문장으로 다음 stage를 열어 줄지**를 한 장으로 고정하는 queue packet이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `1. canonical queue`, `3. stage별 start/stop rule` | `token source-of-truth map`, `token archive bootstrap`, 해당 stage anchor/closing packet | 이번 턴에 stage 1개만 열고 다른 surface는 일부러 대기한다 |
| UI | `1. canonical queue`, `4. role-specific signoff`, `5. stage별 handoff point` | `token closing packet`, `prototype board` | atlas/battle/oath를 한 번에 density pass로 묶지 않는다 |
| 아트 | `1. canonical queue`, `5. stage별 handoff point`, `6. 리스크` | `token anchor packet`, `token evidence manifest` | starter asset sanity는 stage가 열릴 때만 붙인다 |
| QA | `2. status ownership`, `3. stage별 start/stop rule`, `6. 리스크` | `token closing packet`, `token evidence manifest`, `archive bootstrap packet` | `bootstrap-ready`와 `archive-ready`를 다른 사람/다른 문장으로 기록한다 |
| PM/기획 | `0. first screen 결론`, `2. status ownership`, `7. immediate next actions` | `token gap ledger`, `token delivery workboard` | green 문장은 stage archive 1개가 archive-ready일 때만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `atlas / battle / oath 중 이번 턴에 실제로 어떤 stage archive부터 열지?`
- `bootstrap-ready는 누가 쓰고 archive-ready는 누가 승인하지?`
- `다음 stage는 무엇이 완료돼야만 열 수 있지?`

### 가장 짧은 답
- 첫 stage는 `atlas-route-node`다.
- 둘째 stage는 `battle-stakes-plaque`다.
- 마지막 stage는 `oath-banner`다.
- `bootstrap-ready`는 **stage driver가 starter bundle을 만든 뒤** 쓸 수 있다.
- `archive-ready`는 **QA 확인 + PM 기록** 전에는 누구도 closing 문장처럼 쓰면 안 된다.

### 한 줄 판정 규칙
Run Table token refinement는 `stage 하나를 archive-ready로 만들기 전까지 다음 stage를 열지 않는 큐`로 본다.

---

## 1. canonical queue

## 3줄 요약
- stage 순서는 난이도나 취향이 아니라 **의존도와 경계 민감도** 기준으로 고정한다.
- atlas는 archive discipline과 route vocabulary를 먼저 잠그는 가장 안전한 첫 stage다.
- battle은 atlas 다음으로 slot/hierarchy evidence를 닫기 좋고, oath는 ending/carry boundary가 가장 민감하므로 마지막에 여는 것이 맞다.

### 한 줄 목표
가장 안전한 stage부터 닫아 evidence discipline을 굳힌 뒤, 경계 민감도가 높은 stage를 마지막에 연다.

| 순서 | stage | 지금 먼저 여는 이유 | 아직 열면 안 되는 것 |
| --- | --- | --- | --- |
| 1 | `atlas-route-node` | route state / threshold / board first-glance가 가장 독립적이고, stage archive naming discipline을 가장 작게 검증할 수 있다 | battle stakes density, oath carry/boundary 동시 착수 |
| 2 | `battle-stakes-plaque` | atlas에서 archive-ready 문장과 capture naming을 먼저 굳힌 뒤, `current / next / grimoire` slot hierarchy를 같은 discipline으로 닫기 좋다 | oath banner-first closing, reward/shop/ending boundary 논의 |
| 3 | `oath-banner` | banner/result/carry boundary는 가장 예민하고 `ending`/`reward` family와 가장 쉽게 섞이므로 마지막에 여는 것이 drift를 줄인다 | oath와 overrun/onboarding boundary 재설계, ending helper 대확장 |

### 왜 atlas가 첫 stage인가
1. `renderRouteBoard()`는 oath보다 경계 민감도가 낮고 battle보다 UI hierarchy가 더 단순하다.
2. `data-route-node-state`, `data-route-threshold`, board first-glance, threshold focus capture만으로도 `archive-ready` discipline을 검증할 수 있다.
3. atlas가 먼저 닫히면 이후 battle/oath는 `같은 archive naming`, `같은 capture naming`, `같은 signoff 문장`을 재사용할 수 있다.

### 왜 oath가 마지막 stage인가
1. `banner / result / carry`는 reward/shop/ending family와 가장 쉽게 섞인다.
2. oath를 먼저 열면 Run Table token refinement가 다시 overrun/ending boundary 재설계처럼 커질 위험이 있다.
3. battle까지 archive-ready 문장을 먼저 고정한 뒤에 oath를 열어야 PM/QA가 `banner-first closing`을 큰 ending green 문장과 섞지 않는다.

---

## 2. status ownership

## 3줄 요약
- 같은 stage라도 `bootstrap-ready`, `archive-in-progress`, `archive-ready`, `green 기록`은 같은 사람이 쓰면 안 된다.
- 그래야 starter bundle 준비, evidence 축적, review 통과, 최종 기록이 서로 다른 층위로 남는다.
- 특히 PM이 `bootstrap-ready`를 closing처럼 기록하거나, 구현자가 `archive-ready`를 자기 판단으로 선언하면 다시 큰 초록불이 된다.

### 한 줄 목표
stage 상태 문장을 `준비`, `진행`, `검수`, `기록` 네 단계로 분리한다.

| 상태 | 누가 먼저 적을 수 있는가 | 무엇이 있어야 하는가 | 아직 금지 |
| --- | --- | --- | --- |
| `bootstrap-ready` | stage driver(기본은 프로그래머) | stage 폴더 + `manifest.md` + starter file set + owner drop 1회 | closing 문장, archive-ready 주장 |
| `archive-in-progress` | stage driver + UI/아트/QA note | overview/focus capture 또는 예약명, hook/check note, starter asset note가 stage archive 안에 쌓이기 시작함 | PM green, 다음 stage unlock |
| `archive-ready (candidate)` | QA | closing packet과 evidence manifest 기준 required artifacts가 채워짐 | PM green 없이 다음 stage 열기 |
| `stage green` | PM/기획 | QA가 `archive-ready`를 확인했고 handoff line이 manifest와 일치함 | 두 stage를 한 줄로 함께 기록하기 |

### 기본 ownership 규칙
- **프로그래머**: stage 생성과 `bootstrap-ready`까지 책임진다.
- **UI/아트**: density / starter asset sanity를 stage archive에 채운다.
- **QA**: `archive-ready candidate`를 확인한다.
- **PM/기획**: stage 이름으로만 green을 기록하고 다음 stage를 연다.

### 금지 문장
- `Run Table 거의 완료`
- `토큰 정리 완료`
- `atlas/battle/oath 진행중`
- `closing-ready 같음`

허용 문장 예시는 아래 stage별 handoff point에서만 쓴다.

---

## 3. stage별 start / stop rule

## 3줄 요약
- 각 stage는 `열 수 있는 조건`, `이번 턴 산출물`, `아직 금지`, `다음 unlock 조건`이 따로 있다.
- 이 표는 queue를 실제 실행 언어로 내린 것이다.
- `이번 턴에 무엇을 안 하는가`가 명확해야 stage가 커지지 않는다.

### 한 줄 목표
stage별로 `지금 열 수 있는 이유`와 `아직 열면 안 되는 이유`를 같은 표로 본다.

| stage | 열 수 있는 조건 | 이번 턴 최소 산출물 | 아직 금지 | 다음 stage unlock 조건 |
| --- | --- | --- | --- | --- |
| `atlas-route-node` | 없음. 첫 stage이므로 바로 열 수 있다 | atlas stage archive, route hook note, 1440 overview/focus capture, starter asset sanity, handoff line 초안 | battle stakes slot edit, oath banner tone pass | QA가 atlas `archive-ready` 확인 + PM이 atlas green 기록 |
| `battle-stakes-plaque` | atlas green 완료 | battle stage archive, slot hook note, 1440 overview/focus capture, starter asset sanity, handoff line 초안 | oath banner/carry boundary edit, battle action panel 대개편 | QA가 battle `archive-ready` 확인 + PM이 battle green 기록 |
| `oath-banner` | atlas + battle green 완료 | oath stage archive, role hook note, 1440 overview/focus capture, starter asset sanity, handoff line 초안 | overrun/ending boundary 확장, reward/shop family 재작업 | QA가 oath `archive-ready` 확인 + PM이 oath green 기록 |

### stop rule
아래 중 하나라도 보이면 즉시 멈추고 같은 stage archive 안에 note로 남긴다.
1. 한 stage를 닫는 중에 다른 stage root/class/hook를 같이 건드리기 시작함.
2. starter asset sanity가 전체 shell 리뉴얼 논의로 커짐.
3. overview/focus capture가 stage 이름 없이 generic 파일명으로 저장됨.
4. PM/QA가 `archive-ready` 전 큰 green 문장을 쓰려 함.

---

## 4. role-specific signoff

## 3줄 요약
- signoff는 문서 읽기와 다르다. 실제로는 누가 `열기`, `확인`, `기록`을 맡는지 고정돼야 한다.
- 같은 사람이 `bootstrap-ready`와 `green`을 다 쓰면 stage queue는 바로 흐려진다.
- 아래 표는 stage 공통 signoff ladder다.

### 한 줄 목표
Run Table token stage도 `제출자`, `확인자`, `기록자`를 분리한다.

| 단계 | 제출자 | 필수 확인자 | 마지막 기록자 | 기록 문장 예시 |
| --- | --- | --- | --- | --- |
| bootstrap-ready | 프로그래머 | UI 또는 QA가 starter bundle 존재만 확인 | 프로그래머(archive 내부만) | `atlas-route-node bootstrap-ready` |
| archive-in-progress | 프로그래머 + UI/아트 | QA | 없음(진행 note만) | `battle-stakes-plaque archive-in-progress` |
| archive-ready candidate | QA | UI/아트 또는 프로그래머가 누락 없음 재확인 | QA(archive 내부 + review note) | `oath-banner archive-ready candidate` |
| stage green | PM/기획 | QA unlock 필수 | PM/기획 | `atlas-route-node green, battle-stakes-plaque unlock` |

### 중요한 원칙
- QA는 `candidate`까지만 올린다.
- PM은 QA unlock 없이는 green을 쓰지 않는다.
- 프로그래머는 archive를 만들 수 있지만 final green을 쓰지 않는다.

---

## 5. stage별 handoff point

## 5-1. `atlas-route-node`

### 프로그래머에게 넘길 것
- 첫 stage는 atlas다.
- `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold`를 같은 archive vocabulary로 잠근다.
- route board 밖 preview/log까지 같이 다루지 않는다.

### UI에게 넘길 것
- `atlas-1440-overview.png`, `atlas-1440-threshold-focus.png` 두 장만으로 first-glance를 닫는다.
- board가 `핀 보드`처럼 읽히는지 짧은 density note로 남긴다.

### 아트에게 넘길 것
- `Route Node Starter` sanity만 본다.
- 지도 배경 전체 리뉴얼은 금지다.

### QA/PM에게 넘길 것
- `atlas-route-node bootstrap-ready`와 `atlas-route-node green`은 다른 문장이다.
- atlas green 전에는 battle을 열지 않는다.

## 5-2. `battle-stakes-plaque`

### 프로그래머에게 넘길 것
- battle은 atlas green 뒤에만 연다.
- `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"`를 같은 slot language로 잠근다.
- action panel 전체 개편은 금지다.

### UI에게 넘길 것
- `battle-1440-overview.png`, `battle-1440-stakes-focus.png` 두 장만 남긴다.
- `current > next > grimoire`가 바로 읽히는지만 본다.

### 아트에게 넘길 것
- `Stakes Plaque Starter` sanity만 본다.
- battle HUD 전체 tone pass는 금지다.

### QA/PM에게 넘길 것
- battle green 문장에는 atlas를 다시 같이 쓰지 않는다.
- oath unlock은 battle green 뒤에만 열린다.

## 5-3. `oath-banner`

### 프로그래머에게 넘길 것
- oath는 가장 마지막 stage다.
- `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta`만 잠근다.
- overrun/ending boundary 재설계는 금지다.

### UI에게 넘길 것
- `oath-1440-overview.png`, `oath-1440-banner-focus.png` 두 장으로 banner-first hierarchy를 닫는다.
- side column을 reward/shop side card처럼 키우지 않는다.

### 아트에게 넘길 것
- `Oath Banner Starter` sanity만 본다.
- ending background나 carry shell 리뉴얼은 금지다.

### QA/PM에게 넘길 것
- oath green은 `Run Table token complete`가 아니다.
- 필요하면 그다음에야 새 stage 묶음이나 follow-up packet을 논의한다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| atlas/battle/oath를 동시에 조금씩 열어 evidence discipline이 다시 무너지는 위험 | stage archive는 3개인데 기록은 큰 `Run Table 진행` 문장으로 남아 surface closing이 흐려진다 | canonical queue를 `atlas -> battle -> oath`로 고정하고 `stage 1개 = archive 1개 = green 문장 1개` 원칙을 명시 | 다음 Run Table 실제 refinement 착수 직전 |
| `bootstrap-ready`와 `archive-ready`가 같은 사람/같은 문장으로 기록되는 위험 | starter bundle 준비와 closing pass가 같은 초록불처럼 보여 half-closure가 다시 생긴다 | status ownership 표와 signoff ladder를 별도로 고정 | 첫 atlas starter bundle 생성 직전 |
| oath를 너무 일찍 열어 reward/ending/carry 경계가 다시 큰 설계 논의로 커지는 위험 | Run Table token refinement가 overrun/ending boundary 재설계로 팽창한다 | oath는 battle green 뒤 마지막 stage로만 연다고 못 박음 | battle green 직후 |
| PM/QA가 stage green과 다음 stage unlock을 한 줄로 크게 줄여 적는 위험 | tracker/run log/public portal에 stage 경계가 다시 사라진다 | green 문장 예시를 stage 이름 기준으로 고정하고 큰 총괄 문장 금지 | 다음 stage green 기록 직전 |

---

## 7. immediate next actions

1. 다음 Run Table refinement 착수 전 `token source-of-truth map -> token gap ledger -> token archive bootstrap -> 이 queue packet` 순서로 읽는다.
2. 첫 stage는 반드시 `atlas-route-node`로만 연다.
3. atlas stage archive 안에서 `bootstrap-ready`를 만든 뒤에만 live markup/CSS edit에 들어간다.
4. QA가 atlas `archive-ready candidate`를 적고 PM이 atlas green을 남기기 전에는 `battle-stakes-plaque`를 열지 않는다.
5. oath는 `battle-stakes-plaque` green 뒤에만 연다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_delivery_workboard_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 남은 마지막 운영 공백은 `무슨 문서를 더 써야 하지?`가 아니라 **`atlas / battle / oath 중 무엇을 먼저 열고, bootstrap-ready와 archive-ready를 누가 어떤 문장으로 구분하며, stage 1개가 green이 되기 전까지 무엇을 아직 열지 말아야 하지?`**다. 이 문서는 그 공백을 `canonical queue + status ownership + stage별 start/stop rule + signoff ladder`로 다시 압축해, 다음 refinement가 다시 큰 UI polish나 감상형 closing으로 흐르지 않게 만드는 bounded queue packet이다.
