# DiceSpell `overrunEvent` 통합 작업지시서

## 빠른 안내

### 3줄 요약
- 이 문서는 `overrunEvent` 문서 묶음을 실제 구현 순서로 재배열한 **컷오버 작업지시서**다.
- 프로그래머는 Step 0~4와 파일 경계를 먼저, QA는 smoke와 recovery 이행 순서를 먼저 보면 된다.
- 핵심은 한 번에 다 바꾸지 않고 **enter → UI → confirm → recovery** 순으로 같은 기준을 유지하는 것이다.

**한 줄 목표:** `overrunEvent`를 안전한 단계별 컷오버 순서로 옮긴다.

### 왜 이 문서가 필요한가
- 문서가 충분히 많아진 뒤의 병목은 설명 부족이 아니라 실행 순서 혼선이다.
- 이 문서는 그 혼선을 줄여 구현/리뷰/QA가 같은 step 번호를 쓰게 만든다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | Step 0~4, 파일 경계, 상태 변화 금지선 | `smoke migration packet` |
| QA | step별 smoke / recovery | `acceptance matrix` |
| UI | 어느 step에서 화면이 닫히는지 | `screen packet` |
| PM | 커밋 묶음과 handoff 순서 | `summary`, `production cutsheet` |

---

## 문서 목적

이 문서는 이미 작성된 아래 5개 문서를, **실제 구현 슬롯에서 바로 사용할 수 있는 컷오버 작업지시서**로 다시 묶는 최종 보조 문서다.

- `docs/dicespell_overrun_event_handoff_kr.md`
- `docs/dicespell_overrun_event_implementation_packet_kr.md`
- `docs/dicespell_overrun_event_screen_packet_kr.md`
- `docs/dicespell_overrun_event_production_cutsheet_kr.md`
- `docs/dicespell_overrun_event_acceptance_matrix_kr.md`

지금까지의 문서들은 방향, 화면, acceptance, recovery, owner별 DoD를 충분히 고정했다.
하지만 실제 구현 직전 기준으로는 아직 한 가지가 여러 문서에 흩어져 있었다.

> 현재 코드 위에서 `continueOverrun()`를 어떤 순서로 잘라야 하고, `ending` / `overrunEvent` / 다음 세그먼트 준비 상태 중 무엇이 언제 source of truth가 되는가?

이번 문서는 그 마지막 공백만 메운다.
즉 새 기획서가 아니라, **현재 코드베이스 기준 통합 순서 · 상태 소유권 · smoke 마이그레이션 · owner별 실제 납품 묶음**을 한 장으로 고정하는 작업지시서다.

---

## 1. 왜 이 문서가 지금 필요한가

현재 `overrunEvent` 관련 문서 묶음은 이미 충분히 풍부하다.
문제는 문서가 부족해서가 아니라, 구현자가 실제로 손을 대는 순간 아래 세 가지가 다시 분산될 수 있다는 점이다.

1. `continueOverrun()`를 **enter wrapper**로 줄이려는 순간, 기존 smoke와 기존 호출 기대치가 함께 깨진다.
2. `ending.overrunDraft.selectedRewardId`와 새 `overrunEvent` snapshot이 한동안 동시에 살아 있어, 어느 쪽이 source of truth인지 헷갈리기 쉽다.
3. UI/아트는 화면 구조를 알고 있어도, 실제로 어떤 시점부터 어떤 slot/class에 자산을 얹으면 되는지 구현 순서가 없으면 대기 상태로 남는다.

즉 지금 남은 병목은 아이디어나 화면이 아니라 **컷오버 순서와 상태 책임 분리**다.

---

## 2. 현재 코드 기준 실제 병목

현재 실제 코드 기준 핵심 병목은 아래 4개다.

### 2.1 `continueOverrun(state)`가 너무 많은 책임을 한 번에 가진다

현재 `dice-spell-standalone/src/game-engine.js`의 `continueOverrun(state)`는 아래를 한 번에 처리한다.

1. 엔딩 상태 검증
2. 선택한 보관함 reward lookup
3. bundle 즉시 적용
4. `overrunLevel` 증가
5. `segmentTrophyTemplateIds` 초기화
6. `pagePlan`에 다음 세그먼트 추가
7. `currentPage` 증가
8. `phase = 'library'`
9. `ending = null`
10. `maybeEnterNextPage(state)` 호출

즉 지금은 `enter`, `resolve`, `cleanup`, `next-page transition`이 한 함수에 겹쳐 있다.

### 2.2 `renderEnding(summary)`가 아직 즉시 적용 문맥을 말하고 있다

현재 `dice-spell-standalone/src/app.js`의 엔딩 카피는 아래 전제를 유지한다.

- `이번 세그먼트에서 건진 전리품 중 하나를 골라 다음 권 진입 직전에 즉시 적용합니다.`

