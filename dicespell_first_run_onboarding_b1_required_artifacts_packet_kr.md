# DiceSpell 첫 런 온보딩 `B-1 library` Required Artifacts 패킷

## 3줄 요약
- 이 문서는 `B-1 library packet`, `B-1 live kickoff packet`, `B-1 review packet`까지로도 남아 있던 마지막 실무 공백인 **`좋아, 그러면 B-1이 review-ready라고 말하려면 정확히 어떤 artifact가 어떤 문장으로 archive 안에 들어 있어야 하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 primer 설계를 더하는 것이 아니라, 첫 library turn의 evidence bundle을 **파일 이름, owner, 필수 문장, pass/fail 기준**까지 다시 좁혀 프로그래머 / UI / 아트 / QA / PM이 같은 제출물로 말하게 만드는 것이다.
- 첫 화면만 읽어도 `무엇이 있어야 QA가 pass를 쓸 수 있는지`, `무엇이 비어 있으면 아직 bootstrap/in-progress인지`, `왜 B-2 unlock 전에 exact library-only artifact bundle이 먼저 모여야 하는지`가 바로 보여야 한다.

## 한 줄 목표
`B-1 library` 첫 stage를 **required artifacts 8종 + artifact별 owner sentence + review-ready 판정선 1개**로 잠가, 첫 실제 온보딩 턴의 제출물 언어를 감상형 note가 아니라 handoff-ready bundle로 고정한다.

## 왜 이 문서가 필요한가
지금 첫 런 온보딩 B-track은 아래 문서까지 이미 닫혀 있다.

- `docs/dicespell_first_run_onboarding_handoff_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- `docs/dicespell_first_run_onboarding_content_deck_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_owner_workboard_kr.md`
- `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- B-1이 왜 `library-only` turn인가
- 첫 10분 kickoff를 어떤 순서로 열어야 하는가
- green 전 signoff 계층은 무엇인가
- battle/reward/shop/ending을 왜 아직 열면 안 되는가

하지만 실제 B-1 첫 turn 직전에는 여전히 다음 공백이 남아 있다.

1. kickoff와 review는 있어도, **그 사이를 메우는 exact required artifacts 목록**이 B-1 surface 언어로 한 장에 다시 보이지 않는다.
2. `acceptance log`, `state diff`, `hierarchy note`, `boundary note`가 필요하다는 사실만으로는 부족하다. 어떤 파일이 무엇을 증명해야 하는지와 무엇이 generic이면 아직 review-ready가 아닌지를 같은 표로 보고 싶어진다.
3. 프로그래머 / UI / 아트 / QA / PM이 note를 남길 때 wording이 조금씩 달라지면 `B-1 review-ready`가 있어도 QA pass 직전에 다시 설명 회의가 필요해진다.
4. 첫 B-1 turn에서 art token sanity와 capture 언어가 library가 아니라 `튜토리얼 전체`, `추천 책`, `다음은 battle도` 같은 큰 문장으로 흐르면 library-only boundary가 다시 흔들린다.

즉 지금 필요한 것은 새 primer가 아니라, **B-1 첫 stage evidence bundle을 field-level handoff 계약으로 다시 압축한 required artifacts packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. artifact contract table`, `3. note 작성 규칙` | `B-1 library packet`, `B-1 live kickoff packet` | state diff는 generic 변경 메모가 아니라 `seenBookSelectPrimer + hero-primer + highlight 2곳` boundary 언어를 써야 한다 |
| UI | `2. artifact contract table`, `4. capture verdict rule`, `6. 역할별 handoff` | `screen packet`, `B-1 review packet` | 캡처는 예쁜 장면이 아니라 primer hierarchy와 CTA 비차단을 증명해야 한다 |
| 아트 | `2. artifact contract table`, `5. art sanity rule` | `content deck`, `visual asset packet` | 이번 턴의 아트 제출물은 새 일러스트 발주가 아니라 badge/accent/highlight drift 없음 확인이다 |
| QA | `2. artifact contract table`, `7. review-ready rule`, `8. 리스크 & 후속과제` | `acceptance matrix`, `B-1 review packet` | artifact 8종이 같은 B-1 library 문장을 쓸 때만 QA pass 후보로 올린다 |
| PM/기획 | `0. first screen 결론`, `7. review-ready rule`, `9. immediate next actions` | `readiness board`, `execution reporting packet` | B-1 green은 artifact completeness를 보고 쓰는 문장이지 kickoff 메모의 연장이 아니다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `B-1 library review-ready`를 말하려면 어떤 artifact가 반드시 있어야 하지?
- `어떤 note/capture는 아직 bootstrap이고 어떤 순간부터 handoff-ready라고 말할 수 있지?`
- `B-2 unlock 전 마지막으로 봐야 하는 제출물은 무엇이지?`

