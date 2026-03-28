# DiceSpell `overrunEvent` / 첫 런 온보딩 진입점 매트릭스

- 이 문서는 `overrunEvent` A-track과 첫 런 온보딩 B-track 문서가 충분히 쌓인 지금, **이번 턴에 누가 어떤 문서부터 열어야 하는가**를 한 장으로 고정하기 위한 source handoff다.
- 대상 독자는 프로그래머, UI 오너, 아트 오너, QA, 그리고 tracker를 갱신하는 문서 오너다.
- 핵심 목적은 새 설계를 늘리는 것이 아니라, 문서 과밀로 인해 다시 범위가 부풀거나 잘못된 요약 문서에서 구현을 시작하는 일을 막는 것이다.

## 한 줄 목표

`A-1~A-4 실구현 전환`과 `B-1~B-4 후속 온보딩 착수`에서 **owner별 첫 문서 / 보조 문서 / 금지 문서 / 종료 증거**를 즉시 찾게 만든다.

## 왜 이 문서가 필요한가

현재 DiceSpell의 `overrunEvent`와 온보딩 축은 handoff, screen, acceptance, workorder, source-of-truth, content deck, contract examples, delivery packet, A/B commit packet까지 대부분 닫혀 있다.

문제는 이제 문서 부족이 아니라 반대로 아래 위험이다.

1. 프로그래머가 summary에서 바로 구현을 시작해 packet의 종료 조건과 smoke 경계를 놓칠 수 있다.
2. UI/아트가 current baseline 설명 문서와 target handoff 문서를 같은 층위로 읽어 shell/tone 경계를 흐릴 수 있다.
3. QA가 acceptance matrix와 runtime smoke migration packet을 따로 읽다가 `이번 턴 종료 증거`를 commit 단위로 못 묶을 수 있다.
4. 포털 진입면에서 B-4 ending anchor가 약하게 노출되면, 온보딩이 `library → battle → reward/shop → ending` 전 surface를 이미 갖췄다는 사실이 한 화면에서 안 읽힐 수 있다.

즉 지금 필요한 것은 새 기획이 아니라, **문서가 충분한 상태에서 잘못 읽기 쉬운 첫 진입점**을 줄이는 일이다.

---

## 1. 첫 화면 요약

| 지금 하려는 일 | 누가 먼저 봐야 하나 | 첫 문서 | 바로 이어서 볼 문서 | 이 턴에 보면 안 되는 것 |
| --- | --- | --- | --- | --- |
| A-1 enter 분리 시작 | 프로그래머, QA | `docs/dicespell_overrun_cutover_runway_packet_kr.md` | `docs/dicespell_overrun_runtime_patch_map_kr.md`, `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md` | B-track packet, B-track primer 구현 문서 |
| A-2 snapshot canonicalization | 프로그래머, QA | `docs/dicespell_overrun_a2_snapshot_packet_kr.md` | `docs/dicespell_overrun_onboarding_contract_examples_kr.md`, `docs/dicespell_overrun_event_content_deck_kr.md` | render 카피 재조합, 임의 branch 추론 |
| A-3 중앙 카드 UI | UI 오너, 프로그래머, 아트 | `docs/dicespell_overrun_a3_ui_packet_kr.md` | `docs/dicespell_ending_overrun_boundary_packet_kr.md`, `docs/dicespell_overrun_event_screen_packet_kr.md` | A-4 resolve 규칙 선반영 |
| A-4 confirm resolve + smoke | 프로그래머, QA | `docs/dicespell_overrun_a4_resolve_packet_kr.md` | `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`, `docs/dicespell_overrun_cutover_runway_packet_kr.md` | 온보딩 primer 구현 착수 |
| B-1 library primer | UI 오너, 프로그래머, QA | `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md` | `docs/dicespell_first_run_onboarding_content_deck_kr.md`, `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` | battle/reward/shop/ending 동시 착수 |
| B-2 battle primer | UI 오너, 프로그래머, QA | `docs/dicespell_first_run_onboarding_b2_battle_packet_kr.md` | `docs/dicespell_overrun_onboarding_contract_examples_kr.md`, `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` | library/battle/reward를 한 커밋에 묶기 |
| B-3 reward/shop primer | UI 오너, 프로그래머, QA | `docs/dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md` | `docs/dicespell_first_run_onboarding_content_deck_kr.md`, `docs/dicespell_first_run_onboarding_screen_packet_kr.md` | reward와 shop을 한 strip으로 단순 병합 |
| B-4 ending primer | UI 오너, 프로그래머, QA, 아트 | `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md` | `docs/dicespell_ending_overrun_boundary_packet_kr.md`, `docs/dicespell_first_run_onboarding_content_deck_kr.md` | `themeTrophy` / `enemyTrophy` / `none` flavor를 ending이 먹기 |

