# DiceSpell 채워진 매니페스트 예시집 요약

## 3줄 요약
- 새 문서 `docs/dicespell_overrun_onboarding_filled_manifest_examples_kr.md`는 `manifest fill packet`의 규칙을 실제로 채워진 manifest 완성본 예시까지 내려 준다.
- 목적은 템플릿과 규칙 사이의 마지막 공백, 즉 `좋아, 그런데 첫 실전 archive에서는 문장을 어느 밀도로 써야 하지?`를 없애는 것이다.
- Commit 1 / Commit 2 / B-1~B-4 각각의 `owner summary`, `boundary`, `next allowed step`, `handoff line`이 tracker/run log/git까지 그대로 재사용될 수 있는 수준으로 정리돼 있다.

## 한 줄 목표
starter manifest를 읽는 사람이 규칙을 이해하는 데서 멈추지 않고, **바로 복붙 가능한 stage exemplar**까지 확보하게 만든다.

## 왜 중요한가
지금까지는 아래가 이미 있었다.
- archive root 규칙
- starter manifest 템플릿
- 필드별 작성 계약

하지만 실제 첫 archive 직전에는 여전히 이런 질문이 남아 있었다.
- Commit 1과 Commit 2 handoff line은 실제로 얼마나 다르게 써야 하나?
- B-1~B-4는 어디까지를 `archive-ready` 문장으로 써도 되나?
- tracker/run log에 그대로 가져다 써도 과장되지 않는 문장은 어떤 모양인가?

이번 문서는 그걸 **채워진 예시 6종**으로 닫는다.

## 누가 어떤 부분을 보면 되나
| 역할 | 먼저 볼 부분 | 핵심 결론 |
| --- | --- | --- |
| 프로그래머 | Commit 1 / Commit 2 예시 | floor와 closing은 다른 문장으로 기록해야 한다 |
| UI | B-1~B-4 예시 | capture 파일명보다 owner summary가 먼저 surface 역할을 말해야 한다 |
| 아트 | B-4, Commit 2 예시 | tone note는 감상문이 아니라 token/boundary sanity여야 한다 |
| QA | A-track + B-track 예시 | 로그는 항상 stage 문장과 같이 움직여야 한다 |
| PM/기획 | 복붙/재사용 규칙 | handoff line은 tracker/run log/git의 원문 후보여야 한다 |

## 가장 중요한 예시 포인트
### Commit 1
- `archive-in-progress`
- reward apply / page advance / scene closing 금지
- `다음은 Commit 2만 연다`를 명시

### Commit 2
- `archive-ready`
- scene / resolve / boundary evidence 완료
- `다음 허용 단계는 B-1 library뿐`이라고 못 박음

### B-track
- `surface 1개 = manifest 1개`
- `온보딩 진행` 같은 큰 문장 금지
- B-4 ending은 overrun scene flavor를 먹지 않는 boundary를 반드시 적음

## 추가 산출물
- `docs/artifacts/examples/README.md`
- `docs/artifacts/examples/stage_manifest_commit1_floor_example.md`
- `docs/artifacts/examples/stage_manifest_commit2_closing_example.md`
- `docs/artifacts/examples/stage_manifest_b1_library_example.md`
- `docs/artifacts/examples/stage_manifest_b2_battle_example.md`
- `docs/artifacts/examples/stage_manifest_b3_reward_shop_example.md`
- `docs/artifacts/examples/stage_manifest_b4_ending_example.md`

즉 포털에서 읽는 source packet + readable summary뿐 아니라, 실제 복사 가능한 artifact 예시 파일까지 같이 갖췄다.

## 리스크 & 후속과제
- exemplar가 없으면 첫 실전 archive에서 다시 stage 문장이 흔들릴 수 있다.
- B-track이 `온보딩 완료` 같은 큰 문장으로 합쳐지면 surface acceptance가 무너진다.
- owner summary를 비우고 capture만 남기면 handoff-ready가 아니라 파일 보관만 남는다.

## 즉시 다음 액션
1. 다음 Commit 1 또는 B-1 착수 전 이 요약이 아니라 원본 예시집과 대응 example manifest를 함께 연다.
2. tracker/run log/git에는 가능한 한 예시집의 `handoff line`을 원문에 가깝게 재사용한다.
3. 실제 archive drop이 생기면 exemplar보다 느슨한 문장으로 green을 주장하지 않는다.

## 원본 문서
- source handoff: `./dicespell_overrun_onboarding_filled_manifest_examples_kr.md`
- 작성 규칙 원본: `./dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
- archive bootstrap: `./dicespell_overrun_onboarding_archive_bootstrap_packet_kr.md`