### 가장 짧은 답
- B-1 첫 stage는 **artifact 8종이 모두 있고 같은 `library-only` 문장을 쓸 때만** review-ready 후보로 본다.
- log 1개, note 4개, capture 2장, manifest 1장이 다 필요하다.
- 각 artifact는 `B-1 library`, `seenBookSelectPrimer`, `library.book-select`, `openingPlan/caution`, `battle/reward/shop/ending 미오픈` 언어를 유지해야 한다.
- 그중 하나라도 generic summary나 다른 surface 언어로 흐르면 아직 `archive-in-progress`다.

### 한 줄 판정 규칙
B-1 첫 stage의 closing은 **파일 개수**가 아니라 **artifact 간 문장 정렬까지 포함한 bundle completeness**로 판단한다.

---

## 1. core structure

## 3줄 요약
- B-1 required artifacts는 `상태`, `검수`, `변경 근거`, `시각 증거`, `아트 sanity`, `경계 문장` 6축으로 나뉜다.
- 각 artifact는 `무엇을 증명하는가`, `누가 채우는가`, `반드시 들어가야 하는 핵심 문장`, `무엇을 쓰면 안 되는가`가 따로 있다.
- 이 문서의 역할은 B-1 kickoff/review packet의 `무엇을 닫을까`를 실제 제출물 수준의 `어떻게 써서 넘길까`로 바꾸는 것이다.

### 한 줄 목표
artifact를 `파일 존재 여부`가 아니라 `같은 B-1 library stage 문장을 쓰는 제출물`로 만든다.

### B-1 canonical artifact set
- `manifest.md`
- `logs/YYYYMMDD-b1-library-qa-acceptance-log.md`
- `notes/YYYYMMDD-b1-library-programmer-state-diff.md`
- `notes/YYYYMMDD-b1-library-ui-hierarchy-note.md`
- `notes/YYYYMMDD-b1-library-art-token-sanity.md`
- `notes/YYYYMMDD-b1-library-pm-boundary-note.md`
- `captures/YYYYMMDD-b1-library-first-open.png`
- `captures/YYYYMMDD-b1-library-return-state.png`

---

## 2. artifact contract table

## 3줄 요약
- 아래 표는 B-1 첫 stage에서 무엇을 제출해야 하는지보다, **각 파일이 무엇을 증명해야 하는지**를 먼저 고정한다.
- 프로그래머 / UI / 아트 / QA / PM은 이 표를 기준으로 artifact를 나눠 채우되, 최종 review-ready는 bundle completeness로만 판단한다.
- 파일 하나가 좋아도 다른 파일이 generic wording이면 B-1 green 후보가 아니다.

### 한 줄 목표
각 artifact를 `owner별 메모`가 아니라 `같은 B-1 계약의 다른 면`으로 읽게 만든다.

