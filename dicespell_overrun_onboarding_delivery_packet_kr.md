# DiceSpell `overrunEvent` → 첫 런 온보딩 컷오버 전달 패킷

## 문서 목적

이 문서는 이미 닫힌 아래 문서 묶음을 **실제 제작자가 바로 착수 가능한 전달 단위**로 다시 묶는 delivery packet이다.

- `docs/dicespell_overrun_onboarding_cutover_master_plan_kr.md`
- `docs/dicespell_overrun_onboarding_contract_examples_kr.md`
- `docs/dicespell_overrun_event_*`
- `docs/dicespell_first_run_onboarding_*`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`

지금 DiceSpell의 큰 문서 공백은 거의 닫혔다. 남은 병목은 더 단순하다.

> `다음 구현자가 이 문서 묶음을 들고 실제로 어떤 파일을 어떤 순서로 건드리고, 어느 지점에서 누구에게 handoff하며, 어떤 증거가 남으면 이 slice가 끝났다고 말할 수 있는가?`

즉 이 문서는 새 설계를 제안하지 않는다. 이미 확정된 계약을 기준으로 **파일 경계 / handoff 경계 / commit 경계 / 검증 경계**를 한 장으로 고정하는 전달 패킷이다.

---

## 왜 지금 이 문서가 필요한가

현재 상태는 좋지만, 실제 착수 직전엔 오히려 아래 운영 공백이 남아 있다.

1. `overrunEvent`와 온보딩 문서가 충분히 풍부해서, 구현자가 다시 자기 식으로 작업 순서를 재구성하기 쉽다.
2. 프로그래머/UI/아트/QA 모두 각자 읽을 문서는 있는데, **이번 슬롯의 실제 납품물 묶음**이 한 장으로 닫혀 있지는 않다.
3. `continueOverrun()` 컷오버, `renderCenter()` 분기 추가, `getPrimerState()` 주입은 기술적으로 다른 일처럼 보여도 실제 handoff에선 한 번에 엮인다.
4. 문서가 충분할수록 오히려 `이번 커밋에 무엇을 같이 넣고 무엇을 다음 커밋으로 미룰지`가 흐려질 수 있다.

지금 필요한 것은 새로운 방향 문서가 아니라,
**이미 닫힌 방향 문서를 실제 delivery slice로 번역한 handoff packet**이다.

---

## 이 문서의 한 줄 결론

> 다음 컷오버는 `overrunEvent`를 먼저 엔진/렌더/스모크 단위로 독립시키고, 그 다음 온보딩을 `library → battle → reward/shop → ending` 순서의 작은 전달 묶음으로 얹는다. 각 묶음은 파일 경계와 검증 증거를 같이 남겨야 닫힌다.

---

## 범위

### 포함
- `overrunEvent` Step 0~4를 실제 파일/owner/commit 단위로 나눈 전달 순서
- 첫 런 온보딩을 `library`, `battle`, `reward/shop`, `ending` 묶음으로 나눈 전달 순서
- 프로그래머 / UI / 아트 / QA handoff 경계
- 최소 검증 증거와 커밋 단위
- 드리프트를 막기 위한 금지 패턴

### 제외
- 새 시스템 추가 제안
- 새 전리품 타입 설계
- 밸런스 재조정
- 장기 메타 확장
- 대규모 아트 파이프라인 구축

---

## canonical source 요약

| 질문 | source-of-truth |
| --- | --- |
| `overrunEvent`를 왜, 어디까지 붙이는가 | `docs/dicespell_overrun_event_handoff_kr.md` |
| 실제 코드 접점과 함수 전략은 무엇인가 | `docs/dicespell_overrun_event_implementation_packet_kr.md` |
| Step 0~4 컷오버 순서는 무엇인가 | `docs/dicespell_overrun_event_integration_workorder_kr.md` |
| S1~S12 smoke는 어떻게 본다 | `docs/dicespell_overrun_event_smoke_migration_packet_kr.md` |
| exact snapshot/CTA/accent 계약은 무엇인가 | `docs/dicespell_overrun_event_content_deck_kr.md` |
| 샘플 payload는 어떤 모양인가 | `docs/dicespell_overrun_onboarding_contract_examples_kr.md` |
| ending과 overrunEvent의 경계는 어디인가 | `docs/dicespell_ending_overrun_boundary_packet_kr.md` |
| primer 주입 위치와 helper 계약은 무엇인가 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| primer exact content key는 무엇인가 | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| 현재 baseline 코드는 무엇인가 | `docs/dicespell_current_build_reverse_spec_kr.md`, `docs/dicespell_current_build_code_map_kr.md` |

---

## 실제 파일 경계

| owner | 주 파일 | 이번 컷오버 역할 |
| --- | --- | --- |
| 프로그래머 | `dice-spell-standalone/src/game-engine.js` | `overrunEvent` snapshot/enter/resolve/normalize, primer state source 정리 |
| 프로그래머 | `dice-spell-standalone/src/app.js` | `renderCenter()` 분기, `renderOverrunEvent(summary)`, `getPrimerState()` 소비 surface |
| 프로그래머 + UI | `dice-spell-standalone/src/styles.css` | `overrunEvent` 카드와 primer layer 최소 스타일 고정 |
| 프로그래머 + QA | `dice-spell-standalone/scripts/smoke-test.mjs` | S1~S12 / primer acceptance 기반 smoke 이행 |
| UI + 아트 | Figma/토큰 자산 또는 동등 전달본 | badge/accent/backplate 최소 패키지 |
| QA | smoke 결과 + 수동 체크 캡처 | recovery / boundary / density 검수 증거 |

---

## 전달 원칙

### 원칙 1. 한 커밋에 상태 전이와 검증 증거를 같이 남긴다
`overrunEvent` enter만 넣고 smoke를 다음 커밋으로 미루면 source-of-truth drift가 생긴다. 각 slice는 최소한 해당 slice를 닫는 smoke 또는 수동 증거를 같이 남긴다.

### 원칙 2. `ending`은 끝까지 얇다
`ending`은 결과 해석과 CTA 의미만 맡는다. flavor, 장면성, branch 감정선은 `overrunEvent`가 맡는다. 온보딩 ending primer도 이 규칙을 깨지 않는다.

### 원칙 3. `contentKey`를 중심으로 handoff한다
카피 일부가 다듬어져도 `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `CTA semantics`가 같으면 같은 branch다. QA와 UI는 문자열 미세 차이보다 이 축을 먼저 본다.

