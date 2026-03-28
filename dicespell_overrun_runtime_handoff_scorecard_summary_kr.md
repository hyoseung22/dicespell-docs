# DiceSpell `overrunEvent` 런타임 핸드오프 스코어카드 요약

## 3줄 요약
- 이 문서는 `overrunEvent` A-track을 실제 구현/리뷰 턴에서 바로 쓰는 **readable companion**이다.
- 핵심은 Commit 1과 Commit 2를 다른 완료 문장으로 기록하게 만드는 것이다.
- Commit 2가 green이 되기 전에는 onboarding B-track을 열지 않는다.

## 한 줄 목표
`이번 턴에 어디까지 닫고 어떤 문장을 남기면 되는가`를 3~5분 안에 이해하게 만든다.

## 누가 봐야 하나
- **프로그래머**: 이번 커밋이 Commit 1인지 Commit 2인지 먼저 구분해야 할 때
- **QA/PM**: tracker/run log에 어떤 완료 문장을 남겨야 하는지 빠르게 확인할 때
- **UI/아트**: 지금 턴이 shell sanity만 보는 턴인지, 아직 polish를 하면 안 되는 턴인지 확인할 때

## Commit 1 — enter / snapshot floor
### 왜 존재하나
중간 상태를 안전하게 만들기 위한 커밋이다. `overrunEvent`로 들어가고, canonical snapshot을 저장하고, recovery smoke를 green으로 만든다.

### 여기서 끝나야 하는 것
- `phase='overrunEvent'` 진입
- canonical field set 저장
- legacy/invalid/missing -> safe recovery
- non-change line 유지 (`reward` / `currentPage` / `pagePlan` / `segmentTrophyTemplateIds`는 아직 그대로)

### 여기서 하면 안 되는 것
- reward apply
- page advance
- cleanup
- onboarding helper/state 추가

### 남길 문장
> 이번 커밋은 `overrunEvent`의 enter 분리와 canonical snapshot floor만 닫았고, reward apply와 page advance는 아직 열지 않았다.

## Commit 2 — scene / resolve closing
### 왜 존재하나
중간 상태를 실제 플레이 가능한 장면과 적용 시점으로 닫는 커밋이다. 장면 카드가 보이고, confirm CTA에서만 정확히 한 번 넘어가야 한다.

### 여기서 끝나야 하는 것
- `renderOverrunEvent(summary)` 장면 카드
- `themeTrophy` / `enemyTrophy` / `none` 3축 정상 렌더
- confirm 시 reward 1회 적용
- `resolved` reload guard
- old immediate baseline 제거

### 여기서 하면 안 되는 것
- onboarding primer 주입
- 새 reward 타입 추가
- 전역 카드 시스템 리뉴얼

### 남길 문장
> 이번 커밋은 `overrunEvent`의 scene card와 confirm resolve를 닫아, reward 1회 적용·`none` 무보상 진행·`resolved` reload guard·old immediate baseline 제거까지 runtime closing을 마무리했다.

## 제출 순서
1. smoke 결과
2. 상태 diff 메모
3. UI evidence 또는 DOM evidence
4. 코드 diff 요약
5. 한 줄 handoff 문장

## 역할별 체크 포인트
| 역할 | 지금 봐야 하는 것 |
| --- | --- |
| 프로그래머 | 이번 턴이 Commit 1인지 Commit 2인지부터 고정 |
| QA | green 문장을 Commit 1/2로 분리 |
| UI | Commit 2에서도 shell sanity까지만 |
| 아트 | `accentKey` / `artTokenSet` drift만 확인 |
| PM/기획 | Commit 2 pass 전 onboarding 착수 보고 금지 |

## 리스크 & 후속과제
- Commit 1과 Commit 2 기록 문장이 섞이면 half-cutover가 완성처럼 보일 수 있다.
- 제출 순서가 제각각이면 다음 implementer가 같은 evidence bundle을 재사용하기 어렵다.
- Commit 2 green 전 onboarding을 열면 A-track/B-track 원인 추적이 섞인다.

## 바로 다음 액션
1. `runway -> runtime patch map -> first runtime delivery -> second runtime slice -> second runtime review -> runtime commit stack -> scorecard` 순서로 읽는다.
2. 먼저 Commit 1만 닫고 green 문장을 기록한다.
3. 그다음 Commit 2를 닫고 closing 문장을 기록한다.
4. 그 뒤에만 onboarding B-1을 연다.
