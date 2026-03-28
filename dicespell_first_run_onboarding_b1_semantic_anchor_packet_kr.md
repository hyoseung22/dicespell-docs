# DiceSpell 첫 런 온보딩 `B-1 library` semantic anchor packet

## 3줄 요약
- 이 문서는 `B-1 library packet`, `live kickoff`, `required artifacts`, `artifact stub`, `review packet`까지로도 남아 있던 마지막 locator 공백인 **`좋아, 그럼 실제 코드를 열기 직전에 정확히 어느 함수/DOM/캡처를 같은 책임으로 다시 찾아야 하지?`**를 닫는 source-of-truth다.
- 핵심은 새 primer를 더 설계하는 것이 아니라, `readMetaProgress` / `persistMetaProgress` / `renderGrimoireShelf` / `openingPlan` / `caution` / library first-open·return-state evidence를 **semantic zone 기준의 계약**으로 다시 묶어 line drift가 있어도 stage drift가 생기지 않게 만드는 것이다.
- 목표는 다음 implementer/UI/QA/PM이 B-1 첫 live edit 전 `semantic anchor 재확인`을 더 이상 감으로 하지 않고, **같은 locator 이름·같은 non-change line·같은 evidence 질문**으로 시작하게 만드는 것이다.

**한 줄 목표:** `B-1 library` 첫 live turn의 semantic locator를 `meta state / library shell / highlight hook / CTA path / capture proof` 5축으로 고정해, archive/stub가 준비된 뒤 실제 edit도 끝까지 `library-only` stage 언어로 좁게 유지되게 만든다.

---

## 왜 이 문서가 필요한가
지금 B-1 library 축은 거의 충분하다.

이미 있는 것:
- stage 범위: `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- 첫 10분 순서: `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- exact 제출물: `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- starter sentence: `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- green/signoff: `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- cross-track 착수선: `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`

그런데 실제 B-1 첫 live turn 직전에는 아직 아래가 남아 있었다.

1. kickoff packet은 `semantic anchor 재확인`을 요구하지만, **무엇을 semantic anchor로 볼지**는 B-1 surface 언어로 한 장에 다시 압축돼 있지 않았다.
2. `B-1 library packet`에는 runtime anchor가 있지만, implementer/UI/QA가 실제 첫 10분 안에 같은 이름으로 다시 찾을 **function/DOM/evidence question 세트**가 아직 stage packet으로 분리돼 있지 않았다.
3. line number가 조금 밀리거나 body-copy 위치가 미세하게 바뀌면, implementer가 `그럼 이 김에 battle primer slot도 같이 보자` 같은 식으로 surface를 넓히기 쉬웠다.
4. 이 공백이 남아 있으면 archive/stub는 stage-specific인데 실제 code review와 UI sanity만 다시 generic grep 메모로 돌아가 B-1의 `library-only` discipline이 약해진다.

즉 이 문서는 새 방향 문서가 아니라, **B-1 kickoff 뒤 첫 live edit 전 semantic locator를 같은 문장으로 재확인하게 만드는 마지막 좁은 handoff packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. semantic anchor map`, `3. code locator rule` | `B-1 library packet`, `live kickoff`, `artifact stub packet` | `readMetaProgress` / `persistMetaProgress` / `renderGrimoireShelf` / `choose-grimoire` path까지만 다시 찾고, battle/reward/shop/ending render path는 같이 열지 않는다 |
| UI | `2. semantic anchor map`, `4. DOM/hierarchy anchor`, `5. capture anchor` | `screen packet`, `review packet` | `hero-primer 1장 + highlight 2곳 + CTA 비차단`만 판정하며 shelf refresh나 다른 surface density는 이번 stage 바깥이다 |
| 아트 | `4. DOM/hierarchy anchor`, `6. token sanity anchor` | `content deck`, `visual asset packet` | primer-library token sanity는 library shell 안에서만 확인하며 battle/reward/ending family를 같이 묶지 않는다 |
| QA | `2. semantic anchor map`, `5. capture anchor`, `7. review-ready evidence rule` | `acceptance matrix`, `required artifacts packet` | B1-A1~A5 / recovery 3종은 first-open·return-state 캡처와 `library-only` boundary note를 함께 봐야 한다 |
| PM/기획 | `0. first screen 결론`, `7. review-ready evidence rule`, `8. stop/go` | `readiness board`, `execution reporting packet` | semantic anchor 재확인은 kickoff 메모이고, green/unlock 문장은 여전히 evidence 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- B-1 첫 live edit 직전에 정확히 어떤 함수/DOM/캡처를 다시 찾아야 하지?
- line drift가 생겨도 `library-only` stage를 어떻게 유지하지?
- 무엇을 semantic anchor라고 부르고, 무엇은 아직 금지 zone이지?

