# DiceSpell `overrunEvent` / 첫 런 온보딩 문서 스택 패킷

## 3줄 요약
- 이 문서는 지금 DiceSpell의 `overrunEvent` A-track과 첫 런 온보딩 B-track에 쌓여 있는 문서가 충분히 많아진 상태에서, **누가 어떤 문서를 source-of-truth로 읽고 어떤 문서는 빠른 companion으로만 읽어야 하는지**를 고정하는 문서다.
- 핵심은 새 설계를 더하는 것이 아니라, `source handoff 문서`와 `readable summary 문서`를 섞어 읽어 구현 경계가 흐려지는 위험을 줄이는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `지금 내 기준 문서는 무엇인지`, `어느 문서는 참고만 해야 하는지`, `다음 단계에서 어떤 문서 묶음을 열어야 하는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
문서 세트를 `많은 자료`가 아니라 **역할별·단계별로 바로 집어 들 수 있는 source stack**으로 재배열한다.

## 왜 이 문서가 필요한가
지금 DiceSpell의 문서 병목은 `무슨 문서가 없지?`보다 `문서는 충분한데 무엇이 기준 문서고 무엇이 읽기용 요약인지 한눈에 안 보인다`는 데 있다.

이미 있는 것들:
- `overrunEvent`의 phase/화면/검수/실행/기록/증거/아카이브 문서 묶음
- 온보딩 B-track의 surface packet / runtime stack / scorecard / owner workboard / content deck 문서 묶음
- `readiness board`, `gap ledger`, `semantic anchor`, `runtime rehearsal`, `evidence manifest`, `artifact archive` 같은 운영 문서 묶음

하지만 실제 handoff 직전에는 아래 혼선이 아직 다시 생기기 쉽다.

1. source packet 대신 summary만 읽고 구현에 들어간다.
2. current baseline 설명 문서와 target cutover 문서를 같은 층위로 읽는다.
3. Commit 1/2와 B-1~B-4에서 열어야 할 문서 묶음이 owner마다 다르게 해석된다.
4. 포털에서 source doc보다 summary doc가 먼저 보이면, quick-read 문서가 실제 계약서처럼 소비된다.

즉 지금 필요한 것은 새 화면 설계가 아니라, **문서 자체의 읽기 위계를 production handoff 수준으로 다시 못 박는 문서 스택 패킷**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. 단계별 문서 스택`, `3-1. 프로그래머 handoff` | `runtime patch map`, `commit1/commit2 surgery sheet`, 각 source packet | summary가 아니라 packet이 실제 구현 계약이라는 점을 고정한다 |
| UI | `1. first screen 판정`, `2. 단계별 문서 스택`, `3-2. UI handoff` | `visual asset packet`, `A-3 UI packet`, `B-1~B-4 packet` | summary는 읽기용, 시각 경계와 밀도 계약은 source packet이 기준이라는 점을 고정한다 |
| 아트 | `2. 단계별 문서 스택`, `3-3. 아트 handoff` | `visual asset packet`, `content deck`, `boundary packet` | token/accent/boundary 기준은 source packet에 있고 summary는 설명용임을 확인한다 |
| QA | `2. 단계별 문서 스택`, `3-4. QA handoff`, `5. 리스크 & 후속과제` | `fixture ledger`, `execution reporting packet`, `evidence manifest`, `artifact archive packet` | 검수 기준과 제출 구조는 summary가 아니라 packet이 기준이라는 점을 고정한다 |
| PM/기획 | `1. first screen 판정`, `4. source vs summary 규칙`, `5. 리스크 & 후속과제` | `readiness board`, `owner handoff packet`, `development tracker` | 포털/노션 공유용 summary와 실제 기준 packet을 다른 문장으로 다뤄야 함을 확인한다 |

---

## 1. first screen 판정

### 이 문서를 왜 열어야 하나
- **프로그래머**: 지금 구현에 들어갈 때 어느 packet이 실제 계약서인지 다시 잠그려고 연다.
- **UI/아트**: summary를 보고 감으로 마감하지 않고, 실제 density/boundary/token 계약이 어느 문서에 있는지 확인하려고 연다.
- **QA/PM**: 공개 포털에서 summary를 공유하더라도, 검수/기록/착수 판단은 source packet으로 해야 한다는 점을 다시 잠그려고 연다.

