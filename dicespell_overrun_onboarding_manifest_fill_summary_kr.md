# DiceSpell `overrunEvent` / 첫 런 온보딩 매니페스트 작성 요약

## 3줄 요약
- 이 문서는 starter manifest 템플릿을 실제 handoff-ready 문장까지 채울 때 필요한 최소 규칙만 빠르게 읽는 companion이다.
- 핵심은 `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line`을 감각 문장이 아니라 단계 계약 문장으로 쓰는 것이다.
- 프로그래머 / UI / 아트 / QA / PM 모두 `capture만 올리고 문장을 비워 두는 상태`를 완료처럼 읽지 않게 만드는 데 목적이 있다.

## 한 줄 목표
manifest를 메모장이 아니라 **stage contract 초안**으로 쓴다.

## 왜 이 문서가 필요한가
- 템플릿과 폴더 구조는 이미 있다.
- 하지만 실제 drift는 대개 `필드에 무슨 문장을 써야 하지?`에서 생긴다.
- 특히 `handoff line`이 느슨하면 tracker / run log / git 단계 언어가 바로 흔들린다.

## 먼저 읽어야 할 사람
- **프로그래머**: Commit 1/2 note를 그냥 작업 메모가 아니라 floor/closing 계약 문장으로 남겨야 한다.
- **UI/아트**: capture 파일명만 맞추지 말고 owner summary와 tone/boundary note도 함께 채워야 한다.
- **QA/PM**: `archive-ready`는 테스트가 있었다는 뜻이 아니라 required artifacts와 handoff line이 stage 언어로 잠겼다는 뜻이다.

## 필드별 최소 규칙

| 필드 | 이렇게 쓴다 | 이렇게 쓰지 않는다 |
| --- | --- | --- |
| `status` | `bootstrap-ready`, `archive-in-progress`, `archive-ready` | `거의 완료`, `대충 끝남` |
| `owner summary` | owner별 닫힌 범위 + 아직 안 연 범위 | 감상, 칭찬, 역할 밖 판단 |
| `boundary / non-change` | 이번 stage 금지선 2~4개 | 일반 메모, 다음 아이디어 |
| `blockers / notes` | 실제 blocker / 확인 필요점 | 비변경선 |
| `next allowed step` | 다음 stage 개방 조건 1줄 | 막연한 희망사항 |
| `handoff line` | archive 경로 + stage 언어 + evidence 묶음 | vague green 문장 |

## 바로 재사용할 문장 감각
- 좋음: `Commit 1 floor archive-in-progress: docs/artifacts/YYYYMMDD/commit1-floor/ snapshot floor evidence drop 완료, closing 항목은 아직 금지.`
- 좋음: `B-4 ending archive-ready: docs/artifacts/YYYYMMDD/b4-ending/ ending boundary evidence 완료.`
- 나쁨: `거의 완료`, `온보딩 진행`, `이제 감이 잡혔다`

## 역할별 핵심 포인트
- **프로그래머**: Commit 1은 `아직 안 연 것`, Commit 2는 `이번에 제거한 것`을 써야 한다.
- **UI**: capture 파일명과 owner summary가 같은 surface boundary를 말해야 한다.
- **아트**: tone note는 감상문이 아니라 token/accent/boundary sanity여야 한다.
- **QA**: 로그 존재 여부만 보지 말고, 그 로그가 올바른 stage 문장에 묶였는지 본다.
- **PM**: handoff line을 tracker / run log / git에 재사용할 수 있어야 한다.

## 금지 패턴
- `거의 완료`
- `문제 없음`
- `온보딩 진행`
- `다음은 아마 B-2`
- `UI도 같이 보면 됨`

## 바로 다음 액션
1. 다음 archive 생성 시 템플릿 복사 뒤 가장 먼저 `status`와 `handoff line`을 쓴다.
2. `boundary / non-change`와 `blockers / notes`를 섞지 않는다.
3. tracker / run log 기록은 가능하면 manifest handoff line을 그대로 재사용한다.
4. source packet이 아니라 summary만 보고 manifest를 채우지 않는다.

## source packet
- `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
