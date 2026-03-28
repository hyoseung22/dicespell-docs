# DiceSpell `Run Table` Atlas Route Node semantic anchor packet

## 3줄 요약
- 이 문서는 `atlas live kickoff`, `atlas archive file map`, `required artifacts`, `filled archive examples`, `candidate review`까지로도 남아 있던 마지막 locator 공백인 **`좋아, 그럼 실제 atlas live edit 직전에 정확히 어느 함수 / DOM / capture 질문을 같은 책임으로 다시 찾아야 하지?`**를 닫는 source-of-truth다.
- 핵심은 새 route node 규칙을 더 설계하는 것이 아니라, `renderRouteBoard()` / `run-side-panel` / `run-route-node` / `forecast-item` / `upperSection` / preview·log zone을 **atlas semantic zone 기준 계약**으로 다시 묶어 line drift가 있어도 stage drift가 생기지 않게 만드는 것이다.
- 목표는 다음 implementer / UI / QA / PM이 atlas 첫 live edit 전 `semantic anchor 재확인`을 더 이상 감으로 하지 않고, **같은 locator 이름 · 같은 non-change line · 같은 evidence 질문**으로 시작하게 만드는 것이다.

**한 줄 목표:** `atlas-route-node` 첫 live turn의 semantic locator를 `surface root / route node state / threshold pressure / forecast boundary / capture proof` 5축으로 고정해, archive/stub가 준비된 뒤 실제 edit도 끝까지 `atlas-only` stage 언어로 좁게 유지되게 만든다.

---

## 왜 이 문서가 필요한가
지금 atlas first-stage 축은 거의 충분하다.

이미 있는 것:
- stage 범위: `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- 첫 10분 순서: `docs/dicespell_game_flow_token_atlas_live_kickoff_packet_kr.md`
- exact 제출물: `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- starter sentence: `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`
- field contract: `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- file routing: `docs/dicespell_game_flow_token_atlas_archive_file_map_packet_kr.md`
- filled wording exemplar: `docs/dicespell_game_flow_token_atlas_filled_archive_examples_kr.md`
- green/signoff: `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`

그런데 실제 atlas 첫 live turn 직전에는 아직 아래가 남아 있었다.

1. live kickoff packet은 `semantic anchor 재확인`을 요구하지만, **무엇을 atlas semantic anchor로 볼지**는 atlas stage 언어로 한 장에 다시 압축돼 있지 않았다.
2. file map packet은 `어느 경로에 쓸지`를 잠갔지만, implementer / UI / QA가 실제 첫 10분 안에 같은 이름으로 다시 찾을 **function / DOM / evidence question 세트**는 따로 분리돼 있지 않았다.
3. `renderRouteBoard()`와 `run-side-panel`이 실제로는 `route node`, `forecast`, `run pressure`, `preview`, `log`를 함께 품고 있어서 locator 이름이 약하면 `threshold를 조금 보다가 preview/log도 같이`, `forecast도 손대다 battle hint도 같이` 같은 drift가 다시 생기기 쉽다.
4. 이 공백이 남아 있으면 archive / stub / file routing은 stage-specific인데 실제 code review와 UI sanity만 다시 generic grep 메모로 돌아가 atlas의 `route node hook + threshold first-glance` discipline이 약해진다.

즉 이 문서는 새 방향 문서가 아니라, **atlas kickoff 뒤 첫 live edit 전 semantic locator를 같은 문장으로 재확인하게 만드는 마지막 좁은 handoff packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. semantic anchor map`, `3. code locator rule` | `atlas route node stage packet`, `atlas live kickoff`, `atlas archive file map` | `renderRouteBoard()` / `run-side-panel` / `upperSection` 주변 atlas zone까지만 다시 찾고 battle/oath surface나 preview/log 대확장은 같이 열지 않는다 |
| UI | `2. semantic anchor map`, `4. DOM/hierarchy anchor`, `5. capture anchor` | `atlas required artifacts`, `atlas filled archive examples` | route node first-glance와 threshold focus만 판정하며 preview/log 전체 리디자인이나 battle/oath density는 이번 stage 바깥이다 |
| 아트 | `4. DOM/hierarchy anchor`, `6. token sanity anchor` | `token anchor packet`, `atlas required artifacts` | Route Node Starter sanity는 atlas shell 안에서만 확인하며 battle/oath family를 같이 묶지 않는다 |
| QA | `2. semantic anchor map`, `5. capture anchor`, `7. review-ready evidence rule` | `atlas handoff scorecard`, `atlas candidate review` | candidate-ready는 first-glance / threshold / boundary 질문이 같은 atlas-only 문장으로 묶였을 때만 성립한다 |
| PM/기획 | `0. first screen 결론`, `7. review-ready evidence rule`, `8. stop/go` | `queue readiness board`, `atlas candidate review` | semantic anchor 재확인은 kickoff 층위이고, green / battle unlock 문장은 여전히 evidence 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- atlas 첫 live edit 직전에 정확히 어떤 함수 / DOM / 캡처를 다시 찾아야 하지?
- line drift가 생겨도 `atlas-only` stage를 어떻게 유지하지?
- 무엇을 semantic anchor라고 부르고, 무엇은 아직 금지 zone이지?

