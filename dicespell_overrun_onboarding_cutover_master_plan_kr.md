# DiceSpell `overrunEvent` → 첫 런 온보딩 컷오버 마스터 플랜

## 3줄 요약
- 이 문서는 `overrunEvent` A-track과 첫 런 온보딩 B-track을 **실제 handoff 순서 하나**로 다시 압축한 cross-track source-of-truth다.
- 프로그래머는 `Commit 1 → Commit 2 → B-1 → B-2 → B-3 → B-4` 실행 큐를 먼저, UI/아트는 `언제 대기하고 언제 닫는가`를 먼저, PM/QA는 `어떤 green 문장을 언제 남기는가`를 먼저 보면 된다.
- 핵심은 새 설계를 더하는 것이 아니라, 이미 닫힌 runtime stack / handoff scorecard / owner workboard를 한 화면에서 재사용 가능하게 묶어 **문서가 충분한데도 실행이 다시 부푸는 문제**를 없애는 것이다.

## 한 줄 목표
A-track은 `Commit 1 = 엔진/상태 floor`, `Commit 2 = 장면/적용 closing`으로 닫고, 그 다음에만 B-track을 `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending`의 4커밋 스택으로 연다.

## 왜 이 문서가 지금 필요한가
기존 마스터 플랜은 `overrunEvent`를 먼저 닫고 그 다음 온보딩을 붙인다는 큰 순서를 정하는 데는 충분했다. 하지만 지금은 상황이 달라졌다.

- A-track에는 이미 `first runtime delivery`, `second runtime slice/review`, `runtime commit stack`, `runtime handoff scorecard`, `runtime owner workboard`까지 있다.
- B-track에도 이미 `runtime stack`, `handoff scorecard`, `owner workboard`, `B-1~B-4 packet`이 있다.
- 즉 병목은 더 이상 `무슨 문서를 써야 하지?`가 아니라 **이 문서들을 실제 사람 handoff와 tracker/run log 문장까지 포함해 어떤 큐로 실행하지?** 쪽이다.

이 문서는 바로 그 실행 큐를 고정한다.

## 이 문서를 누가 어떻게 읽어야 하나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. 전체 실행 큐`, `2. A-track`, `3. B-track` | A/B 각 track의 packet·scorecard·workboard | 이번 턴에 어떤 커밋까지만 닫는지와 다음 handoff 시점을 동시에 고정한다 |
| UI | `2. A-track`, `3. B-track`, `4. 역할별 handoff 규칙` | `screen packet`, `content deck`, `boundary packet` | 언제는 대기/검수 턴이고 언제부터 실제 readable sanity를 닫는지 구분한다 |
| 아트 | `2. A-track`, `3. B-track`, `4. 역할별 handoff 규칙` | `production cutsheet`, `content deck`, `boundary packet` | 새 대형 자산이 아니라 token/accent/shell drift 통제가 우선인 지점을 확인한다 |
| QA | `1. 전체 실행 큐`, `5. 증거와 기록 규칙`, `6. 리스크 & 후속과제` | A/B 각 track의 acceptance·review·scorecard | commit별 evidence bundle과 green 문장을 섞지 않는 기준을 얻는다 |
| PM/기획 | `0. first screen 결론`, `6. 리스크 & 후속과제`, `7. 즉시 다음 액션` | `runtime commit stack`, `runtime/온보딩 owner workboard` | Commit 1/2와 B-1~B-4를 서로 다른 완료 문장으로 기록해야 함을 확인한다 |

## 0. first screen 결론
### 이 문서를 왜 열어야 하나
- **실행 순서**를 다시 조합하지 않기 위해 연다.
- **역할별 대기선/착수선**을 다시 흩어 놓지 않기 위해 연다.
- **tracker/run log 문장**을 커밋별로 다르게 남기기 위해 연다.

### 이번 문서의 판정 규칙
- A-track Commit 1이 green 되기 전에는 Commit 2 표현을 쓰지 않는다.
- A-track Commit 2가 green 되기 전에는 B-track을 열지 않는다.
- B-track은 반드시 `B-1 -> B-2 -> B-3 -> B-4` 순서로만 연다.
- `ending`은 끝까지 결과/CTA 의미만 말하고, flavor/장면은 끝까지 `overrunEvent`가 맡는다.

