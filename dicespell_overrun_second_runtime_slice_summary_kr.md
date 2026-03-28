# DiceSpell `overrunEvent` 두 번째 런타임 슬라이스 요약

## 3줄 요약
- 이 문서는 `first runtime slice` 다음에 실제로 남는 마지막 구현 턴을 **A-3 장면 카드 + A-4 confirm resolve** 한 묶음으로 요약한 readable companion이다.
- 첫 슬라이스가 `들어가기/저장/복구 바닥`을 닫았다면, 이 슬라이스는 `보여 주기/실제로 넘기기`를 닫는다.
- 즉 `overrunEvent`를 문서-ready 상태에서 runtime-ready 상태로 마무리하는 마지막 closing guide다.

## 한 줄 목표
프로그래머/UI/아트/QA/PM이 `이제 남은 건 overrunEvent를 실제로 끝내는 마지막 bounded commit이다`를 같은 순서와 같은 결론 문장으로 읽게 만든다.

## 왜 이 문서를 먼저 보나
기존 문서만으로도 A-3 UI packet과 A-4 resolve packet은 충분하다. 그런데 실제 직전엔 오히려 이런 공백이 남는다.

- 이번 턴을 한 commit으로 묶을까, 두 commit으로 나눌까?
- UI shell과 confirm resolve를 어디까지 같이 검수해야 하지?
- old immediate baseline 제거까지 이번 턴에 닫아야 하나?
- `none` branch와 reload guard는 누가 같은 evidence bundle로 확인하지?

이 문서는 그 공백을 `마지막 closing slice`라는 한 문장으로 정리한다.

---

## 이번 슬라이스에서 닫는 것

### 1) 장면 카드 읽힘
- `overrunEvent`는 결과표가 아니라 **다음 권으로 넘어가기 직전 장면 카드**로 읽혀야 한다.
- `badge → header → flavor → reward summary → CTA → footnote` 6요소가 theme/enemy/none 3축에서 모두 유지돼야 한다.
- ending과는 같은 shell을 쓰지 않는다.

### 2) confirm 1회 적용
- reward는 confirm CTA에서만 적용된다.
- `none`은 무보상 진행이지만 정상 진행이다.
- invalid/missing/broken state는 `none` fallback 또는 safe recovery로 수렴해야 한다.
- reload/reclick로 reward가 두 번 적용되면 실패다.

### 3) full smoke migration
- A3-S1~S5와 A4-S4~S12가 closing smoke 세트가 된다.
- old immediate baseline은 이번 슬라이스에서 제거돼야 한다.
- 즉 이번 턴이 끝나면 `ending에서 이미 적용됐다`는 가정은 더 이상 기준선이 아니다.

---

## 누가 무엇을 보면 되나

| 역할 | 이번 문서에서 먼저 볼 부분 | 바로 이어서 읽을 문서 | 이번 턴에 확인할 것 |
| --- | --- | --- | --- |
| 프로그래머 | `이번 슬라이스에서 닫는 것`, `실제 구현 순서` | `dicespell_overrun_a3_ui_packet_kr.md`, `dicespell_overrun_a4_resolve_packet_kr.md` | shell + resolve + cleanup + smoke를 한 commit으로 묶기 |
| UI | `장면 카드 읽힘`, `리뷰 통과선` | `dicespell_ending_overrun_boundary_packet_kr.md` | ending과 다른 집중도, `none` 포함 3축 density 확인 |
| 아트 | `장면 카드 읽힘`, `리스크` | `dicespell_overrun_event_screen_packet_kr.md` | token/tone drift 없이 closing visual 확인 |
| QA | `full smoke migration`, `evidence bundle` | `dicespell_overrun_event_smoke_migration_packet_kr.md` | A3/A4 smoke + baseline 제거 증거 확인 |
| PM/기획 | `한 줄 closing 결론` | `dicespell_overrun_onboarding_entrypoint_matrix_summary_kr.md` | 이제 onboarding B-track을 열 수 있는지 판단 |

---

## 실제 구현 순서

1. `renderOverrunEvent(summary)`와 `styles.css`의 overrun shell을 먼저 닫는다.
2. theme/enemy/none 3축 카드 위계가 같은 밀도로 읽히는지 확인한다.
3. `confirm-overrun-event`를 `resolveOverrunEvent(state)` 또는 동등 함수에 연결한다.
4. reward 1회 적용, `none` 무보상 진행, invalid fallback, `resolved` guard, cleanup을 한 번에 닫는다.
5. A3-S1~S5 + A4-S4~S12를 green으로 만들고 old immediate baseline을 제거한다.
6. 아래 한 줄 closing 결론으로 tracker/run log를 정리한다.

---

## 리뷰 통과선

### pass
- `overrunEvent`가 결과표가 아니라 경계 장면 카드로 읽힌다.
- confirm CTA에서만 reward가 정확히 한 번 적용된다.
- `none`/invalid/missing/reload/resolved까지 런이 살아 있다.
- old immediate baseline이 제거됐다.
- onboarding 변경이 같이 섞이지 않았다.

### fail
- UI가 snapshot field를 다시 추론한다.
- confirm 전 적용 또는 confirm 후 중복 적용이 있다.
- `none`이 비거나 CTA semantics가 깨진다.
- old immediate baseline이 여전히 smoke 기준선으로 남아 있다.
- 범위가 커져 onboarding이나 다른 phase 수정이 섞인다.

---

## evidence bundle

- **UI evidence**: theme/enemy/none 3축 캡처 또는 동등 DOM evidence
- **state diff memo**: confirm 전/후 reward 1회 적용, cleanup, guard 동작
- **smoke evidence**: A3-S1~S5, A4-S4~S12, old immediate baseline 제거
- **closing sentence**: 아래 한 줄 결론

---

## 한 줄 closing 결론
> 이번 커밋으로 `overrunEvent`의 중앙 장면 카드와 confirm resolve를 닫아, reward 1회 적용·`none` 무보상 진행·`resolved` reload guard·old immediate baseline 제거까지 포함한 runtime closing slice를 마무리했다.

---

## 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 |
| --- | --- | --- |
| A-3와 A-4가 다시 따로 놀 위험 | shell만 있고 resolve가 없거나, resolve만 있고 장면 읽힘이 흐려진다 | 두 번째 런타임 슬라이스를 closing commit으로 고정 |
| old immediate baseline 잔존 위험 | smoke는 green인데 런타임은 중복 적용될 수 있다 | baseline 제거를 evidence bundle에 포함 |
| onboarding 조기 착수 위험 | 마지막 closing 검수선이 흐려진다 | pass 후에만 B-track을 연다고 명시 |
| `none` branch 약화 위험 | fallback 신뢰가 깨진다 | 3축 공통 density와 CTA semantics를 pass 조건에 포함 |

---

## 즉시 다음 액션
- 프로그래머는 A-3 UI packet + A-4 resolve packet을 이 요약의 순서대로 한 commit closing slice로 실행한다.
- QA는 A3/A4 smoke와 baseline 제거 증거를 같은 리뷰 코멘트에 묶는다.
- PM/기획은 위 한 줄 결론으로 tracker/run log를 정리한다.
- pass가 나면 그 다음부터 `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending` 순서로 onboarding 구현을 연다.

## 한 줄 결론
이 문서는 `overrunEvent`를 실제로 끝내는 마지막 구현 턴을 한 장으로 요약해, 모두가 `이제 남은 건 closing slice 하나`라는 사실을 같은 문장으로 보게 만드는 readable companion이다.