### 가장 짧은 답
- `packet`, `sheet`, `matrix`, `ledger`, `workorder`, `scorecard`, `workboard`는 기본적으로 **source-of-truth 후보**다.
- `summary`는 기본적으로 **readable companion**이다.
- `current build reverse spec` / `current build code map`은 **현재 baseline 설명 문서**이고, `A-track` / `B-track` packet은 **target contract 문서**다.
- 구현 직전에는 `summary → packet` 순서로 읽어도 되지만, **packet 확인 없이 착수하면 안 된다.**

### 한 줄 판정 규칙
- quick-read는 `summary`.
- 실제 handoff/검수/기록/컷오버 기준은 `packet`.
- baseline 확인은 `current build` 문서.
- target 착수 기준은 `A-track/B-track` 문서.

---

## 2. 단계별 문서 스택

## 3줄 요약
- 아래 표는 Commit 1 / Commit 2 / B-1~B-4에서 어떤 문서가 `source`, 어떤 문서가 `companion`, 어떤 문서가 `baseline`, 어떤 문서가 `운영 보조`인지 다시 압축한 것이다.
- 목적은 implementer가 문서를 많이 읽더라도 **기준 문서 1세트**를 잃지 않게 만드는 것이다.
- 각 단계는 `baseline -> source -> companion -> operational` 순서로 읽는 것을 기본으로 한다.

### 한 줄 목표
각 단계마다 `무엇을 참고하고 무엇으로 결정하는지`를 명시한다.

| 단계 | baseline 문서 | source handoff 문서 | readable companion | 운영 보조 문서 | 착수 전 한 줄 결론 |
| --- | --- | --- | --- | --- | --- |
| Commit 1 floor | `dicespell_current_build_reverse_spec_kr.md`, `dicespell_current_build_code_map_kr.md`, `dicespell_overrun_runtime_patch_map_kr.md` | `dicespell_overrun_first_runtime_slice_packet_kr.md`, `dicespell_overrun_commit1_floor_surgery_sheet_kr.md`, `dicespell_overrun_first_runtime_state_matrix_kr.md` | `dicespell_overrun_first_runtime_slice_summary_kr.md`, `dicespell_overrun_commit1_floor_surgery_summary_kr.md` | `readiness board`, `semantic anchor packet`, `runtime rehearsal packet`, `fixture ledger`, `evidence manifest`, `artifact archive packet` | Commit 1은 summary가 아니라 floor packet + surgery sheet로 닫는다 |
| Commit 2 closing | `dicespell_current_build_reverse_spec_kr.md`, `dicespell_overrun_runtime_patch_map_kr.md` | `dicespell_overrun_second_runtime_slice_packet_kr.md`, `dicespell_overrun_commit2_closing_surgery_sheet_kr.md`, `dicespell_overrun_a3_ui_packet_kr.md`, `dicespell_overrun_a4_resolve_packet_kr.md`, `dicespell_ending_overrun_boundary_packet_kr.md` | `dicespell_overrun_second_runtime_slice_summary_kr.md`, `dicespell_overrun_commit2_closing_surgery_summary_kr.md`, `dicespell_overrun_a3_ui_summary_kr.md`, `dicespell_overrun_a4_resolve_summary_kr.md`, `dicespell_ending_overrun_boundary_summary_kr.md` | `visual asset packet`, `semantic anchor packet`, `runtime rehearsal packet`, `fixture ledger`, `evidence manifest`, `artifact archive packet` | Commit 2는 scene/resolve/boundary packet을 source로 묶어야 한다 |
| B-1 library | `dicespell_current_build_reverse_spec_kr.md`, `dicespell_first_run_onboarding_source_of_truth_map_kr.md` | `dicespell_first_run_onboarding_b1_library_packet_kr.md`, `dicespell_first_run_onboarding_screen_packet_kr.md` | `dicespell_first_run_onboarding_b1_library_summary_kr.md` | `runtime stack`, `handoff scorecard`, `owner workboard`, `execution reporting packet`, `artifact archive packet` | B-1은 library packet이 기준이고 runtime stack은 순서 보조다 |
| B-2 battle | `dicespell_current_build_reverse_spec_kr.md`, `dicespell_first_run_onboarding_source_of_truth_map_kr.md` | `dicespell_first_run_onboarding_b2_battle_packet_kr.md`, `dicespell_first_run_onboarding_screen_packet_kr.md` | `dicespell_first_run_onboarding_b2_battle_summary_kr.md` | `runtime stack`, `handoff scorecard`, `owner workboard`, `execution reporting packet`, `artifact archive packet` | B-2는 battle packet과 content deck을 같이 source로 본다 |
| B-3 reward/shop | `dicespell_current_build_reverse_spec_kr.md`, `dicespell_first_run_onboarding_source_of_truth_map_kr.md` | `dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md`, `dicespell_first_run_onboarding_content_deck_kr.md` | `dicespell_first_run_onboarding_b3_reward_shop_summary_kr.md` | `runtime stack`, `handoff scorecard`, `owner workboard`, `execution reporting packet`, `artifact archive packet` | B-3는 reward/shop 목적 분리를 packet과 content deck으로 같이 읽는다 |
| B-4 ending | `dicespell_current_build_reverse_spec_kr.md`, `dicespell_ending_overrun_boundary_packet_kr.md` | `dicespell_first_run_onboarding_b4_ending_packet_kr.md`, `dicespell_ending_overrun_boundary_packet_kr.md`, `dicespell_first_run_onboarding_content_deck_kr.md` | `dicespell_first_run_onboarding_b4_ending_summary_kr.md`, `dicespell_ending_overrun_boundary_summary_kr.md` | `runtime stack`, `handoff scorecard`, `owner workboard`, `execution reporting packet`, `artifact archive packet` | B-4는 ending packet 하나만 보면 안 되고 boundary packet을 반드시 같이 본다 |

