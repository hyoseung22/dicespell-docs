# DiceSpell `overrunEvent` 두 번째 런타임 슬라이스 리뷰 요약

## 3줄 요약
- 이 문서는 `second runtime slice`가 정말 닫혔다고 말하기 위한 **closing review 요약본**이다.
- 핵심은 `장면 카드가 보인다`가 아니라 `confirm 1회 적용 + resolved guard + old immediate baseline 제거`까지 같이 pass해야 한다는 점이다.
- 즉 Commit 2는 단순 UI polish가 아니라 `overrunEvent`를 runtime-ready로 끝내는 마지막 검수선이다.

## 한 줄 목표
`A-3 scene card UI + A-4 confirm resolve` 커밋을 같은 evidence bundle로 검수해, 마지막 closing commit이 half-cutover나 과장된 완료 보고로 흐르지 않게 만든다.

## 왜 먼저 읽나
`second runtime slice packet`은 범위를 정리해 준다. 그런데 리뷰 직전에는 여전히 이런 공백이 남는다.

- 장면 카드가 보이면 정말 끝난 건가?
- confirm은 한 번만 적용된다는 걸 무엇으로 확인하지?
- `none` branch와 invalid/reload는 어디까지 pass여야 하지?
- 이제 onboarding B-track을 열어도 되는가?

이 문서는 그 질문을 `closing review 기준` 한 장으로 압축한다.

---

## 이번 리뷰에서 봐야 할 것

| 범주 | pass 기준 | fail 신호 |
| --- | --- | --- |
| UI shell | `badge -> header -> flavor -> reward summary -> CTA -> footnote` 유지 | 결과표/보상 선택 화면처럼 읽힘 |
| 3축 branch | `themeTrophy` / `enemyTrophy` / `none` 모두 정상 장면 | `none`이 placeholder처럼 비어 있음 |
| resolve 시점 | confirm CTA에서만 reward 1회 적용 | confirm 전 적용, reclick/reload 중복 적용 |
| recovery | invalid/missing/reload/resolved가 safe fallback/no-op | 진행 막힘, drift, 재적용 |
| baseline | old immediate baseline 제거 | 옛 즉시 적용 흐름이 코드나 smoke에 남음 |
| 범위 통제 | onboarding/helper/새 메카닉 미포함 | closing commit이 다시 부풀음 |

---

## evidence bundle
- 코드 diff: A-3 shell + A-4 resolve + baseline 제거가 같은 커밋 경계에 있어야 한다.
- 상태 diff 메모: confirm 전/후 `resolved`, reward 1회 적용, cleanup이 보여야 한다.
- smoke 결과: A3-S1~S5, A4-S4~S12, old immediate baseline 제거가 함께 green이어야 한다.
- UI evidence: theme/enemy/none 3축 카드 위계와 ending 대비 shell 차이가 보여야 한다.
- 한 줄 결론: `이번 커밋으로 scene+resolve closing을 닫았다.`

---

## tracker / run log 권장 문장
> 이번 커밋은 `overrunEvent`의 단일 장면 카드와 confirm resolve를 함께 닫아, reward 1회 적용·`none` 무보상 진행·`resolved` reload guard·old immediate baseline 제거까지 포함한 runtime closing을 마무리했다.

---

## 역할별로 무엇을 보면 되나

| 역할 | 이번 리뷰에서 확인할 것 |
| --- | --- |
| 프로그래머 | confirm 전 미적용, confirm 후 1회 적용, reload/reclick guard |
| UI | ending과 다른 shell, 3축 공통 위계, stale CTA 제거 |
| 아트 | token/accent drift 없음, `none`도 정상 장면 밀도 유지 |
| QA | A3/A4 smoke + recovery + old baseline 제거까지 한 번에 pass |
| PM/기획 | 이제부터만 onboarding B-track을 열 수 있다는 점 |

---

## pass면 무엇이 달라지나
- `overrunEvent`는 문서-ready가 아니라 runtime-ready 상태로 닫힌다.
- 이제 onboarding B-track을 `B-1 -> B-2 -> B-3 -> B-4` 순서로만 열 수 있다.
- tracker/run log에서 Commit 1과 Commit 2를 다른 완료 문장으로 기록할 수 있다.

## fail이면 무엇을 해야 하나
- 새 문서를 더 쓰지 않는다.
- second runtime slice 범위 안에서만 되돌린다.
- confirm 전 적용, 중복 적용, `none` density 붕괴, baseline 잔존 중 무엇이 문제인지 먼저 분리한다.

## 한 줄 결론
이 문서는 `second runtime slice`의 마지막 검수선을 요약해, `장면이 보이는 것`과 `runtime closing이 끝난 것`을 같은 말로 섞지 않게 만드는 readable companion이다.
