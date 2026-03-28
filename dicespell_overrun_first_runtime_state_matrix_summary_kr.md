# DiceSpell `overrunEvent` 첫 런타임 상태 전이 매트릭스 요약

## 3줄 요약
- 이 문서는 `first runtime slice`를 구현할 때 가장 중요한 `지금 바뀌어야 하는 값`과 `아직 바뀌면 안 되는 값`을 빠르게 읽기 위한 companion view다.
- 핵심은 `continueOverrun()` 직후 reward apply나 page progression이 일어나지 않은 채, `overrunEvent` canonical snapshot만 안전하게 생겨야 한다는 점이다.
- 프로그래머는 state diff를, QA는 smoke별 기대 상태를, UI/아트는 payload freeze 시점을 먼저 보면 된다.

## 한 줄 목표
첫 실제 코드 슬라이스를 `보여 주기 전의 저장 가능 상태`로 정확히 고정한다.

## 왜 이 문서가 필요한가
문서는 이미 충분하다. 그런데 구현 직전엔 여전히 `phase만 바꾸면 된 건가?`, `reward 적용도 같이 남아도 되나?`, `UI는 언제부터 payload를 믿어도 되나?` 같은 질문이 남는다. 이 요약은 그 마지막 흔들림을 줄이기 위한 빠른 읽기용 기준선이다.

---

## 이번 슬라이스에서 반드시 참이어야 하는 것
- `phase === 'overrunEvent'`
- canonical snapshot 존재
- `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`, `resolved=false` 존재
- legacy/missing/invalid 상태가 유효 snapshot 또는 `none`으로 normalize 가능

## 이번 슬라이스에서 아직 참이면 안 되는 것
- reward effect 적용 완료
- `currentPage` 증가
- `pagePlan` 증가
- `segmentTrophyTemplateIds` 초기화
- 다음 세그먼트 자동 진입
- `resolved === true`

---

## before → after 핵심 표

| 항목 | 첫 슬라이스 후 기대값 | 실패 신호 |
| --- | --- | --- |
| `phase` | `overrunEvent` | `library`/`battle`로 즉시 이동 |
| `state.overrunEvent` | canonical snapshot 존재 | partial state, 빈 state |
| reward effect | 여전히 미적용 | 이미 적용됨 |
| `currentPage` / `pagePlan` | 그대로 유지 | 값 증가 |
| `segmentTrophyTemplateIds` | 그대로 유지 | 빈 배열로 초기화 |
| `resolved` | `false` | `true` 선적용 |
| invalid/missing/legacy | `none` 또는 안전 snapshot으로 수렴 | crash, silent fallback, render 추론 의존 |

---

## smoke별 빠른 체크
- `S-A1-ENTER`: `phase='overrunEvent'`, reward 미적용
- `S-A1-NONE`: skip/invalid에서도 `none` branch 생성
- `S-A2-CANONICAL`: canonical field set 8개 존재
- `S-A2-LEGACY`: thin state가 canonical snapshot으로 수렴
- `S-A2-MISSING`: 누락 state가 안전하게 복구
- `S-A2-INVALID`: invalid reward가 `none`으로 강등

---

## owner별 한 줄 메모

### 프로그래머
`continueOverrun()`는 enter와 snapshot까지만 닫는다. progression까지 같이 남기면 실패다.

### QA
예쁜 화면보다 먼저 reward 미적용, page 미전진, canonical snapshot 존재를 본다.

### UI
A-3 준비는 canonical field 8개가 엔진 snapshot에 직접 들어온 뒤에만 시작한다.

### 아트
이번 턴은 자산 제작이 아니라 `artTokenSet`/`accentKey` freeze 확인 턴이다.

---

## 하지 말아야 할 것
1. phase만 `overrunEvent`로 바꾸고 old progression을 그대로 두는 것
2. title/sourceType로 badge/accent/header를 다시 판정하는 것
3. partial snapshot을 placeholder UI로 덮는 것
4. recovery를 happy path 뒤로 미루는 것

---

## 바로 다음 액션
1. 구현 턴에서 이 요약과 `first runtime slice packet`, `runtime patch map`을 같이 열어 범위를 선언한다.
2. 리뷰 때는 `무엇이 바뀌었나`만 보지 말고 `무엇이 아직 안 바뀌었나`를 함께 대조한다.
3. 위 상태 기준이 닫힌 뒤에만 A-3 UI와 A-4 resolve로 넘어간다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 첫 런타임 슬라이스를 `phase 전환 + canonical snapshot + reward 미적용 유지`라는 정확한 state diff로 빠르게 읽게 만드는 companion view다.
