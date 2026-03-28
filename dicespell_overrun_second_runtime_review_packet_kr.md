# DiceSpell `overrunEvent` 두 번째 런타임 슬라이스 리뷰 패킷

## 3줄 요약
- 이 문서는 `second runtime slice`가 실제로 닫혔다고 말하기 위한 **closing review 기준선**을 한 장으로 고정하는 source handoff다.
- `A-3 장면 카드 UI`와 `A-4 confirm resolve`는 문서상 범위가 이미 좁혀졌지만, 리뷰 기준이 없으면 `보이긴 하는데 아직 runtime-ready는 아닌 상태`가 통과할 수 있다.
- 목표는 프로그래머/QA/UI/아트/PM이 같은 evidence bundle을 보고 `이 커밋으로 overrunEvent runtime closing이 끝났다`를 같은 문장으로 말하게 만드는 것이다.

## 한 줄 목표
`A-3 scene card UI + A-4 confirm resolve + old immediate baseline 제거` 커밋을 **리뷰 가능한 closing 단위**로 다시 고정해, 마지막 컷오버가 half-cutover나 과장된 완료 보고로 흐르지 않게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell에는 이미 아래 문서가 있다.

- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_overrun_a4_resolve_packet_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`

문제는 실구현 직전에도 아래 혼선이 남는다는 점이다.

1. `second runtime slice`는 범위를 좁혀 주지만, 리뷰어가 **정확히 어떤 diff와 어떤 증거를 pass 기준으로 봐야 하는지** 다시 머릿속에서 조합해야 한다.
2. `renderOverrunEvent(summary)`가 보여도 confirm 1회 적용, `resolved` guard, old immediate baseline 제거가 함께 검수되지 않으면 closing commit이 완성처럼 보일 수 있다.
3. UI/아트는 shell/tone이 닫혔는지 볼 수 있어도, **ending과 다른 화면 책임이 실제로 유지됐는지**를 코드/캡처/상태 diff 중 무엇으로 확인해야 하는지 한 장으로 보기 어렵다.
4. PM/기획은 `좋아, 이제 overrunEvent는 runtime-ready다`를 tracker/run log에서 써도 되는지, 아니면 아직 Commit 2가 덜 닫힌 상태인지 짧게 판정할 문장이 필요하다.

즉 지금 필요한 것은 새 설계가 아니라, **두 번째 런타임 슬라이스 커밋의 리뷰 패키지와 pass/fail 기준**을 한 장으로 압축하는 일이다.

---

## 누가 어디부터 읽어야 하나

| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | `1. 리뷰 범위`, `2. diff 기대값`, `3. evidence bundle` | `dicespell_overrun_second_runtime_slice_packet_kr.md` |
| QA | `3. evidence bundle`, `4. closing 체크리스트`, `6. fail 조건` | `dicespell_overrun_event_smoke_migration_packet_kr.md` |
| UI | `4. closing 체크리스트`, `5. 역할별 handoff` | `dicespell_overrun_a3_ui_packet_kr.md` |
| 아트 | `4. closing 체크리스트`, `5. 역할별 handoff` | `dicespell_overrun_event_screen_packet_kr.md` |
| PM/기획 | `0. 리뷰 한 줄 결론`, `7. 즉시 다음 액션` | `dicespell_overrun_runtime_commit_stack_packet_kr.md` |

---

## 0. 리뷰 한 줄 결론 템플릿

### 한 줄 목표
리뷰 완료 후 누구나 같은 문장으로 이번 closing commit 상태를 말할 수 있게 한다.

아래 문장을 그대로 써도 된다.

> 이번 커밋은 `overrunEvent`의 단일 장면 카드와 confirm resolve를 함께 닫아, reward 1회 적용·`none` 무보상 진행·`resolved` reload guard·old immediate baseline 제거까지 포함한 runtime closing을 마무리했다.

### 이 문장을 써야 하는 이유
- `무엇이 됐는가`와 `무엇이 이제 진짜 닫혔는가`가 같이 들어간다.
- Commit 1의 `enter+snapshot floor`와 명확히 다른 완료선을 만든다.
- onboarding B-track을 열어도 되는지 아닌지 바로 판단할 수 있다.

---

## 1. 이번 리뷰 범위

## 3줄 요약
- 리뷰 범위는 `second runtime slice packet`과 동일하게 **A-3 장면 카드 + A-4 confirm resolve**까지만 본다.
- shell이 예뻐 보이는지보다 `장면 읽힘 + 1회 적용 + recovery/no-op`가 먼저 pass해야 한다.
- 이 범위를 넘는 diff가 들어오면 좋은 코드여도 이번 커밋 기준으로는 fail이다.

## 한 줄 목표
리뷰어가 `좋아 보이니까 통과`가 아니라 **지금 closing commit에 허용된 범위**를 먼저 확인하게 만든다.

| 범주 | 이번 리뷰에서 pass 대상 | 이번 리뷰에서 fail 대상 |
| --- | --- | --- |
| 엔진 | `resolveOverrunEvent(state)` 또는 동등 함수, reward 1회 적용, `resolved` guard, cleanup, invalid/missing -> `none` fallback | 새 reward 타입, onboarding state, unrelated refactor |
| 앱 | `renderOverrunEvent(summary)`, `confirm-overrun-event`, stale ending/CTA 제거 | primer helper 주입, 다른 surface UI 정리 |
| 스타일 | overrun 전용 shell/tone/focus card/fallback density | 전역 카드 체계/테마 리디자인 |
| 검증 | A3-S1~S5, A4-S4~S12, old immediate baseline 제거 증거 | unrelated smoke 대량 추가, 새 시뮬레이션 |

---

## 2. diff 기대값

## 한 줄 목표
리뷰어가 커밋 diff에서 **어떤 파일과 함수가 바뀌는 것이 정상인지** 먼저 잡게 만든다.

| 파일 | 기대 diff | 리뷰 포인트 |
| --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `resolveOverrunEvent(state)` 또는 동등 resolve 함수, reward 1회 적용, `resolved` guard, cleanup | confirm 전 적용이 없는가, reload/reclick 중복 적용이 없는가 |
| `dice-spell-standalone/src/app.js` | `renderCenter()`의 `overrunEvent` 최종 branch, `renderOverrunEvent(summary)`, `confirm-overrun-event` wiring | snapshot field를 display-only로 소비하는가, ending과 다른 화면 책임이 유지되는가 |
| `dice-spell-standalone/src/styles.css` | `.overrun-event-*` shell, focus card, `none` neutral density, ending 대비 tone 차이 | global style rework 없이 closing shell만 닫았는가 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | A3/A4 sanity + resolve/reload/recovery + old baseline 제거 | happy path만이 아니라 `none`/invalid/reload/resolved가 다 들어가는가 |

### 정상적인 작은 diff 패턴
- `renderOverrunEvent(summary)`가 `badge -> header -> flavor -> reward summary -> CTA -> footnote` 위계를 직접 렌더한다.
- confirm action이 `resolveOverrunEvent(state)`를 거친 뒤 다시 `ending`이나 다음 세그먼트 흐름으로 안전하게 돌아간다.
- `resolved`가 confirm 이후에만 켜지고, reload/reclick 시 no-op 또는 safe recovery로 동작한다.
- smoke가 `themeTrophy` / `enemyTrophy` / `none` 3축과 invalid/missing/reload를 함께 다룬다.

### 경계 신호
아래가 보이면 리뷰를 멈추고 범위 초과 여부를 다시 본다.
- onboarding B-1~B-4 helper/state 추가
- 새 보상 타입 또는 새 phase 정의
- `title`/`sourceType` 기반 branch 재추론
- confirm 전 reward apply 또는 enter 시점 cleanup
- 전역 카드 시스템/전역 spacing 리디자인

---

## 3. evidence bundle

## 3줄 요약
- closing slice는 `보인다`만으로 pass가 아니다.
- 증거는 `코드 diff`, `confirm 전/후 상태 diff`, `smoke 결과`, `UI evidence`, `짧은 human summary`가 같이 있어야 한다.
- 하나라도 빠지면 구현은 됐어도 runtime-ready closing이라고 보기 어렵다.

## 한 줄 목표
리뷰가 감상평이 아니라 **같은 closing evidence bundle 확인**으로 끝나게 만든다.

### 필수 evidence 5종

| 증거 | 무엇이 보여야 하나 | 왜 필요한가 |
| --- | --- | --- |
| 코드 diff | A-3 shell + A-4 resolve + baseline 제거가 같은 커밋 경계에 있다 | 범위 초과/누락 확인 |
| 상태 diff 메모 | confirm 전 `resolved=false`, confirm 후 reward 1회 적용 + cleanup + 진행 가능 상태 | 실제 runtime closing 확인 |
| smoke 결과 | A3-S1~S5, A4-S4~S12, old immediate baseline 제거 | recovery/no-op 포함 여부 확인 |
| UI 캡처/DOM evidence | theme/enemy/none 3축 카드 위계, ending 대비 shell 차이, CTA/footnote 존재 | visual responsibility 확인 |
| 한 줄 summary | `이번 커밋으로 scene+resolve closing을 닫았다` | PM/기획/후속 implementer handoff용 |

### 권장 리뷰 코멘트 템플릿

```md
- 확인 범위: A-3 장면 카드 + A-4 confirm resolve + old immediate baseline 제거
- 확인 결과: 3축 UI 위계, confirm 1회 적용, `resolved` guard, invalid/missing/reload recovery, closing smoke 확인
- 다음에 열 수 있는 범위: onboarding B-1 library packet부터 순차 착수 가능
```

---

## 4. closing 체크리스트

## 한 줄 목표
리뷰어가 `바뀐 것`만 보지 말고 **runtime closing이 실제로 끝났는지**를 체크하게 만든다.

| 항목 | 이번 리뷰에서 기대값 | 어긋나면 왜 fail인가 |
| --- | --- | --- |
| 장면 카드 위계 | `badge -> header -> flavor -> reward summary -> CTA -> footnote` 유지 | `overrunEvent`가 결과표/보상 선택 화면처럼 읽힌다 |
| 3축 branch | `themeTrophy` / `enemyTrophy` / `none` 모두 정상 장면으로 렌더 | fallback 신뢰와 UX 밀도가 무너진다 |
| reward 적용 시점 | confirm CTA에서만 1회 적용 | enter/렌더/reload와 resolve가 다시 섞인다 |
| `resolved` guard | confirm 후 reload/reclick에서 중복 적용 방지 | duplicate reward 또는 drift가 남는다 |
| invalid/missing recovery | `none` fallback 또는 safe no-op로 런 생존 | broken save/load에서 진행이 막힌다 |
| ending 경계 | ending은 결과/정산, overrunEvent는 장면/CTA 의미로 읽힘 | 경계 문서와 runtime이 어긋난다 |
| old immediate baseline | 제거됨 | smoke는 새 기준인데 실제 런타임은 옛 흐름으로 남는다 |
| onboarding 비개입 | B-track helper/state/UI가 이번 커밋에 없다 | closing 범위가 부풀고 검수선이 무너진다 |

### 리뷰어 메모
- 이번 슬라이스는 `장면이 보인다`보다 `정확히 한 번 넘어간다`가 더 중요하다.
- 특히 `resolved` guard와 old immediate baseline 제거는 작은 diff라도 빠지면 이번 커밋 기준으로는 fail이다.

---

## 5. 역할별 handoff 포인트

### 프로그래머에게 넘기는 것
- 이번 커밋은 A-3와 A-4를 한 번에 닫는 마지막 runtime closing이다.
- 리뷰어가 `왜 onboarding이 없지?`라고 묻는 것이 아니라, `onboarding이 없어서 맞다`를 확인해야 한다.
- `runtime commit stack` 문장과 실제 diff가 다르면 코드보다 문장부터 맞춘다.

### UI 오너에게 넘기는 것
- 이번 커밋이 pass이면 `overrunEvent`는 ending과 다른 독립 장면으로 닫힌다.
- 지금 확인할 것: 3축 카드 위계, focus card, `none` density, stale CTA 제거.
- 지금 요구하지 않는 것: primer helper, 다른 surface polish, 전역 카드 리뉴얼.

### 아트 오너에게 넘기는 것
- 이번 리뷰는 asset 대량 제작 턴이 아니라 token/accent drift와 shell tone 확인 턴이다.
- `artTokenSet`이 theme/enemy/none 모두에서 ending과 다른 장면성으로 보이는지 본다.
- neutral `none`도 빈 서사 branch가 아니라 정상 장면 branch로 읽혀야 한다.

### QA에게 넘기는 것
- 이번 리뷰는 visual sanity와 recovery sanity를 같이 본다.
- `none`, invalid, missing, reload, resolved guard까지 없으면 pass로 보지 않는다.
- smoke가 green이어도 confirm 전 적용 또는 reclick 중복 적용이 있으면 fail이다.

### PM/기획에게 넘기는 것
- 이 커밋은 `overrunEvent가 이제 runtime-ready다`를 말할 수 있는 마지막 closing 턴이다.
- pass가 나면 다음 슬롯부터만 onboarding B-track을 `B-1 -> B-2 -> B-3 -> B-4` 순서로 열 수 있다.
- 아직 다른 새 메카닉을 같이 묶지 않았다는 점도 함께 기록한다.

---

## 6. fail 조건

## 한 줄 목표
리뷰 중 `겉으로는 통과 같아 보이는 실패`를 빠르게 걸러낸다.

1. UI가 canonical snapshot field 대신 문자열 재추론으로 badge/accent/header를 다시 만든다.
2. confirm 전 reward가 적용되거나 confirm 후 reload/reclick로 다시 적용된다.
3. `none` branch가 placeholder처럼 비어 있거나 진행 의미가 없다.
4. ending과 `overrunEvent`가 여전히 같은 결과표/보상 화면처럼 읽힌다.
5. old immediate baseline이 코드나 smoke 어느 한쪽에라도 남아 있다.
6. onboarding helper/state/UI가 같이 들어와 closing slice 범위가 부풀었다.
7. smoke가 green이어도 confirm 전/후 상태 diff가 문서 결론과 맞지 않는다.

---

## 7. 즉시 다음 액션

## 3줄 요약
- 이 리뷰 패킷은 새 설계를 여는 문서가 아니라, 마지막 closing commit이 닫혔는지 판단하는 문서다.
- pass가 되면 다음 선택지는 하나뿐이다: onboarding B-track을 B-1부터 연다.
- fail이면 새 문서를 더 쓰지 말고 second runtime slice 범위 안에서만 되돌린다.

## 한 줄 목표
리뷰 결과가 바로 다음 bounded action으로 이어지게 만든다.

### pass일 때
1. tracker/run log에 위 closing 문장을 그대로 기록
2. `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md` 기준으로 B-1 library primer만 연다
3. B-track은 반드시 `B-1 -> B-2 -> B-3 -> B-4` 순서로만 진행한다

### fail일 때
1. confirm 전 적용인지, 중복 적용인지, branch density 문제인지 먼저 분리
2. A-3 shell/A-4 resolve/old baseline 제거 중 어느 증거가 비었는지 확인
3. onboarding이나 다른 기능이 섞였으면 second runtime slice 범위로 되돌린다

---

## 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 후속 확인 |
| --- | --- | --- | --- |
| scene card는 보이는데 resolve review가 약한 위험 | runtime-ready가 아닌데 완료처럼 기록될 수 있음 | closing checklist와 evidence bundle을 별도 표로 고정 | Commit 2 리뷰 시 |
| old immediate baseline 제거가 일부 누락되는 위험 | 중복 적용 / smoke drift | fail 조건에 baseline 잔존을 명시 | smoke 리뷰 시 |
| onboarding이 pass 전 미리 열리는 위험 | 범위 팽창 / 원인 추적 난이도 상승 | pass 후 B-1만 허용한다고 명시 | 다음 writer 슬롯 시작 전 |
| `none` branch가 정상 서사 상태로 닫히지 않는 위험 | recovery 신뢰 저하 | 3축 density와 CTA semantics를 pass 조건에 포함 | 수동 캡처/QA 확인 시 |

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_overrun_a4_resolve_packet_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
