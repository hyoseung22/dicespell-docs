# DiceSpell `overrunEvent` / 첫 런 온보딩 실행 기록 계약 요약

## 3줄 요약
- 이 문서는 `overrunEvent` A-track Commit 1/2와 온보딩 B-track B-1~B-4가 실제로 진행될 때, tracker / automation run log / 공개 포털 / git 기록이 **같은 단계 언어**를 쓰게 만드는 readable companion이다.
- 핵심은 새 설계를 더하는 것이 아니라, 이미 있는 commit stack / scorecard / workboard / gate 문서를 **실행 기록 계약**으로 다시 묶는 것이다.
- `Commit 1 floor`, `Commit 2 closing`, `B-1`, `B-2`, `B-3`, `B-4`는 절대 같은 green 문장으로 합치지 않는다.

**한 줄 목표:** 단계별 구현·검수·기록을 같은 단계명, 같은 evidence 묶음, 같은 append-only 로그 규칙으로 잠가 half-cutover 과장을 막는다.

## 누가 먼저 봐야 하나
- **프로그래머**: 이번 턴 evidence와 기록 문장을 같이 닫아야 할 때
- **UI/아트**: Commit 1이 검수 대기 턴인지, Commit 2/B-track이 실제 시각 green 턴인지 구분할 때
- **QA/PM**: smoke/acceptance 결과와 tracker/run log 문장을 같은 번들로 맞출 때
- **자동화 writer**: run log를 append-only로 안전하게 남기고 tracker/public docs를 최소 위험으로 갱신할 때

## 첫 화면 결론
- 모든 단계는 **단계명 1개 + evidence 묶음 1개 + run log entry 1개 + tracker 변경 1개**로 닫는다.
- run log는 항상 **끝에 새 timestamped entry를 append**한다.
- `Commit 1 floor`와 `Commit 2 closing`을 `오버런 green` 같은 큰 문장으로 합치지 않는다.
- B-track도 `온보딩 진행`처럼 뭉개지 말고 `B-1`, `B-2`, `B-3`, `B-4`를 따로 기록한다.

## 단계별 기록 계약표
| 단계 | evidence 묶음 | tracker 핵심 문장 | run log 주제 |
| --- | --- | --- | --- |
| Commit 1 floor | `S1/S2/S3/S7/S8/S9/S10/S11`, 상태 diff, non-change line | `overrunEvent floor green` + reward/page/onboarding 미개방 | `overrunEvent Commit 1 floor 실행 기록 정렬` |
| Commit 2 closing | `S4/S5/S6/S10/S12`, DOM/시각 evidence, resolve 1회 적용 | `runtime-ready closing green` + 다음은 `B-1`만 | `overrunEvent Commit 2 closing 실행 기록 정렬` |
| B-1 | B1 acceptance/recovery | `B-1 library primer green` + 다음은 `B-2` | `onboarding B-1 library primer 실행 기록 정렬` |
| B-2 | B2 acceptance/recovery | `B-2 battle primer green` + 다음은 `B-3` | `onboarding B-2 battle primer 실행 기록 정렬` |
| B-3 | B3 acceptance/recovery | `B-3 reward/shop primer green` + 다음은 `B-4` | `onboarding B-3 reward/shop primer 실행 기록 정렬` |
| B-4 | B4 acceptance/recovery + boundary evidence | `B-4 ending primer green` + overrun flavor는 계속 `overrunEvent`에 남김 | `onboarding B-4 ending primer 실행 기록 정렬` |

## evidence 묶음 규칙
- **Commit 1**: `floor green`, non-change line 필수
- **Commit 2**: `closing green`, resolve 1회 적용 + `resolved` guard + old baseline 제거 필수
- **B-track**: surface별 evidence를 서로 섞지 않는다
- 제출 순서는 `smoke/acceptance -> 상태 또는 UI evidence -> 금지 범위 미개방 메모 -> tracker 문장 -> run log append`가 기본이다

## tracker / run log / 포털 규칙
### tracker
- 최소 갱신 위치: `현재 스냅샷`, `현재 blocker`, `리스크 & 후속과제`, `변경 이력`
- 문장 안에는 반드시 단계명과 다음 허용 단계 1개가 들어간다

### run log
- 새 run은 항상 맨 아래 새 섹션으로 append한다
- 이전 entry 대형 rewrite는 피한다
- 실패/보류도 사실대로 적는다

### public docs
- `docs/development-tracker.html`은 tracker와 같은 사이클에 동기화한다
- `docs/index.html`은 새 pair가 첫 화면에서 바로 보여야 할 때만 갱신한다
- public tracker/index/GDD가 바뀌면 포털 재배포까지 같은 사이클에서 묶는다

## 복붙용 기록 문장
> 이번 턴은 `overrunEvent` Commit 1 floor green이다. `S1/S2/S3/S7/S8/S9/S10/S11`을 같은 evidence 묶음으로 닫았고, reward apply / page advance / onboarding은 아직 열지 않았다.

> 이번 턴은 `overrunEvent` Commit 2 runtime-ready closing green이다. `S4/S5/S6/S10/S12`를 같은 evidence 묶음으로 닫았고, 다음은 `B-1 library primer`만 연다.

> 이번 턴은 `B-1 library primer green`이다. library surface만 닫았고 다음은 `B-2 battle primer`만 연다.

> 이번 턴은 `B-4 ending primer green`이다. ending은 결과/CTA 의미만 닫았고, overrun flavor는 계속 `overrunEvent`에 남긴다.

> 이번 턴은 `실행 기록 계약` 문서화 green이다. 다음 구현/검수/기록은 새 계약의 단계명·evidence·append 규칙만 사용한다.

## 리스크 & 후속과제
- 문서가 많을수록 오히려 기록이 `대충 큰 green 문장`으로 축약될 위험이 커진다.
- `Commit 1 floor`와 `Commit 2 closing`은 코드보다 기록에서 더 쉽게 섞인다.
- B-track도 설계보다 기록 언어가 더 쉽게 흐려진다.
- 다음 슬롯의 최우선은 여전히 A-track 실제 코드 컷오버다. 다만 그 턴부터는 tracker/run log를 이 문서의 단계명과 복붙 문장으로만 남기는 쪽이 맞다.

## 바로 다음 액션
1. 다음 슬롯 착수 세트에 `cutover gate + runtime commit stack + execution reporting packet`을 같이 넣는다.
2. tracker/run log에서 큰 green 문장을 금지하고 단계명만 사용한다.
3. run log는 append-only로 유지한다.
4. 실제 A-track 코드 컷오버부터 이 계약을 적용한다.