하지만 새 `overrunEvent` 기준에서는 `continue-overrun` 직후에는 아직 미적용이어야 하므로, 이 문구는 실제 구현 완료선과 어긋난다.

### 2.3 `renderCenter(summary)`에 `overrunEvent` 분기가 아직 없다

즉 실제 phase 전이가 붙으려면 렌더러 쪽에서 **독립 화면**으로 인지하는 분기부터 추가되어야 한다.

### 2.4 기존 smoke는 “즉시 적용” 버전 기준이다

현재 `dice-spell-standalone/scripts/smoke-test.mjs`의 관련 케이스는 아래를 검증한다.

- 엔딩에서 reward 선택 저장
- `continueOverrun()` 호출
- 선택한 reward 효과가 이미 적용됨
- `segmentTrophyTemplateIds` 초기화됨

즉 새 구조에서는 기존 smoke를 단순 유지할 수 없고, **enter smoke / confirm smoke / recovery smoke**로 분리해야 한다.

---

## 3. 이번 bounded 구현의 최종 목표

이번 구현이 끝났다고 말하려면 아래 문장이 코드/화면/smoke 모두에서 참이어야 한다.

> `continue-overrun`은 더 이상 즉시 적용이 아니라 `overrunEvent` 진입이다. 실제 보상 적용은 `confirm-overrun-event`에서만 1회 일어나고, save/load가 깨져도 `none` fallback으로 런이 살아남는다.

이 문장을 만족하지 못하면 이번 bounded 작업은 아직 닫히지 않은 상태다.

---

## 4. source of truth 소유권 매트릭스

이번 구현에서 가장 중요한 것은 상태별 소유권을 섞지 않는 것이다.

| 단계 | source of truth | 의미 | 이 단계에서 하면 안 되는 것 |
| --- | --- | --- | --- |
| 엔딩 선택 전 | `ending.overrunDraft.options` | 보관함 후보 목록 | `overrunEvent` 미리 생성 |
| 엔딩 선택 후 | `ending.overrunDraft.selectedRewardId` | 무엇을 들고 갈지 선택만 기록 | bundle 즉시 적용 |
| `continue-overrun` 직후 | `overrunEvent` snapshot | 화면/카피/CTA/복구용 단일 snapshot | 다음 세그먼트 효과 적용 |
| `confirm-overrun-event` 직후 | resolve 결과 상태 | reward 1회 적용 + 다음 세그먼트 준비 완료 | `ending`/`overrunEvent`를 source of truth로 계속 유지 |
| 다음 페이지 진입 후 | 런타임 기본 상태 | `battle/shop/library` 등 정상 플로우 | `overrunEvent` 재진입 |

핵심은 아래 두 줄이다.

- `ending.overrunDraft`는 **선택 기록용 source of truth**다.
- `overrunEvent`는 **렌더/복구/최종 확인용 snapshot**이다.

즉 둘이 같은 역할을 하면 안 된다.

---

## 5. 컷오버 구현 순서

이번 구현은 아래 순서로 자르는 것이 가장 안전하다.

## Step 0. 함수 의미를 먼저 바꾼다

### 해야 할 일

- `continueOverrun(state)`를 더 이상 resolve 함수로 취급하지 않는다는 점을 먼저 코드/주석/문서에서 고정한다.
- 외부 액션 이름 `continue-overrun`은 유지해도 괜찮지만, 의미는 `enterOverrunEvent()` wrapper가 된다.

### 이 단계의 완료선

- 구현자가 `continue-overrun` 클릭 후 바로 효과가 적용된다고 가정하지 않는다.
- UI/QA 문서도 이 기준으로 읽힌다.

---

## Step 1. 엔진에 snapshot/enter/resolve/normalize를 분리한다

### 권장 함수 세트

- `createOverrunEventSnapshot(state)`
- `enterOverrunEvent(state)`
- `resolveOverrunEvent(state)`
- `normalizeOverrunEventState(state)`

### 각 함수 책임

#### `createOverrunEventSnapshot(state)`

- `ending.overrunDraft.selectedRewardId`와 `ending.overrunDraft.options`를 읽는다.
- `themeTrophy`, `enemyTrophy`, `none` 중 하나를 결정한다.
- `headerTitle`, `flavorText`, `confirmLabel`, `rewardTag`, `rewardTitle`, `rewardDescription`까지 snapshot에 채운다.

#### `enterOverrunEvent(state)`

- `ending.victory`, `canOverrun`, grimoire 유효성 검증
- snapshot 생성
- `phase = 'overrunEvent'`
- 아직 bundle 미적용

#### `resolveOverrunEvent(state)`

