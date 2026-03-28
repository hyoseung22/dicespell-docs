# DiceSpell `overrunEvent` / 첫 런 온보딩 실행 기록 계약 패킷

## 3줄 요약
- 이 문서는 `overrunEvent` A-track Commit 1/2와 첫 런 온보딩 B-track B-1~B-4가 실제로 진행될 때, **tracker / automation run log / 공개 포털 / git 기록이 서로 같은 단계 언어를 쓰게 만드는 실행 기록 source-of-truth**다.
- 프로그래머는 이번 턴에 어떤 evidence를 남겨야 하는지, QA/PM은 어떤 문장을 tracker/run log에 그대로 써야 하는지, UI/아트는 어떤 단계에서 아직 `검수 대기`인지와 언제 `시각 마감`으로 기록되는지를 먼저 보면 된다.
- 목표는 새 설계를 더하는 것이 아니라, 이미 닫힌 `runtime commit stack`, `handoff scorecard`, `owner workboard`, `cutover gate`, `automation run log` 운용 규칙을 **기록 계약**으로 다시 압축해 half-cutover가 완료처럼 보이는 drift를 막는 것이다.

**한 줄 목표:** `Commit 1 floor -> Commit 2 closing -> B-1/B-4`의 실제 구현/검수/기록을 같은 단계명, 같은 evidence 묶음, 같은 append-only 로그 규칙으로 고정한다.

## 왜 이 문서가 필요한가
- 지금 DiceSpell의 가장 큰 문서 공백은 `무엇을 구현하지?`가 아니라 `좋아, 이 단계가 끝났다면 어디에 어떤 말로 남기지?`다.
- A-track/B-track은 이미 packet/summary/scorecard/workboard까지 충분히 닫혀 있다.
- 그런데 실제 구현이 시작되면 아래 세 가지가 다시 흔들리기 쉽다.
  1. Commit 1 `floor`와 Commit 2 `closing`이 tracker/run log에서 같은 `오버런 green` 문장으로 뭉개짐
  2. B-track surface별 green이 `온보딩 진행` 같은 큰 문장으로 합쳐짐
  3. run log를 중간 수정하려다 append 규칙이 깨지거나, 포털/커밋/증거가 서로 다른 단계 언어를 씀
- 이 패킷은 그 마지막 운영 병목을 줄이기 위해 만든다.

---

