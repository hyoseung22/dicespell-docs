# DiceSpell `overrunEvent` 런타임 핸드오프 스코어카드

## 3줄 요약
- 이 문서는 `overrunEvent` A-track을 실제로 닫을 때 구현자와 리뷰어가 같은 완료선으로 움직이도록 만드는 **fill-in-ready source handoff**다.
- 지금 DiceSpell에는 slice/review/delivery/commit stack 문서가 이미 충분하지만, 실제 턴에서는 여전히 `그래서 이번 커밋에 무엇을 붙이고 어떤 증거를 남기면 끝인가`를 다시 조합할 위험이 남아 있다.
- 목표는 Commit 1과 Commit 2를 각각 **작업 체크리스트 + 증거 체크리스트 + tracker/run log 문장**까지 한 장으로 고정해, half-cutover와 과장된 완료 보고를 함께 막는 것이다.

## 한 줄 목표
`Commit 1 = enter/snapshot floor`, `Commit 2 = scene/resolve closing`을 **복붙 가능한 실행/검수 스코어카드**로 다시 압축해, 프로그래머/UI/아트/QA/PM이 같은 완료 문장과 같은 증거 묶음을 남기게 만든다.

## 왜 이 문서가 필요한가
지금 기준 문서는 이미 충분하다.

- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`

문제는 문서가 없어서가 아니다. 실제 구현 턴에서는 아래가 다시 흔들리기 쉽다.

1. 구현자는 범위를 알아도 **이번 커밋의 체크 완료선을 어디에 긋는지** 다시 머릿속에서 조합하게 된다.
2. 리뷰어는 review packet을 읽어도 **어떤 증거를 먼저 보고 어떤 문장을 tracker/run log에 남겨야 하는지** 다시 요약해야 한다.
3. UI/아트는 장면 카드가 보여도 **이번 턴에 정말 필요한 shell sanity만 확인하는지**, 아니면 polish 욕심으로 범위를 넓히는지 경계가 흐려질 수 있다.
4. PM/기획은 `green` 상태를 봐도 **Commit 1 green인지, Commit 2 green인지, onboarding을 열어도 되는지**를 짧은 문장으로 분리하기 어렵다.

즉 지금 필요한 것은 새 설계가 아니라, 이미 닫힌 문서를 **실행 체크리스트와 handoff 체크리스트**로 한 번 더 압축하는 일이다.

---

## 이 문서는 누가 무엇부터 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 쓸 부분 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. Commit 1 스코어카드`, `2. Commit 2 스코어카드` | `5. 복붙용 기록 문장` | 이번 턴 범위와 증거 묶음을 문서 여러 장 없이 바로 실행한다 |
| QA | `3. 공통 검수 체크`, `4. 제출 묶음` | `5. 복붙용 기록 문장` | green 여부를 same bundle로 남긴다 |
| UI | `2. Commit 2 스코어카드` | `3. 공통 검수 체크` | shell/tone만 보고 범위를 부풀리지 않는다 |
| 아트 | `2. Commit 2 스코어카드` | `4. 제출 묶음` | theme/enemy/none 3축 drift만 확인한다 |
| PM/기획 | `0. first screen 결론`, `5. 복붙용 기록 문장`, `7. 즉시 다음 액션` | `6. 리스크 & 후속과제` | Commit 1과 Commit 2를 다른 완료선으로 기록한다 |

---

## 0. first screen 결론

### 이 문서를 왜 열어야 하나
- **프로그래머**: 이번 커밋에서 실제로 바꿔도 되는 파일/함수/테스트 범위를 바로 확인하려고 연다.
- **UI/아트**: 지금은 장면 카드 sanity만 보는 턴인지, 아직 polish를 하면 안 되는 턴인지 확인하려고 연다.
- **QA/PM**: 이번 턴이 `중간 상태 green`인지 `runtime-ready green`인지 다른 문장으로 기록하려고 연다.