### 가장 짧은 답
- B-1 semantic anchor는 **meta state / library shell / highlight hook / choose CTA path / first-open·return-state evidence** 다섯 묶음이다.
- 프로그래머는 `readMetaProgress`, `persistMetaProgress`, `renderGrimoireShelf`, `data-action="choose-grimoire"` 주변만 다시 찾는다.
- UI/QA는 `section-head -> hero-primer -> body-copy -> legacy loadout -> grimoire grid` 위계와 `openingPlan`, `caution` highlight만 확인한다.
- `renderBattle`, `renderReward`, `renderShop`, `renderEnding`, 다른 seen flag, 공용 primer helper 확장은 B-1 semantic zone 바깥이다.

### 한 줄 판정 규칙
B-1 semantic anchor 재확인은 **locator를 다시 찾는 일**이지 **stage를 다시 설계하는 일**이 아니다.

---

## 1. core structure

## 3줄 요약
- B-1 semantic anchor는 `상태`, `렌더 shell`, `강조 hook`, `행동 경로`, `증거 질문` 5축으로 나뉜다.
- 각 축마다 `무엇을 찾는가`, `왜 찾는가`, `같이 열면 안 되는 주변 책임`, `어느 note에 기록하는가`가 따로 있다.
- 이 문서의 역할은 implementer/UI/QA가 같은 locator 이름을 쓰게 만드는 것이지, 새로운 line contract를 만드는 것이 아니다.

### 한 줄 목표
semantic anchor를 `grep 결과`가 아니라 `B-1 stage 경계를 다시 잠그는 짧은 계약`으로 만든다.

---

## 2. semantic anchor map

| anchor family | canonical locator | 왜 다시 찾는가 | 반드시 기록할 note | 같이 열면 안 되는 것 |
| --- | --- | --- | --- | --- |
| Meta state anchor | `readMetaProgress`, `persistMetaProgress`, `appState.meta.uiGuidance` 또는 동등 zone | `seenBookSelectPrimer`의 저장/재노출 경계를 library-only로 잠그기 위해 | programmer state diff note | `seenBattlePrimer`, `seenRewardPrimer`, `seenShopPrimer`, `seenVictoryPrimer`, `seenOverrunCtaPrimer` 선추가 |
| Library shell anchor | `renderGrimoireShelf()` 본문 shell, `.section-head`, `.body-copy`, `renderLegacyLoadoutPanel()`, `.grimoire-grid` | hero-primer가 어느 높이와 밀도로 들어가야 하는지 stage-specific하게 찾기 위해 | UI hierarchy note | shelf 전체 재배치, modal shell, 별도 primer page |
| Highlight hook anchor | card 내부 `openingPlan`, `caution` 또는 동등 label slot | 강조 범위를 정확히 두 곳으로 한정하고 `추천 책` drift를 막기 위해 | programmer state diff note + UI hierarchy note | `perfectBonus`, `signatureRule`, 카드 전체 강조, 추천 ribbon |
| CTA path anchor | `data-action="choose-grimoire"` 또는 동등 choose flow | primer가 있어도 책 선택 행동이 그대로 살아 있는지 보장하기 위해 | QA acceptance log + UI hierarchy note | CTA 재배치, tutorial confirm blocking, primer dismiss를 선택보다 우선시키는 구조 |
| Evidence anchor | first-open / return-state library capture, B1-A1~A5 / recovery 3종 질문 | review-ready가 캡처/로그/노트의 같은 surface 질문으로 닫히게 만들기 위해 | QA acceptance log + PM boundary note | battle/reward/shop/ending 캡처 혼입, generic `final.png` |

