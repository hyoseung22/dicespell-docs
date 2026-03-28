# DiceSpell 첫 런 온보딩 `B-1 library` review & signoff packet

## 3줄 요약
- 이 문서는 `B-1 library packet`과 `B-1 live kickoff packet`이 이미 **착수 순서**를 닫아 둔 상태에서, 마지막으로 남아 있던 **green 판정 순서**를 고정한다.
- 프로그래머는 `seenBookSelectPrimer` / `renderGrimoireShelf()` / `openingPlan·caution highlight` diff가 실제로 **library-only**인지 먼저 확인하면 된다.
- UI/아트/QA/PM은 `library acceptance evidence 통과 -> boundary 유지 확인 -> B-1 green 선언 -> B-2 unlock` 순서를 공유하면 된다.

**한 줄 목표:** `B-1 library`를 단순히 “붙였다”가 아니라, **어떤 evidence가 모여야 green이라고 말할 수 있고 누가 다음 `B-2 battle`을 열 수 있는지**를 source-of-truth로 고정한다.

---

## 왜 이 문서가 필요한가
이미 온보딩 문서 세트는 충분히 강하다.

- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`: 무엇을 구현할지
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`: 첫 10분에 무엇부터 잠글지
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`: B-track 실행 순서
- `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md`: surface별 handoff 체크리스트
- `docs/dicespell_first_run_onboarding_owner_workboard_kr.md`: owner별 제출물
- `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`: evidence 규칙
- `docs/dicespell_overrun_onboarding_signoff_ladder_kr.md`: 단계별 승인 계층

그런데 실전 직전에는 여전히 작은 공백이 남는다.

1. kickoff가 끝난 뒤 **무엇이 모이면 B-1 green인지**가 B-1 surface 언어로 한 장에 닫혀 있지 않다.
2. QA pass, UI sanity, PM green, B-2 unlock이 서로 다른 층위인데도 실제 리뷰에서는 쉽게 한 문장으로 뭉개진다.
3. `library hero-primer를 붙였다`와 `B-1 library를 닫았다`는 다른 말인데, evidence 순서가 없으면 half-cutover가 완료처럼 보인다.

즉 이 문서는 새 primer 설계를 더하는 문서가 아니라, **B-1 library를 archive-ready -> green -> B-2 unlock까지 어떻게 닫는지**를 production review 언어로 고정하는 packet이다.

---

## 누가 어디부터 읽으면 되나

| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | 2. review 범위, 4. 필수 evidence | `B-1 library packet`, `B-1 live kickoff packet` | B-1은 `seenBookSelectPrimer + hero-primer + highlight 2곳`까지만 green 후보다 |
| UI | 3. visual sanity, 5. signoff 순서 | `screen packet`, `content deck` | primer density는 닫되 shelf 전체 redesign은 green 근거가 아니다 |
| 아트 | 3. visual sanity, 6. 금지 문장 | `content deck`, `visual asset packet` | 이번 턴 산출물은 token sanity이지 대형 신규 자산이 아니다 |
| QA | 4. 필수 evidence, 5. signoff 순서 | `acceptance matrix`, `evidence manifest` | B1-A1~A5 + recovery 3종 + library-only boundary가 pass 기준이다 |
| PM/기획 | 0. first screen, 5. signoff 순서, 7. 기록 문장 | `readiness board`, `execution reporting packet` | `QA pass`, `B-1 green`, `B-2 unlock`은 다른 문장으로 기록해야 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `B-1 library`를 언제 `green`이라고 말할 수 있지?
- B-1 evidence bundle 안에서 무엇이 필수이고 무엇은 참고 자료지?
- 누가 먼저 pass를 말하고, 누가 마지막 green을 기록하며, 언제 B-2가 열리지?

### 가장 짧은 답
- **QA pass**는 `B1-A1~A5 + recovery 3종 + library-only boundary`가 모였을 때만 가능하다.
- **UI/아트 sanity**는 `hero-primer 1장 + openingPlan/caution highlight 2곳 + CTA 비차단`이 과장 없이 읽히는지만 본다.
- **PM green**은 QA/UI evidence가 모인 뒤 `battle/reward/shop/ending 미오픈`이 유지된 상태에서만 기록한다.
- **B-2 unlock**은 green 이후에만 쓴다. `거의 됨`, `온보딩 진행 중`, `library/battle 초안 완료` 같은 큰 문장은 금지다.

---

## 1. B-1 review의 한 줄 정의

> `seenBookSelectPrimer`, `library.book-select` hero-primer, `openingPlan/caution` highlight, library acceptance/recovery evidence, boundary note가 archive에 모였고 battle/reward/shop/ending primer가 아직 열리지 않았음을 확인하는 review 단계.

이 정의보다 커지면 B-1 review가 아니라 다른 단계다.

---

## 2. review 범위와 비범위

### review에 포함되는 것
- `seenBookSelectPrimer` 저장/정규화 여부
- `renderGrimoireShelf()` 안 hero-primer 삽입 여부
- `openingPlan` / `caution` highlight 가독성
- 책 선택 CTA 비차단성
- B1-A1~A5 acceptance
- B1-R1~R3 recovery
- `battle/reward/shop/ending 미오픈` boundary note

### review에 포함되지 않는 것
- `battle.entry`, `battle.first-spell`, `battle.auto-pass`
- reward/shop primer 카피 또는 accent 분리
- ending helper, overrun CTA helper
- shelf 전체 visual refresh
- primer family 공용화 리팩터링

### review의 목적
B-1 review는 `온보딩이 시작됐다`를 선언하는 단계가 아니다.
B-1 review의 목적은 **첫 surface 하나가 독립된 commit/evidence/signoff 언어로 닫혔는지**를 확인하는 것이다.

---

## 3. visual sanity 기준

## 3줄 요약
- B-1 visual sanity는 새 key art를 요구하는 단계가 아니다.
- 확인할 것은 `hero-primer가 shelf를 먹어 치우지 않는가`, `highlight가 추천 책처럼 읽히지 않는가`, `CTA가 가려지지 않는가`다.
- 즉 이 단계는 polish review가 아니라 **density/boundary review**다.

### one-line goal
primer가 library surface를 보강해야지, library surface를 대체하면 안 된다.

### 왜 중요한가
B-1은 가장 쉽게 `좋은 의도` 때문에 무너진다.
- primer를 더 읽히게 하려다 shelf 전체 hierarchy를 깨뜨릴 수 있다.
- highlight를 더 분명히 하려다 특정 책 추천으로 읽힐 수 있다.
- 아트 token을 더 풍부하게 하려다 B-1이 asset blocker처럼 보일 수 있다.

### visual sanity checklist
| 항목 | pass 기준 | fail 예시 |
| --- | --- | --- |
| hero-primer hierarchy | `.section-head` 아래에서 한 화면 안에 읽히고 카드 그리드를 가리지 않는다 | primer가 카드 첫 줄을 과도하게 밀어낸다 |
| highlight density | `openingPlan`, `caution`만 subtle하게 읽힌다 | `완주 보너스`, 책 전체 카드까지 같은 세기로 강조된다 |
| CTA 비차단 | primer가 있어도 `이 책으로 시작`이 바로 읽히고 클릭 가능하다 | primer가 버튼을 밀거나 가린다 |
| tone discipline | `첫 런 가이드`로 읽힌다 | `추천 책`, `정답 책`, `우선 pick`처럼 읽힌다 |
| asset scope | 기존 library tone 안에서 badge/accent/highlight만 정리한다 | 신규 일러스트/대형 장식이 없으면 review가 멈춘다 |

### role-specific handoff points
- 프로그래머: DOM/클래스가 visual sanity를 소비하되 새 shell을 만들지 않는다.
- UI: hierarchy note에 `primer > body-copy > grid` 밀도만 기록한다.
- 아트: token sanity 메모만 남기고 신규 자산 필요를 green 조건으로 만들지 않는다.

---

## 4. 필수 evidence bundle

## 한 줄 목표
B-1 green은 **문장**이 아니라 **증거 묶음**으로 성립한다.

| 증거 | 필수 내용 | owner | B-1 green에서 하는 역할 |
| --- | --- | --- | --- |
| `manifest.md` | `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line` | PM/프로그래머 | stage identity를 고정 |
| acceptance log | B1-A1~A5 / B1-R1~R3 / CTA 비차단 결과 | QA | surface pass/fail 근거 |
| state diff note | `seenBookSelectPrimer` 추가 여부, 다른 seen flag 미추가, 관련 render hook | 프로그래머 | library-only diff 근거 |
| hierarchy note | hero-primer 위치, highlight 강도, CTA 가림 여부 | UI | visual sanity 근거 |
| boundary note | battle/reward/shop/ending 미오픈 유지, B-2 unlock 대기 | PM | half-cutover 방지 근거 |
| capture 1~2장 | library first-open, primer/hightlight density 또는 primer off evidence | UI/QA | portal/tracker 요약 근거 |

### evidence가 모였다고 보기 위한 최소선
1. `manifest.md`에 아직 `next allowed step: B-2 battle unlock 대기` 또는 동등 문장이 있어야 한다.
2. acceptance log에 `library-only`가 명시돼야 한다.
3. state diff note에 `seenBattlePrimer` 등 다른 primer flag 미추가가 적혀 있어야 한다.
4. boundary note에 `reward/shop/ending 미오픈`이 적혀 있어야 한다.

### evidence가 부족한 상태의 예
- 캡처는 있는데 acceptance log가 없음
- QA pass는 적었는데 state diff note가 없음
- hierarchy note는 있는데 battle primer slot도 같이 열림
- green 문장은 있는데 `manifest.md`가 여전히 bootstrap-ready 상태

이 경우는 green이 아니라 **partial / review-pending**에 가깝다.

---

## 5. signoff 순서

## 3줄 요약
- B-1 signoff는 한 번에 끝나는 단일 도장이 아니다.
- `프로그래머 제출 -> UI/아트 sanity -> QA pass -> PM green -> B-2 unlock`의 다섯 단계다.
- 이 순서가 깨지면 half-cutover가 완료처럼 보인다.

### one-line goal
`review-ready`, `QA pass`, `green`, `unlock`을 다른 층위로 분리한다.

### core structure
| 단계 | 선언자 | 필요 evidence | 이 단계에서 말할 수 있는 문장 | 아직 말하면 안 되는 문장 |
| --- | --- | --- | --- | --- |
| review-ready | 프로그래머 | manifest + state diff + kickoff archive + 코드 diff | `B-1 review-ready` | `B-1 green`, `B-2 unlock` |
| visual sanity | UI/아트 | hierarchy note + capture | `B-1 visual sanity ok` | `QA pass`, `green` |
| QA pass | QA | acceptance log + recovery + boundary 확인 | `B-1 QA pass` | `B-2 unlock` |
| PM green | PM/기획 | 위 3단계 evidence bundle 완비 | `B-1 library green` | `온보딩 진행`, `온보딩 완료` |
| next unlock | PM/기획 | tracker/run log/portal 반영 | `다음 단계는 B-2 battle만 연다` | `reward/shop도 준비됨`, `ending도 같이 진행` |

### role-specific handoff points
- 프로그래머: review-ready 전에는 green 문장을 쓰지 않는다.
- UI/아트: visual sanity는 `읽힘 ok`이지 `이제 끝`이 아니다.
- QA: pass는 surface 범위 검증까지 포함해야 한다.
- PM: portal/tracker 반영 전에는 unlock을 쓰지 않는다.

---

## 6. 금지 문장 / 금지 패턴

### 금지 문장
- `온보딩 진행`
- `첫 런 가이드 적용`
- `library/battle 초안 완료`
- `B-1 거의 끝`
- `온보딩 green`
- `B-2도 사실상 ready`

### 금지 패턴
1. B-1 review에서 battle/reward/shop/ending evidence를 한 archive에 섞는 것
2. QA pass 전에 PM이 green을 먼저 쓰는 것
3. hierarchy sanity만으로 pass를 선언하는 것
4. `seenBookSelectPrimer` 외 다른 primer flag가 생겼는데 boundary note에 누락하는 것
5. stage archive 없이 스크린샷만 DM/메신저에 남기고 green을 쓰는 것

---

## 7. tracker / run log / portal 기록 문장

### review-ready 문장
> B-1 review-ready: `seenBookSelectPrimer`, `library.book-select` hero-primer, `openingPlan/caution` highlight, library-only archive/evidence 초안을 묶었고 battle/reward/shop/ending primer는 아직 열지 않았다.

### QA pass 문장
> B-1 QA pass: B1-A1~A5와 recovery 3종, CTA 비차단, library-only boundary 유지가 확인됐다.

### PM green 문장
> 이번 단계는 `B-1 library` primer만 닫아 `seenBookSelectPrimer`, `library.book-select` hero-primer, `openingPlan/caution` highlight, library acceptance/recovery evidence를 고정했고 battle/reward/shop/ending primer는 아직 열지 않았다.

### next unlock 문장
> B-1 evidence bundle이 tracker/run log/portal에 반영됐으므로 다음 단계는 `B-2 battle`만 연다.

---

## 8. 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 | 다음 handoff |
| --- | --- | --- | --- |
| B-1과 B-2 기록이 섞이는 위험 | surface별 완료선이 무너진다 | review/pass/green/unlock 문장 분리 | B-1 실제 리뷰 턴 |
| visual sanity가 신규 자산 blocker처럼 부풀어 오르는 위험 | 작은 primer turn이 art 대기열로 변한다 | token sanity만 green 조건으로 고정 | B-1 UI/아트 sanity |
| QA pass 전에 green이 먼저 기록되는 위험 | half-cutover가 완료처럼 보인다 | signoff ladder를 B-1 surface 언어로 재압축 | tracker/run log 작성 시 |
| capture만 있고 state diff가 없는 위험 | library-only diff가 증명되지 않는다 | evidence bundle에 state diff note를 필수화 | 프로그래머 제출 시 |
| B-1 green 뒤에도 B-2 unlock 문장이 누락되는 위험 | 다음 단계 착수선이 모호해진다 | unlock 문장을 별도 단계로 고정 | PM 기록 시 |

---

## 9. 즉시 다음 액션
1. B-1 첫 live turn이 열리면 `readiness board -> B-1 live kickoff packet -> 이 review packet` 순서로 읽는다.
2. review-ready 전에는 green 문장을 쓰지 않는다.
3. QA pass는 반드시 B1-A1~A5 / recovery 3종 / boundary note까지 보고 쓴다.
4. PM green 뒤에만 `다음 단계는 B-2 battle만 연다`를 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_owner_workboard_kr.md`
- `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`
- `docs/dicespell_overrun_onboarding_signoff_ladder_kr.md`