### 가장 짧은 답
- atlas semantic anchor는 **surface root / route node state / threshold pressure / forecast boundary / first-glance·threshold-focus evidence** 다섯 묶음이다.
- 프로그래머는 `renderRouteBoard()`, `run-side-panel`, `run-route-node`, `renderRunPressureCards(summary)`, `renderUpperSectionPanel(summary)` 주변만 다시 찾는다.
- UI / QA는 `Run Atlas eyebrow -> 페이지 보드 제목 -> route board -> forecast 2칸 -> run pressure -> preview/log` 위계와 `thresholdLabel`, `pointsLeft`, `progressLabel` 문맥만 확인한다.
- `renderBattle()`, `renderEnding()`, `battle-stakes-grid`, `ending-banner`, 다른 surface capture는 atlas semantic zone 바깥이다.

### 한 줄 판정 규칙
atlas semantic anchor 재확인은 **locator를 다시 찾는 일**이지 **stage를 다시 설계하거나 surface 범위를 다시 넓히는 일**이 아니다.

---

## 1. core structure

## 3줄 요약
- atlas semantic anchor는 `렌더 루트`, `route node`, `threshold`, `경계`, `증거 질문` 5축으로 나뉜다.
- 각 축마다 `무엇을 찾는가`, `왜 찾는가`, `같이 열면 안 되는 주변 책임`, `어느 live note에 기록하는가`가 따로 있다.
- 이 문서의 역할은 implementer / UI / QA가 같은 locator 이름을 쓰게 만드는 것이지, 새로운 line contract를 만드는 것이 아니다.

### 한 줄 목표
semantic anchor를 `grep 결과`가 아니라 `atlas stage 경계를 다시 잠그는 짧은 계약`으로 만든다.

---

## 2. semantic anchor map

| anchor family | canonical locator | 왜 다시 찾는가 | 반드시 기록할 live file | 같이 열면 안 되는 것 |
| --- | --- | --- | --- | --- |
| Surface root anchor | `run-side-panel`, `Run Atlas`, `페이지 보드`, `renderRouteBoard(...)` 호출 zone | atlas stage를 side-panel 전체가 아니라 `route board first-glance` 기준으로 다시 잠그기 위해 | `notes/hook-manifest.md` | `renderBattle()`, `renderEnding()`, battle/oath root hook 선추가 |
| Route node anchor | `.run-route-board`, `.run-route-node`, `.run-route-step`, `pageTypeMeta(page.type)` | current / past / shop / boss route node 읽힘을 atlas-only vocabulary로 고정하기 위해 | `notes/hook-manifest.md`, `checks/dom-sanity.md` | preview list를 route node 대체 증거처럼 사용하는 것 |
| Threshold pressure anchor | `renderRunPressureCards(summary)`, `summary.upperSection?.thresholdLabel`, `renderUpperSectionPanel(summary)`, `pointsLeft`, `progressLabel` | threshold가 route node 옆에서 어떤 압력 문장으로 읽혀야 하는지 review 가능한 hook 언어로 잠그기 위해 | `notes/hook-manifest.md`, `notes/density-review.md` | upper bonus overlay 전체 확장, battle draft UI 손대기 |
| Forecast boundary anchor | `.run-side-summary`, `.forecast-item`, `nextShop`, `nextBoss`, `.run-peek-panel`, `.log-panel` | forecast는 atlas context 보조선이고 preview/log는 비범위라는 경계를 메모에 먼저 박기 위해 | `notes/hook-manifest.md`, `manifest.md` | preview/log 재배치, forecast를 battle hint처럼 확장 |
| Evidence anchor | `captures/atlas-1440-overview.png`, `captures/atlas-1440-threshold-focus.png`, `notes/density-review.md` | candidate-ready가 그냥 예쁜 캡처가 아니라 같은 atlas semantic 질문 두 개를 답하게 만들기 위해 | `captures/*`, `notes/density-review.md`, `notes/handoff-line.md` | battle/oath capture 혼입, generic `final.png` |

