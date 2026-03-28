# DiceSpell `overrunEvent` 런타임 오너 워크보드

## 3줄 요약
- 이 문서는 `overrunEvent` A-track을 실제로 닫을 때 **프로그래머 / UI / 아트 / QA / PM이 이번 턴에 무엇을 넘기고 무엇은 아직 넘기지 말아야 하는지**를 역할별 작업 보드로 다시 압축한 source handoff다.
- 현재 DiceSpell에는 slice/review/delivery/commit stack/scorecard 문서가 이미 충분하지만, 실제 착수 직전에는 여전히 `좋아, 그래서 이번 커밋에서 각 오너가 실제로 뭘 제출하면 되는가`가 문서 사이에 흩어져 보일 수 있다.
- 목표는 Commit 1과 Commit 2를 역할별 산출물·입력 의존성·검수 증거·handoff 문장까지 한 장으로 고정해, 구현 착수와 리뷰 착수를 같은 오너 언어로 맞추는 것이다.

## 한 줄 목표
`Commit 1 = enter/snapshot floor`, `Commit 2 = scene/resolve closing`을 **오너별 작업 묶음**으로 재배열해, 프로그래머/UI/아트/QA/PM이 같은 순서로 handoff를 주고받게 만든다.

## 왜 이 문서가 필요한가
지금 기준 `overrunEvent` 문서 축은 이미 충분하다.

- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`

문제는 문서 부족이 아니라, 실제 착수 직전의 **오너별 전달 형태**가 다시 퍼질 수 있다는 점이다.

1. 프로그래머는 범위를 알아도 `이번 턴에 UI/아트/QA에게 무엇을 넘기면 되는지`를 다시 조합하게 된다.
2. UI는 장면 카드 위계를 알아도 `Commit 1에서는 아직 무엇을 기다려야 하는지`가 흐려지면 너무 일찍 shell을 확정할 수 있다.
3. 아트는 `accentKey` / `artTokenSet` 계약을 알아도 `이번 턴 제출물`이 새 자산인지, 토큰 매칭 검수인지 헷갈릴 수 있다.
4. QA/PM은 smoke와 상태 diff를 알아도 `이번 커밋이 어느 오너 handoff까지 닫힌 것인지`를 짧게 기록하기 어렵다.

즉 지금 필요한 것은 새 설계가 아니라, 이미 닫힌 문서를 **오너별 workboard와 handoff queue**로 다시 압축하는 일이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. Commit 1 오너 워크보드`, `2. Commit 2 오너 워크보드` | `runtime patch map`, `first runtime delivery`, `second runtime slice` | 이번 턴 코드 범위와 오너 handoff 시점을 동시에 고정한다 |
| UI | `2. Commit 2 오너 워크보드`, `4. surface handoff 규칙` | `a3_ui_packet`, `ending_overrun_boundary_packet` | 장면 카드가 열리는 정확한 턴과 금지 범위를 확인한다 |
| 아트 | `2. Commit 2 오너 워크보드`, `4. surface handoff 규칙` | `event_content_deck`, `event_screen_packet` | 새 자산 추가보다 token/accent drift 검수가 우선임을 확인한다 |
| QA | `1. Commit 1 오너 워크보드`, `2. Commit 2 오너 워크보드`, `5. evidence bundle` | `first runtime review`, `second runtime review`, `smoke migration` | 커밋별 pass/fail을 같은 owner bundle로 기록한다 |
| PM/기획 | `0. first screen 결론`, `6. 리스크 & 후속과제`, `7. 즉시 다음 액션` | `runtime handoff scorecard`, `runtime commit stack` | Commit 1과 Commit 2의 handoff 완료선을 다른 문장으로 남긴다 |

---

## 0. first screen 결론

### 이 문서를 왜 열어야 하나
- **프로그래머**: 이번 커밋이 실제로 누구에게 어떤 입력/산출물을 넘겨야 끝나는지 바로 확인하려고 연다.
- **UI/아트**: 지금 턴이 아직 기다리는 턴인지, 장면 카드 sanity를 닫는 턴인지 확인하려고 연다.
- **QA/PM**: 이번 커밋이 `엔진 floor handoff`인지 `runtime closing handoff`인지 다른 문장으로 기록하려고 연다.

### 한 줄 판정 규칙
- Commit 1은 **엔진/상태/복구 바닥이 닫혀 다음 오너가 같은 snapshot 언어를 볼 수 있게 되면** 끝이다.
- Commit 2는 **장면 카드/confirm resolve/old baseline 제거가 닫혀 모든 오너가 runtime-ready handoff를 남길 수 있으면** 끝이다.
- Commit 2가 green이 되기 전에는 onboarding B-track을 열지 않는다.

