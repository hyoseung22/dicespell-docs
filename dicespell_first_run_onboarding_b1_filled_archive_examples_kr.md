# DiceSpell 첫 런 온보딩 `B-1 library` 채워진 archive 예시집

## 3줄 요약
- 이 문서는 `B-1 live kickoff`, `artifact stub`, `manifest`, `semantic anchor`, `required artifacts`, `archive bootstrap`, `review packet`이 정한 계약을 **실제로 채워진 archive bundle 예시**까지 내려 주는 source-of-truth다.
- 핵심은 새 primer 규칙을 더 만드는 것이 아니라, `docs/artifacts/examples/b1-library/` 안 8종 artifact가 **어느 문장 밀도와 어느 boundary 언어**로 채워져야 review-ready / QA pass / PM green이 다시 섞이지 않는지 보여 주는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `starter sentence는 썼는데 아직 review-ready인가?`, `어느 줄까지 tracker/run log에 그대로 재사용해도 과장되지 않는가?`, `무슨 wording이면 아직 archive-in-progress에 머물러야 하는가?`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`B-1 library` starter bundle을 **복붙 가능한 filled archive exemplar**까지 내려, 첫 실전 archive에서도 같은 library-only 문장과 같은 handoff line을 망설임 없이 재사용하게 만든다.

---

## 왜 이 문서가 필요한가
지금 B-1 문서 스택은 이미 두껍다.

- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`

즉 아래 질문은 이미 닫혀 있다.

- archive를 어떤 순서로 열 것인가
- starter note와 manifest 첫 줄은 무엇으로 시작해야 하는가
- 어떤 함수/DOM/캡처를 같은 책임으로 다시 찾아야 하는가
- review-ready / QA pass / PM green / B-2 unlock은 어떤 순서로 말해야 하는가

그런데 실제 첫 live turn 직전에는 마지막 작은 공백이 남는다.

1. 규칙은 충분하지만, **8종 artifact가 실제로 함께 채워진 완성본**이 없으면 owner마다 문장 밀도가 다시 흔들린다.
2. `manifest`, `acceptance log`, `state diff`, `hierarchy note`, `art token sanity`, `boundary note`, `first-open`, `return-state`가 모두 필요하다는 사실만으로는 부족하다. 어떤 줄은 tracker/run log에 그대로 재사용해도 되고, 어떤 줄은 아직 review-ready 전에 쓰면 안 되는지 예시가 필요하다.
3. 특히 B-1은 첫 stage라서 `좋아, 이 정도면 됐겠지`라는 감각이 가장 쉽게 끼어든다. filled exemplar가 없으면 `온보딩 진행`, `추천 책`, `battle도 곧` 같은 큰 문장이 다시 섞인다.
4. archive bootstrap packet이 starter bundle을 고정했다면, 그 다음 단계의 마지막 공백은 **그 starter bundle이 실제로 어떤 충분성으로 채워져야 honest하게 `archive-in-progress` / `review-ready candidate` / `green-ready`를 말할 수 있느냐**다.

즉 지금 필요한 것은 새 packet이 아니라, **B-1 archive bundle 전체를 한 번 끝까지 채워 보여 주는 exemplar deck**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 것 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. exemplar bundle`, `3. manifest / state diff 예시` | `manifest packet`, `semantic anchor packet` | state diff와 handoff line은 `seenBookSelectPrimer + hero-primer + openingPlan/caution + other surfaces locked`를 같은 문장으로 말해야 한다 |
| UI | `2. exemplar bundle`, `4. hierarchy / capture 예시` | `screen packet`, `review packet` | capture 2장은 예쁜 스크린샷이 아니라 first-open / return-state 서로 다른 질문의 증거여야 한다 |
| 아트 | `2. exemplar bundle`, `5. art sanity / boundary 예시` | `content deck`, `artifact stub packet` | 아트 note는 큰 자산 요구가 아니라 `primer-library`가 추천/정답처럼 읽히지 않는다는 충분성 판단으로 끝나야 한다 |
| QA | `2. exemplar bundle`, `6. acceptance log 예시`, `8. 금지 문장` | `required artifacts packet`, `review packet` | artifact 8종이 같은 B-1 문장을 쓸 때만 review-ready 후보로 보고, 하나라도 generic이면 archive-in-progress로 내려야 한다 |
| PM/기획 | `1. first screen 판정`, `7. handoff line 재사용 규칙`, `9. 리스크 & 후속과제` | `readiness board`, `execution reporting packet` | tracker/run log/portal 문장은 exemplar의 `handoff line`과 `next allowed step`에 가깝게 재사용해야 안전하다 |