---

## 2. 실행 라인별 진입점

### 2.1 A-track: `overrunEvent` 실구현 라인

#### 3-line summary
- A-track의 첫 문서는 항상 `runway packet`이다.
- 그 다음엔 `runtime patch map`으로 현재 live anchor를 확인하고, 해당 step packet으로 들어간다.
- summary 문서는 읽기 보조용이지, step 종료 조건을 대신하지 않는다.

#### 한 줄 목표
`runway → patch map → 현재 step packet → smoke/contract 보조 문서` 순서를 강제해 half-cutover를 막는다.

#### 왜 중요한가
A-track은 지금 가장 큰 blocker이지만, 동시에 문서도 가장 많다. 그래서 잘못 시작하면 아래 식으로 다시 무너진다.

- `summary`만 읽고 `continueOverrun()`를 잘랐다가 smoke 범위를 놓침
- A-2 전에 A-3 shell을 붙여 canonical snapshot을 다시 render가 판정함
- A-4 resolve 전에 onboarding B-track을 열어 잘못된 baseline 위에 primer를 얹음

#### 프로그래머 읽기 순서
1. `docs/dicespell_overrun_cutover_runway_packet_kr.md`
2. `docs/dicespell_overrun_runtime_patch_map_kr.md`
3. 해당 step packet
   - A-1: `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`
   - A-2: `docs/dicespell_overrun_a2_snapshot_packet_kr.md`
   - A-3: `docs/dicespell_overrun_a3_ui_packet_kr.md`
   - A-4: `docs/dicespell_overrun_a4_resolve_packet_kr.md`
4. step별 보조 기준
   - A-2: `docs/dicespell_overrun_onboarding_contract_examples_kr.md`, `docs/dicespell_overrun_event_content_deck_kr.md`
   - A-3: `docs/dicespell_ending_overrun_boundary_packet_kr.md`, `docs/dicespell_overrun_event_screen_packet_kr.md`
   - A-4: `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`

#### UI 오너 읽기 순서
1. `docs/dicespell_overrun_a3_ui_summary_kr.md`
2. `docs/dicespell_overrun_a3_ui_packet_kr.md`
3. `docs/dicespell_ending_overrun_boundary_summary_kr.md`
4. `docs/dicespell_overrun_event_content_deck_kr.md`

#### 아트 오너 읽기 순서
1. `docs/dicespell_overrun_a3_ui_summary_kr.md`
2. `docs/dicespell_overrun_event_screen_packet_kr.md`
3. `docs/dicespell_ending_overrun_boundary_packet_kr.md`
4. `docs/dicespell_overrun_event_content_deck_kr.md`

