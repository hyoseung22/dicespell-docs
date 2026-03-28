# DiceSpell `overrunEvent` 런타임 커밋 스택 요약

## 3줄 요약
- 이 문서는 `first runtime delivery`와 `second runtime slice`를 실제 **2커밋 실행 순서**로 압축한 readable companion이다.
- 핵심은 `Commit 1 = enter/snapshot floor`, `Commit 2 = scene/resolve closing`을 같은 언어로 보게 만드는 것이다.
- 즉 새 설계를 더하는 문서가 아니라, 이미 충분한 A-track 문서를 실제 착수 순서로 묶는 빠른 안내서다.

## 한 줄 목표
`overrunEvent`를 두 번의 bounded commit으로 끝내고, Commit 1의 중간 상태와 Commit 2의 최종 상태를 서로 섞어 말하지 않게 만든다.

## 왜 먼저 읽나
문서는 이미 충분하다. 그런데 실제 구현 직전에는 이런 공백이 남는다.

- 첫 커밋은 어디서 멈춰야 하지?
- 두 번째 커밋은 정확히 무엇으로 닫아야 하지?
- UI/QA는 언제부터 `완성`이라는 말을 써도 되지?
- tracker/run log에는 어떤 문장을 남겨야 하지?

이 문서는 그 공백을 `2커밋 스택`이라는 한 문장으로 정리한다.

---

## 커밋 스택 한눈에 보기

| 구분 | Commit 1 | Commit 2 |
| --- | --- | --- |
| 역할 | `A-1 enter 분리 + A-2 canonical snapshot floor` | `A-3 scene card UI + A-4 confirm resolve` |
| 핵심 질문 | `무엇이 바뀌었고 무엇이 아직 안 바뀌었는가?` | `이제 장면/적용/다음 세그먼트가 정확히 한 번 성립하는가?` |
| 반드시 하지 말 것 | reward apply, page advance, cleanup, onboarding 변경 | onboarding B-track, 새 reward 타입, 전역 리디자인 |
| 완료 문장 | `enter+snapshot floor까지만 닫았다.` | `scene+resolve closing까지 닫았다.` |

---

## Commit 1에서 닫는 것
- `continueOverrun()`를 즉시 적용 경로에서 `overrunEvent` 진입 wrapper로 바꾼다.
- canonical field set을 snapshot에 직접 저장한다.
- invalid/missing/legacy를 `none` fallback 또는 safe recovery로 모은다.
- reward는 아직 적용하지 않는다.
- `currentPage`, `pagePlan`, `segmentTrophyTemplateIds`도 아직 바꾸지 않는다.

### Commit 1이 pass인 상태
- `phase='overrunEvent'`가 들어온다.
- snapshot 표정이 reload 후에도 유지된다.
- non-change line이 문서와 smoke 증거로 남는다.

### Commit 1이 fail인 상태
- enter 직후 reward가 이미 적용된다.
- UI가 문자열을 다시 추론한다.
- 상태 diff는 불명확한데 smoke만 green이다.

---

## Commit 2에서 닫는 것
- `renderOverrunEvent(summary)` 장면 카드 shell을 닫는다.
- `confirm-overrun-event`와 `resolveOverrunEvent(state)`를 연결한다.
- reward 1회 적용, `none` 무보상 진행, `resolved` guard, cleanup을 닫는다.
- old immediate baseline을 제거한다.

### Commit 2가 pass인 상태
- `themeTrophy` / `enemyTrophy` / `none` 3축이 모두 정상 장면으로 읽힌다.
- confirm CTA에서만 reward가 정확히 한 번 적용된다.
- reload/reclick에서도 중복 적용이 없다.
- ending과 `overrunEvent`가 다른 화면 책임으로 읽힌다.

### Commit 2가 fail인 상태
- confirm 전 적용되거나, confirm 후 중복 적용된다.
- `none`이 placeholder처럼 비어 있다.
- closing commit에 onboarding이나 다른 메카닉이 같이 들어온다.

---

## 역할별로 뭘 보면 되나

| 역할 | Commit 1에서 볼 것 | Commit 2에서 볼 것 |
| --- | --- | --- |
| 프로그래머 | enter/snapshot/normalize/non-change line | scene/resolve/cleanup/closing smoke |
| UI | Commit 1에서는 shell 욕심 금지 | Commit 2에서 ending과 다른 focus card/tone 마감 |
| 아트 | Commit 1에서는 freeze만 확인 | Commit 2에서 token/accent drift와 `none` density 확인 |
| QA | enter/recovery + 비변경선 검수 | A3/A4 smoke + old baseline 제거 검수 |
| PM/기획 | `중간 상태`라고 기록 | `runtime closing`이라고 기록 |

---

## evidence bundle
- Commit 1: enter/recovery smoke + 상태 diff + non-change line 메모 + handoff 문장
- Commit 2: A3/A4 smoke + confirm 전/후 diff + UI evidence + closing 문장

### tracker / run log 권장 문장
- Commit 1: `이번 커밋은 overrunEvent의 enter 분리와 canonical snapshot floor만 닫았고, reward apply와 page advance는 아직 열지 않았다.`
- Commit 2: `이번 커밋은 overrunEvent의 scene card와 confirm resolve를 닫아, reward 1회 적용·none 무보상 진행·resolved reload guard·old immediate baseline 제거까지 runtime closing을 마무리했다.`

---

## 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 |
| --- | --- | --- |
| Commit 1이 완성 상태처럼 기록되는 위험 | half-cutover가 pass처럼 보인다 | 커밋별 handoff 문장을 분리 |
| Commit 2에 onboarding이 섞이는 위험 | closing 범위가 무너진다 | 2커밋 외 작업 금지 명시 |
| non-change line 누락 위험 | 중간 상태 증거가 사라진다 | Commit 1 evidence bundle에 상태 diff 포함 |
| `none` branch 약화 위험 | recovery 신뢰가 떨어진다 | Commit 2 pass 조건에 3축 density 유지 포함 |

---

## 즉시 다음 액션
- implementer는 Commit 1만 먼저 닫는다.
- review는 Commit 1을 `중간 상태`로 기록한다.
- Commit 1 green 뒤에만 Commit 2를 연다.
- Commit 2 pass 뒤에만 onboarding B-track을 `B-1 -> B-2 -> B-3 -> B-4` 순서로 연다.

## 한 줄 결론
이 문서는 `overrunEvent` 문서 묶음을 실제 **2커밋 runtime stack**으로 요약해, 다음 구현자가 `어디서 멈추고 어디서 끝내는가`를 같은 문장으로 실행하게 만드는 readable companion이다.
