# DiceSpell `overrunEvent` 컷오버 게이트 패킷

## 3줄 요약
- 이 문서는 `overrunEvent` A-track과 첫 런 온보딩 B-track 사이에서 실제 구현자가 가장 쉽게 무너뜨리는 마지막 실행 병목, 즉 **"이번 턴을 시작해도 되는가 / 여기서 멈춰야 하는가 / 누구 승인을 받고 다음 단계로 넘어가는가"**를 한 장으로 고정하는 source-of-truth다.
- 프로그래머는 착수 전 go/no-go 게이트와 stop 조건을, UI/아트는 지금이 대기 턴인지 closing 턴인지를, QA/PM은 어떤 증거 묶음이 있어야 다음 단계 green을 허용할지 먼저 보면 된다.
- 목표는 새 설계를 더하는 것이 아니라, 이미 닫힌 `runtime patch map`, `fixture ledger`, `owner handoff`, `onboarding stack` 문서를 **실제 착수/중단/인계 규칙**으로 다시 압축해 실행 드리프트를 막는 것이다.

**한 줄 목표:** `Commit 1 floor -> Commit 2 closing -> B-1/B-4` 순서에 대해 **착수 게이트 / 중단 게이트 / 인계 게이트**를 고정해, 문서가 충분한 상태에서도 범위 부풀리기와 과장된 green 기록을 막는다.

## 왜 이 문서가 필요한가
- 지금 DiceSpell의 문제는 더 이상 `무슨 문서를 써야 하지?`가 아니다. 오히려 문서는 충분한데, 실제 작업 순간에 `좋아, 그럼 지금 바로 코드를 열어도 되나?`, `이건 Commit 2에서 해야 하나, B-track에서 해야 하나?`, `이 증거로 다음 단계 green을 말해도 되나?`가 다시 사람 머릿속에서 합쳐진다.
- `runtime handoff scorecard`는 커밋별 체크리스트를 준다.
- `runtime fixture ledger`는 `S1~S12`와 증거 묶음을 준다.
- `owner workboard`와 `owner handoff packet`은 역할별 전달 묶음을 준다.
- 그런데 착수 직전에는 여전히 **go/no-go 판정**, **멈춤 기준**, **다음 단계 개방 조건**이 한 화면에 안 모여 있다.
- 이 패킷은 그 마지막 운영 공백을 줄이기 위해 만든다.

---