---

## 1. Commit 1 오너 워크보드 — enter / snapshot floor

## 3줄 요약
- Commit 1은 `continueOverrun()`를 `overrunEvent` 진입 wrapper로 좁히고, canonical snapshot floor와 recovery를 닫는 엔진 중심 턴이다.
- 이 단계에서 UI/아트는 **장면 카드를 확정하는 것이 아니라**, 다음 턴에 쓸 snapshot 언어와 non-change line을 확인하는 대기/검수 역할이다.
- 즉 Commit 1의 성공 기준은 `보여 준다`가 아니라 `같은 상태를 같은 필드로 전달할 수 있다`다.

### 한 줄 목표
`phase='overrunEvent'` 진입 + canonical snapshot floor + recovery smoke를 닫아, Commit 2의 UI/resolve 오너가 같은 payload를 보게 만든다.

### Commit 1 오너별 작업 표

| 역할 | 이번 턴 입력 | 이번 턴 산출물 | 이번 턴 금지 | handoff 문장 |
| --- | --- | --- | --- | --- |
| 프로그래머 | `continueOverrun()` current baseline, state matrix, first runtime delivery packet | enter wrapper, canonical field set, normalize/recovery, non-change line 증거 | reward apply, page advance, cleanup, onboarding helper | `엔진은 overrunEvent floor까지만 넘겼다.` |
| UI | state matrix, content deck의 `contentKey`/`badgeLabel`/`accentKey` | Commit 2에서 쓸 위계 메모, `none` branch density 주의점 확인 | shell 확정, CTA 배치 완성, ending copy 선반영 | `UI는 이번 턴에 payload 표정만 동결했다.` |
| 아트 | content deck, event screen packet | `artTokenSet` / accent drift 체크 메모 | 새 대형 자산 제작, 마감급 scene polish | `아트는 token 계약만 잠갔다.` |
| QA | first runtime review packet, smoke migration packet | enter/recovery/non-change smoke 판정, fail 조건 기록 | Commit 2 closing 기준으로 pass 선언 | `QA는 floor green만 기록했다.` |
| PM/기획 | runtime handoff scorecard, commit stack packet | Commit 1 완료 문장, 다음은 Commit 2만 허용한다는 기록 | runtime-ready처럼 과장된 완료 보고 | `이번 턴은 중간 상태 green이다.` |

### Commit 1 프로그래머 제출 체크
- `dice-spell-standalone/src/game-engine.js`
  - `continueOverrun()`가 즉시 적용 함수가 아니라 진입 wrapper 또는 동등 의미로 축소된다.
  - canonical field set이 엔진 snapshot에 직접 저장된다.
  - invalid / missing / legacy 입력이 `none` fallback 또는 안전한 recovery로 수렴한다.
- `dice-spell-standalone/src/app.js`
  - `phase='overrunEvent'`를 최소한 죽지 않게 처리한다.
  - render가 branch를 문자열로 재추론하지 않는다.
- `dice-spell-standalone/scripts/smoke-test.mjs`
  - enter / recovery / non-change line smoke가 green이다.

### Commit 1 UI / 아트 체크
- UI는 아래 문장만 확인한다.
  - `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet`이 render 재추론 없이 직접 내려온다.
  - `none` branch는 placeholder가 아니라 neutral scene으로 읽혀야 한다.
  - ending은 아직 결과/정산/버튼 의미만 맡고, flavor는 Commit 2 이후 `overrunEvent`로 넘겨진다.
- 아트는 아래만 체크한다.
  - slime/goblin/lich/golem/collapse/none branch를 토큰 레벨에서 다시 분류하지 않아도 된다.
  - 새 asset 요구가 없어도 Commit 1 handoff는 성립한다.

### Commit 1 QA / PM 제출 묶음
1. smoke 결과
2. 상태 diff 메모 (`phase='overrunEvent'`, reward 미적용, `currentPage`/`pagePlan`/`segmentTrophyTemplateIds` 미변경)
3. render 재추론 부재 메모
4. handoff 문장

### Commit 1 완료 문장
> 이번 커밋은 `overrunEvent`의 enter 분리와 canonical snapshot floor만 닫아, 다음 오너가 같은 payload 언어로 Commit 2를 시작할 수 있게 만들었다.

---

## 2. Commit 2 오너 워크보드 — scene / resolve closing

## 3줄 요약
- Commit 2는 Commit 1 snapshot을 display-only로 소비해 `장면 카드 + confirm resolve + old baseline 제거`를 닫는 closing 턴이다.
- 이 단계에서만 UI/아트가 실제 장면 밀도를 닫고, 엔진은 reward 1회 적용과 `resolved` guard를 닫고, QA/PM은 runtime-ready handoff를 남긴다.
- 즉 Commit 2의 성공 기준은 `보여 준다`가 아니라 `모든 오너가 같은 장면과 같은 적용 시점을 공유한다`다.

