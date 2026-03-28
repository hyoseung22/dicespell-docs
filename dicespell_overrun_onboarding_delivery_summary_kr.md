# DiceSpell `overrunEvent` → 첫 런 온보딩 전달 요약

## 이 문서가 추가로 필요한 이유

`overrunEvent`와 첫 런 온보딩은 방향, 화면, acceptance, source-of-truth, content key, contract example까지 이미 production-ready 수준으로 닫혀 있다.

하지만 실제 구현 직전엔 다른 질문이 남는다.

- 이번 턴에 어느 파일을 같이 건드려야 하나?
- 어느 시점에 프로그래머 일이 끝나고 UI/아트/QA handoff로 넘어가나?
- 어떤 증거가 남아야 이 slice가 진짜로 닫혔다고 말할 수 있나?

`docs/dicespell_overrun_onboarding_delivery_packet_kr.md`는 이 마지막 운영 공백을 메우기 위한 문서다.

---

## 한 줄 결론

> 다음 구현은 `overrunEvent`를 먼저 엔진/렌더/스모크 단위로 독립시키고, 그 다음 온보딩을 `library → battle → reward/shop → ending`의 작은 전달 묶음으로 얹어야 한다.

즉 이제 남은 일은 새 기획 추가가 아니라, **문서 묶음을 실제 전달 묶음으로 옮기는 것**이다.

---

## 이번 전달 패킷이 고정하는 것

### 1) 실제 파일 경계
- `dice-spell-standalone/src/game-engine.js`
- `dice-spell-standalone/src/app.js`
- `dice-spell-standalone/src/styles.css`
- `dice-spell-standalone/scripts/smoke-test.mjs`

### 2) 실제 전달 묶음
- A-1 엔진 진입 분리
- A-2 snapshot / normalize
- A-3 중앙 카드 1장 UI
- A-4 confirm resolve + smoke
- B-1 library primer
- B-2 battle primer
- B-3 reward / shop primer
- B-4 ending primer

### 3) owner별 handoff 경계
- 프로그래머: 상태 전이와 helper/source-of-truth를 닫는다.
- UI: 카드/primer 위계와 surface 밀도를 닫는다.
- 아트: accent/backplate/badge 최소 토큰 패키지를 닫는다.
- QA: phase, contentKey, CTA semantics, recovery, density 검수를 닫는다.

### 4) 권장 커밋 묶음
- `overrunEvent` enter/snapshot baseline
- `overrunEvent` card UI + confirm resolve + smoke migration
- `onboarding` library/battle primer
- `onboarding` reward/shop/ending primer

---

## 가장 중요한 규칙

1. `ending`은 끝까지 결과 해석 + CTA 의미만 말한다.
2. `overrunEvent`가 장면/flavor를 맡는다.
3. 온보딩은 새 phase가 아니라 guidance layer다.
4. `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet` / `CTA semantics`를 먼저 본다.
5. 검증 증거 없는 handoff 완료 선언은 금지다.

---

## 지금 기준 다음 실무 액션

1. 실제 구현은 A-1 `continueOverrun()` 진입 분리부터 시작한다.
2. 같은 커밋에 진입 smoke와 fallback snapshot 증거를 남긴다.
3. `overrunEvent` A-4가 닫히기 전엔 온보딩 ending primer를 먼저 붙이지 않는다.
4. 온보딩은 B-1 `library`부터 차례로 붙인다.

---

## 한 문장으로 요약하면

지금 DiceSpell은 문서가 부족한 상태가 아니라, **다음 구현자가 문서들을 어떻게 실제 전달 단위로 옮길지까지 닫아야 하는 상태**다. 이번 전달 패킷은 그 마지막 handoff 공백을 메운다.