---

## 1. first screen 판정

### 이 문서를 왜 열어야 하나
- **프로그래머**: starter sentence를 다 썼는데도 `이게 review-ready candidate인지, 아직 archive-in-progress인지`를 실제 완성본과 비교하려고 연다.
- **UI/아트**: hierarchy note / token sanity note가 감상문으로 커지지 않았는지, 같은 stage boundary를 말하고 있는지 확인하려고 연다.
- **QA/PM**: artifact가 다 있어도 wording drift가 있으면 왜 pass나 green을 쓰면 안 되는지, 그 판단 기준을 복붙 가능한 문장으로 확인하려고 연다.

### 가장 짧은 답
- B-1 filled archive는 `manifest 1 + log 1 + note 4 + capture 2`가 **같은 library-only 문장을 공유하는 상태**를 보여 주는 exemplar다.
- `archive-ready`라는 단어보다 중요한 것은 각 artifact가 `B-1 library`, `seenBookSelectPrimer`, `library.book-select`, `openingPlan/caution`, `battle/reward/shop/ending 미오픈`, `B-2 unlock 대기`를 서로 다른 방식으로 같은 뜻으로 반복한다는 점이다.
- 이 예시보다 느슨해지면 다시 stage drift가 커지고, 이 예시보다 더 큰 문장이 들어가면 B-1이 아니라 온보딩 전체 착수처럼 부풀기 쉽다.
- tracker/run log/git 문장은 가능하면 이 exemplar의 `handoff line`, `next allowed step`, `boundary`를 거의 그대로 재사용하는 편이 안전하다.

### 한 줄 판정 규칙
이 문서는 작성 스타일 가이드가 아니라, **B-1 first archive를 honest하게 닫는 최소 충분성 샘플**이다.

---

## 2. exemplar bundle 한눈에 보기

## 3줄 요약
- B-1 exemplar는 파일 하나가 좋아 보이는지가 아니라 **8종이 함께 같은 stage를 말하는지**를 본다.
- 아래 표는 실제 예시 파일과 그 역할, 재사용 가능한 핵심 줄, 아직 쓰면 안 되는 줄을 한 장으로 묶는다.
- review-ready / QA pass / PM green은 이 표를 통과한 뒤에만 다른 문서로 올라간다.

### 한 줄 목표
artifact를 `파일 존재`가 아니라 `같은 B-1 library 계약의 8개 면`으로 읽게 만든다.

| 예시 파일 | 무엇을 증명하나 | 바로 재사용 가능한 핵심 줄 | 아직 쓰면 안 되는 줄 |
| --- | --- | --- | --- |
| `stage_manifest_b1_library_example.md` | stage 상태와 다음 unlock 경계 | `다음 허용 단계는 review-ready 확인 뒤 B-2 battle뿐이다.` | `온보딩 진행`, `battle도 사실상 ready` |
| `stage_log_b1_library_acceptance_example.md` | B1-A1~A5 / recovery 3종 / CTA 비차단 | `이 로그는 B-1 library acceptance/recovery만 기록한다.` | `battle primer도 같이 확인` |
| `stage_note_b1_library_programmer_state_diff_example.md` | seen flag / render hook / non-change | `이번 stage는 seenBookSelectPrimer와 shelf hero-primer만 닫았고 다른 primer flag는 열지 않았다.` | `battle helper 구조도 미리 넣어 둠` |
| `stage_note_b1_library_ui_hierarchy_example.md` | 읽힘 순서와 CTA 비차단 | `section-head -> hero-primer -> body-copy -> grid 순서는 유지되고 CTA는 가려지지 않는다.` | `shelf 전체를 다시 정리해야 함` |
| `stage_note_b1_library_art_token_sanity_example.md` | token/accent/highlight 충분성 | `primer-library는 안내 표식으로 읽히며 추천/정답 책처럼 보이지 않는다.` | `도서관 전체 key art 재작업 필요` |
| `stage_note_b1_library_pm_boundary_example.md` | library-only 범위와 unlock 대기 | `battle/reward/shop/ending primer는 아직 미오픈이며 B-2 unlock은 B-1 green 뒤에만 연다.` | `온보딩 전체 시작`, `battle도 함께 진행` |
| `stage_capture_b1_library_first_open_example.md` | first-open 질문의 캡처 메타 | `hero-primer, body-copy, CTA가 한 화면 안에서 동시에 읽힌다.` | `예쁜 첫 화면`, `final shot` |
| `stage_capture_b1_library_return_state_example.md` | return-state 질문의 캡처 메타 | `재진입 시 primer는 재노출되지 않고 highlight/CTA만 남는다.` | `return도 괜찮음` |