### 원칙 4. 온보딩은 새 phase가 아니라 guidance layer다
`overrunEvent`는 중앙 카드 1장 phase다. 반면 온보딩은 hero/inline/badge guidance layer다. 이 둘을 같은 구조로 그리면 안 된다.

---

# 1. 패스 A — `overrunEvent` 전달 묶음

## A-1. 엔진 진입 분리 묶음

### 목표
`continueOverrun()`를 즉시 적용 함수가 아니라 `overrunEvent` 진입 wrapper로 좁힌다.

### 프로그래머 deliverable
- `createOverrunEventSnapshot(state)` 또는 동등 함수
- `enterOverrunEvent(state)` 또는 동등 함수
- `continueOverrun()`가 직접 보상을 적용하지 않고 `phase = 'overrunEvent'`로 들어가게 수정
- invalid/skip/missing 상태에서도 `none` fallback snapshot 생성

### UI/아트 handoff
- 아직 완성 화면이 아니어도 `overrunEvent` 전용 render slot이 생길 것이라는 전제를 공유
- 이 단계에서는 구조보다 slot 존재 여부가 중요

### QA deliverable
- `ending -> overrunEvent` 진입 smoke 또는 동등 검증 1세트
- 진입 직후 아직 보상이 적용되지 않았다는 증거

### 추천 커밋 범위
- `src/game-engine.js`
- `scripts/smoke-test.mjs`
- 필요 시 `src/app.js`의 최소 분기 placeholder

### 이 묶음의 종료 조건
- `continue-overrun` 후 즉시 적용이 사라졌다
- `phase === 'overrunEvent'` 상태가 실제 저장 상태에 남는다
- 런이 막히지 않는다