### 한 줄 결론
atlas anchor는 `함수 이름 몇 개`만이 아니라 **code + DOM + evidence question**이 함께 묶인 stage locator다.

---

## 3. code locator rule

## 3줄 요약
- 프로그래머는 line number가 아니라 `atlas root`, `route node`, `threshold`, `forecast boundary` 네 책임을 먼저 다시 찾는다.
- 이 단계의 진짜 목적은 `무엇을 바꾸나`보다 `무엇을 아직 안 바꾸나`를 live note에 먼저 적는 것이다.
- line drift가 생겨도 semantic family가 같으면 stage는 그대로고, 주변 panel / 다른 surface까지 넓어지면 stage drift다.

### one-line goal
`renderRouteBoard()` / `run-side-panel` / `renderRunPressureCards(summary)` / `.forecast-item`까지를 atlas semantic zone으로 다시 찾고, 그 밖의 preview/log 대확장과 battle/oath surface는 찾더라도 이번 turn에는 닫아 둔다.

### 찾은 뒤 note에 남길 최소 5줄
1. `root family:` atlas surface가 실제로 시작하는 render zone과 panel root
2. `route node family:` node state를 읽게 하는 class / label / title zone
3. `threshold family:` `thresholdLabel`, `pointsLeft`, `progressLabel`이 atlas first-glance와 연결되는 위치
4. `boundary family:` forecast는 보조선이고 preview/log는 이번 stage 비범위라는 선언
5. `non-change line:` battle/oath root, preview/log 대확장, upper bonus overlay, queue closing은 untouched

### 허용되는 기록 예시
> `renderRouteBoard()` semantic zone에서는 `.run-route-board`와 `.run-route-node`의 atlas state 읽힘만 다루고, battle/oath surface root와 preview/log 대확장은 이번 stage에서 열지 않는다.

> `renderRunPressureCards(summary)`와 `renderUpperSectionPanel(summary)` semantic zone에서는 `thresholdLabel`, `pointsLeft`, `progressLabel`이 atlas threshold focus에 어떻게 읽히는지만 확인하고, upper bonus overlay 선택 흐름 자체는 battle stage 바깥으로 남긴다.

### 금지되는 기록 예시
- `이 김에 page preview도 route atlas 쪽으로 다시 합치자`
- `battle stakes slot vocabulary도 같이 맞춰 두면 편함`
- `ending carry banner랑 같은 pressure tone으로 묶어 보자`
- `Run Table 전체 side panel을 공용 helper로 정리 예정`

---

## 4. DOM / hierarchy anchor

## 3줄 요약
- UI / 아트는 atlas에서 더 화려한 보드를 만드는 것이 아니라, atlas surface가 여전히 atlas로 읽히는지 본다.
- semantic anchor는 결국 DOM hierarchy와 density에서 확인돼야 한다.
- 이 단계에서 필요한 건 새 shell이 아니라 `어디까지가 route node first-glance이고 어디부터가 forecast / pressure / preview / log인가`의 경계 확인이다.

### one-line goal
`Run Atlas eyebrow -> 페이지 보드 제목 -> route board -> forecast 2칸 -> pressure cards -> preview/log` 위계를 유지한 채 route node와 threshold만 atlas stage 핵심 읽힘으로 잠근다.

### UI / 아트가 다시 확인할 DOM zone
| DOM zone | atlas에서 묻는 질문 | pass 기준 | fail 예시 |
| --- | --- | --- | --- |
| `.run-side-panel` 헤더 | atlas surface가 바로 `Run Atlas / 페이지 보드`로 읽히는가 | eyebrow / title / compact copy가 route board 앞에서 명확히 읽힌다 | preview/log 인상이 더 먼저 튄다 |
| `.run-route-board` / `.run-route-node` | current node와 next path가 첫 스크린에서 immediately 읽히는가 | current / past / shop / boss 분위기가 node 자체에서 드러난다 | route node가 그냥 작은 리스트 카드처럼 보인다 |
| `.run-side-summary` / `.forecast-item` | forecast는 atlas 보조 맥락으로만 읽히는가 | 다음 상점 / 다음 보스 두 줄이 route board 아래 보조선처럼 붙는다 | forecast가 stage 핵심 카드처럼 과하게 커진다 |
| `renderRunPressureCards(summary)` 결과 | threshold pressure가 atlas decision 문맥으로 이어지는가 | upper bonus 카드가 `score / thresholdLabel`을 짧게 묶어 준다 | pressure가 battle HUD처럼 읽힌다 |
| `.run-peek-panel`, `.log-panel` | preview / log가 atlas 핵심 scope를 침범하지 않는가 | 부가 정보처럼 남는다 | preview/log가 route board보다 먼저 읽힌다 |

