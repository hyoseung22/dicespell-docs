# DiceSpell `Run Table` Battle Stakes Plaque semantic anchor packet

## 3줄 요약
- 이 문서는 `battle live kickoff`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도, **`좋아, 그럼 실제 battle live edit 직전에 정확히 어느 함수 / DOM / capture 질문을 같은 책임으로 다시 찾아야 하지?`**를 닫는 source-of-truth다.
- 핵심은 새 전투 화면을 더 설계하는 것이 아니라, `renderBattle(summary)` / `battle-table` / `battle-stakes-grid` / `data-stakes-slot="current|next|grimoire"` / action panel·dice tray·log boundary를 **battle semantic zone 기준 계약**으로 다시 묶어 line drift가 있어도 stage drift가 생기지 않게 만드는 것이다.
- 목표는 다음 implementer / UI / 아트 / QA / PM이 battle 첫 live edit 전에 `semantic anchor 재확인`을 더 이상 감으로 하지 않고, **같은 locator 이름 · 같은 non-change line · 같은 evidence 질문**으로 시작하게 만드는 것이다.

**한 줄 목표:** `battle-stakes-plaque` 첫 live turn의 semantic locator를 `surface root / stakes slot state / hierarchy pressure / battle boundary / capture proof` 5축으로 고정해, archive/stub가 준비된 뒤 실제 edit도 끝까지 `battle-only` stage language로 좁게 유지되게 만든다.

---

## 왜 이 문서가 필요한가
지금 battle second-stage 축은 거의 충분하다.

이미 있는 것:
- stage 범위: `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- 첫 10분 순서: `docs/dicespell_game_flow_token_battle_live_kickoff_packet_kr.md`
- exact 제출물: `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- starter sentence: `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`
- field contract: `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- green/signoff: `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
- 문서 위계: `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- 현재 미완료 gap: `docs/dicespell_game_flow_token_gap_ledger_kr.md`

그런데 실제 battle 첫 live turn 직전에는 아직 아래가 남아 있었다.

1. live kickoff packet은 `stakes semantic locator 재확인`을 요구하지만, **무엇을 battle semantic anchor로 볼지**는 battle stage 언어로 한 장에 다시 압축돼 있지 않았다.
2. implementer / UI / QA는 모두 `current / next / grimoire` stakes bundle을 말하지만, 실제 코드에서는 `renderBattle(summary)`, `.battle-stakes-grid`, action panel, dice tray, log, status panel이 가까이 붙어 있어 locator 이름이 약하면 다시 `이 김에 action panel도`, `dice tray spacing도`, `log도 같이` 같은 drift가 생기기 쉽다.
3. battle stage는 atlas보다 화면 정보량이 많아서 line drift가 생길 때 더 쉽게 `전투 전체 polish`로 확대된다. 그래서 line number보다 **semantic family**를 계약으로 다시 잠글 필요가 있다.
4. 이 공백이 남아 있으면 archive / stub / required artifacts는 stage-specific인데 실제 code review와 UI sanity만 다시 generic grep 메모로 돌아가, `battle-stakes-plaque`라는 stage boundary가 가장 먼저 약해진다.

즉 이 문서는 새 방향 문서가 아니라, **battle kickoff 뒤 첫 live edit 전 semantic locator를 같은 문장으로 재확인하게 만드는 마지막 좁은 handoff packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. semantic anchor map`, `3. code locator rule` | `battle stakes stage packet`, `battle live kickoff`, `battle required artifacts` | `renderBattle(summary)` / `battle-table` / `battle-stakes-grid` / `data-stakes-slot` 주변만 다시 찾고 action panel, dice tray, log, atlas/oath surface는 이번 stage에서 열지 않는다 |
| UI | `2. semantic anchor map`, `4. DOM/hierarchy anchor`, `5. capture anchor` | `battle required artifacts`, `battle candidate review` | current/next/grimoire first-glance와 stakes focus만 판정하며 action panel/HUD 전체 polish나 oath density는 이번 stage 바깥이다 |
| 아트 | `4. DOM/hierarchy anchor`, `6. token sanity anchor` | `token anchor packet`, `battle required artifacts` | `Stakes Plaque Starter` sanity는 battle stakes zone 안에서만 확인하며 combat shell 전체나 oath/ending family를 같이 묶지 않는다 |
| QA | `2. semantic anchor map`, `5. capture anchor`, `7. review-ready evidence rule` | `battle candidate review`, `queue readiness board` | candidate-ready는 first-glance / slot hierarchy / boundary 질문이 같은 battle-only 문장으로 묶였을 때만 성립한다 |
| PM/기획 | `0. first screen 결론`, `7. review-ready evidence rule`, `8. stop/go` | `queue readiness board`, `battle candidate review` | semantic anchor 재확인은 kickoff 층위이고, green / oath unlock 문장은 여전히 evidence 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- battle 첫 live edit 직전에 정확히 어떤 함수 / DOM / 캡처를 다시 찾아야 하지?
- line drift가 생겨도 `battle-only` stage를 어떻게 유지하지?
- 무엇을 semantic anchor라고 부르고, 무엇은 아직 금지 zone이지?

