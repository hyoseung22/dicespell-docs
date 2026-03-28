# DiceSpell `overrunEvent` 첫 런타임 슬라이스 요약

## 3줄 요약
- 이 문서는 `overrunEvent` 실구현 직전 가장 중요한 첫 코드 턴을 빠르게 읽기 위한 companion view다.
- 범위는 `A-1 enter 분리`와 `A-2 canonical snapshot floor`를 한 번의 bounded slice로 묶는 것이고, A-3 UI와 A-4 resolve는 일부러 뒤로 미룬다.
- 핵심은 `보여 주기 전에 먼저 저장 가능한 상태를 만든다`는 원칙을 owner별로 같은 문장으로 읽게 만드는 것이다.

## 한 줄 목표
첫 실제 구현 슬라이스를 `enter + canonical payload + recovery smoke`까지만 고정해, 이후 UI와 resolve가 흔들리지 않게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell은 문서가 부족한 상태가 아니다. 오히려 A-1~A-4, runway, patch map, content deck, smoke migration까지 충분해서 implementer가 첫 코드 턴 범위를 다시 자기 식으로 넓히기 쉽다. 이 문서는 그 위험을 줄이기 위한 readability-first 요약이다.

---

## 누가 먼저 무엇을 봐야 하나

### 프로그래머
- 이번 턴은 `first runtime slice`라고 먼저 선언한다.
- `continueOverrun()` 즉시 적용 체인을 끊고, canonical snapshot field set과 normalize만 닫는다.
- `styles.css`와 카드 polish는 건드리지 않는다.

### QA
- pass 기준은 예쁜 화면이 아니라 enter/recovery smoke다.
- happy path만 통과하면 끝이 아니다.
- legacy/invalid/missing recovery가 빠지면 fail이다.

### UI / 아트
- 아직 카드 제작 턴이 아니다.
- 이번 슬라이스가 끝나면 `contentKey`, `accentKey`, `artTokenSet`을 믿고 A-3 준비를 시작할 수 있다.
- `none`도 정상 장면이라는 사실만 먼저 고정하면 된다.

---

## 이번 슬라이스에서 실제로 닫는 것

| 범주 | 닫는 것 | 일부러 미루는 것 |
| --- | --- | --- |
| 엔진 | `phase='overrunEvent'` 진입, canonical snapshot 생성, normalize/recovery | reward 실제 적용, `resolved` guard 최종 wiring |
| 앱 | `overrunEvent` explicit branch만 추가 | 중앙 카드 1장 UI 완성 |
| 검증 | enter smoke + canonical/recovery smoke | A-3 visual sanity, A-4 full resolve smoke |

### 종료선
1. ending CTA 후 즉시 reward가 적용되지 않는다.
2. canonical field set이 state에 저장된다.
3. legacy/invalid/missing가 모두 normalize 규칙으로 수렴한다.

---

## 필수 canonical field set
- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `flavorText`
- `confirmLabel`
- `resolved`

### 중요한 규칙
- render가 위 필드를 다시 추론하면 실패다.
- `none`은 빈 fallback이 아니라 정상 branch다.
- UI는 이 값들을 display-only로 소비해야 한다.

---

## smoke 종료 증거

| smoke | 의미 |
| --- | --- |
| S-A1-ENTER | enter 분리가 실제로 됐는가 |
| S-A1-NONE | 선택 없음/invalid draft에서도 런이 살아남는가 |
| S-A2-CANONICAL | canonical field set이 모두 저장되는가 |
| S-A2-LEGACY | legacy thin state가 복구되는가 |
| S-A2-MISSING | missing state가 안전하게 수렴하는가 |
| S-A2-INVALID | invalid reward/branch가 `none`으로 강등되는가 |

---

## 이 슬라이스에서 하지 말아야 할 것
1. reward apply와 page progression을 일부라도 유지한 채 enter 분리를 끝났다고 말하는 것
2. canonical payload가 덜 닫힌 상태에서 A-3 UI를 먼저 여는 것
3. title 문자열이나 `sourceType`로 badge/accent/header를 다시 판정하는 것
4. `styles.css`를 크게 건드려 A-3 범위를 섞는 것
5. A-4 resolve를 같이 열며 범위를 부푸는 것

---

## 바로 다음 액션
1. 다음 writer 구현 턴은 이 요약과 `runtime patch map`을 함께 열고 `first runtime slice`로 범위를 선언한다.
2. `game-engine.js` → `app.js` 최소 branch → `smoke-test.mjs` 순으로 닫는다.
3. 위 smoke가 모두 통과한 뒤에만 A-3 UI packet으로 넘어간다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 실구현의 첫 코드 턴을 `enter + canonical payload + recovery smoke`까지만 고정해 주는 빠른 읽기용 companion이다.