### role-specific handoff points
- UI는 hierarchy note를 `무엇이 먼저 읽히는가` 중심으로 남긴다.
- 아트는 `Route Node Starter` token이 route board first-glance를 방해하지 않는지만 확인한다.
- 둘 다 preview/log shell 재설계 제안은 이번 stage에서 보류한다.

---

## 5. capture anchor

## 3줄 요약
- atlas 캡처는 시안 모음이 아니라 semantic anchor의 결과를 증명하는 두 장 세트다.
- overview는 `route node가 먼저 읽히는가`, threshold focus는 `threshold pressure가 atlas 문맥으로 붙는가`를 답해야 한다.
- 이 두 질문이 빠지면 review-ready가 아니라 그냥 예쁜 화면 모음이다.

### one-line goal
overview / threshold-focus를 `atlas-only semantic question` 두 개로 고정한다.

| capture | 답해야 하는 질문 | 반드시 같이 적을 note | 금지 혼입 |
| --- | --- | --- | --- |
| `captures/atlas-1440-overview.png` | 첫 스크린에서 route board와 current node가 forecast / preview / log보다 먼저 읽히는가 | `notes/density-review.md` + `checks/dom-sanity.md` | battle/oath screen, generic `latest.png` |
| `captures/atlas-1440-threshold-focus.png` | threshold pressure가 `thresholdLabel / score / pointsLeft` 문맥으로 atlas route reading에 붙는가 | `notes/density-review.md` + `notes/handoff-line.md` | upper bonus overlay, battle bonus draft, reward/shop overlay |

### QA note에 꼭 들어갈 문장
> overview / threshold-focus 캡처는 모두 atlas route node stage 안에서만 기록하며, preview/log polish나 battle/oath evidence로 재사용하지 않는다.

---

## 6. token sanity anchor

## 3줄 요약
- atlas 아트 sanity의 semantic anchor는 `Route Node Starter`, `route node tone`, `threshold focus contrast`뿐이다.
- token sanity는 새 자산 발주 여부가 아니라 현재 stage를 닫아도 되는 최소 충분성 verdict여야 한다.
- atlas primer가 아니라 route board surface인 만큼, 큰 배경이나 공용 frame 논의로 커지는 순간 stage drift다.

### one-line goal
atlas token sanity를 `route node first-glance 유지`라는 단일 질문으로 제한한다.

### 아트 note에 적을 최소 구조
1. `이번 sanity는 Route Node Starter / route node tone / threshold focus contrast만 확인한다.`
2. `verdict: 충분 / 약함 / 과함`
3. `이번 stage 비범위: battle stakes plaque, oath banner, preview/log 재장식, 신규 공통 frame`

### 금지되는 확장
- `battle plaque도 같은 frame family로 맞추자`
- `oath banner seal까지 같이 재정리하자`
- `Run Table 전체 side-panel background를 새로 열자`
- `preview/log도 atlas asset bundle로 포함하자`

---

## 7. review-ready evidence rule

## 3줄 요약
- semantic anchor 재확인은 review-ready의 선행 조건이지 green 자체가 아니다.
- review-ready는 `locator 메모 + atlas-only diff + overview / threshold-focus 질문 + boundary note`가 같은 문장을 쓸 때 성립한다.
- locator가 있어도 wording이 generic하거나 다른 surface가 섞이면 아직 bootstrap / in-progress다.

### review-ready로 볼 수 있는 경우
- `notes/hook-manifest.md`가 `surface root / route node / threshold / boundary` 4축을 atlas-only로 기록한다.
- `checks/dom-sanity.md`가 `.run-route-board`, `.run-route-node`, threshold pressure zone 존재와 preview/log 비범위를 함께 판정한다.
- `notes/density-review.md`가 overview / threshold-focus 두 질문을 `atlas-only` 범위로 기록한다.
- `manifest.md`와 `notes/handoff-line.md`가 `battle/oath/queue closing 미오픈`을 유지한다.

### 아직 review-ready가 아닌 경우
- function grep 결과만 있고 semantic family 설명이 없다.
- threshold note가 upper bonus overlay나 battle draft까지 미리 끌고 온다.
- overview 캡처가 있어도 어떤 semantic question을 답하는지 note에 연결돼 있지 않다.
- preview/log 재배치 메모가 atlas route node note 안에 섞여 있다.

### 한 줄 판정
atlas semantic anchor packet의 목적은 **무엇을 찾았는지**보다 **찾고도 범위를 넓히지 않았는지**를 증명하는 것이다.

---

## 8. stop / go 판정

