# DiceSpell `Run Table` Battle Stakes Plaque Candidate Review 패킷

## 3줄 요약
- 이 문서는 `battle stakes stage packet`과 `battle required artifacts packet`까지 충분한 상태에서도 마지막으로 남아 있던 **`좋아, artifact 8종은 모였어. 그런데 QA와 PM은 정확히 어떤 순서와 어떤 문장으로 battle을 candidate에서 green까지 올리지?`**를 고정하는 source-of-truth다.
- 핵심은 새 전투 화면을 더 설계하는 것이 아니라, battle 두 번째 stage review를 **`artifact alignment review -> stakes first-glance verdict -> starter sanity verdict -> candidate line -> green/unlock line`** 순서로 잠가 implementer/UI/아트/QA/PM이 서로 다른 초록불을 같은 뜻으로 쓰지 않게 만드는 것이다.
- 첫 화면만 읽어도 누가 어떤 파일을 보고 pass/fail을 말하는지, 어떤 fail이면 다시 `archive-in-progress`로 내려야 하는지, oath unlock 문장을 언제 써도 되는지가 바로 보여야 한다.

## 한 줄 목표
`battle-stakes-plaque` 두 번째 stage의 review를 **candidate와 green이 다른 층위라는 점까지 포함한 handoff review 절차**로 고정한다.

## 왜 이 문서가 필요한가
지금 Run Table battle 두 번째 stage는 아래 문서까지 이미 닫혀 있다.

- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`

즉 아래는 이미 정리돼 있다.

- 둘째 stage가 왜 battle인지
- stage boundary가 어디까지인지
- required artifacts 8종이 무엇인지
- `bootstrap-ready -> archive-in-progress -> archive-ready candidate -> battle green` ownership이 무엇인지

하지만 실제 candidate 직전 마지막 공백은 여전히 남아 있다.

1. artifact 8종이 있어도 **누가 어떤 순서로 읽어 같은 battle stage 결론을 내리는지**가 없으면 QA와 PM이 서로 다른 기준으로 candidate/green을 적기 쉽다.
2. `overview/focus capture`와 `density review`, `starter asset list`가 각각 있어도, 어떤 fail이 `candidate 보류`인지와 어떤 fail이 `green 보류`인지가 분리돼 있지 않으면 다시 `전투 UI 거의 완료` 문장이 생긴다.
3. implementer는 bundle을 채웠다고 생각하고, UI/아트는 slot hierarchy 보완 note가 더 필요하다고 느끼고, PM은 unlock까지 써 버리는 식의 **층위 충돌**이 battle stage에서 가장 쉽게 생긴다.
4. oath unlock 직전 마지막 판단 문장이 고정돼 있지 않으면 `battle-stakes-plaque green`이 아니라 `Battle Table 진행` 같은 큰 요약으로 흘러 queue discipline이 무너진다.

즉 지금 필요한 것은 새 설계가 아니라, **battle second stage review를 owner별 읽기 순서 / 질문 / fail rule / unlock 문장까지 압축한 candidate review packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. review sequence`, `3. fail rule` | `battle stakes stage packet`, `battle required artifacts packet` | artifact 제출이 끝이어도 QA candidate 전에는 아직 green이 아니며, fail note가 오면 battle-only 범위에서 다시 보정해야 한다 |
| UI | `2. review sequence`, `4. first-glance verdict rule`, `5. handoff 문장` | `token closing packet`, `battle required artifacts packet` | overview/focus/density verdict는 candidate 근거이지 전투 미감 회고가 아니고, oath는 여전히 review 범위 밖이다 |
| 아트 | `2. review sequence`, `4. starter sanity verdict rule`, `5. handoff 문장` | `token anchor packet`, `battle required artifacts packet` | starter note는 battle stage closing 근거이며 큰 combat shell backlog 제안으로 확장하면 candidate가 미뤄진다 |
| QA | `2. review sequence`, `3. fail rule`, `5. handoff 문장`, `6. signoff ladder` | `token refinement queue`, `token evidence manifest` | candidate는 artifact alignment + battle-only verdict가 있을 때만 쓰고, green은 PM의 별도 기록이라는 결론을 가져간다 |
| PM/기획 | `0. first screen 결론`, `5. handoff 문장`, `6. signoff ladder`, `7. immediate next actions` | `token gap ledger`, `token refinement queue summary` | `battle-stakes-plaque green, oath-banner unlock` 문장은 QA candidate 뒤에만 쓰고, 큰 총괄 문장은 여전히 금지다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `battle-stakes-plaque archive-ready candidate`와 `battle-stakes-plaque green`은 정확히 어떻게 다른가?
- QA와 PM은 어떤 순서로 무엇을 보고 pass/fail을 말해야 하는가?
- 어떤 fail이면 battle을 다시 `archive-in-progress`로 내려야 하는가?

