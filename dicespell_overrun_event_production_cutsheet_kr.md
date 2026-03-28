# DiceSpell `overrunEvent` production cutsheet

## 빠른 안내

### 3줄 요약
- 이 문서는 `overrunEvent` 문서 묶음을 실제 제작 handoff 기준으로 정리한 **production cutsheet**다.
- 프로그래머·UI·아트·QA가 각자 무엇을 넘기면 되는지 첫 화면에서 바로 찾을 수 있게 설계한다.
- 설명보다 전달물 중심으로 읽는 문서이므로, **대상 파일 / 산출물 / DoD**를 먼저 보면 된다.

**한 줄 목표:** `overrunEvent`를 owner별 산출물과 handoff 순서가 보이는 제작 패킷으로 압축한다.

### 왜 이 문서가 필요한가
- readable spec이 많아질수록 실제 제작 단계에서는 별도 cutsheet가 속도를 결정한다.
- 이 문서는 포털과 노션에서 바로 붙잡고 움직일 수 있는 실행판이다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | 대상 파일, 엔진/UI/smoke 산출물 | `integration workorder` |
| UI/아트 | 화면/자산 납품 패키지 | `screen packet`, `content deck` |
| QA | DoD와 증거 묶음 | `acceptance matrix` |
| PM | handoff 순서와 범위 | `summary` |

---

## 문서 목적

이 문서는 아래 3개 문서를 실제 제작 전달 단위로 다시 묶는 **생산 컷시트**다.

- `docs/dicespell_overrun_event_handoff_kr.md`
- `docs/dicespell_overrun_event_implementation_packet_kr.md`
- `docs/dicespell_overrun_event_screen_packet_kr.md`

지금까지의 문서화는 충분히 좋다. 방향도 고정됐고, phase도 고정됐고, 화면 구조도 고정됐다.
하지만 실제 제작 관점에서는 아직 마지막 빈칸이 남아 있다.

> 프로그래머 / UI 오너 / 아트 오너 / QA가 각자 “내가 이번 bounded 슬롯에서 무엇을 넘기면 끝인가”를 한 장에서 바로 읽을 수 있는 전달본이 아직 없다.

이번 문서는 그 빈칸만 메운다.

즉 이 문서는 새 시스템 제안서가 아니라, **오버런 이벤트 1차 제작을 실제로 닫기 위한 역할별 납품 기준표**다.

---

## 1. 왜 지금 이 문서가 필요한가

현재 실제 코드 기준으로 남은 병목은 더 이상 아이디어 부족이 아니다.

- `game-engine.js`의 `continueOverrun(state)`는 아직 **선택 보상을 즉시 적용하고 다음 세그먼트를 바로 여는 단일 함수**다.
- `app.js`의 `renderCenter(summary)`에는 아직 `overrunEvent` 분기가 없다.
- `renderEnding(summary)`의 보관함 카피도 아직 `다음 권 진입 직전에 즉시 적용` 기준으로 남아 있다.
- 화면/자산 handoff 문서는 있지만, 제작자 입장에서는 여전히 `내 할 일의 종료 조건`을 문서 3개 사이에서 조합해 읽어야 한다.

이 상태에서 바로 구현에 들어가면 아래 위험이 생긴다.

1. 프로그래머는 함수 분리와 save normalization까지 했는데, UI 오너는 `ending 확장`처럼 받아들일 수 있다.
2. UI 오너는 중앙 카드 1장 화면을 만들었는데, 아트 오너는 어떤 배지가 필수이고 어떤 건 후순위인지 헷갈릴 수 있다.
3. QA는 smoke는 통과했는데 handoff 완료인지 아닌지 판단 기준이 흐릴 수 있다.

이번 문서는 그 혼선을 막기 위해, **역할별 산출물 / 파일 접점 / 종료 조건 / 인수인계 순서**만 딱 고정한다.

---

## 2. 이번 bounded 제작의 한 줄 목표

> 책 클리어 엔딩에서 고른 전리품 1개를, 다음 세그먼트 진입 직전의 중앙 카드 1장 화면으로 보여 주고, CTA 시점에만 실제 적용한 뒤 다음 권 첫 페이지로 넘긴다.

이 목표를 벗어나는 것은 전부 이번 범위 밖이다.

---

## 3. 이번 컷시트의 최종 산출물

이번 작업이 끝났다고 말하려면 아래 4개가 모두 있어야 한다.

### 3.1 프로그래머 산출물

