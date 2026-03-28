# DiceSpell `overrunEvent` 첫 런타임 상태 전이 매트릭스

## 3줄 요약
- 이 문서는 `first runtime slice`를 실제 코드/QA/리뷰 단위로 옮길 때 가장 쉽게 흔들리는 **상태 전이 기준선**을 한 장으로 고정하는 source handoff다.
- 핵심은 `continueOverrun()` 직후 무엇이 **즉시 바뀌면 안 되는지**, 무엇이 **반드시 canonical snapshot으로 생겨야 하는지**, 어떤 예외가 `none`으로 안전 수렴해야 하는지를 before/after 표로 못 박는 것이다.
- 프로그래머는 state diff와 helper 책임을 먼저 보고, UI/아트는 언제부터 payload를 믿어도 되는지, QA는 smoke별 기대 상태를 먼저 보면 된다.

## 한 줄 목표
`A-1 enter 분리 + A-2 canonical snapshot floor`를 구현자가 감으로 해석하지 않도록, **첫 슬라이스의 상태 전이와 비변경선**을 exact matrix로 고정한다.

## 왜 이 문서가 필요한가
지금 DiceSpell에는 이미 아래 문서가 있다.

- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`
- `docs/dicespell_overrun_a2_snapshot_packet_kr.md`
- `docs/dicespell_overrun_onboarding_contract_examples_kr.md`

문제는 구현 직전에도 아래 공백이 남는다는 점이다.

1. `current live chain`은 설명돼 있지만, **첫 슬라이스 직후 state가 정확히 어떤 diff를 가져야 하는지**는 문서 여러 장에 나뉘어 있다.
2. 프로그래머는 `phase만 바꾸고 나머지는 나중에` 같은 half-cutover를 만들기 쉽고, QA는 그 상태가 pass인지 fail인지 흔들릴 수 있다.
3. UI/아트는 `contentKey`/`accentKey`/`artTokenSet`이 언제 freeze되는지 모르면 너무 이르게 shell 검토에 들어가 drift를 키운다.
4. legacy/missing/invalid 복구가 happy path 부록처럼 밀리면, save/load 기준선이 다시 구현자 기억에 의존하게 된다.

즉 지금 필요한 것은 새 아이디어가 아니라, **첫 슬라이스가 끝났을 때 state가 무엇을 하고 무엇을 아직 하지 않아야 하는지**를 한 장으로 못 박는 일이다.

---

## 누가 어디부터 읽어야 하나

| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | `1. 상태 전이 핵심 원칙`, `2. before → after 매트릭스`, `3. helper 책임 분리` | `dicespell_overrun_first_runtime_slice_packet_kr.md`, `dicespell_overrun_runtime_patch_map_kr.md` |
| QA | `2. before → after 매트릭스`, `4. smoke별 기대 상태`, `6. 실패 판정선` | `dicespell_overrun_event_acceptance_matrix_kr.md` |
| UI | `2. before → after 매트릭스`, `5. UI/아트 handoff freeze 기준` | `dicespell_overrun_a3_ui_packet_kr.md`, `dicespell_overrun_event_content_deck_kr.md` |
| 아트 | `5. UI/아트 handoff freeze 기준`, `7. 다음 슬라이스로 넘기는 것` | `dicespell_overrun_event_screen_packet_kr.md` |

---

## 1. 상태 전이 핵심 원칙

### 3줄 요약
- 첫 슬라이스는 `새 phase를 보여 주는 것`보다 먼저 `save/load 가능한 canonical state`를 만드는 단계다.
- 따라서 `continueOverrun()` 직후에는 **바뀌어야 하는 값**과 **절대 바뀌면 안 되는 값**이 동시에 존재한다.
- 이 문서는 그 둘을 섞지 않게 하는 기준선이다.

**한 줄 목표:** `enter 성공`과 `resolve 미실행`을 동시에 만족하는 중간 상태를 정확히 정의한다.

### 이번 슬라이스에서 반드시 참이어야 하는 것
1. `phase === 'overrunEvent'`
2. `state.overrunEvent` 또는 동등 위치에 canonical snapshot이 존재
3. snapshot은 `contentKey` 중심 field set을 가짐
4. invalid/missing/legacy 상태는 `none` 또는 유효 canonical snapshot으로 normalize 가능

### 이번 슬라이스에서 아직 참이면 안 되는 것
1. reward effect bundle 적용 완료
2. `currentPage` 증가
3. `pagePlan` 확장 반영
4. `segmentTrophyTemplateIds` 초기화
5. 다음 세그먼트 자동 진입
6. `resolved === true`

### 해석 규칙
- `phase='overrunEvent'`가 됐다고 해서 resolve가 된 것이 아니다.
- `contentKey`가 생겼다고 해서 UI shell이 완성된 것도 아니다.
- 첫 슬라이스의 성공 기준은 **중간 상태를 안전하게 고정했는가**다.

---

## 2. before → after 상태 전이 매트릭스

### 3줄 요약
- 아래 표는 `ending`에서 `continue-overrun`을 누른 직후, 상태가 어디까지 변해야 하는지를 exact diff로 고정한다.
- 핵심은 `phase`와 `overrunEvent snapshot`은 바뀌되, reward 적용/페이지 전진/세그먼트 초기화는 아직 그대로여야 한다는 점이다.
- 표의 `A-1/A-2 후 기대값`이 구현/QA/review 공통 기준선이다.

**한 줄 목표:** 첫 슬라이스 직후 상태를 `보여 주기 전의 저장 가능 상태`로 구체화한다.

| 항목 | 버튼 누르기 직전 (`ending`) | A-1/A-2 후 기대값 | A-4 전까지 금지되는 변화 |
| --- | --- | --- | --- |
| `phase` | `ending` | `overrunEvent` | `library`/`battle`로 즉시 넘어가기 |
| `ending.overrunDraft.selectedRewardId` | 선택됨 또는 비어 있음 | 보존 가능. 단 canonical snapshot 생성의 입력값으로만 사용 | 여기서 effect apply 판정 끝내기 |
| `state.overrunEvent` | 없음 또는 legacy thin state | canonical snapshot 존재 | 비어 있음, partial state 방치 |
| `overrunEvent.resolved` | 없음 또는 legacy 값 | `false`로 수렴 | `true` 선적용 |
| `overrunEvent.contentKey` | 없음 | exact branch identity 저장 | title/sourceType에서 재추론 |
| `overrunEvent.badgeLabel` | 없음 | canonical text 저장 | render 시점 재생성 |
| `overrunEvent.accentKey` | 없음 | canonical tone key 저장 | CSS class나 reward tag에서 역산 |
| `overrunEvent.artTokenSet` | 없음 | canonical art token set 저장 | UI/아트가 따로 재분기 |
| `overrunEvent.headerTitle` | 없음 | content deck 기준 exact copy 저장 | ending 본문을 재사용 |
| `overrunEvent.flavorText` | 없음 | overrunEvent 소유 flavor 저장 | ending/summary에서 대신 소비 |
| `overrunEvent.confirmLabel` | 없음 | CTA 의미용 copy 저장 | A-4 전 버튼 동작 완료로 간주 |
| reward effect 결과 | 아직 미적용 | 여전히 미적용 | applyEffectBundle 즉시 실행 |
| `currentPage` | 엔딩 직전 값 | 그대로 유지 | +1 증가 |
| `pagePlan.length` | 엔딩 직전 값 | 그대로 유지 | 새 세그먼트 push |
| `segmentTrophyTemplateIds` | 이번 세그먼트 기록 유지 | 그대로 유지 | 빈 배열 초기화 |
| `reward`/`shop`/`battle` surface | ending 정산 이후 상태 | `overrunEvent` 표시에 필요한 최소 상태만 유지 | 새 전투 진입까지 완료 |

### 가장 중요한 해석
- 첫 슬라이스는 `phase`와 `snapshot`만 새긴다.
- progression 관련 값이 같이 움직였다면 `half-cutover`가 아니라 **old baseline 잔존**이다.
- snapshot이 partial이면 `enter 성공`처럼 보여도 QA 기준으론 실패다.

---

## 3. helper 책임 분리

### 한 줄 목표
`누가 canonical state를 만들고, 누가 그것을 소비만 해야 하는지`를 함수 단위로 고정한다.

| 책임 | 가져야 하는 함수/층 | 하면 안 되는 일 |
| --- | --- | --- |
| ending 선택 저장 | `chooseEndingOverrunReward()` 또는 동등 선택 함수 | canonical snapshot 생성, effect apply |
| enter 전이 | `continueOverrun()` 또는 동등 enter wrapper | page progression, reward apply finalize |
| canonical 생성 | `buildCanonicalOverrunEventSnapshot()` / `buildNoneOverrunEventSnapshot()` 계열 helper | render copy 생성에 의존 |
| 복구/정규화 | `normalizeOverrunEventState()` 계열 helper | render에서 조용히 보정 |
| 표시 | `renderCenter(summary)` → 향후 `renderOverrunEvent(summary)` | `contentKey`/`accentKey`/`badgeLabel` 재판정 |
| 적용/전진 | A-4의 `resolveOverrunEvent(state)` 또는 동등 함수 | A-1/A-2에 섞여 들어오기 |

### 프로그래머 handoff 메모
- `continueOverrun()`가 canonical snapshot 생성까지만 닫혀도 괜찮다.
- `continueOverrun()`가 apply/progression까지 계속 들고 있으면 안 된다.
- render 단계 보정은 복구가 아니라 drift 시작점으로 본다.

---

## 4. smoke별 기대 상태

### 3줄 요약
- smoke는 `무슨 함수가 호출됐는가`보다 `그 직후 state가 무엇을 아직 하지 않았는가`를 증명해야 한다.
- 아래 표는 첫 슬라이스에서 필요한 smoke가 각각 어떤 상태를 남겨야 하는지 고정한다.
- QA는 이 표를 pass/fail 기준으로, 프로그래머는 fixture 설계 기준으로 쓴다.

**한 줄 목표:** enter/recovery smoke를 state expectation 단위로 맞춘다.

| smoke id | 입력 상태 | pass일 때 남아야 하는 상태 | fail 신호 |
| --- | --- | --- | --- |
| `S-A1-ENTER` | 유효 selected reward + ending 상태 | `phase='overrunEvent'`, reward 미적용, canonical snapshot 존재 | `battle/library` 진입, reward 즉시 적용 |
| `S-A1-NONE` | 선택 없음 또는 skip | `contentKey='fallback.none'` 또는 동등 neutral branch, CTA copy 존재 | 빈 snapshot, crash, silent return |
| `S-A2-CANONICAL` | 유효 theme/enemy reward | `contentKey`/`badgeLabel`/`accentKey`/`artTokenSet`/`headerTitle`/`flavorText`/`confirmLabel`/`resolved=false` 모두 존재 | 일부 필드 누락, sourceType만 존재 |
| `S-A2-LEGACY` | `selectedRewardId`만 있는 thin state | canonical snapshot으로 수렴 | ending으로 조용히 복귀, partial state 유지 |
| `S-A2-MISSING` | `phase='overrunEvent'`인데 snapshot 누락 | `fallback.none` 또는 안전 canonical snapshot 생성 | exception, default library fall-through |
| `S-A2-INVALID` | invalid rewardId / branch mismatch | `none` branch로 안전 강등, render 가능한 payload 유지 | broken label, undefined copy, crash |

### QA 메모
- 문자열 한 줄보다 먼저 `phase`, `resolved`, `contentKey`, progression 미실행 여부를 본다.
- `none`이 예쁘지 않아도 pass가 아니라, **정상 장면 payload인지**가 우선이다.

---

## 5. UI/아트 handoff freeze 기준

### 한 줄 목표
UI/아트가 payload를 믿어도 되는 시점을 `필드 존재` 기준으로 고정한다.

### UI가 A-3 준비를 시작해도 되는 조건
아래 8개 필드가 engine snapshot에 직접 존재하고, recovery smoke까지 통과했을 때만 A-3 shell 검토를 시작한다.

1. `contentKey`
2. `badgeLabel`
3. `accentKey`
4. `artTokenSet`
5. `headerTitle`
6. `flavorText`
7. `confirmLabel`
8. `resolved`

### UI에 넘길 것
- `none`도 동일한 정보 위계를 가진 정상 branch
- heading/flavor/CTA는 display-only 소비 대상
- `reward.title`, `reward.description`, `sourceType`는 보조 정보일 뿐 branch 판정 기준이 아님

### 아트에 넘길 것
- `artTokenSet`이 freeze되면 최소 asset package 범위를 산정할 수 있다.
- 첫 슬라이스에서는 새 shell 제작이 아니라 token 안정화만 확인한다.
- neutral `none`이 오류/빈 상태처럼 보이지 않을 기준도 `accentKey`/`artTokenSet` 존재로 먼저 담보한다.

---

## 6. 실패 판정선

### 한 줄 목표
리뷰 때 `겉으로는 돌아가지만 실제론 실패`인 상태를 빠르게 잡는다.

1. `phase='overrunEvent'`지만 `state.overrunEvent`가 partial이면 실패
2. canonical field가 없이 `sourceType`/`rewardTitle`만 맞으면 실패
3. reward effect가 이미 적용됐으면 실패
4. `currentPage`, `pagePlan`, `segmentTrophyTemplateIds` 중 하나라도 움직였으면 실패
5. invalid/missing/legacy 중 하나라도 render fallback에만 의존하면 실패
6. `none` branch가 빈 payload거나 CTA가 없으면 실패
7. UI가 title/sourceType로 tone을 재판정하면 실패

---

## 7. 다음 슬라이스로 넘기는 것

### 3줄 요약
- 이 문서가 닫아 주는 것은 A-3/A-4의 완성이 아니라, 그들이 얹힐 **상태 바닥**이다.
- 따라서 다음 슬라이스는 이 matrix를 기준으로 `무엇을 새로 바꾸는지`보다 `무엇은 이미 바꾸지 않기로 고정됐는지`부터 읽어야 한다.
- 이 구분이 무너지면 A-3 UI와 A-4 resolve가 다시 A-1/A-2까지 먹어치운다.

**한 줄 목표:** 다음 슬라이스가 state floor 위에서만 열리게 만든다.

| 다음 슬라이스 | 이 문서가 넘겨주는 것 | 다음에 새로 열리는 것 |
| --- | --- | --- |
| A-3 UI | canonical payload freeze, `none` 정상 branch 기준, progression 미실행 상태 | 중앙 카드 1장 shell, badge→header→flavor→reward summary→CTA→footnote 위계 |
| A-4 resolve | enter와 snapshot이 이미 분리된 상태, reward 미적용/페이지 미전진 상태 | 1회 apply, page progression, `resolved` guard, old baseline 제거 |
| B-track 온보딩 | 없음. 착수 조건만 명확해짐 | A-4 이후에만 library → battle → reward/shop → ending |

---

## 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 후속 확인 |
| --- | --- | --- | --- |
| phase만 바꾸고 progression도 그대로 두는 half-cutover | old baseline과 new phase가 섞임 | before/after matrix에 비변경선 명시 | 첫 구현 PR 리뷰 시 |
| canonical snapshot이 partial로 저장되는 위험 | UI/QA가 다시 역추론 | 필수 field set과 smoke pass 상태를 함께 고정 | A-2 smoke 작성 직후 |
| legacy/missing/invalid recovery를 후순위로 미루는 위험 | save/load 호환 붕괴 | recovery 케이스를 happy path와 같은 표에 포함 | recovery fixture 추가 시 |
| UI/아트가 too-early shell 검토를 시작하는 위험 | accent/badge/header drift | freeze 조건 8개를 먼저 만족해야 한다고 명시 | A-3 착수 직전 |

---

## 즉시 다음 액션
1. 다음 writer 구현 턴은 `runway → runtime patch map → first runtime slice packet → 이 state matrix` 순서로 열고, 상태 전이 기준선을 먼저 선언한다.
2. 구현 리뷰에서는 `무엇이 바뀌었는가`만 보지 말고 `무엇이 아직 안 바뀌었는가`를 이 표와 대조한다.
3. tracker와 run log에는 이번 문서를 `첫 슬라이스 exact state diff 기준`으로 기록해, 다음 구현자가 문서 여러 장을 다시 짜맞추지 않게 한다.

---

## 한 줄 결론
이 문서는 `overrunEvent` 첫 런타임 슬라이스를 `phase 전환 + canonical snapshot 생성 + recovery 수렴`이라는 **정확한 상태 전이 계약**으로 다시 고정하는 source handoff다.
