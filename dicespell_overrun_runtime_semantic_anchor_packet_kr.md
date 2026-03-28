# DiceSpell `overrunEvent` 런타임 시맨틱 앵커 패킷

## 3줄 요약
- 이 문서는 `Commit 1 floor`와 `Commit 2 closing` handoff가 이미 line-anchored surgery sheet까지 갖춘 상태에서, **line number drift가 생겨도 같은 절개 지점을 다시 찾게 만드는 시맨틱 locator source-of-truth**다.
- 핵심은 새 설계를 더하는 것이 아니라 `continueOverrun`, `renderCenter`, `renderEnding`, `continue-overrun`, smoke overrun block을 **함수 책임 / grep token / 비변경선 / 오너별 제출물** 기준으로 다시 고정하는 것이다.
- 첫 화면만 읽어도 프로그래머, UI, 아트, QA, PM이 `라인이 밀렸을 때 어디를 다시 찾아야 하는가`, `무엇이 여전히 같은 단계인가`, `무엇을 같은 green 문장으로 기록하면 안 되는가`를 판단할 수 있어야 한다.

## 한 줄 목표
`Commit 1/2` handoff를 **line number 의존 문서**가 아니라 **책임 기반 semantic locator**까지 갖춘 실행 패킷으로 재정렬해, 실제 구현 턴이 line drift 때문에 다시 범위를 키우지 않게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell의 A-track 문서는 충분하다.

이미 닫힌 것:
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_runtime_fixture_ledger_kr.md`
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`

그런데 실제 코드 턴 직전 마지막 작은 병목은 여전히 남아 있다.

1. 수술 시트는 정확한 line anchor를 주지만, 문서 자동화가 계속 도는 환경에서는 라인이 밀릴 수 있다.
2. implementer가 line number가 조금만 어긋나도 다시 소스를 넓게 grep하면서 범위를 재조합할 위험이 있다.
3. reviewer/QA/PM도 `같은 함수 책임을 찾는 것`과 `새 책임을 추가하는 것`을 구분하지 못하면 half-cutover가 다시 큰 green 문장으로 뭉개진다.
4. UI/아트는 line anchor보다 `scene-card`, `result-helper`, `hero-primer`처럼 semantic surface를 읽는데, 현재 handoff 묶음은 엔진/라인 기준이 더 강하다.

