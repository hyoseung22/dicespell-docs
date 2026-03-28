# DiceSpell `B-1 library` handoff scorecard 요약

## 3줄 요약
- 새 문서 `docs/dicespell_first_run_onboarding_b1_handoff_scorecard_kr.md`는 B-1 archive를 실제로 열 때 **누가 어떤 순서로 무엇을 제출하고, 어디서 review-ready / QA pass / PM green / B-2 unlock을 나누는지**를 한 장으로 고정한다.
- 목적은 새 primer 규칙을 더하는 것이 아니라, 이미 충분한 B-1 문서 세트를 **실제 owner handoff 순서 + 복붙용 기록 문장**으로 다시 압축하는 것이다.
- 이제 B-1은 `artifact는 모였는데 누가 어떤 문장을 써야 하지?`를 감으로 처리하지 않고, `프로그래머 바닥 -> UI/아트 evidence -> QA pass -> PM green` 순서로만 닫는다.

## 한 줄 목표
B-1 첫 실전 archive를 `무엇을 만들지`에서 끝내지 않고, **누가 언제 무엇을 제출하고 어떤 문장으로 닫을지**까지 handoff-ready하게 만든다.

## 왜 중요한가
지금까지도 아래는 이미 있었다.
- B-1 범위 packet
- live kickoff 순서
- archive bootstrap 규칙
- starter stub / manifest / semantic anchor
- required artifacts 8종
- filled archive exemplar
- review / signoff packet

하지만 실제 첫 archive 직전에는 여전히 이런 질문이 남아 있었다.
- 프로그래머, UI, 아트, QA, PM은 정확히 어떤 순서로 제출해야 하나?
- review-ready와 QA pass와 PM green은 누가 쓰는가?
- tracker/run log/git에는 어떤 문장을 그대로 남겨야 하는가?

이번 문서는 그 마지막 운영 공백을 **owner별 scorecard + handoff ladder + 기록 문장**으로 닫는다.

## 가장 중요한 결론
### 1) 제출 순서는 5단계다
1. 프로그래머가 `manifest + state diff + boundary note`로 archive 바닥을 잠근다.
2. UI가 `first-open / return-state capture + hierarchy note`를 낸다.
3. 아트가 `primer-library` 충분성 sanity note를 낸다.
4. QA가 `review-ready`와 `QA pass`를 분리 기록한다.
5. PM이 마지막으로 `B-1 library green`과 `B-2 battle만 다음 허용 단계`를 기록한다.

### 2) 먼저 오면 안 되는 제출물이 있다
- capture가 state/boundary note보다 먼저 오면 안 된다.
- review-ready와 QA pass는 같은 문장이 아니다.
- QA는 green을 기록하지 않는다.
- PM은 review-ready나 pass를 반복하지 않고 마지막 green/unlock만 남긴다.

### 3) B-1에서 여전히 금지인 것
- battle/reward/shop/ending primer 예열
- 추천/정답 책처럼 읽히는 아트 연출
- `온보딩 진행`, `거의 완료`, `B-2도 사실상 ready` 같은 큰 문장
- archive 경로 없이 green만 먼저 쓰는 기록

## owner별 한 줄 결론
| 역할 | 이 문서에서 가져갈 핵심 |
| --- | --- |
| 프로그래머 | `manifest + state diff + boundary note`가 먼저 잠기기 전에는 stage가 아직 archive-in-progress다 |
| UI | first-open / return-state capture는 서로 다른 질문의 증거여야 하며 green 문장을 대신하지 않는다 |
| 아트 | 이번 stage 산출물은 `primer-library` 충분성 verdict이지 신규 key art 요구가 아니다 |
| QA | `review-ready`와 `QA pass`를 분리 기록하고, green은 PM 단계 전까지 보류한다 |
| PM | `B-1 library green. 다음 허용 단계는 B-2 battle뿐이다.`만 마지막 기록으로 남긴다 |

## 복붙용 기록 문장
- archive 바닥 생성: `B-1 library archive를 열고 manifest + state/boundary 바닥을 먼저 잠갔다.`
- review-ready: `B-1 review-ready: artifact 8종과 B-1 library wording alignment를 확인했고 battle/reward/shop/ending primer는 아직 열지 않았다.`
- QA pass: `B-1 QA pass: B1-A1~A5와 recovery 3종, CTA 비차단, library-only boundary 유지가 확인됐다.`
- PM green: `B-1 library green. 다음 허용 단계는 B-2 battle archive 생성뿐이다.`

## 리스크 & 후속과제
- capture가 먼저 올라오고 state/boundary note가 늦어지면 stage contract가 비어 있는 채 green이 과장될 수 있다.
- QA pass와 PM green이 같은 초록불로 기록되면 B-2 unlock이 너무 빨리 열린다.
- art sanity가 추천/정답 책 연출 요구로 커지면 B-1이 다시 큰 visual polish 단계로 부푼다.

## 즉시 다음 액션
1. 실제 B-1 archive를 열 때는 exemplar보다 먼저 이 scorecard로 제출 순서를 잠근다.
2. 프로그래머 바닥 제출 전에는 capture/hierarchy/token sanity를 받지 않는다.
3. QA는 `review-ready`와 `QA pass`를 분리 기록한다.
4. PM만 마지막 `green + B-2 unlock`을 기록한다.

## 원본 문서
- source handoff: `./dicespell_first_run_onboarding_b1_handoff_scorecard_kr.md`
- B-1 required artifacts: `./dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- B-1 filled archive examples: `./dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md`
- B-1 review packet: `./dicespell_first_run_onboarding_b1_review_packet_kr.md`
