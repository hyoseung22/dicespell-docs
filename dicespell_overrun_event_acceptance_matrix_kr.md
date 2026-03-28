# DiceSpell `overrunEvent` acceptance / recovery matrix

## 빠른 안내

### 3줄 요약
- 이 문서는 `overrunEvent`가 **언제 pass인지, 저장/분기 누락 시 어디까지 복구해야 하는지**를 판정하는 검수표다.
- QA는 acceptance/recovery를 먼저, 프로그래머는 fallback·resolved guard를 먼저, PM은 owner별 증거 열을 먼저 보면 된다.
- `보여 준다`보다 `정확히 한 번 적용되고 reload 뒤에도 drift하지 않는다`를 기준으로 읽는다.

**한 줄 목표:** `overrunEvent` 구현의 완료선과 recovery 기준을 한 장으로 고정한다.

### 왜 이 문서가 필요한가
- overrunEvent는 phase 추가라서 half-cutover가 가장 위험하다.
- 이 문서는 그 half-cutover를 막기 위해 화면/상태/복구 기준을 같은 표로 묶는다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| QA | acceptance / recovery 전체 | `screen packet`, `smoke migration packet` |
| 프로그래머 | resolved / invalid / none fallback | `implementation packet` |
| UI | 필수 시각 요소와 금지 상태 | `screen packet` |
| PM | owner별 증거와 완료선 | `summary` |

---

## 문서 목적

이 문서는 이미 작성된 아래 4개 문서를 **실제 구현 직전 QA/프로그래머/UI/아트 공통 검수 기준**으로 다시 묶는 최종 검증 패킷이다.

- `docs/dicespell_overrun_event_handoff_kr.md`
- `docs/dicespell_overrun_event_implementation_packet_kr.md`
- `docs/dicespell_overrun_event_screen_packet_kr.md`
- `docs/dicespell_overrun_event_production_cutsheet_kr.md`

지금까지의 문서들은 방향, patch surface, 화면 구조, 역할별 DoD를 충분히 고정했다.
하지만 실제 구현 직전 기준으로는 아직 마지막 병목이 하나 남아 있다.

> `overrunEvent`가 붙은 뒤 무엇이 "정말 닫힌 상태"인지, 특히 save/load/fallback/카피/시각 구분까지 포함해 누가 어떤 증거를 넘기면 되는지가 한 장으로 정리돼 있지 않다.

이번 문서는 그 빈칸만 메운다.

즉 이 문서는 새 기획서가 아니라, **구현 직전의 acceptance matrix + recovery matrix + owner handoff evidence sheet**다.

---

## 1. 왜 지금 이 문서가 필요한가

현재 남은 가장 큰 리스크는 아이디어 부족이 아니라 **복구 경계와 완료선의 어긋남**이다.

이미 고정된 것은 많다.

- `ending -> overrunEvent -> 다음 세그먼트` phase 방향
- `continueOverrun()` enter화 원칙
- 중앙 카드 1장 UI
- `themeTrophy / enemyTrophy / none` 3상태 구분
- CTA 시점 보상 적용 원칙
- production cutsheet의 acceptance 12종

하지만 실제 제작 현장에서는 아래가 여전히 쉽게 흔들린다.

1. 프로그래머는 엔진 상태 전이를 닫았는데, UI는 `none` fallback을 화면상 임시 placeholder로 처리할 수 있다.
2. UI는 화면을 다 만들었는데, save/load 복구 후 문자열/배지/CTA가 기본값으로 무너질 수 있다.
3. QA는 smoke를 통과했는데, `invalid rewardId`, `resolved=true`, `snapshot 누락` 같은 세이브 경계가 빠질 수 있다.
4. 아트는 필요한 자산을 다 넘겼는데, 실제 CSS slot/class 매핑이 문서 밖에서 암묵적으로 처리될 수 있다.

즉 지금 필요한 것은 또 다른 방향 문서가 아니라, **누가 무엇을 어떤 시나리오 증거로 넘기면 이 bounded phase가 닫히는지**를 명시한 검수 문서다.

---

## 2. 이번 bounded 제작의 최종 정의

이번 슬롯 이후 `overrunEvent` 관련 문서 묶음은 아래 한 문장으로 구현팀에 넘길 수 있어야 한다.

