# DiceSpell 첫 런 온보딩 `B-1 library` Readiness Board

## 3줄 요약
- 이 문서는 `B-1 library packet`, `live kickoff`, `archive bootstrap`, `required artifacts`, `filled archive examples`, `handoff scorecard`, `review packet`까지 문서가 충분한 지금도 끝까지 남아 있던 **`그래서 지금 실제로 누가 B-1을 시작해도 되고, 누가 아직 기다려야 하지?`**를 한 화면으로 고정하는 source-of-truth다.
- 핵심은 새 primer 설계를 더하는 것이 아니라, `bootstrap-ready -> archive-in-progress -> review-ready -> QA pass -> PM green -> B-2 unlock`을 **실제 착수/대기/인계 판단 언어**로 다시 잠가 프로그래머 / UI / 아트 / QA / PM이 같은 stop line을 보게 만드는 것이다.
- 첫 화면만 읽어도 `B-1은 지금 유일한 live-ready onboarding stage인가`, `B-2/B-3/B-4는 왜 아직 locked인가`, `어떤 evidence가 모여야 review-ready와 green을 분리해서 말할 수 있는가`가 바로 보여야 한다.

## 한 줄 목표
B-track 첫 실전 turn을 `문서 묶음`이 아니라 **오너별 시작 가능 / 대기 / 제출 / 기록 보드**로 재배열해, B-1이 다시 `온보딩 전체 착수`나 `B-2도 같이 준비` 같은 큰 진행 메모로 부풀지 않게 만든다.

## 왜 이 문서가 필요한가
지금 첫 런 온보딩 축은 이미 아래 문서까지 충분히 닫혀 있다.

- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md`
- `docs/dicespell_first_run_onboarding_b1_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_archive_file_map_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_document_stack_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- B-1이 왜 `library-only` stage인가
- 첫 10분 kickoff를 어떤 순서로 열어야 하는가
- 어떤 archive / stub / manifest / capture / note가 필요한가
- review-ready / QA pass / PM green / B-2 unlock을 어떤 문장으로 나눠야 하는가

하지만 실제 live turn 직전에는 아직 아래 공백이 남아 있다.

1. 문서는 충분한데도 implementer는 `그래서 지금 정말 B-1만 열 수 있는가?`를 여러 packet에서 다시 조합해 읽어야 한다.
2. UI/아트는 `지금 들어가도 되는 턴`과 `아직 evidence sanity만 준비해야 하는 턴`을 같은 표에서 보지 못하면, B-1 green 전 B-2 battle 문구나 다음 surface token 메모를 먼저 열기 쉽다.
3. QA/PM은 `archive가 있다`, `review packet이 있다`, `scorecard가 있다`를 곧바로 `거의 green`으로 뭉개기 쉽다.
4. 결국 첫 B-1 실전 turn이 `library만` 닫는 bounded execution이 아니라 `온보딩 kickoff`, `battle도 곧`, `surface 여러 개 동시 준비` 같은 큰 진행 메모로 부풀 수 있다.