## 1. 전체 실행 큐

### 1-1. 읽기 순서 한 장 요약
1. 현재 실구현 범위 확인: `docs/dicespell_overrun_runtime_patch_map_kr.md`
2. A-track Commit 1 착수: `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md` -> `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md` -> `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md` -> `docs/dicespell_overrun_runtime_owner_workboard_kr.md`
3. A-track Commit 2 착수: `docs/dicespell_overrun_second_runtime_slice_packet_kr.md` -> `docs/dicespell_overrun_second_runtime_review_packet_kr.md` -> `docs/dicespell_overrun_runtime_commit_stack_packet_kr.md` -> `docs/dicespell_overrun_runtime_owner_workboard_kr.md`
4. B-track 진입 허용 조건 확인: A-track Commit 2 green + boundary packet 재확인
5. B-track 착수: `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md` -> `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md` -> `docs/dicespell_first_run_onboarding_owner_workboard_kr.md` -> `B-1/B-2/B-3/B-4 packet`

### 1-2. 커밋 스택 요약표
| 단계 | 한 줄 목표 | 닫히는 범위 | 열면 안 되는 것 | 종료 문장 |
| --- | --- | --- | --- | --- |
| Commit 1 | `overrunEvent` enter/snapshot floor 고정 | enter wrapper, canonical snapshot, normalize/recovery, non-change line 증거 | 장면 카드 마감, reward 적용, old baseline 제거 완성 | `이번 턴은 overrunEvent floor green이다.` |
| Commit 2 | `overrunEvent` runtime-ready closing | 장면 카드, confirm resolve, reward 1회 적용, `resolved` guard, full smoke migration | onboarding primer, 새 메카닉, overrun flavor를 ending에 되돌리기 | `이번 턴은 overrunEvent runtime-ready closing green이다.` |
| B-1 | library primer floor | 책 선택 hero-primer, seen 제어, 비차단성 | battle/reward/shop/ending primer | `이번 턴은 library primer green이다.` |
| B-2 | battle trigger handoff | entry / first-spell / auto-pass hint, seen 제어, 비차단성 | reward/shop/ending primer, 전투 레이아웃 재설계 | `이번 턴은 battle primer green이다.` |
| B-3 | reward/shop 목적 분리 | reward/shop primer 2면, 목적 차이, boss draft 공존 | ending primer, reward/shop 공용 배지로 축약 | `이번 턴은 reward/shop primer green이다.` |
| B-4 | ending boundary handoff | defeat/victory/overrun-cta primer, 경계 유지 | overrun flavor 흡수, onboarding 전체 polish | `이번 턴은 ending primer green이다.` |

## 2. A-track — `overrunEvent` runtime cutover

### 2-1. 왜 아직 A-track이 먼저인가
- `overrunEvent`가 closing 전이면 ending surface의 책임 경계가 아직 runtime으로 증명되지 않았다.
- B-track ending primer는 이 경계 위에 올라가므로, A-track이 반쯤만 닫힌 상태에서 들어가면 다시 flavor 혼합이 일어난다.
- 따라서 문서 우선순위가 아니라 **실행 우선순위**로도 A-track이 먼저다.

### 2-2. A-track owner별 handoff 초점
| 역할 | Commit 1에서 해야 할 일 | Commit 2에서 해야 할 일 | 계속 금지되는 것 |
| --- | --- | --- | --- |
| 프로그래머 | `continueOverrun()` enter wrapper, snapshot canonicalization, normalize/recovery smoke | display-only scene card 소비, confirm resolve, old immediate baseline 제거 | onboarding helper 선반영, reward 2회 적용 허용 |
| UI | payload 표정/카드 위계 메모, `none` branch density 주의 | 실제 장면 카드 hierarchy/CTA readable sanity 마감 | Commit 1에서 shell을 마감처럼 기록 |
| 아트 | token/accent 계약 메모 | shell/tone drift 검수, 최소 자산 sanity | 대형 신규 자산 제작을 선행 목표로 열기 |
| QA | floor smoke / non-change line 판정 | closing review / full smoke migration 판정 | Commit 1을 runtime-ready처럼 pass 처리 |
| PM/기획 | Commit 1 완료 문장 기록 | Commit 2 closing 문장 기록 | 두 커밋을 같은 green 문장으로 합치기 |