### 단계별 공통 해석
- `baseline 문서`는 지금 코드가 어디에 서 있는지 확인하는 용도다.
- `source handoff 문서`는 실제 구현/검수/인계 기준이다.
- `readable companion`은 빠른 공유와 맥락 정리에 쓰되, 단독 계약서가 아니다.
- `운영 보조 문서`는 실행 discipline과 제출 구조를 맞추는 문서다.

---

## 3. 역할별 handoff 포인트

## 3-1. 프로그래머에게 넘길 것

### 3줄 요약
- 프로그래머는 포털에서 가장 짧은 summary를 먼저 읽을 수는 있지만, 실제 착수는 항상 source packet 확인 뒤에만 해야 한다.
- 특히 Commit 1/2는 `summary 이해`보다 `surgery sheet + state/boundary packet 확인`이 우선이다.
- B-track도 `runtime stack`만 읽고 들어가면 안 되고, surface packet을 다시 source로 열어야 한다.

**한 줄 목표:** 구현자는 항상 `baseline 1개 + source packet 2~3개 + 운영 문서 1세트`로 들어간다.

| 단계 | 프로그래머가 먼저 열 문서 | 구현 기준 문서 | 구현 전 마지막 확인 |
| --- | --- | --- | --- |
| Commit 1 | `runtime patch map` | `first runtime slice packet`, `commit1 surgery sheet`, `state matrix` | `semantic anchor packet`, `runtime rehearsal packet` |
| Commit 2 | `runtime patch map` | `second runtime slice packet`, `commit2 surgery sheet`, `A-3 UI packet`, `A-4 resolve packet` | `boundary packet`, `visual asset packet`, `runtime rehearsal packet` |
| B-1 | `onboarding runtime stack` | `B-1 library packet`, `screen packet` | `handoff scorecard`, `artifact archive packet` |
| B-2 | `onboarding runtime stack` | `B-2 battle packet`, `content deck` | `handoff scorecard`, `artifact archive packet` |
| B-3 | `onboarding runtime stack` | `B-3 reward/shop packet`, `content deck` | `handoff scorecard`, `artifact archive packet` |
| B-4 | `onboarding runtime stack` | `B-4 ending packet`, `boundary packet`, `content deck` | `handoff scorecard`, `artifact archive packet` |