### 한 줄 목표
`renderOverrunEvent(summary)` + `confirm-overrun-event` + `resolveOverrunEvent(state)`를 오너별 closing handoff로 마무리한다.

### Commit 2 오너별 작업 표

| 역할 | 이번 턴 입력 | 이번 턴 산출물 | 이번 턴 금지 | handoff 문장 |
| --- | --- | --- | --- | --- |
| 프로그래머 | Commit 1 payload, second runtime slice/review packet | scene render wiring, confirm resolve, `resolved` guard, old baseline 제거 | onboarding primer, 새 reward 타입, unrelated refactor | `엔진/앱은 runtime closing을 넘겼다.` |
| UI | A3 UI packet, ending-overrun boundary packet, content deck | badge→header→flavor→reward summary→CTA→footnote 위계, ending과 다른 shell/tone | 전역 카드 시스템 리뉴얼 | `UI는 장면 카드 위계를 닫았다.` |
| 아트 | content deck, event screen packet, UI shell draft | `themeTrophy`/`enemyTrophy`/`none` 3축 tone sanity, token/accent drift check | 새 대형 삽화 범위 확장 | `아트는 3축 표정을 runtime-ready로 맞췄다.` |
| QA | second runtime review packet, smoke migration packet | full smoke, confirm 전/후 diff, reload/reclick no-op 판정 | Commit 1 수준 증거만으로 pass 선언 | `QA는 runtime closing green을 기록했다.` |
| PM/기획 | scorecard, commit stack, boundary packet | Commit 2 완료 문장, 다음은 B-1만 허용한다는 기록 | A-track과 B-track 동시 오픈 | `이번 턴은 runtime-ready green이다.` |

### Commit 2 프로그래머 제출 체크
- `dice-spell-standalone/src/app.js`
  - `renderOverrunEvent(summary)` 또는 동등 surface가 독립 분기로 렌더된다.
  - `confirm-overrun-event`가 ending CTA와 다른 의미의 action으로 배치된다.
- `dice-spell-standalone/src/styles.css`
  - ending과 다른 shell / focus / spacing / tone이 적용된다.
  - `none` branch도 빈 화면이 아니라 정상 density로 읽힌다.
- `dice-spell-standalone/src/game-engine.js`
  - confirm 시점에만 reward가 정확히 1회 적용된다.
  - invalid / missing / resolved reload가 safe recovery 또는 no-op로 수렴한다.
- `dice-spell-standalone/scripts/smoke-test.mjs`
  - old immediate baseline이 제거되고, A3/A4 + recovery 기준으로 검증이 이동한다.

### Commit 2 UI / 아트 체크
- UI는 아래를 닫는다.
  - 장면 카드 위계: `badge -> header -> flavor -> reward summary -> CTA -> footnote`
  - ending과 `overrunEvent`가 서로 다른 shell로 읽힌다.
  - `none` branch는 neutral density를 유지한다.
- 아트는 아래를 닫는다.
  - `accentKey` / `artTokenSet`이 render 결과와 drift 없이 일치한다.
  - `themeTrophy` / `enemyTrophy` / `none` 3축이 같은 카드 시스템 안에서도 다른 표정으로 읽힌다.
  - 새 대형 자산 없이도 런타임 closing 검수는 가능하다.

### Commit 2 QA / PM 제출 묶음
1. full smoke 결과
2. confirm 전/후 상태 diff
3. UI evidence 또는 DOM evidence
4. old baseline 제거 메모
5. handoff 문장

### Commit 2 완료 문장
> 이번 커밋은 `overrunEvent`의 scene card와 confirm resolve를 닫아, 프로그래머/UI/아트/QA/PM이 같은 장면·같은 적용 시점·같은 closing 문장으로 runtime-ready handoff를 남길 수 있게 만들었다.

---

## 3. 오너 간 handoff 큐

## 한 줄 목표
이번 턴에 **누가 누구에게 무엇을 넘겨야 하는지**를 한 줄 순서로 고정한다.

### Commit 1 handoff 큐
1. 프로그래머 → QA: enter/recovery/non-change smoke와 상태 diff 전달
2. 프로그래머 → UI/아트: canonical field set 예시와 `none` fallback 표정 전달
3. QA → PM: `floor green` 또는 fail 조건 전달
4. PM → 전체: `Commit 2만 연다`는 문장 고정