### 가장 짧은 답
- candidate는 **artifact bundle이 battle stage language로 정렬됐는지**를 말하는 review 단계다.
- green은 **QA candidate가 난 뒤 PM이 oath unlock을 같이 기록하는 운영 단계**다.
- capture가 좋거나 artifact가 많아도 `battle-only 범위`, `current / next / grimoire stakes first-glance`, `starter asset 비범위`, `unlock 문장`이 안 맞으면 candidate가 아니다.
- oath unlock은 `battle-stakes-plaque green`과 같은 줄에서만 열고, 그 전에는 누구도 `다음 stage 시작`을 말하지 않는다.

### 한 줄 판정 규칙
battle 두 번째 stage는 **`candidate = review pass`**, **`green = unlock 기록`**으로 분리한다.

---

## 1. core structure

## 3줄 요약
- review는 `artifact 존재 확인`이 아니라 `같은 stage boundary를 말하는 bundle인가`를 확인하는 절차다.
- 순서는 `문장 정렬 -> 시선 정렬 -> starter 정렬 -> candidate -> green`이다.
- 한 단계라도 fail이면 다시 `archive-in-progress` note로 내려가고, oath unlock은 보류한다.

### 한 줄 목표
battle review를 파일 체크가 아니라 **같은 stage 결론을 만드는 review stack**으로 고정한다.

### review stack
1. `manifest / hook-manifest / dom-sanity / handoff-line` 문장 정렬 확인
2. `overview / stakes-focus / density-review` stakes first-glance verdict 확인
3. `starter-asset-list`의 충분성/비범위 확인
4. QA `battle-stakes-plaque archive-ready candidate` 기록
5. PM `battle-stakes-plaque green, oath-banner unlock` 기록

---

## 2. review sequence

## 3줄 요약
- QA와 PM은 모든 artifact를 한 번에 감상하지 않는다. 읽는 순서가 고정돼야 drift가 줄어든다.
- 먼저 문장 경계가 맞는지 보고, 다음에 시각 evidence를 보고, 마지막에 starter/운영 문장을 본다.
- review sequence를 건너뛰면 overview 캡처 한 장만 보고 green처럼 느끼는 오류가 다시 생긴다.

### 한 줄 목표
review 순서를 `artifact alignment -> visual verdict -> starter verdict -> signoff`로 잠근다.

| 순서 | 무엇을 본다 | 주 책임자 | pass 질문 | fail이면 어떻게 하나 |
| --- | --- | --- | --- | --- |
| 1 | `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md`, `notes/handoff-line.md` | QA | 네 파일이 모두 `battle-stakes-plaque` / `current-next-grimoire` / `stakes` / `oath unlock only after battle green` 같은 뜻을 말하는가? | wording drift note를 남기고 `archive-in-progress` 유지 |
| 2 | `captures/battle-1440-overview.png`, `captures/battle-1440-stakes-focus.png`, `notes/density-review.md` | UI + QA | current stakes와 next/grimoire hierarchy가 first-glance로 읽히고 density verdict가 두 캡처를 직접 참조하는가? | capture 또는 density note 보강 후 재검토 |
| 3 | `assets/starter-asset-list.md` | 아트 + QA | `Stakes Plaque Starter` verdict와 `이번 stage에서 아직 안 하는 것`이 같이 적혀 있는가? | starter note 보강 후 재검토 |
| 4 | candidate line | QA | 위 1~3이 모두 pass이며 atlas/oath drift가 없는가? | `candidate 보류` note를 남기고 PM green 금지 |
| 5 | green/unlock line | PM/기획 | QA candidate가 기록됐고 handoff-line 초안과 unlock 문장이 같은 뜻인가? | battle green 보류, oath unlock 금지 |

### review 순서에서 의도적으로 하지 않는 것
- prototype board를 final contract처럼 다시 읽지 않는다.
- atlas/oath 캡처와 비교 평가를 하지 않는다.
- `전투 화면 전체적으로 더 좋아졌는가` 같은 큰 질문을 던지지 않는다.

---

## 3. fail rule

## 3줄 요약
- battle review의 핵심은 `조금 아쉽지만 candidate` 같은 회색지대를 줄이는 것이다.
- fail은 aesthetic dislike가 아니라 stage contract 불일치일 때 선언한다.
- fail을 짧고 명확하게 적어야 battle-only 범위에서 다시 닫을 수 있다.