---

## 3. manifest / programmer state diff 예시

## 3줄 요약
- B-1에서 가장 먼저 흔들리는 것은 manifest와 programmer state diff다.
- 둘 다 `무엇을 바꿨는가`보다 `무엇을 아직 안 열었는가`를 같은 밀도로 적어야 한다.
- 아래 예시는 surface 하나만 닫는 문장이 어떻게 생겨야 honest한지 보여 준다.

### 한 줄 목표
프로그래머 문장이 `library primer 적용` 같은 큰 표현으로 커지지 않게 만든다.

### manifest 발췌 예시
```md
# b1-library manifest

- stage: b1-library
- date: 2026-03-19
- status: review-ready-candidate
- archive path: docs/artifacts/20260319/b1-library/

## owner summary
- programmer: `seenBookSelectPrimer` 저장/복구와 `renderGrimoireShelf()` hero-primer 1면, `openingPlan` / `caution` highlight 정렬만 닫았다. battle/reward/shop/ending primer와 추가 seen flag는 열지 않았다.
- ui: first-open / return-state capture 2장을 회수했고 primer가 grid와 CTA를 가리지 않는 hierarchy sanity만 확인했다.
- art: `primer-library` / highlight token 충분성만 확인했고 추천/정답 책처럼 보이는 연출은 넣지 않았다.
- qa: B1-A1~A5와 recovery 3종, CTA 비차단 기록을 B-1 library stage로만 묶었다.
- pm: 이번 stage는 B-1 library green 후보이며 다음 허용 단계는 review-ready 확인 뒤 B-2 battle뿐이다.

## boundary / non-change
- battle.entry / battle.first-spell / battle.auto-pass는 이번 stage에서 열지 않는다.
- reward.first / shop.first / ending.defeat / ending.victory / ending.overrun-cta도 이번 stage 범위 밖이다.
- shelf 전체 리디자인, 추천 책 카피, grimoire balance 조정은 이번 stage non-change다.

## next allowed step
- artifact wording alignment와 review packet 확인 뒤에만 B-1 QA pass를 기록하고, 그 다음에만 B-2 battle을 연다.

## handoff line
> B-1 library review-ready candidate: docs/artifacts/20260319/b1-library/ manifest 기준 `seenBookSelectPrimer`, hero-primer, openingPlan/caution highlight, library-only evidence bundle을 묶었고 다른 surface primer는 아직 열지 않았다.
```

### programmer state diff 발췌 예시
```md
# B-1 library programmer state diff example

B-1 library stage에서는 `meta.uiGuidance.seenBookSelectPrimer` 저장/복구와 `renderGrimoireShelf()` hero-primer 1면만 닫았다.
이번 stage 포함 범위는 `library.book-select` primer, `openingPlan` / `caution` highlight 정렬, CTA 비차단성까지다.
`seenBattlePrimer`, `seenFirstSpellHint`, `seenAutoPassHint`, `seenRewardPrimer`, `seenShopPrimer`, `seenDefeatPrimer`, `seenVictoryPrimer`, `seenOverrunCtaPrimer`는 이번 stage에서 추가하지 않는다.
semantic locator는 `readMetaProgress`, `persistMetaProgress`, `renderGrimoireShelf`, `openingPlan`, `caution`, `data-action="choose-grimoire"` 축으로 다시 찾는다.
review-ready 전에는 battle/reward/shop/ending helper 구조 예열이나 공통 primer registry 확장을 이 파일에 적지 않는다.
```