- 선택된 reward가 bundle이면 이 시점에 1회 적용
- `overrunLevel` 증가
- `segmentTrophyTemplateIds` 초기화
- `pagePlan` 확장
- `currentPage` 증가
- `reward/shop/battle/ending/overrunEvent` 정리
- `phase = 'library'` 후 `maybeEnterNextPage(state)` 호출

#### `normalizeOverrunEventState(state)`

- `phase='overrunEvent'`인데 snapshot 누락 시 재생성 시도
- `rewardId` lookup 실패 시 `none` 강등
- `resolved=true` 상태 재로드 시 중복 적용 금지
- 누락 카피는 기본 카피 재주입

### 이 단계의 완료선

- `continueOverrun(state)`는 내부적으로 `enterOverrunEvent(state)`만 호출한다.
- 기존 즉시 적용 로직은 `resolveOverrunEvent(state)`로 이동한다.

---

## Step 2. UI를 최소 동작 상태로 먼저 붙인다

### 해야 할 일

- `renderCenter(summary)`에 `case 'overrunEvent'` 추가
- `renderOverrunEvent(summary)` 신설
- `data-action="confirm-overrun-event"` 연결
- `renderEnding(summary)`의 보관함 안내 문구를 `최종 확인 후 적용` 기준으로 수정

### 최소 DOM 구조

- eyebrow
- H1
- flavor copy
- focus card 1장
- CTA 1개
- footnote 1줄

### 이 단계에서 아직 안 해도 되는 것

- 최종 아트 자산 적용
- 세부 accent polish
- 고급 장면 연출

### 이 단계의 완료선

- 텍스트/임시 배지 기준으로도 `themeTrophy`, `enemyTrophy`, `none` 화면이 모두 뜬다.
- CTA를 누르기 전까지는 reward 효과가 미적용 상태다.

---

## Step 3. recovery를 구현 완료선으로 승격한다

이 단계는 선택이 아니라 필수다.

### 반드시 닫아야 하는 recovery

1. `phase='overrunEvent'`인데 snapshot 없음
2. `rewardId` lookup 실패
3. `sourceType` 비정상 값
4. `resolved=true` 재로드
5. `header/flavor/confirmLabel` 누락
6. `none` placeholder payload 누락

### 이 단계의 완료선

- 위 6종 모두에서 런이 죽지 않는다.
- 최악의 경우에도 `none` fallback 카드 + CTA + footnote가 유지된다.

---

## Step 4. 아트/스타일을 slot 단위로 얹는다

### 필요한 class/slot

- `.overrun-event-card`
- `.overrun-event-shell`
- `.overrun-event-focus-card`
- `.overrun-event-reward-card`
- `.overrun-event-head`
- `.overrun-event-actions`
- `.overrun-source-badge.theme`
- `.overrun-source-badge.enemy`
- `.overrun-source-badge.none`

### 아트가 얹혀야 하는 순서

1. 헤더 패널
2. 배지 3종
3. focus backplate
4. accent 4종

### 이 단계의 완료선

- 텍스트를 읽지 않아도 상태 구분이 어느 정도 된다.
- `none`도 임시 화면처럼 보이지 않는다.

---

## 6. smoke 마이그레이션 작업표

기존 smoke는 `continueOverrun()` 즉시 적용 구조를 전제로 한다.
이번 구현에서는 아래처럼 테스트를 갈아타는 것이 맞다.

| 기존 smoke 의미 | 새 smoke로 쪼갤 항목 | 기대 결과 |
| --- | --- | --- |
| 엔딩 reward 선택 후 `continueOverrun()` 성공 | S1. 엔딩 선택 저장 smoke | `selectedRewardId`가 ending에 저장 |
| `continueOverrun()` 후 보상 적용 확인 | S2. enter smoke | `phase='overrunEvent'`, 아직 미적용 |
| 동일 | S3. confirm smoke | CTA 후 1회 적용 |
| `segmentTrophyTemplateIds` 초기화 확인 | S4. resolve cleanup smoke | resolve 후 이력 초기화 |
| 암묵적 다음 세그먼트 진입 | S5. next-page smoke | pagePlan 증가 + 다음 페이지 진입 |
| 없음 | S6. `none` fallback smoke | 선택 없음에서도 정상 렌더/진행 |
| 없음 | S7. invalid reward recovery smoke | 런 생존 + `none` 강등 |
| 없음 | S8. `resolved=true` reload smoke | 중복 적용 없음 |
| 없음 | S9. missing snapshot recovery smoke | 재생성 또는 `none` fallback |

### 권장 smoke 최소 세트

1. 선택된 책 전리품 -> enter -> confirm
2. 선택된 적 전리품 -> enter -> confirm
3. 선택 없음 -> enter -> confirm
4. invalid reward save -> `none`
5. missing snapshot -> 재생성 또는 `none`
6. `resolved=true` reload -> 중복 적용 없음