## 이 문서를 먼저 봐야 하는 사람
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. 단계별 기록 계약표`, `2. evidence 묶음 규칙` | `runtime handoff scorecard`, `runtime fixture ledger` | 코드를 닫는 순간 무엇을 같이 제출해야 하는지 다시 추론하지 않는다 |
| UI | `1. 단계별 기록 계약표`, `3. 오너별 기록 포인트` | `A-3 UI packet`, `B-1~B-4 packet` | Commit 1은 검수 대기 턴이고 Commit 2/B-track만 시각 green을 남긴다 |
| 아트 | `3. 오너별 기록 포인트`, `4. 공개 포털 반영 규칙` | `content deck`, `boundary packet` | 자산 sanity와 실제 마감 기록 시점을 섞지 않는다 |
| QA | `2. evidence 묶음 규칙`, `5. run log append 규칙` | `first/second runtime review`, `onboarding handoff scorecard` | 단계별 smoke/evidence와 기록 문장을 같은 번들로 남긴다 |
| PM/기획 | `0. first screen 결론`, `1. 단계별 기록 계약표`, `6. 복붙용 문장` | `cutover gate packet`, `owner handoff packet` | `floor`, `closing`, `B-1~B-4`를 절대 큰 green 문장으로 합치지 않는다 |
| 자동화 writer | `5. run log append 규칙`, `6. 복붙용 문장`, `7. 리스크 & 후속과제` | `automation run log`, `development tracker` | 대형 exact-text rewrite 대신 append와 최소 안전 수정만 허용한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `이번 턴이 Commit 1인지 Commit 2인지, 아니면 B-1~B-4인지 어디에 어떻게 남겨야 하지?`
- `tracker와 run log에 같은 말을 어떻게 남기지?`
- `public portal과 git commit은 어느 단계에서 같이 묶고, 언제 보류하지?`

### 가장 짧은 답
- 모든 단계는 **단계명 1개 + evidence 묶음 1개 + run log entry 1개 + tracker 변경 1개**로 닫는다.
- `Commit 1 floor`, `Commit 2 closing`, `B-1`, `B-2`, `B-3`, `B-4`는 서로 다른 단계이며 같은 완료 문장으로 기록하면 안 된다.
- run log는 항상 **끝에 새 timestamped entry를 append**한다. 이전 entry를 큰 exact-text 치환으로 다시 쓰지 않는다.
- tracker는 snapshot / blocker / 리스크 & 후속과제 / 변경 이력 중 실제로 달라진 부분만 안전하게 갱신한다.
- public portal 재배포와 git commit은 **의미 있는 문서 변화가 tracker/index/public HTML에 반영됐을 때** 같은 사이클에서 묶는다. 검증/정합성이 부족하면 강행하지 않는다.

---

## 1. 단계별 기록 계약표

| 단계 | 구현 완료선 | 필수 evidence 묶음 | tracker에 남길 핵심 문장 | run log 첫 문장 | 포털/배포 | git 기록 |
| --- | --- | --- | --- | --- | --- | --- |
| Commit 1 floor | enter wrapper + canonical snapshot + recovery + non-change line | `S1/S2/S3/S7/S8/S9/S10/S11`, 상태 diff, non-change line | `이번 턴은 overrunEvent floor green이며 reward apply / page advance / onboarding은 아직 열지 않았다.` | `작업 주제: overrunEvent Commit 1 floor 실행 기록 정렬` | tracker/public tracker/index에 새 step 언어가 생기면 반영 | `floor` 의미가 드러나는 커밋 제목만 허용 |
| Commit 2 closing | scene card + confirm resolve + resolved guard + old baseline 제거 | `S4/S5/S6/S10/S12`, DOM/시각 evidence, resolve 1회 적용 | `이번 턴은 overrunEvent runtime-ready closing green이며 다음은 B-1 library primer만 연다.` | `작업 주제: overrunEvent Commit 2 closing 실행 기록 정렬` | public tracker/index/portal 반영 권장 | `closing` 의미가 드러나는 커밋 제목만 허용 |
| B-1 library | library primer만 노출/저장/재노출 제어 | B1 acceptance/recovery, hero-primer 위치 evidence | `이번 턴은 B-1 library primer green이며 다음은 B-2 battle primer만 연다.` | `작업 주제: onboarding B-1 library primer 실행 기록 정렬` | primer 관련 public docs 반영 시 재배포 | `library primer`가 드러나는 커밋 제목 |
| B-2 battle | battle primer/hint만 노출 | B2 acceptance/recovery, first-spell/auto-pass evidence | `이번 턴은 B-2 battle primer green이며 다음은 B-3 reward/shop primer만 연다.` | `작업 주제: onboarding B-2 battle primer 실행 기록 정렬` | 필요 시 재배포 | `battle primer`가 드러나는 커밋 제목 |
| B-3 reward/shop | reward/shop primer만 노출 | B3 acceptance/recovery, reward-vs-shop 분리 evidence | `이번 턴은 B-3 reward/shop primer green이며 다음은 B-4 ending primer만 연다.` | `작업 주제: onboarding B-3 reward/shop primer 실행 기록 정렬` | 필요 시 재배포 | `reward/shop primer`가 드러나는 커밋 제목 |
| B-4 ending | ending primer만 노출, boundary 유지 | B4 acceptance/recovery, ending-vs-overrun boundary evidence | `이번 턴은 B-4 ending primer green이며 overrun flavor는 계속 overrunEvent에 남긴다.` | `작업 주제: onboarding B-4 ending primer 실행 기록 정렬` | public docs 반영 권장 | `ending primer`가 드러나는 커밋 제목 |

### 한 줄 규칙
- **단계명 없는 green 문장은 금지**다.
- **단계와 evidence가 어긋난 run log는 실패 기록**로 본다.
- **같은 run에서 두 단계 이상을 완료처럼 쓰지 않는다.**

---

## 2. evidence 묶음 규칙

## 2-1. Commit 1 / Commit 2 evidence 묶음

### Commit 1 floor
- smoke: `S1/S2/S3/S7/S8/S9/S10/S11`
- 상태 diff: `phase='overrunEvent'`, canonical snapshot field set 존재
- non-change line: reward apply 없음 / `currentPage` 미전진 / `pagePlan` 미확장 / `segmentTrophyTemplateIds` 미정리
- 문장: `floor green`
- 다음 단계: `Commit 2 closing`만 허용

### Commit 2 closing
- smoke: `S4/S5/S6/S10/S12`
- DOM/시각 evidence: `themeTrophy` / `enemyTrophy` / `none` 카드 위계 또는 동등 evidence
- resolve evidence: confirm 1회 적용, `resolved` no-op, old immediate baseline 제거
- 문장: `runtime-ready closing green`
- 다음 단계: `B-1 library primer`만 허용

## 2-2. B-track evidence 묶음
- 각 surface는 해당 packet의 acceptance/recovery 번호만 남긴다.
- `library`, `battle`, `reward/shop`, `ending` evidence는 서로 다른 묶음이다.
- B-4에서도 `themeTrophy` / `enemyTrophy` / `none` flavor는 ending evidence에 포함하지 않는다. boundary evidence만 남긴다.

## 2-3. 제출 순서
1. smoke / acceptance 결과
2. 상태 diff 또는 UI/DOM/화면 evidence
3. 금지 범위를 열지 않았다는 메모
4. tracker 한 줄 green 문장
5. run log append entry
6. 필요 시 public tracker / index 동기화
7. 의미 있는 변경이면 git commit / push

---

## 3. 오너별 기록 포인트

| 역할 | 이번 턴에 남겨야 하는 것 | 남기면 안 되는 것 |
| --- | --- | --- |
| 프로그래머 | 단계별 smoke 번호, 상태 diff, 금지 범위 미개방 메모 | `거의 다 됨`, `오버런 완료` 같은 큰 문장 |
| UI | Commit 2/B-track에서만 shell/primer 시각 green 증거 | Commit 1 sanity 턴을 UI 마감처럼 기록 |
| 아트 | token/accent/artTokenSet sanity 또는 자산 미착수 유지 메모 | 신규 자산이 없다는 이유로 단계 미완료처럼 기록 |
| QA | 단계명 + S번호/acceptance 번호가 들어간 pass/fail 문장 | `보인다`, `정상 같음` 같은 느슨한 표현 |
| PM/기획 | tracker snapshot/blocker/리스크 갱신과 다음 허용 단계 1개 | 여러 단계를 한 번에 green으로 요약 |
| 자동화 writer | append-only run log entry, 충돌 위험이 낮은 tracker 수정, 배포/커밋 결과 | 대형 run log rewrite, 과장된 사용자 보고 |

### 역할별 handoff 한 줄 예시
- 프로그래머: `이번 턴은 Commit 1 floor만 닫았고 reward apply는 아직 열지 않았다.`
- UI: `Commit 2 closing 전까지는 shell 마감이 아니라 payload sanity만 본다.`
- QA: `S4/S5/S6/S10/S12 pass가 없으면 runtime-ready closing green을 허용하지 않는다.`
- PM: `다음 허용 단계는 B-1 library primer 하나뿐이다.`

---

## 4. tracker / public tracker / index 반영 규칙

## 4-1. tracker
### 최소 갱신 위치
- `현재 스냅샷`의 `최근 큰 변경` 또는 `다음 큰 결정`
- `운영 대시보드`의 `현재 blocker`
- `리스크 & 후속 과제`
- `변경 이력` 최상단 1행

### tracker 문장 규칙
- 문장 안에 반드시 단계명(`Commit 1 floor`, `Commit 2 closing`, `B-1`...)을 넣는다.
- 다음 단계는 항상 **하나만** 적는다.
- `새 문서 추가`가 목적일 때도 왜 이 문서가 구현/검수/기록을 쉽게 하는지 first screen에서 읽히게 쓴다.

## 4-2. public tracker / index
- `docs/development-tracker.html`은 `dicespell_development_tracker_kr.html`과 같은 사이클에 동기화한다.
- `docs/index.html`은 새 packet/summary pair가 포털 첫 화면에서 바로 찾아야 할 가치가 있을 때만 갱신한다.
- index 설명은 `무슨 파일이 추가됐는가`보다 `누가 왜 이 문서를 먼저 읽어야 하는가`를 3~4문장으로 적는다.

## 4-3. 포털 재배포
- 아래 중 하나라도 참이면 같은 사이클에 `scripts/publish_docs_portal.sh`를 실행한다.
  - public tracker 갱신
  - index 갱신
  - GDD/public HTML 갱신
- 실패 시에는 강행하지 말고 run log에 실패 원인만 append한다.

---

## 5. automation run log append 규칙

## 5-1. append-only 규칙
- 새 run은 항상 맨 아래에 새 `## YYYY-MM-DD HH:MM` 섹션으로 append한다.
- 이전 run의 bullet을 large rewrite 하지 않는다.
- 충돌/정합성 위험이 있으면 **짧은 factual note**만 추가하고, 오래된 entry 정리는 다음 안전한 수동 턴으로 미룬다.