### 가장 짧은 답
- battle semantic anchor는 **surface root / stakes slot state / hierarchy pressure / battle boundary / first-glance·stakes-focus evidence** 다섯 묶음이다.
- 프로그래머는 `renderBattle(summary)`, `.battle-table`, `.battle-stakes-grid`, `[data-stakes-slot="current|next|grimoire"]`, stakes card title/value zone 주변만 다시 찾는다.
- UI / QA는 `Battle Table eyebrow -> stakes plaque title -> current / next / grimoire cards -> action panel -> dice tray / log` 위계와 `current`의 즉시 읽힘, `next/grimoire`의 관계 읽힘을 확인한다.
- `renderRouteBoard()`, `renderEnding()`, `data-oath-role`, `data-overrun-cta`, overrun/onboarding helper surface는 battle semantic zone 바깥이다.

### 한 줄 판정 규칙
battle semantic anchor 재확인은 **locator를 다시 찾는 일**이지 **stage를 다시 설계하거나 전투 전체 범위를 다시 넓히는 일**이 아니다.

---

## 1. core structure

## 3줄 요약
- battle semantic anchor는 `렌더 루트`, `stakes slot`, `hierarchy`, `경계`, `증거 질문` 5축으로 나뉜다.
- 각 축마다 `무엇을 찾는가`, `왜 찾는가`, `같이 열면 안 되는 주변 책임`, `어느 live note에 기록하는가`가 따로 있다.
- 이 문서의 역할은 implementer / UI / QA가 같은 locator 이름을 쓰게 만드는 것이지, 새로운 line contract를 만드는 것이 아니다.

### 한 줄 목표
semantic anchor를 `grep 결과`가 아니라 `battle stage 경계를 다시 잠그는 짧은 계약`으로 만든다.

---

## 2. semantic anchor map

| anchor family | canonical locator | 왜 다시 찾는가 | 반드시 기록할 live file | 같이 열면 안 되는 것 |
| --- | --- | --- | --- | --- |
| Surface root anchor | `renderBattle(summary)`, `.battle-table`, `Battle Table`, `data-run-surface="battle-table"` | battle stage를 전투 화면 전체가 아니라 stakes plaque first-glance 기준으로 다시 잠그기 위해 | `notes/hook-manifest.md` | `renderRouteBoard()`, `renderEnding()`, atlas/oath/onboarding root hook 선추가 |
| Stakes slot anchor | `.battle-stakes-grid`, `.stakes-card`, `[data-stakes-slot="current"]`, `[data-stakes-slot="next"]`, `[data-stakes-slot="grimoire"]` | current / next / grimoire 읽힘을 battle-only vocabulary로 고정하기 위해 | `notes/hook-manifest.md`, `checks/dom-sanity.md` | action panel card, spell list, generic stat card를 stakes 증거처럼 사용하는 것 |
| Hierarchy pressure anchor | stakes card title/value/emphasis zone, current 강조 토큰, next/grimoire 보조선, `notes/density-review.md`에서 다룰 first-glance 질문 | `current > next > grimoire` 위계를 review 가능한 hook 언어로 잠그기 위해 | `notes/hook-manifest.md`, `notes/density-review.md` | dice tray, combat log, controls spacing 전체 확장 |
| Battle boundary anchor | `.battle-actions`, `.dice-tray`, `.log-panel`, battle status/summary 보조 영역 | action panel / dice tray / log는 비범위라는 경계를 메모에 먼저 박기 위해 | `notes/hook-manifest.md`, `manifest.md` | action panel helper 재설계, dice tray 레이아웃 pass, log tone 정리 |
| Evidence anchor | `captures/battle-1440-overview.png`, `captures/battle-1440-stakes-focus.png`, `notes/density-review.md` | candidate-ready가 그냥 예쁜 전투 캡처가 아니라 같은 battle semantic 질문 두 개를 답하게 만들기 위해 | `captures/*`, `notes/density-review.md`, `notes/handoff-line.md` | atlas/oath capture 혼입, generic `final.png` |

