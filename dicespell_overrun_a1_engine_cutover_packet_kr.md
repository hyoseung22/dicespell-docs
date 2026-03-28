# DiceSpell `overrunEvent` A-1 엔진 컷오버 패킷

## 문서 목적

이 문서는 이미 작성된 아래 문서들을 **실제 첫 구현 커밋(A-1)** 기준으로 다시 좁힌 프로그래머 중심 handoff packet이다.

- `docs/dicespell_overrun_onboarding_delivery_packet_kr.md`
- `docs/dicespell_overrun_event_implementation_packet_kr.md`
- `docs/dicespell_overrun_event_integration_workorder_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
- `docs/dicespell_overrun_onboarding_contract_examples_kr.md`

지금 DiceSpell 문서 체계에서 가장 큰 공백은 방향, 레이아웃, acceptance가 아니다. 실제 첫 컷오버를 하려는 순간 아래 질문이 다시 남는다.

> `좋아, 이제 A-1부터 실제로 닫자. 그런데 정확히 어느 함수에서 무엇을 떼고, 어떤 상태를 새로 만들고, smoke는 어디까지 같이 바꿔야 하지?`

이 문서는 그 질문에 답하는 **첫 커밋 전용 구현 packet**이다. 즉 `overrunEvent` 전체를 다시 설명하지 않고, delivery packet이 정의한 **A-1 = continueOverrun() 진입 분리 + fallback snapshot + 진입 smoke**만 다룬다.

---

## 왜 지금 이 문서가 필요한가

현재 문서 묶음은 충분히 풍부하다. 오히려 그 풍부함 때문에 첫 코드 커밋에서 아래 네 가지가 다시 흔들릴 수 있다.

1. `continueOverrun()`를 어디까지 쪼개는지가 여전히 구현자 머릿속 pseudo-refactor로 남을 수 있다.
2. `snapshot 생성`과 `resolve 분리` 사이에서, 첫 커밋에 무엇을 넣고 무엇을 다음 커밋으로 미룰지 흔들릴 수 있다.
3. `scripts/smoke-test.mjs`는 아직 즉시 적용 baseline을 갖고 있어서, A-1 시점에 무엇을 삭제하지 말고 무엇을 새로 추가해야 하는지 경계가 필요하다.
4. QA/UI는 A-1이 `완성 화면`이 아니라는 걸 알아야 하는데, 그렇다고 placeholder만 두고 검증 없이 넘어가면 다음 커밋의 drift가 커진다.

즉 이 문서는 새 설계가 아니라 **첫 구현 묶음의 파일 경계 / 함수 경계 / state diff / smoke 증거**를 한 장으로 고정한다.

---

## A-1의 한 줄 정의

> `continueOverrun()`는 더 이상 즉시 적용 함수가 아니라 `overrunEvent` 진입 wrapper가 된다. 이 커밋에서는 snapshot을 안전하게 만들고 phase를 `overrunEvent`로 넘기기까지만 닫는다. 보상 적용은 아직 하지 않는다.

---

## A-1 범위

### 포함
- `continueOverrun()`의 역할을 `enter`로 축소
- `createOverrunEventSnapshot(state)` 또는 동등 helper 추가
- `phase = 'overrunEvent'` 진입
- 선택 없음 / invalid reward / draft 누락 시 `none` fallback snapshot 생성
- `renderCenter()` 최소 분기 또는 안전 placeholder
- `enter` 전용 smoke 추가

### 제외
- 실제 reward bundle 적용
- `confirm-overrun-event` action
- 중앙 카드 완성 UI
- `contentKey` 세부 branching 전체 구현
- S1~S12 전체 smoke 이행

즉 A-1의 끝은 `장면이 완성되었다`가 아니라,
**엔진이 이제 `ending -> overrunEvent`를 별도 phase로 안전하게 저장/복구할 수 있다**다.

---

## canonical source

| 질문 | source-of-truth |
| --- | --- |
| A-1이 delivery packet에서 어디에 해당하는가 | `docs/dicespell_overrun_onboarding_delivery_packet_kr.md` |
| 현재 코드 접점은 어디인가 | `docs/dicespell_overrun_event_implementation_packet_kr.md` |
| 전체 Step 0~4 중 지금 어디까지인가 | `docs/dicespell_overrun_event_integration_workorder_kr.md` |
| smoke를 어떤 방향으로 갈아타는가 | `docs/dicespell_overrun_event_smoke_migration_packet_kr.md` |
| fallback snapshot이 어떤 surface를 흉내 내야 하는가 | `docs/dicespell_overrun_onboarding_contract_examples_kr.md` |

---

## 현재 코드 기준 실제 접점

### 1) 엔진
파일: `dice-spell-standalone/src/game-engine.js`

현재 직접 수정 후보:

- `chooseEndingOverrunReward(state, rewardId)`
- `continueOverrun(state)`
- `maybeEnterNextPage(state)` 호출 직전/직후 오버런 진입 흐름

현재 `continueOverrun(state)`는 아래를 한 번에 하고 있다.

1. 선택 reward lookup
2. 선택 reward 즉시 적용
3. `overrunLevel += 1`
4. `segmentTrophyTemplateIds = []`
5. `pagePlan` 확장
6. `currentPage += 1`
7. `phase = 'library'`
8. `ending = null`
9. `maybeEnterNextPage(state)`

A-1에서는 이 함수의 의미를 아래로 바꾼다.

1. `ending.overrunDraft`를 읽는다.
2. `overrunEvent` snapshot을 만든다.
3. `phase = 'overrunEvent'`로 바꾼다.
4. 아직 reward/apply/page advance는 하지 않는다.

### 2) 앱
파일: `dice-spell-standalone/src/app.js`

현재 직접 수정 후보:

- `renderEnding(summary)`
- `renderCenter(summary)`
- `data-action="continue-overrun"` 처리부

A-1에서는 여기서 완성 UI를 만들 필요는 없다. 하지만 최소한 아래 둘은 필요하다.

- `renderCenter()`가 `phase === 'overrunEvent'`를 죽지 않고 처리할 것
- `renderEnding()`의 `continue-overrun` 버튼이 이제 `resolve`가 아니라 `enter`라는 문맥을 깨지 않을 것

### 3) smoke
파일: `dice-spell-standalone/scripts/smoke-test.mjs`

현재 오버런 baseline은 `continueOverrun()` 직후 reward가 이미 적용된다고 가정한다.
A-1에서는 그 baseline을 아직 완전히 지우지 않고, **enter 전용 검수선**을 먼저 병렬 추가하는 것이 안전하다.

---

## A-1 전후 상태 계약

### 현재

```text
ending
  └─ continueOverrun()
      └─ reward apply + next segment + next page