> 책 클리어 엔딩에서 고른 전리품 1개를, `overrunEvent` 중앙 카드 1장 화면으로 보여 주고, CTA 시점에만 실제 적용한 뒤 다음 세그먼트 첫 페이지로 넘긴다. 선택이 없거나 save가 일부 깨져도 런은 항상 `none` fallback으로 살아남는다.

이 문장을 깨는 것은 전부 이번 범위 밖이다.

---

## 3. owner별 최종 납품 증거

### 3.1 프로그래머가 넘겨야 하는 증거

프로그래머는 코드 자체보다 아래 증거를 넘겨야 완료로 본다.

1. `continue-overrun` 입력이 더 이상 즉시 resolve가 아니라 `phase='overrunEvent'` 진입임을 보여 주는 재현 가능 시나리오 1개
2. `confirm-overrun-event` 전에는 bundle 효과가 적용되지 않음을 보여 주는 상태 비교 1개
3. `confirm-overrun-event` 후 bundle 효과가 정확히 1회만 적용됨을 보여 주는 상태 비교 1개
4. `invalid rewardId` / `missing snapshot` / `resolved=true` save 복구 규칙을 보여 주는 smoke 또는 fixture 3개

### 3.2 UI 오너가 넘겨야 하는 증거

1. `themeTrophy`, `enemyTrophy`, `none` 3상태 렌더 캡처 또는 DOM snapshot
2. `ending reward grid`와 `overrunEvent`가 다른 계층감으로 읽히는 비교 화면 1개
3. `none` 상태도 placeholder가 아니라 완성된 경계 화면으로 읽히는 상태 캡처 1개

### 3.3 아트 오너가 넘겨야 하는 증거

1. 헤더 패널 1종의 실제 slot/class 적용 결과 1개
2. 배지 3종이 상태별로 다른 shape/color/signifier를 가지는 결과 1개
3. backplate/accent가 `theme/enemy/none` 상태를 코드 임시색 없이도 구분시키는 결과 1개

### 3.4 QA가 넘겨야 하는 증거

1. acceptance 12종 pass/fail 체크표
2. recovery 6종 pass/fail 체크표
3. `none fallback`이 런 생존 전략으로 실제 유효함을 보여 주는 짧은 코멘트 1개

---

## 4. acceptance matrix

아래 12개는 production cutsheet의 acceptance를 구현/검수용 표로 다시 고정한 것이다.

| ID | 시나리오 | 입력/준비 상태 | 기대 결과 | 주요 owner |
| --- | --- | --- | --- | --- |
| A1 | 선택된 책 전리품으로 진입 | 승리 엔딩, `canOverrun=true`, `selectedRewardId`가 책 전리품 | `continue-overrun` 후 `phase='overrunEvent'`, 헤더/배지/CTA가 `themeTrophy` 규칙으로 렌더 | 프로그래머, UI |
| A2 | 선택된 적 전리품으로 진입 | 승리 엔딩, `selectedRewardId`가 적 전리품 | `enemyTrophy` 규칙으로 렌더, 적 잔해 문맥 카피 적용 | 프로그래머, UI |
| A3 | 선택 없이 진입 | 승리 엔딩, `selectedRewardId=null` | `none` snapshot 생성, 카드/배지/CTA/footnote 모두 존재 | 프로그래머, UI |
| A4 | CTA 전 미적용 | A1 또는 A2 상태에서 `overrunEvent` 진입 직후 | 보상 수치/bundle 효과가 아직 상태에 반영되지 않음 | 프로그래머 |
| A5 | CTA 후 1회 적용 | A1 또는 A2에서 `confirm-overrun-event` | 선택한 전리품 bundle이 정확히 1회 적용 | 프로그래머, QA |
| A6 | 세그먼트 증가 | A5 완료 | pagePlan/overrunLevel/다음 세그먼트 준비 상태가 정상 증가 | 프로그래머, QA |
| A7 | 전리품 이력 정리 | A5 완료 | `segmentTrophyTemplateIds`가 초기화 | 프로그래머, QA |
| A8 | 재진입 방지 | A5 완료 후 다음 페이지 진행 | 같은 세그먼트에서 `overrunEvent`가 다시 열리지 않음 | 프로그래머, QA |
| A9 | 시각 구분성 | A1/A2/A3 비교 | 세 상태가 배지/헤더/색감으로 즉시 구분 | UI, 아트, QA |
| A10 | `none` 화면 완성도 | A3 상태 | 빈손 상태가 placeholder가 아니라 완성된 경계 화면처럼 보임 | UI, 아트 |
| A11 | 저장 후 복구 | `phase='overrunEvent'` 상태 저장 후 로드 | 동일 상태/카피/배지/CTA 재구성 가능 | 프로그래머, QA |
| A12 | invalid reward 강등 | `rewardId` lookup 실패 save | 런은 죽지 않고 `none` fallback으로 강등 | 프로그래머, QA |

