# DiceSpell `overrunEvent` / 첫 런 온보딩 사인오프 래더

## 3줄 요약
- 이 문서는 `Commit 1 floor -> Commit 2 closing -> B-1/B-2/B-3/B-4`를 **누가 먼저 보고, 누가 멈춰 세우고, 누가 다음 단계를 열 수 있는가** 기준으로 다시 고정하는 source-of-truth다.
- 지금 DiceSpell의 남은 병목은 설계 부족보다 `좋아, evidence는 모였는데 정확히 누가 green을 선언하지?`가 owner마다 다르게 해석될 수 있다는 점이다.
- 첫 화면만 읽어도 프로그래머, UI, 아트, QA, PM이 `내 제출물`, `내 veto 조건`, `내 승격 문장`을 바로 판단할 수 있어야 한다.

## 한 줄 목표
문서 세트를 `읽기 자료`에서 한 단계 더 밀어 **단계별 승인 사다리(signoff ladder)** 로 바꿔, half-cutover가 review drift 때문에 완료처럼 기록되는 일을 막는다.

## 왜 이 문서가 필요한가
지금 DiceSpell에는 이미 충분한 문서가 있다.

- `runtime commit stack`, `handoff scorecard`, `owner workboard`, `cutover gate`, `execution reporting`, `gap ledger`, `cutover readiness board`
- `B-1~B-4 packet`, `onboarding runtime stack`, `acceptance matrix`, `boundary packet`, `owner handoff packet`
- `Commit 1/2 surgery sheet`, `visual asset packet`, `content deck`, `contract examples`

그런데 실제 제작 턴에서 마지막으로 흔들리기 쉬운 것은 아래 네 가지다.

1. 프로그래머가 smoke green을 만들었는데, QA가 `floor green`과 `runtime-ready green`을 같은 무게로 기록한다.
2. UI/아트가 `검토 완료`를 `사인오프 완료`처럼 말해 Commit 2나 B-track을 너무 일찍 연다.
3. PM이 evidence bundle은 맞게 받았지만, 누가 다음 단계를 열 권한이 있는지 분명하지 않아 큰 문장으로 축약한다.
4. run log / tracker / 포털 / 커밋 메시지가 서로 다른 단계 언어를 써서 다음 implementer가 실제 baseline을 오판한다.

