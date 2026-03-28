# DiceSpell `overrunEvent` / 첫 런 온보딩 컷오버 준비 보드

## 3줄 요약
- 이 문서는 `overrunEvent` A-track과 첫 런 온보딩 B-track 문서가 충분히 쌓인 지금, **실제 제작자가 이번 턴에 무엇을 시작해도 되고 무엇은 아직 기다려야 하는지**를 한 화면에서 판단하게 만드는 source-of-truth다.
- 핵심은 새 설계를 더하는 것이 아니라, `Commit 1 floor -> Commit 2 closing -> B-1/B-2/B-3/B-4`를 **착수 가능 상태 / 대기 상태 / 제출 증거 / 금지 범위**로 다시 압축해 handoff-ready를 runtime-ready로 착각하지 않게 만드는 것이다.
- 첫 화면만 읽어도 프로그래머, UI, 아트, QA, PM이 `지금 내 차례인가`, `무엇을 넘기면 되나`, `어디서 멈춰야 하나`를 즉시 판단할 수 있어야 한다.

## 한 줄 목표
문서 세트를 `읽기용 아카이브`가 아니라 **오너별 착수/대기/인계 판단 보드**로 재배열해, 다음 구현 턴이 더 이상 문서 탐색 때문에 범위를 부풀리지 않게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell의 병목은 설계 부족보다 **착수 판단의 분산**이다.

이미 충분히 닫힌 것:
- `overrunEvent` A-1~A-4 packet, runtime patch map, fixture ledger, review/delivery/scorecard/workboard, cutover gate, execution reporting
- 온보딩 B-1~B-4 packet, runtime stack, handoff scorecard, owner workboard, content deck, boundary packet
- visual asset packet, gap ledger, commit1/commit2 surgery sheet

그런데 실제 제작 턴에서는 아직도 아래가 다시 흔들리기 쉽다.
1. 프로그래머가 `Commit 1은 지금 바로 시작 가능`과 `Commit 2는 아직 visual contract 재확인 후 시작`을 같은 수준으로 읽는다.
2. UI/아트가 `대기 검토 턴`과 `실제 마감 턴`을 구분하지 못해 너무 일찍 shell을 닫거나, 반대로 이미 시작 가능한 surface를 계속 기다린다.
3. QA/PM이 handoff-ready와 runtime-ready를 같은 초록불로 기록해 half-cutover를 완료처럼 보이게 만든다.
4. 문서가 많아질수록 implementer가 `그래도 이번 턴엔 뭘 하면 되지?`를 다시 여러 packet에서 조합해야 한다.