| 파일 | 역할 | 주요 작성자 | 반드시 들어가야 하는 핵심 문장/내용 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- | --- |
| `manifest.md` | stage 상태/경계/다음 unlock 기준 | 프로그래머 + PM | `현재 stage는 B-1 library`, `이번 stage는 seenBookSelectPrimer + hero-primer + highlight 2곳까지만 닫음`, `next allowed step: review-ready 후 QA pass 대기` | `온보딩 진행`, `B-2도 사실상 ready`, `reward/shop도 곧 가능` |
| `logs/...qa-acceptance-log.md` | B1-A1~A5 / recovery 3종 / CTA 비차단 기록 | QA | `이 로그는 B-1 library acceptance/recovery만 기록한다.`, pass/fail와 재현 경로 | battle/reward/shop/ending 검수 선기록 |
| `notes/...programmer-state-diff.md` | state/render diff 경계 고정 | 프로그래머 | `seenBookSelectPrimer` 추가 여부, 다른 seen flag 미추가, `renderGrimoireShelf()` / `openingPlan` / `caution` hook 범위 | battle primer slot 예열, schema 확장 계획 |
| `notes/...ui-hierarchy-note.md` | hero-primer 밀도와 CTA 비차단 sanity | UI | `section-head -> hero-primer -> body-copy -> grid` 또는 동등 구조, CTA 비가림 여부, highlight 2곳 판정 | shelf 전체 리디자인 제안, reward/shop 톤 메모 |
| `notes/...art-token-sanity.md` | badge/accent/highlight drift 없음 확인 | 아트 | `primer-library`, `첫 런 가이드`, `openingPlan/caution` highlight가 `안내`로 읽힌다는 verdict | 신규 대형 아트 패키지 발주, 추천 책 메달 제안 |
| `notes/...pm-boundary-note.md` | library-only 경계와 unlock 대기 고정 | PM | `battle/reward/shop/ending 미오픈`, `B-2 unlock은 B-1 green 뒤`, 현재 stage와 다음 stage 분리 | `온보딩 전체 착수`, `library/battle 동시 진행` |
| `captures/...first-open.png` | 첫 진입 primer on 상태 증명 | UI + QA | hero-primer가 첫 스크린 안에서 읽히고 CTA를 가리지 않는 장면 | modal처럼 가려진 장면, generic `final.png` |
| `captures/...return-state.png` | 재진입/재노출 정책 또는 동등 정책 증명 | UI + QA | primer off 또는 동등 정책, highlight/CTA 유지 확인 | battle/reward surface가 같이 보이는 캡처 |

---

## 3. note 작성 규칙

## 3줄 요약
- B-1 첫 stage note는 다 짧아야 한다. 길게 쓰는 순간 stage boundary보다 감상과 설계 재논의가 커진다.
- 공통 원칙은 `이번 stage가 무엇을 증명했는지 1줄`, `무엇은 아직 금지인지 1줄`, `다음 unlock 조건 1줄`이다.
- note마다 어휘가 달라지면 review-ready는 보류한다.

### 한 줄 목표
모든 note가 `B-1 library` 같은 문장 규칙을 공유하게 만든다.

### note 공통 문장 규칙
1. 첫 줄에는 stage명을 넣는다. 예: `B-1 library stage에서는 ...`
2. 둘째 줄에는 이번 stage 포함 범위를 넣는다. 예: `seenBookSelectPrimer와 hero-primer 1장, openingPlan/caution highlight까지만 닫았다.`
3. 셋째 줄에는 비범위/금지 범위를 넣는다. 예: `battle/reward/shop/ending primer와 추가 seen flag는 이번 stage 바깥이다.`
4. 마지막 줄에는 review-ready 또는 green 조건을 넣는다.

### 금지되는 note 습관
- `대충 괜찮음`
- `거의 다 됨`
- `온보딩도 이제 진행`
- `battle도 준비될 듯`
- `추천 책처럼 보여도 괜찮음`

### 권장되는 짧은 문장 패턴
- `이번 stage는 B-1 library primer와 highlight 2곳까지만 닫는다.`
- `현재 artifact는 B-1 review용이며 B-2 unlock 근거로만 사용한다.`
- `reward/shop/ending primer와 새 primer flag는 이번 stage 비범위다.`

---

## 4. capture verdict rule

