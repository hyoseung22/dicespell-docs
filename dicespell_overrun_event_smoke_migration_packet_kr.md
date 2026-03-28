# DiceSpell `overrunEvent` smoke 마이그레이션 패킷

## 빠른 안내

### 3줄 요약
- 이 문서는 `overrunEvent` 컷오버에서 smoke baseline을 어떻게 옮길지 정리한 **검증 마이그레이션 패킷**이다.
- 프로그래머와 QA는 `현재 baseline → 목표 fixture → step별 증거` 흐름을 먼저 보면 된다.
- 핵심은 smoke를 나중에 덧붙이는 것이 아니라, **enter → confirm → recovery 분리를 테스트도 같이 따라오게 만드는 것**이다.

**한 줄 목표:** `continueOverrun()` 즉시 적용 baseline에서 `overrunEvent` 단계형 baseline으로 검증 체계를 안전하게 옮긴다.

### 왜 이 문서가 필요한가
- 구현보다 테스트가 늦게 바뀌면 overrunEvent는 가장 쉽게 half-cutover 상태가 된다.
- 이 문서는 그 위험을 줄이기 위해 smoke migration 자체를 별도 source-of-truth로 분리한다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | baseline 차이, fixture 단계 | `integration workorder` |
| QA | S1~S12, recovery 증거 | `acceptance matrix` |
| PM | 마이그레이션 완료선 | `summary` |

---

## 문서 목적

`overrunEvent` 문서 묶음은 이미 방향, 상태 계약, 화면 구조, acceptance/recovery 완료선을 충분히 고정했다.
그런데 실제 구현 착수 직전 기준으로 아직 한 가지가 더 구체적으로 고정되지 않으면 프로그래머와 QA가 다시 멈칫할 수 있다.

> 현재 `dice-spell-standalone/scripts/smoke-test.mjs` 안의 오버런 검증은 아직 `continueOverrun()` 즉시 적용 baseline에 묶여 있고, 다음 컷오버가 요구하는 `enter -> confirm -> recovery` 분리를 fixture 수준까지 어떻게 옮길지는 한 장으로 정리돼 있지 않다.

이 문서는 그 마지막 빈칸을 메우는 **테스트 중심 handoff 문서**다.
새 설계를 더 만드는 문서가 아니라, 이미 고정된 `overrunEvent` 계약을 현재 smoke 코드 구조 위에서 **어떤 fixture 묶음, 어떤 상태 diff, 어떤 owner 납품 증거**로 바꿔야 하는지까지 구현 단위로 정리한다.

---

## 1. 왜 지금 이 문서가 필요한가

현재 관련 문서는 이미 충분하다.

- `docs/dicespell_overrun_event_implementation_packet_kr.md`
- `docs/dicespell_overrun_event_screen_packet_kr.md`
- `docs/dicespell_overrun_event_acceptance_matrix_kr.md`
- `docs/dicespell_overrun_event_integration_workorder_kr.md`
- `docs/dicespell_overrun_event_source_of_truth_map_kr.md`

하지만 실제 코드 착수 순간에는 아래 병목이 남아 있다.

1. `smoke-test.mjs`의 현재 오버런 검증은 여전히 `continueOverrun()` 호출 직후 Shield/주입/강화가 이미 적용되는지 확인한다.
2. 이 baseline을 그대로 들고 있으면 프로그래머는 새 `enterOverrunEvent()` 경로를 붙이면서도 기존 assertion을 동시에 만족시키려다 half-cutover 상태에 빠질 수 있다.
3. QA는 acceptance 12종 + recovery 6종을 알고 있어도, 그것을 현재 smoke 파일 안에서 어떤 fixture 묶음으로 재현할지 바로 연결되지 않는다.
4. UI/아트는 화면 구조 계약을 알고 있어도, 어떤 상태 캡처를 pass 증거로 제출해야 하는지 테스트 기준선이 모호할 수 있다.

즉 지금 필요한 것은 문서 추가 자체가 아니라, **현재 smoke baseline을 다음 컷오버 테스트 패킷으로 갈아타는 정확한 이행표**다.

---

## 2. 현재 smoke baseline이 실제로 무엇을 가정하는가

현재 `dice-spell-standalone/scripts/smoke-test.mjs`에는 최소 두 종류의 오버런 baseline이 있다.

### 2.1 단순 오버런 진입 baseline

현재 smoke는 아래를 한 번에 기대한다.