## 5-2. 최소 필드
- `실행 상태`
- `작업 주제`
- `변경 내용`
- `수정 파일`
- `검증 결과`
- `공개 문서 반영 여부`
- `git 처리 결과`
- `다음 추천 작업`
- 필요 시 `재미/FQA 관찰`

## 5-3. 좋은 append 예시
```md
## 2026-03-18 04:30

- 실행 상태: completed
- 작업 주제: `overrunEvent`/온보딩 실행 기록 계약 문서화
- 변경 내용: ...
- 수정 파일: ...
- 검증 결과: 문서 교차 검토 + public tracker/index 동기화 확인
- 공개 문서 반영 여부: 반영 완료 (...)
- git 처리 결과: ...
- 다음 추천 작업: 다음 writer 슬롯은 여전히 A-track 실제 코드 컷오버가 최우선이다. 다만 이제 tracker/run log는 새 기록 계약 문장만 사용한다.
```

## 5-4. 나쁜 패턴
- 기존 10개 entry를 한 번에 같은 어조로 재작성
- `최근 작업: overrun 진행`처럼 단계명이 없는 제목
- 검증 결과 없이 `문서 정리 완료`만 남김
- push 실패/배포 실패를 숨기고 `반영 완료`로 적음

---

## 6. 복붙용 기록 문장