### Commit 2 handoff 큐
1. 프로그래머 → UI: `renderOverrunEvent(summary)` payload/slot 전달
2. UI ↔ 아트: 3축 shell/tone/token sanity 확정
3. 프로그래머 → QA: confirm resolve / reload guard / old baseline 제거 증거 전달
4. QA → PM: `runtime-ready green` 또는 fail 조건 전달
5. PM → 전체: `다음은 onboarding B-1만 연다`는 문장 고정

---

## 4. surface handoff 규칙

## 3줄 요약
- `ending`, `overrunEvent`, `onboarding`은 모두 관련 있지만 같은 층위가 아니다.
- Commit 1은 payload ownership만, Commit 2는 scene ownership만 닫아야 한다.
- 오너별 handoff가 섞이면 가장 먼저 ending/overrun 경계와 `none` branch가 흔들린다.

### 한 줄 목표
오너가 각자 다른 목적의 surface를 같은 턴에 섞지 않게 만든다.

| surface | Commit 1 owner focus | Commit 2 owner focus | 끝까지 금지 |
| --- | --- | --- | --- |
| ending | 결과/정산/버튼 의미 유지 확인 | overrun flavor를 빨아들이지 않게 trim | ending에 scene flavor를 다시 넣는 것 |
| overrunEvent | payload identity만 잠금 | scene card + confirm resolve closing | reward 선택 화면처럼 재설계 |
| onboarding | 아직 열지 않음 | 아직 열지 않음 | Commit 2 안에 primer를 섞는 것 |
| none branch | neutral payload 확인 | neutral scene density 확인 | placeholder / 오류 카드처럼 처리 |

---

## 5. evidence bundle

## 한 줄 목표
오너별 증거를 따로 따로 남기지 않고 **같은 제출 묶음**으로 정렬한다.

| 커밋 | 오너 | 필수 증거 |
| --- | --- | --- |
| Commit 1 | 프로그래머 | enter wrapper diff, canonical field set, normalize/recovery 코드 메모 |
| Commit 1 | UI/아트 | `none` 포함 payload identity 확인 메모 |
| Commit 1 | QA | enter/recovery/non-change smoke 결과 |
| Commit 1 | PM | `floor green` 기록 문장 |
| Commit 2 | 프로그래머 | confirm resolve diff, `resolved` guard, old baseline 제거 메모 |
| Commit 2 | UI | shell/tone hierarchy evidence |
| Commit 2 | 아트 | 3축 token/accent drift check |
| Commit 2 | QA | full smoke + reload/reclick no-op 확인 |
| Commit 2 | PM | `runtime-ready green` 기록 문장 |

### 제출 순서
1. smoke 결과
2. 상태 diff 메모
3. UI/아트 evidence
4. 코드 diff 요약
5. handoff 문장

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| 오너별 산출물 경계가 흐려져 Commit 1에서 UI/아트가 너무 일찍 마감하는 위험 | 중간 상태와 closing 상태가 다시 섞임 | Commit 1/2를 오너별 입력/산출물 기준으로 분리 | 첫 실제 코드 턴 착수 전 |
| Commit 2에서 프로그래머/UI/아트 evidence가 따로 놀 위험 | runtime-ready handoff가 owner마다 다른 의미가 됨 | handoff 큐와 공통 evidence bundle 고정 | Commit 2 리뷰 시 |
| PM이 `green`만 보고 floor/closing을 같은 문장으로 기록하는 위험 | half-cutover가 완성처럼 보임 | Commit 1/2 완료 문장 분리 | tracker/run log 기록 시 |
| A-track closing 전에 onboarding을 여는 위험 | 원인 추적과 smoke 해석이 섞임 | Commit 2 이후 B-1만 허용한다고 재명시 | 다음 writer 슬롯 시작 전 |

---

## 7. 즉시 다음 액션

1. implementer는 `runtime patch map -> first runtime delivery packet -> runtime commit stack -> runtime handoff scorecard -> 이 문서` 순서로 Commit 1 오너 handoff를 먼저 확인한다.
2. Commit 1에서는 프로그래머/QA/PM handoff만 닫고, UI/아트는 payload identity 검수까지만 한다.
3. Commit 1 green 문장이 남은 뒤에만 `second runtime slice -> second runtime review -> 이 문서` 순서로 Commit 2 오너 handoff를 연다.
4. Commit 2 green 문장이 남은 뒤에만 onboarding B-track을 `B-1 -> B-2 -> B-3 -> B-4` 순서로 연다.
5. fail이면 새 방향 문서를 추가하지 말고, 현재 커밋의 오너 제출 묶음 안에서만 되돌린다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- `docs/dicespell_overrun_event_content_deck_kr.md`
