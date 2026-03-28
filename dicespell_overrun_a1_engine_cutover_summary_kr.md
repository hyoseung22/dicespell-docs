# DiceSpell `overrunEvent` A-1 엔진 컷오버 요약

## 이 문서는 무엇인가

`docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`의 읽기용 companion이다.

원본 packet이 프로그래머/QA 중심의 실제 커밋 계약서라면, 이 문서는 PM/기획/UI/아트가 **이번 첫 구현 커밋이 어디까지 닫히고 어디까지는 아직 안 닫히는지**를 3~5분 안에 이해하도록 만든 요약본이다.

---

## 이번 A-1 커밋의 목표

이번 첫 컷오버는 `overrunEvent`를 완성하는 작업이 아니다.
핵심은 아래 한 줄이다.

> `오버런 계속`은 이제 보상을 즉시 적용하는 버튼이 아니라, `overrunEvent`라는 별도 장면으로 들어가는 버튼이 된다.

즉 이번 커밋의 성패는 화면 완성도가 아니라,
**엔딩과 오버런 장면이 실제로 분리됐는가**로 본다.

---

## 지금 왜 이 작업이 먼저 필요한가

현재 코드에서는 `continueOverrun()` 한 번으로 아래가 한꺼번에 일어난다.

- 전리품 적용
- 오버런 레벨 증가
- 다음 세그먼트 추가
- 다음 페이지 진입

문제는 이렇게 되면 `overrunEvent` 문서가 아무리 잘 닫혀 있어도,
실제 코드에서는 여전히 `엔딩 버튼 = 즉시 적용` 구조를 벗어나지 못한다는 점이다.

그래서 첫 커밋은 욕심내서 장면 전체를 만드는 대신,
먼저 **진입과 적용을 분리하는 뼈대**를 세운다.

---

## 이번 커밋에서 실제로 닫히는 것

### 1. `continueOverrun()` 의미 변경
- 이전: 즉시 적용 + 다음 권 진입
- 이후: `overrunEvent` 장면 진입

### 2. 최소 snapshot 생성
- 선택한 전리품이 있으면 그 정보를 snapshot으로 만든다.
- 선택이 없거나 데이터가 깨졌으면 `none` fallback snapshot으로 강등한다.

### 3. `phase = 'overrunEvent'` 저장
- 저장/복구 기준에서 이제 `ending`과 `overrunEvent`가 서로 다른 phase로 존재한다.

### 4. enter smoke 추가
- `continueOverrun()` 직후 아직 reward가 적용되지 않았다는 것을 smoke로 증명한다.

---

## 이번 커밋에서 아직 안 닫히는 것

### 아직 하지 않는 것
- confirm CTA 완성
- 전리품 실제 적용
- 중앙 카드 완성 UI
- `contentKey` / `accentKey` / `artTokenSet` 세부 branching
- S1~S12 전체 smoke migration

즉 이번 커밋은 `오버런 이벤트 완성`이 아니라,
**오버런 이벤트가 들어갈 엔진 진입점 확보**다.

---

## owner별로 보면

### 프로그래머
- `continueOverrun()`를 enter wrapper로 줄이는 것이 핵심이다.
- 즉시 적용 로직은 다음 커밋으로 넘긴다.

### UI
- 완성 카드 디자인을 아직 강하게 닫지 않는다.
- 대신 `overrunEvent`가 `ending` 내부 섹션이 아니라 독립 장면처럼 보이게 해야 한다.

### 아트
- 이 단계에선 새 자산 제작보다 `none` fallback도 빈 화면처럼 보이지 않게 하는 톤 가이드가 우선이다.

### QA
- pass 기준은 `화면이 예쁘다`가 아니라 `즉시 적용이 사라졌다`다.
- resource/page progression이 그대로인지 먼저 본다.

---

## A-1의 최소 pass 조건

아래 네 가지가 충족되면 A-1은 닫혔다고 본다.

1. `continueOverrun()` 후 `phase === 'overrunEvent'`
2. `overrunEvent` snapshot이 실제로 생김
3. 선택 없음 / invalid reward에서도 `none` fallback으로 진입 가능
4. enter 시점에는 Shield/MP/주입/강화/page progression이 아직 변하지 않음

---

## 왜 `none` fallback이 중요한가

이번 문서 묶음에서 가장 중요한 운영 원칙 중 하나는,
**데이터가 조금 깨져도 런은 계속 살아 있어야 한다**는 점이다.

그래서 A-1은 멋진 branch보다 먼저 아래를 보장해야 한다.

- 선택이 없어도 진입 가능
- 잘못된 `rewardId`여도 진입 가능
- draft가 불완전해도 최소한 `빈손` 카드로 들어갈 수 있음

이게 있어야 이후 A-2/A-3/A-4를 안전하게 얹을 수 있다.

---

## 다음 커밋으로 넘어가는 기준

A-1이 닫힌 다음에야 아래가 자연스럽게 열린다.

- A-2: snapshot/contentKey/normalize 강화
- A-3: 중앙 카드 1장 UI
- A-4: confirm resolve + full smoke migration

즉 이번 커밋은 작아 보이지만,
실제로는 이후 모든 `overrunEvent` 구현이 안전하게 서는 기반 커밋이다.

---

## 한 줄 결론

> A-1은 `오버런 이벤트를 완성하는 커밋`이 아니라, `오버런 계속`을 즉시 적용 버튼에서 `별도 장면 진입`으로 바꾸는 첫 엔진 컷오버 커밋이다. 이게 닫혀야 나머지 UI/QA/아트 handoff도 흔들리지 않는다.