### 한 줄 목표
fail을 `느낌`이 아니라 `경계/문장/증거 불일치`로만 남긴다.

### candidate 보류로 내려야 하는 대표 fail
1. `manifest.md`는 battle-only인데 `handoff-line.md`가 `Battle Table green`처럼 큰 문장을 쓴다.
2. `hook-manifest.md`와 `dom-sanity.md`가 다른 hook 이름이나 다른 boundary를 말한다.
3. overview 캡처는 있는데 stakes-focus와 `density-review.md`가 연결되지 않는다.
4. `starter-asset-list.md`가 Stakes Plaque Starter note가 아니라 combat shell 리뉴얼 backlog처럼 읽힌다.
5. note 중 하나라도 atlas/oath 범위를 이번 stage 안쪽처럼 적는다.

### green 보류로 내려야 하는 대표 fail
1. QA candidate는 있지만 PM 기록 문장이 `oath unlock` 조건 없이 battle만 vague하게 끝낸다.
2. handoff-line 초안과 PM green 문장이 서로 다른 단계 언어를 쓴다.
3. candidate 이후 추가 note에서 oath를 같이 열자는 문장이 등장한다.

### fail 메모 짧은 예시
- `battle-stakes-plaque candidate 보류: handoff-line이 battle stage가 아닌 Battle Table 총괄 문장으로 커짐.`
- `battle-stakes-plaque candidate 보류: density-review가 overview/focus 두 캡처를 직접 참조하지 않음.`
- `battle-stakes-plaque green 보류: QA candidate는 있으나 oath unlock 문장이 handoff-line과 불일치.`

---

## 4. verdict rule

## 3줄 요약
- overview/focus/density와 starter note는 각각 다른 verdict를 말해야 한다.
- 시각 verdict는 `무엇이 먼저 읽히는가`, starter verdict는 `지금 stage에서 충분한가`를 말한다.
- 둘 중 하나라도 큰 redesign 제안으로 흐르면 battle stage review가 아니라 backlog 회의가 된다.

### 한 줄 목표
battle review를 `stakes first-glance verdict`와 `starter sanity verdict` 두 축으로 나눈다.

### 4-1. stakes first-glance verdict rule

| 항목 | pass 질문 | pass 예시 | fail 예시 |
| --- | --- | --- | --- |
| overview | current stakes가 first-glance로 읽히는가? | `current stakes가 먼저 읽히고 next/grimoire와 action panel은 그 뒤에 따라온다` | `action panel이나 하단 controls가 먼저 커 보인다` |
| stakes-focus | current / next / grimoire slot 관계가 같은 언어로 붙어 있는가? | `세 slot과 stakes plaque 계층이 한 장에서 이어진다` | `current만 강조되고 next/grimoire는 다른 카드 무리처럼 보인다` |
| density-review | 위 두 캡처를 근거로 짧은 verdict를 남겼는가? | `유지: overview에서 current stakes가 먼저 읽히고 focus에서 current/next/grimoire slot 연결이 유지된다` | `전체적으로 전투 화면이 괜찮다` |

### 4-2. starter sanity verdict rule

| 항목 | pass 질문 | pass 예시 | fail 예시 |
| --- | --- | --- | --- |
| starter asset list | `Stakes Plaque Starter`가 지금 stage 기준으로 충분/약함/과함 중 무엇인지 말하는가? | `충분: current/next/grimoire 3프레임 차이가 battle stage first-glance를 버틴다. 이번 stage는 action panel shell 재설계를 하지 않는다.` | `향후 전투 공용 frame 재정의 필요` |
| 비범위 선언 | 이번 stage에서 아직 하지 않는 것을 적었는가? | `action panel, atlas route node, oath banner는 비범위` | 비범위가 비어 있음 |

---

## 5. handoff 문장

## 3줄 요약
- handoff 문장은 candidate와 green을 분리하는 마지막 장치다.
- QA 문장과 PM 문장은 서로 비슷하지만 같지 않아야 한다.
- tracker/run log/portal/manifest 모두 아래 문장 축을 재사용한다.

### 한 줄 목표
battle review 문장을 `candidate`와 `green/unlock` 두 줄로 고정한다.

### QA candidate 문장
- `battle-stakes-plaque archive-ready candidate: current / next / grimoire slot hook, stakes first-glance, Stakes Plaque Starter sanity bundle 정렬 확인, oath-banner는 battle green 뒤에만 unlock.`