### 2-3. A-track에서 실제로 넘겨야 할 패킷
- **프로그래머에게**: `runtime patch map` + `first runtime delivery` + `runtime commit stack` + `runtime handoff scorecard`
- **UI owner에게**: `a3_ui_packet` + `second runtime slice` + `runtime owner workboard`
- **아트 owner에게**: `event_screen_packet` + `event_content_deck` + `runtime owner workboard`
- **QA owner에게**: `first runtime review` + `second runtime review` + `smoke migration packet`
- **PM owner에게**: `runtime commit stack` + `runtime handoff scorecard` + `runtime owner workboard`

### 2-4. A-track 종료 판정
A-track은 아래가 동시에 성립할 때만 끝이다.
- Commit 1 문장과 Commit 2 문장이 각각 따로 남아 있다.
- `ending`은 여전히 결과/CTA 의미만 말한다.
- `overrunEvent`는 장면/flavor/confirm 시점 적용을 맡는다.
- full smoke migration evidence가 tracker/run log 기준으로 남아 있다.

## 3. B-track — 첫 런 온보딩 runtime stack

### 3-1. 왜 B-track은 surface 1개 = commit 1개여야 하나
- library/battle/reward-shop/ending은 각각 노출 조건, 저장 필드, 검수 포인트가 다르다.
- B-track이 다시 `작은 UX니까 같이 붙이자`로 뭉치면 owner handoff 문장과 acceptance evidence가 즉시 흐려진다.
- 특히 ending은 boundary packet 영향이 크므로, B-4를 다른 surface와 섞는 순간 A-track의 경계 합의가 무너진다.

### 3-2. B-track owner별 handoff 초점
| 역할 | B-1/B-2에서 우선인 것 | B-3/B-4에서 우선인 것 | 계속 금지되는 것 |
| --- | --- | --- | --- |
| 프로그래머 | seen 필드 / helper / trigger 비차단성 | reward-shop 분기 / ending boundary / CTA 공존 | 여러 surface의 helper를 한 커밋에 같이 닫기 |
| UI | hero/inline/badge 밀도 sanity | reward vs shop 목적 차이, ending의 얇은 layer 유지 | overrunEvent flavor를 onboarding copy로 복제 |
| 아트 | 최소 token/accent sanity | reward/shop 구분 토큰, ending의 절제된 강조 | 대형 key art와 primer scope를 혼합 |
| QA | 첫 노출/재노출/비차단 smoke | 목적 분리/경계 유지/CTA 공존 판정 | surface 여러 개를 같은 evidence로 pass 처리 |
| PM/기획 | surface별 다른 완료 문장 기록 | ending green과 overrunEvent green 분리 기록 | `온보딩 전체 green` 같은 뭉뚱그린 보고 |

### 3-3. B-track에서 실제로 넘겨야 할 패킷
- **프로그래머에게**: `first_run_onboarding_runtime_stack_packet` + `handoff_scorecard` + `owner_workboard` + 각 `B-1~B-4 packet`
- **UI owner에게**: `screen_packet` + `content_deck` + `owner_workboard`
- **아트 owner에게**: `production_cutsheet` + `content_deck` + `owner_workboard`
- **QA owner에게**: `acceptance_matrix` + `handoff_scorecard` + `owner_workboard`
- **PM owner에게**: `runtime_stack_packet` + `handoff_scorecard` + `owner_workboard`

### 3-4. B-track 종료 판정
B-track은 아래가 동시에 성립할 때만 끝이다.
- `B-1/B-2/B-3/B-4`가 각자 다른 green 문장으로 기록돼 있다.
- reward/shop 목적 차이와 ending boundary가 같은 문서 세트로 검수됐다.
- `ending`은 끝까지 `결과/CTA 의미만`, flavor는 끝까지 `overrunEvent`에 남아 있다.