- 승리 엔딩 상태에서 `continueOverrun(state)` 호출
- `overrunLevel` 증가
- `pagePlan` 새 세그먼트 추가
- `currentPage` 증가
- `phase === 'battle'`

즉 액션 한 번으로 `enter + resolve + next page`가 모두 끝난다고 가정한다.

### 2.2 보관함 전리품 적용 baseline

현재 smoke는 아래도 한 번에 기대한다.

- `chooseEndingOverrunReward()`로 `selectedRewardId` 기록
- `continueOverrun()` 호출
- Shield 증가 / hand 강화 / element 주입이 이미 끝남
- `segmentTrophyTemplateIds` 초기화 완료

즉 테스트 기준선 자체가 `즉시 적용` 구조다.

이 baseline은 현재 런타임 설명으로는 맞지만, 다음 `overrunEvent` 컷오버의 pass 조건으로는 더 이상 맞지 않는다.

---

## 3. 다음 smoke 패킷의 최종 목표

이번 마이그레이션이 끝났다고 말하려면 아래 문장이 smoke와 수동 증거 모두에서 참이어야 한다.

> `continue-overrun`은 `overrunEvent` 진입만 수행하고, 실제 보상 적용은 `confirm-overrun-event`에서만 1회 일어난다. 저장 상태가 일부 깨져도 `none` fallback으로 런이 계속 진행된다.

이 문장을 만족하지 못하면 엔진/UI가 붙어도 테스트 기준선은 아직 구버전이다.

---

## 4. fixture 재편 원칙

### 4.1 기존 단일 통합 fixture를 억지로 유지하지 않는다

현재 baseline은 `선택 -> continueOverrun() -> 즉시 적용` 하나로 묶여 있다.
다음 구조에서는 이를 아래 세 축으로 쪼개야 한다.

1. **enter fixture**: `ending -> overrunEvent` 진입 검증
2. **confirm fixture**: `overrunEvent -> 다음 세그먼트` resolve 검증
3. **recovery fixture**: save/load 비정상 상태에서 `none` 또는 안전 복귀 검증

### 4.2 fixture 이름이 계약을 말해야 한다

권장 네이밍 예시:

- `buildEndingWithOverrunDraft()`
- `buildThemeTrophyEndingState()`
- `buildEnemyTrophyEndingState()`
- `buildNoneOverrunState()`
- `buildBrokenOverrunSave()`
- `assertOverrunEventEntered()`
- `assertOverrunRewardStillPending()`
- `assertOverrunResolveAppliedExactlyOnce()`
- `assertOverrunFallbackSnapshot()`

핵심은 `테스트 이름만 봐도 지금 enter를 보는지, confirm을 보는지, recovery를 보는지`가 드러나야 한다는 점이다.

### 4.3 diff assertion은 자원/phase/sourceType 세 층으로 나눈다

권장 검증 축:

- **상태 전이**: `phase`, `ending`, `overrunEvent`, `currentPage`, `pagePlan.length`
- **보상 적용 여부**: `player.shield`, `player.mp`, `handSlots[].element`, `handSlots[].powerLevel`
- **복구 스냅샷**: `sourceType`, `rewardId`, `rewardTitle`, `confirmLabel`, `resolved`

즉 다음 smoke는 숫자만 보는 것이 아니라, **phase와 snapshot이 실제로 분리됐는지**를 같이 봐야 한다.

---

## 5. 권장 smoke 패킷 12종

아래 12종은 `acceptance 12종 + recovery 6종` 전체를 전부 한 파일에서 한 번에 만들라는 뜻이 아니라, 현재 오버런 baseline을 우선 교체하는 **최소 구현 우선순위**다.

### S1. theme trophy enter

**준비 상태**
- 승리 엔딩
- `canOverrun=true`
- `selectedRewardId`가 책 전리품 bundle

**검증**
- `continueOverrun()` 또는 enter wrapper 호출 후 `phase === 'overrunEvent'`
- `overrunEvent.sourceType === 'themeTrophy'`
- `overrunEvent.rewardId`/`rewardTitle`/`confirmLabel` 존재
- 아직 Shield/MP/주입/강화 변화 없음

### S2. enemy trophy enter

**준비 상태**
- 승리 엔딩
- `selectedRewardId`가 적 전리품 bundle

**검증**
- `sourceType === 'enemyTrophy'`
- 적 잔해 문맥 카피 존재
- 아직 bundle 미적용