### 한 줄 결론
B-1 anchor는 `코드 함수 3개`만이 아니라 **code + DOM + evidence question**이 함께 묶인 stage locator다.

---

## 3. code locator rule

## 3줄 요약
- 프로그래머는 line number가 아니라 `상태 저장`, `library render`, `choose action` 세 책임을 먼저 다시 찾는다.
- 이 단계의 진짜 목적은 `무엇을 바꾸나`보다 `무엇을 아직 안 바꾸나`를 code note에 먼저 적는 것이다.
- line drift가 생겨도 semantic family가 같으면 stage는 그대로고, 주변 함수까지 넓어지면 stage drift다.

### one-line goal
`readMetaProgress` / `persistMetaProgress` / `renderGrimoireShelf` / choose path를 먼저 다시 찾고, 그 밖의 primer path는 찾더라도 이번 turn에는 닫아 둔다.

### 찾은 뒤 note에 남길 최소 4줄
1. `state family:` `seenBookSelectPrimer`를 어디서 읽고 어디서 저장하는지
2. `render family:` hero-primer가 어느 DOM 층위에 삽입되는지
3. `highlight family:` `openingPlan` / `caution` hook을 어디서 소비하는지
4. `non-change line:` 다른 primer flag / battle·reward·shop·ending render path는 아직 untouched

### 허용되는 기록 예시
> `readMetaProgress` / `persistMetaProgress` semantic zone에서 `uiGuidance.seenBookSelectPrimer`만 다루고, 다른 primer seen flag는 이번 stage에서 추가하지 않는다.

> `renderGrimoireShelf` semantic zone에서는 section-head 아래 hero-primer 1장과 card 내부 openingPlan/caution highlight까지만 닫고 library shell 구조는 유지한다.

### 금지되는 기록 예시
- `온보딩 helper를 이번에 공용화할 수도 있음`
- `battle slot도 같이 열어 두면 다음 커밋 편함`
- `meta schema 전반 정리 예정`

---

## 4. DOM / hierarchy anchor

## 3줄 요약
- UI/아트는 B-1에서 primer 하나를 더 예쁘게 만드는 것이 아니라, library surface가 여전히 library로 읽히는지 본다.
- semantic anchor는 결국 DOM hierarchy와 density에서 확인돼야 한다.
- 이 단계에서 필요한 건 새 shell이 아니라 `어디까지가 primer이고 어디부터가 기존 shelf인가`의 경계 확인이다.

### one-line goal
`section-head -> hero-primer -> body-copy -> legacy loadout -> grimoire grid` 위계를 그대로 유지한 채 hero-primer와 highlight 2곳만 library surface 안에 눌러 넣는다.

### UI/아트가 다시 확인할 DOM zone
| DOM zone | B-1에서 묻는 질문 | pass 기준 | fail 예시 |
| --- | --- | --- | --- |
| `.section-head` 주변 | primer가 여전히 library intro의 일부처럼 보이는가 | primer가 머리말 바로 아래에서 읽힌다 | 별도 modal/headline처럼 떠 있다 |
| `.hero-primer` 또는 동등 block | primer가 한 화면 안에 읽히되 grid를 먹지 않는가 | title + 2 lines + CTA가 compact하게 보인다 | primer가 카드 첫 줄을 과도하게 밀어낸다 |
| `.body-copy` / `renderLegacyLoadoutPanel()` 사이 | primer와 기존 설명이 충돌하지 않는가 | primer 뒤 body-copy와 loadout panel이 연속적으로 읽힌다 | primer 때문에 loadout panel이 첫 스크린 밖으로 밀린다 |
| `.grimoire-card` 내부 `openingPlan` / `caution` | 강조가 guide로 읽히는가 | 두 줄만 subtle하게 올라온다 | 카드 전체가 추천/정답 책처럼 읽힌다 |
| `choose` CTA | primer가 선택 행동을 막지 않는가 | 버튼이 바로 보이고 눌린다 | primer dismiss가 선택보다 먼저 강제된다 |

### role-specific handoff points
- UI는 hierarchy note를 `무엇이 먼저 읽히는가` 중심으로 남긴다.
- 아트는 `primer-library` token이 안내로 읽히는지만 확인한다.
- 둘 다 shelf 전체 refresh 제안은 이번 stage에서 보류한다.