## 이 문서를 먼저 봐야 하는 사람
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. 단계별 게이트 표`, `2. Commit 1/2 착수 게이트`, `3. stop 게이트` | `runtime patch map`, `runtime fixture ledger` | 지금 턴에 시작 가능한 범위와 멈춰야 할 범위를 다시 추론하지 않는다 |
| UI | `1. 단계별 게이트 표`, `4. 역할별 승인 게이트` | `A-3 UI packet`, `ending_overrun_boundary_packet` | Commit 1은 대기/메모 턴이고 Commit 2만 closing 턴임을 즉시 확인한다 |
| 아트 | `1. 단계별 게이트 표`, `4. 역할별 승인 게이트` | `content deck`, `event_screen_packet` | 새 자산 제작 시작선과 drift sanity 검수선을 구분한다 |
| QA | `3. stop 게이트`, `4. 역할별 승인 게이트`, `5. 복붙용 게이트 문장` | `first/second runtime review`, `smoke migration packet` | stop/fail과 next-green 허용 문장을 같은 언어로 남긴다 |
| PM/기획 | `0. first screen 결론`, `5. 복붙용 게이트 문장`, `6. 리스크 & 후속과제` | `cutover master plan`, `owner handoff packet` | Commit 1/2와 B-track 개방을 같은 문장으로 섞지 않는다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `지금 이 턴은 시작해도 되는가?`
- `어느 신호가 나오면 바로 멈추고 다음 트랙을 열지 말아야 하는가?`
- `어떤 증거가 모여야 다음 단계 green을 허용할 수 있는가?`

### 가장 짧은 답
- **Commit 1 착수 전**에는 `runway -> runtime patch map -> smoke migration packet -> runtime fixture ledger`를 먼저 열고, `S1/S2/S3/S7~S11`만 이번 턴 픽스처로 잠가야 한다.
- **Commit 2 착수 전**에는 Commit 1이 실제로 `floor green` 문장과 증거 묶음까지 남겼는지 확인해야 하며, `S4/S5/S6/S10/S12` 외 범위는 열면 안 된다.
- **B-track 착수 전**에는 Commit 2가 `runtime-ready closing green`으로 닫혔는지, `ending`과 `overrunEvent` 경계가 유지되는지, `surface 1개 = commit 1개` 규칙이 다시 잠겼는지 확인해야 한다.
- 중간에 아래 stop 신호가 하나라도 나오면 **문서 업데이트 후 중단**이 맞다.
  - Commit 1인데 reward apply / page advance가 열림
  - Commit 2인데 onboarding primer를 같이 열려고 함
  - B-track인데 여러 surface를 한 커밋에 묶으려 함
  - tracker/run log에 `floor`/`closing`/`B-1~B-4`를 섞어 쓰려 함

---

## 1. 단계별 컷오버 게이트 한눈표

| 단계 | 착수 게이트 | 이번 단계에서 닫아야 하는 것 | stop 게이트 | 다음 단계 개방 조건 |
| --- | --- | --- | --- | --- |
| Commit 1 | `runway`, `runtime patch map`, `smoke migration`, `fixture ledger` 선독 + `S1/S2/S3/S7~S11` 잠금 | enter wrapper, canonical snapshot, normalize/recovery, non-change evidence | reward apply/page advance/cleanup/onboarding helper 시도 | `floor green` 문장 + smoke + 상태 diff + S번호 명시 |
| Commit 2 | Commit 1 `floor green` 기록 존재 + `S4/S5/S6/S10/S12` 잠금 | scene card, confirm resolve, `resolved` guard, old baseline 제거, S12 visual sanity | onboarding primer 동시 착수, 새 reward 타입, 전역 카드 리디자인 | `runtime-ready closing green` 문장 + full smoke + DOM/시각 evidence |
| B-1 | Commit 2 green + boundary packet 재확인 | library primer only | battle/reward/shop/ending primer 동시 착수 | `library primer green` 문장 |
| B-2 | B-1 green + battle packet 선독 | battle primer only | reward/shop/ending 선착수 | `battle primer green` 문장 |
| B-3 | B-2 green + reward/shop packet 선독 | reward/shop primer only | ending primer 동시 착수 | `reward/shop primer green` 문장 |
| B-4 | B-3 green + boundary packet 재확인 | ending primer only | overrun flavor 흡수, onboarding 전체 polish | `ending primer green` 문장 |

### 한 줄 규칙
- **단계 하나당 커밋 하나**다.
- **단계 하나당 증거 묶음 하나**다.
- **단계 하나당 green 문장 하나**다.

---

## 2. Commit 1 / Commit 2 착수 게이트

## 2-1. Commit 1 착수 게이트 — enter / snapshot floor

### 3줄 요약
- Commit 1은 `overrunEvent`를 실제 장면으로 닫는 턴이 아니라, **안전하게 들어가고 저장하고 복구하는 바닥**을 닫는 턴이다.
- 따라서 이 단계에서 중요한 것은 `무엇을 더 보여 주는가`보다 `무엇을 아직 일부러 하지 않는가`를 같이 잠그는 일이다.
- 시작 전에 `이번 턴 픽스처 = S1/S2/S3/S7/S8/S9/S10/S11`이 아니면 이미 범위가 흔들린 상태로 본다.

**한 줄 목표:** `phase='overrunEvent'` 진입과 canonical snapshot floor만 닫고, resolve 의미는 Commit 2까지 보류한다.

### 시작 전에 체크할 것
- `runway`, `runtime patch map`, `smoke migration`, `runtime fixture ledger`, `first runtime delivery packet`을 열었다.
- `이번 턴 픽스처`가 `S1/S2/S3/S7/S8/S9/S10/S11`로 잠겨 있다.
- tracker/run log에 쓸 예정 문장이 `floor green`으로 고정돼 있다.
- UI/아트는 이번 턴이 closing이 아니라 payload sanity 턴이라는 것을 알고 있다.

### Commit 1에서 허용되는 것
- `continueOverrun()` enter wrapper 또는 동등 진입 구조화
- canonical field set snapshot 저장
- invalid/missing/legacy payload normalize
- `none` fallback 수렴
- `resolved` 상태 안전 읽기 바닥
- 누락 카피 재주입처럼 `S11`에 직접 속하는 safety 보강

### Commit 1에서 금지되는 것
| 금지 | 왜 stop 신호인가 |
| --- | --- |
| reward apply | Commit 2 resolve 의미를 미리 열어 half-cutover를 완성처럼 보이게 만든다 |
| `currentPage` 전진 | non-change evidence가 깨진다 |
| `segmentTrophyTemplateIds` cleanup | resolve 전/후 책임이 섞인다 |
| scene card polish | UI/아트 closing 범위가 조기 개방된다 |
| onboarding helper/state 추가 | A-track/B-track 경계가 무너진다 |

### Commit 1 인계 게이트
아래가 모두 있어야 Commit 2를 열 수 있다.
1. `floor green` 문장
2. `S1/S2/S3/S7/S8/S9/S10/S11`이 명시된 smoke 결과
3. 상태 diff 메모 (`reward`/`currentPage`/`pagePlan`/`segmentTrophyTemplateIds` 미변경)
4. `Commit 2에서만 resolve/UI closing을 연다`는 다음 액션 문장

---

## 2-2. Commit 2 착수 게이트 — scene / resolve closing

### 3줄 요약
- Commit 2는 Commit 1 snapshot을 display-only로 소비해 **장면과 적용 시점**을 닫는 턴이다.
- 이 단계에서만 UI/아트 closing, confirm 1회 적용, old baseline 제거가 열린다.
- 시작 전에 `Commit 1 floor green evidence`가 없으면 Commit 2는 열지 않는 것이 맞다.

**한 줄 목표:** `renderOverrunEvent(summary)`와 `resolveOverrunEvent(state)`를 closing handoff로 닫고, 그 결과를 `runtime-ready closing green`으로 기록한다.

### 시작 전에 체크할 것
- Commit 1 evidence bundle이 tracker/run log와 실제 변경분에 남아 있다.
- `이번 턴 픽스처`가 `S4/S5/S6/S10/S12`로 잠겨 있다.
- UI/아트/QA가 Commit 2를 closing 턴으로 읽고 있다.
- `ending`은 끝까지 결과/CTA 의미만, flavor는 `overrunEvent`가 맡는다는 경계가 다시 확인됐다.

### Commit 2에서 허용되는 것
- `renderOverrunEvent(summary)` 장면 카드 위계 닫기
- `themeTrophy` / `enemyTrophy` / `none` 3축 CTA/footnote sanity
- confirm 시 reward 1회 적용
- `resolved` reload / reclick no-op guard
- old immediate baseline 제거

### Commit 2에서 금지되는 것
| 금지 | 왜 stop 신호인가 |
| --- | --- |
| onboarding primer 동시 착수 | A-track closing과 B-track 원인이 섞인다 |
| 새 reward 타입 추가 | 검증 범위가 바뀐다 |
| 전역 카드 시스템 리뉴얼 | S12 visual sanity 검증이 과도하게 커진다 |
| ending에 overrun flavor 복제 | boundary 합의가 깨진다 |

### Commit 2 인계 게이트
아래가 모두 있어야 B-track을 열 수 있다.
1. `runtime-ready closing green` 문장
2. `S4/S5/S6/S10/S12`가 명시된 full smoke 결과
3. `theme/enemy/none` DOM 또는 시각 evidence
4. `resolved` no-op와 old baseline 제거 메모
5. 다음은 `B-1 library primer`만 연다는 문장

---

## 2-3. B-track 착수 게이트 — surface 1개 = commit 1개

### 한 줄 목표
A-track closing 이후에만 `B-1 -> B-2 -> B-3 -> B-4`를 **surface별 독립 handoff**로 연다.

### B-track 공통 착수 규칙
- A-track Commit 2 green이 없으면 B-track은 시작하지 않는다.
- 각 단계 시작 전 해당 packet / scorecard / owner workboard를 다시 연다.
- 단계명과 다른 surface를 같은 커밋에 넣으려 하면 stop한다.
- `ending`은 B-4에서도 결과/CTA 의미만 맡고, `overrunEvent` flavor를 흡수하지 않는다.

### B-track 공통 stop 신호
- `library+battle`, `reward/shop+ending`처럼 두 surface를 합쳐 닫으려 함
- seen state/helper를 다음 surface까지 한꺼번에 심으려 함
- tracker/run log 문장이 `온보딩 green`처럼 surface를 뭉뚱그림
- boundary packet을 열지 않은 채 ending helper tone을 바꾸려 함

---

## 3. stop 게이트 — 여기서 멈추고 문서를 먼저 갱신해야 하는 경우

## 3-1. 프로그래머 stop 게이트
| 신호 | 의미 | 즉시 해야 할 일 |
| --- | --- | --- |
| `이번엔 S12도 같이 가자`가 나옴 | Commit 1/2 범위가 섞였다 | 커밋 범위 축소, fixture ledger 재확인 |
| `onboarding도 간단하니 같이`가 나옴 | A/B-track 경계가 무너졌다 | master plan / owner handoff packet 재확인 |
| smoke는 green인데 상태 diff가 커짐 | hidden side effect가 생겼다 | tracker보다 코드/검증 원인 먼저 고정 |
| render가 branch를 재추론함 | source-of-truth drift | content deck / state matrix 재확인 |

## 3-2. UI / 아트 stop 게이트
| 신호 | 의미 | 즉시 해야 할 일 |
| --- | --- | --- |
| Commit 1에서 shell을 마감하려 함 | closing 턴 조기 개방 | Commit 2까지 대기, payload sanity 메모만 남김 |
| `none` branch를 빈 상태로 처리하려 함 | fallback 장면 의미 훼손 | A-3 UI packet / boundary packet 재확인 |
| ending과 overrunEvent를 같은 카드 표정으로 맞추려 함 | 경계 침범 | boundary packet 기준으로 분리 복구 |
| 신규 key art가 gating 조건처럼 올라옴 | 범위 과대화 | token/accent drift 검수로 범위 축소 |

## 3-3. QA / PM stop 게이트
| 신호 | 의미 | 즉시 해야 할 일 |
| --- | --- | --- |
| `거의 완료`, `오버런 green`, `온보딩 green` 같은 문장이 나옴 | 단계 언어가 흐려짐 | 단계명(`floor`, `closing`, `B-1~B-4`)으로 문장 교체 |
| S번호 없이 green 기록을 남기려 함 | 증거 번들이 흐려짐 | fixture ledger 기준으로 재기록 |
| Commit 1 evidence로 Commit 2 착수를 허용하려 함 | closing 기준 미충족 | second runtime review 재확인 |
| B-4 전에 ending boundary 재확인 없이 진행하려 함 | shared surface drift | boundary packet 선독 후 재판정 |

---

## 4. 역할별 승인 게이트

| 역할 | 다음 단계 green을 허용하려면 확인해야 하는 것 | 허용하면 안 되는 것 |
| --- | --- | --- |
| 프로그래머 | 이번 단계 문서 묶음 선독 + 해당 단계 픽스처 번호 잠금 + 금지 범위 확인 | `문서 충분하니 그냥 구현`식 착수 |
| UI | 이번 턴이 sanity 턴인지 closing 턴인지 명확함 | Commit 1에서 shell 완성 판정 |
| 아트 | token/accent sanity와 자산 범위가 분리돼 있음 | 신규 자산이 없으면 handoff 불가라고 판단 |
| QA | S번호와 단계 문장이 같이 붙은 evidence bundle | `보인다`만으로 green 허용 |
| PM/기획 | tracker/run log 문장에 단계명과 다음 허용 단계가 둘 다 적힘 | 여러 단계 green을 한 문장에 합치기 |

### 승인 문장 규칙
- 문장에는 반드시 **단계명**이 들어간다.
- 문장에는 가능하면 **S번호 또는 surface 이름**이 들어간다.
- 문장에는 반드시 **다음에 열 수 있는 단계 하나**만 적는다.

예시:
- `이번 턴은 overrunEvent floor green이며, 다음은 Commit 2 closing만 연다.`
- `이번 턴은 overrunEvent runtime-ready closing green이며, 다음은 B-1 library primer만 연다.`
- `이번 턴은 reward/shop primer green이며, 다음은 B-4 ending primer만 연다.`

---

## 5. 복붙용 게이트 문장

### Commit 1 착수 전
> 이번 턴은 `overrunEvent` Commit 1만 연다. `S1/S2/S3/S7/S8/S9/S10/S11` 외 범위는 닫고, 목표는 enter/snapshot floor와 recovery evidence다.

### Commit 1 종료 시
> 이번 턴은 `overrunEvent` floor green이다. `S1/S2/S3/S7/S8/S9/S10/S11`을 같은 증거 묶음으로 닫았고, reward apply / page advance / onboarding은 아직 열지 않았다.

### Commit 2 착수 전
> 이번 턴은 `overrunEvent` Commit 2만 연다. Commit 1 floor evidence를 전제로 `S4/S5/S6/S10/S12`만 닫고, onboarding B-track은 아직 열지 않는다.

### Commit 2 종료 시
> 이번 턴은 `overrunEvent` runtime-ready closing green이다. `S4/S5/S6/S10/S12`를 같은 증거 묶음으로 닫았고, 다음은 `B-1 library primer`만 연다.

### B-track 공통
> 이번 턴은 `{surface}` primer green이다. 다음은 `{다음 surface}`만 열고, 다른 surface 증거는 같은 번들에 섞지 않는다.

---

## 6. 리스크 & 후속과제
- 문서 세트가 충분해질수록, 역설적으로 구현자는 `이 정도면 같이 해도 되겠지`라는 압박을 받기 쉽다. 이번 게이트 패킷은 그 심리적 범위 확장을 막는 장치지만, 실제 코드 컷오버 전까지는 여전히 drift 위험이 남아 있다.
- `S10`처럼 양쪽 커밋에 걸친 guard 픽스처는 기록이 특히 쉽게 흐려진다. tracker/run log에는 `Commit 1의 S10`과 `Commit 2의 S10`을 같은 말로 쓰지 않도록 계속 강제해야 한다.
- B-track은 문서상 준비가 끝났지만, A-track closing 전에는 열지 않는 discipline 자체가 핵심 리스크다. 즉 blocker는 아이디어 부족이 아니라, **문서가 충분한 상태에서도 멈출 줄 아는가**다.
- 후속 구현 슬롯은 새 문서 추가보다 실제 A-track 코드 컷오버가 우선이다. 다만 착수 전에는 이번 게이트 패킷을 먼저 열어 go/no-go를 잠그고, 중간에 stop 신호가 나오면 기능 추가보다 tracker/로그 정정을 먼저 해야 한다.

## 7. 즉시 다음 액션
1. `runway -> runtime patch map -> smoke migration packet -> runtime fixture ledger -> cutover gate packet` 순서로 착수 게이트를 잠근다.
2. Commit 1에서는 `S1/S2/S3/S7/S8/S9/S10/S11` 외 범위를 닫고 floor evidence만 남긴다.
3. Commit 2에서는 `S4/S5/S6/S10/S12`만 닫고 runtime-ready closing 문장을 남긴다.
4. Commit 2 green 이후에만 `onboarding runtime stack -> onboarding handoff scorecard -> onboarding owner workboard -> B-1 packet` 순서로 B-track을 연다.