## 3줄 요약
- B-1 캡처 2장은 시각 참고자료가 아니라 `library first-open / return-state pass/fail` 증거다.
- 두 장 모두 있어야 하고, 각각 다른 질문에 답해야 한다.
- 한 장이라도 generic glamour shot이면 다시 in-progress다.

### 한 줄 목표
B-1 캡처를 `예쁜 샷`이 아니라 `다른 질문을 답하는 2장 세트`로 고정한다.

| 캡처 | 반드시 답해야 하는 질문 | pass 조건 | fail 예시 |
| --- | --- | --- | --- |
| `...first-open.png` | `첫 library 진입에서 무엇부터 읽어야 하는가가 한 화면 안에 보이는가?` | hero-primer, body-copy, grid 첫 줄, CTA가 함께 읽힌다 | primer가 카드 줄을 과도하게 밀거나 CTA를 가린다 |
| `...return-state.png` | `재진입 또는 동등 정책에서 primer 재노출 제어가 library-only로 유지되는가?` | primer off 또는 동등 정책이 보이고 highlight/CTA는 남는다 | primer 정책이 모호하거나 battle/reward 화면까지 같이 찍힌다 |

### 캡처 메모 규칙
- hierarchy note에는 각 캡처를 1~2줄씩 직접 참조한다.
- PM boundary note에는 캡처 파일명을 직접 적지 않아도 되지만, `first-open / return-state 둘 다 존재`는 적는다.
- 캡처 파일명은 `b1-library`와 상태를 포함한 canonical naming만 쓴다.

---

## 5. art sanity rule

## 3줄 요약
- `art-token-sanity.md`는 아트 backlog 문서가 아니라 B-1 첫 stage의 **작은 충분성 증명**이다.
- 이 note는 무엇이 모자란지보다, 지금 stage에서 어디까지면 충분한지와 무엇을 일부러 안 하는지를 함께 적어야 한다.
- 큰 리뉴얼 제안은 이 파일에 적지 않는다.

### 한 줄 목표
`primer-library`를 `지금 B-1 stage를 닫게 해 주는 최소 차이`로만 본다.

### 반드시 들어가야 하는 항목
- 현재 stage에서 확인한 badge/accent/highlight 범위
- `충분 / 약함 / 과함` 중 하나의 verdict
- `이번 stage에서 아직 하지 않음` 항목 1~3개
- `추천 책`처럼 읽히지 않게 만드는 포인트 1줄

### 금지 항목
- `도서관 전체 key art 재작업 필요`
- `battle/reward도 같은 ribbon family로 가자`
- `책별 개별 배너 제작 필요`
- 대형 발주 일정이나 moodboard 확장

---

## 6. 역할별 handoff 포인트

### 프로그래머에게 넘길 것
- B-1 required artifacts의 중심은 `state diff`와 `manifest`다.
- 프로그래머 note가 battle/reward/shop/ending seen flag 계획까지 넘기기 시작하면 bundle이 흐려진다.
- 첫 줄은 항상 library-only stage boundary부터 적는다.

### UI에게 넘길 것
- first-open / return-state는 두 장 모두 필요하다.
- UI note는 `어디가 더 예쁜가`가 아니라 `무엇이 먼저 읽히는가`와 `CTA를 가리지 않는가`를 판정한다.
- hierarchy note는 4줄 내로 끝나는 편이 좋다.

### 아트에게 넘길 것
- `art-token-sanity.md`는 지금 발주할 큰 작업이 아니라 지금 stage를 닫게 해 주는 작은 판단 note다.
- library stage와 무관한 공통 style 제안은 다음 surface/후속 backlog로 미룬다.

### QA에게 넘길 것
- artifact 8종이 모두 있어도 wording drift가 있으면 pass를 미룬다.
- `review-ready`는 file existence 체크가 아니라 stage contract alignment 판정이다.