#### QA 읽기 순서
1. `docs/dicespell_overrun_cutover_runway_packet_kr.md`
2. 해당 step packet
3. `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
4. `docs/dicespell_overrun_event_acceptance_matrix_kr.md`

#### 이 라인에서 금지하는 시작점
- `docs/dicespell_overrun_event_summary_kr.md`만 읽고 구현 시작
- `docs/dicespell_current_build_reverse_spec_kr.md`만 읽고 target contract를 추론
- A-4 전인데 B-track packet을 동시에 펴는 것

---

### 2.2 B-track: 첫 런 온보딩 후속 라인

#### 3-line summary
- B-track은 A-4가 실제로 닫힌 뒤에만 연다.
- B-track 첫 문서는 항상 해당 surface의 B-step packet이다.
- content deck과 boundary packet은 카피/경계 확인용 보조문서이지, step packet을 대체하지 않는다.

#### 한 줄 목표
`B-1 → B-2 → B-3 → B-4` 순서를 owner별로 흔들리지 않게 유지한다.

#### 왜 중요한가
온보딩은 UI성 작업처럼 보이기 때문에 쉽게 합쳐지지만, 실제로는 `meta.uiGuidance` 플래그, action hook, render 밀도, CTA 비차단, ending-vs-overrun 경계가 함께 얽혀 있다.

즉 작은 안내처럼 보여도 잘못 묶으면 가장 빨리 drift가 난다.

#### surface별 첫 문서
| surface | 첫 문서 | 꼭 같이 볼 보조 문서 | 절대 섞지 말 것 |
| --- | --- | --- | --- |
| B-1 library | `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md` | `docs/dicespell_first_run_onboarding_content_deck_kr.md`, `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` | battle/reward hook |
| B-2 battle | `docs/dicespell_first_run_onboarding_b2_battle_packet_kr.md` | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`, `docs/dicespell_overrun_onboarding_contract_examples_kr.md` | reward/shop surface |
| B-3 reward/shop | `docs/dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md` | `docs/dicespell_first_run_onboarding_screen_packet_kr.md`, `docs/dicespell_first_run_onboarding_content_deck_kr.md` | ending CTA helper |
| B-4 ending | `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md` | `docs/dicespell_ending_overrun_boundary_packet_kr.md`, `docs/dicespell_first_run_onboarding_content_deck_kr.md` | `overrunEvent` flavor |

#### 역할별 handoff 포인트

##### 프로그래머
- B-1: `readMetaProgress()` / `persistMetaProgress()` / `renderGrimoireShelf()` anchor부터 시작
- B-2: `renderBattle(summary)` + cast / auto-pass event hook만 본다
- B-3: `renderReward(summary)` / `renderShop()` / boss draft 공존만 본다
- B-4: `renderEnding(summary)` / `오버런 전리품 보관함` / CTA helper만 본다

##### UI 오너
- 각 B-step summary를 먼저 읽고, packet에서 실제 밀도/금지 패턴을 확인한다.
- B-4에서는 반드시 `ending은 결과/정산/버튼 의미만, flavor는 overrunEvent` 규칙을 같이 본다.

##### 아트 오너
- B-1 hero-primer, B-2 inline hint, B-3 reward/shop accent 분리, B-4 ending 보조선 순으로 asset 밀도를 확인한다.
- B-4에서 flavor 일러스트를 ending에 선적용하지 않는다.

##### QA
- 메인 acceptance matrix를 기본으로 보되, 실제 시작점은 각 B-step packet의 acceptance/recovery다.
- B-4는 `B4-A1~A7`을 독립 커밋 종료 증거로 본다.

---

## 3. Owner별 빠른 읽기 경로

### 프로그래머

| 상황 | 1순위 | 2순위 | 3순위 | 종료 증거 |
| --- | --- | --- | --- | --- |
| A-1 착수 | runway packet | runtime patch map | A-1 packet | A1-S1~S4 smoke |
| A-2 착수 | A-2 packet | contract examples | content deck | canonical field set + normalize smoke |
| A-3 착수 | A-3 packet | ending boundary | screen packet | 단일 카드 UI sanity |
| A-4 착수 | A-4 packet | smoke migration | runway packet | S4~S12 + recovery |
| B-step 착수 | 해당 B-step packet | content deck / workorder | acceptance matrix | step acceptance + CTA 비차단 |

### UI 오너

| 상황 | 먼저 볼 것 | 확인 포인트 |
| --- | --- | --- |
| overrunEvent 카드 UI | `A-3 summary → A-3 packet` | badge → header → flavor → reward summary → CTA → footnote 위계 |
| ending 경계 검토 | `ending_overrun_boundary_summary → B4 summary → B4 packet` | ending이 flavor를 먹지 않는지 |
| reward/shop primer | `B-3 summary → B-3 packet` | reward와 shop의 목적 문구가 한 톤으로 뭉개지지 않는지 |