### Commit 1 종료
> 이번 턴은 `overrunEvent` Commit 1 floor green이다. `S1/S2/S3/S7/S8/S9/S10/S11`을 같은 evidence 묶음으로 닫았고, reward apply / page advance / onboarding은 아직 열지 않았다.

### Commit 2 종료
> 이번 턴은 `overrunEvent` Commit 2 runtime-ready closing green이다. `S4/S5/S6/S10/S12`를 같은 evidence 묶음으로 닫았고, 다음은 `B-1 library primer`만 연다.

### B-1 종료
> 이번 턴은 `B-1 library primer green`이다. library surface만 닫았고 다음은 `B-2 battle primer`만 연다.

### B-2 종료
> 이번 턴은 `B-2 battle primer green`이다. battle surface만 닫았고 다음은 `B-3 reward/shop primer`만 연다.

### B-3 종료
> 이번 턴은 `B-3 reward/shop primer green`이다. reward/shop surface만 닫았고 다음은 `B-4 ending primer`만 연다.

### B-4 종료
> 이번 턴은 `B-4 ending primer green`이다. ending은 결과/CTA 의미만 닫았고, overrun flavor는 계속 `overrunEvent`에 남긴다.

### 문서화 슬롯 종료
> 이번 턴은 `실행 기록 계약` 문서화 green이다. 다음 구현/검수/기록은 새 계약의 단계명·evidence·append 규칙만 사용한다.

---

## 7. 리스크 & 후속과제
- 가장 큰 리스크는 문서가 충분한 상태에서 오히려 `이제는 기록쯤은 대충 써도 되겠지`가 되는 것이다. 구현 문서가 많을수록 기록 문장 하나가 전체 상태 인상을 결정한다.
- `Commit 1 floor`와 `Commit 2 closing`은 코드보다 기록에서 더 쉽게 섞인다. 특히 `S10`처럼 양쪽에 걸친 guard는 evidence는 맞는데 문장만 잘못 쓰기 쉽다.
- B-track은 surface 단위 문서가 충분해도 run log에서 `온보딩 진행`으로 뭉개지면 다시 추론 비용이 생긴다. 즉 온보딩의 마지막 운영 리스크는 설계가 아니라 **기록 언어의 축약**이다.
- 후속 구현 슬롯은 새 문서 추가보다 실제 A-track 코드 컷오버가 우선이다. 다만 그 턴부터는 tracker/run log를 이 문서의 복붙 문장으로만 남겨 half-cutover 과장을 막아야 한다.

## 8. 즉시 다음 액션
1. `runtime commit stack`, `runtime handoff scorecard`, `cutover gate`, 이 문서를 같은 턴 착수 세트로 묶는다.
2. 다음 문서/구현/검수 슬롯부터 tracker와 run log에 `Commit 1 floor`, `Commit 2 closing`, `B-1~B-4` 외 큰 green 문장을 쓰지 않는다.
3. run log는 항상 append-only로 기록하고, 충돌 위험이 있으면 짧은 factual note만 더한다.
4. 다음 writer 구현 슬롯은 여전히 A-track 코드 컷오버를 먼저 닫고, 이 문서의 기록 계약으로 snapshot/blocker/리스크/변경 이력을 같이 정리한다.
