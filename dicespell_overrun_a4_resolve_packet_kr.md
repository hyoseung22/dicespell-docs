# DiceSpell `overrunEvent` A-4 confirm resolve 컷오버 패킷

## 문서 목적

이 문서는 `docs/dicespell_overrun_a3_ui_packet_kr.md` 다음 단계인 **A-4 = confirm CTA 시점 resolve + full smoke migration 고정**만을 다루는 구현 전용 handoff packet이다.

이미 DiceSpell에는 아래 문서가 있다.

- `docs/dicespell_overrun_event_integration_workorder_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
- `docs/dicespell_overrun_onboarding_delivery_packet_kr.md`
- `docs/dicespell_overrun_a2_snapshot_packet_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`

하지만 실제 구현 직전의 남은 마지막 작은 공백은 여전히 아래 질문이다.

> `좋아, 이제 overrunEvent는 장면으로 읽혀. 그런데 confirm CTA를 눌렀을 때 정확히 어떤 상태만 한 번 바뀌어야 하고, 무엇을 cleanup하며, 어떤 smoke가 붙어야 진짜로 컷오버가 닫혔다고 말할 수 있지?`

이 문서는 그 질문에 답한다. 즉 `overrunEvent` 전체를 다시 설명하지 않고, **A-4 commit 범위 / resolve state diff / reload guard / smoke migration 완료선 / owner별 deliverable**만 좁혀서 다룬다.

---

## 왜 지금 이 문서가 필요한가

A-3가 닫히면 화면은 이미 `badge → header → flavor → reward summary → CTA → footnote` 위계로 읽힌다. 그런데 바로 다음 커밋에서 구현자가 다시 흔들릴 지점은 아래 여섯 가지다.

1. confirm CTA가 실제 적용 시점인데, 기존 즉시 적용 baseline이 남아 있으면 effect가 두 번 들어갈 수 있다.
2. `resolved` guard가 없으면 reload 후 CTA를 다시 눌러 중복 적용될 수 있다.
3. `none` branch도 정상 진행해야 하는데, reward가 없다는 이유로 다음 세그먼트 진입이 막힐 수 있다.
4. invalid/missing snapshot recovery와 confirm resolve가 다른 규칙으로 움직이면 save/load 경계에서 drift가 생긴다.
5. `ending` 정산 데이터, `segmentTrophyTemplateIds`, `pagePlan`, `currentPage`, `overrunLevel` 중 무엇을 언제 지워야 하는지가 한 커밋 안에서 섞이기 쉽다.
6. smoke가 enter 기준에 머물면 `장면은 보이지만 실제로 런이 안 넘어가는 상태`를 놓치기 쉽다.

즉 A-4에서 필요한 것은 새 메카닉이 아니라,
**confirm CTA가 정확히 한 번만 런 상태를 전진시키고, 그 동작이 recovery까지 포함한 smoke 증거로 닫혔는지**를 한 장으로 고정하는 일이다.

---

## A-4의 한 줄 정의

> `overrunEvent`의 confirm CTA는 선택한 전리품을 정확히 한 번만 적용하고, 세그먼트를 다음 상태로 전진시키며, 재실행 없이 cleanup까지 닫는 단일 resolve 경계다.

---

## A-4 범위

### 포함
- `confirm-overrun-event` action 연결
- `resolveOverrunEvent(state)` 또는 동등 함수 추가/정리
- reward 적용 1회 보장
- `none` branch 무보상 진행 보장
- `resolved` reload/retry guard 고정
- S4~S12 + recovery 세트 마이그레이션 완료
- old immediate-apply baseline 제거 또는 비활성화

### 제외
- A-3 이전 단계 UI shell 재설계
- primer helper 구현
- 새 보상 종류 추가
- 오버런 외 다른 phase 리팩터링

즉 A-4의 끝은 `버튼이 있다`가 아니라,
**버튼이 문서가 말한 상태 diff를 정확히 1회 수행하고 recovery까지 닫힌다**다.

---

## canonical source

| 질문 | source-of-truth |
| --- | --- |
| confirm 시점 상태 전이/owner handoff | `docs/dicespell_overrun_event_integration_workorder_kr.md` |
| full smoke migration 세트 | `docs/dicespell_overrun_event_smoke_migration_packet_kr.md` |
| A-4가 delivery packet에서 차지하는 위치 | `docs/dicespell_overrun_onboarding_delivery_packet_kr.md` |
| canonical snapshot/recovery field set | `docs/dicespell_overrun_a2_snapshot_packet_kr.md` |
| CTA가 UI에서 어떤 의미로 읽혀야 하나 | `docs/dicespell_overrun_a3_ui_packet_kr.md` |
| ending과의 책임 경계 | `docs/dicespell_ending_overrun_boundary_packet_kr.md` |

---

## A-4 완료 정의

A-4는 아래 다섯 줄이 모두 참일 때만 닫혔다고 본다.

1. confirm CTA에서만 보상이 적용된다.
2. 선택 있음 / 선택 없음 / invalid / reload / resolved 케이스가 모두 런을 살린다.
3. effect, `overrunLevel`, `pagePlan`, `currentPage`가 정확히 한 번만 바뀐다.
4. `ending`은 cleanup되고, `segmentTrophyTemplateIds`는 다음 세그먼트 전에 초기화된다.
5. 기존 즉시 적용 baseline이 제거되고 S1~S12 + recovery가 새 기준선이 된다.

---

## resolve 핵심 결론

A-4에서는 아래 여섯 가지를 절대 흔들리지 않는 규칙으로 본다.

1. **confirm 전에는 어떤 보상도 적용되지 않는다.**
2. **confirm 후에는 같은 reward가 두 번 적용되면 실패다.**
3. **`none`은 무보상 진행이지, 진행 불가 상태가 아니다.**
4. **invalid/missing branch는 `none` fallback으로 수렴한다.**
5. **`resolved=true` 상태는 재실행이 아니라 안전 복귀/cleanup만 허용한다.**
6. **smoke 기준선은 이제 `phase='overrunEvent' enter -> confirm -> recovery` 순서다.**

즉 A-4는 `버튼 구현`이 아니라,
`장면 -> 적용 -> 다음 세그먼트`가 한번만 성립하는 런타임 계약을 닫는 단계다.

---

## 권장 런타임 흐름

```text
ending
→ continueOverrun() / enterOverrunEvent()
→ phase = 'overrunEvent'
→ A-3 UI 렌더
→ confirm-overrun-event
→ resolveOverrunEvent()
→ apply selected reward exactly once (or none fallback)
→ overrunLevel/pagePlan/currentPage advance
→ cleanup ending + overrunEvent draft/history reset
→ library 경유 후 다음 세그먼트 준비
```

핵심은 **effect 적용, 세그먼트 전진, cleanup이 모두 confirm 이후 한 트랜잭션처럼 읽혀야 한다**는 점이다.

---

## 권장 상태 diff

### confirm 직전에 반드시 참이어야 하는 것

- `phase === 'overrunEvent'`
- `overrunEvent.resolved === false`
- `ending` 정보가 아직 남아 있음
- `segmentTrophyTemplateIds`가 이전 세그먼트 잔해를 들고 있음
- `rewardId`가 있거나 `sourceType === 'none'`

### confirm 직후에 반드시 바뀌어야 하는 것

- 선택한 bundle 효과 1회 적용
- `overrunLevel += 1`
- `pagePlan` 다음 세그먼트 분량 추가
- `currentPage += 1`
- `segmentTrophyTemplateIds = []`
- `ending = null`
- `overrunEvent.resolved = true` 또는 동등 cleanup mark

### confirm 직후에 절대 두 번 바뀌면 안 되는 것

- Shield / MP / Gold / inject / enhance 등 bundle 효과
- `overrunLevel`
- `pagePlan.length`
- `currentPage`

즉 A-4 resolve는 **정확히 한 번의 전진**이어야 한다.

---

## 함수 경계 권장안

### 엔진
- `resolveOverrunEvent(state)` 또는 동등 함수
- 역할:
  - canonical snapshot normalize/fallback
  - `resolved` guard 확인
  - reward lookup/fallback
  - effect bundle 1회 적용
  - next segment advance
  - cleanup

### 앱
- `data-action="confirm-overrun-event"` click 연결
- 역할:
  - 현재 `phase === 'overrunEvent'` 확인
  - 엔진 resolve 호출
  - 결과 state 반영
  - stale card 제거 후 다음 surface 렌더

### 금지 패턴
- app.js가 reward effect를 직접 적용
- `confirm-overrun-event` handler 내부에서 `sourceType`별 수치를 분기
- `resolved` guard 없이 같은 action을 재호출
- A-4 커밋에서 onboarding primer까지 같이 붙임

핵심은 **effect는 엔진, action wiring은 앱**으로 자르는 것이다.

---

## `none` / invalid / reload 규칙

### `none`
- CTA는 정상 동작해야 한다.
- reward 수치 변화는 없다.
- 다음 세그먼트 준비만 수행한다.
- `segmentTrophyTemplateIds`는 초기화한다.

### invalid reward
- lookup 실패 시 `none` fallback으로 강등한다.
- CTA는 유지한다.
- 런은 멈추지 않는다.
- header/flavor/badge/confirmLabel도 fallback 문구로 수렴한다.

### `resolved=true`
- 보상 재적용 금지
- 재클릭 시 no-op 또는 안전 복귀만 허용
- reload 후에도 중복 effect 없음

즉 A-4는 정상 branch뿐 아니라 **고장난 저장 상태도 같은 출구로 보내는 단계**다.

---

## smoke migration 완료선

### 반드시 살아 있어야 하는 세트

| 묶음 | 항목 | 의미 |
| --- | --- | --- |
| enter | S1~S3 | 화면 진입이 effect 없이 살아 있음 |
| confirm | S4~S6 | theme/enemy/none resolve 정상 동작 |
| recovery | S7~S11 | invalid/missing/broken/resolved/missing-copy 복구 |
| density | S12 | UI 밀도 sanity 유지 |

### old baseline 처리

기존 `continueOverrun()` 즉시 적용 smoke는 A-4가 닫히면 더 이상 기준선이 아니다.

- 즉시 적용 assertion은 제거 또는 `legacy` 보관 후 비활성화
- 새 기준선은 `phase='overrunEvent' -> confirm` 구조
- `effect already applied at ending` 가정이 하나라도 남으면 실패로 본다

---

## owner별 deliverable

### 프로그래머
- `resolveOverrunEvent(state)` 또는 동등 함수 구현
- `confirm-overrun-event` action 연결
- `resolved` guard / invalid fallback / cleanup 고정
- S4~S12 smoke 마이그레이션 완료
- 기존 즉시 적용 baseline 제거

### UI
- confirm 직후 stale overrun card/ending copy 잔상 없음 확인
- CTA press 이후 다음 phase 전환이 끊기지 않는지 sanity 캡처
- `none` branch에서도 CTA semantics가 동일한지 확인

### 아트
- confirm transition 중 tone token/CTA flicker 없음 확인
- `none` fallback에서도 neutral shell이 깨지지 않는지 확인
- transition 자산이 있다면 next segment 진입 직전에만 짧게 개입

### QA
- S4~S12 + recovery 6종 수동/자동 검수
- 선택 있음 / 선택 없음 / invalid / reload / resolved 케이스 캡처
- effect 중복 적용 여부 수치 비교 검수

---

## 추천 구현 순서

1. `confirm-overrun-event` action wiring 추가
2. `resolveOverrunEvent(state)` 또는 동등 함수 구현
3. theme confirm(S4) 먼저 통과
4. enemy confirm(S5), none confirm(S6) 추가
5. invalid / missing / broken / resolved recovery(S7~S11) 이행
6. legacy immediate-apply baseline 제거
7. S12 density sanity까지 최종 확인

---

## A4-S4~S12 기준 재정리

| ID | 시나리오 | 기대 결과 |
| --- | --- | --- |
| A4-S4 | theme confirm resolve | 책 전리품 effect 1회 적용, `overrunLevel/pagePlan/currentPage` 전진 |
| A4-S5 | enemy confirm resolve | 적 전리품 effect 1회 적용, resource/inject/enhance 기대값 일치 |
| A4-S6 | none confirm resolve | 수치 보상 없이 다음 세그먼트만 정상 준비 |
| A4-S7 | invalid reward recovery | `none` 강등 후 CTA 유지, 런 생존 |
| A4-S8 | missing snapshot recovery | snapshot 재생성 또는 `none` fallback 후 confirm 가능 |
| A4-S9 | broken sourceType recovery | 기본 배지/CTA/카피 재주입 |
| A4-S10 | resolved reload guard | 재실행 금지, 보상 중복 적용 없음 |
| A4-S11 | missing copy reinjection | header/flavor/confirmLabel 빈 상태 방지 |
| A4-S12 | full migration sanity | old immediate baseline 제거, 새 fixture 세트만 기준선 |

### 증거 형식
- 가능하면 smoke assertion
- 최소한 state diff 로그
- `resolved` 중복 방지 전후 비교 필수

---

## 파일 경계

| 파일 | 역할 | 이번 단계 변경 여부 |
| --- | --- | --- |
| `src/game-engine.js` | resolve/apply/cleanup | 필수 |
| `src/app.js` | CTA action wiring / rerender | 필수 |
| `scripts/smoke-test.mjs` | S4~S12 + recovery | 필수 |
| `src/styles.css` | flicker 보정 정도만 선택 | 선택 |

A-4는 엔진 + 액션 + smoke commit이다. 따라서 이 단계에서 UI shell을 다시 크게 흔들면 A-3와 A-4가 뒤섞인다.

---

## DoD

- confirm CTA에서만 effect가 적용된다.
- `none`/invalid/missing/resolved 케이스가 모두 런을 살린다.
- 중복 적용이 없다.
- 기존 즉시 적용 smoke 기준선이 제거된다.
- S4~S12 + recovery가 새 기준선으로 남는다.

---

## 자주 생길 오해와 금지선

### 오해 1. `resolved=true`면 그냥 다시 눌러도 큰 문제 없다
금지다. A-4의 핵심은 재실행 금지다.

### 오해 2. invalid reward면 예외를 던져도 된다
금지다. 문서 기준 fallback은 `none`이다.

### 오해 3. `none`은 effect가 없으니 smoke를 약하게 해도 된다
금지다. `none`은 정상 branch라 S6이 필수다.

### 오해 4. enter smoke가 이미 있으니 confirm smoke는 얕아도 된다
금지다. A-4의 완료선은 confirm 이후 state diff다.

---

## 리스크 & 후속 과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| resolve가 app.js와 engine에 반쯤 나뉘는 위험 | 중복 적용/재현성 붕괴 | effect와 state diff는 엔진이 독점 |
| `resolved` guard 누락 위험 | reload 후 dup reward | A4-S10 필수 smoke |
| invalid reward를 예외 처리로 밀어버리는 위험 | 런 중단 | `none` fallback 강제 |
| legacy immediate baseline이 일부 남는 위험 | 새 smoke와 충돌 | A4-S12에서 old baseline 제거 증거 남김 |

---

## 다음 단계

A-4가 닫히면 문서상 남아 있는 `overrunEvent` handoff 공백은 사실상 사라진다.

그 다음 우선순위는 문서 추가가 아니라 실제 컷오버 실행이다.

- A-1 enter 분리 실구현
- A-2 canonical snapshot/normalize 실구현
- A-3 UI shell 실구현
- A-4 confirm resolve + full smoke 실구현
- 그 뒤 B-1~B-4 onboarding 순차 구현

즉 A-4는 `overrunEvent` 문서 묶음을 마지막으로 실구현 전 상태까지 닫는 단계다.
