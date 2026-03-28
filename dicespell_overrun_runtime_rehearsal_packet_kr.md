# DiceSpell `overrunEvent` 런타임 리허설 패킷

## 3줄 요약
- 이 문서는 `readiness board`, `Commit 1/2 surgery sheet`, `semantic anchor packet`까지 이미 닫힌 상태에서 남아 있던 마지막 실행 공백인 **"좋아, 이제 live edit 직전에 팀이 뭘 같은 순서로 확인해야 하지?"**를 메우는 리허설 source-of-truth다.
- 핵심은 새 설계를 더하는 것이 아니라, 프로그래머/UI/아트/QA/PM이 **코드 수정 전에 같은 단계명, 같은 금지 범위, 같은 제출물**을 5분 안에 다시 잠그게 만드는 것이다.
- 첫 화면만 읽어도 `Commit 1 floor`와 `Commit 2 closing`이 각각 **무엇을 아직 열지 말아야 하는가**, **누가 지금 시작 가능하고 누가 아직 freeze여야 하는가**, **run log에 어떤 문장만 남겨야 하는가**를 바로 판단할 수 있어야 한다.

## 한 줄 목표
실제 live cutover 직전에 `Commit 1 floor`와 `Commit 2 closing`을 **리허설 가능한 실행 단계**로 다시 압축해, 문서가 충분한데도 첫 수정이 즉흥적으로 커지는 위험을 줄인다.

## 왜 이 문서가 필요한가
지금 DiceSpell의 A-track 문서는 이미 충분하다.

이미 닫힌 것:
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_runtime_semantic_anchor_packet_kr.md`
- `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`

그런데 실제 구현 직전 마지막 작은 병목은 아직 남아 있다.

1. 문서는 많지만, live edit 직전 `누가 무엇을 freeze하고 무엇을 아직 건드리면 안 되는가`를 팀이 같은 순서로 다시 잠그는 문서가 없다.
2. implementer는 `알겠어, 이제 그냥 들어가면 되지`라고 느끼기 쉬운데, 이 순간 Commit 1에 resolve/UI polish가 섞이거나 Commit 2에 onboarding/balance 손질이 섞이기 쉽다.
3. UI/아트는 `아직 대기선인가, 지금 visual closing인가`를 readiness 문서와 surgery sheet 사이에서 다시 조합해야 하고, QA/PM은 `리허설 완료`와 `실구현 완료`를 서로 다른 문장으로 구분해 남겨야 한다.
4. 즉 지금 필요한 것은 새 spec이 아니라, **실제 수정 전에 같은 단계명을 다시 말해보는 rehearsal packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. Commit 1 리허설`, `3. Commit 2 리허설` | `semantic anchor packet`, `commit1/2 surgery sheet` | 수정 시작 전 `이번 커밋에서 절대 안 여는 것`을 먼저 잠근다 |
| UI | `1. first screen 판정`, `4-2. UI 리허설` | `A-3 UI packet`, `visual asset packet` | Commit 1은 freeze, Commit 2에서만 scene/result closing을 연다 |
| 아트 | `4-3. 아트 리허설` | `visual asset packet`, `content deck` | Commit 1에서는 token identity만 확인, 새 자산/톤 마감은 Commit 2 이후만 연다 |
| QA | `4-4. QA 리허설`, `5. 기록 규칙`, `6. 리스크 & 후속과제` | `fixture ledger`, `execution reporting packet` | 리허설 완료와 구현 완료를 같은 green 문장으로 기록하지 않는다 |
| PM/기획 | `1. first screen 판정`, `4-5. PM 리허설`, `5. 기록 규칙` | `readiness board`, `runtime commit stack` | 시작 가능 판정과 실제 green 기록을 분리한다 |

---

## 1. first screen 판정

### 왜 이 문서를 먼저 열어야 하나
- **프로그래머**: 코드를 열기 전에 `이번 커밋에서 안 할 일`을 먼저 입 밖으로 확인하려고 연다.
- **UI/아트**: 지금이 visual review 턴인지, 아직 semantic freeze 턴인지 확인하려고 연다.
- **QA/PM**: `착수 가능`과 `완료`를 같은 초록불로 적지 않으려고 연다.

### 가장 짧은 답
- `Commit 1 floor`는 여전히 **enter 분리 + snapshot floor + recovery smoke**까지만 닫는 턴이다.
- `Commit 2 closing`은 여전히 **scene card + confirm resolve + old immediate baseline 제거**까지만 닫는 턴이다.
- 둘 다 아직 onboarding B-track, 새 balance 가설, 새 content branch를 열면 안 된다.

### 한 줄 판정 규칙
- 문서가 많을수록 **무엇을 할지**보다 **무엇을 아직 안 할지**를 먼저 읽는다.
- 리허설에서 금지 범위를 말로 못 잠그면, live edit을 시작하지 않는다.

---

## 2. Commit 1 floor 리허설