## 3-2. UI에게 넘길 것

### 3줄 요약
- UI는 summary로 맥락을 잡되, 실제 visual closing은 packet과 boundary/source 문서 기준으로 해야 한다.
- Commit 1은 source packet을 읽어도 **대기 검토** 단계이고, Commit 2부터 shell과 density closing이 열린다.
- B-track은 surface packet이 실제 기준이고, runtime stack은 순서 보조다.

**한 줄 목표:** UI는 summary를 보더라도 visual 계약은 packet에서만 확정한다.

| 단계 | UI quick-read | UI source-of-truth | 금지 |
| --- | --- | --- | --- |
| Commit 1 | `commit1 surgery summary` | `commit1 surgery sheet`, `readiness board` | scene shell 선마감 |
| Commit 2 | `commit2 surgery summary`, `A-3 UI summary` | `commit2 surgery sheet`, `A-3 UI packet`, `boundary packet`, `visual asset packet` | ending/result 패널 재활용으로 장면 밀도 결정 |
| B-1 | `B-1 summary` | `B-1 packet`, `screen packet` | library surface 전체 재설계 |
| B-2 | `B-2 summary` | `B-2 packet`, `screen packet`, `content deck` | battle 힌트 과밀화 |
| B-3 | `B-3 summary` | `B-3 packet`, `content deck` | reward/shop strip 평준화 |
| B-4 | `B-4 summary`, `boundary summary` | `B-4 packet`, `boundary packet`, `content deck` | ending helper에 overrun flavor 혼입 |

## 3-3. 아트에게 넘길 것

### 3줄 요약
- 아트도 summary를 공유받을 수 있지만, token/accent/boundary 판단은 source packet이 기준이다.
- Commit 2와 B-4에서 특히 `boundary packet`과 `visual asset packet`을 source로 다시 열어야 한다.
- 현재 기본값은 신규 대형 자산이 아니라 최소 패키지 sanity다.

**한 줄 목표:** 아트는 source packet으로 boundary를 읽고 summary로만 톤을 복기한다.

| 단계 | source-of-truth | companion | 판정해야 할 것 |
| --- | --- | --- | --- |
| Commit 2 | `visual asset packet`, `A-3 UI packet`, `boundary packet` | 각 summary | scene-card와 ending-helper의 token/density 분리 |
| B-1/B-2/B-3 | `visual asset packet`, 각 B packet, content deck | 각 B summary | badge/accent/token drift 없는 최소 패키지 |
| B-4 | `visual asset packet`, `B-4 packet`, `boundary packet` | `B-4 summary`, `boundary summary` | 결과 helper가 장면 자산처럼 읽히지 않는지 |

## 3-4. QA에게 넘길 것

### 3줄 요약
- QA는 summary를 검수 기준으로 사용하면 안 된다.
- 실제 acceptance, smoke, artifact 구조, 기록 문장은 packet/ledger/manifest/archive 문서가 기준이다.
- `summary에서 이해`와 `packet으로 pass/fail 판정`을 분리해야 한다.

**한 줄 목표:** QA는 설명 문서와 판정 문서를 다르게 쓴다.

| 영역 | 이해용 문서 | 판정용 문서 |
| --- | --- | --- |
| Commit 1/2 | 각 summary | `fixture ledger`, `review packet`, `scorecard`, `evidence manifest`, `artifact archive packet` |
| B-1~B-4 | 각 B summary | `acceptance matrix`, 각 B packet, `handoff scorecard`, `execution reporting packet`, `artifact archive packet` |

## 3-5. PM/기획에게 넘길 것

### 3줄 요약
- PM/기획은 summary를 포털/노션 공유본으로 쓰되, 단계 판단과 기록 문장은 source packet/scorecard/reporting packet 기준으로 고정해야 한다.
- 특히 `summary가 있으니 거의 끝났다`는 인상을 기록 문장에 실으면 안 된다.
- source/summary 경계는 곧 handoff-ready/runtime-ready 경계이기도 하다.