### GO 조건
- archive / stub / file route가 이미 생성돼 있다.
- programmer note가 `renderRouteBoard()`와 threshold pressure zone만 다룬다고 적는다.
- UI / QA가 overview / threshold-focus 두 질문을 atlas-only로 기록한다.
- PM boundary note가 `battle-stakes-plaque unlock 대기`를 유지한다.

### STOP 신호
- preview / log 재배치가 route node edit보다 먼저 열린다.
- `data-stakes-slot`, `data-oath-role`, battle/oath capture가 note에 들어간다.
- threshold note가 upper bonus overlay 선택 흐름까지 확장된다.
- overview 캡처가 route board보다 preview/log 가독성 회고로 변한다.

### STOP 이후 다음 행동
1. semantic anchor note부터 `atlas-only`로 되돌린다.
2. 이번 turn에서 어떤 locator가 과하게 열렸는지 boundary note에 적는다.
3. 필요하면 이번 stage는 `archive-in-progress`로 남기고 battle은 열지 않는다.

---

## 9. 역할별 handoff 포인트

### 프로그래머
- semantic anchor note는 code diff보다 먼저 남긴다.
- `root / route node / threshold / boundary / non-change` 다섯 줄이면 충분하다.
- preview/log helper 정리나 battle/oath surface까지 넓히기 시작하면 atlas 범위를 벗어난다.

### UI
- hierarchy note는 `route node first-glance`와 `threshold focus`만 본다.
- preview/log polish는 다음 stage 또는 별도 backlog다.
- overview / threshold-focus 질문을 note에 먼저 써 두고 캡처를 채운다.

### 아트
- token sanity는 Route Node Starter 세트에 한정한다.
- 신규 대형 자산은 이번 stage pass 조건이 아니다.
- `forecast가 핵심처럼 보이지 않는다`, `threshold가 route atlas 문맥에 붙는다` 같은 문장을 남기는 쪽이 더 중요하다.

### QA
- overview / threshold-focus 질문을 같은 로그 안에 묶는다.
- `atlas-only boundary 유지`가 빠지면 pass를 미룬다.
- preview/log glamour shot은 review-ready 근거가 아니다.

### PM/기획
- semantic anchor 재확인은 kickoff 메모 층위다.
- green / unlock 문장은 evidence 정렬 뒤에만 쓴다.
- `Run Table 진행`, `atlas도 하고 battle도 준비` 같은 큰 문장은 계속 금지한다.

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| line drift 때문에 implementer가 atlas semantic zone을 넘어 preview/log나 다른 surface까지 같이 grep하는 위험 | atlas first-stage가 다시 큰 UI polish 묶음으로 부푼다 | semantic family를 code / DOM / evidence 질문 단위로 고정 | atlas 첫 live edit 직전 |
| threshold pressure note가 upper bonus overlay / battle bonus draft까지 커지는 위험 | atlas threshold focus가 battle runtime과 다시 섞인다 | threshold anchor를 `thresholdLabel / pointsLeft / progressLabel` 위주로 한정 | programmer review-ready 제출 시 |
| overview / threshold-focus 캡처가 generic shot으로 흐르는 위험 | QA candidate가 visual 참고 수준으로 약해진다 | capture anchor를 질문 기반 2장 세트로 고정 | QA candidate 직전 |
| token sanity가 battle/oath 톤 정렬 논의로 커지는 위험 | atlas stage가 asset backlog 회의로 변한다 | Route Node Starter 세트만 atlas semantic zone으로 한정 | UI / 아트 sanity review 시 |
| PM이 semantic anchor 재확인을 green처럼 기록하는 위험 | half-cutover가 완료처럼 보인다 | semantic anchor는 kickoff layer, green은 evidence layer로 분리 | tracker / run log 작성 시 |

---

## 11. 즉시 다음 액션
1. 다음 atlas 실제 refinement는 `source-of-truth map -> gap ledger -> atlas live kickoff -> atlas archive file map -> 이 semantic anchor packet` 순서로 읽는다.
2. programmer는 `notes/hook-manifest.md`에 `root / route node / threshold / boundary / non-change` 다섯 줄을 먼저 적는다.
3. UI는 `notes/density-review.md`에 overview / threshold-focus 질문을 먼저 적는다.
4. 그 뒤에만 atlas-only edit와 evidence drop을 시작한다.
5. `archive-ready candidate / green / battle unlock`은 여전히 이 semantic anchor 메모 뒤의 evidence bundle로만 닫는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_archive_file_map_packet_kr.md`
- `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_filled_archive_examples_kr.md`
- `docs/dicespell_game_flow_token_atlas_handoff_scorecard_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