## 3줄 요약
- Commit 1 리허설의 목적은 코드를 고치기 전에 `floor만 닫는다`는 말을 다시 같은 문장으로 맞추는 것이다.
- 프로그래머는 `continueOverrun`, `renderCenter`, overrun smoke block을 다시 찾기 전에 `reward apply / page advance / segment cleanup / scene polish는 안 한다`를 먼저 선언해야 한다.
- UI/아트/QA/PM도 이 턴을 `runtime-ready`가 아니라 `중간 바닥 고정`으로만 읽어야 한다.

### 한 줄 목표
Commit 1을 `첫 수정`이 아니라 **floor rehearsal이 끝난 뒤에만 여는 bounded commit**으로 다시 잠근다.

### Commit 1 시작 전 5문장
1. `이번 턴은 overrunEvent enter와 canonical snapshot floor까지만 닫는다.`
2. `reward apply, currentPage/pagePlan progression, segmentTrophyTemplateIds cleanup은 아직 안 연다.`
3. `renderCenter는 explicit branch floor까지만, scene card polish는 아직 안 연다.`
4. `smoke는 S1/S2/S3/S7~S11 floor evidence만 본다.`
5. `run log에는 Commit 1 floor 문장만 남기고 overrun 전체 완료 문장은 쓰지 않는다.`

### 프로그래머 리허설 시퀀스
| 순서 | 해야 하는 확인 | 왜 필요한가 | 금지 |
| --- | --- | --- | --- |
| 1 | `semantic anchor packet`으로 `continueOverrun`, `renderCenter`, smoke block 재확인 | locator drift를 stage drift로 오해하지 않기 위해 | line 못 찾았다고 resolve/UI를 같이 여는 것 |
| 2 | `commit1 floor surgery sheet`로 live cut line 재확인 | 실제 절개 지점을 다시 좁히기 위해 | 큰 함수 주변을 넓게 정리하는 것 |
| 3 | `state matrix`로 non-change line 확인 | reward 미적용 / page 미전진을 같이 잠그기 위해 | 중간 상태를 `반쯤 완료`처럼 바꾸는 것 |
| 4 | `fixture ledger`로 S1/S2/S3/S7~S11만 확인 | smoke 증거를 커밋 경계와 맞추기 위해 | S4/S5/S6/S10/S12를 같이 그린 처리 |
| 5 | `execution reporting` 문장 미리 복붙 준비 | run log 과장을 막기 위해 | 커밋 후에 문장을 즉흥적으로 새로 쓰는 것 |

### Commit 1에서 UI/아트가 확인할 것
- UI: `overrunEvent` explicit branch가 생겨도 **scene-card 시각 마감은 아직 아님**을 확인한다.
- 아트: `artTokenSet`, `accentKey`, `none` branch identity가 snapshot으로 살아 있는지만 본다.
- 둘 다 새 shell, 새 backplate, 새 density 논의를 시작하지 않는다.

### Commit 1 종료선
- `phase='overrunEvent'` 진입이 생겼다.
- canonical snapshot field set이 있다.
- reward는 아직 적용되지 않았다.
- `currentPage`, `pagePlan`, `segmentTrophyTemplateIds`는 아직 progression 전 상태를 유지한다.
- tracker/run log에는 `Commit 1 floor`만 남는다.

---

## 3. Commit 2 closing 리허설

## 3줄 요약
- Commit 2 리허설의 목적은 `이제 closing을 열어도 된다`를 팀이 같은 말로 확인하는 것이다.
- 이 단계는 scene card가 보이는 것만이 아니라 `confirm 1회 적용`, `resolved guard`, `old immediate baseline 제거`까지 닫혀야 끝난다.
- Commit 2도 끝나기 전에는 onboarding B-track이나 새 polish 범위를 열면 안 된다.

### 한 줄 목표
Commit 2를 `마지막 손질`이 아니라 **runtime-ready closing 묶음**으로만 연다.

### Commit 2 시작 전 5문장
1. `Commit 1 floor green이 남아 있지 않으면 Commit 2를 열지 않는다.`
2. `이번 턴은 scene card, confirm resolve, old immediate baseline 제거까지만 닫는다.`
3. `ending helper가 overrun flavor를 먹지 않게 유지한다.`
4. `smoke는 S4/S5/S6/S10/S12 closing evidence만 본다.`
5. `Commit 2 green 전에는 onboarding B-track을 열지 않는다.`

### 프로그래머 리허설 시퀀스
| 순서 | 해야 하는 확인 | 왜 필요한가 | 금지 |
| --- | --- | --- | --- |
| 1 | `second runtime slice`와 `second runtime review`를 같이 연다 | UI와 resolve를 같은 closing 묶음으로 유지하기 위해 | UI만 먼저 닫고 resolve를 다음 턴으로 미루는 것 |
| 2 | `commit2 closing surgery sheet`로 live anchor 재확인 | render/resolve/baseline 제거 cut line을 좁히기 위해 | ending 전반을 다시 크게 손보는 것 |
| 3 | `visual asset packet`과 `ending boundary` 재확인 | scene/result helper 경계를 지키기 위해 | ending 본문에 trophy flavor를 미리 싣는 것 |
| 4 | `fixture ledger`로 S4/S5/S6/S10/S12만 확인 | closing evidence를 Commit 1과 분리하기 위해 | Commit 1 floor smoke를 같이 green 처리 |
| 5 | `execution reporting` closing 문장 미리 준비 | runtime-ready closing 과장을 막기 위해 | `오버런 전체 완료`처럼 크게 기록 |