즉 지금 필요한 것은 새 설계가 아니라, **line anchor가 조금 밀려도 같은 책임을 다시 찾게 만드는 semantic locator packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. Commit 1/2 semantic locator` | `commit1/commit2 surgery sheet`, `runtime patch map`, `first/second runtime slice` | line이 밀려도 `같은 함수 책임`만 다시 찾고 새 책임은 열지 않는다 |
| UI | `1. first screen 판정`, `3-2. UI semantic handoff` | `A-3 UI packet`, `visual asset packet`, `ending boundary` | `overrunEvent`는 scene shell, ending은 result/helper라는 semantic 경계를 유지한다 |
| 아트 | `3-3. 아트 semantic handoff` | `visual asset packet`, `content deck` | `artTokenSet` / accent / neutral branch sanity를 line이 아니라 surface 책임으로 검수한다 |
| QA | `2. Commit 1/2 semantic locator`, `3-4. QA semantic handoff`, `5. 리스크 & 후속과제` | `fixture ledger`, `execution reporting packet`, `readiness board` | line drift가 있어도 같은 S번호, 같은 non-change line, 같은 stage 문장으로 검수한다 |
| PM/기획 | `1. first screen 판정`, `4. 기록 문장`, `5. 리스크 & 후속과제` | `readiness board`, `runtime commit stack`, `execution reporting packet` | line drift를 이유로 단계명을 넓히지 않는다 |

---

## 1. first screen 판정

### 왜 이 문서를 열어야 하나
- **프로그래머**: 수술 시트의 line number가 조금 밀렸을 때도 `정확히 같은 함수 책임`을 좁혀 찾으려고 연다.
- **UI/아트**: line이 아니라 `scene card / result helper / hero-primer` surface 경계가 여전히 같은지 확인하려고 연다.
- **QA/PM**: line drift와 범위 drift를 같은 문제로 보지 않으려고 연다.

### 가장 짧은 답
- `Commit 1`에서 다시 찾아야 하는 것은 `continueOverrun()`의 **즉시 적용 체인**과 `renderCenter()`의 **missing overrunEvent branch**다.
- `Commit 2`에서 다시 찾아야 하는 것은 `renderCenter()`/`renderEnding()`의 **scene vs result 경계**, `continue-overrun` 이후의 **confirm resolve 경계**, smoke의 **old immediate baseline 제거 지점**이다.
- line이 밀려도 stage는 안 바뀐다. 즉 `Commit 1 floor`는 여전히 floor만, `Commit 2 closing`은 여전히 closing만 닫는다.

### 한 줄 판정 규칙
- line number는 **locator**고, 함수 책임은 **contract**다.
- locator가 흔들려도 contract는 넓히지 않는다.
- 못 찾겠으면 line을 새로 찾되, 단계명은 그대로 유지한다.

---

## 2. Commit 1 / Commit 2 semantic locator

## 3줄 요약
- 아래 표는 현재 live anchor를 `함수/행동/grep token/비변경선` 기준으로 다시 압축한다.
- 목적은 구현자가 `어디지?`를 다시 찾는 동안 `무엇까지 하기로 했지?`를 넓히지 못하게 만드는 것이다.
- 특히 `line number`와 `stage boundary`를 분리해 읽게 하는 것이 핵심이다.

### 한 줄 목표
각 단계를 `몇 번째 줄`이 아니라 **어떤 책임을 가진 코드 덩어리인가**로 다시 찾게 만든다.

| 단계 | semantic zone | 지금 live에서 찾을 grep token | 이번 단계가 해야 하는 일 | 이번 단계가 아직 하면 안 되는 일 |
| --- | --- | --- | --- | --- |
| Commit 1 floor | 엔진 enter floor | `export function continueOverrun`, `selectedOverrunRewardId`, `pagePlan.push`, `maybeEnterNextPage` | immediate progression 제거, canonical snapshot floor 생성, `phase='overrunEvent'` 진입 | reward apply, page advance, segment cleanup, resolve |
| Commit 1 floor | 렌더 route floor | `function renderCenter`, `case \"continue-overrun\"`, `renderEnding(` | `overrunEvent` explicit branch를 넣고 library fall-through를 막음 | scene card polish, confirm CTA resolve |
| Commit 1 floor | smoke floor | `const overrunResult = continueOverrun`, `const overrunDraftResult = continueOverrun`, `segmentTrophyTemplateIds.length !== 0` | old immediate assertion을 floor assertion으로 교체 | closing smoke를 미리 닫기 |
| Commit 2 closing | 엔진 resolve zone | `confirm-overrun-event`(신규/예정), `resolved`, `applyEffectBundle`, `segmentTrophyTemplateIds = []`, `maybeEnterNextPage` | reward 1회 적용, `resolved` guard, page progression closing | onboarding primer, 새 branch 추가 |
| Commit 2 closing | 렌더 scene zone | `function renderCenter`, `function renderEnding`, `selectedRewardId`, `reward-card` | scene card / result helper semantic 분리, `none` branch neutral density 유지 | ending이 overrun flavor까지 먹는 것 |
| Commit 2 closing | smoke closing zone | `overrunResult`, `overrunDraftResult`, `selectedRewardId !==`, `segmentTrophyTemplateIds.length !== 0` | `S4/S5/S6/S10/S12` 중심 closing evidence로 치환 | Commit 1과 Commit 2 evidence를 같은 묶음으로 기록 |

### locator 재탐색 규칙
1. 먼저 함수명/액션명으로 찾는다: `continueOverrun`, `renderCenter`, `renderEnding`, `continue-overrun`.
2. 다음으로 semantic token으로 좁힌다: `pagePlan.push`, `maybeEnterNextPage`, `selectedRewardId`, `segmentTrophyTemplateIds`, `reward-card`.
3. 마지막으로 문서의 line anchor를 대조한다.
4. 찾은 뒤에도 stage boundary가 넓어지면 다시 readiness board로 되돌린다.

### stage별 canonical grep 묶음
- **Commit 1 엔진**: `continueOverrun|selectedOverrunRewardId|pagePlan.push|maybeEnterNextPage|segmentTrophyTemplateIds`
- **Commit 1 렌더**: `renderCenter|continue-overrun|renderEnding\(`
- **Commit 1 smoke**: `overrunResult|overrunDraftResult|segmentTrophyTemplateIds.length`
- **Commit 2 렌더/엔진**: `confirm-overrun-event|resolved|applyEffectBundle|reward-card|renderEnding\(`

---

## 3. 역할별 semantic handoff