### 왜 이 예시가 중요한가
| 포인트 | 이유 |
| --- | --- |
| owner summary가 `무엇을 안 열었는가`를 같은 비중으로 적음 | B-1이 온보딩 전체로 부푸는 것을 가장 싸게 막는다 |
| handoff line이 tracker/run log 원문 후보 수준으로 짧음 | 기록 drift를 줄인다 |
| state diff에 semantic locator를 같이 적음 | line drift가 stage drift로 번지는 것을 막는다 |
| `review-ready-candidate`라는 honest status 사용 | starter bundle과 green을 섞지 않는다 |

---

## 4. UI hierarchy / capture 예시

## 3줄 요약
- UI exemplar의 핵심은 `더 예쁜가`가 아니라 `무엇이 먼저 읽히는가`다.
- capture 2장은 각각 다른 질문에 답해야 하고, hierarchy note는 그 질문을 직접 참조해야 한다.
- B-1이 첫 surface라서 더더욱 screenshot 미학보다 stage boundary가 우선이다.

### 한 줄 목표
first-open / return-state를 서로 다른 검수 질문으로 고정한다.

### UI hierarchy note 예시
```md
# B-1 library UI hierarchy example

B-1 library stage에서는 `section-head -> hero-primer -> body-copy -> grid` 순서가 한 화면 안에서 유지된다.
첫 진입 capture(`...first-open`) 기준 hero-primer는 책 카드 첫 줄을 밀어내지 않고 `이 책으로 시작` CTA를 가리지 않는다.
재진입 capture(`...return-state`) 기준 primer는 재노출되지 않고 highlight와 CTA만 남아 library-only 정책이 유지된다.
이번 stage에서는 shelf 전체 리디자인, 추천 책 뱃지 강화, battle/reward 톤 예열을 하지 않는다.
review-ready 전까지 hierarchy note는 readable sanity와 CTA 비차단 verdict만 남긴다.
```

### first-open capture 메타 예시
```md
# B-1 library first-open capture example

- file: captures/20260319-b1-library-first-open.png
- verdict: pass
- question: 첫 library 진입에서 무엇부터 읽어야 하는가가 한 화면 안에 보이는가?
- answer: hero-primer, body-copy, 첫 줄 grid, `이 책으로 시작` CTA가 동시에 읽히고 CTA hit area는 가려지지 않는다.
- boundary: battle/reward/shop/ending surface는 화면에 포함되지 않는다.
```

### return-state capture 메타 예시
```md
# B-1 library return-state capture example

- file: captures/20260319-b1-library-return-state.png
- verdict: pass
- question: 재진입 시 primer 재노출 제어가 library-only로 유지되는가?
- answer: hero-primer는 다시 뜨지 않고 `openingPlan` / `caution` highlight와 CTA만 남는다.
- boundary: battle primer, reward/shop primer, ending CTA helper는 여기서 보이거나 언급되지 않는다.
```

### 왜 이 예시가 중요한가
| 포인트 | 이유 |
| --- | --- |
| capture 2장이 서로 다른 질문을 직접 적음 | generic screenshot drift를 막는다 |
| hierarchy note가 capture 파일명을 직접 참조 | 증거 간 정렬을 쉽게 만든다 |
| `추천 책`이나 `정답 경로` 어휘를 배제 | first-run guidance가 선택 강요로 읽히는 문제를 줄인다 |
| boundary 줄이 포함됨 | 다른 surface 혼입을 빠르게 감지할 수 있다 |

---

## 5. art sanity / PM boundary 예시

## 3줄 요약
- B-1 아트와 PM note는 둘 다 `충분성`과 `금지선`을 같이 적어야 한다.
- 아트 note는 backlog가 아니라 small enough verdict이고, PM note는 진행 보고가 아니라 unlock discipline이다.
- 둘 중 하나라도 커지면 B-1 archive는 다시 전체 온보딩 기획처럼 보인다.

### 한 줄 목표
아트와 PM 문장을 모두 `이번 stage를 닫게 해 주는 최소 판단`으로 유지한다.