### 한 줄 판정 규칙
- `Commit 1`은 **들어가기 + 저장/복구 바닥**이 닫히면 끝이다.
- `Commit 2`는 **장면 카드 + confirm resolve + old baseline 제거**가 닫혀야 끝이다.
- `Commit 2`가 green이 되기 전에는 onboarding B-track을 열지 않는다.

---

## 1. Commit 1 스코어카드 — enter / snapshot floor

## 3줄 요약
- Commit 1은 `continueOverrun()`를 즉시 적용 함수가 아니라 `overrunEvent` 진입 wrapper로 바꾸는 턴이다.
- 이 단계에서는 canonical snapshot을 저장하고 recovery smoke를 green으로 만들지만, reward apply와 page advance는 일부러 열지 않는다.
- 즉 Commit 1의 성공 기준은 `무엇이 바뀌었는가`보다 `무엇이 아직 안 바뀌었는가`까지 함께 증명하는 것이다.

### 한 줄 목표
`phase='overrunEvent'` 진입 + canonical snapshot floor + recovery smoke만 닫고, 적용/정리/온보딩은 의도적으로 열지 않는다.

### 프로그래머 작업 체크

| 체크 | 기대값 | 파일 / surface |
| --- | --- | --- |
| C1-1 | `continueOverrun()`가 즉시 보상 적용 대신 phase 진입 wrapper로 좁혀진다 | `src/game-engine.js` |
| C1-2 | canonical field set이 snapshot에 직접 저장된다 | `src/game-engine.js` |
| C1-3 | `legacy` / `invalid` / `missing` 상태가 `none` fallback 또는 safe recovery로 수렴한다 | `src/game-engine.js` |
| C1-4 | 앱은 `phase='overrunEvent'` 분기 진입만 최소한으로 인식한다 | `src/app.js` |
| C1-5 | smoke가 enter/recovery/non-change line을 검수한다 | `scripts/smoke-test.mjs` |

### canonical field set 체크
아래 필드는 Commit 1 snapshot에 직접 들어 있어야 한다.

- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `flavorText`
- `confirmLabel`
- `resolved`

### Commit 1에서 일부러 하지 말 것

| 금지 | 이유 |
| --- | --- |
| reward apply | 중간 상태와 최종 상태가 다시 섞인다 |
| `currentPage` 전진 | non-change line이 깨진다 |
| `pagePlan` 변경 | half-cutover가 통과해 보인다 |
| `segmentTrophyTemplateIds` cleanup | resolve 시점 책임이 무너진다 |
| onboarding helper/state 추가 | closing 범위가 부풀고 원인 추적이 어려워진다 |

### Commit 1 제출 증거

| 증거 | 최소 내용 |
| --- | --- |
| smoke 결과 | enter/recovery/non-change line green |
| 상태 diff 메모 | `phase='overrunEvent'` 생성, reward 미적용, `currentPage`/`pagePlan`/`segmentTrophyTemplateIds` 미변경 |
| 코드 diff 메모 | enter wrapper + canonical snapshot floor + normalize/recovery |
| 한 줄 handoff | `이번 커밋은 enter+snapshot floor까지만 닫았다.` |

### Commit 1 fail 신호
- enter 직후 reward가 이미 적용된다.
- render가 `title`/`sourceType` 문자열로 branch를 다시 추론한다.
- reload 뒤 snapshot 표정이 바뀐다.
- smoke가 green이어도 `currentPage`/`pagePlan`/`segmentTrophyTemplateIds`가 바뀌어 있다.

---

## 2. Commit 2 스코어카드 — scene / resolve closing

## 3줄 요약
- Commit 2는 Commit 1 snapshot을 display-only로 소비해 `장면 카드 + confirm resolve`를 닫는 턴이다.
- 이 단계에서만 reward 1회 적용, `resolved` reload guard, `none` 무보상 진행, old immediate baseline 제거가 열린다.
- 즉 Commit 2는 `보인다`가 아니라 `정확히 한 번 넘어간다`를 증명해야 green이다.

### 한 줄 목표
`renderOverrunEvent(summary)` + `confirm-overrun-event` + `resolveOverrunEvent(state)`를 한 closing commit으로 닫는다.

### 프로그래머 / UI / 아트 작업 체크