## 3-1. 프로그래머에게 넘길 것

### 3줄 요약
- 프로그래머는 이 문서를 `라인이 다르면 새 설계를 해도 된다`는 허가증으로 읽으면 안 된다.
- line drift 상황에서도 여전히 `같은 함수 책임`만 다시 찾아야 한다.
- 즉 locator를 새로 찾는 순간에도 stage boundary는 surgery sheet와 readiness board 그대로다.

**한 줄 목표:** line drift를 `탐색 문제`로만 다루고, `범위 재설계 문제`로 바꾸지 않는다.

| 단계 | 먼저 찾아야 하는 semantic zone | 이번 턴 핵심 판단 | 제출물 |
| --- | --- | --- | --- |
| Commit 1 | `continueOverrun()` immediate chain + `renderCenter()` branch gap + overrun smoke block | `enter-only floor`까지만 닫혔는가 | 코드 diff, floor smoke, semantic locator 메모 1개 |
| Commit 2 | resolve 경계 + scene/result 경계 + old smoke baseline | runtime-ready closing만 닫혔는가 | 코드 diff, closing smoke, scene/result 분리 메모 |

### 프로그래머 체크리스트
1. line이 달라도 함수 책임이 같으면 같은 단계다.
2. `pagePlan.push`와 `maybeEnterNextPage`가 보이면 Commit 1에서는 제거/이동 대상이다.
3. `reward-card`와 `renderEnding`이 보인다고 ending helper 책임까지 넓혀 잡지 않는다.
4. 못 찾은 line은 기록하되, stage를 넓혀 해결하지 않는다.

---

## 3-2. UI에게 넘길 것

### 3줄 요약
- UI는 line number 대신 `scene-card`, `result-helper`, `hero-primer`, `inline-hint` semantic family를 읽는다.
- 따라서 Commit 2 visual review에서 중요한 것은 몇 번째 줄이 아니라 어떤 surface가 무엇을 말해야 하는지다.
- line drift가 있어도 `scene shell != ending helper`만 유지되면 handoff는 살아 있다.

**한 줄 목표:** UI는 line이 아니라 surface responsibility를 기준으로 visual drift를 막는다.

| surface | semantic locator | 유지해야 하는 읽힘 | 금지 |
| --- | --- | --- | --- |
| `overrunEvent` scene | center-column main card, badge/header/flavor/CTA | 장면 카드 1장 | ending 결과표 밀도 재사용 |
| ending helper | ending CTA, 결과/정산 카피 | CTA 의미 보조 | trophy flavor 선탑재 |
| onboarding primer | hero-primer / inline-hint | surface별 guidance | all-in-one 카드 평준화 |

---

## 3-3. 아트에게 넘길 것

### 3줄 요약
- 아트는 line이 아니라 `artTokenSet`, accent, neutral `none` branch를 semantic anchor로 읽어야 한다.
- Commit 1에서는 token identity가 payload에 들어오는지만 보면 된다.
- Commit 2와 B-track에서도 새 대형 자산보다 `어느 surface가 어떤 tone을 갖는가`를 먼저 본다.

**한 줄 목표:** 아트는 token identity와 surface tone을 semantic anchor로 검수한다.

| 단계 | semantic anchor | 이번 턴 확인할 것 | 아직 필요 없는 것 |
| --- | --- | --- | --- |
| Commit 1 | `artTokenSet`, `accentKey`, `none` | payload identity가 살아 있는가 | 새 일러스트 |
| Commit 2 | scene card / ending helper tone | neutral vs trophy tone 분리 | 결과창용 flavor art 확장 |
| B-track | hero-primer / inline-hint / result-helper | surface별 밀도 차이 | 통합 카드 패밀리화 |

---

## 3-4. QA에게 넘길 것

### 3줄 요약
- QA는 line drift를 발견해도 stage boundary가 유지되는지 먼저 본다.
- 같은 smoke 이름이라도 stage가 다르면 다른 evidence 묶음이다.
- `찾는 위치가 달라졌다`와 `해야 할 일이 달라졌다`를 절대 같은 코멘트로 합치지 않는다.

**한 줄 목표:** QA는 semantic locator가 바뀌어도 같은 S번호와 같은 non-change line으로 검수한다.

