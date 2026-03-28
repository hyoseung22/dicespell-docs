# DiceSpell `overrunEvent` / 첫 런 온보딩 문서 스택 요약

## 3줄 요약
- 이 문서는 DiceSpell의 `overrunEvent` A-track과 온보딩 B-track에서 **어떤 문서가 기준 packet이고 어떤 문서가 읽기용 summary인지**를 빠르게 구분하기 위한 readable companion이다.
- 결론은 단순하다. `summary`는 빠르게 읽는 문서이고, 실제 착수/검수/인계 기준은 `packet`, `sheet`, `matrix`, `ledger`, `workorder`, `scorecard`, `workboard` 쪽에 있다.
- 즉 이제 병목은 새 문서 부족이 아니라, **올바른 문서 묶음을 올바른 순서로 여는 discipline**이다.

## 한 줄 목표
포털 첫 화면만 보고도 `무엇이 기준 문서인지`와 `어느 요약 문서는 참고용인지`를 헷갈리지 않게 만든다.

## 왜 이 문서가 필요한가
문서가 많아질수록 생기는 문제는 늘 비슷하다.

- summary를 읽고 바로 구현하려 한다.
- current baseline 문서와 target packet을 같은 층위로 읽는다.
- UI/아트/PM이 summary만 보고 closing 판단을 내린다.
- 포털에서 source와 summary가 나란히 보이지 않으면 quick-read 문서가 실제 계약서처럼 소비된다.

그래서 지금 필요한 것은 새 설계보다 **문서 읽기 위계의 고정**이다.

---

## first screen 결론

### 지금 꼭 기억할 것
- `*_summary_kr.md` → 읽기용 companion
- `*_packet_kr.md`, `*_sheet_kr.md`, `*_matrix_kr.md`, `*_ledger_kr.md`, `*_workorder_kr.md`, `*_scorecard_kr.md`, `*_workboard_kr.md` → 실제 source-of-truth 후보
- `current_build_*` → 현재 baseline 설명 문서
- `A-track / B-track packet` → 실제 target contract 문서

### 한 줄 규칙
**summary만 읽고 구현/검수/기록 판단을 하지 않는다.**

---

## 단계별로 무엇을 기준으로 읽나

| 단계 | 먼저 확인할 baseline | 실제 기준 packet | summary는 어디까지 쓰나 |
| --- | --- | --- | --- |
| Commit 1 floor | `current build` + `runtime patch map` | `first runtime slice packet`, `commit1 surgery sheet`, `state matrix` | floor 맥락 복기용 |
| Commit 2 closing | `current build` + `runtime patch map` | `second runtime slice packet`, `commit2 surgery sheet`, `A-3 UI/A-4 resolve packet`, `boundary packet` | scene/resolve 맥락 공유용 |
| B-1 library | `current build` + `source_of_truth_map` | `B-1 packet`, `screen packet` | library surface quick-read |
| B-2 battle | `current build` + `source_of_truth_map` | `B-2 packet`, `screen packet`, `content deck` | battle surface quick-read |
| B-3 reward/shop | `current build` + `source_of_truth_map` | `B-3 packet`, `content deck` | reward/shop 목적 차이 quick-read |
| B-4 ending | `current build` + `boundary packet` | `B-4 packet`, `boundary packet`, `content deck` | ending helper 톤 quick-read |

---

## 역할별 handoff 포인트

### 프로그래머
- summary를 먼저 읽어도 되지만, 실제 착수는 source packet 재확인 뒤에만 한다.
- Commit 1/2는 `surgery sheet`를 안 읽고 들어가면 안 된다.
- B-track은 `runtime stack`만으로 구현하지 말고 surface packet을 다시 열어야 한다.

### UI
- Commit 1은 대기 검토 턴이다.
- Commit 2와 B-track부터 실제 visual closing이 열린다.
- visual 계약은 summary가 아니라 `visual asset packet`, `A-3 UI packet`, 각 B packet, `boundary packet`이 기준이다.

### 아트
- 대형 신규 자산보다 token/accent/boundary sanity가 우선이다.
- Commit 2와 B-4는 `boundary packet`과 `visual asset packet`을 source로 다시 열어야 한다.

### QA
- pass/fail 기준은 summary가 아니라 `fixture ledger`, `acceptance matrix`, `evidence manifest`, `artifact archive packet`이다.
- 설명용 문서와 판정용 문서를 반드시 분리한다.

### PM/기획
- summary는 공유용, packet은 결정용이다.
- tracker/run log에는 `source packet 기준`, `summary 동기화`를 분리해서 적는다.

---

## source vs summary 규칙

| 문서 유형 | 역할 | 단독 착수 기준 여부 |
| --- | --- | --- |
| `summary` | 빠른 읽기 / 브라우징 | 아니오 |
| `packet` | handoff / 착수 기준 | 예 |
| `sheet` | 수술 / anchor 기준 | 예 |
| `matrix` | acceptance / state 기준 | 예 |
| `ledger` | gap / evidence / fixture 기준 | 예 |
| `workorder` | 구현 순서 기준 | 예 |
| `scorecard` / `workboard` | 오너별 실행 기준 | 예 |
| `current_build_*` | baseline 설명 | 아니오 |

### 포털 정렬 원칙
- source와 summary는 함께 보여 준다.
- source 없는 summary처럼 보이는 배치는 피한다.
- 새 문서를 올릴 때는 `packet + summary`를 같이 싣는다.

---

## 리스크 & 후속과제

- summary가 source packet보다 먼저 소비되면 quick-read 문서가 계약서처럼 읽힐 수 있다.
- `current build` 설명 문서와 `target packet`이 다시 섞이면 baseline/target 혼동이 생긴다.
- UI/아트/PM이 summary만 읽고 closing 판단을 내리면 boundary와 density 계약이 흐려진다.
- 포털에서 source+summary 쌍이 안 보이면 사람마다 읽기 순서가 달라진다.

---

## 즉시 다음 액션

1. 다음 구현자는 `readiness board -> document stack packet -> 해당 단계 source packet` 순서로 문서를 연다.
2. 포털에는 source packet과 summary를 같은 구역에 함께 노출한다.
3. handoff 때는 summary 링크만 보내지 말고 paired source packet도 같이 보낸다.
4. tracker/run log에는 `source packet 기준`과 `summary 동기화`를 역할 분리 문장으로 남긴다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_document_stack_packet_kr.md`
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_runtime_rehearsal_packet_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