```

### A-1 이후

```text
ending
  └─ continueOverrun()
      └─ createOverrunEventSnapshot()
      └─ phase = overrunEvent
      └─ reward 미적용 상태 유지
```

### A-1에서 반드시 참이어야 하는 것

| 항목 | 기대값 |
| --- | --- |
| `state.phase` | `ending -> overrunEvent` |
| `state.overrunEvent` | 새 snapshot 존재 |
| `state.player.shield/mp/handSlots` | enter 시점엔 변하지 않음 |
| `state.overrunLevel` | 아직 증가하지 않음 |
| `state.pagePlan.length` | 아직 늘어나지 않음 |
| `state.currentPage` | 아직 증가하지 않음 |
| `state.ending` | 당장은 유지 가능, 단 snapshot source 역할만 함 |

핵심은 **enter는 장면 진입만 바꾸고 런 결과는 아직 안 건드린다**는 점이다.

---

## 권장 snapshot 최소 스키마

A-1에서는 최종 full contract를 모두 채울 필요는 없지만, 최소한 아래 필드는 있어야 다음 커밋이 안전하다.

```json
{
  "sourceType": "themeTrophy | enemyTrophy | none",
  "rewardId": "string | null",
  "rewardTitle": "string",
  "rewardDescription": "string",
  "rewardTag": "string | null",
  "confirmLabel": "string",
  "flavorText": "string",
  "resolved": false
}
```

### A-1에서 필수인 이유

- `sourceType`: UI가 빈 상태인지 아닌지 판단할 수 있어야 함
- `rewardTitle` / `rewardDescription`: placeholder라도 카드 1장은 유지돼야 함
- `confirmLabel`: 버튼 문맥이 비지 않게 함
- `resolved`: 다음 커밋에서 중복 적용 가드 시작점이 됨

### A-1에서는 아직 미뤄도 되는 것

- `contentKey`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `fromTemplateId`

이 필드들은 A-2에서 붙여도 된다. A-1은 우선 **phase와 fallback 생존성**이 목표다.

---

## fallback 규칙

A-1의 품질은 사실상 fallback 품질로 판정된다.

### 케이스 F-1. 선택 없음

```json
{
  "sourceType": "none",
  "rewardId": null,
  "rewardTitle": "챙긴 전리품 없음",
  "rewardDescription": "이번에는 아무 잔해도 들지 않고 다음 권으로 넘어간다.",
  "rewardTag": "빈손",
  "confirmLabel": "가볍게 다음 권을 연다",
  "flavorText": "이번 세그먼트의 잔향을 남겨 둔 채 다음 장을 펼친다.",
  "resolved": false
}
```

### 케이스 F-2. `selectedRewardId`는 있는데 option lookup 실패

동작 원칙:
- 에러로 런을 죽이지 않는다.
- 위 `none` snapshot으로 강등한다.
- 가능하면 로그 1줄만 남긴다.

### 케이스 F-3. `ending.overrunDraft` 자체가 없음

동작 원칙:
- 승리 엔딩 + `canOverrun`이면 여전히 `none` snapshot으로 진입 가능해야 한다.
- A-1에서는 완벽한 복원보다 **오버런 흐름 생존**을 우선한다.

---

## 권장 함수 분리

### `createOverrunEventSnapshot(state)`

역할:
- `ending.overrunDraft`를 읽는다.
- 선택 있음/없음/깨짐을 처리한다.
- 최소 snapshot을 반환한다.

권장 pseudo:

```js
function createOverrunEventSnapshot(state) {
  const draft = state.ending?.overrunDraft;
  const selectedId = draft?.selectedRewardId ?? null;
  const option = selectedId ? draft?.options?.find((item) => item.id === selectedId) ?? null : null;

  if (!option) {
    return buildNoneOverrunEventSnapshot();
  }

  return {
    sourceType: detectOverrunSourceType(option),
    rewardId: option.id,
    rewardTitle: option.title,
    rewardDescription: option.description,
    rewardTag: option.rewardTag ?? null,
    confirmLabel: option.rewardTag?.includes("적") ? "이 잔해를 들고 다음 권으로" : "이 전리품을 챙기고 다음 권으로",
    flavorText: buildEnterFlavor(option, state),
    resolved: false,
  };
}
```

### `continueOverrun(state)`

A-1 권장 pseudo:

```js
export function continueOverrun(state) {
  if (!state || state.phase !== 'ending' || !state.ending?.victory) {
    return { success: false };
  }

  state.overrunEvent = createOverrunEventSnapshot(state);
  state.phase = 'overrunEvent';

  addLog(state, '다음 권으로 넘어가기 전, 챙길 잔향을 정리합니다.', 'info');
  return { success: true, entered: true };
}
```

A-1에서는 아래를 **절대 하지 않는다**.

- `applyEffectBundle(...)`
- `state.overrunLevel += 1`
- `segmentTrophyTemplateIds = []`
- `pagePlan.push(...)`
- `currentPage += 1`
- `ending = null`
- `maybeEnterNextPage(state)`

---

## 앱/렌더 최소 계약

### `renderCenter(summary)`

A-1 최소 요구:

```js
case 'overrunEvent':
  return renderOverrunEventPlaceholder(summary);