| 체크 | 기대값 | 파일 / surface |
| --- | --- | --- |
| C2-1 | `renderOverrunEvent(summary)`가 `badge -> header -> flavor -> reward summary -> CTA -> footnote` 위계를 렌더한다 | `src/app.js` |
| C2-2 | `themeTrophy` / `enemyTrophy` / `none` 3축이 모두 정상 장면 branch로 읽힌다 | `src/app.js`, `src/styles.css` |
| C2-3 | confirm CTA에서만 reward가 정확히 1회 적용된다 | `src/game-engine.js`, `src/app.js` |
| C2-4 | `resolved` guard가 reload/reclick 중복 적용을 막는다 | `src/game-engine.js` |
| C2-5 | invalid/missing/broken state가 safe recovery 또는 `none`으로 살아남는다 | `src/game-engine.js`, `scripts/smoke-test.mjs` |
| C2-6 | ending과 다른 shell/tone/focus card가 유지된다 | `src/styles.css`, `src/app.js` |
| C2-7 | old immediate baseline assertion이 제거되고 새 smoke로 대체된다 | `scripts/smoke-test.mjs` |

### Commit 2에서 일부러 하지 말 것

| 금지 | 이유 |
| --- | --- |
| onboarding primer 주입 | B-track이 closing slice에 섞인다 |
| 새 reward 타입 추가 | resolve 검수선이 흐려진다 |
| 전역 카드 시스템 리디자인 | UI sanity와 디자인 리뉴얼이 섞인다 |
| unrelated refactor | review 범위가 커져 fail 원인 추적이 어려워진다 |

### Commit 2 제출 증거

| 증거 | 최소 내용 |
| --- | --- |
| smoke 결과 | A3/A4 sanity + resolve/reload/recovery + old baseline 제거 |
| confirm 전/후 상태 diff | confirm 전 `resolved=false`, confirm 후 reward 1회 적용 + cleanup + 진행 가능 상태 |
| UI evidence | theme/enemy/none 3축 카드 위계, ending 대비 shell 차이, CTA/footnote |
| 코드 diff 메모 | scene card + confirm resolve + baseline 제거가 같은 커밋 경계 |
| 한 줄 handoff | `이번 커밋은 scene+resolve closing까지 닫았다.` |

### Commit 2 fail 신호
- confirm 전에 reward가 적용된다.
- confirm 후 reload/reclick로 reward가 다시 적용된다.
- `none` branch가 placeholder처럼 비어 있다.
- ending과 `overrunEvent`가 다시 같은 결과표처럼 읽힌다.
- old immediate baseline이 코드나 smoke 어느 한쪽에라도 남아 있다.

---

## 3. 공통 검수 체크

## 한 줄 목표
모든 역할이 `좋아 보인다`가 아니라 **같은 checkline**으로 pass/fail을 말하게 만든다.

| 공통 체크 | Commit 1 기대값 | Commit 2 기대값 |
| --- | --- | --- |
| 범위 고정 | enter/snapshot/recovery까지만 | scene/resolve/baseline 제거까지만 |
| 상태 ownership | snapshot이 canonical field set을 직접 든다 | render는 snapshot을 display-only로 소비한다 |
| non-change line | reward/page/cleanup 미변경 | 이제 confirm 시점에만 정확히 1회 바뀐다 |
| recovery | legacy/invalid/missing -> safe recovery | invalid/missing/reload/resolved -> safe recovery/no-op |
| 기록 문장 | 중간 상태 green | runtime-ready green |
| 다음 단계 허용 | Commit 2만 연다 | onboarding B-1부터만 연다 |

### 역할별 빠른 handoff
- **프로그래머**: Commit 1은 저장/복구 바닥, Commit 2는 장면/적용 마감이다.
- **UI**: Commit 1에서는 shell polish를 하지 않고, Commit 2에서만 card readability를 닫는다.
- **아트**: Commit 2에서도 새 대형 자산보다 `accentKey`/`artTokenSet` drift 여부가 우선이다.
- **QA**: Commit 1과 Commit 2의 green 문장을 같은 코멘트로 쓰지 않는다.
- **PM/기획**: Commit 2 pass 전에는 onboarding 착수 보고를 쓰지 않는다.

