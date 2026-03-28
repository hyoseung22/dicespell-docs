# DiceSpell `overrunEvent` 첫 런타임 전달 패킷

## 3줄 요약
- 이 문서는 `first runtime slice` 구현을 실제로 시작하는 사람에게 필요한 문서를 **한 번 더 실행 묶음으로 압축한 source handoff**다.
- 기존 문서가 부족해서가 아니라, 충분한 문서가 오히려 `이번 턴에 뭘 열고 어디서 멈출지`를 다시 분산시키는 병목이 됐기 때문에 만든다.
- 목표는 프로그래머/UI/아트/QA가 `A-1 enter 분리 + A-2 canonical snapshot floor + review evidence bundle`을 **한 커밋 묶음**으로 같은 문장으로 넘기게 만드는 것이다.

## 한 줄 목표
`overrunEvent` 첫 실제 코드 턴을 **읽기 순서 + 파일 경계 + 증거 묶음 + handoff 순서**까지 한 장으로 고정해, 첫 커밋이 다시 half-cutover나 범위 팽창으로 흐르지 않게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell에는 이미 아래 문서가 있다.

- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_first_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`
- `docs/dicespell_overrun_a2_snapshot_packet_kr.md`

문제는 구현 직전에도 아래 혼선이 남는다는 점이다.

1. 프로그래머는 `읽기 순서`와 `실제 파일 수정 순서`를 다시 머릿속에서 재조합해야 한다.
2. QA는 smoke를 봐도 `이번 커밋에서 꼭 같이 확인해야 하는 비변경선`을 다른 문서로 다시 넘겨야 한다.
3. UI/아트는 `이번 턴은 아직 참여 대기인지`, `payload freeze 확인까지만 하면 되는지`를 실행 단위로 보기 어렵다.
4. PM/기획은 `그래서 이번 턴이 실제로 넘길 수 있는 handoff packet은 무엇인가`를 한 문장으로 요약하기 어렵다.

즉 지금 필요한 것은 새 설계가 아니라, **첫 구현 턴의 전달 묶음 자체를 한 장으로 닫는 일**이다.

---

## 1. 이 문서를 누가 먼저 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 읽을 문서 | 이번 턴 목표 |
| --- | --- | --- | --- |
| 프로그래머 | `2. 이번 전달 범위`, `3. 파일별 전달 묶음`, `4. 구현 순서` | `dicespell_overrun_runtime_patch_map_kr.md`, `dicespell_overrun_first_runtime_slice_packet_kr.md` | enter + canonical snapshot + recovery smoke를 한 커밋으로 닫기 |
| QA | `5. 증거 묶음`, `6. 리뷰 통과선` | `dicespell_overrun_first_runtime_state_matrix_kr.md`, `dicespell_overrun_first_runtime_review_packet_kr.md` | smoke green + 비변경선 유지 확인 |
| UI | `7. UI handoff` | `dicespell_overrun_a3_ui_packet_kr.md` | 아직 대기. payload freeze 확인만 수행 |
| 아트 | `8. 아트 handoff` | `dicespell_overrun_event_screen_packet_kr.md` | 아직 대기. `artTokenSet` freeze만 확인 |
| PM/기획 | `9. 한 줄 handoff 문장`, `10. 즉시 다음 액션` | `dicespell_overrun_cutover_runway_summary_kr.md` | 이번 턴이 어디까지 닫혔는지 과장 없이 기록 |

---

## 2. 이번 전달 범위

## 3줄 요약
- 이번 전달 묶음은 `A-1 enter 분리`와 `A-2 canonical snapshot floor`까지만 다룬다.
- A-3 UI와 A-4 resolve는 **다음 전달 묶음**이다.
- 종료선은 `phase='overrunEvent'` 진입, canonical payload 저장, recovery smoke, review evidence bundle 완료다.

## 한 줄 목표
이번 턴을 `실행 가능한 첫 커밋 + 검수 가능한 첫 리뷰`로만 닫는다.

| 범주 | 이번 전달 묶음에 포함 | 이번 전달 묶음에서 제외 |
| --- | --- | --- |
| 엔진 | `continueOverrun()` enter wrapper화, fallback snapshot, canonical snapshot builder, normalize helper | reward 적용, progression finalize, `resolved` 최종 guard |
| 앱 | `phase='overrunEvent'` 최소 explicit branch, snapshot 전달선 유지 | 중앙 카드 위계, shell/tone, CTA polish |
| 스타일 | 원칙적 미수정 | badge/header/flavor layout, spacing polish |
| 검증 | enter smoke, canonical field set smoke, legacy/missing/invalid recovery smoke, review evidence bundle | full visual sanity, confirm resolve smoke |

---

## 3. 파일별 전달 묶음

## 한 줄 목표
누가 어떤 파일을 같은 커밋에 묶어야 하는지 다시 흔들리지 않게 만든다.

| 파일 | 이번 턴 필수 변경 | 넘기면 안 되는 변경 | handoff 메모 |
| --- | --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `continueOverrun()` enter 전용화, canonical snapshot helper, normalize helper | reward apply, `currentPage += 1`, `pagePlan` 확장, `segmentTrophyTemplateIds` 초기화 | 이번 턴의 중심 파일 |
| `dice-spell-standalone/src/app.js` | `phase='overrunEvent'` 최소 branch, snapshot 전달선 | A-3 중앙 카드 위계, 최종 CTA wiring | placeholder 허용, polish 금지 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | enter/canonical/recovery smoke | resolve smoke, full visual smoke | QA 종료선과 같은 커밋에 묶음 |
| `dice-spell-standalone/src/styles.css` | 없음 | 어떤 시각 polish도 금지 | A-3 전용 파일로 남김 |

### 권장 커밋 경계
- **한 커밋**: `game-engine.js` + `app.js` 최소 branch + `smoke-test.mjs`
- **한 줄 커밋 의미**: `overrunEvent first runtime slice: enter + canonical snapshot + recovery`
- **이 커밋에 섞지 말 것**: styles, reward resolve, A-3 layout, onboarding primer

---

## 4. 실제 구현 순서

## 한 줄 목표
읽기 순서와 수정 순서를 일치시켜, 구현 도중 문서 점프를 줄인다.

1. `docs/dicespell_overrun_runtime_patch_map_kr.md`로 현재 live anchor를 확인한다.
2. `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`로 이번 턴 범위를 다시 선언한다.
3. `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`를 켜 두고 비변경선을 확인하며 엔진을 수정한다.
4. `game-engine.js`에서 `continueOverrun()`를 enter wrapper로 줄인다.
5. canonical snapshot builder / normalize helper를 붙인다.
6. `app.js`에 최소 explicit branch만 추가한다.
7. `smoke-test.mjs`에 enter/canonical/recovery smoke를 같은 턴에 고정한다.
8. `docs/dicespell_overrun_first_runtime_review_packet_kr.md` 기준으로 evidence bundle을 정리한다.

### 구현 중 자기 점검 질문
- 지금 reward가 실제로 적용됐는가? → **되면 안 된다**
- `currentPage`나 `pagePlan`이 움직였는가? → **되면 안 된다**
- UI가 `contentKey` 대신 title/sourceType를 다시 읽는가? → **되면 안 된다**
- smoke가 legacy/missing/invalid를 모두 다루는가? → **반드시 그래야 한다**

---

## 5. 증거 묶음

## 3줄 요약
- 이번 턴은 코드만 있으면 닫힌 게 아니다.
- `코드 diff + 상태 diff + smoke + 한 줄 결론`이 같이 있어야 handoff-ready다.
- 이 네 가지가 하나의 전달 묶음이다.

## 한 줄 목표
리뷰와 다음 handoff가 같은 evidence bundle을 보게 만든다.

| 증거 | 필수 내용 | owner |
| --- | --- | --- |
| 코드 diff | `continueOverrun()` enter화, canonical helper, normalize helper, minimal branch | 프로그래머 |
| 상태 diff 메모 | `phase='overrunEvent'`, canonical field set 존재, reward/progression 미실행 | 프로그래머 + QA |
| smoke 결과 | enter / canonical / legacy / missing / invalid / none | QA |
| 한 줄 결론 | `이번 커밋은 enter+snapshot까지만 닫았다` | PM/기획 |

### 권장 첨부 순서
1. smoke 결과
2. state diff 메모
3. 코드 diff 요약
4. 한 줄 결론

---

## 6. 리뷰 통과선

## 한 줄 목표
이번 전달 묶음이 pass인지 fail인지를 누구나 같은 문장으로 말하게 만든다.

### pass 조건
- `phase='overrunEvent'`가 실제로 생긴다.
- canonical field set이 저장된다.
- legacy/missing/invalid state가 normalize를 거쳐 `none` 또는 유효 snapshot으로 수렴한다.
- reward apply / `currentPage` 증가 / `pagePlan` 확장 / `segmentTrophyTemplateIds` 초기화가 아직 없다.
- A-3 shell/A-4 resolve diff가 아직 없다.

### fail 조건
- smoke가 green이어도 progression 값이 움직였다.
- render가 badge/accent/header를 다시 추론한다.
- `resolved`가 enter 시점에 이미 true가 된다.
- `styles.css`나 중앙 카드 위계가 같이 들어왔다.
- recovery가 happy path만 있고 legacy/missing/invalid가 없다.

---

## 7. UI handoff

## 한 줄 목표
UI는 이번 턴에 구현을 시작하지 않고, **다음 턴 시작 조건만 확인**한다.

### 이번 턴에 UI가 확인할 것
- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `flavorText`
- `confirmLabel`

### 이번 턴에 UI가 하지 않을 것
- 중앙 카드 위계 구현
- shell/tone class 확정
- `none` branch visual density 조정
- CTA polish

### 다음 턴 진입 조건
위 7개 필드가 엔진에서 직접 저장되고, render가 재추론하지 않는다는 것이 리뷰에서 확인될 때만 A-3 UI packet을 연다.

---

## 8. 아트 handoff

## 한 줄 목표
아트는 이번 턴에 asset을 만들지 않고, **token freeze 확인만** 한다.

### 이번 턴에 아트가 확인할 것
- `artTokenSet`이 canonical payload에 포함되는가
- `none`이 비어 있는 placeholder가 아니라 정상 branch인가
- ending과 overrunEvent의 flavor 소유권이 아직 섞이지 않았는가

### 이번 턴에 아트가 하지 않을 것
- backplate 제작
- badge 시안 확정
- `none` 전용 neutral card 시안 제작

### 다음 턴 진입 조건
A-3 packet이 열릴 때 `artTokenSet`이 이미 freeze됐다는 리뷰 결론이 남아 있을 것.

---

## 9. 한 줄 handoff 문장

## 한 줄 목표
tracker / run log / 리뷰 코멘트가 같은 결론 문장을 쓰게 만든다.

> 이번 턴은 `ending -> overrunEvent` 진입과 canonical snapshot 저장/복구까지만 닫았고, reward 적용·page progression·중앙 카드 UI polish·confirm resolve는 아직 열지 않았다.

### 왜 이 문장이 중요한가
- 과장 없이 현재 범위를 말한다.
- A-3/A-4를 섞지 않는다.
- 다음 implementer가 바로 다음 packet을 고를 수 있다.

---

## 10. 즉시 다음 액션

1. 프로그래머는 이번 문서 기준으로 first runtime slice 커밋을 실제로 닫는다.
2. QA는 smoke 결과와 state diff를 같은 리뷰 코멘트에 묶는다.
3. tracker와 automation run log에는 위 한 줄 handoff 문장을 그대로 남긴다.
4. pass가 나면 다음 턴은 **A-3 UI 또는 A-4 resolve 중 하나만** 연다.
5. fail이면 새 문서를 추가하지 말고 first runtime slice 범위로 되돌린다.

---

## 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| 문서가 충분한데도 구현자가 다시 여러 packet을 동시 개봉하는 위험 | 첫 커밋 범위 팽창 | 첫 전달 묶음을 별도 packet으로 압축 | 다음 구현 슬롯 시작 전 |
| smoke는 green인데 handoff 문장이 과장되는 위험 | tracker/run log 드리프트 | 한 줄 handoff 문장을 고정 | 리뷰 직후 |
| UI/아트가 payload freeze 전 시각 작업을 시작하는 위험 | branch drift | 이번 턴은 참여 대기 기준만 명시 | A-3 착수 직전 |
| fail 시 또 다른 문서 추가로 회피하는 위험 | 실행 지연 | 되돌릴 범위를 first runtime slice로 제한 | fail 판정 즉시 |

---

## 한 줄 결론
이 문서는 `overrunEvent` 첫 실제 코드 턴을 **문서 묶음이 아니라 전달 묶음**으로 다시 압축해, 프로그래머/UI/아트/QA/PM이 같은 첫 커밋과 같은 첫 리뷰 결론을 공유하게 만드는 source handoff다.