1. `phase = 'overrunEvent'`가 실제 런타임에서 동작한다.
2. `continueOverrun()`는 더 이상 즉시 resolve가 아니라 **진입(enter)** 역할을 맡는다.
3. 실제 전리품 적용은 `confirm-overrun-event` 액션에서만 1회 일어난다.
4. invalid save / empty selection / reload 복구가 `none` fallback까지 포함해 안정적으로 동작한다.

### 3.2 UI 산출물

1. `renderCenter(summary)`가 `overrunEvent`를 별도 화면으로 렌더한다.
2. 중앙 카드 1장 / CTA 1개 / footnote 1줄 구조가 유지된다.
3. `themeTrophy`, `enemyTrophy`, `none`이 한눈에 다른 상태로 읽힌다.
4. skip 상태도 임시 placeholder가 아니라 동일 밀도의 완성된 화면처럼 보인다.

### 3.3 아트 산출물

1. 헤더 패널 1종
2. 출처 배지 3종
3. 중앙 카드 강조 backplate 1세트
4. accent 규칙 4종(`slime`, `goblin`, `lich`, `none`)

### 3.4 QA 산출물

1. enter / render / confirm / load fallback까지 포함한 acceptance 체크리스트
2. 선택 있음 / 적 전리품 / 빈손 / invalid reward / reload의 다섯 축이 모두 닫혔다는 증거

---

## 4. 역할별 handoff 패킷

## 4.1 프로그래머용 컷시트

### 대상 파일

- `dice-spell-standalone/src/game-engine.js`
- `dice-spell-standalone/src/app.js`
- `dice-spell-standalone/src/styles.css`
- `dice-spell-standalone/scripts/smoke-test.mjs`

### 실제 변경해야 하는 핵심 지점

#### 엔진

현재:

- `chooseEndingOverrunReward(state, rewardId)`는 선택만 기록한다.
- `continueOverrun(state)`는 선택 보상 적용 + 오버런 세그먼트 추가 + 다음 페이지 진입을 한 번에 처리한다.

목표:

- `continueOverrun(state)`는 이제 **오버런 이벤트 진입용 wrapper**가 된다.
- 새 함수군은 아래 4개가 권장 기본 세트다.
  - `createOverrunEventSnapshot(state)`
  - `enterOverrunEvent(state)`
  - `resolveOverrunEvent(state)`
  - `normalizeOverrunEventState(state)`

#### 앱/UI

현재:

- `renderCenter(summary)`는 `battle / shop / reward / ending`만 처리한다.
- `renderEnding(summary)`는 보관함과 `오버런 계속` 버튼을 렌더한다.

목표:

- `renderCenter(summary)`에 `overrunEvent` 분기 추가
- `renderOverrunEvent(summary)` 신설
- `data-action="confirm-overrun-event"` 액션 추가
- `renderEnding(summary)`의 보관함 안내 문구를 `즉시 적용` 기준에서 `최종 확인 후 적용` 기준으로 수정

#### 스타일

현재:

- `.hero-card`, `.ending-card`, `.meta-retirement-card`, `.reward-card`, `.section-head`, `.primary-button`, `.ghost-button` 재사용 가능

목표:

- 재사용을 최우선으로 하되 아래 최소 클래스만 추가
  - `.overrun-event-card`
  - `.overrun-event-shell`
  - `.overrun-event-focus-card`
  - `.overrun-event-reward-card`
  - `.overrun-event-head`
  - `.overrun-event-actions`
  - `.overrun-source-badge`
  - `.overrun-source-badge.theme`
  - `.overrun-source-badge.enemy`
  - `.overrun-source-badge.none`

### 프로그래머 정의 완료(DoD)

아래가 모두 맞으면 프로그래머 산출물은 완료다.

- `continue-overrun` 클릭 후 `ending`에 머물지 않고 `overrunEvent`로 진입한다.
- 선택 전리품이 있으면 중앙 카드에 정확한 title / description / badge / CTA가 보인다.
- 선택 전리품이 없으면 `none` fallback 카드가 보인다.
- CTA 전에는 실제 bundle 효과가 적용되지 않는다.
- CTA 후에는 정확히 1회만 적용된다.
- resolve 직후 `segmentTrophyTemplateIds`가 비워진다.
- resolve 직후 다음 세그먼트가 pagePlan에 추가된다.
- invalid `rewardId` load에서도 런이 죽지 않고 `none`으로 강등된다.

---

## 4.2 UI 오너용 컷시트

### 화면 목적

이 화면은 `보상 확인 화면`이 아니라 **세그먼트 경계 화면**이다.

즉 UI는 아래 세 가지를 동시에 만족해야 한다.

