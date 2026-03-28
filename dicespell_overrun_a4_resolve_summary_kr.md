# DiceSpell `overrunEvent` A-4 confirm resolve 요약

## 이번 문서가 닫는 공백

A-3 문서로 `overrunEvent`의 중앙 카드 UI와 ending과의 시각 경계는 이미 고정됐다. 그런데 그 다음 실제 구현 직전에는 마지막 공백이 남는다.

> `좋아, 이제 장면은 보여. 그런데 confirm CTA를 누르면 정확히 어떤 상태만 한 번 바뀌어야 하고, 어떤 smoke가 붙어야 진짜로 닫혔다고 말할 수 있지?`

이번 문서 `docs/dicespell_overrun_a4_resolve_packet_kr.md`는 바로 이 질문에 답한다.

---

## 핵심 결론

A-4의 confirm CTA는 **보상을 정확히 한 번만 적용하고 다음 세그먼트로 넘기는 resolve 경계**다.

그래서 아래 여섯 가지를 고정한다.

1. confirm 전에는 어떤 보상도 적용되지 않는다.
2. confirm 후에는 같은 reward가 두 번 적용되면 실패다.
3. `none`은 무보상 진행이지, 진행 불가 상태가 아니다.
4. invalid/missing branch는 `none` fallback으로 수렴한다.
5. `resolved=true` 상태는 재실행이 아니라 안전 복귀만 허용한다.
6. smoke 기준선은 이제 `enter → confirm → recovery` 구조다.

즉 이 단계는 `버튼 연결`이 아니라,
`장면 -> 적용 -> 다음 세그먼트`가 한번만 성립하는 계약을 닫는 일이다.

---

## 프로그래머가 실제로 닫아야 할 것

- `resolveOverrunEvent(state)` 또는 동등 함수
- `confirm-overrun-event` action 연결
- reward 적용 1회 보장
- `none` branch 무보상 진행 보장
- `resolved` reload guard
- S4~S12 + recovery smoke 마이그레이션
- 기존 즉시 적용 baseline 제거

핵심은 **effect는 엔진, action wiring은 앱**으로 자르는 것이다.

---

## QA가 봐야 할 것

반드시 살아 있어야 하는 세트는 아래 네 묶음이다.

- enter: S1~S3
- confirm: S4~S6
- recovery: S7~S11
- density sanity: S12

특히 `none`, invalid, reload, already resolved`는 옵션 케이스가 아니라 필수 케이스다.

---

## owner별 포인트

### 프로그래머
- 엔진에서만 effect/state diff 처리
- app.js는 CTA wiring만 담당
- old immediate-apply smoke 기준선 제거

### UI
- confirm 후 stale card/flicker 없음 확인
- `none`에서도 CTA semantics 동일한지 확인

### 아트
- transition 중 tone/token 깨짐 없음 확인
- neutral fallback shell 유지

### QA
- 중복 적용 여부 수치 비교
- reload/recovery 포함 full set 검수

---

## A-4 완료선

아래 다섯 가지가 모두 성립해야 A-4가 닫힌다.

1. confirm CTA에서만 effect가 적용된다.
2. `none`/invalid/missing/resolved 케이스가 모두 런을 살린다.
3. effect와 `overrunLevel/pagePlan/currentPage`가 정확히 한 번만 바뀐다.
4. 기존 즉시 적용 smoke 기준선이 제거된다.
5. S4~S12 + recovery가 새 기준선으로 남는다.

---

## 그 다음

이 문서가 닫히면 `overrunEvent` 쪽 문서 공백은 사실상 끝이다.

그 다음 우선순위는 문서 추가가 아니라 실제 컷오버 실행이다.

- A-1 enter 분리
- A-2 canonical snapshot/normalize
- A-3 UI shell
- A-4 confirm resolve + smoke
- 이후 B-1~B-4 onboarding 구현

즉 이번 문서는
**`overrunEvent` 문서 묶음을 마지막으로 실구현 직전 상태까지 닫는 A-4 commit-level handoff**다.