## 4. 역할별 handoff 규칙

### 프로그래머
- 먼저 `source handoff` 문서를 읽고, summary는 확인용으로만 본다.
- 커밋 범위를 선언하기 전 `patch map` 또는 각 track의 `runtime stack`을 다시 확인한다.
- 한 커밋에 다음 단계 helper/state를 미리 섞지 않는다.

### UI
- Commit 1과 B-1에서는 마감이 아니라 **읽힘/밀도 sanity**가 우선이다.
- `overrunEvent`는 장면 카드, onboarding은 안내 레이어라는 구조를 끝까지 유지한다.
- ending은 가장 절제된 surface로 남겨야 한다.

### 아트
- 지금 단계의 1순위는 대형 신규 자산이 아니라 token/accent/shell drift 통제다.
- B-4와 Commit 2에서만 실제 화면 강조를 본다.
- `overrunEvent`와 onboarding의 강조 언어를 같은 카드 표정으로 수렴시키지 않는다.

### QA
- 증거는 항상 `커밋 단위`로 묶는다. track 단위로 뭉치지 않는다.
- Commit 1/2, B-1~B-4를 각각 다른 evidence bundle과 다른 완료 문장으로 기록한다.
- `실행은 됨`을 `handoff ready`로, `보임`을 `runtime-ready`로 과장하지 않는다.

### PM/기획
- tracker/run log 문장은 scorecard/workboard의 복붙 문장을 그대로 사용한다.
- `온보딩 전체`, `오버런 컷오버 진행 중` 같은 뭉뚱그린 표현을 피한다.
- 다음 턴을 열 때는 언제나 `직전 green 문장`과 `다음 허용 문장`을 함께 남긴다.

## 5. 증거와 기록 규칙
| 단계 | 최소 evidence bundle | tracker/run log에 남길 문장 |
| --- | --- | --- |
| Commit 1 | smoke, 상태 diff, non-change line, 코드 diff | `이번 턴은 overrunEvent floor green이다.` |
| Commit 2 | smoke migration, UI/DOM evidence, resolve 1회 적용 증거, 코드 diff | `이번 턴은 overrunEvent runtime-ready closing green이다.` |
| B-1 | 첫 노출/재노출/비차단 증거 | `이번 턴은 library primer green이다.` |
| B-2 | entry/first-spell/auto-pass trigger 증거 | `이번 턴은 battle primer green이다.` |
| B-3 | reward/shop 목적 분리 증거 | `이번 턴은 reward/shop primer green이다.` |
| B-4 | ending primer + boundary 유지 증거 | `이번 턴은 ending primer green이다.` |

## 6. 리스크 & 후속과제
- 가장 큰 리스크는 문서 부족이 아니라 **문서가 충분한데도 Commit 1/2와 B-1~B-4를 다시 큰 묶음으로 합치는 것**이다.
- A-track의 새 리스크는 `Commit 1 floor`와 `Commit 2 closing`을 같은 green 문장으로 기록해 half-cutover가 runtime-ready처럼 보이는 것이다.
- B-track의 새 리스크는 owner workboard를 무시하고 `UI는 다 됐다`, `온보딩은 거의 붙었다` 같은 모호한 handoff 언어로 다시 섞이는 것이다.
- 특히 B-4에서 ending green과 overrunEvent green을 한 줄로 합치면 boundary packet이 가장 먼저 무너진다.
- 후속 구현은 새 문서를 더 늘리기보다, 이 문서가 정한 큐와 문장을 실제 코드/검수/기록에 그대로 재사용하는지 확인하는 쪽이 맞다.

## 7. 즉시 다음 액션
1. 실제 writer 구현 슬롯은 여전히 A-track Commit 1부터 닫는다.
2. Commit 1 기록이 남기 전에는 Commit 2 표현을 쓰지 않는다.
3. Commit 2 closing review가 green 되기 전에는 B-track을 열지 않는다.
4. B-track을 열 때는 반드시 `runtime stack -> handoff scorecard -> owner workboard -> B-1/B-2/B-3/B-4 packet` 순서로 읽고, surface별 다른 green 문장을 남긴다.
