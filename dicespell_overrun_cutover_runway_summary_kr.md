# DiceSpell `overrunEvent` 컷오버 실행 런웨이 요약

## 3줄 요약
- 이 문서는 `overrunEvent` 문서가 충분한 상태에서 남아 있던 마지막 실행 공백, 즉 `이번 턴에 무엇을 어디까지 닫고 어떤 증거를 남기면 끝인가`를 빠르게 읽기 위한 companion view다.
- 설계를 새로 늘리지 않고, 이미 닫힌 `A-1~A-4` packet을 실제 구현 순서와 owner handoff 기준으로 다시 압축한다.
- 프로그래머는 `A-1→A-2→A-3→A-4` 순서, UI/아트는 `ending vs overrunEvent` 경계, QA는 `1회 적용 / recovery / resolved guard`를 먼저 보면 된다.

## 한 줄 목표
실제 코딩 직전에 `overrunEvent` 컷오버를 다시 머릿속에서 재조합하지 않게 만드는 빠른 실행 요약을 제공한다.

## 왜 이 문서가 필요한가
지금 DiceSpell은 문서가 부족해서 멈춘 상태가 아니다. 오히려 handoff, screen, content deck, delivery packet, A-1~A-4 commit packet까지 충분해서 실제 구현자가 다시 자기 식으로 범위를 자르기 쉽다. 이 문서는 그 위험을 줄이기 위한 **readability-first 실행 요약**이다.

---

## 먼저 읽어야 할 사람별 포인트

### 프로그래머
- 이번 턴은 어느 A-step까지 닫는지 먼저 정한다.
- `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs` 중 무엇을 같이 묶을지 이 문서 기준으로 자른다.
- A-4가 닫히기 전 onboarding은 열지 않는다.

### UI / 아트
- `overrunEvent`는 중앙 카드 1장 장면이고, ending은 결과/정산/CTA 의미만 맡는다.
- `none` branch도 placeholder가 아니라 정상 장면이다.
- reward card나 ending shell 재사용이 읽힘을 흐리면 실패다.

### QA
- confirm 전 effect 미적용, confirm 후 1회 적용, resolved 재실행 금지, invalid/missing -> none fallback을 먼저 본다.
- happy path만 통과해도 완료가 아니다.

---

## 실행 순서 핵심

| 단계 | 이번 단계 목표 | 닫혀야 하는 증거 |
| --- | --- | --- |
| A-1 | `continueOverrun()`를 enter wrapper로 분리 | `phase='overrunEvent'` 진입 + confirm 전 미적용 |
| A-2 | canonical snapshot/normalize 고정 | reload/invalid/missing recovery 수렴 |
| A-3 | 중앙 카드 1장 UI 고정 | ending과 다른 shell/tone + `none` 정상 밀도 |
| A-4 | confirm resolve + full smoke | 1회 적용 + resolved guard + S4~S12 |

### 중요한 규칙
- A-3와 A-4를 한 번에 섞지 않는다.
- A-2 없이 render가 branch를 다시 판정하게 두지 않는다.
- A-4 전 onboarding 구현 금지.

---

## 파일 경계 한눈에 보기

| 단계 | 필수 파일 |
| --- | --- |
| A-1 | `src/game-engine.js`, `scripts/smoke-test.mjs` |
| A-2 | `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs` |
| A-3 | `src/app.js`, `src/styles.css` |
| A-4 | `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs` |

### 한 줄 메모
- 엔진이 effect/state diff를 독점하고,
- 앱은 display/action wiring을 맡고,
- smoke는 slice 종료 증거를 남긴다.

---

## 가장 자주 생길 실수

1. 문서가 많다는 이유로 이번 턴 범위를 다시 크게 잡는 것
2. `none`을 임시 fallback이라며 비어 보이게 두는 것
3. A-3 카드 UI와 A-4 resolve를 한 commit에서 같이 밀어 넣는 것
4. ending이 overrun flavor를 다시 먹는 것
5. resolved guard 없이 reload 후 CTA를 다시 눌러도 통과시키는 것

---

## 이번 문서가 실제로 막아 주는 것
- `이번 턴은 정확히 어디까지 닫지?`라는 범위 혼선을 줄인다.
- `누가 무엇을 넘기면 끝인가?`를 owner별로 다시 정렬한다.
- tracker / run log / 코드 커밋에서 같은 step 이름을 쓰게 만든다.

---

## 바로 다음 액션
1. 다음 writer 구현 턴에서 이 문서를 열고 `이번 턴은 A-1만` 또는 `A-1~A-2까지`처럼 먼저 선언한다.
2. 각 step은 코드와 증거를 같은 턴에 같이 남긴다.
3. A-4가 닫힌 뒤에만 onboarding B-1~B-4를 연다.

---

## 한 줄 결론
이 문서는 새 기획이 아니라, 이미 충분한 `overrunEvent` 문서를 실제 구현 런웨이로 정렬해 주는 빠른 읽기용 companion이다.
