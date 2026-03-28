# DiceSpell 첫 런 온보딩 `B-1 library` review & signoff summary

## 3줄 요약
- 이 문서는 `B-1 library` 첫 실구현 뒤 **무엇이 모이면 green인지**를 빠르게 읽는 companion이다.
- 핵심은 `review-ready`, `QA pass`, `PM green`, `B-2 unlock`을 같은 말로 쓰지 않는 것이다.
- B-1은 끝까지 `seenBookSelectPrimer + hero-primer 1장 + openingPlan/caution highlight + library-only evidence`만 닫는다.

**한 줄 목표:** `B-1 library`를 “붙였다”가 아니라 **어떤 evidence 순서로 닫고 누가 다음 B-2를 여는지**를 3~5분 안에 읽게 만든다.

---

## 이 문서가 필요한 이유
`B-1 library packet`은 무엇을 만들지 설명하고, `B-1 live kickoff packet`은 첫 10분 순서를 설명한다.
그런데 실제 리뷰 직전에는 여전히 아래가 남는다.

- 그래서 언제 `B-1 green`이라고 말하지?
- QA pass와 PM green은 뭐가 다르지?
- B-2 unlock은 언제 적지?

이 문서는 그 공백만 빠르게 정리한다.

---

## first screen 결론
- **프로그래머 제출**: `seenBookSelectPrimer`, hero-primer, highlight 2곳, state diff, manifest 초안이 있어야 한다.
- **UI/아트 sanity**: primer가 shelf를 먹지 않고 `첫 런 가이드`로 읽히는지만 본다.
- **QA pass**: B1-A1~A5, recovery 3종, CTA 비차단, library-only boundary가 확인돼야 한다.
- **PM green**: 위 evidence가 다 모였고 battle/reward/shop/ending이 아직 닫혀 있어야 한다.
- **B-2 unlock**: green 뒤에만 쓴다.

---

## review 범위 한 장 요약

| 포함 | 제외 |
| --- | --- |
| `seenBookSelectPrimer` 저장/정규화 | `seenBattlePrimer` 등 다른 primer flag |
| `library.book-select` hero-primer | battle/reward/shop/ending primer |
| `openingPlan` / `caution` highlight | shelf 전체 redesign |
| B1-A1~A5 / recovery 3종 | overrun flavor / ending helper |
| library-only boundary note | 온보딩 전체 진행 문장 |

---

## signoff 순서

| 단계 | 말할 수 있는 문장 | 아직 금지인 문장 |
| --- | --- | --- |
| review-ready | `B-1 review-ready` | `B-1 green`, `B-2 unlock` |
| visual sanity | `B-1 visual sanity ok` | `QA pass`, `green` |
| QA pass | `B-1 QA pass` | `B-2 unlock` |
| PM green | `B-1 library green` | `온보딩 진행`, `온보딩 완료` |
| next unlock | `다음 단계는 B-2 battle만 연다` | `reward/shop도 ready`, `ending도 같이 진행` |

---

## 필수 evidence bundle
- `manifest.md`
- acceptance log (`B1-A1~A5`, recovery 3종)
- programmer state diff note
- UI hierarchy note
- PM boundary note
- library capture 1~2장

### green 최소선
1. acceptance log에 `library-only`가 적혀 있다.
2. state diff note에 다른 primer flag 미추가가 적혀 있다.
3. boundary note에 `battle/reward/shop/ending 미오픈`이 적혀 있다.
4. PM이 green을 쓸 때까지 unlock 문장은 아직 없다.

---

## role-specific handoff points
- **프로그래머:** review-ready 전에는 green 문장을 쓰지 않는다.
- **UI:** hierarchy sanity는 density 확인이지 redesign 승인이 아니다.
- **아트:** token sanity만 본다. 신규 대형 자산은 green 조건이 아니다.
- **QA:** surface pass와 boundary pass를 같이 본다.
- **PM:** tracker/run log/portal 반영 전에는 B-2 unlock을 쓰지 않는다.

---

## 리스크 & 후속과제
- 가장 큰 리스크는 `B-1 review`와 `B-2 unlock`이 같은 문장으로 기록되는 것이다.
- 두 번째 리스크는 visual sanity가 신규 자산 blocker처럼 부풀어 오르는 것이다.
- 세 번째 리스크는 capture만 있고 state diff가 없어 `library-only`를 증명하지 못하는 것이다.

### immediate next actions
1. B-1 live turn 뒤에는 이 summary로 signoff 순서를 먼저 확인한다.
2. source packet에서 evidence와 금지 범위를 다시 확인한다.
3. PM green 뒤에만 `다음 단계는 B-2 battle만 연다`를 남긴다.

---

## source packet
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