| 단계 | semantic proof | 같이 남길 질문 | fail 신호 |
| --- | --- | --- | --- |
| Commit 1 | `continueOverrun` floor, `renderCenter` branch, floor smoke block | progression이 여전히 안 열렸는가 | line drift를 이유로 resolve까지 같이 넣었을 때 |
| Commit 2 | resolve zone, scene/result split, closing smoke block | confirm 1회 적용과 `resolved` guard가 함께 닫혔는가 | ending helper가 overrun scene까지 먹었을 때 |

---

## 3-5. PM/기획에게 넘길 것

### 3줄 요약
- PM/기획은 line drift를 `문서가 틀렸다`로만 읽으면 안 되고, `stage는 그대로인데 locator가 밀렸다`로 먼저 읽어야 한다.
- 중요한 것은 여전히 `Commit 1 floor`, `Commit 2 closing`, `B-1~B-4` 단계명을 좁게 유지하는 것이다.
- locator 재탐색 사실은 기록해도, 단계명을 넓히면 안 된다.

**한 줄 목표:** locator drift를 기록하되, green 문장은 stage 그대로 유지한다.

### PM 체크포인트
- run log에는 `semantic anchor 재확인`을 적을 수 있어도 `범위 조정`이라고 과장하지 않는다.
- Commit 1/2와 B-track 단계명은 line drift와 무관하게 유지한다.
- 단계가 막히면 `locator drift` 또는 `live anchor refresh 필요`라고 짧게 남기고 다음 단계를 열지 않는다.

---

## 4. 기록 문장 / 인계 규칙

## 3줄 요약
- semantic anchor packet은 새 단계명을 만들지 않는다.
- 오직 기존 `Commit 1 floor`, `Commit 2 closing`, `B-1~B-4`를 더 drift-proof하게 찾게 만든다.
- 따라서 기록은 locator 메모를 추가할 수 있지만, green 문장은 그대로다.

**한 줄 목표:** locator는 보강하고, 단계명은 늘리지 않는다.

| 상황 | 허용 기록 | 금지 기록 |
| --- | --- | --- |
| line drift 발견 | `Commit 1 floor semantic anchor를 live code 기준으로 재확인했다.` | `Commit 1 범위를 재설계했다.` |
| Commit 1 green | `Commit 1 floor를 닫고 overrunEvent 진입/snapshot 바닥만 고정했다.` | `오버런 런타임 전환 완료` |
| Commit 2 green | `Commit 2 closing을 닫고 scene/resolve/runtime-ready 경계를 고정했다.` | `오버런 전체 컷오버 완료` |

---

## 5. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| line anchor가 밀리면 implementer가 다시 넓게 grep하며 범위를 키우는 위험 | bounded execution discipline이 무너지고 half-cutover가 다시 생긴다 | semantic locator packet으로 함수 책임 / grep token / surface 책임을 함께 고정했다 | 다음 실제 Commit 1 착수 직전 |
| UI/아트가 line anchor를 못 읽어 visual layer를 감각적으로 합쳐 버리는 위험 | scene / result / primer 경계가 다시 섞인다 | line이 아닌 surface semantic family 기준 handoff를 분리했다 | Commit 2 visual review 직전 |
| QA/PM이 locator drift와 stage drift를 같은 코멘트로 남기는 위험 | 기록상 범위가 실제보다 커 보인다 | locator 메모와 green 문장을 분리했다 | tracker/run log 작성 직전 |
| smoke block 재탐색 중 old immediate baseline이 일부 남는 위험 | Commit 1/2 evidence bundle이 다시 섞인다 | smoke semantic zone을 별도 locator로 고정했다 | Commit 1/2 smoke 수정 직전 |

---

## 6. 즉시 다음 액션
1. 다음 구현자는 `readiness board -> commit1/commit2 surgery sheet -> 이 semantic anchor packet` 순서로 읽고, line drift가 있어도 stage boundary는 그대로 유지한다.
2. Commit 1 착수 전 `continueOverrun`, `renderCenter`, overrun smoke block을 semantic token으로 먼저 다시 찾는다.
3. Commit 2 착수 전 `renderEnding`, `reward-card`, `resolved`, `confirm-overrun-event` semantic zone을 먼저 다시 찾는다.
4. tracker/run log에는 새 단계명을 만들지 않고, 필요하면 `semantic anchor 재확인` 메모만 append한다.
5. 실제 코드 변경 뒤에는 이 패킷이 아니라 기존 `Commit 1 floor` / `Commit 2 closing` green 문장으로만 보고한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_runtime_fixture_ledger_kr.md`
- `docs/dicespell_overrun_onboarding_visual_asset_packet_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