### 아트 오너

| 상황 | 먼저 볼 것 | 확인 포인트 |
| --- | --- | --- |
| overrunEvent | `A-3 summary` + `screen packet` | 장면 카드 집중도, ending과 다른 shell/tone |
| onboarding | 각 B-step summary | hero / inline / badge 밀도 차등 |
| ending | `B4 summary` + `boundary summary` | CTA 주변 보조선은 남기되 flavor는 분리 |

### QA

| 상황 | 먼저 볼 것 | 확인 포인트 |
| --- | --- | --- |
| A-track | runway packet | 이번 턴 종료선이 A-1인지 A-2인지 먼저 고정됐는가 |
| A-4 | A-4 packet + smoke migration | 재실행 금지, `none` fallback, old baseline 제거 |
| B-track | 해당 B-step packet | surface별 acceptance/recovery가 독립 증거로 남는가 |

---

## 4. 포털/문서 진입면 정렬 기준

### 3-line summary
- 포털은 단순 링크 창고가 아니라, `누가 어디부터 읽는가`가 첫 화면에서 보여야 한다.
- 특히 온보딩은 B-4 ending anchor까지 같이 보여야 전 surface가 handoff-ready 상태라는 점이 읽힌다.
- 이번 문서의 companion은 포털 링크용 빠른 보기 역할을 맡는다.

### 이번 턴 반영 원칙
1. `docs/index.html`의 온보딩 섹션에서 B-4 ending summary/packet을 같은 위계로 노출한다.
2. `entrypoint matrix`와 `summary`를 포털에서 바로 열 수 있게 넣는다.
3. 오버런 섹션에도 같은 문서를 연결해, A-track/B-track 분기 기준을 한 화면에서 찾게 한다.

---

## 5. 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 | 다음 후속 |
| --- | --- | --- | --- |
| summary 문서에서 바로 구현을 시작하는 위험 | 종료선과 smoke 범위를 놓치기 쉽다 | owner별 `첫 문서`를 이 매트릭스로 고정 | tracker와 포털이 항상 packet 우선 진입을 가리키는지 유지 |
| 온보딩 B-4가 포털에서 약하게 보이는 위험 | ending surface도 commit-level anchor를 갖췄다는 점이 묻힌다 | index에 B-4 summary/packet 노출 추가 | A-track 종료 후 B-4 착수 시 같은 링크 구조 재사용 |
| current baseline 문서와 target packet 혼독 위험 | UI/카피/branch가 다시 역추론된다 | current vs target을 문서 역할별로 다시 분리 표기 | 실구현 턴에도 patch map을 먼저 읽는 습관 유지 |
| A-track 직전에 B-track을 같이 여는 위험 | 잘못된 baseline 위 primer 구현 | 이 문서에서 A-4 선행 규칙 재강조 | tracker 리스크 섹션에서도 같은 순서 유지 |

---

## 6. 즉시 다음 액션

1. `overrunEvent` 실구현 턴은 여전히 `runway packet → runtime patch map → 해당 A-step packet` 순서로만 시작한다.
2. 온보딩은 포털과 읽기 경로를 B-4까지 같은 레벨로 노출하되, 실제 구현은 A-4 종료 전 열지 않는다.
3. tracker / blueprint / portal index / automation log는 이번 문서의 역할을 **새 설계 추가가 아니라 문서 진입점 정렬**로 기록한다.

---

## 7. 누가 무엇을 읽어야 하나 — 한 줄 버전

- 프로그래머: `runway` 또는 해당 `B-step packet`부터.
- UI: 해당 `summary` → `packet` 순서로.
- 아트: `summary`로 밀도 확인 후 `screen/packet`으로.
- QA: `packet`과 `smoke/acceptance`를 같은 턴 종료 증거로 묶어서.
- 문서 오너: 포털 첫 화면에서 위 순서가 실제로 보이는지 먼저 확인.
