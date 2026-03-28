# DiceSpell `overrunEvent` 런타임 리허설 요약

## 3줄 요약
- 이 문서는 `Commit 1 floor`와 `Commit 2 closing` 문서가 이미 충분한 상태에서 남아 있던 마지막 실행 공백인 **"실제 live edit 직전에 팀이 뭘 같은 순서로 다시 잠가야 하지?"**를 빠르게 읽기 위한 요약본이다.
- 새 설계를 더하지 않고, 프로그래머/UI/아트/QA/PM이 **수정 시작 전 금지 범위와 제출 범위**를 같은 문장으로 확인하게 만드는 데 목적이 있다.
- 핵심은 `문서가 많다 = 바로 고친다`가 아니라 `문서가 많을수록 먼저 안 할 일을 잠근다`는 실행 discipline이다.

## 한 줄 목표
실제 cutover 직전 `Commit 1 floor`와 `Commit 2 closing`을 **말로 다시 잠근 뒤에만** 시작하게 만든다.

## 누가 무엇을 먼저 읽나
| 역할 | 먼저 읽을 것 | 바로 얻어야 하는 결론 |
| --- | --- | --- |
| 프로그래머 | `Commit 1/2 리허설` | 이번 커밋에서 아직 안 여는 것을 먼저 선언한다 |
| UI | `Commit 2 리허설`, `UI 리허설` | Commit 1은 freeze, Commit 2에서만 scene/result closing을 연다 |
| 아트 | `아트 리허설` | Commit 1은 token 확인만, visual 마감은 Commit 2만 본다 |
| QA | `QA 리허설`, `기록 규칙` | rehearsal note와 green record를 같은 문장으로 적지 않는다 |
| PM/기획 | `first screen 판정`, `기록 규칙` | `착수 가능`과 `완료`를 같은 초록불로 적지 않는다 |

## Commit 1 floor 리허설 핵심
### 3줄 요약
- Commit 1은 여전히 `enter 분리 + canonical snapshot floor + recovery smoke`까지만 닫는 턴이다.
- 이 턴에서 reward apply, page advance, segment cleanup, scene polish를 같이 열면 안 된다.
- run log에는 `Commit 1 floor`만 남고, overrun 전체 완료 문장은 금지다.

### 시작 전 5문장
1. 이번 턴은 overrunEvent enter와 snapshot floor까지만 닫는다.
2. reward apply와 progression은 아직 안 연다.
3. renderCenter는 explicit branch floor까지만 본다.
4. smoke는 S1/S2/S3/S7~S11만 본다.
5. 기록은 Commit 1 floor 문장만 쓴다.

## Commit 2 closing 리허설 핵심
### 3줄 요약
- Commit 2는 `scene card + confirm resolve + old immediate baseline 제거`를 같은 closing 묶음으로 닫는 턴이다.
- 이 턴도 끝나기 전에는 onboarding B-track, 새 polish 범위, ending flavor 확장을 열면 안 된다.
- green이 되려면 `보인다`가 아니라 `1회 적용 + resolved guard + closing smoke`까지 닫혀야 한다.

### 시작 전 5문장
1. Commit 1 floor green이 없으면 Commit 2를 열지 않는다.
2. 이번 턴은 scene card와 confirm resolve까지만 닫는다.
3. ending helper는 결과/CTA 의미만 말한다.
4. smoke는 S4/S5/S6/S10/S12만 본다.
5. Commit 2 green 전 onboarding은 닫혀 있다.

## 역할별 한 줄 리허설
- **프로그래머:** 못 찾은 line은 다시 찾되 범위는 넓히지 않는다.
- **UI:** Commit 1은 floor, Commit 2가 visual closing이다.
- **아트:** Commit 1은 token identity, Commit 2가 tone closing이다.
- **QA:** 리허설 완료와 실구현 완료를 같은 체크로 적지 않는다.
- **PM/기획:** `시작 가능`은 green이 아니다.

## 기록 규칙
- 허용: `Commit 1 floor 착수 전 rehearsal 완료`, `Commit 2 closing 착수 전 rehearsal 완료`
- 금지: `오버런 컷오버 재설계`, `오버런 전체 완료`, `온보딩 병행 착수`

## 즉시 다음 액션
1. 다음 구현자는 `readiness board -> semantic anchor packet -> surgery sheet -> rehearsal packet` 순서로 읽는다.
2. Commit 1 전에는 floor 금지 범위를 먼저 말로 잠그고 시작한다.
3. Commit 2 전에는 `Commit 1 green 확인 -> scene/result 경계 확인 -> closing smoke 범위 확인`을 먼저 잠근다.
4. green 문장은 기존 `Commit 1 floor` / `Commit 2 closing`만 쓴다.
