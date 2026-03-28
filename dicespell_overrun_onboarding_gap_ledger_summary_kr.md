# DiceSpell `overrunEvent` / 첫 런 온보딩 구현 갭 레저 요약

## 3줄 요약
- 이 문서는 `overrunEvent` A-track과 첫 런 온보딩 B-track에서 **무엇이 이미 문서로 닫혔고, 무엇이 아직 실제 구현 갭으로 남아 있는지**를 빠르게 읽기 위한 companion view다.
- 핵심은 새 설계 문서를 더 찾는 게 아니라, 다음 턴이 `문서 보강`이 아니라 `Commit 1/2 + B-1~B-4 gap closure`임을 한 화면에서 확인하게 만드는 것이다.
- 프로그래머는 file-bound code gap을, UI/아트는 closing 시점 gap을, QA/PM은 evidence/기록 gap을 바로 보면 된다.

**한 줄 목표:** 문서 완료와 구현 완료를 분리해, 다음 실행이 `A-track Commit 1/2 -> B-track B-1~B-4` 순서의 실제 gap closure로 바로 이어지게 만든다.

---

## first screen

### 지금 가장 중요한 결론
- `overrunEvent`는 **문서 blocker가 거의 없다.** 남은 것은 `current immediate-apply baseline -> runtime-ready overrunEvent` 전환이다.
- 온보딩도 **surface 설계 blocker가 거의 없다.** 남은 것은 A-track 이후 `surface 1개 = commit 1개`로 실제 primer를 심는 일이다.
- 따라서 다음 구현 턴의 기본 질문은 `무슨 문서를 더 읽지?`보다 `어떤 갭을 지금 닫지?`다.

### 누가 무엇부터 읽어야 하나
| 역할 | 먼저 볼 것 | 바로 얻어야 하는 결론 |
| --- | --- | --- |
| 프로그래머 | A-track gap 표 | Commit 1/2는 code gap이고 문서 gap이 아니다 |
| UI | A-track / B-track UI gap | Commit 1은 대기, Commit 2와 B-track만 closing |
| 아트 | branch/token gap | 새 대형 자산보다 drift 방지가 우선 |
| QA | evidence/기록 gap | 문서 green과 runtime green을 다른 bundle로 기록 |
| PM | 문서 완료 vs 구현 완료 표 | 추가 문서보다 bounded execution 관리가 우선 |

---

## 한눈표: 남은 실제 갭

| 단계 | 이미 닫힌 것 | 아직 남은 실제 갭 | 닫힘 증거 |
| --- | --- | --- | --- |
| Commit 1 floor | A-1/A-2 packet, first runtime slice/state/review/delivery, patch map | `overrunEvent` 진입 wrapper + canonical snapshot + recovery + non-change line | `S1/S2/S3/S7/S8/S9/S10/S11` + floor 문장 |
| Commit 2 closing | A-3/A-4 packet, second runtime slice/review, commit stack, workboard | scene card + confirm resolve + `resolved` guard + old baseline 제거 | `S4/S5/S6/S10/S12` + closing 문장 |
| B-1 | library packet/summary/scorecard/workboard | library hero-primer + seen flag + 비차단 유지 | B1 acceptance/recovery |
| B-2 | battle packet/summary/scorecard/workboard | entry/first-spell/auto-pass primer 주입 | B2 acceptance/recovery |
| B-3 | reward/shop packet/summary/scorecard/workboard | reward/shop 목적 분리 primer 주입 | B3 acceptance/recovery |
| B-4 | ending packet/summary/boundary packet | 결과/CTA 의미 primer만 추가, overrun flavor 비침범 | B4 acceptance/recovery + boundary evidence |

---

## A-track 요약

### 왜 아직 blocker인가
현재 코드는 아직 `continueOverrun()` 즉시 적용 baseline에 가깝다. 문서는 runtime-ready까지 충분하지만, 실제 구현은 아직 `Commit 1 floor -> Commit 2 closing`이 안 닫혔다.

### 오너별 handoff 포인트
- **프로그래머**: `game-engine.js` / `app.js` / `styles.css` / `smoke-test.mjs`의 runtime gap을 Commit 1/2로 나눠 닫는다.
- **UI**: Commit 1에서 shell을 닫지 않는다. Commit 2에서만 card hierarchy를 닫는다.
- **아트**: `themeTrophy` / `enemyTrophy` / `none` token/accent drift만 먼저 본다.
- **QA**: Commit 1은 floor, Commit 2는 closing으로 다른 evidence bundle을 쓴다.
- **PM**: `floor green`과 `closing green`을 절대 같은 문장으로 합치지 않는다.

---

## B-track 요약

### 왜 아직 blocker가 아닌가
온보딩은 지금 설계 부족보다 실행 discipline 문제가 더 크다. 즉 남은 위험은 `못 정해서 못 만드는 것`이 아니라 `한 번에 여러 surface를 붙여서 다시 흐려지는 것`이다.

### 오너별 handoff 포인트
- **프로그래머**: A-track Commit 2 green 전에는 B-track을 열지 않는다.
- **UI**: primer가 본문을 삼키지 않도록 surface별 밀도만 닫는다.
- **아트**: ending에서도 overrun flavor 자산을 끌어오지 않는다.
- **QA/PM**: B-1/B-2/B-3/B-4를 각각 다른 green 문장으로 기록한다.

---

## 문서 완료 vs 구현 완료

| 항목 | 문서 완료 | 구현 완료 |
| --- | --- | --- |
| `overrunEvent` | 높음 | 아직 아님 |
| 온보딩 B-track | 높음 | 아직 아님 |
| 기록 운영 규칙 | 높음 | 다음 실제 구현 턴에서 처음 증명 필요 |

### 가장 짧은 해석
문서는 이미 handoff-ready에 가깝다. 이제 필요한 건 새 packet보다 **gap closure evidence**다.

---

## 리스크 & 후속과제
- 문서가 충분하다는 이유로 실제 남은 code gap이 가려질 수 있다.
- Commit 1/2와 B-1~B-4가 다시 큰 덩어리로 합쳐지면 half-cutover가 재발한다.
- UI/아트가 Commit 1이나 B-1 이전에 closing 감각으로 들어가면 drift가 생긴다.
- PM/QA가 문서 green과 runtime green을 같은 말로 쓰면 다음 구현자가 baseline을 잘못 읽는다.

---

## immediate next actions
1. 다음 구현자는 `runtime patch map -> runtime commit stack -> execution reporting packet -> gap ledger` 순서로 읽고 Commit 1 code gap부터 닫는다.
2. Commit 2 green 뒤에만 B-1을 연다.
3. 모든 기록은 `무슨 문서를 추가했는가`보다 `어떤 갭을 닫았는가` 기준으로 남긴다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_gap_ledger_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