### Commit 2에서 UI/아트가 확인할 것
- UI: `badge -> header -> flavor -> reward summary -> CTA -> footnote` 위계가 scene card 1장으로 읽힌다.
- UI: ending은 여전히 result/helper만 말하고, overrun flavor는 장면 카드에서만 읽힌다.
- 아트: `scene-card`, `result-helper`, `hero-primer`, `inline-hint` 패밀리가 섞이지 않는다.
- 아트: `none` branch도 placeholder가 아니라 정상 장면 밀도를 유지한다.

### Commit 2 종료선
- scene card가 explicit `overrunEvent` surface로 읽힌다.
- confirm CTA는 reward를 1회만 적용한다.
- `resolved` guard가 reload/re-entry에서 중복 적용을 막는다.
- old immediate baseline이 smoke와 runtime에서 제거된다.
- tracker/run log에는 `Commit 2 closing`만 남는다.

---

## 4. 역할별 리허설 포인트

## 4-1. 프로그래머
- `지금 추가하는 것이 아니라 닫는 중이다.`
- `못 찾은 line은 다시 찾되, 범위는 넓히지 않는다.`
- `Commit 1/2 사이에 balance/content/onboarding을 끼우지 않는다.`

## 4-2. UI
- `Commit 1은 branch floor, Commit 2가 scene closing이다.`
- `scene-card`와 `ending helper`를 같은 family로 평준화하지 않는다.
- `surface 1개 = visual bundle 1개` 원칙을 B-track까지 유지한다.

## 4-3. 아트
- `Commit 1에서는 token identity, Commit 2에서만 tone closing을 본다.`
- 새 key art나 추가 flavor asset 요구를 Commit 1에 얹지 않는다.
- `none` branch를 빈 상태처럼 다루지 않는다.

## 4-4. QA
- `리허설 완료`와 `실구현 완료`를 같은 체크로 적지 않는다.
- Commit 1은 floor evidence, Commit 2는 closing evidence만 받는다.
- line drift를 발견해도 stage drift 코멘트로 번역하지 않는다.

## 4-5. PM/기획
- `지금 시작 가능`은 green이 아니다.
- Commit 1/2 기록 문장은 execution reporting packet 복붙만 쓴다.
- 리허설 메모가 있어도 단계명은 늘리지 않는다.

---

## 5. 기록 규칙

## 3줄 요약
- 이 문서는 새 단계명을 만들지 않는다.
- 오직 `Commit 1 floor`, `Commit 2 closing`을 live edit 직전 다시 잠그게 만들 뿐이다.
- 따라서 기록은 rehearsal 메모를 남길 수 있어도, 완료 문장은 기존 scorecard/reporting packet 문장만 쓴다.

### 허용 기록
- `Commit 1 floor 착수 전 semantic/line anchor rehearsal을 완료했다.`
- `Commit 2 closing 착수 전 scene/result boundary rehearsal을 완료했다.`

### 금지 기록
- `오버런 컷오버를 다시 설계했다.`
- `overrunEvent 구현 완료 전반을 재정리했다.`
- `온보딩도 같이 열 수 있다고 판단했다.`

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| 문서가 충분하다는 이유로 첫 live edit이 다시 즉흥적으로 커지는 위험 | Commit 1/2 경계가 다시 섞인다 | 리허설 packet으로 시작 전 금지 범위를 문장으로 잠그게 만들었다 | 다음 실제 Commit 1 착수 직전 |
| UI/아트가 Commit 1과 Commit 2의 visual 책임을 다시 섞는 위험 | scene/result/helper 경계가 흐려진다 | Commit 1 freeze / Commit 2 closing을 역할별로 분리했다 | Commit 2 visual review 직전 |
| QA/PM이 rehearsal note와 green record를 같은 층위로 적는 위험 | half-cutover가 완료처럼 보인다 | rehearsal 메모와 execution reporting 문장을 분리했다 | tracker/run log 작성 직전 |
| Commit 2 green 전에 onboarding B-track을 여는 위험 | ending↔overrun boundary와 owner queue가 무너진다 | Commit 2 종료선에 `B-track closed`를 명시했다 | Commit 2 closing review 직전 |

---

## 7. 즉시 다음 액션
1. 다음 구현자는 `readiness board -> semantic anchor packet -> commit1/commit2 surgery sheet -> 이 rehearsal packet` 순서로 읽고, 그 다음에만 소스를 연다.
2. Commit 1 직전에는 `이번 턴에 절대 안 여는 것` 5문장을 먼저 선언하고 floor만 닫는다.
3. Commit 2 직전에는 `Commit 1 green 확인 -> scene/result 경계 재확인 -> closing smoke 범위 재확인` 3단계를 먼저 말로 잠근다.
4. tracker/run log에는 필요하면 `rehearsal 완료` 메모를 append할 수 있지만, green 문장은 기존 `Commit 1 floor` / `Commit 2 closing`만 쓴다.