---

## A-2. snapshot / normalize 묶음

### 목표
snapshot이 `contentKey` 중심 계약을 직접 들고 다니게 만든다.

### 프로그래머 deliverable
- snapshot에 `sourceType`, `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel` 포함
- `normalizeOverrunEventState(state)` 또는 동등 복구 함수
- reload 시 `overrunEvent` 복구
- 잘못된 reward/누락 데이터는 `fallback.none`으로 강등

### UI deliverable
- render가 문자열 역추론 없이 snapshot 필드를 그대로 소비한다는 계약 확인

### 아트 deliverable
- `themeTrophy`, `enemyTrophy`, `none` 세 가지 기본 토큰 레이어 정의
- `none`도 빈 장면이 아니라 정상 카드로 읽히게 하는 neutral 토큰 합의

### QA deliverable
- slime/goblin/lich/golem/collapse/none 또는 동등 exact key 검수표
- reload/invalid reward/missing snapshot recovery 검수 기준

### 추천 커밋 범위
- `src/game-engine.js`
- `src/app.js`
- `scripts/smoke-test.mjs`

### 종료 조건
- render 단계에서 branch 재판정이 없다
- recovery 시나리오가 전부 `none` 포함 정상 밀도로 수렴한다

---

## A-3. 중앙 카드 1장 UI 묶음

### 목표
`overrunEvent`를 정산표가 아니라 장면 카드로 읽히게 만든다.

### UI deliverable
- badge / header / flavor / reward summary / CTA / footnote 1줄 위계 확정
- `ending`과 시각적으로 다른 중앙 카드 1장 구조
- reward-card 재사용은 display-only로만 제한

### 아트 deliverable
- `themeTrophy`, `enemyTrophy`, `none` 공통 backplate
- accent strip / badge tone / focus token 최소 패키지
- `none` branch가 빈 상태처럼 보이지 않게 하는 neutral asset note

### 프로그래머 deliverable
- `renderCenter(summary)` 내 `overrunEvent` 분기
- `renderOverrunEvent(summary)` 또는 동등 함수
- CTA 동작은 아직 confirm 시점 1회 resolve 전제를 유지

### QA deliverable
- `ending`보다 `overrunEvent`가 장면 카드로 읽히는지 수동 캡처
- CTA semantics가 `확인 후 적용`인지 확인

### 추천 커밋 범위
- `src/app.js`
- `src/styles.css`
- 필요 시 `scripts/smoke-test.mjs`

### 종료 조건
- `overrunEvent`가 결과표처럼 읽히지 않는다
- `ending` 카피가 flavor를 중복하지 않는다

---

## A-4. confirm resolve + smoke 묶음

### 목표
confirm CTA 시점에만 보상을 1회 적용하고 다음 세그먼트로 넘긴다.

### 프로그래머 deliverable
- `resolveOverrunEvent(state)` 또는 동등 함수
- `data-action="confirm-overrun-event"` 연결
- confirm 후 reward 적용 + 다음 세그먼트 진입 + cleanup
- resolved 재실행 방지

### QA deliverable
- S1~S12 smoke 통과 또는 동등 세트
- recovery 6종 통과
- 선택 있음 / 선택 없음 / invalid / reload / 이미 resolved 케이스 검수

### UI/아트 deliverable
- confirm 이후 다음 phase 전환에서 화면 flicker / stale copy 없음

### 추천 커밋 범위
- `src/game-engine.js`
- `src/app.js`
- `scripts/smoke-test.mjs`

### 종료 조건
- `overrunEvent` 컷오버 전체가 문서와 같은 동작을 가리킨다
- `ending`은 끝까지 결과/버튼 의미만 말한다

---

# 2. 패스 B — 첫 런 온보딩 전달 묶음

## B-1. library primer 묶음

### 목표
첫 책 선택 화면에서 무엇을 먼저 읽어야 하는지 만든다.