즉 지금 필요한 것은 새 primer나 새 카피가 아니라, **B-1 문서 readiness와 실제 execution readiness를 분리해서 보여 주는 readiness board**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 판정`, `2. stage readiness 보드`, `3-1. 프로그래머 handoff` | `B-1 live kickoff`, `semantic anchor`, `required artifacts`, `archive file map` | 지금 열 수 있는 live edit 범위는 B-1 library뿐이고, B-2/B-3/B-4는 PM green 전까지 참고 문서이지 실행 범위가 아니다 |
| UI | `0. first screen 판정`, `2. stage readiness 보드`, `3-2. UI handoff` | `screen packet`, `filled archive examples`, `handoff scorecard` | 지금 필요한 것은 library first-open / return-state density 검토이며 battle hero-primer나 reward/shop strip은 아직 대기다 |
| 아트 | `2. stage readiness 보드`, `3-3. 아트 handoff`, `5. 리스크 & 후속과제` | `content deck`, `visual asset packet`, `review packet` | 이번 턴 아트 역할은 badge/accent/highlight sanity이고, 신규 대형 자산이나 다음 surface token 제안은 아직 비범위다 |
| QA | `2. stage readiness 보드`, `3-4. QA handoff`, `4. 기록 문장 규칙` | `acceptance matrix`, `required artifacts`, `review packet` | `review-ready`, `QA pass`, `PM green`은 서로 다른 증거 묶음을 가진다 |
| PM/기획 | `0. first screen 판정`, `2. stage readiness 보드`, `4. 기록 문장 규칙`, `5. 리스크 & 후속과제` | `cutover readiness board`, `runtime stack`, `execution reporting packet` | 지금 열려 있는 온보딩 stage는 B-1 하나뿐이고, green 뒤에만 B-2 battle을 연다 |

---

## 0. first screen 판정

### 이 문서가 답하는 질문
- 지금 실제로 시작 가능한 온보딩 stage는 무엇인가?
- `문서가 충분함`과 `지금 구현/리뷰를 열어도 됨`은 왜 다른가?
- B-2 battle은 왜 B-1 PM green 뒤에만 열리는가?

### 가장 짧은 답
- **지금 실제로 열 수 있는 stage**: `B-1 library`
- **B-1 review-ready 뒤에만 가능한 단계**: `B-1 QA pass`
- **B-1 PM green 뒤에만 가능한 다음 stage**: `B-2 battle`
- **지금 금지**: B-1 evidence bundle 안에 `battle.entry`, `reward.first`, `shop.first`, `ending.defeat/victory/overrun-cta`를 섞는 것

### 한 줄 판정 규칙
첫 런 온보딩은 지금 **`B-1 only live-ready, B-2/B-3/B-4 locked`** 상태로 읽어야 한다.

---

## 1. first screen 결론

## 3줄 요약
- B-1 문서는 이미 많지만, 실제 실행 순서는 여전히 `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending` 하나뿐이다.
- readiness board의 역할은 문서 목록을 더 늘리는 것이 아니라 `지금 열 수 있는 단계`와 `아직 문장만 준비해야 하는 단계`를 분리하는 데 있다.
- 이 분리가 없으면 문서 완성도가 높을수록 오히려 B-1 turn이 `온보딩 전반 kickoff`처럼 부풀기 쉽다.

### 한 줄 목표
`문서가 있음`과 `지금 착수 가능`을 다른 층위로 고정한다.

| 질문 | 짧은 판정 | 이유 |
| --- | --- | --- |
| B-1은 지금 열 수 있는가? | 예 | runtime stack, live kickoff, archive/bootstrap/stub/manifest/semantic anchor, required artifacts, filled examples, scorecard, review packet까지 닫혀 있어 첫 bounded library stage를 열 조건이 갖춰져 있다 |
| B-2는 지금 열 수 있는가? | 아니오 | battle 문서는 준비됐지만 unlock 조건은 여전히 `B-1 library green`이다 |
| B-3는 지금 열 수 있는가? | 아니오 | reward/shop 문서는 준비됐지만 unlock 조건은 `B-2 battle green`이다 |
| B-4는 지금 열 수 있는가? | 아니오 | ending helper 문서는 준비됐지만 unlock 조건은 `B-3 reward/shop green`이고, overrun boundary도 끝까지 분리돼야 한다 |
| 지금 해도 되는 일은 무엇인가? | B-1 archive 생성 + artifact alignment + review/green 준비 | stage 범위를 넓히지 않고도 실질 evidence를 남길 수 있는 유일한 시작점이기 때문이다 |

---

## 2. stage readiness 보드

## 3줄 요약
- 아래 표는 B-1을 `bootstrap-ready`, `archive-in-progress`, `review-ready`, `QA pass`, `PM green`, `B-2 unlock`의 실제 실행 계층으로 다시 읽게 만든다.
- 목적은 첫 B-1 turn이 `archive를 만들었다 = 거의 green`이나 `capture가 있다 = B-2도 곧` 같은 과장으로 흐르지 않게 하는 것이다.
- 프로그래머, UI, 아트, QA, PM 모두 이 표의 단계 언어를 tracker/run log/git에 그대로 복붙해야 한다.

### 한 줄 목표
B-1을 `파일 있음`이 아니라 **시작 가능 / 제출 가능 / signoff 가능** 상태로 고정한다.

| 단계 | 지금 상태 | 시작 조건 | 이번 단계 핵심 산출물 | 아직 금지 | 다음 단계로 넘기는 증거 |
| --- | --- | --- | --- | --- | --- |
| `bootstrap-ready` | **즉시 시작 가능** | `B-1 document stack -> B-1 live kickoff -> B-1 archive bootstrap` 읽기 완료 | `docs/artifacts/YYYYMMDD/b1-library/` root, starter manifest, stub 4종, kickoff 메모 | B-2 battle 메모, green 문장, next surface seen flag | `manifest.md` + starter stub + `archive-in-progress` 선언 |
| `archive-in-progress` | **bootstrap 뒤 즉시 가능** | `semantic anchor`, `archive file map`, `required artifacts` 재확인 | state diff, hierarchy note, art sanity, boundary note, first-open/return-state capture | QA pass, PM green, reward/shop/ending wording | artifact 8종이 canonical file route와 같은 stage 언어를 쓴다 |
| `review-ready` | **artifact alignment 뒤 가능** | `filled archive examples`, `handoff scorecard`, `review packet` 재확인 | `B-1 review-ready` 문장, bundle completeness, non-change line | `B-1 green`, `B-2 unlock` | manifest / acceptance log / state diff / hierarchy note / boundary note / capture 2장이 모두 같은 `library-only` 문장을 쓴다 |
| `QA pass` | **review-ready 뒤 가능** | B1-A1~A5 + recovery 3종 + CTA 비차단 + library-only boundary 확인 | `B-1 QA pass` 문장, pass/fail note, recovery verdict | `B-1 green`, `B-2 unlock`, 온보딩 전체 진행 메모 | acceptance/recovery log와 boundary note가 같은 archive를 기준으로 일치한다 |
| `PM green` | **QA pass 뒤 가능** | QA pass + UI/아트 sanity + bundle completeness + tracker/run log 반영 준비 | `B-1 library green` 문장, stage close verdict | `온보딩 진행`, `B-2도 사실상 ready`, reward/shop/ending 선기록 | PM이 `B-1 library`만 닫았음을 한 줄로 기록한다 |
| `B-2 unlock` | **PM green 뒤 가능** | tracker/run log/portal 반영 완료 | `다음 단계는 B-2 battle만 연다` 문장 | B-3/B-4 동시 unlock, surface 여러 개 동시 착수 | B-1 archive path와 green 문장이 포털/트래커/로그에 모두 같은 뜻으로 반영된다 |

### 단계별 한 줄 해석
- **bootstrap-ready**: archive 바닥을 여는 단계
- **archive-in-progress**: live evidence를 채우는 단계
- **review-ready**: artifact alignment를 선언하는 단계
- **QA pass**: acceptance/recovery를 surface boundary까지 검수하는 단계
- **PM green**: B-1 하나만 닫았다고 기록하는 단계
- **B-2 unlock**: 다음 stage를 하나만 여는 단계

---

## 3. 역할별 handoff 포인트

## 3-1. 프로그래머에게 넘길 것

## 3줄 요약
- 프로그래머는 지금 `무슨 primer를 더 붙일까`보다 `어느 stage 하나만 열어도 되는가`를 판단해야 한다.
- 현재 실제로 열 수 있는 live edit 범위는 `seenBookSelectPrimer`, `renderGrimoireShelf()`, `openingPlan/caution` highlight, library archive evidence뿐이다.
- B-2/B-3/B-4는 문서가 있어도 unlock 전에는 참고 문서이지 실행 범위가 아니다.

**한 줄 목표:** 프로그래머는 B-1 green 전까지 B-1만 닫고, unlock 전 stage를 미리 열지 않는다.

| 단계 | 열어도 되는 것 | 이번 턴 핵심 작업 | 절대 섞지 말 것 |
| --- | --- | --- | --- |
| bootstrap-ready | `docs/artifacts/YYYYMMDD/b1-library/`, `manifest.md`, stub files | archive root 생성, starter 문장 고정 | B-2 note, green 선기록 |
| archive-in-progress | `readMetaProgress`, `persistMetaProgress`, `renderGrimoireShelf`, highlight hook 관련 note/capture | state diff, first-open/return-state evidence, library-only non-change note | battle/reward/shop/ending primer, 공용 primer helper 확장 |
| review-ready | 동일 archive 내 evidence alignment | `B-1 review-ready` 문장과 bundle completeness | QA pass/green 선기록 |
| PM green 이후 | `B-2 packet` 재확인 | 다음 stage 착수 준비 | B-3/B-4 동시 예열 |

### 프로그래머 제출물
1. stage 이름 1개
2. archive path 1개
3. state diff / non-change 메모 1개
4. 현재 단계 handoff 문장 1개

---

## 3-2. UI에게 넘길 것

## 3줄 요약
- UI의 기본 역할은 지금 `도서관을 더 예쁘게`가 아니라 `B-1 evidence가 honest한 hierarchy를 말하는가`를 닫는 것이다.
- B-1 green 전에는 battle hero-primer, reward/shop strip, ending helper를 같이 정리하면 안 된다.
- queue 같은 운영 문장이 아니라 `first-open / return-state 두 장이 library-only 질문에 답하는가`가 핵심이다.

**한 줄 목표:** UI는 B-1에서 library 선택 surface의 읽힘만 닫고, 다음 surface visual pass를 미리 열지 않는다.

| 단계 | UI 역할 | 이번 턴 필수 확인 | 금지 |
| --- | --- | --- | --- |
| archive-in-progress | 실제 evidence closing | hero-primer hierarchy, highlight 2곳 강도, CTA 비차단, first-open / return-state capture | shelf 전체 리디자인, battle primer 선탑재 |
| review-ready | evidence alignment 확인 | hierarchy note와 capture가 같은 질문을 답하는지 | `거의 green` 문장 사용 |
| QA pass 이후 | PM green 지원 | B-1 한 줄 기록과 capture reference 정렬 | B-2 visual scope 선메모 |

### UI 제출물
1. hierarchy note 1개
2. first-open / return-state capture 세트
3. 비범위 polish 메모 1개
4. stage language 정렬 확인 1개

---

## 3-3. 아트에게 넘길 것

## 3줄 요약
- 아트의 기본 임무는 대형 신규 자산이 아니라 `primer-library` badge/accent/highlight sanity를 닫는 것이다.
- 가장 큰 위험은 `이왕이면 battle token도 같이`나 `도서관 전체 key art refresh`로 stage 경계를 흐리는 것이다.
- 지금 필요한 것은 library stage의 bounded sanity다.

**한 줄 목표:** 아트는 B-1 한 면의 token/accent sanity만 닫고, 전체 온보딩 visual bundle을 열지 않는다.

| 단계 | 아트 기본 역할 | 이번 턴 필요한 것 | 이번 턴 필요 없는 것 |
| --- | --- | --- | --- |
| archive-in-progress | sanity closing | `첫 런 가이드` badge/accent, `openingPlan/caution` highlight drift 없음 메모 | 신규 key art, battle/reward/shop 공용 ribbon 제안 |
| review-ready | evidence alignment | art-token-sanity가 `충분/약함/과함` 중 하나로 끝나는지 | 대형 발주 일정 |
| PM green 이후 | 후속 대기 | B-2 battle용 token 준비 여부만 메모 | B-2 실제 산출물 시작 |

### 아트 제출물
1. art-token-sanity note 1개
2. drift 없음 / 보류 이유 1줄
3. 다음 surface는 아직 비범위라는 메모 1줄

---

## 3-4. QA에게 넘길 것

## 3줄 요약
- QA는 지금부터 `artifact가 있다`를 pass로 기록하면 안 된다.
- B-1은 `review-ready`, `QA pass`, `PM green`이 각각 다른 증거 묶음을 가진다.
- stage 이름이 흐려지면 가장 먼저 무너지는 것은 검수보다 기록 신뢰도다.

**한 줄 목표:** QA는 `review-ready`, `QA pass`, `green`을 서로 다른 evidence로 분리 기록한다.

| 단계 | 필수 evidence | 같이 남길 질문 | fail로 돌려야 하는 경우 |
| --- | --- | --- | --- |
| review-ready | artifact 8종 + manifest + library-only wording alignment | 모든 파일이 같은 `B-1 library` 문장을 쓰는가 | battle/reward/shop/ending wording이 섞였을 때 |
| QA pass | B1-A1~A5 + recovery 3종 + CTA 비차단 + boundary note | primer가 선택을 막지 않고 재진입 정책이 보이는가 | capture만 있고 recovery verdict가 없을 때 |
| PM green 지원 | QA pass + UI/아트 sanity + tracker/run log 준비 | green 문장이 surface 하나만 닫았다고 말하는가 | `온보딩 진행`, `B-2도 ready` 같은 큰 문장이 섞였을 때 |

---

## 3-5. PM/기획에게 넘길 것

## 3줄 요약
- PM/기획의 가장 중요한 역할은 지금부터 `문서가 충분하다`와 `구현이 끝났다`를 같은 문장으로 쓰지 않는 것이다.
- 이 보드는 곧 기록 보드다. 어떤 단계명을 쓰는지가 실제 범위를 통제한다.
- stop signal이 보이면 기능 추가보다 기록 정정이 먼저다.

**한 줄 목표:** PM은 B-1 단계명을 관리해 B-track 범위 드리프트를 막는다.

### PM 체크포인트
- 지금 남길 수 있는 green 문장은 B-1 하나뿐이다.
- `온보딩 진행`, `첫 런 가이드 적용`, `library/battle 동시 준비` 같은 큰 문장은 금지한다.
- B-2 unlock 전에는 언제나 `battle/reward/shop/ending 미오픈`이 tracker/run log/portal에 남아 있어야 한다.

---

## 4. 기록 문장 규칙

## 3줄 요약
- 단계가 분리돼도 기록 문장이 섞이면 결국 같은 문제로 돌아간다.
- 따라서 `archive-in-progress`, `review-ready`, `QA pass`, `green`, `unlock`은 항상 다른 문장으로 남겨야 한다.
- 이 보드는 실행 순서만이 아니라 기록 순서도 고정한다.

**한 줄 목표:** 단계별로 `문장 1개 + evidence 1개 + append log 1개`만 남긴다.

| 단계 | tracker/run log에 남길 기본 문장 | 같이 묶을 evidence |
| --- | --- | --- |
| archive-in-progress | `B-1 archive-in-progress: library-only starter bundle과 first-open/return-state evidence를 채우는 중이며 battle/reward/shop/ending primer는 아직 열지 않았다.` | manifest + stub + capture 초안 |
| review-ready | `B-1 review-ready: seenBookSelectPrimer, hero-primer, openingPlan/caution highlight, library-only archive/evidence 초안을 묶었고 다른 surface primer는 아직 열지 않았다.` | artifact 8종 정렬 |
| QA pass | `B-1 QA pass: B1-A1~A5와 recovery 3종, CTA 비차단, library-only boundary 유지가 확인됐다.` | acceptance/recovery log |
| PM green | `B-1 library green: seenBookSelectPrimer, library.book-select hero-primer, openingPlan/caution highlight, library acceptance/recovery evidence를 고정했고 battle/reward/shop/ending primer는 아직 열지 않았다.` | green verdict + boundary note |
| B-2 unlock | `다음 단계는 B-2 battle만 연다.` | tracker/run log/portal 동기화 |

### 기록 규칙
1. run log는 항상 새 timestamped entry를 **끝에 append**한다.
2. tracker/public tracker/index는 가장 최근 단계명만 짧고 명확하게 반영한다.
3. `온보딩 진행`, `library/battle 초안`, `거의 완료` 같은 큰 문장은 금지한다.

---

## 5. 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 | 다음 handoff |
| --- | --- | --- | --- |
| B-1 readiness와 green이 다시 같은 뜻으로 기록되는 위험 | half-cutover가 완료처럼 보인다 | 단계명을 `archive-in-progress / review-ready / QA pass / PM green / B-2 unlock`으로 분리했다 | 첫 B-1 실전 archive turn |
| UI/아트가 B-2 visual/tone 메모를 너무 일찍 여는 위험 | B-1 library-only boundary가 흐려진다 | readiness board에서 B-2/B-3/B-4를 locked로 못 박았다 | B-1 UI/아트 sanity |
| artifact가 다 있어도 wording drift가 남는 위험 | review-ready 판정이 감각적으로 흐른다 | artifact alignment를 review-ready 선결 조건으로 재고정했다 | QA pass 직전 |
| PM이 `온보딩 진행` 같은 큰 문장을 다시 쓰는 위험 | B-1과 B-2 기록이 다시 섞인다 | stage별 복붙 문장을 board 안에 다시 고정했다 | tracker/run log/portal 반영 시 |
| B-1 green 뒤 B-2 unlock 문장이 누락되는 위험 | 다음 착수선이 모호해진다 | unlock을 별도 단계로 분리했다 | PM green 직후 |

---

## 6. 즉시 다음 액션
1. 다음 B-1 실전 turn이 열리면 `cutover readiness board -> onboarding runtime stack -> B-1 document stack -> 이 readiness board -> B-1 live kickoff` 순서로 읽는다.
2. `docs/artifacts/YYYYMMDD/b1-library/` root를 먼저 만들고 `archive-in-progress` 문장부터 적는다.
3. review-ready 전에는 QA pass / PM green / B-2 unlock 문장을 쓰지 않는다.
4. PM green 뒤에만 `다음 단계는 B-2 battle만 연다`를 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_document_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