### 한 줄 결론
battle anchor는 `함수 이름 몇 개`만이 아니라 **code + DOM + evidence question**이 함께 묶인 stage locator다.

---

## 3. code locator rule

## 3줄 요약
- 프로그래머는 line number가 아니라 `battle root`, `stakes slot`, `hierarchy`, `boundary` 네 책임을 먼저 다시 찾는다.
- 이 단계의 진짜 목적은 `무엇을 바꾸나`보다 `무엇을 아직 안 바꾸나`를 live note에 먼저 적는 것이다.
- line drift가 생겨도 semantic family가 같으면 stage는 그대로고, 주변 action panel / dice tray / log / 다른 surface까지 넓어지면 stage drift다.

### one-line goal
`renderBattle(summary)` / `.battle-stakes-grid` / `[data-stakes-slot]` / `.battle-actions` / `.dice-tray` / `.log-panel`까지를 battle semantic zone으로 다시 찾고, 그 밖의 atlas/oath/ending helper와 전투 보조 패널 대확장은 이번 turn에 닫아 둔다.

### 찾은 뒤 note에 남길 최소 5줄
1. `root family:` battle surface가 실제로 시작하는 render zone과 root hook
2. `stakes slot family:` current / next / grimoire를 읽게 하는 class / data attribute / title zone
3. `hierarchy family:` current 우선 읽힘과 next/grimoire 보조 관계를 만드는 emphasis zone
4. `boundary family:` action panel / dice tray / log는 이번 stage 비범위라는 선언
5. `non-change line:` atlas/oath helper, action panel layout, dice tray rhythm, log tone은 untouched

### 허용되는 기록 예시
> `renderBattle(summary)` semantic zone에서는 ".battle-stakes-grid"와 `[data-stakes-slot]`의 current / next / grimoire 읽힘만 다루고, action panel / dice tray / log 구조 재설계와 atlas/oath surface root는 이번 stage에서 열지 않는다.

> `battle root`와 `stakes slot` note에서는 `current > next > grimoire` 위계를 review 가능한 DOM vocabulary로만 고정하고, controls spacing이나 log density는 비범위로 남긴다.

### 금지되는 기록 예시
- `이 김에 action panel hierarchy도 같이 다듬자`
- `dice tray spacing도 battle pass에 포함하자`
- `oath banner 쪽 copy tone도 미리 맞춰 두자`
- `Battle Table 전체 shell을 공용 helper로 정리 예정`

---

## 4. DOM / hierarchy anchor

## 3줄 요약
- UI / 아트는 battle에서 더 화려한 전투 HUD를 만드는 것이 아니라, battle surface가 여전히 stakes plaque 중심으로 읽히는지 본다.
- semantic anchor는 결국 DOM hierarchy와 density에서 확인돼야 한다.
- 이 단계에서 필요한 건 새 shell이 아니라 `어디까지가 stakes plaque first-glance이고 어디부터가 action panel / dice tray / log인가`의 경계 확인이다.

### one-line goal
`Battle Table eyebrow -> stakes plaque title -> current / next / grimoire cards -> action panel -> dice tray / log` 위계를 유지한 채 current/next/grimoire만 battle stage 핵심 읽힘으로 잠근다.

### UI / 아트가 다시 확인할 DOM zone
| DOM zone | battle에서 묻는 질문 | pass 기준 | fail 예시 |
| --- | --- | --- | --- |
| `.battle-table` 헤더 | battle surface가 바로 `Battle Table`과 stakes plaque 문맥으로 읽히는가 | eyebrow / title / compact stakes copy가 grid 앞에서 명확히 읽힌다 | action panel headline이나 로그 인상이 더 먼저 튄다 |
| `.battle-stakes-grid` / `.stakes-card` | current node와 next/grimoire 관계가 첫 스크린에서 즉시 읽히는가 | current가 먼저 읽히고 next/grimoire가 같은 family 안에서 따라온다 | 3장이 그냥 비슷한 stat 카드처럼 보인다 |
| `[data-stakes-slot]` | slot 이름과 역할이 같은 문장으로 붙어 있는가 | current / next / grimoire가 hook과 시각 위계 모두에서 맞는다 | next/grimoire가 보조 태그처럼 사라진다 |
| `.battle-actions` | action panel이 battle 핵심 scope를 침범하지 않는가 | stakes plaque가 먼저 읽히고 actions는 뒤에 온다 | action panel이 stakes보다 먼저 튄다 |
| `.dice-tray`, `.log-panel` | dice/log가 battle stage 핵심 scope를 침범하지 않는가 | 보조 정보처럼 남는다 | 주사위/로그가 stakes보다 먼저 읽힌다 |