### S3. none enter

**준비 상태**
- 승리 엔딩
- `selectedRewardId = null`

**검증**
- `sourceType === 'none'`
- `rewardId === null`
- `rewardTitle`, `rewardDescription`, `confirmLabel`이 placeholder가 아니라 실제 fallback 문구로 채워짐
- CTA 진행 가능

### S4. theme confirm resolve

**준비 상태**
- S1 직후 상태

**검증**
- `confirm-overrun-event` 호출 후 책 전리품 효과 1회 적용
- `overrunLevel` 증가
- `pagePlan` 세그먼트 증가
- `currentPage` 증가
- `segmentTrophyTemplateIds` 초기화
- `phase`가 `library` 경유 후 정상 전투/상점 흐름으로 복귀

### S5. enemy confirm resolve

**준비 상태**
- S2 직후 상태

**검증**
- 적 전리품 bundle 1회 적용
- 전리품별 자원/주입/강화가 기대값과 일치
- 로그 또는 상태 변화에 적 전리품 맥락이 남음

### S6. none confirm resolve

**준비 상태**
- S3 직후 상태

**검증**
- 보상 수치 변화 없이 다음 세그먼트만 정상 준비
- 런이 멈추지 않음
- `segmentTrophyTemplateIds` 초기화

### S7. invalid reward recovery

**준비 상태**
- `phase='overrunEvent'`
- `rewardId`는 있는데 option lookup 불가

**검증**
- `sourceType='none'`으로 강등
- CTA 유지
- 런 생존

### S8. missing snapshot recovery

**준비 상태**
- `phase='overrunEvent'`
- `overrunEvent` payload 누락
- `ending.overrunDraft`는 잔존

**검증**
- snapshot 재생성 또는 `none` fallback 생성
- 중단 없이 렌더 가능

### S9. broken sourceType recovery

**준비 상태**
- `sourceType='weird'`

**검증**
- `none` 강등
- 배지/CTA/카피 기본값 재주입

### S10. resolved reload guard

**준비 상태**
- `phase='overrunEvent'`
- `resolved=true`

**검증**
- resolve 재실행 금지
- 보상 중복 적용 없음
- 안전 복귀 또는 재구성만 수행

### S11. missing copy reinjection

**준비 상태**
- `confirmLabel` 또는 `flavorText` 누락 snapshot

**검증**
- 기본 카피 재주입
- 버튼/헤더가 빈 상태로 남지 않음

### S12. visual density sanity fixture

**준비 상태**
- `themeTrophy`, `enemyTrophy`, `none` 3상태 summary

**검증**
- 최소 DOM snapshot 또는 render string 비교로 카드 1장 + CTA 1개 + footnote 1줄 유지
- `none`도 카드가 사라지지 않음

---

## 6. 상태 diff 체크리스트

### 6.1 enter 시점에 반드시 바뀌어야 하는 것

- `phase: 'ending' -> 'overrunEvent'`
- `overrunEvent` snapshot 생성

### 6.2 enter 시점에 절대 바뀌면 안 되는 것

- `player.shield`
- `player.mp`
- `handSlots[].element`
- `handSlots[].powerLevel`
- `segmentTrophyTemplateIds`
- `pagePlan.length`
- `currentPage`

즉 enter fixture는 **화면 상태만 바뀌고 런 효력은 아직 그대로**여야 한다.

### 6.3 confirm 시점에 반드시 바뀌어야 하는 것

- 선택한 bundle 효과 1회 적용
- `overrunLevel += 1`
- `segmentTrophyTemplateIds = []`
- `pagePlan` 증가
- `currentPage += 1`
- `ending = null`
- `overrunEvent.resolved = true` 또는 최종 cleanup

### 6.4 confirm 시점에 절대 두 번 바뀌면 안 되는 것

- bundle 효과 수치
- pagePlan 증가량
- currentPage 증가량

즉 confirm fixture는 **정확히 한 번**만 성공해야 한다.

---

## 7. 파일별 handoff 메모

### 7.1 프로그래머에게 넘길 것

- 현재 smoke 2개는 구버전 baseline이므로 유지 대상이 아니라 분해 대상이다.
- `continueOverrun()` 관련 기존 assertion은 `phase === 'battle'`가 아니라 우선 `phase === 'overrunEvent'`로 바뀌어야 한다.
- 기존 `선택 reward 즉시 적용` assertion은 confirm fixture로 이동해야 한다.
- 새로운 helper는 `ending fixture builder`와 `overrunEvent assertion helper`를 먼저 만들고, 그 위에 세부 케이스를 쌓는 편이 안전하다.