즉 지금 필요한 것은 새 카피나 새 설계가 아니라, **오너별로 지금 시작 가능한 단계와 아직 금지된 단계를 같은 문장으로 보여 주는 준비 보드**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. 단계별 readiness 표`, `3-1. 프로그래머 핸드오프` | `runtime patch map`, `commit1/commit2 surgery sheet`, 각 B-step packet | 지금 열 수 있는 코드 범위와 아직 열면 안 되는 범위를 즉시 판정한다 |
| UI | `1. first screen 판정`, `2. 단계별 readiness 표`, `3-2. UI 핸드오프` | `visual asset packet`, `A-3 UI packet`, `B-1~B-4 screen/packet` | Commit 1은 대기 검토 턴이고 Commit 2/B-track만 closing 턴임을 확인한다 |
| 아트 | `1. first screen 판정`, `2. 단계별 readiness 표`, `3-3. 아트 핸드오프` | `visual asset packet`, `content deck`, `boundary packet` | 지금 필요한 것은 대형 신작이 아니라 token/accent sanity인지 확인한다 |
| QA | `2. 단계별 readiness 표`, `3-4. QA 핸드오프`, `5. 리스크 & 후속과제` | `fixture ledger`, `execution reporting packet`, `scorecard` | 각 단계별 증거 묶음과 stop line을 같은 표로 본다 |
| PM/기획 | `1. first screen 판정`, `2. 단계별 readiness 표`, `4. 기록 문장/인계 규칙`, `5. 리스크 & 후속과제` | `cutover gate packet`, `owner handoff packet`, `development tracker` | 어떤 단계가 지금 착수 가능인지와 어떤 문장을 기록해야 하는지 즉시 판단한다 |

---

## 1. first screen 판정

### 이 문서를 왜 열어야 하나
- **프로그래머**: 이번 턴에 `무슨 파일을 열지`보다 먼저 `지금 이 단계가 열려 있는가`를 판단하려고 연다.
- **UI/아트**: 지금이 실제 마감 턴인지, 아니면 payload/branch/boundary를 검토만 하고 기다리는 턴인지 판단하려고 연다.
- **QA/PM**: 지금 남길 기록이 `floor`, `closing`, `B-1`, `B-2`, `B-3`, `B-4` 중 무엇인지 혼동하지 않으려고 연다.

### 가장 짧은 답
- **지금 즉시 시작 가능**: `A-track Commit 1 floor` 문서/코드/검수 착수
- **Commit 1 green 뒤 시작 가능**: `A-track Commit 2 closing`
- **Commit 2 green 뒤 시작 가능**: `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending`
- **지금 금지**: Commit 1에서 resolve/UI closing을 섞는 것, Commit 2 이전에 온보딩 B-track을 여는 것, B-4에서 overrun flavor를 ending helper에 섞는 것

### 한 줄 판정 규칙
- 지금의 질문은 `무엇을 더 설계하지?`가 아니라 **`지금 누가 시작할 수 있지?`**다.
- `시작 가능` 표기가 없는 단계는 읽고 준비만 하고, 구현/마감/green 기록은 하지 않는다.
- `대기` 단계에서 할 일은 다음 단계 문서를 더 쓰는 것이 아니라 **시작 조건과 금지 범위를 공유하는 것**이다.

---

## 2. 단계별 readiness 보드

## 3줄 요약
- 아래 표는 A-track/B-track을 `지금 시작 가능 / 시작 조건 / 오너별 제출물 / stop signal` 기준으로 다시 압축한다.
- 목적은 문서가 많아도 implementer가 이번 턴 범위를 다시 넓게 잡지 못하게 하는 것이다.
- 특히 UI/아트/QA/PM이 `언제 들어오고 언제 기다려야 하는가`를 프로그래머와 같은 단계 언어로 공유해야 한다.

### 한 줄 목표
각 단계가 `문서상 존재`하는 상태가 아니라 **작업 착수 가능 여부**로 읽히게 만든다.

| 단계 | 지금 상태 | 시작 조건 | 이번 단계 핵심 산출물 | 아직 금지 | 다음 단계로 넘기는 증거 |
| --- | --- | --- | --- | --- | --- |
| Commit 1 floor | **즉시 시작 가능** | `cutover gate` 확인, `runtime patch map -> first runtime slice -> state matrix -> fixture ledger -> commit1 surgery sheet` 읽기 완료 | `phase='overrunEvent'` 진입, canonical snapshot floor, recovery convergence, non-change line 유지 | scene card closing, confirm resolve, page progression, onboarding primer | `S1/S2/S3/S7/S8/S9/S10/S11`, 상태 diff, floor handoff 문장 |
| Commit 2 closing | **Commit 1 green 뒤 시작** | Commit 1 evidence pass, `commit2 surgery sheet -> visual asset packet -> A-3 UI packet -> boundary/content deck` 재확인 | scene card, confirm 1회 적용, `resolved` guard, old baseline 제거 | B-track primer, ending flavor 혼합, 새 메카닉 추가 | `S4/S5/S6/S10/S12`, DOM/시각 캡처, closing handoff 문장 |
| B-1 library | **Commit 2 green 뒤 시작** | A-track closing green, `onboarding runtime stack -> B-1 packet -> content deck` 재확인 | `seenBookSelectPrimer`, library hero-primer, 비차단 선택 | battle/reward/shop/ending primer 동시 구현 | B1 acceptance/recovery, library handoff 문장 |
| B-2 battle | **B-1 green 뒤 시작** | B-1 evidence pass, `B-2 packet` 재확인 | `battle.entry`/`first-spell`/`auto-pass` primer, seen flags, 비차단 조작 | reward/shop/ending 확장, battle hero card 과밀화 | B2 acceptance/recovery, battle handoff 문장 |
| B-3 reward/shop | **B-2 green 뒤 시작** | B-2 evidence pass, `B-3 packet` 재확인 | reward/shop 목적 분리 primer, boss draft 공존, seen flags | ending helper 동시 구현, reward/shop strip 평준화 | B3 acceptance/recovery, reward/shop handoff 문장 |
| B-4 ending | **B-3 green 뒤 시작** | B-3 evidence pass, `B-4 packet + boundary packet` 재확인 | defeat/victory/overrun-cta helper, CTA 의미 보조, boundary 유지 | overrun flavor 선탑재, 장면 카드화 | B4 acceptance/recovery, boundary evidence, ending handoff 문장 |

### 단계별 한 줄 해석
- **Commit 1**: 엔진/상태 바닥을 닫는 턴
- **Commit 2**: 장면/적용/중복방지 closing을 닫는 턴
- **B-1~B-4**: primer surface를 하나씩 여는 턴
- 무엇이든 둘 이상 섞이면 준비 보드 위반이다.

---

## 3. 역할별 핸드오프 포인트

## 3-1. 프로그래머에게 넘길 것

### 3줄 요약
- 프로그래머는 이 보드를 `무엇을 코딩할까`보다 `어느 단계만 코딩해도 되는가`를 판단하는 표로 읽어야 한다.
- 지금 즉시 여는 코드는 Commit 1 뿐이며, Commit 2와 B-track은 시작 조건이 충족된 뒤에만 열린다.
- 특히 `지금 열 수 있는 파일`보다 `아직 열면 안 되는 책임`을 먼저 고정해야 한다.

**한 줄 목표:** 단계별로 파일은 열어도 되지만, 책임은 넘겨 열지 않는다.

| 단계 | 열어도 되는 파일/영역 | 이번 턴 핵심 작업 | 절대 섞지 말 것 |
| --- | --- | --- | --- |
| Commit 1 | `src/game-engine.js`, `src/app.js`, `scripts/smoke-test.mjs`의 floor anchor | enter wrapper, canonical snapshot floor, normalize/recovery, explicit non-change line 유지 | resolve, scene card closing, page advance, reward apply |
| Commit 2 | `src/game-engine.js`, `src/app.js`, `src/styles.css`, `scripts/smoke-test.mjs` | scene card render, confirm resolve, `resolved` guard, old baseline 제거 | onboarding primer, 새 branch 설계 |
| B-1~B-4 | 각 surface render/helper/meta flag | surface 1개 primer 주입 + seen flag + recovery | 여러 surface 동시 구현, ending에 overrun flavor 주입 |

### 프로그래머 제출물
1. 단계명 1개
2. 파일 경계가 보이는 코드 diff
3. 해당 단계 smoke 결과
4. 해당 단계 handoff 문장 1개

---

## 3-2. UI에게 넘길 것

### 3줄 요약
- UI는 Commit 1에서 마감자가 아니라 **검토자**다.
- 실제 closing은 Commit 2와 B-track부터 시작하며, 그마저도 `surface 1개 = visual bundle 1개` 원칙으로만 열린다.
- 가장 큰 위험은 보기 좋게 맞추려다가 단계 경계를 지워 버리는 것이다.

**한 줄 목표:** UI는 지금 `예쁘게 마감`보다 `언제 마감해도 되는지`를 먼저 맞춘다.

| 단계 | UI 역할 | 이번 턴 필수 확인 | 금지 |
| --- | --- | --- | --- |
| Commit 1 | 대기 검토 | Commit 2에서 필요한 shell 분리 조건만 메모 | scene card 선마감, ending/helper와 합체 |
| Commit 2 | 실제 closing | `scene-card` 위계, `ending`과 다른 shell, `none` neutral density | reward/result 표 재활용으로 장면을 죽이는 것 |
| B-1 | 실제 closing | library hero-primer가 shelf 선택을 가리지 않는지 | shelf 전체 재설계 |
| B-2 | 실제 closing | battle hero-primer와 inline hint 밀도 분리 | 전투 UI 전체 재배치 |
| B-3 | 실제 closing | reward/shop 목적 분리, boss draft 공존 | 동일 strip 평준화 |
| B-4 | 실제 closing | result-helper 저밀도 유지, CTA 의미만 보조 | overrun flavor/helper 혼입 |

### UI 제출물
1. hierarchy note 1개
2. 단계별 캡처
3. 보류한 polish 메모 1개
4. handoff 문장 검토 OK/수정 포인트

---

## 3-3. 아트에게 넘길 것

### 3줄 요약
- 아트의 기본 역할은 새 key art 생산이 아니라, 이번 단계가 **소형 token/accent sanity로 닫히는지**를 판정하는 것이다.
- Commit 2와 B-track 모두 기본값은 대형 신규 자산 없음이다.
- `ending`과 `overrunEvent`의 경계를 아트가 무심코 흐리는 순간, 문서상 분리도 같이 무너진다.

**한 줄 목표:** 대형 자산보다 layer boundary를 먼저 지킨다.

| 단계 | 아트 기본 역할 | 이번 턴 필요한 것 | 이번 턴 필요 없는 것 |
| --- | --- | --- | --- |
| Commit 1 | 대기 검토 | Commit 2 token/shell 분리 체크 | 새 카드 일러스트 |
| Commit 2 | sanity closing | `scene-card` token, accent, neutral `none` variant sanity | ending용 flavor art, 신규 보상 일러스트 |
| B-1 | sanity closing | library primer badge/highlight token | shelf 배경 리뉴얼 |
| B-2 | sanity closing | battle inline token/icon | 대형 전투 연출 자산 |
| B-3 | sanity closing | reward vs shop accent 분리 token | shop 전면 아트 팩 |
| B-4 | sanity closing | neutral/result helper token | trophy flavor art 선탑재 |

### 아트 제출물
1. token/accent sanity 메모
2. 새 대형 자산 불필요/예외 필요 판정 1줄
3. boundary 비혼입 확인 메모

---

## 3-4. QA에게 넘길 것

### 3줄 요약
- QA는 지금부터 각 단계를 `완료`가 아니라 `어느 단계 green인가`로 구분해 기록해야 한다.
- Commit 1/2와 B-1~B-4는 모두 다른 evidence bundle을 가진다.
- 단계명이 흐려지면 가장 먼저 무너지는 것은 smoke보다 기록 신뢰도다.

**한 줄 목표:** QA는 같은 통과라도 단계별로 다른 증거 묶음을 남긴다.

| 단계 | 필수 evidence | 같이 남길 질문 | fail로 돌려야 하는 경우 |
| --- | --- | --- | --- |
| Commit 1 | `S1/S2/S3/S7/S8/S9/S10/S11`, 상태 diff | reward/page/segment가 아직 안 바뀌었는가 | resolve나 scene closing이 섞였을 때 |
| Commit 2 | `S4/S5/S6/S10/S12`, DOM/시각 캡처 | confirm 1회 적용과 `resolved` guard가 함께 닫혔는가 | old baseline이 남았을 때 |
| B-1 | library acceptance/recovery | primer가 비차단인가 | battle/reward surface가 같이 열렸을 때 |
| B-2 | battle acceptance/recovery | entry/first-spell/auto-pass가 각기 다른 trigger인가 | battle 힌트가 hero card처럼 커졌을 때 |
| B-3 | reward/shop acceptance/recovery | reward와 shop 목적이 시각/문구 모두 분리되는가 | shop/reward가 같은 strip처럼 읽힐 때 |
| B-4 | ending acceptance/recovery + boundary evidence | helper가 CTA 의미만 말하는가 | overrun flavor가 ending에 들어갔을 때 |

---

## 3-5. PM/기획에게 넘길 것

### 3줄 요약
- PM/기획의 가장 중요한 일은 지금부터 `문서가 충분하다`와 `구현이 끝났다`를 같은 문장으로 쓰지 않는 것이다.
- 이 보드는 곧 기록 보드다. 즉 어떤 단계명을 쓰는지가 실제 범위를 통제한다.
- stop signal이 보이면 기능 추가보다 기록 정정이 먼저다.

**한 줄 목표:** PM은 단계명을 관리해 범위 드리프트를 막는다.

### PM 체크포인트
- 지금 남길 수 있는 green 문장은 해당 단계에만 한정된다.
- `오버런 거의 완료`, `온보딩 진행`, `문서 정리 끝` 같은 큰 문장은 금지한다.
- 다음 단계 착수 전에는 항상 이전 단계 evidence 묶음이 tracker/run log/portal에 반영됐는지 확인한다.

---

## 4. 기록 문장 / 인계 규칙

## 3줄 요약
- 단계가 분리돼도 기록 문장이 섞이면 결국 같은 문제로 돌아간다.
- 따라서 handoff-ready와 runtime-ready는 항상 다른 문장으로 남겨야 한다.
- 이 보드는 실행 순서만이 아니라 기록 순서도 고정한다.

**한 줄 목표:** 단계별로 `문장 1개 + evidence 1개 + append log 1개`만 남긴다.

| 단계 | tracker/run log에 남길 기본 문장 | 같이 묶을 evidence |
| --- | --- | --- |
| Commit 1 | `Commit 1 floor를 닫고 overrunEvent 진입/snapshot 바닥만 고정했다.` | floor smoke + 상태 diff |
| Commit 2 | `Commit 2 closing을 닫고 scene/resolve/runtime-ready 경계를 고정했다.` | closing smoke + DOM/시각 캡처 |
| B-1 | `B-1 library primer를 닫고 첫 책 선택 guidance를 비차단으로 고정했다.` | library acceptance/recovery |
| B-2 | `B-2 battle primer를 닫고 entry/first-spell/auto-pass guidance를 분리했다.` | battle acceptance/recovery |
| B-3 | `B-3 reward/shop primer를 닫고 목적 분리를 surface별로 고정했다.` | reward/shop acceptance/recovery |
| B-4 | `B-4 ending helper를 닫고 결과/CTA 의미만 남기는 경계를 고정했다.` | ending acceptance/recovery + boundary evidence |

### 기록 규칙
1. run log는 항상 새 timestamped entry를 **끝에 append**한다.
2. tracker/current snapshot/public portal은 가장 최근 단계명만 짧고 명확하게 반영한다.
3. 한 번의 슬롯에서 단계 둘 이상을 green 처리하지 않는다.
4. 단계가 막히면 `무엇을 하려다 막혔는지`만 짧게 남기고 다음 단계를 열지 않는다.

---

## 5. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| 문서가 많아서 오히려 `지금 누가 시작 가능한가`가 흐려지는 위험 | 프로그래머/UI/아트/QA/PM이 각자 다른 단계부터 움직이게 된다 | 이번 readiness board로 단계별 착수 가능 여부와 대기선을 한 표로 고정했다 | 다음 실제 구현 슬롯 착수 전 |
| Commit 1 대기 검토 턴인데 UI/아트가 너무 일찍 마감을 시작하는 위험 | floor evidence가 흐려지고 half-cutover가 다시 생긴다 | Commit 1은 UI/아트 `대기 검토`, Commit 2부터 `실제 closing`으로 역할을 분리했다 | Commit 1 리뷰 직전 |
| Commit 2 green 전 B-track을 열어 primer 구현이 잘못된 baseline 위에 올라가는 위험 | 온보딩 primer가 overrunEvent old baseline과 섞여 재작업이 생긴다 | B-track 모든 surface를 `Commit 2 green 뒤 시작`으로 못 박았다 | Commit 2 closing 직후 |
| PM/QA가 handoff-ready와 runtime-ready를 같은 green 문장으로 기록하는 위험 | 다음 implementer가 이미 닫힌 단계와 아직 안 닫힌 단계를 오판한다 | 단계별 복붙 문장과 evidence bundle을 같이 고정했다 | tracker/run log 작성 직전 |
| 단계별 stop signal이 무시돼 `이번에 같이 하자`가 다시 반복되는 위험 | bounded execution discipline이 무너진다 | 각 단계마다 `아직 금지` 열을 별도로 고정했다 | 각 단계 착수 직전 |

---

## 6. 즉시 다음 액션

1. 다음 구현자는 먼저 `cutover gate -> runtime commit stack -> 이 readiness board` 순서로 읽고, **지금은 Commit 1만 열려 있다**는 사실부터 확인한다.
2. Commit 1 슬롯에서는 프로그래머는 floor만, UI/아트는 대기 검토만, QA/PM은 floor evidence/문장 준비만 한다.
3. Commit 1 green 뒤에만 `commit2 surgery sheet -> visual asset packet -> 이 readiness board` 순서로 Commit 2를 연다.
4. Commit 2 green 뒤에만 `onboarding runtime stack -> 이 readiness board -> 각 B-step packet` 순서로 B-1부터 surface별로 연다.
5. 어떤 단계에서든 막히면 새 문서를 더 늘리기보다 `어느 시작 조건이 안 맞았는가`를 짧게 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_cutover_gate_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_onboarding_visual_asset_packet_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
- `docs/dicespell_overrun_onboarding_gap_ledger_kr.md`