즉 지금 필요한 것은 새 설계가 아니라, **단계마다 누가 먼저 확인하고 누가 마지막으로 green을 선언하는지**를 같은 문장으로 고정하는 것이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. 단계별 사인오프 래더`, `3-1. 프로그래머 handoff` | `runtime commit stack`, `commit1/commit2 surgery sheet`, `B-1~B-4 packet` | 내 제출물이 green을 의미하는지, 아니면 아직 review-ready까지만 의미하는지 구분한다 |
| UI | `2. 단계별 사인오프 래더`, `3-2. UI handoff` | `a3_ui_packet`, `screen packet`, `boundary packet` | 내가 지금 해야 할 일은 closing인지 sanity review인지 판정한다 |
| 아트 | `2. 단계별 사인오프 래더`, `3-3. 아트 handoff` | `visual asset packet`, `content deck` | token/accent sanity와 실제 signoff를 같은 문장으로 섞지 않는다 |
| QA | `2. 단계별 사인오프 래더`, `3-4. QA handoff`, `5. 리스크 & 후속과제` | `handoff scorecard`, `acceptance matrix`, `execution reporting packet` | 어떤 증거가 있어야 다음 단계를 열어 줄 수 있는지 owner별로 판정한다 |
| PM/기획 | `1. first screen 판정`, `2. 단계별 사인오프 래더`, `4. 기록 문장 규칙` | `cutover readiness board`, `owner handoff packet`, `development tracker` | green 선언권과 다음 단계 개방권을 같은 문장으로 관리한다 |

---

## 1. first screen 판정

### 이 문서를 왜 열어야 하나
- 지금 질문은 `무슨 문서를 더 읽지?`가 아니라 **`누가 signoff를 끝내야 다음 단계가 열리는가?`** 다.
- 문서가 충분한 상태일수록 `검토 완료`, `review-ready`, `green`, `runtime-ready`가 섞이기 쉽다.
- 이 문서는 그 네 단어를 단계별로 다시 분리한다.

### 가장 짧은 답
- **Commit 1 floor**: 프로그래머가 제출하고, QA가 non-change line까지 확인하고, PM이 `floor green`을 기록해야만 Commit 2가 열린다.
- **Commit 2 closing**: 프로그래머 + UI + 아트 + QA의 closing evidence가 모인 뒤 PM이 `runtime-ready closing green`을 기록해야만 B-1이 열린다.
- **B-1~B-4**: 각 surface는 `프로그래머 제출 -> UI/아트 sanity -> QA acceptance -> PM green 기록` 순서로만 닫는다.
- **지금 금지**: reviewer 메모만 있고 PM green이 없는 상태에서 다음 단계를 여는 것.

### 한 줄 판정 규칙
- `검토 완료`는 다음 단계를 열지 못한다.
- `QA pass + PM green`만 다음 단계를 연다.
- stage를 여는 권한은 항상 **이전 stage의 마지막 green 문장**에 묶여 있다.

---

## 2. 단계별 사인오프 래더

## 3줄 요약
- 아래 표는 각 단계의 `제출자 -> 필수 동반 리뷰어 -> 마지막 green 기록자 -> 다음 단계 개방 조건`을 같은 표로 묶는다.
- 핵심은 산출물을 많이 모으는 것이 아니라, **누가 stop signal을 낼 수 있고 누가 next-step unlock을 쥐는지**를 고정하는 것이다.
- 이 표가 없으면 half-cutover는 주로 코드보다 기록에서 먼저 생긴다.

### 한 줄 목표
각 단계를 `작업 단위`가 아니라 **signoff 단위**로 읽히게 만든다.

| 단계 | 1차 제출자 | 필수 동반 리뷰어 | 마지막 green 기록자 | 다음 단계 개방 조건 | 아직 금지 |
| --- | --- | --- | --- | --- | --- |
| Commit 1 floor | 프로그래머 | QA, PM | PM | `S1/S2/S3/S7/S8/S9/S10/S11` + non-change line + `floor green` 문장 | UI/아트 closing, resolve, B-track 개방 |
| Commit 2 closing | 프로그래머 | UI, 아트, QA, PM | PM | `S4/S5/S6/S10/S12` + scene/resolve evidence + `runtime-ready closing green` 문장 | B-2~B-4 동시 개방, ending에 overrun flavor 혼입 |
| B-1 library | 프로그래머 | UI, QA, PM | PM | B1 acceptance/recovery + `library primer green` 문장 | B-2 동시 구현, shelf 리디자인 |
| B-2 battle | 프로그래머 | UI, QA, PM | PM | B2 acceptance/recovery + `battle primer green` 문장 | B-3 동시 구현, 전투 규칙/카피 전면 수정 |
| B-3 reward/shop | 프로그래머 | UI, QA, PM | PM | B3 acceptance/recovery + `reward/shop primer green` 문장 | B-4 동시 구현, 가격/규칙 조정 혼입 |
| B-4 ending | 프로그래머 | UI, 아트, QA, PM | PM | B4 acceptance/recovery + boundary evidence + `ending primer green` 문장 | overrun flavor를 ending helper가 흡수하는 것 |

### 단계별 한 줄 해석
- **Commit 1**: `코드 제출`보다 `아직 안 바뀐 것 증명`이 더 중요하다.
- **Commit 2**: `보이기 시작함`이 아니라 `1회 적용 + reload guard`까지 묶여야 green이다.
- **B-1~B-4**: 각 surface는 signoff를 따로 받는다. `온보딩 green` 같은 큰 문장은 없다.

---

## 3. 역할별 handoff 포인트

## 3-1. 프로그래머에게 넘길 것

### 3줄 요약
- 프로그래머는 `내 diff가 맞다`에서 멈추면 안 되고, **이 diff가 어느 단계 green까지 의미하는가**를 같이 넘겨야 한다.
- Commit 1/2와 B-track은 모두 파일 경계보다 단계 경계가 먼저다.
- 즉 코드를 닫는 것과 단계를 여는 것은 다른 일이다.

**한 줄 목표:** 프로그래머 제출물은 항상 `코드 diff + smoke 번호 + 아직 금지된 범위`를 함께 가진다.

| 단계 | 프로그래머 제출물 | 꼭 같이 적을 문장 | 제출만으로 안 되는 것 |
| --- | --- | --- | --- |
| Commit 1 | enter wrapper, snapshot floor, recovery, smoke 번호 | `reward apply / page advance / onboarding은 아직 열지 않았다.` | Commit 2 개방 선언 |
| Commit 2 | scene card, confirm resolve, resolved guard, smoke 번호 | `다음은 B-1만 연다.` | B-1 개방 선언 없이 온보딩 착수 |
| B-1 | hero-primer, seen flag, recovery | `library surface만 닫았다.` | battle/reward/shop까지 묶은 보고 |
| B-2 | entry/first-spell/auto-pass primer | `battle surface만 닫았다.` | reward/shop 동시 착수 |
| B-3 | reward/shop primer, 목적 분리 | `reward/shop surface만 닫았다.` | ending helper 동시 착수 |
| B-4 | ending helper, boundary 유지 | `ending은 CTA 의미만 닫았다.` | overrun flavor signoff 선언 |

---

## 3-2. UI에게 넘길 것

### 3줄 요약
- UI의 가장 큰 리스크는 `sanity review`를 `visual closing`처럼 말하는 것이다.
- Commit 1에서는 UI가 다음 턴 메모만 남기고, 실제 signoff는 Commit 2부터 시작된다.
- B-track에서도 `surface 1개 = signoff 1개` 원칙을 지켜야 한다.

**한 줄 목표:** UI는 지금 닫는 것이 `톤 sanity`인지 `실제 surface closing`인지 먼저 명확히 남긴다.

| 단계 | UI 역할 | signoff 전 체크 | veto 조건 |
| --- | --- | --- | --- |
| Commit 1 | 대기 검토 | Commit 2에서 필요한 shell 차이만 메모 | Commit 1을 visual green처럼 기록하려는 경우 |
| Commit 2 | 실제 closing 리뷰어 | `theme/enemy/none` 위계, ending과 다른 shell, CTA 가독성 | scene card가 ending 결과표처럼 읽히는 경우 |
| B-1 | 실제 closing 리뷰어 | hero-primer가 shelf 선택을 가리지 않는지 | primer가 shelf 자체를 덮는 경우 |
| B-2 | 실제 closing 리뷰어 | entry / first-spell / auto-pass가 과밀 중첩되지 않는지 | battle hint가 hero card처럼 비대해지는 경우 |
| B-3 | 실제 closing 리뷰어 | reward vs shop 목적이 문구/위계/톤 모두 분리되는지 | reward/shop이 같은 strip처럼 읽히는 경우 |
| B-4 | 실제 closing 리뷰어 | ending helper가 결과/CTA 의미만 말하는지 | overrun flavor가 ending helper로 새는 경우 |

---

## 3-3. 아트에게 넘길 것

### 3줄 요약
- 아트의 signoff는 `새 자산이 있다/없다`보다 **boundary가 유지되는가**를 보는 일에 가깝다.
- Commit 2와 B-4에서 특히 ending vs overrunEvent 차이를 지키는 것이 중요하다.
- 대부분의 단계는 대형 신규 아트보다 token/accent/backplate sanity로 닫힌다.

**한 줄 목표:** 아트는 `필요 없음`도 signoff 결과로 남기되, boundary drift는 반드시 veto한다.

| 단계 | 아트 기본 역할 | signoff 산출물 | veto 조건 |
| --- | --- | --- | --- |
| Commit 1 | 대기 검토 | Commit 2용 token/accent sanity 메모 | Commit 1에서 새 카드 마감을 시작하는 경우 |
| Commit 2 | closing sanity | `artTokenSet` / `accentKey` / neutral `none` tone 확인 | ending과 same-card 톤으로 수렴하는 경우 |
| B-1 | 경량 sanity | library primer badge/highlight 메모 | shelf 리뉴얼이 같이 섞이는 경우 |
| B-2 | 경량 sanity | battle inline token/icon 메모 | 대형 전투 연출 자산 요구가 signoff를 밀어내는 경우 |
| B-3 | 경량 sanity | reward/shop accent 분리 메모 | shop이 reward 기대 카드처럼 읽히는 경우 |
| B-4 | closing sanity | result-helper 절제 톤 메모 | overrun flavor asset이 ending으로 넘어오는 경우 |

---

## 3-4. QA에게 넘길 것

### 3줄 요약
- QA는 이제 `보임/안 보임`보다 **다음 단계 개방권을 줄 수 있는가**를 판정하는 역할이다.
- 따라서 QA pass는 단순 검수 완료가 아니라 step unlock의 일부다.
- 특히 Commit 1과 Commit 2는 smoke가 겹쳐 보여도 green 의미가 다르다.

**한 줄 목표:** QA는 각 단계에서 `pass`뿐 아니라 `next step unlock 가능/불가`를 같이 적는다.

| 단계 | QA 필수 evidence | pass 문장에 꼭 들어갈 것 | veto 조건 |
| --- | --- | --- | --- |
| Commit 1 | `S1/S2/S3/S7/S8/S9/S10/S11`, 상태 diff, non-change line | `Commit 2만 열 수 있음` | resolve나 page advance가 섞였을 때 |
| Commit 2 | `S4/S5/S6/S10/S12`, DOM/시각 evidence, resolve 1회 적용 | `B-1만 열 수 있음` | old baseline 잔존, 중복 적용, ending과 시각 경계 붕괴 |
| B-1 | B1 acceptance/recovery | `B-2만 열 수 있음` | battle primer가 같이 열렸을 때 |
| B-2 | B2 acceptance/recovery | `B-3만 열 수 있음` | reward/shop surface까지 같이 바뀌었을 때 |
| B-3 | B3 acceptance/recovery | `B-4만 열 수 있음` | 가격/규칙 조정까지 섞였을 때 |
| B-4 | B4 acceptance/recovery + boundary evidence | `온보딩 전체가 아니라 ending만 닫힘` | overrun flavor가 ending에 섞였을 때 |

---

## 3-5. PM/기획에게 넘길 것

### 3줄 요약
- PM은 이제부터 `문서가 충분하다`와 `단계가 green이다`를 절대 같은 문장으로 쓰면 안 된다.
- PM green은 다음 단계 개방권이므로, 가장 큰 책임은 단계명 관리다.
- big sentence 하나가 설계보다 더 큰 drift를 만든다.

**한 줄 목표:** PM은 각 단계의 마지막 green 문장을 관리해 범위 드리프트를 막는다.

### PM 체크포인트
1. `QA pass`가 있어도 PM green이 기록되기 전에는 다음 단계를 열지 않는다.
2. PM green 문장에는 항상 단계명이 들어간다.
3. `오버런 완료`, `온보딩 진행`, `거의 완료` 같은 큰 문장은 금지한다.
4. stop signal이 보이면 새 일감 추가보다 기록 정정이 먼저다.

---

## 4. 기록 문장 규칙

## 3줄 요약
- signoff ladder는 결국 기록 문장으로 증명된다.
- 같은 evidence가 있어도 `누가 green을 기록했는가`가 없으면 다음 단계는 열리지 않는다.
- 따라서 tracker / run log / commit note는 단계별 문장이 동일해야 한다.

**한 줄 목표:** 각 단계는 `QA pass 문장 1개 + PM green 문장 1개 + append log 1개`로 닫는다.

| 단계 | QA pass 문장 | PM green 문장 |
| --- | --- | --- |
| Commit 1 | `Commit 1 floor evidence pass. Commit 2만 열 수 있음.` | `이번 턴은 overrunEvent Commit 1 floor green이다.` |
| Commit 2 | `Commit 2 closing evidence pass. B-1만 열 수 있음.` | `이번 턴은 overrunEvent Commit 2 runtime-ready closing green이다.` |
| B-1 | `B-1 acceptance pass. B-2만 열 수 있음.` | `이번 턴은 B-1 library primer green이다.` |
| B-2 | `B-2 acceptance pass. B-3만 열 수 있음.` | `이번 턴은 B-2 battle primer green이다.` |
| B-3 | `B-3 acceptance pass. B-4만 열 수 있음.` | `이번 턴은 B-3 reward/shop primer green이다.` |
| B-4 | `B-4 acceptance pass. ending boundary 유지.` | `이번 턴은 B-4 ending primer green이다.` |

### 기록 규칙
- QA pass가 없으면 PM green이 없다.
- PM green이 없으면 다음 단계 개방이 없다.
- run log는 항상 새 entry를 끝에 append하고, old entry를 signoff 정리 명목으로 대형 rewrite 하지 않는다.

---

## 5. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| reviewer 메모와 실제 signoff가 같은 말로 남아 단계가 너무 일찍 열리는 위험 | half-cutover가 다시 `완료`처럼 보인다 | 이번 signoff ladder로 `review-ready / QA pass / PM green / next unlock`을 분리했다 | 다음 실제 구현 슬롯 직전 |
| Commit 1에서 UI/아트의 sanity 메모가 closing 승인처럼 기록되는 위험 | Commit 2 의미가 약해지고 old baseline 위에서 작업이 열린다 | Commit 1은 UI/아트 `검토만`, Commit 2부터 `closing signoff`로 못 박았다 | Commit 1 evidence 정리 직전 |
| B-track이 surface별이 아니라 `온보딩 진행` 한 문장으로 기록되는 위험 | B-1~B-4 경계가 무너지고 재작업 범위가 커진다 | 각 surface에 `QA unlock 문장`과 `PM green 문장`을 따로 고정했다 | 각 B-step 종료 직전 |
| PM green 없이 다음 단계가 암묵적으로 열리는 위험 | tracker/run log/포털/커밋 메시지가 서로 다른 baseline을 가리킨다 | `QA pass + PM green`이 없으면 next unlock이 없다는 규칙을 first screen에 명시했다 | tracker/run log 작성 직전 |

---

## 6. 즉시 다음 액션

1. 다음 implementer는 `cutover readiness board -> execution reporting packet -> 이 signoff ladder` 순서로 읽고, `누가 green을 선언해야 하는가`부터 잠근다.
2. Commit 1 슬롯에서는 프로그래머 제출 후 QA가 `Commit 2만 열 수 있음` 문장을 먼저 남기고, 그 다음 PM이 `floor green`을 기록한다.
3. Commit 2 슬롯에서는 UI/아트가 scene card closing sanity를 signoff하고 QA가 unlock을 준 뒤 PM이 B-1만 연다.
4. B-track은 각 surface마다 `프로그래머 제출 -> UI/아트 sanity -> QA unlock -> PM green` 순서를 지키고, 단계 둘 이상을 한 번에 green 처리하지 않는다.
5. 막히면 새 문서를 더 쓰기보다 `어느 signoff가 아직 안 모였는가`를 짧게 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md`
- `docs/dicespell_overrun_onboarding_owner_handoff_packet_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