---

## 5. capture anchor

## 3줄 요약
- B-1 캡처는 시안 모음이 아니라 semantic anchor의 결과를 증명하는 두 장 세트다.
- first-open은 `무엇부터 읽는가`, return-state는 `재노출 정책이 library-only로 유지되는가`를 답해야 한다.
- 이 두 질문이 빠지면 review-ready가 아니라 그냥 예쁜 화면 모음이다.

### one-line goal
first-open / return-state를 `library-only semantic question` 두 개로 고정한다.

| capture | 답해야 하는 질문 | 반드시 같이 적을 note | 금지 혼입 |
| --- | --- | --- | --- |
| `captures/YYYYMMDD-b1-library-first-open.png` | 첫 진입에서 hero-primer, body-copy, grid 첫 줄, CTA가 한 화면 안에 읽히는가 | UI hierarchy note + QA acceptance log | battle/reward surface, generic `final` naming |
| `captures/YYYYMMDD-b1-library-return-state.png` | 재진입 또는 동등 정책에서 primer 재노출 제어와 highlight/CTA 유지가 library-only로 보이는가 | QA acceptance log + PM boundary note | 다른 primer surface, overrun/ending 화면 |

### QA note에 꼭 들어갈 문장
> first-open / return-state 캡처는 모두 library surface 안에서만 기록하며, battle/reward/shop/ending evidence로 재사용하지 않는다.

---

## 6. token sanity anchor

## 3줄 요약
- B-1 아트 sanity의 semantic anchor는 `primer-library`, `library-gold`, `openingPlan/caution` highlight뿐이다.
- token sanity는 새 자산 발주 여부가 아니라 현재 stage를 닫아도 되는 최소 충분성 verdict여야 한다.
- library primer와 다른 surface primer family를 한꺼번에 맞추기 시작하면 stage drift다.

### one-line goal
B-1 token sanity를 `library surface 안내성 유지`라는 단일 질문으로 제한한다.

### 아트 note에 적을 최소 구조
1. `이번 sanity는 primer-library / library-gold / openingPlan-caution highlight만 확인한다.`
2. `verdict: 충분 / 약함 / 과함`
3. `이번 stage 비범위: battle/reward/shop/ending tone, 신규 key art, 추천 ribbon family`

### 금지되는 확장
- `battle primer도 같은 badge family로 맞추자`
- `reward/shop도 이 기회에 같은 ribbon을 쓰자`
- `도서관 전체 key art를 먼저 다시 열자`

---

## 7. review-ready evidence rule

## 3줄 요약
- semantic anchor 재확인은 review-ready의 선행 조건이지 green 자체가 아니다.
- review-ready는 `locator 메모 + library-only diff + first-open/return-state 질문 + boundary note`가 같은 문장을 쓸 때 성립한다.
- locator가 있어도 wording이 generic하거나 다른 surface가 섞이면 아직 bootstrap/in-progress다.

### review-ready로 볼 수 있는 경우
- programmer state diff note가 `readMetaProgress` / `persistMetaProgress` / `renderGrimoireShelf` / `choose-grimoire` semantic family를 명시한다.
- UI hierarchy note가 `section-head -> hero-primer -> body-copy -> grid` 위계를 적고 CTA 비차단을 함께 판정한다.
- QA acceptance log가 first-open / return-state 두 질문을 `library-only` 범위로 기록한다.
- PM boundary note가 `battle/reward/shop/ending 미오픈`, `green 전 B-2 unlock 금지`를 유지한다.

### 아직 review-ready가 아닌 경우
- line number나 grep 결과만 있고 semantic family 설명이 없다.
- state diff note가 다른 seen flag 예열을 암시한다.
- capture가 있어도 어떤 semantic question을 답하는지 note에 연결돼 있지 않다.
- hierarchy note가 shelf refresh나 다른 surface family 제안으로 커진다.

### 한 줄 판정
B-1 semantic anchor packet의 목적은 **무엇을 찾았는지**보다 **찾고도 범위를 넓히지 않았는지**를 증명하는 것이다.

---

## 8. stop / go 판정