### PM green 문장
- `battle-stakes-plaque green, oath-banner unlock: battle second-stage evidence bundle과 handoff line 정렬 확인.`

### 금지 문장
- `Battle Table 거의 완료`
- `battle은 사실상 끝`
- `oath도 같이 시작 가능할 듯`
- `토큰 refinement 진행`

### tracker/run log용 짧은 복붙 문장
- candidate: `battle-stakes-plaque candidate는 artifact 8종의 battle-only wording alignment가 맞을 때만 기록한다.`
- green: `battle-stakes-plaque green은 QA candidate 뒤 PM이 oath-banner unlock을 같은 stage명으로 기록할 때만 성립한다.`

---

## 6. signoff ladder

## 3줄 요약
- battle second-stage review도 `제출`, `후보`, `기록`이 다른 사람 손에서 닫혀야 한다.
- review packet은 누가 어떤 순간에 멈출 수 있는지를 더 분명하게 한다.
- 특히 PM은 candidate 이전에 green을 쓸 수 없다.

### 한 줄 목표
candidate review를 stage ownership과 다시 연결한다.

| 단계 | 제출자 | 필수 동반 확인자 | 마지막 기록자 | stop 권한 |
| --- | --- | --- | --- | --- |
| artifact bundle 제출 | 프로그래머 + UI + 아트 | QA | 없음 | QA |
| `archive-ready candidate` | QA | UI 또는 프로그래머, 아트 note 누락 없음 확인 | QA | QA |
| `battle-stakes-plaque green` | PM/기획 | QA candidate 필수 | PM/기획 | PM |
| `oath-banner unlock` | PM/기획 | battle green 필수 | PM/기획 | PM |

### stop rule
- QA는 wording drift, capture drift, starter note drift 중 하나라도 있으면 candidate를 보류한다.
- PM은 QA candidate가 없거나 handoff-line과 green 문장이 다르면 green을 보류한다.
- 누구도 battle green 전 oath unlock을 쓰지 않는다.

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| artifact 8종이 모여도 QA/PM이 서로 다른 순서로 읽어 candidate와 green 기준이 다시 갈리는 위험 | battle stage가 문서상으론 닫혔는데 실제 기록은 다시 사람 설명 의존형으로 흐른다 | 이번 packet에서 review 순서와 fail rule, candidate/green 문장을 고정 | 첫 battle candidate review 직전 |
| overview/focus가 예쁜 캡처로 소비되고 density-review가 그 근거를 직접 참조하지 않는 위험 | stakes first-glance evidence가 약해져 candidate가 다시 감으로 판정된다 | stakes first-glance verdict rule로 캡처별 질문과 fail 예시를 분리 | 첫 density review 작성 직전 |
| starter asset note가 battle stage closing 근거가 아니라 combat shell backlog로 커지는 위험 | battle green이 불필요하게 늦어지고 범위가 커진다 | starter sanity verdict를 `충분성 + 비범위 선언`으로 고정 | 아트 note 작성 직전 |
| PM이 candidate를 green처럼 기록하거나 green을 큰 총괄 문장으로 남기는 위험 | oath unlock이 조기 개방되고 queue discipline이 깨진다 | handoff 문장과 signoff ladder를 분리해 stage명 기준으로만 기록하게 고정 | battle green 기록 직전 |

---

## immediate next actions

1. 다음 battle refinement review 직전에는 `token refinement queue -> battle stakes stage packet -> battle required artifacts packet -> 이 candidate review packet` 순서로 읽는다.
2. QA는 먼저 `manifest / hook-manifest / dom-sanity / handoff-line` 문장 정렬부터 확인한다.
3. UI/QA는 overview/focus 캡처와 density-review가 같은 질문에 답하는지 확인한다.
4. 아트/QA는 starter verdict와 비범위 선언이 같이 있는지 확인한다.
5. QA가 candidate를 기록한 뒤에만 PM이 `battle-stakes-plaque green, oath-banner unlock` 문장을 남긴다.
6. green 전에는 oath 관련 새 review나 unlock 문장을 어디에도 남기지 않는다.

---

## 한 장 결론
battle second stage의 마지막 공백은 `artifact가 있나`가 아니라 **`그 artifact를 누가 어떤 질문으로 읽고, 언제 candidate와 green을 각각 말하나`**였다. 이 문서는 그 review 절차를 한 화면으로 줄여, battle 턴이 다시 감상형 closing이나 큰 총괄 green 문장으로 흐르지 않게 만드는 candidate review packet이다.
