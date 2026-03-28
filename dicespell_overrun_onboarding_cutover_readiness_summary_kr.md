# DiceSpell `overrunEvent` / 첫 런 온보딩 컷오버 준비 보드 요약

## 3줄 요약
- 이 문서는 `overrunEvent` A-track과 온보딩 B-track이 이미 handoff-ready한 지금, **누가 지금 시작 가능하고 누가 아직 기다려야 하는지**를 빠르게 보여 주는 readable companion이다.
- 새 설계를 더하지 않고 `Commit 1 floor -> Commit 2 closing -> B-1/B-2/B-3/B-4`를 착수 가능 상태, 제출물, 금지 범위 기준으로 다시 압축한다.
- 첫 화면만 읽어도 프로그래머/UI/아트/QA/PM이 이번 턴의 자기 역할을 바로 판단할 수 있어야 한다.

## 한 줄 목표
문서 세트를 `많이 읽어야 하는 묶음`이 아니라 **이번 턴 시작/대기 판단 보드**로 바꾼다.

## 왜 이 문서가 필요한가
지금 DiceSpell의 문제는 설계 부족보다 `그래서 지금 누가 움직여도 되는가`가 여러 packet에 흩어져 있다는 점이다.

- 프로그래머는 Commit 1만 바로 열 수 있다.
- UI/아트는 Commit 1에서 검토만 하고, Commit 2부터 closing에 들어간다.
- B-track 온보딩은 Commit 2 green 뒤에만 `B-1 -> B-2 -> B-3 -> B-4` 순서로 열린다.
- PM/QA는 단계별로 다른 green 문장과 증거 묶음을 남겨야 한다.

---

## 지금 누가 무엇을 해야 하나

| 역할 | 지금 해야 할 일 | 아직 하면 안 되는 일 |
| --- | --- | --- |
| 프로그래머 | Commit 1 floor 착수 | Commit 2 resolve/UI closing, B-track primer 동시 구현 |
| UI | Commit 2 visual contract 검토 준비 | Commit 1에서 scene card 선마감 |
| 아트 | token/accent sanity 준비 | 새 대형 자산 착수 |
| QA | Commit 1 floor evidence 준비 | Commit 1을 closing green처럼 기록 |
| PM | 단계명/append log 규칙 준비 | `오버런 거의 완료`, `온보딩 진행` 같은 큰 문장 기록 |

---

## 단계별 readiness 한눈표

| 단계 | 상태 | 이번 단계 산출물 | 다음 단계로 넘기는 증거 |
| --- | --- | --- | --- |
| Commit 1 floor | 지금 즉시 시작 가능 | `overrunEvent` 진입, snapshot floor, recovery, non-change line 유지 | `S1/S2/S3/S7/S8/S9/S10/S11` + 상태 diff |
| Commit 2 closing | Commit 1 green 뒤 시작 | scene card, confirm 1회 적용, `resolved` guard, old baseline 제거 | `S4/S5/S6/S10/S12` + DOM/시각 캡처 |
| B-1 library | Commit 2 green 뒤 시작 | library hero-primer + seen flag | B1 acceptance/recovery |
| B-2 battle | B-1 green 뒤 시작 | battle entry/first-spell/auto-pass primer | B2 acceptance/recovery |
| B-3 reward/shop | B-2 green 뒤 시작 | reward/shop 목적 분리 primer | B3 acceptance/recovery |
| B-4 ending | B-3 green 뒤 시작 | 결과/CTA 의미 helper + boundary 유지 | B4 acceptance/recovery + boundary evidence |

---

## 역할별 한 줄 핸드오프
- **프로그래머**: 지금은 Commit 1만 열려 있다. 파일을 열더라도 책임은 Commit 1 범위 밖으로 넘기지 않는다.
- **UI**: Commit 1은 대기 검토 턴, Commit 2와 B-track이 실제 closing 턴이다.
- **아트**: 대부분의 단계는 새 대형 자산보다 token/accent sanity로 닫는다.
- **QA**: 각 단계별 증거 묶음을 섞지 않는다.
- **PM**: 단계명 하나와 evidence 하나로만 기록한다.

---

## 리스크 & 후속과제
- 문서가 많아서 오히려 시작 가능 단계가 흐려질 수 있다.
- Commit 1에서 UI/아트가 너무 일찍 마감을 시작하면 floor 경계가 무너진다.
- Commit 2 green 전에 B-track을 열면 온보딩이 잘못된 baseline 위에 올라간다.
- handoff-ready와 runtime-ready를 같은 green 문장으로 쓰면 다음 implementer가 범위를 오판한다.

---

## 즉시 다음 액션
1. `cutover gate -> runtime commit stack -> readiness board` 순서로 읽고 Commit 1만 시작한다.
2. Commit 1 green 뒤에만 Commit 2를 연다.
3. Commit 2 green 뒤에만 B-1부터 surface별로 연다.
4. run log는 새 항목을 끝에 append하고, 단계별 문장만 사용한다.

---

## 바로 가기
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_overrun_cutover_gate_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