### 7.2 UI 오너에게 넘길 것

- UI pass 증거는 최소 3장이다: `themeTrophy`, `enemyTrophy`, `none`.
- `none`은 QA 편의상 생략 가능한 상태가 아니라, smoke와 같은 우선순위의 render evidence가 필요하다.
- `ending`과 `overrunEvent` 비교 캡처 1장은 필수다. 그래야 새로운 phase가 실제로 별도 장면인지 검수 가능하다.

### 7.3 아트 오너에게 넘길 것

- 테스트 기준에서도 `none`은 빈 화면이면 실패다.
- 배지 3종은 screenshot 차이로 바로 보일 정도의 signifier가 있어야 한다.
- backplate/accent는 theme/enemy/none 세 상태를 최소한 색/shape로 분리해야 한다.

### 7.4 QA에게 넘길 것

- 기존 baseline pass는 이제 회귀선이 아니라 교체 대상이다.
- 이번 패킷의 우선 검수 순서는 `S1~S6` 이후 `S7~S11`, 마지막 `S12`다.
- `resolved reload`, `missing snapshot`, `invalid reward`는 옵션 테스트가 아니라 저장 경계 필수 테스트다.

---

## 8. 구현 순서 제안

### Step 1. 현재 baseline smoke를 보호 복사한다

- 기존 오버런 smoke 블록을 바로 수정하지 말고, 임시로 `legacy immediate-apply baseline` 주석 아래 보존한 뒤 단계별로 교체한다.
- 이유: enter/confirm 분리 중 어디서 깨지는지 보기 쉽다.

### Step 2. S1~S3 enter 3종을 먼저 붙인다

- 실제 `phase='overrunEvent'`가 먼저 살아야 UI와 엔진 양쪽이 붙는다.
- 이 단계에서는 아직 confirm smoke를 강하게 쓰지 않는다.

### Step 3. S4~S6 confirm 3종으로 기존 즉시 적용 assertion을 이동한다

- 기존 Shield/주입/강화 assertion은 전부 여기로 옮긴다.
- 이 단계가 끝나면 `legacy immediate-apply baseline`은 삭제한다.

### Step 4. S7~S11 recovery를 붙인다

- save/load 복구선이 실제로 계약과 일치하는지 닫는다.

### Step 5. S12 visual sanity를 DOM snapshot 또는 render string 체크로 추가한다

- UI/아트와 QA가 같은 증거를 보게 만든다.

---

## 9. 리스크 & 후속 과제

### 리스크 1. 기존 smoke를 너무 오래 남겨 half-cutover를 허용할 위험

- 영향: 엔진은 새 phase를 갖는데 테스트는 즉시 적용까지 동시에 요구해 구현이 꼬인다.
- 대응: enter 3종과 confirm 3종이 붙는 즉시 legacy baseline을 제거한다.

### 리스크 2. recovery가 여전히 수동 테스트로만 남을 위험

- 영향: save/load 경계에서 런 생존성이 문서로만 존재하게 된다.
- 대응: S7~S11을 smoke fixture로 승격한다.

### 리스크 3. UI/아트 검수가 텍스트 위주로만 끝날 위험

- 영향: `none`이 빈 화면처럼 보여도 엔진 smoke만 통과하면 넘어갈 수 있다.
- 대응: S12와 상태별 캡처 3종을 owner 납품 증거로 고정한다.

### 후속 과제

이 문서 다음 우선순위는 추가 문서 확장이 아니다.

1. 실제 `continueOverrun()` enter화
2. `overrunEvent` snapshot/resolve/normalize 구현
3. 현재 smoke baseline을 S1~S12 구조로 교체
4. 그 다음에만 오버런 바리에이션 또는 후속 콘텐츠 검토

---

## 10. 한 줄 결론

이번에 남아 있던 마지막 handoff 공백은 `overrunEvent` 설계가 아니라, **현재 즉시 적용 smoke baseline을 어떤 fixture 묶음으로 갈아타야 실제 컷오버가 안전해지는가**였다. 이 문서는 그 테스트 이행표를 고정해, 프로그래머/QA/UI/아트가 같은 pass 조건으로 구현에 들어가게 만드는 smoke 마이그레이션 패킷이다.