---

## 4. 제출 묶음 순서

## 3줄 요약
- 구현이 끝나도 제출 묶음이 흐리면 tracker/run log가 다시 과장되기 쉽다.
- 따라서 증거는 항상 같은 순서로 남긴다.
- 이 순서를 지키면 review packet과 commit stack packet을 실제 업무 기록으로 바로 옮길 수 있다.

### 권장 제출 순서
1. **smoke 결과**
2. **상태 diff 메모**
3. **UI evidence 또는 DOM evidence**
4. **코드 diff 요약**
5. **한 줄 handoff 문장**

### tracker / run log에 남길 때 필수 요소

| 항목 | Commit 1 | Commit 2 |
| --- | --- | --- |
| 작업 주제 | `overrunEvent` first runtime floor | `overrunEvent` runtime closing |
| 변경 요약 | enter 분리 + canonical snapshot floor + recovery smoke | scene card + confirm resolve + baseline 제거 |
| 검증 | non-change line 포함 recovery smoke | UI/resolve/recovery/full smoke |
| 다음 추천 | Commit 2만 연다 | onboarding B-1만 연다 |

---

## 5. 복붙용 기록 문장

### Commit 1용 tracker / run log 문장
> 이번 커밋은 `overrunEvent`의 enter 분리와 canonical snapshot floor만 닫았고, reward apply와 page advance는 아직 열지 않았다.

### Commit 2용 tracker / run log 문장
> 이번 커밋은 `overrunEvent`의 scene card와 confirm resolve를 닫아, reward 1회 적용·`none` 무보상 진행·`resolved` reload guard·old immediate baseline 제거까지 runtime closing을 마무리했다.

### 리뷰 코멘트 템플릿
```md
- 확인 범위: Commit 1 floor 또는 Commit 2 closing 중 하나만 검수
- 확인 증거: smoke 결과, 상태 diff, UI/DOM evidence, 코드 diff, handoff 문장
- 판정: Commit 1 green이면 Commit 2만 열 수 있고, Commit 2 green이면 onboarding B-1만 열 수 있다
```

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| Commit 1과 Commit 2 기록 문장이 다시 섞이는 위험 | 중간 상태가 runtime-ready처럼 보일 수 있음 | 복붙용 기록 문장과 제출 묶음 순서를 별도 고정 | 첫 실제 코드 커밋 기록 시 |
| review는 통과했는데 증거 제출 순서가 제각각인 위험 | 후속 implementer가 same bundle을 재사용하지 못함 | 5단 제출 순서와 역할별 필수 요소를 고정 | 첫 QA/PM handoff 시 |
| UI/아트가 Commit 2에서 polish 범위를 넓히는 위험 | closing slice가 다시 커지고 smoke 원인 추적이 어려워짐 | shell/tone/drift sanity까지만 허용한다고 명시 | Commit 2 착수 전 |
| Commit 2 green 전 onboarding이 열리는 위험 | A-track과 B-track 원인 추적이 섞임 | Commit 2 pass 후 B-1만 허용한다고 명시 | 다음 writer 슬롯 시작 전 |

---

## 7. 즉시 다음 액션

1. implementer는 `runway -> runtime patch map -> first runtime delivery packet -> second runtime slice packet -> second runtime review packet -> runtime commit stack packet -> 이 문서` 순서로 범위를 재확인한다.
2. 실제 코드 턴을 열면 먼저 이 문서의 Commit 1 체크와 제출 묶음만 닫는다.
3. Commit 1 green 문장이 tracker/run log에 남은 뒤에만 Commit 2 체크와 제출 묶음을 연다.
4. Commit 2 green 문장이 남은 뒤에만 onboarding B-track을 `B-1 -> B-2 -> B-3 -> B-4` 순서로 연다.
5. fail이면 새 문서를 늘리지 말고, 현재 커밋 범위 안에서만 되돌린다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_onboarding_entrypoint_matrix_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