1. ending reward grid와 확실히 다른 무게감
2. 새 시스템 소개처럼 과하지 않은 밀도
3. CTA를 누르기 전까지는 아직 “들고 넘어가기 직전”이라는 문맥 유지

### 정보 배치 고정

순서는 반드시 아래와 같다.

1. 전환 문맥
2. 대상 카드 1장
3. 행동 버튼
4. 시스템 확인 문구 1줄

### 화면 구조 고정

- eyebrow 1줄
- H1 1줄
- flavor copy 1~2줄
- 중앙 focus card 1장
- CTA 1개
- footnote 1줄

### 금지 사항

- reward grid 재사용
- secondary CTA 추가
- 뒤로 가기 / 다시 고르기 버튼 추가
- 상세 설명 패널을 카드 옆에 또 붙이는 구성

### UI 정의 완료(DoD)

- 처음 봐도 `ending 안의 부속 카드`가 아니라 `다음 권으로 넘어가는 중간 장면`으로 읽힌다.
- 카드 1장이 충분히 중심에 오고, 주변 정보가 그 카드를 받쳐 준다.
- `themeTrophy`, `enemyTrophy`, `none` 세 상태가 배지/카피/색감에서 모두 구분된다.
- `none`도 임시 페이지처럼 보이지 않는다.

---

## 4.3 아트 오너용 컷시트

### 이번 슬롯의 아트 목표

이번 단계의 아트는 풀아트가 아니라 **UI 장면화 패키지**다.

즉 새 삽화보다 아래가 더 중요하다.

- 헤더가 장면 전환처럼 보이는가
- 카드 1장이 제본대 위에 놓인 핵심 오브젝트처럼 읽히는가
- sourceType이 배지와 accent만으로 분명히 구분되는가

### 필수 자산 목록

#### 1) 헤더 패널

권장 산출물:

- `overrun_event_header_panel`

역할:

- `ending`과 `battle` 사이의 중간 의식 같은 톤을 만든다.
- 새 풀배경이 아니라 상단 영역 프레이밍에 집중한다.

#### 2) 출처 배지 3종

- `overrun_badge_theme`
- `overrun_badge_enemy`
- `overrun_badge_none`

역할:

- 텍스트만이 아니라 shape/color에서도 상태 구분을 만든다.

#### 3) 카드 강조 backplate 1세트

- `overrun_focus_backplate`
- 약한 glow 또는 edge accent 포함 가능

역할:

- 기존 reward card를 영웅 오브젝트처럼 보이게 만든다.

#### 4) accent 규칙 4종

- `slime`: 안정 / 회복 / 녹청 계열
- `goblin`: 노획 / 황동 / 경제 계열
- `lich`: 금서 / 자주 / 청보라 계열
- `none`: 중성 / 저채도 / 잿빛 계열

### 이번 단계에서 요구하지 않는 것

- 전리품별 새 일러스트
- sourceType별 완전 별도 배경
- 시네마틱 애니메이션
- 파티클 중심 연출

### 아트 정의 완료(DoD)

- 코드 임시 컬러 없이도 sourceType과 theme가 읽힌다.
- 기존 reward card를 올려놨을 때 장면 전환감이 생긴다.
- `none` 상태도 비어 보이지 않는다.

---

## 4.4 QA 오너용 컷시트

### 반드시 확인할 acceptance 12종

1. 승리 엔딩 + 오버런 가능 + 선택 있음 -> `continue-overrun` 시 `overrunEvent` 진입
2. 책 전리품 선택 -> 배지/헤더/CTA가 `themeTrophy` 규칙으로 표시
3. 적 전리품 선택 -> 배지/헤더/CTA가 `enemyTrophy` 규칙으로 표시
4. 선택 없이 진행 -> `none` fallback 카드 생성
5. `none` 상태에서도 CTA/footnote/배지가 모두 존재
6. CTA 전에는 bundle 효과 미적용
7. CTA 후에는 bundle 효과 정확히 1회 적용
8. resolve 후 pagePlan 세그먼트 추가
9. resolve 후 `segmentTrophyTemplateIds` 초기화
10. resolve 후 `overrunEvent` 재진입 없이 다음 페이지로 진행
11. `phase === 'overrunEvent'` 상태 저장 후 복구 -> 동일 화면 재구성 가능
12. invalid `rewardId` 복구 -> 런 생존 + `none` fallback 강등

### QA 한 줄 기준

> 이 화면은 예쁘기만 하면 되는 게 아니라, `보상이 늦게 적용된 단일 확인 단계`로서 상태/시각/저장 복구가 모두 같은 말을 해야 한다.

