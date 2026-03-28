# DiceSpell `overrunEvent` 런타임 픽스처 레저 요약

## 3줄 요약
- 이 문서는 `S1~S12` 픽스처를 `Commit 1 floor`와 `Commit 2 closing`에 어떻게 배치해 읽어야 하는지 빠르게 보여 주는 **readable companion**이다.
- 핵심은 새 설계가 아니라, 이미 있는 smoke/state/review/scorecard 문서를 `같은 픽스처 언어`로 묶어 구현/리뷰/기록 드리프트를 줄이는 것이다.
- 한마디로: **Commit 1은 enter/recovery, Commit 2는 scene/resolve, `none`과 `resolved` guard는 절대 보조 케이스가 아니다.**

**한 줄 목표:** 오버런 컷오버 실구현 직전, `이번 커밋이 어떤 픽스처 세트를 닫는지`를 프로그래머/UI/아트/QA/PM이 3분 안에 같은 문장으로 읽게 만든다.

## 왜 이 문서를 먼저 봐야 하나
- 문서는 충분한데, 실제 턴에서는 여전히 `S4가 Commit 1인가?`, `UI는 뭘 캡처하지?`, `PM은 뭐라고 기록하지?`가 다시 흔들린다.
- 이 요약은 그 흔들림을 줄이기 위해 픽스처를 **커밋/오너/증거** 기준으로 다시 압축한다.

## first screen 결론
| 구분 | Commit 1 floor | Commit 2 closing |
| --- | --- | --- |
| 닫아야 할 픽스처 | `S1/S2/S3/S7/S8/S9/S10/S11` | `S4/S5/S6/S10/S12` |
| 핵심 의미 | 진입 + canonical snapshot + recovery 수렴 | 장면 카드 + confirm 1회 적용 + baseline 제거 |
| 프로그래머가 남길 것 | smoke + non-change line + enter/recovery handoff 문장 | smoke + resolve diff + closing handoff 문장 |
| UI/아트가 남길 것 | `S11` 최소 DOM evidence만 | `S12-theme/enemy/none` + ending 비교 캡처 |
| PM이 기록할 말 | `enter/recovery floor` | `scene/resolve closing` |
| 아직 하면 안 되는 것 | reward apply, page advance, onboarding | onboarding B-track 조기 착수 |

## 누구에게 무엇을 넘기나
### 프로그래머
- Commit 1에서는 `S1/S2/S3/S7~S11`만 green이어야 정상이다.
- Commit 2에서는 `S4/S5/S6/S10/S12`만 green으로 닫는다.
- smoke/helper/review 코멘트에 `S번호`를 그대로 남긴다.

### UI / 아트
- `none`은 보조 케이스가 아니라 필수 화면이다.
- Commit 2 제출물은 `theme/enemy/none` 3장 + `ending vs overrun` 비교 1장으로 충분하다.
- 지금 턴은 대형 리디자인이 아니라 shell/tone/signifier sanity 턴이다.

### QA
- `S7~S11`은 recovery 옵션이 아니라 생존선이다.
- Commit 1 green과 Commit 2 green을 한 코멘트로 합치지 않는다.

### PM / 기획
- Commit 1 green 뒤에는 `Commit 2만 열 수 있다`까지만 기록한다.
- Commit 2 green 뒤에만 onboarding `B-1 library`를 다음 액션으로 올린다.

## 리스크 & 후속과제
- `S번호` 없이 기록하면 문서/코드/QA가 다시 엇갈린다.
- Commit 1에서 S12까지 욕심내면 floor와 closing이 다시 섞인다.
- `none`을 보조 상태로 취급하면 fallback 장면이 비어 보인다.
- Commit 1/2를 같은 green 문장으로 쓰면 half-cutover가 완성처럼 보인다.

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_runtime_fixture_ledger_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