### role-specific handoff points
- UI는 hierarchy note를 `무엇이 먼저 읽히는가` 중심으로 남긴다.
- 아트는 `Stakes Plaque Starter` token이 stakes first-glance를 방해하지 않는지만 확인한다.
- 둘 다 action panel, dice tray, log shell 재설계 제안은 이번 stage에서 보류한다.

---

## 5. capture anchor

## 3줄 요약
- battle 캡처는 시안 모음이 아니라 semantic anchor의 결과를 증명하는 두 장 세트다.
- overview는 `current stakes가 먼저 읽히는가`, stakes focus는 `current / next / grimoire 관계가 같은 문장으로 붙는가`를 답해야 한다.
- 이 두 질문이 빠지면 review-ready가 아니라 그냥 예쁜 전투 화면 모음이다.

### one-line goal
overview / stakes-focus를 `battle-only semantic question` 두 개로 고정한다.

| capture | 답해야 하는 질문 | 반드시 같이 적을 note | 금지 혼입 |
| --- | --- | --- | --- |
| `captures/battle-1440-overview.png` | 첫 스크린에서 current stakes가 action panel / dice tray / log보다 먼저 읽히는가 | `notes/density-review.md` + `checks/dom-sanity.md` | atlas/oath screen, generic `latest.png` |
| `captures/battle-1440-stakes-focus.png` | current / next / grimoire slot 관계가 한 장에서 same-family로 읽히는가 | `notes/density-review.md` + `notes/handoff-line.md` | controls close-up, dice tray glamour shot, reward/shop overlay |

### QA note에 꼭 들어갈 문장
> overview / stakes-focus 캡처는 모두 battle stakes plaque stage 안에서만 기록하며, action panel / dice tray / log polish evidence로 재사용하지 않는다.

---

## 6. token sanity anchor

## 3줄 요약
- battle 아트 sanity의 semantic anchor는 `Stakes Plaque Starter`, `slot contrast`, `current emphasis`, `next/grimoire support tone`뿐이다.
- token sanity는 새 자산 발주 여부가 아니라 현재 stage를 닫아도 되는 최소 충분성 verdict여야 한다.
- battle은 정보량이 많기 때문에 큰 battle HUD 리뉴얼 논의로 커지는 순간 stage drift다.

### one-line goal
battle token sanity를 `stakes plaque first-glance 유지`라는 단일 질문으로 제한한다.

### 아트 note에 적을 최소 구조
1. `이번 sanity는 Stakes Plaque Starter / current emphasis / next-grimoire support tone만 확인한다.`
2. `verdict: 충분 / 약함 / 과함`
3. `이번 stage 비범위: action panel shell, dice tray restyle, log tone, oath banner, atlas route node`

### 금지되는 확장
- `battle action panel도 같은 frame family로 맞추자`
- `oath banner seal까지 같이 재정리하자`
- `Run Table 전체 combat shell background를 새로 열자`
- `dice tray / log도 같은 자산 패키지로 포함하자`

---

## 7. review-ready evidence rule

## 3줄 요약
- semantic anchor 재확인은 review-ready의 선행 조건이지 green 자체가 아니다.
- review-ready는 `locator 메모 + battle-only diff + overview / stakes-focus 질문 + boundary note`가 같은 문장을 쓸 때 성립한다.
- locator가 있어도 wording이 generic하거나 다른 surface/패널이 섞이면 아직 bootstrap / in-progress다.

### review-ready로 볼 수 있는 경우
- `notes/hook-manifest.md`가 `surface root / stakes slot / hierarchy / boundary` 4축을 battle-only로 기록한다.
- `checks/dom-sanity.md`가 `.battle-stakes-grid`, `[data-stakes-slot]`, boundary zone 존재와 action panel / dice tray / log 비범위를 함께 판정한다.
- `notes/density-review.md`가 overview / stakes-focus 두 질문을 `battle-only` 범위로 기록한다.
- `manifest.md`와 `notes/handoff-line.md`가 `oath unlock only after battle green`을 유지한다.

### 아직 review-ready가 아닌 경우
- function grep 결과만 있고 semantic family 설명이 없다.
- hierarchy note가 action panel / dice tray / log까지 미리 끌고 온다.
- overview 캡처가 있어도 어떤 semantic question을 답하는지 note에 연결돼 있지 않다.
- atlas/oath helper 메모가 battle stakes note 안에 섞여 있다.

### 한 줄 판정
battle semantic anchor packet의 목적은 **무엇을 찾았는지**보다 **찾고도 범위를 넓히지 않았는지**를 증명하는 것이다.