```

### placeholder 수준에서 필요한 것

완성 카드가 아니어도 아래는 필요하다.

- 카드 1장
- `rewardTitle`
- `rewardDescription`
- CTA 1개는 없어도 됨, 단 `overrunEvent` phase임은 보여야 함
- `none`도 빈 영역이 아니어야 함

즉 A-1의 UI 증거는 “예쁜 화면”이 아니라 **phase가 안전하게 존재하고 snapshot이 보인다**다.

### `renderEnding(summary)` 문맥 보정

A-1에서는 카피를 전면 수정할 필요는 없지만, 적어도 내부 handoff 기준은 아래로 바뀌어야 한다.

- 이전: `오버런 계속 = 다음 권 진입 직전 즉시 적용`
- 이후: `오버런 계속 = 다음 권 장면으로 들어가 최종 확인 단계 진입`

즉 카피를 바로 바꾸지 못해도, 다음 커밋에서 resolve semantics와 충돌할 표현은 남겨 두지 않는 편이 좋다.

---

## A-1 smoke 권장 4종

### A1-S1. 선택 있음 enter
- 승리 엔딩 + `selectedRewardId` 있음
- `continueOverrun()` 후 `phase === 'overrunEvent'`
- `overrunEvent.rewardId` 존재
- player resource 불변

### A1-S2. 선택 없음 enter
- `selectedRewardId = null`
- `sourceType === 'none'`
- `rewardTitle` / `confirmLabel` 비어 있지 않음

### A1-S3. invalid reward fallback
- `selectedRewardId`는 있으나 options에 없음
- `sourceType === 'none'`
- 런이 죽지 않음

### A1-S4. page progression 미발생
- `continueOverrun()` 직후
- `overrunLevel`, `currentPage`, `pagePlan.length`, `segmentTrophyTemplateIds`가 그대로

이 4종은 A-1에서 “들어갔다”를 주장할 최소 증거다.

---

## 커밋 경계

### 추천 커밋 제목 예시
`docs/spec: pin overrun A-1 engine cutover contract`
또는 실제 코드 컷오버 시
`feat(overrun): split continue into event entry`

### A-1 커밋에 같이 들어가야 하는 것
- `src/game-engine.js`
- `src/app.js`의 최소 분기/placeholder
- `scripts/smoke-test.mjs`의 enter smoke
- 관련 문서 갱신

### A-1 커밋에 아직 넣지 말 것
- confirm action wiring
- full UI styling
- A-2 contentKey 정렬
- S1~S12 전체 migration

---

## owner별 handoff

### 프로그래머
- `continueOverrun()`를 enter wrapper로 바꾸는 순간이 A-1의 핵심이다.
- 즉시 적용 로직은 삭제하지 말고, 다음 커밋에서 옮길 임시 helper로 분리하는 편이 rollback이 쉽다.
- `overrunEvent` snapshot은 최소 필드만 가져도 되지만, `none` fallback은 완성도 있게 넣어야 한다.

### UI
- A-1은 완성 카드 납품 단계가 아니다.
- 단, `overrunEvent`가 실제 별도 phase라는 것을 캡처로 보여 줄 수 있어야 한다.
- placeholder가 `ending` 하위 섹션처럼 보이면 실패다.

### 아트
- 이 단계에서는 새 자산 제작보다 `none`이 빈 상태처럼 보이지 않게 하는 톤 가이드만 합의하면 충분하다.
- 풀아트/전용 장면 제작은 A-3에서 본다.

### QA
- A-1 pass 기준은 `예쁘다`가 아니라 `즉시 적용이 사라졌다`다.
- 특히 resource/page progression이 그대로인지 먼저 본다.

---

## 리스크 & 후속 과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| A-1에서 resolve까지 욕심내는 위험 | half-cutover로 엔진/smoke가 같이 깨짐 | A-1은 enter/fallback/smoke만 닫고 apply는 다음 커밋으로 미룸 |
| placeholder가 `ending` 내부 섹션처럼 보이는 위험 | phase 분리 의미 약화 | `renderCenter()` 독립 분기와 별도 카드 래퍼 유지 |
| invalid draft를 에러로 처리하는 위험 | save/load 또는 오래된 상태에서 런 중단 | 무조건 `none` fallback 강등 |
| 기존 smoke를 너무 빨리 지우는 위험 | 회귀 원인 추적 어려움 | enter smoke를 먼저 추가하고, 즉시 적용 baseline 제거는 A-4 또는 최소 A-2 이후 |

### 다음 후속 액션
1. A-1 코드 컷오버 전에 이 문서 기준으로 함수 분리 범위를 먼저 선언한다.
2. A-1이 닫히면 다음 문서는 A-2 snapshot/contentKey packet으로 이어진다.
3. A-1 단계에서는 ending primer나 onboarding surface를 건드리지 않는다.

---

## 한 줄 결론

> `overrunEvent` 첫 커밋의 진짜 목표는 장면 완성이 아니라, `continueOverrun()`를 즉시 적용 함수에서 떼어 내어 안전한 `enter` phase로 만드는 것이다. 이 문서는 그 A-1 컷오버를 파일/함수/state/smoke 단위로 고정한다.
