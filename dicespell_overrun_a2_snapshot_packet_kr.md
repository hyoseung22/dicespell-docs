# DiceSpell `overrunEvent` A-2 snapshot/normalize 컷오버 패킷

## 문서 목적

이 문서는 `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md` 다음 단계인 **A-2 = snapshot contract 정렬 + normalize/reload 복구선 고정**만을 다루는 구현 전용 handoff packet이다.

이미 DiceSpell에는 다음 문서가 있다.

- `docs/dicespell_overrun_event_content_deck_kr.md`
- `docs/dicespell_overrun_event_integration_workorder_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
- `docs/dicespell_overrun_onboarding_contract_examples_kr.md`
- `docs/dicespell_overrun_onboarding_delivery_packet_kr.md`
- `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`

지금 남은 실제 병목은 방향이 아니라 아래 질문이다.

> `A-1에서 phase 진입을 분리한 뒤, A-2에서는 정확히 어떤 필드를 snapshot에 고정하고 어떤 normalize 규칙으로 save/load drift를 막아야 하지?`

이 문서는 그 질문에 답한다. 즉 `overrunEvent` 전체를 다시 설명하지 않고, **snapshot field contract / 복구 규칙 / owner별 handoff / A2 smoke 증거**만 좁혀서 다룬다.

---

## 왜 지금 이 문서가 필요한가

A-1이 닫히면 `continueOverrun()`는 더 이상 즉시 적용 함수가 아니다. 하지만 그 다음 커밋에서 가장 흔하게 다시 흔들리는 지점은 아래 네 가지다.

1. `sourceType`만 저장하고 render 시점에 다시 header/flavor/badge/accent를 추론하려는 유혹이 생긴다.
2. `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet`를 어디서 canonicalize할지 애매하면 코드/문서/UI가 각각 다른 branch 표정을 갖게 된다.
3. `reload` 또는 구버전 save를 열 때 누락 필드가 생기면, 구현자는 `임시 문자열 보정`과 `문서 계약` 사이에서 흔들리기 쉽다.
4. `invalid rewardId`, `missing snapshot`, `legacy sourceType-only state`를 어떻게 강등할지 명시되지 않으면 `none fallback`이 실제론 제각각 생긴다.

즉 A-2에서 필요한 것은 새 아이디어가 아니라,
**snapshot이 무엇을 이미 들고 저장되어야 하는지**와
**normalize가 무엇을 절대 render 쪽으로 넘기지 않아야 하는지**를 한 장으로 고정하는 일이다.

---

## A-2의 한 줄 정의

> `overrunEvent` snapshot은 이제 `sourceType`만 가진 얇은 state가 아니라, `contentKey` 중심의 display contract를 스스로 들고 저장/복구되는 독립 payload가 된다. render는 이를 다시 추론하지 않는다.

---

## A-2 범위

### 포함
- snapshot 필수 필드 확정
- `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet` canonical source 고정
- `normalizeOverrunEventState(state)` 또는 동등 helper 정의
- reload / legacy / invalid / missing data recovery 규칙 정리
- A2-S1~S6 smoke 기준 정의

### 제외
- 중앙 카드 1장 최종 UI 완성
- confirm CTA 실제 resolve
- `overrunEvent` 이후 segment 진행 로직 변경
- 온보딩 primer helper 구현

즉 A-2의 끝은 `화면이 예뻐졌다`가 아니라,
**같은 save를 다시 열어도 같은 branch가 같은 표정으로 복구된다**다.

---

## canonical source

| 질문 | source-of-truth |
| --- | --- |
| branch별 exact content는 어디서 오나 | `docs/dicespell_overrun_event_content_deck_kr.md` |
| snapshot 샘플은 어떤 모양인가 | `docs/dicespell_overrun_onboarding_contract_examples_kr.md` |
| Step 0~4 전체 순서는 무엇인가 | `docs/dicespell_overrun_event_integration_workorder_kr.md` |
| smoke가 무엇을 검수해야 하는가 | `docs/dicespell_overrun_event_smoke_migration_packet_kr.md` |
| A-1에서 이미 보장된 것은 무엇인가 | `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md` |
| 파일/owner/commit 경계는 무엇인가 | `docs/dicespell_overrun_onboarding_delivery_packet_kr.md` |

---

## A-2 완료 정의

A-2는 아래 네 줄이 모두 참일 때만 닫혔다고 본다.

1. `state.overrunEvent`는 render용 역추론 없이 바로 소비 가능한 field set을 갖는다.
2. `normalizeOverrunEventState()`는 legacy / invalid / missing payload를 `none` 또는 canonical branch로 안정 수렴시킨다.
3. save/load 후에도 branch identity가 `contentKey` 기준으로 유지된다.
4. smoke가 branch drift와 recovery drift를 문자열 일부가 아니라 key contract 기준으로 잡아낸다.

---

## 필수 snapshot 스키마

A-2부터 `overrunEvent` snapshot은 아래 필드를 canonical field set으로 본다.

```json
{
  "sourceType": "themeTrophy | enemyTrophy | none",
  "contentKey": "overrun.theme.slime | overrun.theme.goblin | overrun.theme.lich | overrun.enemy.golem | overrun.enemy.collapse | overrun.none",
  "rewardId": "string | null",
  "rewardTitle": "string",
  "rewardDescription": "string",
  "rewardTag": "string | null",
  "badgeLabel": "string",
  "accentKey": "slime | goblin | lich | golem | collapse | neutral",
  "artTokenSet": "theme-slime | theme-goblin | theme-lich | enemy-golem | enemy-collapse | none-neutral",
  "headerTitle": "string",
  "flavorText": "string",
  "confirmLabel": "string",
  "resolved": false
}
```

### 필드별 역할

| 필드 | 역할 | render 재추론 허용 여부 |
| --- | --- | --- |
| `sourceType` | branch 상위 축 구분 | 허용하되 display 결정의 단독 근거로 쓰지 않음 |
| `contentKey` | exact branch identity | 불가 |
| `rewardId` | 선택 reward 추적/resolve 키 | 불가 |
| `rewardTitle` / `rewardDescription` | reward surface text | 불가 |
| `rewardTag` | reward card 보조 라벨 | 불가 |
| `badgeLabel` | 카드 상단 badge | 불가 |
| `accentKey` | tone/accent class | 불가 |
| `artTokenSet` | UI/아트 토큰 묶음 | 불가 |
| `headerTitle` | 장면 headline | 불가 |
| `flavorText` | 장면 flavor | 불가 |
| `confirmLabel` | CTA copy | 불가 |
| `resolved` | 중복 적용 방지 | 불가 |

핵심은 **render 단계에서는 `contentKey`를 다시 만들지 않고, 만든 `contentKey`를 그대로 읽는다**는 점이다.

---

## branch canonicalization 규칙

### themeTrophy

| 상황 | `contentKey` | `accentKey` | `artTokenSet` |
| --- | --- | --- | --- |
| 슬라임 책 테마 전리품 | `overrun.theme.slime` | `slime` | `theme-slime` |
| 고블린 책 테마 전리품 | `overrun.theme.goblin` | `goblin` | `theme-goblin` |
| 리치 책 테마 전리품 | `overrun.theme.lich` | `lich` | `theme-lich` |

### enemyTrophy

| 상황 | `contentKey` | `accentKey` | `artTokenSet` |
| --- | --- | --- | --- |
| 기록 골렘 전리품 | `overrun.enemy.golem` | `golem` | `enemy-golem` |
| 붕락 정령 전리품 | `overrun.enemy.collapse` | `collapse` | `enemy-collapse` |

### none

| 상황 | `contentKey` | `accentKey` | `artTokenSet` |
| --- | --- | --- | --- |
| 선택 없음 / invalid / legacy 복구 fallback | `overrun.none` | `neutral` | `none-neutral` |

---

## `contentKey` 결정 책임

### 프로그래머 기준

`contentKey`는 아래 두 단계를 통해 **엔진에서 먼저 확정**한다.

1. 선택한 reward option의 정체를 읽는다.
2. 그 option이 속하는 canonical branch를 `contentKey`로 매핑한다.

render는 이 값을 다시 계산하지 않는다.

### 금지 패턴

- `if (sourceType === 'themeTrophy' && rewardTitle.includes('슬라임')) ...`
- `badgeLabel` 미지정 시 app에서 fallback 문자열을 새로 만드는 방식
- CSS class를 `rewardTag` 문자열 일부에서 유추하는 방식
- save에 `sourceType`만 넣고 reload 시 content deck를 다시 뒤져 임시 구성하는 방식

---

## 권장 helper 구조

### 1) `buildCanonicalOverrunEventSnapshot(option, state)`

역할:
- 선택된 option을 canonical branch로 매핑
- content deck 기준 field set 채움
- exact branch를 완성된 snapshot으로 반환

권장 pseudo:

```js
function buildCanonicalOverrunEventSnapshot(option, state) {
  const meta = resolveOverrunEventContentMeta(option, state);

  return {
    sourceType: meta.sourceType,
    contentKey: meta.contentKey,
    rewardId: option.id,
    rewardTitle: option.title,
    rewardDescription: option.description,
    rewardTag: option.rewardTag ?? null,
    badgeLabel: meta.badgeLabel,
    accentKey: meta.accentKey,
    artTokenSet: meta.artTokenSet,
    headerTitle: meta.headerTitle,
    flavorText: meta.flavorText,
    confirmLabel: meta.confirmLabel,
    resolved: false,
  };
}
```

### 2) `buildNoneOverrunEventSnapshot()`

역할:
- `none` branch canonical field set 반환
- invalid / missing / legacy fallback의 단일 진입점 역할

주의:
- `none`도 placeholder가 아니라 **정상 branch**여야 한다.
- `badgeLabel`, `headerTitle`, `confirmLabel`이 비면 안 된다.

### 3) `normalizeOverrunEventState(state)`

역할:
- 저장된 `state.overrunEvent` 유효성 확인
- 필드 누락/invalid 값을 canonical branch로 수렴
- render와 resolve가 안전하게 읽을 수 있는 shape만 남김

권장 pseudo:

```js
function normalizeOverrunEventState(state) {
  const snapshot = state.overrunEvent;
  if (!snapshot) return buildNoneOverrunEventSnapshot();

  if (!snapshot.contentKey) {
    return normalizeLegacyOverrunEventSnapshot(snapshot, state);
  }

  if (!isKnownOverrunContentKey(snapshot.contentKey)) {
    return buildNoneOverrunEventSnapshot();
  }

  return patchMissingOverrunEventFields(snapshot);
}
```

---

## normalize 규칙

### N-1. snapshot 자체 없음

증상:
- `phase === 'overrunEvent'`인데 `state.overrunEvent == null`

대응:
- 즉시 `buildNoneOverrunEventSnapshot()`로 수복
- 런을 죽이지 않음

### N-2. legacy snapshot (`sourceType`만 있고 `contentKey` 없음)

증상:
- A-1 직후 save 또는 중간 개발 save

대응:
- `rewardId`가 유효하면 option/branch를 다시 읽어 canonical snapshot으로 승격
- option lookup 실패 시 `none`으로 강등

### N-3. `contentKey`는 있으나 field 일부 누락

증상:
- `badgeLabel`, `accentKey`, `confirmLabel` 등 일부 빠짐

대응:
- `contentKey` 기준 content deck에서 빠진 필드만 보정
- 단, `rewardTitle` / `rewardDescription`은 reward source가 없으면 임의 생성하지 않고 `none` 강등 또는 기존 snapshot 유지 정책 중 하나로 통일
- 권장: reward payload가 깨졌으면 `none` 강등

### N-4. `rewardId` invalid

증상:
- 저장된 `rewardId`가 현재 draft option과 맞지 않음

대응:
- branch identity를 유지하려 억지 복원하지 말고 `none`으로 강등
- 이유: 실제 적용 시점 A-4에서 ghost reward를 만들면 안 됨

### N-5. `resolved === true`

증상:
- 이미 처리된 snapshot이 남아 있음

대응:
- A-2에서는 재실행 방지 flag만 유지
- render는 여전히 안전하게 가능하되, A-4 resolve에서는 중복 적용을 막는 guard로 사용

---

## save/load 복구 계약

| 케이스 | 기대 동작 |
| --- | --- |
| A-1 직후 세이브 로드 | legacy snapshot을 A-2 canonical field set으로 승격 |
| 정상 A-2 save 로드 | 같은 `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet` 유지 |
| invalid reward save 로드 | `overrun.none`으로 강등 |
| `phase=overrunEvent`인데 snapshot 누락 | `overrun.none` 생성 후 계속 진행 |
| `resolved=true` save 로드 | A-4 이전에는 화면은 열리되 재실행 가드 유지 |

---

## 프로그래머 handoff

### 대상 파일
- `dice-spell-standalone/src/game-engine.js`
- 필요 시 `dice-spell-standalone/src/app.js`
- `dice-spell-standalone/scripts/smoke-test.mjs`

### deliverable
1. canonical snapshot builder 추가
2. `contentKey` 중심 branch resolve 함수 추가
3. `normalizeOverrunEventState()` 또는 동등 복구 helper 추가
4. A2-S1~S6 smoke 추가
5. render가 snapshot field만 읽는다는 계약 주석 또는 동등 명시

### DoD
- 엔진이 snapshot을 canonical field set으로 저장한다.
- reload 후 branch identity가 drift하지 않는다.
- invalid/legacy/missing이 모두 `none` 또는 canonical branch로 수렴한다.

---

## UI handoff

### UI owner가 받아야 하는 것
- `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`이 snapshot에서 이미 온다는 계약
- `none` branch도 정상 카드라는 계약
- render가 `rewardTitle` / `rewardDescription` / `rewardTag`만 display-only reward card에 재사용한다는 계약

### UI owner가 하면 안 되는 것
- `badgeLabel`을 없애고 `sourceType`로 badge 문구를 새로 짓기
- `accentKey` 대신 title 문구에 따라 CSS class를 새로 붙이기
- `themeTrophy` 내부에서 슬라임/고블린/리치를 title string으로 다시 구분하기

---

## 아트 handoff

### 아트 owner에게 넘겨야 하는 것
- `artTokenSet`은 최소 6종(`theme-slime`, `theme-goblin`, `theme-lich`, `enemy-golem`, `enemy-collapse`, `none-neutral`)
- `none-neutral`도 빈 상태 asset이 아니라 정상 branch용 neutral backplate/strip/badge tone이어야 함
- `accentKey`와 `artTokenSet`은 1:1로 읽히되, 아트 변경이 들어와도 `contentKey` identity는 바뀌지 않음

### 아트 owner 검수 포인트
- slime/goblin/lich/golem/collapse/none이 save/load 후에도 같은 톤으로 유지되는가
- `none`이 오류 화면처럼 읽히지 않는가

---

## QA handoff

### A2-S1~S6 권장 smoke

| ID | 시나리오 | 기대값 |
| --- | --- | --- |
| A2-S1 | 슬라임 themeTrophy snapshot 생성 | `contentKey=overrun.theme.slime`, `accentKey=slime`, `artTokenSet=theme-slime` |
| A2-S2 | 기록 골렘 enemyTrophy snapshot 생성 | `contentKey=overrun.enemy.golem`, `badgeLabel`/`headerTitle` canonical 유지 |
| A2-S3 | 선택 없음 fallback | `contentKey=overrun.none`, `rewardId=null`, `confirmLabel` 비지 않음 |
| A2-S4 | legacy sourceType-only save normalize | canonical field set으로 승격 |
| A2-S5 | invalid rewardId normalize | `overrun.none` 강등 |
| A2-S6 | canonical save reload | `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet` drift 없음 |

### 수동 recovery 체크
- `phase=overrunEvent`에서 새로고침/로드 후 카드 톤 유지
- `none` branch에서 CTA 의미가 비지 않음
- `rewardTitle`이 깨졌을 때 ghost reward를 그리지 않음

---

## 커밋 경계 권장안

### 한 커밋에 같이 들어가야 하는 것
- snapshot builder/normalize helper
- contentKey canonicalization
- A2-S1~S6 smoke

### 다음 커밋으로 미뤄야 하는 것
- 카드 레이아웃 polish
- confirm resolve
- primer helper 연동

이유:
A-2는 display contract와 recovery contract를 닫는 커밋이다. UI polish나 resolve를 섞으면 drift 원인을 다시 분리하기 어려워진다.

---

## 구현 전 체크리스트

- [ ] `docs/dicespell_overrun_event_content_deck_kr.md`의 exact key 표를 다시 읽었는가
- [ ] `docs/dicespell_overrun_onboarding_contract_examples_kr.md` 상태 A~C와 필드명이 맞는가
- [ ] `none`을 placeholder가 아니라 canonical branch로 취급하는가
- [ ] render에서 branch를 역추론하는 코드가 없는가
- [ ] legacy / invalid / missing recovery가 모두 smoke에 들어갔는가

---

## A-2가 닫힌 뒤 바로 이어질 작업

A-2가 닫히면 다음 A-3는 훨씬 단순해진다. 이유는 중앙 카드 1장 UI가 더 이상 branch 추론기를 품을 필요가 없고, 이미 canonical snapshot을 display-only로 소비하면 되기 때문이다.

즉 다음 커밋은 아래처럼 좁혀진다.

1. `renderOverrunEvent(summary)`를 만든다.
2. `badge/header/flavor/reward/CTA/footnote` 위계를 카드로 배치한다.
3. `accentKey` / `artTokenSet`을 style token에 연결한다.

A-2의 목표는 바로 이 상태를 만드는 것이다.

---

## 한 줄 결론

A-2는 `overrunEvent`를 예쁘게 만드는 단계가 아니라,
**save/load/branch/recovery가 같은 말을 하게 만드는 단계**다.
A-1이 문을 열었다면, A-2는 그 문 안의 payload가 더 이상 흔들리지 않게 바닥을 굳히는 작업이다.
