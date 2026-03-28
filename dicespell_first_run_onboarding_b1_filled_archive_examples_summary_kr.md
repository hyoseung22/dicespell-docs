# DiceSpell `B-1 library` 채워진 archive 예시집 요약

## 3줄 요약
- 새 문서 `docs/dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md`는 B-1 starter bundle 규칙을 실제로 채워진 8종 artifact 완성본 예시까지 내려 준다.
- 목적은 `좋아, manifest/log/note/capture는 알겠는데 실제 문장 밀도는 어디까지가 honest하지?`라는 마지막 공백을 없애는 것이다.
- `manifest 1 + log 1 + note 4 + capture 2`가 같은 `library-only` 언어를 공유하는 예시라서, review-ready / QA pass / PM green / B-2 unlock 문장이 다시 섞이지 않게 한다.

## 한 줄 목표
B-1 archive를 읽는 사람이 규칙을 이해하는 데서 멈추지 않고, **바로 복붙 가능한 filled bundle exemplar**까지 확보하게 만든다.

## 왜 중요한가
지금까지는 이미 아래가 있었다.
- live kickoff 순서
- starter stub 첫 줄
- starter manifest 5필드
- semantic anchor 5축
- required artifacts 8종
- archive bootstrap verdict ladder
- review/signoff 계층

하지만 실제 첫 archive 직전에는 여전히 이런 질문이 남아 있었다.
- 이 artifact 8종은 실제로 얼마나 짧고 정확하게 써야 하나?
- 어떤 줄은 tracker/run log에 그대로 가져다 써도 되는가?
- 무엇이 있으면 `review-ready candidate`이고, 무엇이 아직 too generic한가?

이번 문서는 그걸 **filled archive 예시 8종 + 재사용 규칙**으로 닫는다.

## 누가 어떤 부분을 보면 되나
| 역할 | 먼저 볼 부분 | 핵심 결론 |
| --- | --- | --- |
| 프로그래머 | manifest / state diff 예시 | `seenBookSelectPrimer + hero-primer + openingPlan/caution + other surfaces locked`를 한 문장으로 같이 말해야 한다 |
| UI | hierarchy / capture 예시 | first-open / return-state는 서로 다른 질문의 증거여야 한다 |
| 아트 | art sanity 예시 | 아트 note는 충분성 verdict이지 대형 자산 요구 목록이 아니다 |
| QA | acceptance log 예시 | artifact가 다 있어도 wording drift가 있으면 archive-in-progress로 내려야 한다 |
| PM/기획 | handoff line 재사용 규칙 | tracker/run log는 exemplar의 `handoff line`과 `next allowed step`에 가깝게 쓰는 편이 안전하다 |

## 가장 중요한 예시 포인트
### manifest / state diff
- `review-ready-candidate`처럼 honest한 상태어를 쓴다.
- 다른 primer flag 미오픈을 같은 비중으로 적는다.
- semantic locator까지 적어 line drift를 막는다.

### UI / capture
- `first-open`은 hero-primer + CTA 비차단 질문에 답한다.
- `return-state`는 재노출 제어 질문에 답한다.
- hierarchy note는 예쁜 장면 평가가 아니라 읽힘 순서만 판정한다.

### 아트 / PM / QA
- 아트는 `primer-library`가 안내 표식인지 판정만 남긴다.
- PM은 `battle/reward/shop/ending 미오픈`과 `B-2 battle만 다음 허용 단계`를 못 박는다.
- QA는 첫 줄부터 `이 로그는 B-1 library만 기록한다`고 범위를 잠근다.

## 추가 산출물
- `docs/artifacts/examples/b1-library/README.md`
- `docs/artifacts/examples/b1-library/stage_manifest_b1_library_example.md`
- `docs/artifacts/examples/b1-library/stage_log_b1_library_acceptance_example.md`
- `docs/artifacts/examples/b1-library/stage_note_b1_library_programmer_state_diff_example.md`
- `docs/artifacts/examples/b1-library/stage_note_b1_library_ui_hierarchy_example.md`
- `docs/artifacts/examples/b1-library/stage_note_b1_library_art_token_sanity_example.md`
- `docs/artifacts/examples/b1-library/stage_note_b1_library_pm_boundary_example.md`
- `docs/artifacts/examples/b1-library/stage_capture_b1_library_first_open_example.md`
- `docs/artifacts/examples/b1-library/stage_capture_b1_library_return_state_example.md`

즉 source handoff + readable summary뿐 아니라 실제 복사 가능한 B-1 archive exemplar 묶음까지 같이 갖췄다.

## 리스크 & 후속과제
- exemplar가 없으면 첫 실전 archive에서 다시 `온보딩 진행` 같은 큰 문장이 돌아오기 쉽다.
- capture만 있고 note들이 서로 다른 stage를 말하면 review-ready 판단이 다시 감각에 의존한다.
- tracker/run log가 generic progress 문장으로 돌아가면 archive 완성도가 외부 기록에서 사라진다.

## 즉시 다음 액션
1. 다음 B-1 turn 전에는 요약이 아니라 원본 예시집과 example 파일 8종을 같이 연다.
2. 실제 archive drop은 exemplar보다 느슨한 문장으로 `review-ready candidate`를 주장하지 않는다.
3. tracker/run log/portal에는 가능하면 exemplar `handoff line`을 원문에 가깝게 재사용한다.

## 원본 문서
- source handoff: `./dicespell_first_run_onboarding_b1_filled_archive_examples_kr.md`
- archive bootstrap: `./dicespell_first_run_onboarding_b1_archive_bootstrap_packet_kr.md`
- required artifacts: `./dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- review packet: `./dicespell_first_run_onboarding_b1_review_packet_kr.md`