### art token sanity 예시
```md
# B-1 library art token sanity example

B-1 library stage에서는 `primer-library` badge, library accent, `openingPlan/caution` highlight만 확인했다.
현재 verdict는 `충분`이며 hero-primer는 안내 표식으로 읽히고 추천/정답 책 메달처럼 보이지 않는다.
이번 stage에서는 도서관 전체 key art 재작업, 책별 개별 배너, battle/reward 공용 ribbon family 제안을 하지 않는다.
review-ready 전에는 token 충분성만 기록하고 신규 자산 요구는 backlog로 뺀다.
```

### PM boundary note 예시
```md
# B-1 library PM boundary example

현재 archive는 B-1 library stage만 다루며 battle/reward/shop/ending primer는 아직 미오픈이다.
이번 stage는 `seenBookSelectPrimer`, hero-primer 1면, openingPlan/caution highlight, acceptance/recovery evidence까지만 닫는다.
QA pass와 PM green 전에는 B-2 unlock 문장을 쓰지 않고, green 뒤에도 다음 허용 단계는 B-2 battle 하나만 남긴다.
`온보딩 진행`, `온보딩 전체 green`, `battle도 같이 가능` 같은 큰 문장은 금지한다.
```

---

## 6. acceptance log 예시

## 3줄 요약
- QA exemplar는 모든 것을 길게 적는 문서가 아니라, `이 로그가 어느 stage만 다루는지`를 첫 줄부터 못 박는 문서다.
- B-1에서는 acceptance와 recovery가 모두 필요하지만, 둘 다 library-only 질문으로 묶여야 한다.
- pass/fail보다 먼저 중요한 것은 질문 범위가 넓어지지 않는 것이다.

### 한 줄 목표
QA 로그가 B-1 surface 범위를 넘어서지 않게 만든다.

```md
# B-1 library acceptance log example

이 로그는 B-1 library acceptance/recovery만 기록한다.

- B1-A1 첫 진입 hero-primer 노출: pass — `library.book-select` hero-primer가 첫 shelf 진입 시 1회만 보인다.
- B1-A2 CTA 비차단: pass — `이 책으로 시작` CTA hit area가 primer에 가리지 않는다.
- B1-A3 highlight 정렬: pass — `openingPlan` / `caution` highlight가 primer와 함께 읽히되 추천 책 강조처럼 보이지 않는다.
- B1-A4 재진입 미노출: pass — 재진입 시 hero-primer는 재노출되지 않는다.
- B1-A5 library-only boundary: pass — battle/reward/shop/ending primer는 stage 증거와 로그에 섞이지 않는다.
- B1-R1 reload 후 seen 유지: pass — reload 뒤에도 primer 1회 규칙이 유지된다.
- B1-R2 save/load return-state 유지: pass — return-state에서 highlight/CTA 읽힘이 유지된다.
- B1-R3 empty/archive-reopen 메모: pass — generic fallback 없이 stage archive와 evidence 경로를 다시 찾을 수 있다.

다음 단계는 review-ready 확인 뒤 QA pass를 기록하는 것이며, B-2 battle unlock은 아직 이 로그 단계에서 쓰지 않는다.
```

### QA가 이 예시에서 가져가야 할 것
- 첫 줄에 stage scope를 먼저 적는다.
- 각 항목이 `library-only` 질문인지 확인한다.
- 마지막 줄은 `다음은 QA pass` 정도까지만 쓰고 `green`이나 `B-2 unlock`을 앞당기지 않는다.

---

## 7. handoff line 재사용 규칙

## 3줄 요약
- exemplar의 진짜 목적은 tracker/run log/portal/git까지 문장을 재사용하게 만드는 것이다.
- `handoff line`, `next allowed step`, `boundary / non-change`는 서로 다른 길이로 같은 뜻을 반복해야 한다.
- 어떤 문장이 어디까지 올라가도 과장되지 않는지 먼저 정해 두면 기록이 훨씬 안전해진다.

### 한 줄 목표
실전 기록 문장을 B-1 archive 예시에서 바로 가져다 쓸 수 있게 만든다.