---

## 5. 산출물 간 인수인계 순서

이번 작업은 아래 순서로 넘기는 것이 가장 안전하다.

1. **프로그래머**가 snapshot 필드와 action 이름을 먼저 고정한다.
2. **UI 오너**가 그 필드 기준으로 DOM 구조와 class 배치를 확정한다.
3. **아트 오너**가 이미 고정된 class/slot에 헤더 패널, 배지, backplate를 얹는다.
4. **QA**가 선택 있음/빈손/invalid reward/reload 네 축부터 닫는다.

즉 이번 작업은 아트를 기다리며 멈출 필요가 없다.
초기 버전은 임시 색/텍스트 배지로도 구현 가능해야 하고, 아트는 그 위에 얹히는 순서가 맞다.

---

## 6. 실제 구현 순서 제안

### Step 1. 엔진 진입/resolve 분리

- `continueOverrun()`를 enter wrapper로 축소
- snapshot 생성
- `phase = 'overrunEvent'`

### Step 2. UI 최소 렌더

- `renderCenter()`에 분기 추가
- 텍스트만으로 중앙 카드 1장 렌더
- confirm action 연결

### Step 3. save/load normalization

- invalid reward -> `none`
- missing snapshot -> 재생성 또는 `none`
- reload 후 중복 적용 방지

### Step 4. CSS/배지/시각 차이 부여

- 카드 강조
- source badge
- accent 규칙 반영

### Step 5. QA + copy polish

- `즉시 적용` 문구 제거
- footnote 정리
- `none` 상태 카피 다듬기

이 순서를 어기면 보통 UI는 나오는데 상태가 흔들리거나, 상태는 맞는데 화면이 늦게 굳는 문제가 생긴다.

---

## 7. out-of-scope 재확인

이번 컷시트는 아래를 하지 않는다.

- 전리품 재선택
- 전리품 강화/합성
- 오버런 경로 분기
- 오버런 이벤트 바리에이션 추가
- 새 보상 시스템 정의
- 보스형 골렘 구현

이것들은 다음 단계 후보다.
이번 단계는 오직 **현재 보관함 선택을 장면과 상태 전이로 완성**하는 데만 집중한다.

---

## 8. 리스크 & 방지책

### 리스크 1. 문서 4개로 늘어나며 오히려 분산될 위험

- 영향: 제작자가 또 여러 문서를 왕복해야 할 수 있다.
- 대응: 이번 문서는 새 방향 문서가 아니라 `최종 생산 컷시트`임을 분명히 한다. 구현자는 방향이 아니라 체크리스트를 볼 때 이 문서를 우선 본다.

### 리스크 2. UI가 너무 풍성해져 bounded 범위를 넘을 위험

- 영향: 중앙 카드 1장 화면이 다시 소형 이벤트 시스템으로 커질 수 있다.
- 대응: CTA 1개, 카드 1장, 재선택 금지, 추가 패널 금지를 컷시트 수준에서 못 박는다.

### 리스크 3. `none` 상태가 여전히 임시 화면처럼 느껴질 위험

- 영향: skip fallback의 존재 이유가 약해진다.
- 대응: 배지/헤더/카드/backplate/CTA를 모두 유지하고, neutral accent까지 포함해 같은 완성도로 취급한다.

### 리스크 4. 아트가 늦어지면 구현도 늦어질 위험

- 영향: 문서만 쌓이고 실제 phase 구현이 밀릴 수 있다.
- 대응: 임시 텍스트 배지 + 기존 카드 재사용만으로도 먼저 동작하게 만드는 순서를 기본 전략으로 둔다.

---

## 9. 다음 handoff 문장

- **프로그래머에게 넘길 것:** `continueOverrun()` enter화, `overrunEvent` snapshot/resolve/normalize, `renderOverrunEvent()` 및 confirm action, smoke 12종.
- **UI 오너에게 넘길 것:** 중앙 카드 1장 구조, 상태별 배지/카피/footnote 배치, `ending`과 구분되는 계층감.
- **아트 오너에게 넘길 것:** 헤더 패널 1종, 배지 3종, backplate 1세트, accent 4종.
- **QA에게 넘길 것:** 선택 있음 / 적 전리품 / 빈손 / invalid reward / reload 축 acceptance.

---

## 10. 한 줄 결론

지금 `overrunEvent`에 더 필요한 것은 새 아이디어가 아니라, **실제 제작자가 자기 몫을 바로 끝낼 수 있게 만드는 납품 기준표**다. 이 컷시트는 그 마지막 빈칸을 메우는 문서다.
