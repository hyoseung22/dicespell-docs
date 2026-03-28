# DiceSpell `overrunEvent` → 첫 런 온보딩 컷오버 요약

## 3줄 요약
- 이 문서는 A-track `Commit 1/2`와 B-track `B-1~B-4`를 **실행 큐 기준으로 다시 압축한 readable companion**이다.
- 프로그래머는 `무슨 순서로 닫는가`, UI/아트는 `언제 대기하고 언제 마감하는가`, PM/QA는 `어떤 green 문장을 남기는가`만 빠르게 찾으면 된다.
- 핵심은 새 spec 추가가 아니라, 이미 있는 runtime stack / scorecard / owner workboard를 실제 handoff 큐로 재사용하게 만드는 것이다.

## 한 줄 목표
`overrunEvent`는 Commit 1/2로 runtime-ready까지 닫고, 그 다음에만 첫 런 온보딩을 `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending` 순서로 연다.

## 왜 이 요약이 필요한가
기존 컷오버 요약은 큰 순서를 설명하는 데 충분했지만, 지금은 문서 세트가 더 구체화됐다.

- A-track에는 `runtime commit stack`, `handoff scorecard`, `owner workboard`가 있다.
- B-track에도 `runtime stack`, `handoff scorecard`, `owner workboard`가 있다.
- 그래서 이제 중요한 건 `무엇을 더 쓸까`가 아니라 **이 문서들을 어떤 순서로 실행할까**다.

## first screen 결론
- Commit 1과 Commit 2는 다른 green 문장을 남긴다.
- Commit 2 green 전에는 B-track을 열지 않는다.
- B-track은 반드시 `B-1 -> B-2 -> B-3 -> B-4` 순서로만 연다.
- `ending`은 결과/CTA 의미만 말하고, flavor는 끝까지 `overrunEvent`가 맡는다.

## 실행 큐 한눈표
| 단계 | 먼저 읽을 문서 | 한 줄 종료 문장 |
| --- | --- | --- |
| Commit 1 | `runtime patch map` -> `first runtime delivery` -> `runtime commit stack` -> `runtime handoff scorecard` -> `runtime owner workboard` | `이번 턴은 overrunEvent floor green이다.` |
| Commit 2 | `second runtime slice` -> `second runtime review` -> `runtime commit stack` -> `runtime owner workboard` | `이번 턴은 overrunEvent runtime-ready closing green이다.` |
| B-1 | `onboarding runtime stack` -> `handoff scorecard` -> `owner workboard` -> `B-1 packet` | `이번 턴은 library primer green이다.` |
| B-2 | 위 순서 + `B-2 packet` | `이번 턴은 battle primer green이다.` |
| B-3 | 위 순서 + `B-3 packet` | `이번 턴은 reward/shop primer green이다.` |
| B-4 | 위 순서 + `B-4 packet` | `이번 턴은 ending primer green이다.` |

## 역할별 빠른 판단
### 프로그래머
- source handoff 문서를 먼저 읽고 summary는 확인용으로만 쓴다.
- 한 커밋에 다음 단계 helper/state를 미리 섞지 않는다.

### UI / 아트
- Commit 1과 B-1은 마감 턴이 아니라 sanity/대기 턴이다.
- Commit 2와 B-3/B-4에서만 실제 readable closing을 닫는다.
- `overrunEvent`는 장면 카드, onboarding은 안내 레이어라는 차이를 끝까지 유지한다.

### QA / PM
- 증거와 green 문장은 항상 commit별·surface별로 따로 남긴다.
- `온보딩 전체 green`, `오버런 거의 완료` 같은 문장은 금지한다.

## 리스크 & 후속과제
- 가장 큰 리스크는 여전히 문서 부족이 아니라 **범위 부풀리기**다.
- Commit 1/2를 같은 문장으로 기록하면 half-cutover가 runtime-ready처럼 보인다.
- B-1~B-4를 같은 handoff 문장으로 기록하면 owner workboard가 무너지고 ending boundary가 다시 흐려진다.

## 즉시 다음 액션
1. 다음 writer 구현은 A-track Commit 1부터 닫는다.
2. Commit 2 closing review green 전에는 B-track을 열지 않는다.
3. B-track을 열 때는 `runtime stack -> handoff scorecard -> owner workboard -> B packet` 순서를 그대로 따른다.