---

## 8. stop / go 판정

### GO 조건
- archive / stub가 이미 생성돼 있다.
- programmer note가 `renderBattle(summary)`와 stakes slot zone만 다룬다고 적는다.
- UI / QA가 overview / stakes-focus 두 질문을 battle-only로 기록한다.
- PM boundary note가 `oath-banner unlock 대기`를 유지한다.

### STOP 신호
- action panel / dice tray / log 재배치가 stakes edit보다 먼저 열린다.
- `data-route-node-state`, `data-oath-role`, overrun/onboarding helper가 note에 들어간다.
- overview 캡처가 stakes보다 action panel이나 dice tray 가독성 회고로 변한다.
- starter sanity가 battle shell 리디자인 제안으로 커진다.

### STOP 이후 다음 행동
1. semantic anchor note부터 `battle-only`로 되돌린다.
2. 이번 turn에서 어떤 locator가 과하게 열렸는지 boundary note에 적는다.
3. 필요하면 이번 stage는 `archive-in-progress`로 남기고 oath는 열지 않는다.

---

## 9. 역할별 handoff 포인트

### 프로그래머
- semantic anchor note는 code diff보다 먼저 남긴다.
- `root / stakes slot / hierarchy / boundary / non-change` 다섯 줄이면 충분하다.
- action panel, dice tray, log helper 정리나 atlas/oath surface까지 넓히기 시작하면 battle 범위를 벗어난다.

### UI
- hierarchy note는 `current stakes first-glance`와 `next/grimoire support`만 본다.
- action panel/HUD 전체 polish는 다음 stage 또는 별도 backlog다.
- overview / stakes-focus 질문을 note에 먼저 써 두고 캡처를 채운다.

### 아트
- token sanity는 Stakes Plaque Starter 세트에 한정한다.
- 신규 대형 자산은 이번 stage pass 조건이 아니다.
- `current가 먼저 읽힌다`, `next/grimoire가 같은 family에 남는다` 같은 문장을 남기는 쪽이 더 중요하다.

### QA
- overview / stakes-focus 질문을 같은 로그 안에 묶는다.
- `battle-only boundary 유지`가 빠지면 pass를 미룬다.
- action panel glamour shot이나 dice tray 샷은 review-ready 근거가 아니다.

### PM/기획
- semantic anchor 재확인은 kickoff 메모 층위다.
- green / unlock 문장은 evidence 정렬 뒤에만 쓴다.
- `Battle Table 진행`, `전투 polish 계속`, `oath도 준비` 같은 큰 문장은 계속 금지한다.

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| line drift 때문에 implementer가 battle semantic zone을 넘어 action panel / dice tray / log까지 같이 grep하는 위험 | battle second-stage가 다시 큰 전투 polish 묶음으로 부푼다 | semantic family를 code / DOM / evidence 질문 단위로 고정 | battle 첫 live edit 직전 |
| hierarchy note가 action panel / dice tray / controls layout까지 커지는 위험 | `current > next > grimoire` stage 핵심이 약해진다 | hierarchy anchor를 stakes card title/value/emphasis zone으로 한정 | programmer review-ready 제출 시 |
| overview / stakes-focus 캡처가 generic combat shot으로 흐르는 위험 | QA candidate가 visual 참고 수준으로 약해진다 | capture anchor를 질문 기반 2장 세트로 고정 | QA candidate 직전 |
| token sanity가 battle HUD 전체 리뉴얼 논의로 커지는 위험 | battle green이 늦어지고 oath unlock 경계가 흐려진다 | `Stakes Plaque Starter` 세트만 battle semantic zone으로 한정 | UI / 아트 sanity review 시 |
| PM이 semantic anchor 재확인을 green처럼 기록하는 위험 | half-cutover가 완료처럼 보인다 | semantic anchor는 kickoff layer, green은 evidence layer로 분리 | tracker / run log 작성 시 |

---

## 11. 즉시 다음 액션
1. 다음 battle 실제 refinement는 `source-of-truth map -> gap ledger -> battle live kickoff -> 이 semantic anchor packet` 순서로 읽는다.
2. programmer는 `notes/hook-manifest.md`에 `root / stakes slot / hierarchy / boundary / non-change` 다섯 줄을 먼저 적는다.
3. UI는 `notes/density-review.md`에 overview / stakes-focus 질문을 먼저 적는다.
4. 그 뒤에만 battle-only edit와 evidence drop을 시작한다.
5. `archive-ready candidate / green / oath unlock`은 여전히 이 semantic anchor 메모 뒤의 evidence bundle로만 닫는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
