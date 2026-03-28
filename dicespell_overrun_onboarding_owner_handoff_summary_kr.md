# DiceSpell `overrunEvent` → 첫 런 온보딩 오너 핸드오프 요약

## 3줄 요약
- 이 문서는 A-track `Commit 1/2`와 B-track `B-1~B-4`를 **오너별 실제 전달 묶음**으로 다시 압축한 readable companion이다.
- 프로그래머는 `이번 턴에 어떤 문서/파일/증거를 같이 잠그는가`, UI/아트는 `지금이 검수 턴인가 마감 턴인가`, PM/QA는 `어떤 green 문장을 어떤 evidence bundle과 함께 남기는가`만 빠르게 찾으면 된다.
- 핵심은 새 설계를 더하는 것이 아니라, 이미 있는 stack / scorecard / owner workboard를 사람 handoff 언어까지 묶어 실행 드리프트를 줄이는 것이다.

## 한 줄 목표
각 오너가 `지금 읽을 문서`, `이번 턴 산출물`, `아직 열면 안 되는 범위`, `기록 문장`을 한 번에 가져가게 만든다.

## first screen 결론
- Commit 1은 프로그래머/QA/PM handoff까지만 green이다.
- Commit 2에서만 UI/아트 closing handoff가 열린다.
- B-track은 반드시 `B-1 -> B-2 -> B-3 -> B-4` 순서다.
- B-4에서도 `ending`은 결과/CTA 의미만 맡고, flavor는 끝까지 `overrunEvent`에 남긴다.

## 오너별 빠른 전달표
| 역할 | 지금 먼저 읽을 문서 | 이번 턴에 넘길 것 | 아직 넘기면 안 되는 것 |
| --- | --- | --- | --- |
| 프로그래머 | 각 track의 `runtime stack/commit stack + scorecard + owner workboard + step packet` | 단계별 코드 diff와 bounded 상태 변경 | 다음 단계 helper/state 선반영 |
| UI | `screen packet`, `content deck`, `owner workboard` | Commit 1/B-1/B-2는 sanity, Commit 2/B-3/B-4는 readable closing | 여러 surface를 한 번에 마감 |
| 아트 | `production cutsheet`, `content deck`, `owner workboard` | token/accent/shell drift 통제 | 대형 신규 자산을 지금 턴 목표로 열기 |
| QA | `review/acceptance + scorecard + owner workboard` | 단계별 evidence bundle과 pass/fail | 여러 단계/여러 surface를 같은 evidence로 pass 처리 |
| PM/기획 | `commit/runtime stack + scorecard + owner workboard` | 단계별 green 문장 기록 | `온보딩 green`, `오버런 거의 완료` 같은 뭉뚱그린 문장 |

## 단계별 한눈표
| 단계 | 실제 handoff 대상 | 종료 문장 |
| --- | --- | --- |
| Commit 1 | 프로그래머, QA, PM | `이번 턴은 overrunEvent floor green이다.` |
| Commit 2 | 프로그래머, UI, 아트, QA, PM | `이번 턴은 overrunEvent runtime-ready closing green이다.` |
| B-1 | 프로그래머, UI, QA, PM | `이번 턴은 library primer green이다.` |
| B-2 | 프로그래머, UI, QA, PM | `이번 턴은 battle primer green이다.` |
| B-3 | 프로그래머, UI, 아트, QA, PM | `이번 턴은 reward/shop primer green이다.` |
| B-4 | 프로그래머, UI, 아트, QA, PM | `이번 턴은 ending primer green이다.` |

## 왜 이 요약이 필요한가
지금 병목은 문서 부족이 아니다.

- 문서는 이미 많고 구체적이다.
- 문제는 실제 handoff 순간에 오너들이 다시 `이번 턴엔 뭘 건네지?`를 트랙별 문서 여러 장에서 재조합하게 되는 점이다.
- 그러면 Commit 1/2와 B-1~B-4가 다시 큰 묶음 문장으로 합쳐진다.

이 요약은 그걸 막기 위한 빠른 전달본이다.

## 리스크 & 후속과제
- Commit 1에서 UI/아트가 마감처럼 움직이면 half-cutover가 완성처럼 보인다.
- B-track에서 PM이 surface별 green 문장을 합치면 owner workboard 의미가 사라진다.
- B-4에서 ending이 overrun flavor를 흡수하면 boundary packet이 무너진다.

## 즉시 다음 액션
1. 다음 writer 구현은 Commit 1부터 닫는다.
2. Commit 2 closing review green 전에는 B-track을 열지 않는다.
3. B-track은 `runtime stack -> handoff scorecard -> owner workboard -> 해당 B packet` 순서로만 연다.
4. tracker/run log에는 단계별 green 문장을 그대로 복붙한다.