### GO 조건
- archive/stub가 이미 생성돼 있다.
- programmer note가 `seenBookSelectPrimer`와 library render hook만 다룬다고 적는다.
- UI/QA가 first-open / return-state 두 질문을 library-only로 기록한다.
- PM boundary note가 아직 `B-2 unlock 대기`를 유지한다.

### STOP 신호
- `renderBattle`, `renderReward`, `renderShop`, `renderEnding` locator까지 같이 열기 시작한다.
- 다른 seen flag 이름이 state diff note에 들어간다.
- UI note가 shelf 전체 redesign이나 추천 책 판단으로 넘어간다.
- QA capture가 다른 surface와 섞인다.

### STOP 이후 다음 행동
1. semantic anchor note부터 `library-only`로 되돌린다.
2. 이번 turn에서 어떤 locator가 과하게 열렸는지 boundary note에 적는다.
3. 필요하면 이번 stage는 `review-pending`으로 남기고 B-2를 열지 않는다.

---

## 9. 역할별 handoff 포인트

### 프로그래머
- semantic anchor note는 code diff보다 먼저 남긴다.
- `state family / render family / highlight family / CTA path / non-change line` 다섯 칸이면 충분하다.
- line drift를 핑계로 helper 공용화나 다른 primer slot까지 열면 B-1 범위를 벗어난다.

### UI
- hierarchy note는 `읽힘`과 `비차단`만 본다.
- shelf 리프레시는 다음 surface/후속 backlog다.
- first-open / return-state 질문을 note에 먼저 써 두고 캡처를 채운다.

### 아트
- token sanity는 primer-library 세트에 한정한다.
- 신규 대형 자산은 이번 stage pass 조건이 아니다.
- `추천 책`처럼 읽히지 않는다는 문장을 남기는 쪽이 더 중요하다.

### QA
- first-open / return-state 질문을 같은 로그 안에 묶는다.
- `library-only boundary 유지`가 빠지면 pass를 미룬다.
- 다른 surface evidence가 섞인 캡처는 다시 찍게 하는 편이 맞다.

### PM/기획
- semantic anchor 재확인은 kickoff 메모 층위다.
- green과 unlock 문장은 evidence 정렬 뒤에만 쓴다.
- `온보딩 진행`, `library/battle 초안` 같은 큰 문장은 계속 금지한다.

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| line drift 때문에 implementer가 stage까지 넓혀 grep하는 위험 | B-1이 다시 battle/reward/shop/ending을 건드리는 커밋으로 부푼다 | semantic family를 code/DOM/evidence 질문 단위로 고정 | B-1 첫 live edit 직전 |
| state diff note가 다른 seen flag 예열로 커지는 위험 | library-only recovery 범위가 흐려진다 | meta state anchor를 `seenBookSelectPrimer` 하나로 못 박음 | programmer review-ready 제출 시 |
| first-open / return-state 캡처가 generic shot으로 흐르는 위험 | QA pass가 visual 참고 수준으로 약해진다 | capture anchor를 질문 기반 2장 세트로 고정 | QA pass 직전 |
| token sanity가 다른 surface tone 정렬 논의로 커지는 위험 | B-1이 asset backlog 회의로 변한다 | primer-library 세트만 B-1 semantic zone으로 한정 | UI/아트 sanity review 시 |
| PM이 semantic anchor 재확인을 green처럼 기록하는 위험 | half-cutover가 완료처럼 보인다 | semantic anchor는 kickoff layer, green은 evidence layer로 분리 | tracker/run log 작성 시 |

---

## 11. 즉시 다음 액션
1. `Commit 2 green` 뒤 B-1을 열 때 `readiness board -> B-1 live kickoff packet -> B-1 artifact stub packet -> 이 semantic anchor packet` 순서로 읽는다.
2. programmer state diff note에 `state/render/highlight/CTA/non-change` 다섯 줄을 먼저 적는다.
3. UI hierarchy note에 first-open / return-state 질문을 먼저 적는다.
4. 그 뒤에만 library-only edit와 evidence drop을 시작한다.
5. review-ready / QA pass / PM green / B-2 unlock은 여전히 이 semantic anchor 메모 뒤의 evidence bundle로만 닫는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- `docs/dicespell_first_run_onboarding_content_deck_kr.md`
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