즉 기존 1개 통합 smoke를 유지하려고 억지로 끌고 가기보다, **enter/confirm/recovery 3축으로 쪼개는 편이 이 phase의 실제 계약과 맞다.**

---

## 7. owner별 실제 전달 묶음

## 7.1 프로그래머에게 넘길 것

### 반드시 구현할 것

- `continueOverrun()` enter wrapper화
- snapshot/create/resolve/normalize 4단 분리
- `confirm-overrun-event` 액션 연결
- recovery 6종 smoke/fixture

### 프로그래머 납품 완료선

- 엔딩 선택이 `overrunEvent`에서 다시 보인다.
- CTA 전 미적용 / CTA 후 1회 적용이 상태 diff로 증명된다.
- save/load 이상 케이스가 모두 런 생존으로 귀결된다.

---

## 7.2 UI 오너에게 넘길 것

### 반드시 고정할 것

- 중앙 카드 1장
- CTA 1개
- footnote 1줄
- `문맥 -> 대상 -> 행동` 순서
- `none` 동일 밀도 화면

### UI 납품 완료선

- `ending reward grid`와 `overrunEvent`가 전혀 다른 계층으로 읽힌다.
- `themeTrophy`, `enemyTrophy`, `none`가 카피/배지/구조에서 모두 구분된다.

---

## 7.3 아트 오너에게 넘길 것

### 반드시 준비할 것

- 헤더 패널 1종
- 배지 3종
- focus backplate 1세트
- accent 4종

### 아트 납품 완료선

- 코드 임시색을 제거해도 상태 구분이 남는다.
- `none`이 빈 화면처럼 보이지 않는다.

---

## 7.4 QA에게 넘길 것

### 반드시 닫을 것

- acceptance 12종
- recovery 6종
- 특히 `none`, invalid reward, missing snapshot, resolved reload

### QA 납품 완료선

- 이 phase의 실패 모드가 `런 중단`이 아니라 `fallback 진행`으로 설계되었음을 pass 결과로 증명한다.

---

## 8. 금지 shortcuts

이번 구현에서 아래 shortcut은 금지로 본다.

1. `continueOverrun()` 안에 enter와 resolve를 둘 다 남겨 두는 것
2. `overrunEvent` 화면에서 reward를 다시 lookup/추론하는 것
3. `none` 상태를 카드 없는 간이 문구로 처리하는 것
4. recovery를 `나중 smoke`로 미루는 것
5. 기존 smoke 1개가 있으니 새 recovery smoke를 생략하는 것

이 다섯 가지를 허용하면 이번 bounded 작업은 다시 `보이는 것만 붙고 실제 계약은 안 닫힌` 상태로 돌아간다.

---

## 9. 리스크 & 후속 과제

### 리스크 1. 문서는 충분한데 컷오버 순서가 없어서 실제 구현이 다시 망설여질 위험

- 영향: handoff-ready 문서가 쌓였는데도 `어디서부터 자르지?` 때문에 또 한 슬롯을 잃을 수 있다.
- 대응: 이번 문서에서 Step 0~4 순서를 고정하고, 기존 `continueOverrun()` 분해를 가장 먼저 시작점으로 못 박는다.

### 리스크 2. `ending`과 `overrunEvent`가 둘 다 source of truth처럼 다뤄질 위험

- 영향: save/load와 UI가 서로 다른 데이터를 보게 된다.
- 대응: `ending.overrunDraft`는 선택 기록, `overrunEvent`는 렌더/복구 snapshot이라는 책임 분리를 문서와 smoke 모두에 명시한다.

### 리스크 3. 기존 smoke가 즉시 적용 구조를 계속 암묵적으로 강요할 위험

- 영향: 구현은 바뀌었는데 테스트가 옛 계약을 붙들고 있게 된다.
- 대응: 이번 문서에서 smoke를 enter/confirm/recovery 축으로 재편하는 것을 별도 작업 항목으로 고정한다.

### 후속 과제

이 문서가 붙은 뒤 다음 우선순위는 더 이상의 오버런 문서 추가가 아니다.

1. `continueOverrun()` enter화
2. `overrunEvent` snapshot/resolve/normalize 실제 구현
3. smoke 12+6 고정
4. 그 다음에만 `보스형 골렘` 또는 오버런 바리에이션 검토

---

## 10. 한 줄 결론

`overrunEvent`에 남아 있던 마지막 공백은 방향이 아니라 **현재 코드 위에서의 컷오버 순서와 상태 소유권**이었다. 이 문서는 `continueOverrun()`를 실제로 어떻게 쪼개고, 기존 smoke를 어떤 순서로 갈아타며, 누구에게 무엇을 넘기면 구현이 닫히는지까지 한 장으로 고정하는 통합 작업지시서다.