---

## 5. recovery matrix

이 문서는 acceptance뿐 아니라 **복구 전략이 실제로 구현 완료선의 일부**라는 점을 고정한다.

### 5.1 recovery 케이스 6종

| ID | 깨진 상태 | 복구 규칙 | 기대 결과 | owner |
| --- | --- | --- | --- | --- |
| R1 | `phase='overrunEvent'`인데 `overrunEvent` 없음 | `ending.overrunDraft` 기반 snapshot 재생성, 실패 시 `none` | 런 생존, 렌더 가능 | 프로그래머 |
| R2 | `rewardId`는 있는데 option lookup 실패 | 무조건 `none` 강등 | 진행 가능, 카피/배지 정상 | 프로그래머, QA |
| R3 | `sourceType` 값이 이상함 | 유효값 아니면 `none` 강등 | 스타일/카피 붕괴 없이 렌더 | 프로그래머, UI |
| R4 | `resolved=true`인데 여전히 `phase='overrunEvent'` | resolve 재실행 금지, 안전한 다음 페이지 복귀 또는 재구성 | 중복 적용 없음 | 프로그래머, QA |
| R5 | `confirmLabel/header/flavorText` 누락 | snapshot 재생성 또는 sourceType 기본 카피 주입 | 기본 카피라도 일관성 유지 | 프로그래머, UI |
| R6 | `none` 상태에서 reward card payload 일부 누락 | placeholder title/description/tag 재주입 | 화면 밀도 유지 | 프로그래머, UI |

### 5.2 recovery 우선순위 원칙

복구는 아래 우선순위를 따른다.

1. 런이 죽지 않을 것
2. 중복 적용이 없을 것
3. 화면이 최소 밀도로라도 정상 렌더될 것
4. 카피/아트/세부 표현은 그 다음

즉 recovery는 `완벽한 원형 복원`보다 **런 생존 + 1회성 상태 전이 보존**이 우선이다.

---

## 6. 화면 상태 매트릭스

### 6.1 state별 필수 시각 요소

| 상태 | eyebrow | header | flavor | badge | reward card | CTA | footnote | accent |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `themeTrophy` | 필수 | 필수 | 필수 | `책 전리품` | 실제 선택 카드 | `이 전리품을 챙기고 다음 권으로` | 필수 | 책 테마 accent |
| `enemyTrophy` | 필수 | 필수 | 필수 | `적 전리품` | 실제 선택 카드 | `이 잔해를 들고 다음 권으로` | 필수 | 적 잔해 + 책 테마 보조 |
| `none` | 필수 | 필수 | 필수 | `빈손` | placeholder 카드 | `가볍게 다음 권을 연다` | 필수 | neutral accent |

### 6.2 화면이 실패한 것으로 간주하는 경우

아래 중 하나면 화면 handoff는 실패로 본다.

- `none` 상태에서 카드가 아예 사라짐
- 배지가 텍스트 없이 색만 다름
- `themeTrophy`와 `enemyTrophy`가 헤더/CTA에서 같은 문구를 공유해 구분감이 약함
- CTA가 2개 이상 존재함
- `ending`의 reward grid와 시각적으로 거의 같은 구조로 보여 `중간 장면`으로 읽히지 않음

---

## 7. 파일/slot handoff 표

| 영역 | 파일 | 필수 접점 | 비고 |
| --- | --- | --- | --- |
| 엔진 진입/복구 | `src/game-engine.js` | `continueOverrun`, snapshot/create/resolve/normalize | 중복 적용 방지 핵심 |
| 렌더 분기 | `src/app.js` | `renderCenter`, `renderOverrunEvent`, confirm action | DOM payload 소비부 |
| 시각 스타일 | `src/styles.css` | `overrun-event-*`, `overrun-source-badge.*` | 기존 hero/reward 계열 재사용 우선 |
| 검증 | `scripts/smoke-test.mjs` | acceptance/recovery fixture | 12+6 축 고정 |
| UI 아트 slot | CSS class / asset mapping | header panel, badges, backplate, accent token | 임시색 대체 가능 |