| 쓰임새 | 재사용하면 좋은 exemplar 줄 | 이유 |
| --- | --- | --- |
| tracker 변경 요약 | `seenBookSelectPrimer`, hero-primer, openingPlan/caution highlight, library-only evidence bundle을 묶었고 다른 surface primer는 아직 열지 않았다. | 범위와 비범위를 동시에 말한다 |
| automation run log 변경 내용 | `manifest 1 + log 1 + note 4 + capture 2`가 같은 library-only 문장으로 정렬된 review-ready candidate bundle | artifact completeness를 짧게 설명한다 |
| portal index 한 줄 소개 | `B-1 library` archive를 실제 채워진 8종 bundle exemplar까지 내려 줌 | 링크 소개 문장으로 짧다 |
| git commit body / notes | `filled archive examples for B-1 library` + `review-ready wording discipline` | 작업 목적이 또렷하다 |

### 재사용하면 안 되는 줄
- `충분`, `괜찮음`, `거의 green` 같은 감각형 표현
- `온보딩 진행`, `초기 UX 정리`, `튜토리얼 보강` 같은 큰 범위 표현
- `battle도 ready`, `reward/shop도 비슷하게 가능` 같은 예고 문장

---

## 8. 금지 문장 / 금지 패턴

### 금지 문장
- `온보딩 진행 중`
- `B-1도 사실상 끝`
- `추천 책처럼 보여도 괜찮음`
- `battle도 같이 준비함`
- `첫 런 UX 거의 green`
- `archive-ready = green`

### 금지 패턴
1. capture 2장은 있는데 note 4종 문장이 서로 다른 stage를 말하는 것
2. PM boundary note가 `다음은 B-2 battle` 대신 `다음은 battle/reward/shop`처럼 범위를 키우는 것
3. art note가 충분성 verdict 대신 대형 자산 요구 목록이 되는 것
4. QA 로그가 B-1 scope 첫 줄 없이 바로 pass/fail 항목만 나열되는 것
5. manifest `status`가 honest한데 handoff line이 과장돼서 green처럼 읽히는 것

---

## 9. 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| starter bundle은 만들었지만 실제 filled wording이 다시 흔들리는 위험 | artifact 8종이 있어도 review-ready/green 문장이 다시 owner 감각으로 갈라진다 | B-1 archive filled examples와 example 파일 8종을 추가해 stage별 충분성 문장을 exemplar로 고정 | 다음 실제 B-1 archive 생성 직전 |
| UI/아트 note가 backlog 문서처럼 커지는 위험 | 첫 surface가 다시 대형 UX/아트 개편 논의로 번진다 | hierarchy / art sanity 예시를 4~5줄 충분성 note로 제한 | 첫 visual sanity 리뷰 시 |
| QA 로그가 acceptance는 맞지만 unlock 문장을 너무 빨리 쓰는 위험 | review-ready / QA pass / PM green / B-2 unlock 계층이 흐려진다 | QA exemplar 마지막 줄을 `다음은 QA pass` 수준으로 제한 | 첫 review-ready candidate 판정 시 |
| tracker/run log가 generic progress 문장으로 회귀하는 위험 | archive bundle을 채워도 외부 기록에서 다시 surface 경계가 사라진다 | handoff line 재사용 규칙을 문서에 별도 고정 | tracker/run log 작성 시 |

---

## 10. 즉시 다음 액션
1. 다음 B-1 live turn 전에는 `readiness board -> B-1 live kickoff packet -> B-1 archive bootstrap packet -> 이 filled archive examples` 순서로 읽는다.
2. `docs/artifacts/examples/b1-library/` exemplar 8종을 archive starter bundle과 나란히 열어 wording alignment 기준으로 쓴다.
3. 실제 archive에서는 exemplar보다 느슨한 문장으로 `review-ready candidate`를 주장하지 않는다.
4. tracker/run log/portal 문장은 가능하면 이 문서의 exemplar `handoff line`과 `next allowed step`을 거의 그대로 재사용한다.
5. B-1 green 뒤에도 다음 허용 단계는 `B-2 battle` 하나만 남기고 reward/shop/ending 예고 문장은 쓰지 않는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- `docs/artifacts/examples/b1-library/README.md`