### 프로그래머 deliverable
- `meta.uiGuidance` seen 구조 또는 동등 저장 구조
- `library.book-select` primer payload 생성
- 첫 진입 노출 / 이후 비재노출 제어

### UI deliverable
- shelf를 가리지 않는 hero/inline primer 배치
- `추천 운영 -> 주의점` 읽기 순서 유지

### 아트 deliverable
- shelf marker 계열 최소 토큰 패키지

### QA deliverable
- 첫 진입만 노출, 선택 방해 없음, 새 프로필 재노출 검수

### 추천 커밋 범위
- `src/meta-progression.js`
- `src/app.js`
- `src/styles.css`
- 필요 시 smoke 또는 수동 체크 캡처

### 종료 조건
- primer가 있어도 책 선택 자체가 느려지지 않는다

---

## B-2. battle primer 묶음

### 목표
첫 전투에서 `한 턴 = 한 주문`, `첫 주문`, `자동 종료`를 과장 없이 읽히게 한다.

### 프로그래머 deliverable
- `battle.entry`, `battle.first-spell`, `battle.auto-pass` helper payload
- 첫 주문 직전/직후, 자동 종료 시점 hook 연결

### UI deliverable
- hero → inline → badge 밀도 유지
- 주사위/행동 버튼/로그와 겹치지 않는 위치 확정

### 아트 deliverable
- battle ribbon / spell ping / auto-pass badge 최소 토큰

### QA deliverable
- 첫 전투 진입 1회, 첫 주문 힌트 1회, 자동 종료 사유 1회 검수
- 반복 노출 과다 없음

### 종료 조건
- battle primer가 튜토리얼 창처럼 흐름을 멈추지 않는다

---

## B-3. reward / shop primer 묶음

### 목표
같은 선택 화면처럼 보여도 역할이 다르다는 점을 drift 없이 분리한다.

### 프로그래머 deliverable
- `reward.first`, `shop.first` helper payload
- 공통 컴포넌트 재사용 시 surface별 accent와 message source 유지

### UI deliverable
- reward는 즉시 전술, shop은 장기 투자라는 톤 차이 고정
- 두 surface가 같은 컴포넌트를 쓰더라도 읽기 목적이 섞이지 않게 배치

### 아트 deliverable
- reward-green, shop-purple 또는 동등 대비 토큰

### QA deliverable
- reward/shop 카피 목적 뒤바뀜 없음
- `accentKey`와 `badgeLabel`이 서로 다르게 소비되는지 확인

### 종료 조건
- 플레이어가 보상과 상점을 같은 결정 축으로 읽지 않는다

---

## B-4. ending primer 묶음

### 목표
결과 화면에서 무엇이 남고 버튼이 무엇을 뜻하는지까지만 얇게 말한다.

### 프로그래머 deliverable
- `ending.defeat`, `ending.victory`, `ending.overrun-cta` payload
- `overrunEvent`가 있는 빌드 기준으로 ending primer와 장면 card가 겹치지 않게 연결

### UI deliverable
- inline / badge 레벨 유지
- hero-card 금지

### 아트 deliverable
- ending-seal / overrun-arrow 계열 경량 토큰

### QA deliverable
- ending이 overrun flavor를 먹지 않는지 확인
- `결과 해석 + CTA 의미`까지만 남는지 확인

### 종료 조건
- `ending`과 `overrunEvent`가 같은 말을 하지 않는다

---

# 3. owner별 handoff 순서

| 순서 | 프로그래머 | UI | 아트 | QA |
| --- | --- | --- | --- | --- |
| 1 | A-1 엔진 진입 분리 | `overrunEvent` slot placeholder 확인 | 구조보다 토큰 범위 확인 | 진입 smoke 준비 |
| 2 | A-2 snapshot/normalize | snapshot 소비 규칙 확인 | branch별 accent/backplate 정의 | recovery 기준 준비 |
| 3 | A-3 카드 UI | 중앙 카드 위계 확정 | 최소 패키지 납품 | density / boundary 수동 검수 |
| 4 | A-4 confirm resolve | flicker/stale copy 점검 | CTA transition 자산 점검 | S1~S12 + recovery 통과 |
| 5 | B-1 library primer | library 배치 확정 | shelf marker | 첫 진입 검수 |
| 6 | B-2 battle primer | battle 밀도 확정 | battle 토큰 | 첫 주문/자동 종료 검수 |
| 7 | B-3 reward/shop primer | 목적 톤 분리 확정 | reward/shop strip | 목적 뒤바뀜 검수 |
| 8 | B-4 ending primer | ending 얇은 레이어 확정 | ending badge | shared-surface 경계 검수 |

