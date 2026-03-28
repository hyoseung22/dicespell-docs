# DiceSpell `overrunEvent` / 첫 런 온보딩 진입점 매트릭스 요약

- 이 문서는 `docs/dicespell_overrun_onboarding_entrypoint_matrix_kr.md`의 읽기용 companion이다.
- 목적은 새 문서를 더 설명하는 게 아니라, **이번 턴에 누가 어떤 문서부터 열어야 하는지**를 3~5분 안에 잡게 만드는 것이다.
- 핵심은 단순하다. A-track은 `runway → patch map → step packet`, B-track은 `B-1 → B-2 → B-3 → B-4 packet` 순서만 허용한다.

## 한 줄 목표

문서가 충분한 상태에서 **잘못된 첫 진입점 때문에 범위가 다시 부푸는 일**을 막는다.

## 왜 중요한가

지금 DiceSpell의 병목은 더 이상 `문서가 없다`가 아니다.
병목은 오히려 다음이다.

- summary만 읽고 구현을 시작해 packet의 종료선을 놓침
- current baseline 문서와 target packet을 같은 층위로 읽음
- 온보딩 B-4가 포털에서 약하게 보여 ending도 commit-level anchor를 갖췄다는 사실이 묻힘
- A-track이 안 닫혔는데 B-track을 같이 열어 버림

즉 이번 문서의 역할은 새 설계가 아니라 **문서 진입점 정렬**이다.

---

## 빠른 읽기 표

| 지금 하려는 일 | 첫 문서 | 바로 같이 볼 것 | 금지 |
| --- | --- | --- | --- |
| A-1 시작 | `dicespell_overrun_cutover_runway_packet_kr.md` | `dicespell_overrun_runtime_patch_map_kr.md`, `dicespell_overrun_a1_engine_cutover_packet_kr.md` | B-track 동시 착수 |
| A-2 시작 | `dicespell_overrun_a2_snapshot_packet_kr.md` | `dicespell_overrun_onboarding_contract_examples_kr.md`, `dicespell_overrun_event_content_deck_kr.md` | render가 branch 재판정 |
| A-3 시작 | `dicespell_overrun_a3_ui_packet_kr.md` | `dicespell_ending_overrun_boundary_packet_kr.md` | A-4 resolve 규칙 선반영 |
| A-4 시작 | `dicespell_overrun_a4_resolve_packet_kr.md` | `dicespell_overrun_event_smoke_migration_packet_kr.md` | 온보딩 구현 착수 |
| B-1 시작 | `dicespell_first_run_onboarding_b1_library_packet_kr.md` | content deck, acceptance | battle/reward 동시 묶기 |
| B-2 시작 | `dicespell_first_run_onboarding_b2_battle_packet_kr.md` | workorder, contract examples | reward/shop 선착수 |
| B-3 시작 | `dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md` | screen packet, content deck | ending helper 섞기 |
| B-4 시작 | `dicespell_first_run_onboarding_b4_ending_packet_kr.md` | ending-overrun boundary, content deck | flavor를 ending이 먹기 |

---

## 역할별 첫 문서

### 프로그래머
- A-track: `runway → patch map → 해당 A-step packet`
- B-track: `해당 B-step packet`
- summary는 읽기 보조이지 구현 시작점이 아니다.

### UI
- 항상 `summary → packet` 순서
- 특히 B-4는 `ending은 결과/버튼 의미만, flavor는 overrunEvent`를 먼저 확인

### 아트
- `summary`로 밀도/톤 먼저 확인
- 그 다음 `screen packet` 또는 해당 `packet`으로 들어간다

### QA
- step packet의 acceptance/recovery를 이번 턴 종료선으로 잡는다
- A-4는 `smoke migration packet`을 꼭 같이 본다

---

## 이번 턴 포털 정렬 포인트

1. 온보딩 카드에 `B-4 ending summary/packet`을 같은 위계로 노출한다.
2. `entrypoint matrix`와 이 summary를 포털에서 바로 열 수 있게 둔다.
3. 오버런 섹션에도 같은 링크를 연결해 A-track/B-track 분기 기준을 한 화면에서 찾게 한다.

---

## 리스크 & 후속과제

- 리스크: summary에서 바로 구현 시작
  - 대응: 첫 문서를 packet/runway로 고정
- 리스크: B-4가 포털에서 약하게 보임
  - 대응: index에서 B-4와 entrypoint guide를 같은 층위로 노출
- 리스크: A-track과 B-track 동시 착수
  - 대응: A-4 완료 전 B-track 금지 규칙 재강조

---

## 즉시 다음 액션

- A-track은 여전히 `runway → patch map → step packet` 순서로만 시작
- B-track은 A-4가 닫힌 뒤 `B-1 → B-2 → B-3 → B-4` 순서로만 시작
- tracker / blueprint / portal / run log는 이번 문서를 `문서 진입점 정렬` 작업으로 기록
