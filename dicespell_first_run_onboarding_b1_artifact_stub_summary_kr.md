# DiceSpell 첫 런 온보딩 `B-1 library` artifact stub summary

## 3줄 요약
- `B-1 live kickoff`, `required artifacts`, `review packet`이 있어도 실제 archive를 열면 가장 먼저 필요한 건 빈 파일이 아니라 **B-1 stage-specific 첫 문장**이다.
- 이 문서는 `acceptance log / programmer state diff / UI hierarchy / art token sanity / PM boundary` 5개 stub가 어떤 첫 줄과 어떤 금지 문장으로 시작해야 하는지 빠르게 읽게 만든다.
- 목표는 B-1 첫 turn이 다시 `온보딩 전체`, `추천 책`, `거의 완료` 같은 큰 문장으로 흐르지 않게 막는 것이다.

**한 줄 목표:** `B-1 library` archive를 manifest만 있는 폴더가 아니라, 시작부터 library-only boundary가 적혀 있는 starter kit로 연다.

---

## 왜 필요한가
- B-1은 문서가 충분한 대신 첫 실전 turn에서 wording drift가 가장 쉽게 생기는 surface다.
- `required artifacts 8종`이 정해져 있어도 실제 maker가 처음 보는 건 여전히 빈 log / 빈 note다.
- 그래서 `무엇을 제출할까` 다음 단계인 `첫 줄을 어떻게 시작할까`를 B-1 stage 언어로 다시 잠글 필요가 있다.

---

## first screen 결론
- B-1 starter kit은 `manifest + stub 5종 + capture reservation 2개`다.
- stub 5종은 아래다.
  1. QA acceptance log
  2. programmer state diff
  3. UI hierarchy note
  4. art token sanity note
  5. PM boundary note
- 두 capture는 실제 png가 아직 없어도 예약 문장부터 적어야 한다.
- `TODO`, `온보딩 진행`, `battle도 곧`, `거의 완료` 같은 문장이 보이면 아직 bootstrap도 끝나지 않았다.

---

## stub별 핵심 규칙

| stub | 첫 줄에 꼭 들어갈 말 | 아직 쓰면 안 되는 말 |
| --- | --- | --- |
| QA acceptance log | `이 로그는 B-1 library acceptance/recovery만 기록한다.` | battle/reward/shop/ending 검수 계획 |
| programmer state diff | `이번 diff는 B-1 library에서 seenBookSelectPrimer와 library hero-primer만 다룬다.` | 다른 seen flag 예열 |
| UI hierarchy note | `hero-primer 1장과 highlight 2곳까지만 확인한다.` | shelf 전체 리디자인 |
| art token sanity note | `안내로 읽히는지만 확인한다.` | 신규 대형 아트 패키지 요청 |
| PM boundary note | `B-1 library의 비확장선과 unlock 대기만 기록한다.` | `온보딩 진행`, `B-2도 사실상 ready` |

---

## 캡처 예약 규칙
- `captures/YYYYMMDD-b1-library-first-open.png`
  - hero-primer, body-copy, 카드 그리드 첫 줄, CTA가 한 화면 안에 읽히는지 증명
- `captures/YYYYMMDD-b1-library-return-state.png`
  - 재진입 또는 동등 정책에서 primer 재노출 제어와 highlight/CTA 유지가 library-only로 읽히는지 증명

캡처가 아직 없어도 note에는 이 파일명을 먼저 적어 둔다.

---

## 역할별 handoff 포인트
- 프로그래머: non-change line부터 적는다.
- UI: 예쁨 메모 대신 hierarchy / CTA 비차단만 적는다.
- 아트: 발주 메모가 아니라 `충분 / 약함 / 과함` verdict만 남긴다.
- QA: B1-A1~A5 / recovery 3종만 기록한다.
- PM: green 전에 unlock 문장을 쓰지 않는다.

---

## 즉시 다음 액션
1. `stage_manifest_b1_library.md`와 B-1 stub 5종을 먼저 복사한다.
2. first-open / return-state capture reservation 문장을 note에 넣는다.
3. 그 다음에만 실제 library-only edit와 evidence drop을 시작한다.
4. QA pass와 PM green은 starter bundle이 실제 artifact bundle로 채워진 뒤에만 쓴다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