---

## 8. 구현 순서별 완료선

### Step 1. 엔진 완료선

아래가 되면 Step 1 완료다.

- `continue-overrun`이 `overrunEvent` 진입으로 바뀜
- snapshot이 `theme/enemy/none` 중 하나로 생성됨
- confirm 전 미적용 / confirm 후 1회 적용이 상태 diff로 증명됨

### Step 2. 최소 UI 완료선

- `renderCenter(summary)`가 `overrunEvent`를 독립 화면으로 그림
- 카드 1장 / CTA 1개 / footnote 1줄 구조가 보임
- `none` 상태에서도 같은 구조 유지

### Step 3. recovery 완료선

- R1~R6이 fixture 또는 smoke로 재현 가능
- invalid/누락/재로드 케이스에서 런이 죽지 않음

### Step 4. 시각 polish 완료선

- 헤더 패널/배지/backplate/accent가 적용됨
- 상태 구분성이 screenshot 기준으로 분명함

### Step 5. 최종 acceptance 완료선

- A1~A12, R1~R6 모두 pass
- ending 카피의 `즉시 적용` 문맥이 제거됨
- tracker/run log/blueprint가 같은 완료선을 가리킴

---

## 9. 프로그래머 / UI / 아트 오너별 실제 handoff 문장

### 프로그래머에게 넘길 것

- `continueOverrun()`를 enter wrapper로 축소
- `overrunEvent` snapshot/create/resolve/normalize 구현
- `theme/enemy/none` 3상태와 recovery 6종 smoke 고정
- `confirm-overrun-event` 전 미적용 / 후 1회 적용을 증거로 남길 것

### UI 오너에게 넘길 것

- 중앙 카드 1장, CTA 1개, footnote 1줄 구조 고정
- `문맥 -> 대상 -> 행동` 순서를 깨지 말 것
- `none`도 동일 밀도 화면으로 다룰 것
- `ending reward grid`와 다른 계층감을 만들 것

### 아트 오너에게 넘길 것

- 헤더 패널 1종
- 배지 3종
- backplate 1세트
- accent 4종
- sourceType 구분이 텍스트 없이도 읽히도록 shape/color signifier를 줄 것

---

## 10. 리스크 & 후속 과제

### 리스크 1. acceptance 12종만 보고 recovery가 약하게 처리될 위험

- 영향: 겉보기 구현은 끝났는데 save/load에서 런이 죽을 수 있다.
- 대응: 이번 문서에서 recovery 6종을 acceptance와 동급 완료선으로 명시한다.

### 리스크 2. `none` 상태가 여전히 임시 화면으로 밀릴 위험

- 영향: skip fallback이 설계상 존재하더라도 실제 체감은 허술해진다.
- 대응: 카드/배지/CTA/footnote/neutral accent를 모두 필수로 고정한다.

### 리스크 3. 아트 slot과 CSS class가 암묵적으로 연결될 위험

- 영향: 자산은 준비됐는데 실제 적용 과정에서 다시 해석 차이가 생긴다.
- 대응: 구현 착수 시 class/slot 이름을 먼저 고정하고 아트는 그 slot에만 맞춘다.

### 리스크 4. 문서가 충분하다는 이유로 실제 구현 슬롯이 계속 뒤로 밀릴 위험

- 영향: handoff-ready 문서가 쌓이기만 하고 phase가 영영 닫히지 않을 수 있다.
- 대응: 이번 문서의 목표는 문서 확장이 아니라 구현 착수 전 마지막 검수선 고정임을 tracker에 명시한다. 다음 슬롯은 이 5종 문서 기준 실제 구현이 우선이다.

### 후속 과제

이 문서가 붙은 뒤 다음 최우선 작업은 더 이상의 오버런 문서 추가가 아니다.

1. `overrunEvent` 실제 엔진/UI 구현
2. acceptance 12종 + recovery 6종 smoke 고정
3. 문서와 구현이 일치하는지 교차 확인
4. 그 다음에만 `보스형 골렘`, 오버런 바리에이션, 추가 이벤트 확장 검토

---

## 11. 한 줄 결론

`overrunEvent`에 남아 있던 마지막 빈칸은 방향이 아니라 **복구와 완료선**이었다. 이 문서는 프로그래머/UI/아트/QA가 같은 종료 조건을 보게 만드는 최종 acceptance / recovery matrix다.
