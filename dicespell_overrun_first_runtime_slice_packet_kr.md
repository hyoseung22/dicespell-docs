# DiceSpell `overrunEvent` 첫 런타임 슬라이스 패킷

## 3줄 요약
- 이 문서는 `overrunEvent` 문서가 이미 충분한 상태에서, **실제 첫 구현 슬라이스를 어디까지 닫아야 하는가**를 한 장으로 다시 고정하는 source handoff다.
- 범위는 새 시스템 추가가 아니라, `A-1 enter 분리`와 `A-2 canonical snapshot floor`를 **한 번의 bounded runtime slice**로 묶어 프로그래머/QA/UI/아트가 같은 baseline을 보게 만드는 데 있다.
- 목표는 `continueOverrun()` 즉시 적용 체인을 끊고, UI가 title 문자열이나 reward tag를 다시 추론하지 않아도 되는 canonical payload 바닥을 먼저 닫는 것이다.

## 한 줄 목표
`overrunEvent` 실구현의 **첫 실제 코드 슬라이스를 `enter + canonical snapshot + recovery smoke`까지만** 고정해, 이후 A-3 UI와 A-4 resolve가 안정된 payload 위에서만 열리게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell에는 이미 아래 문서가 있다.

- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`
- `docs/dicespell_overrun_a2_snapshot_packet_kr.md`
- `docs/dicespell_overrun_event_content_deck_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`

문제는 실구현 직전에도 여전히 아래 혼선이 남는다는 점이다.

1. `A-1만 닫을지`, `A-1과 A-2를 같이 닫을지`를 implementer가 다시 임의로 판단하게 된다.
2. A-1만 먼저 열고 canonical payload를 뒤로 미루면, A-3 UI가 다시 `reward.title`, `reward.description`, `sourceType` 같은 얇은 값에서 badge/accent/header를 역추론하게 된다.
3. QA는 enter smoke와 snapshot recovery smoke를 한 슬라이스의 종료 증거로 묶어야 하는데, 문서가 분리돼 있어 실제 커밋 범위가 다시 흔들릴 수 있다.
4. UI/아트 오너는 `언제부터 참여해도 되는지`가 불분명하면, payload가 아직 고정되지 않은 상태에서 shell/카피 검토를 시작해 drift를 키울 수 있다.

즉 지금 필요한 것은 새 설계가 아니라, **첫 실제 runtime slice의 경계와 handoff 시점**을 한 장으로 다시 압축하는 일이다.

---

## 1. 이 문서를 누가 어떻게 읽어야 하나

### 프로그래머
1. `2. 이번 슬라이스 범위`
2. `3. 파일별 패치 경계`
3. `4. canonical snapshot floor`
4. `5. smoke 종료 증거`
5. 그다음 `docs/dicespell_overrun_a3_ui_packet_kr.md`는 **읽기만 하고 구현은 보류**한다.

### QA
1. `5. smoke 종료 증거`
2. `6. recovery 기준`
3. `8. 이 슬라이스에서 금지하는 것`

### UI 오너
1. `4. canonical snapshot floor`
2. `7. 역할별 handoff`
3. 이 문서에서는 **UI 구현 착수 조건**만 확인한다.

### 아트 오너
1. `7. 역할별 handoff`
2. `9. 다음 슬라이스로 넘기는 것`
3. 이 슬라이스에서는 **art token set freeze 기준**만 확인한다.

---

## 2. 이번 슬라이스 범위

## 3줄 요약
- 이번 슬라이스는 `A-1 enter 분리`와 `A-2 canonical snapshot floor`를 묶는다.
- A-3 UI shell과 A-4 resolve는 일부러 열지 않는다.
- 종료선은 `phase='overrunEvent'` 진입 + canonical field set 저장/복구 + recovery smoke 통과다.

## 한 줄 목표
첫 실제 코드 턴에서 `overrunEvent`를 **보여 주기 전에 먼저 저장 가능한 상태**로 만든다.

| 범주 | 이번 슬라이스에서 한다 | 이번 슬라이스에서 하지 않는다 |
| --- | --- | --- |
| 엔진 | `continueOverrun()` enter wrapper화, fallback snapshot 생성, canonical snapshot field set 고정, normalize helper 연결 | reward 실제 적용, `resolved` guard 최종 wiring, 세그먼트 전진 finalize |
| 앱 | `phase='overrunEvent'`가 죽지 않게 하는 최소 explicit branch, summary/snapshot 전달선 정리 | 중앙 카드 1장 UI 완성, CTA 시각 polish |
| 스타일 | 없음 또는 최소 placeholder 수준 | A-3 shell/tone, badge/header/flavor layout |
| 검증 | enter smoke, snapshot canonicalization smoke, legacy/invalid/missing recovery smoke | full A-3 visual sanity, full A-4 resolve smoke |

### 슬라이스 종료 정의
아래 셋이 동시에 만족되어야 종료로 본다.

1. ending CTA 후 즉시 reward가 적용되지 않고 `phase='overrunEvent'`로 들어간다.
2. `state.overrunEvent` 또는 동등 위치가 `contentKey` 중심 canonical field set을 저장한다.
3. reload/legacy/invalid/missing 상태가 모두 같은 normalize 규칙으로 `none` 또는 유효 snapshot으로 수렴한다.

---

## 3. 파일별 패치 경계

## 한 줄 목표
`이번 턴에 무엇을 같이 커밋하고, 무엇을 일부러 다음 턴으로 미루는지`를 파일 단위로 고정한다.

| 파일 | 이번 슬라이스 필수 작업 | 메모 |
| --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `continueOverrun()` enter 전용화, fallback snapshot 생성, canonical snapshot builder/normalize helper 추가 | 이번 슬라이스의 중심 파일 |
| `dice-spell-standalone/src/app.js` | `renderCenter(summary)`에서 `phase='overrunEvent'` explicit branch 추가, snapshot 전달선 유지 | visual polish 금지, placeholder 허용 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | enter smoke + canonical field set smoke + recovery smoke 추가 | old immediate baseline은 완전 삭제보다 단계적 대체 우선 |
| `dice-spell-standalone/src/styles.css` | 원칙적으로 미수정 | placeholder가 필요해도 A-3 전용 class 추가는 보류 |

### 권장 커밋 묶음
- **한 커밋**: `game-engine.js` + `app.js` 최소 branch + `smoke-test.mjs`
- **같이 묶는 이유**: enter 분리와 canonical snapshot은 smoke 없이는 반쪽 상태가 되기 때문
- **같이 묶지 않는 이유**: styles/UI polish를 얹는 순간 A-3 범위가 섞인다

---

## 4. canonical snapshot floor

## 3줄 요약
- 이 슬라이스의 가장 중요한 산출물은 UI가 아니라 `canonical snapshot floor`다.
- 이후 UI/아트/QA는 이 필드 세트를 기준으로만 움직인다.
- render가 branch를 재판정하면 이 슬라이스는 실패다.

## 한 줄 목표
`overrunEvent`가 save/load와 owner handoff를 통과할 수 있는 **최소 canonical payload**를 먼저 고정한다.

### 필수 필드
아래 필드는 이번 슬라이스 안에서 엔진이 직접 만들어 저장/복구해야 한다.

| 필드 | 의미 | owner 메모 |
| --- | --- | --- |
| `contentKey` | exact branch identity (`theme.slime`, `enemy.golem`, `none` 등) | UI/QA가 가장 먼저 믿는 값 |
| `badgeLabel` | 카드 상단 배지 텍스트 | render가 다시 추론하지 않음 |
| `accentKey` | color/accent 분기 키 | UI/아트 token 연결 anchor |
| `artTokenSet` | art owner용 최소 자산 키 | 이번 슬라이스에서는 값만 고정 |
| `headerTitle` | 장면 헤더 문구 | content deck source-of-truth 사용 |
| `flavorText` | 장면 flavor 문구 | ending이 아니라 overrunEvent 소유 |
| `confirmLabel` | confirm CTA 문구 | A-4 wiring 전에도 payload에 존재 |
| `resolved` | 이미 적용됐는지 여부 | 이번 슬라이스에서는 기본 false, recovery 포함 |

### branch 결정 원칙
- `contentKey`는 title 문자열에서 재파생하지 않는다.
- invalid `rewardId`, missing draft, legacy thin state는 모두 normalize 단계에서 `none` 또는 유효 canonical snapshot으로 수렴한다.
- `none`은 fallback이지만 **정상 branch**다. 비어 있거나 placeholder처럼 취급하지 않는다.

### UI/아트에 넘길 수 있는 최소 기준
이번 슬라이스가 끝나면 UI/아트는 아래만 믿고 다음 슬라이스 준비를 시작할 수 있다.

1. `contentKey`
2. `badgeLabel`
3. `accentKey`
4. `artTokenSet`
5. `headerTitle`
6. `flavorText`
7. `confirmLabel`

즉 A-3부터는 `reward.title`이나 `sourceType`를 다시 만지지 않고, 이 payload를 display-only로 소비하는 쪽으로만 열어야 한다.

---

## 5. smoke 종료 증거

## 한 줄 목표
QA와 프로그래머가 같은 슬라이스 종료선을 보게 만든다.

| smoke id | 무엇을 증명하나 | 이번 슬라이스에서 pass여야 하는 이유 |
| --- | --- | --- |
| S-A1-ENTER | ending CTA 후 즉시 apply 없이 `phase='overrunEvent'` 진입 | A-1이 실제로 분리됐는지 확인 |
| S-A1-NONE | 선택 없음/invalid draft에서도 enter 가능 | 런이 죽지 않는지 확인 |
| S-A2-CANONICAL | canonical field set이 snapshot에 모두 존재 | UI 역추론 방지 |
| S-A2-LEGACY | legacy thin state load 시 normalize 수렴 | 세이브 호환 |
| S-A2-MISSING | 누락 state 복구 시 `none` 또는 안전 snapshot 수렴 | recovery baseline |
| S-A2-INVALID | invalid `rewardId`/branch 불일치가 `none`으로 안전 강등 | drift 방지 |

### 종료 판단 규칙
- 위 smoke 중 하나라도 빠지면 이번 슬라이스는 `completed`가 아니라 `half-cutover`다.
- visual polish가 없어도 위 smoke가 닫히면 이 슬라이스는 성공으로 본다.
- 반대로 카드 UI placeholder가 예쁘게 보여도 위 smoke가 없으면 성공으로 보지 않는다.

---

## 6. recovery 기준

## 한 줄 목표
A-3/A-4 전에 save/load와 예외 상태를 먼저 안정시킨다.

### 반드시 복구되어야 하는 상태
1. `selectedRewardId`만 있고 canonical snapshot이 없는 legacy state
2. `rewardId`가 현재 reward pool과 어긋나는 invalid state
3. `overrunEvent` 필드 일부만 남은 partial state
4. `phase='overrunEvent'`인데 snapshot이 비어 있는 missing state

### recovery의 결과
- 가능한 경우: canonical snapshot 재구성
- 불가능한 경우: `none` canonical snapshot 생성
- 금지: exception으로 중단, render default fall-through, ending 화면으로 조용히 복귀

---

## 7. 역할별 handoff 포인트

## 프로그래머에게 넘기는 것
- 이번 슬라이스는 `A-1 enter + A-2 payload floor`만 닫는다.
- `resolveOverrunEvent()`나 동등 resolve 함수 최종 wiring은 다음 슬라이스다.
- `styles.css`와 CTA polish를 욕심내지 않는다.

## UI 오너에게 넘기는 것
- 이번 슬라이스가 닫히면 A-3는 `contentKey` 기반 display-only shell로 들어갈 수 있다.
- 아직 요구하지 않는 것: 최종 shell/tone, reward summary density, footnote hierarchy.
- 지금 확인할 것: `none`도 정상 장면이라는 사실, `accentKey`/`artTokenSet`이 canonical payload에 포함된다는 사실.

## 아트 오너에게 넘기는 것
- 이번 슬라이스는 자산 제작 턴이 아니다.
- 다만 `artTokenSet` freeze가 확인되면 A-3에서 필요한 최소 패키지 범위를 바로 산정할 수 있다.
- ending용 자산을 `overrunEvent`에 선적용하지 않는다.

## QA에게 넘기는 것
- 이번 슬라이스의 pass/fail은 visual 완성도가 아니라 enter/recovery smoke로 본다.
- A-4 resolve smoke를 미리 요구하지 않는다.
- 대신 legacy/invalid/missing recovery가 빠지면 바로 fail로 본다.

---

## 8. 이 슬라이스에서 금지하는 것

1. `continueOverrun()`에서 reward apply와 page progression을 일부라도 유지한 채 enter 분리를 완료로 간주하는 것
2. canonical payload가 덜 닫힌 상태에서 A-3 card UI를 먼저 얹는 것
3. `contentKey` 대신 title 문자열, reward tag, `sourceType`에서 badge/accent/header를 재추론하는 것
4. `styles.css`까지 크게 건드려 A-3 범위를 섞는 것
5. `resolved` wiring까지 욕심내며 A-4를 같이 여는 것
6. onboarding B-track 문서를 근거로 primer 구현을 같이 시작하는 것

---

## 9. 다음 슬라이스로 넘기는 것

| 다음 슬라이스 | 열어도 되는 조건 | 이 문서가 넘겨주는 산출물 |
| --- | --- | --- |
| A-3 중앙 카드 UI | canonical payload와 recovery smoke가 닫혔을 때 | `contentKey`/`badgeLabel`/`accentKey`/`artTokenSet`/`headerTitle`/`flavorText`/`confirmLabel` |
| A-4 confirm resolve | A-3에서 display-only shell이 고정됐을 때 | enter와 payload floor가 분리된 상태, old baseline 잔여 목록 |
| B-track 온보딩 | A-4 resolve까지 실제 코드로 닫혔을 때 | 없음. 이 슬라이스는 B-track 착수 조건만 준비 |

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| A-1/A-2를 다시 분리해 half-cutover가 남는 위험 | UI가 다시 branch를 역추론하게 됨 | 첫 실제 슬라이스를 두 단계 결합형 packet으로 고정 | 다음 writer 구현 턴 시작 전 |
| UI가 payload freeze 전 shell 검토를 시작하는 위험 | badge/accent/header drift | UI/아트는 이번 슬라이스에서 payload floor만 확인 | A-3 착수 직전 |
| recovery를 happy path 뒤로 미루는 위험 | save/load 호환성 붕괴 | legacy/invalid/missing recovery를 종료선에 포함 | A-2 smoke 작성 직후 |
| styles 작업이 섞여 범위가 부푸는 위험 | A-3와 A-1/A-2 경계 붕괴 | `styles.css` 원칙적 미수정 규칙 명시 | 커밋 리뷰 시 |

---

## 11. 즉시 다음 액션

1. 다음 writer 구현 턴은 `docs/dicespell_overrun_cutover_runway_packet_kr.md`와 `docs/dicespell_overrun_runtime_patch_map_kr.md`를 연 뒤, **이번 턴 범위를 `first runtime slice`로 선언**한다.
2. 구현은 `game-engine.js` → `app.js` 최소 branch → `smoke-test.mjs` 순으로 닫고, styles는 건드리지 않는다.
3. tracker와 automation run log에는 `A-1/A-2 first runtime slice`라는 이름으로 같은 종료선을 기록한다.
4. 위 smoke가 모두 닫힌 뒤에만 A-3 UI shell packet으로 넘어간다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 실구현의 첫 실제 코드 턴을 `enter + canonical snapshot + recovery smoke`로 다시 좁혀, 이후 UI/resolve가 안정된 payload 위에서만 열리게 만드는 첫 슬라이스 handoff다.
