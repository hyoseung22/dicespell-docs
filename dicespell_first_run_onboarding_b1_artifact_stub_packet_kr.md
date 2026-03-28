# DiceSpell 첫 런 온보딩 `B-1 library` artifact stub packet

## 3줄 요약
- 이 문서는 `B-1 live kickoff`, `required artifacts`, `review packet`까지로도 남아 있던 마지막 text-first 실행 공백인 **`좋아, archive는 만들었어. 그런데 B-1 첫 줄은 정확히 무엇으로 시작해야 하지?`**를 닫는 source-of-truth다.
- 핵심은 새 primer 설계를 더하는 것이 아니라, `manifest` 다음으로 가장 먼저 열리는 `acceptance log / programmer state diff / UI hierarchy / art token sanity / PM boundary`의 **starter sentence와 금지 문장**을 B-1 stage 언어로 고정하는 것이다.
- 목표는 다음 implementer와 reviewer가 빈 note/log 대신 같은 starter kit로 시작해, `B-1 library`가 다시 `온보딩 전체`, `추천 책`, `거의 완료` 같은 큰 문장으로 퍼지지 않게 만드는 것이다.

**한 줄 목표:** `B-1 library` 첫 archive를 **manifest 1개 + stub 5종 + capture reservation 2개**의 같은 stage 언어로 시작하게 만들어, 첫 온보딩 turn의 증거 바닥을 generic placeholder가 아닌 handoff-ready starter kit로 고정한다.

---

## 왜 이 문서가 필요한가
지금 B-1 library 축은 이미 많이 닫혀 있다.

- 무엇을 구현하는가: `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- 첫 10분 순서: `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- 무엇을 제출해야 하는가: `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- 언제 green인가: `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- archive/manifest 공통 규칙: `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`, `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`, `docs/dicespell_overrun_onboarding_artifact_stub_packet_kr.md`

그런데 실제 B-1 첫 live turn 직전에는 여전히 작은 실무 공백이 남아 있다.

1. 공통 stub 규칙은 있지만, **B-1 library stage-specific 첫 문장**은 한 장에 다시 압축돼 있지 않다.
2. `required artifacts 8종`은 정해졌지만, implementer가 실제 archive를 열면 가장 먼저 마주치는 건 여전히 `빈 log / 빈 note`다.
3. B-1은 특히 `온보딩`, `책 추천`, `다음은 battle도` 같은 큰 문장으로 쉽게 부풀기 때문에, 첫 줄부터 stage boundary를 못 박을 필요가 있다.
4. 첫 줄이 흔들리면 review-ready와 green 전에 다시 wording 정렬 회의가 생기고, library-only evidence가 감상형 note로 흐른다.