### PM/기획에게 넘길 것
- green 기록 전 마지막 확인 포인트는 artifact completeness와 `battle/reward/shop/ending 미오픈` 정렬이다.
- B-1 green은 `B-2 battle unlock 가능`만 의미한다. `온보딩 전체 green`이 아니다.

---

## 7. review-ready rule

## 3줄 요약
- B-1 review-ready는 `문서를 읽었다`가 아니라 `artifact 8종이 같은 stage 문장을 쓴다`는 뜻이다.
- 한 파일이라도 다른 surface를 미리 열거나 generic wording을 쓰면 아직 review-ready가 아니다.
- QA pass와 PM green은 이 review-ready bundle 위에서만 시작할 수 있다.

### review-ready로 볼 수 있는 경우
- artifact 8종이 모두 B-1 archive 안에 있다.
- `manifest`, `state diff`, `boundary note`, `acceptance log`가 모두 `B-1 library`와 `library-only` 문장을 쓴다.
- first-open / return-state 두 캡처가 모두 존재하고 hierarchy note가 두 장을 근거로 판정한다.
- art token sanity가 `충분 / 약함 / 과함` 중 하나로 끝나고 추천 책/대형 자산 논의로 흐르지 않는다.

### 아직 review-ready가 아닌 경우
- 파일은 다 있는데 wording이 generic하다.
- acceptance log는 있는데 state diff note가 다른 seen flag 확장을 암시한다.
- 캡처는 있는데 `return-state`가 빠져 재노출 정책이 안 보인다.
- PM boundary note가 `다음은 battle`이 아니라 `온보딩 진행` 같은 큰 문장을 쓴다.

### 한 줄 판정
B-1 첫 stage는 **artifact completeness + artifact alignment**가 둘 다 있을 때만 review-ready다.

---

## 8. 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 | 다음 handoff |
| --- | --- | --- | --- |
| artifact는 다 있는데 wording이 generic한 위험 | review-ready와 green이 다시 감각적으로 기록된다 | required artifacts 8종에 필수 문장/금지 문장을 붙였다 | 첫 B-1 archive 작성 직전 |
| capture가 1장뿐이라 재노출 정책이 흐려지는 위험 | B1-R1~R3 recovery가 screenshot 없이 말로만 남는다 | `first-open / return-state` 2장 세트를 required로 고정했다 | QA pass 직전 |
| art sanity가 asset backlog로 부푸는 위험 | 작은 primer turn이 art blocker가 된다 | `art-token-sanity.md`를 충분성 note로 제한했다 | UI/아트 sanity review 시 |
| PM boundary note가 큰 문장으로 흐르는 위험 | B-1과 B-2 기록이 다시 섞인다 | `B-2 unlock 가능`과 `온보딩 진행`을 분리했다 | tracker/run log 기록 시 |
| programmer note가 다른 seen flag 예열까지 적는 위험 | B-1 surface 경계가 무너진다 | state diff note에 미추가 필드를 필수로 적게 했다 | 첫 code diff handoff 시 |

---

## 9. immediate next actions
1. B-1 첫 live turn이 열리면 `readiness board -> B-1 live kickoff packet -> 이 required artifacts packet -> B-1 review packet` 순서로 읽는다.
2. archive 생성 직후 artifact 8종 canonical file set부터 만든다.
3. `manifest`, `state diff`, `boundary note`, `acceptance log`의 첫 줄이 모두 `B-1 library`와 `library-only`를 가리키는지 먼저 확인한다.
4. first-open / return-state 캡처와 hierarchy note를 같이 채운다.
5. QA는 wording alignment까지 본 뒤에만 `B-1 QA pass`를 적고, PM은 그 뒤에만 `B-1 library green`과 `다음 단계는 B-2 battle만 연다`를 기록한다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **`B-1 evidence가 있다`를 `B-1 artifact 8종이 같은 library-only 문장으로 정렬됐다`로 바꾸는 것**이다. 다음 온보딩 turn은 이 기준으로 artifact 8종을 채우고, 그 뒤에만 QA pass와 B-2 unlock을 말해야 한다.