**한 줄 목표:** PM은 읽기용 공유 문서와 실제 기준 문서를 다른 층위로 다룬다.

---

## 4. source vs summary 규칙

## 3줄 요약
- 문서 이름에 `summary`가 붙으면 기본값은 readable companion이다.
- `packet`, `sheet`, `matrix`, `ledger`, `workorder`, `scorecard`, `workboard`는 기본값이 source-of-truth다.
- 다만 `current build reverse spec` / `code map`은 source이긴 하지만 **현재 baseline 설명**이지 target cutover 계약은 아니다.

### 한 줄 목표
문서 이름만 보고도 읽기 위계를 오해하지 않게 만든다.

| 문서 유형 | 기본 역할 | 단독으로 구현 착수 가능? | 비고 |
| --- | --- | --- | --- |
| `*_summary_kr.md` | quick-read / 공유 / portal browsing | 아니오 | source packet과 짝으로 봐야 한다 |
| `*_packet_kr.md` | handoff / 착수 / 컷오버 기준 | 예 | 가장 우선되는 계약 문서 |
| `*_sheet_kr.md` | line/semantic surgery 기준 | 예 | 구현 단계 anchor 문서 |
| `*_matrix_kr.md` | acceptance / state / responsibility 기준 | 예 | 검수 기준 문서 |
| `*_ledger_kr.md` | gap / fixture / evidence 정렬 | 예 | 운영 기준 문서 |
| `*_workorder_kr.md` | 구현 순서와 단계 정의 | 예 | source contract |
| `current_build_*` | 현재 baseline 설명 | 아니오 | target packet과 반드시 분리해 읽는다 |

### 포털 정렬 규칙
1. source packet과 summary는 가능하면 같은 구역에 함께 노출한다.
2. source가 없는 summary처럼 보이는 배치는 피한다.
3. 새 문서 추가 시 포털에는 **source + companion**을 함께 올린다.
4. tracker/run log에는 `source packet 기준으로 정리했고 summary도 동기화했다`는 식으로 역할을 분리해 적는다.

---

## 5. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| summary가 source packet보다 먼저 소비돼 quick-read 문서가 계약서처럼 읽히는 위험 | half-cutover, visual drift, acceptance 누락이 다시 생김 | 문서 유형별 역할과 단계별 source stack을 표로 분리해 고정 | 다음 구현 착수 전, 포털 링크 점검 시 |
| `current build` 설명 문서와 `target packet`이 다시 섞이는 위험 | baseline과 target을 혼동해 잘못된 단계에서 구현 시작 | 단계별로 baseline/source/companion/운영 보조를 분리 명시 | Commit 1/2 착수 직전 |
| UI/아트/PM이 summary만 읽고 closing 판단을 내리는 위험 | boundary/density/token 계약이 문장 수준으로만 소비됨 | 역할별로 반드시 다시 열어야 할 source packet을 명시 | Commit 2, B-4 착수 전 |
| 포털에서 source+summary 쌍이 안 보이면 문서 위계가 다시 흐려지는 위험 | 읽기 순서가 다시 사람마다 달라짐 | 새 문서 추가 시 source/summary 동시 노출 원칙을 명시 | 다음 포털 동기화 시 |

---

## 6. 즉시 다음 액션

1. 다음 구현자는 `readiness board -> 이 document stack packet -> 해당 단계 source packet 묶음` 순서로 읽고, summary만 읽은 상태에서 착수하지 않는다.
2. 포털에는 새 source packet과 readable companion을 같은 영역에 함께 노출한다.
3. Commit 1/2와 B-1~B-4의 run log/tracker 기록에는 `source packet 기준`, `summary 동기화 완료`를 역할 분리 문장으로 남긴다.
4. UI/아트/PM handoff 때는 summary 링크만 보내지 말고, 반드시 paired source packet을 같이 보낸다.
5. 이후 새 문서가 추가되면 먼저 이 스택 문서에 owner별 읽기 위치를 반영한 뒤 포털을 갱신한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_runtime_rehearsal_packet_kr.md`
- `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