즉 이 문서는 새 artifact 종류를 더하는 문서가 아니라, **이미 합의된 B-1 artifact bundle을 실제 첫 3분 안에 채우는 starter sentence packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. stub set`, `3. programmer stub` | `B-1 live kickoff`, `B-1 required artifacts` | state diff 첫 줄부터 `seenBookSelectPrimer + library-only`를 못 박아야 한다 |
| UI | `2. stub set`, `4. UI/capture stub` | `screen packet`, `B-1 review packet` | hierarchy note는 예쁨 메모가 아니라 hero-primer / CTA 비차단 판정이어야 한다 |
| 아트 | `2. stub set`, `5. art sanity stub` | `content deck`, `visual asset packet` | 아트 note는 발주 요청서가 아니라 `충분 / 약함 / 과함` verdict note다 |
| QA | `2. stub set`, `6. QA acceptance stub` | `acceptance matrix`, `B-1 required artifacts` | acceptance log는 B1-A1~A5 / recovery 3종 / library-only boundary만 기록한다 |
| PM/기획 | `0. first screen 결론`, `7. PM boundary stub`, `8. 금지 패턴` | `readiness board`, `B-1 review packet` | boundary note 첫 줄부터 `B-2 unlock 대기`를 적어 half-cutover 과장을 막는다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `B-1 library` archive를 실제로 열면 note/log 첫 줄은 무엇으로 시작해야 하지?
- 어떤 stub가 있어야 implementer와 reviewer가 같은 stage 문장으로 시작하지?
- 언제 `archive-in-progress`이고, 언제 `review-ready` 후보로 볼 수 있지?

### 가장 짧은 답
- B-1 archive는 `manifest`만으로 시작하지 않는다.
- 최소한 아래 **stub 5종**이 stage-specific 문장으로 함께 열려 있어야 한다.
  1. QA acceptance log
  2. programmer state diff note
  3. UI hierarchy note
  4. art token sanity note
  5. PM boundary note
- 두 장의 capture는 파일이 아직 없어도 **예약 문장**이 note 안에 먼저 있어야 한다.
- `TODO`, `나중에`, `거의 완료`, `온보딩 진행`, `battle도 곧` 같은 문장이 보이면 아직 bootstrap도 덜 끝난 상태다.

### 한 줄 판정 규칙
B-1 starter bundle은 **파일 존재**보다 먼저 **첫 줄이 library-only stage boundary를 말하는가**로 본다.

---

## 1. core structure

## 3줄 요약
- B-1 starter kit은 `공통 manifest` 위에 `B-1 stub 5종`을 덧씌우는 구조다.
- 각 stub는 `이번 stage에 포함되는 것 1줄`, `이번 stage에서 하지 않는 것 1줄`, `다음 증거/리뷰 조건 1줄`을 갖는다.
- 이 문서의 목적은 B-1 첫 archive를 빈칸 없는 stage contract 초안으로 여는 것이다.

### 한 줄 목표
`B-1 library` 첫 archive를 “나중에 채울 빈 폴더”가 아니라 “이미 stage boundary가 적혀 있는 handoff 초안”으로 만든다.

### B-1 canonical starter kit
- `docs/artifacts/templates/stage_manifest_b1_library.md`
- `docs/artifacts/stubs/stage_stub_b1_acceptance_log.md`
- `docs/artifacts/stubs/stage_stub_b1_guidance_state.md`
- `docs/artifacts/stubs/stage_stub_b1_ui_hierarchy_note.md`
- `docs/artifacts/stubs/stage_stub_b1_art_token_sanity.md`
- `docs/artifacts/stubs/stage_stub_b1_boundary_note.md`

### capture reservation rule
실제 png가 아직 없더라도 아래 두 캡처는 stub 단계에서 먼저 예약한다.
- `captures/YYYYMMDD-b1-library-first-open.png`
- `captures/YYYYMMDD-b1-library-return-state.png`

---

## 2. stub set contract table

| stub | 무엇을 증명하는가 | 첫 줄 핵심 문장 | 반드시 같이 적을 것 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- | --- |
| QA acceptance log | B1-A1~A5 / recovery 3종 / CTA 비차단 | `이 로그는 B-1 library acceptance/recovery만 기록한다.` | 재현 경로, pass/fail, capture reservation | battle/reward/shop/ending 검수 계획 |
| programmer state diff | `seenBookSelectPrimer`와 library hook만 바뀌었는가 | `이번 diff는 B-1 library에서 seenBookSelectPrimer와 library hero-primer만 다룬다.` | non-change line, semantic anchor, render hook | 다른 seen flag 예열, schema 확장 메모 |
| UI hierarchy note | hero-primer / highlight / CTA 비차단 | `B-1 library hierarchy는 hero-primer 1장과 highlight 2곳까지만 본다.` | first-open / return-state capture 질문 | shelf 전체 리디자인 제안 |
| art token sanity note | badge/accent/highlight가 `첫 런 가이드`로 읽히는가 | `이번 sanity는 B-1 library primer token이 안내로 읽히는지만 확인한다.` | `충분 / 약함 / 과함` verdict, 비범위 | 신규 대형 아트 패키지 요청 |
| PM boundary note | library-only 경계와 B-2 unlock 대기 | `이번 note는 B-1 library의 비확장선과 unlock 대기만 기록한다.` | battle/reward/shop/ending 미오픈, green 전 unlock 금지 | `온보딩 진행`, `B-2도 사실상 ready` |

---

## 3. programmer stub

## 한 줄 목표
프로그래머 note는 `무엇을 바꿨는가`보다 먼저 `무엇을 아직 안 바꿨는가`를 적어 B-1 범위를 잠근다.

### starter sentence
> 이번 diff는 B-1 library에서 `seenBookSelectPrimer`, `library.book-select` hero-primer, `openingPlan/caution` highlight까지만 다루고 battle/reward/shop/ending primer는 아직 열지 않는다.

### 이어서 적을 3줄
1. semantic anchor: `readMetaProgress` / `persistMetaProgress` / `renderGrimoireShelf` / `openingPlan` / `caution`
2. changed line: `seenBookSelectPrimer 추가 여부`, `hero-primer 주입`, `highlight 2곳`
3. non-change line: `seenBattlePrimer`, `seenRewardPrimer`, `seenShopPrimer`, `seenVictoryPrimer`, `seenOverrunCtaPrimer` 미추가

### 금지 문장
- `battle도 곧 붙일 예정`
- `helper를 공용화할 수도 있음`
- `온보딩 전반 schema 정리`

---

## 4. UI hierarchy + capture stub

## 한 줄 목표
UI note는 `어디가 더 예쁜가`가 아니라 `무엇이 먼저 읽히고 CTA를 가리지 않는가`를 판정한다.

### starter sentence
> B-1 library hierarchy는 shelf 선택을 막지 않는 hero-primer 1장과 `openingPlan/caution` highlight 2곳까지만 확인한다.

### first-open capture reservation
> 예약 capture: `captures/YYYYMMDD-b1-library-first-open.png`는 첫 진입 시 hero-primer, body-copy, 카드 그리드 첫 줄, CTA가 한 화면 안에 함께 읽히는지 증명한다.

### return-state capture reservation
> 예약 capture: `captures/YYYYMMDD-b1-library-return-state.png`는 재진입 또는 동등 정책에서 primer 재노출 제어와 highlight/CTA 유지가 library-only로 읽히는지 증명한다.

### 금지 문장
- `책 선택 화면 전체 리프레시 필요`
- `추천 책처럼 보이지만 더 강해도 됨`
- `battle primer 톤도 같이 맞추자`

---

## 5. art token sanity stub

## 한 줄 목표
아트 note는 발주 backlog가 아니라 지금 stage를 닫게 해 주는 최소 충분성 verdict다.

### starter sentence
> 이번 sanity는 B-1 library primer token이 `첫 런 가이드`와 `안내`로 읽히는지, 그리고 `추천 책`이나 `정답 선택`처럼 오독되지 않는지만 확인한다.

### 필수 구조
- verdict: `충분 / 약함 / 과함`
- 확인 범위: badge / accent / highlight
- 비범위: 신규 key art, 배너 패밀리 확장, battle/reward 톤 확장

### 금지 문장
- `도서관 전체 key art 재작업 필요`
- `책별 배너 제작부터 선행`
- `reward/shop도 같은 리본으로 통일`

---

## 6. QA acceptance stub

## 한 줄 목표
QA log는 B-1 stage를 닫는 테스트 언어만 남기고 미래 surface 검수를 섞지 않는다.

### starter sentence
> 이 로그는 B-1 library acceptance/recovery만 기록하며 B1-A1~A5, recovery 3종, CTA 비차단, library-only boundary 유지 여부만 다룬다.

### 필수 구조
- 재현 경로
- pass/fail
- first-open capture 연결
- return-state capture 연결
- boundary 확인 한 줄

### 금지 문장
- `battle primer도 문제없어 보임`
- `reward/shop도 같은 helper면 될 듯`
- `온보딩 전반 pass`

---

## 7. PM boundary stub

## 한 줄 목표
PM note는 green 문장을 미리 쓰지 않고 `아직 열지 않은 것`과 `다음 unlock 조건`을 먼저 잠근다.

### starter sentence
> 이번 note는 B-1 library의 비확장선과 signoff 대기만 기록하며, battle/reward/shop/ending primer는 아직 미오픈 상태로 유지한다.

### 이어서 적을 2줄
1. `B-1 green 전에는 B-2 unlock 문장을 쓰지 않는다.`
2. `다음 단계는 QA pass와 PM green 뒤에만 B-2 battle로 한정한다.`

### 금지 문장
- `온보딩 진행`
- `첫 런 가이드 적용 완료`
- `library/battle 초안 완료`

---

## 8. 금지 패턴

1. B-1 archive를 manifest만 만들고 note/log는 빈 파일로 두는 것
2. stub 첫 줄부터 `온보딩`, `튜토리얼`, `추천 책` 같은 큰 문장으로 시작하는 것
3. capture 예약 없이 나중에 `final.png`를 임시로 떨구는 것
4. state diff note에 다른 seen flag 예열을 같이 적는 것
5. art sanity note를 신규 자산 발주 메모로 쓰는 것
6. PM boundary note에서 green 전에 unlock을 먼저 쓰는 것

---

## 역할별 handoff 포인트

### 프로그래머
- `state diff`는 existing stub를 그대로 복사한 뒤 semantic anchor와 non-change line부터 채운다.
- B-1에서 schema 논의가 길어지면 stage boundary가 흐려진다.

### UI
- hierarchy note는 capture 2장 질문과 함께 써야 한다.
- primer는 shelf를 돕는지, 밀어내는지만 본다.

### 아트
- art sanity는 `충분성 note`다.
- 신규 자산 필요는 후속 backlog로만 남긴다.

### QA
- acceptance log는 fixture 전체가 아니라 B1-A1~A5 / recovery 3종만 남긴다.
- wording drift가 있으면 파일이 다 있어도 review-ready를 미룬다.

### PM/기획
- boundary note는 가장 먼저 `미오픈`을 적는다.
- green/unlock 문장은 review와 QA pass 뒤에만 나온다.

---

## 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| B-1 archive가 다시 빈 note/log로 시작하는 위험 | 첫 live turn에서 wording drift와 boundary drift가 동시에 생김 | B-1 stage-specific stub 5종과 starter sentence를 고정 | B-1 첫 archive 생성 직후 |
| programmer note가 다른 seen flag 예열까지 적는 위험 | library-only diff가 흐려지고 B-2 경계가 약해짐 | state diff stub에 non-change line을 먼저 쓰게 고정 | 첫 code diff 리뷰 시 |
| UI/아트 note가 감상형 메모나 발주 요청서로 커지는 위험 | small primer turn이 polish/backlog 회의로 변질 | hierarchy / token sanity를 verdict note로 한정 | B-1 visual sanity 직전 |
| PM note가 green 전에 unlock을 암시하는 위험 | half-cutover가 완료처럼 보임 | boundary stub에 `green 전 unlock 금지` 문장을 고정 | tracker/run log 작성 시 |

---

## 즉시 다음 액션
1. `Commit 2 green` 뒤 B-1을 열 때 `readiness board -> B-1 live kickoff packet -> B-1 required artifacts packet -> 이 artifact stub packet` 순서로 읽는다.
2. `docs/artifacts/templates/stage_manifest_b1_library.md`와 stub 5종을 먼저 복사한다.
3. capture 파일이 없어도 first-open / return-state reservation 문장부터 채운다.
4. 그 뒤에만 library-only edit와 evidence drop을 시작한다.
5. QA pass와 PM green은 이 starter bundle이 실제 artifact bundle로 채워진 뒤에만 쓴다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_stub_packet_kr.md`
- `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
- `docs/dicespell_overrun_onboarding_filled_manifest_examples_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