---

# 4. commit / PR 권장 구조

## 권장 묶음 1
- `docs + overrunEvent enter/snapshot baseline`
- 포함: A-1, A-2 일부
- 증거: 진입 smoke, fallback snapshot 예시

## 권장 묶음 2
- `overrunEvent card UI + confirm resolve + smoke migration`
- 포함: A-3, A-4
- 증거: S1~S12, recovery, 캡처 2~3장

## 권장 묶음 3
- `onboarding library/battle primer`
- 포함: B-1, B-2
- 증거: 첫 진입/첫 주문/자동 종료 검수 캡처

## 권장 묶음 4
- `onboarding reward/shop/ending primer`
- 포함: B-3, B-4
- 증거: surface 간 목적 분리 캡처, boundary 검수

> 혼자 작업하더라도 이 묶음 경계를 유지하는 편이 rollback과 review가 쉽다.

---

# 5. 이번 전달 패킷 기준 금지 패턴

1. `reward.title`이나 UI 문자열을 다시 읽어 `contentKey`를 추론하지 말 것
2. `ending` 본문에 `themeTrophy / enemyTrophy / none` flavor를 넣지 말 것
3. primer를 새 phase 카드처럼 키우지 말 것
4. `overrunEvent` smoke 마이그레이션 전 UI만 보고 완료로 간주하지 말 것
5. `reward`와 `shop` primer를 같은 문장 구조로 통합하지 말 것
6. `none` fallback을 빈 상태로 처리하지 말 것
7. 검증 증거 없이 커밋만 쪼개고 handoff가 닫혔다고 말하지 말 것

---

# 6. 리스크 & 후속 과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| 문서가 많아 구현자가 이번 slice 범위를 다시 임의로 자를 위험 | commit 경계와 source-of-truth drift | 이 문서의 A-1~A-4 / B-1~B-4 묶음을 기본 전달 단위로 사용 |
| `ending`과 `overrunEvent`가 다시 같은 카피를 먹는 위험 | 감정선/책임 경계 붕괴 | boundary packet + 이 문서의 B-4 종료 조건을 같이 확인 |
| QA가 카피 일부보다 중요한 phase/recovery를 놓칠 위험 | 저장/복구 버그 미탐지 | `contentKey`, `CTA semantics`, `density`, `recovery` 우선 검수 |
| UI가 온보딩을 새 장면 카드로 키우는 위험 | 본 게임 흐름 방해 | `overrunEvent = 중앙 카드`, `onboarding = guidance layer` 규칙 유지 |
| 문서 커밋은 했지만 tracker/log가 늦게 따라오는 위험 | 운영 이력 드리프트 | 같은 사이클에서 tracker, public tracker, automation log 같이 갱신 |

### 다음 후속 액션
1. 실제 구현자가 들어가기 전 이 문서를 `cutover master plan`과 같이 읽고, 이번 턴이 어느 묶음까지인지 먼저 선언한다.
2. 첫 실구현은 A-1부터 시작하되, 적어도 진입 smoke와 fallback snapshot 증거를 같은 커밋에 남긴다.
3. 온보딩은 A-4가 닫힌 뒤 B-1부터 시작한다. `ending` primer 선행 구현은 금지다.

---

## 한 줄 결론

> DiceSpell의 문서 공백은 거의 닫혔다. 지금 필요한 것은 새 spec이 아니라, **다음 제작자가 바로 파일/커밋/검증 단위로 옮길 수 있는 전달 패킷**이다. 이 문서는 그 마지막 운영 공백을 메운다.
