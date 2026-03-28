# DiceSpell 증거 스텁 킷 요약

## 3줄 요약
- 새 문서 `docs/dicespell_overrun_onboarding_artifact_stub_packet_kr.md`는 archive root, starter manifest, filled exemplar 다음에 남아 있던 마지막 작은 실전 공백, 즉 `logs/`와 `notes/`의 첫 문장을 무엇으로 시작할지`를 고정한다.
- 핵심은 빈 파일이나 generic placeholder 대신, stage 책임과 금지 범위를 먼저 적는 **honest stub**로 evidence 바닥을 까는 것이다.
- 이번에는 source packet뿐 아니라 `docs/artifacts/stubs/` 아래에 Commit 1 / Commit 2 / B-1~B-4용 텍스트 stub starter 파일도 같이 추가해 바로 복사·수정 가능한 형태로 맞췄다.

## 한 줄 목표
stage archive를 `폴더는 있는데 본문은 빈 상태`로 두지 않고, **stage-specific stub + manifest exemplar** 조합으로 첫 3분 안에 같은 기록 언어를 깔게 만든다.

## 왜 중요한가
지금까지는 아래가 이미 있었다.
- archive root 규칙
- starter manifest 템플릿
- manifest 작성 계약
- 채워진 exemplar

하지만 실제 첫 live edit 직전에는 여전히 이런 질문이 남아 있었다.
- state diff note 첫 줄에 무엇을 적어야 하지?
- boundary note는 언제부터 써 두어야 하지?
- 캡처가 아직 없을 때는 어디에 예약 문장을 남기지?
- log/note 파일이 빈 상태면 reviewer가 `bootstrap-ready`와 `archive-in-progress`를 어떻게 구분하지?

이번 문서는 그걸 **text stub 규칙 + 복사 가능한 starter file**로 닫는다.

## 누가 어떤 부분을 보면 되나
| 역할 | 먼저 볼 부분 | 핵심 결론 |
| --- | --- | --- |
| 프로그래머 | Commit 1 / Commit 2 stub | state diff와 resolve diff는 빈 파일이 아니라 비변경선/범위 선언으로 시작한다 |
| UI | capture reservation rule | fake PNG를 만들지 않고 text stub와 manifest에 예약한다 |
| 아트 | B-4 tone/boundary stub | tone note는 감상문이 아니라 token/boundary sanity로 시작한다 |
| QA | QA log stub | fixture 번호와 not-in-scope를 첫 줄에 적는다 |
| PM/기획 | handoff line guard | green 문장은 manifest 전용이고 stub는 근거만 말한다 |

## 이번에 같이 생긴 실제 파일
- `docs/artifacts/stubs/README.md`
- `docs/artifacts/stubs/stage_stub_commit1_floor_smoke_log.md`
- `docs/artifacts/stubs/stage_stub_commit1_floor_state_diff.md`
- `docs/artifacts/stubs/stage_stub_commit1_floor_nonchange_note.md`
- `docs/artifacts/stubs/stage_stub_commit2_closing_smoke_log.md`
- `docs/artifacts/stubs/stage_stub_commit2_closing_resolve_diff.md`
- `docs/artifacts/stubs/stage_stub_commit2_closing_boundary_note.md`
- `docs/artifacts/stubs/stage_stub_b1_guidance_state.md`
- `docs/artifacts/stubs/stage_stub_b2_trigger_note.md`
- `docs/artifacts/stubs/stage_stub_b3_purpose_boundary.md`
- `docs/artifacts/stubs/stage_stub_b4_boundary_note.md`
- `docs/artifacts/stubs/stage_stub_b4_tone_note.md`

## 리스크 & 후속과제
- stub가 없으면 manifest는 좋아 보여도 실제 note/log 본문이 `TODO`, `later`, `final` 같은 placeholder로 다시 drift할 수 있다.
- fake capture를 만들면 evidence처럼 보이기 쉬우므로, capture는 계속 manifest + reservation text로만 잠근다.
- handoff line을 stub까지 적기 시작하면 완료 문장이 여러 버전으로 갈라지므로, green 문장은 manifest와 run log에만 남겨야 한다.

## immediate next actions
1. 다음 Commit 1/Commit 2 착수 전 exemplar manifest와 대응 stub 파일을 함께 연다.
2. 빈 `notes/`와 `logs/` 파일을 만들지 말고 `docs/artifacts/stubs/` 파일을 복사해 첫 문장부터 stage 책임을 적는다.
3. 캡처가 아직 없으면 manifest와 stub의 `capture reservation` 줄만 먼저 채운다.
4. `archive-ready` 문장은 여전히 manifest와 run log에서만 확정한다.

## 원본 문서
- source handoff: `./dicespell_overrun_onboarding_artifact_stub_packet_kr.md`
- archive packet: `./dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
- filled manifest examples: `./dicespell_overrun_onboarding_filled_manifest_examples_kr.md`
