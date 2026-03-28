# DiceSpell `Run Table` Token Evidence Manifest Summary

## 3줄 요약
- 새 문서 `docs/dicespell_game_flow_token_evidence_manifest_kr.md`는 `Run Atlas / Battle Table / Overrun Oath` closing 증거를 **실제 파일 묶음**으로 어떻게 남길지 고정한다.
- 핵심은 `무엇이 closing인가` 다음 단계인 `그 closing을 어떤 archive / 파일명 / owner 제출물로 정리할까`를 잠그는 것이다.
- 읽는 순서는 `token closing packet -> token evidence manifest packet`이다.

## 한 줄 목표
Run Table token delivery를 캡처 몇 장이 아니라 **stage archive 3개 + 필수 파일 7종**으로 review 가능하게 만든다.

## 왜 필요한가
지금까지는 방향, 토큰, workboard, closing language는 충분했다. 그런데 실제 refinement 직전에는 여전히 아래 공백이 남아 있었다.

- 캡처는 어디에 어떤 이름으로 저장하지?
- hook 목록은 어떤 파일로 남기지?
- starter asset은 무엇까지만 제출하면 충분하지?
- tracker/run log용 한 줄 handoff 문장은 어디서 바로 복사하지?

이걸 안 잠그면 evidence가 DM, 임시 폴더, generic 파일명으로 흩어지고 closing review가 다시 느려진다.

## 이번 문서가 고정한 것

### 1) stage archive는 3개만 쓴다
- `atlas-route-node`
- `battle-stakes-plaque`
- `oath-banner`

### 2) 각 stage는 4묶음으로만 정리한다
- `captures/`
- `notes/`
- `checks/`
- `assets/`

### 3) surface마다 필수 파일이 같다
- `overview` 캡처 1장
- `focus` 캡처 1장
- `hook-manifest.md`
- `density-review.md`
- `handoff-line.md`
- `dom-sanity.md`
- `starter-asset-list.md`

## 역할별로 무엇을 보면 되나
| 역할 | 먼저 볼 것 | 바로 해야 할 일 |
| --- | --- | --- |
| 프로그래머 | `hook-manifest.md`, `dom-sanity.md` 규칙 | root/state/slot/role hook 파일 먼저 채우기 |
| UI 오너 | `overview/focus` 캡처 규칙 | 1440p 캡처 2장 + density review 1개 남기기 |
| 아트 오너 | `starter-asset-list.md` 규칙 | starter asset 범위/금지 범위만 먼저 제출 |
| QA / PM | `handoff-line.md` 규칙 | tracker/run log 문장을 archive 기준으로 복붙 |

## 좋은 변화
- atlas/battle/oath 증거가 한 폴더에 섞이지 않는다.
- 캡처-only 제출이 아니라 hook/review/asset note가 같이 묶인다.
- 다음 reviewer가 `무엇을 어디서 찾지?`보다 `이 증거로 pass/fail 가능한가?`에 바로 들어간다.

## 아직 금지
- `final.png`, `latest.png`, `misc-notes.md` 같은 generic 파일명
- 큰 배경/전체 리페인트를 starter asset처럼 제출
- atlas/battle/oath를 한 stage로 묶기
- 감상형 진행 보고만 남기고 `handoff-line.md`를 비워 두기

## 즉시 다음 액션
1. `docs/artifacts/run-table-token-delivery/` 아래에 stage archive 3개를 먼저 만든다.
2. 각 stage에 `hook-manifest.md`, `dom-sanity.md`, `handoff-line.md`부터 만든다.
3. 그 다음 UI 캡처와 density review를 넣는다.
4. starter asset은 list부터 제출하고 큰 자산은 아직 연다 말아야 한다.

## 한 장 결론
Run Table의 다음 병목은 더 이상 `무엇이 closing인가`가 아니라 **`그 closing을 어떤 파일 구조로 제출해야 다시 찾기 쉽고 과장 없이 기록되나`**다. 새 evidence manifest 문서는 그 마지막 실무 공백을 메우는 제출 계약서다.
