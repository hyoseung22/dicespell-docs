# DiceSpell `overrunEvent` / 첫 런 온보딩 증거 매니페스트 패킷

## 3줄 요약
- 이 문서는 `overrunEvent` A-track Commit 1/2와 첫 런 온보딩 B-track B-1~B-4를 실제로 닫을 때, **프로그래머 / UI / 아트 / QA / PM이 정확히 어떤 제출물 묶음을 같은 이름으로 넘겨야 하는지**를 고정하는 source-of-truth다.
- 지금 DiceSpell에는 scorecard, workboard, gate, reporting contract까지 이미 충분하지만, 실제 handoff 직전에는 여전히 `좋아, evidence bundle이라고 했는데 폴더 안에 정확히 뭘 넣고 무엇을 누락으로 볼까?`가 마지막으로 흔들린다.
- 목표는 새 설계를 더하는 것이 아니라, 이미 정의된 evidence를 **artifact manifest + 파일명 규칙 + 단계별 제출 체크**로 다시 압축해 reviewer와 maker가 같은 묶음을 같은 단계명으로 읽게 만드는 것이다.

**한 줄 목표:** `Commit 1 floor -> Commit 2 closing -> B-1/B-2/B-3/B-4`를 **단계별 artifact manifest 1개 + 역할별 제출 책임 1세트**로 고정해 handoff-ready 증거 묶음을 재추론하지 않게 만든다.

---

## 왜 이 문서가 필요한가
- 현재 DiceSpell의 문서 공백은 `무엇을 구현하지?`가 아니라 `좋아, 구현/검수/기록은 알겠어. 그런데 실제 제출 폴더 안에 무엇이 있어야 reviewer가 다시 물어보지 않지?`에 가깝다.
- 기존 문서들은 이미 아래를 충분히 닫고 있다.
  - `runtime handoff scorecard`
  - `runtime owner workboard`
  - `cutover gate packet`
  - `execution reporting packet`
  - `onboarding handoff scorecard`
  - `onboarding owner workboard`
- 문제는 실제 handoff 직전 evidence가 다시 아래처럼 흐려지기 쉽다는 점이다.
  1. `evidence bundle`이라는 말만 있고, 어떤 smoke/스크린샷/상태 diff가 한 묶음인지 파일 수준으로는 안 박혀 있음
  2. UI/아트 sanity 스크린샷과 프로그래머 상태 diff가 섞여 `누가 무엇을 제출했는지`가 흐려짐
  3. tracker/run log 문장은 있는데, 그 문장을 뒷받침하는 artifact 이름과 최소 수량이 고정되지 않아 reviewer가 다시 확인 질문을 던짐
  4. Commit 1 floor와 Commit 2 closing, B-1~B-4가 각자 다른 단계인데도 제출 묶음이 `오버런 증거`, `온보딩 증거`처럼 다시 크게 뭉개짐
- 이 패킷은 그 마지막 handoff 마찰을 줄이기 위해 만든다.

---

## 이 문서를 먼저 봐야 하는 사람
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. 단계별 artifact manifest`, `2. 파일명 규칙` | `runtime handoff scorecard`, `onboarding handoff scorecard` | 어떤 로그/상태 diff/검증 출력이 없으면 제출 미완료인지 바로 안다 |
| UI | `1. 단계별 artifact manifest`, `3. 역할별 제출 책임` | `A-3 UI packet`, `B-1~B-4 packet` | Commit 1은 UI 마감 턴이 아니고, Commit 2/B-track만 shell 증거를 남긴다 |
| 아트 | `3. 역할별 제출 책임`, `4. surface 캡처 규칙` | `visual asset packet`, `content deck` | 자산 제작 여부와 visual sanity 제출물을 따로 다뤄야 함을 안다 |
| QA | `1. 단계별 artifact manifest`, `5. 검수 체크리스트` | `execution reporting packet`, `acceptance matrix` | 단계별 smoke/acceptance 번호와 캡처를 같은 묶음으로 리뷰한다 |
| PM/기획 | `0. first screen 결론`, `6. handoff-ready 판정` | `cutover gate packet`, `owner handoff packet` | `증거가 있다`가 아니라 `정해진 manifest가 꽉 찼다`가 green 기준임을 안다 |
| 자동화 writer | `2. 파일명 규칙`, `7. 리스크 & 후속과제` | `automation run log`, `development tracker` | run log 문장과 artifact 묶음을 같은 단계명으로만 묶는다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `Commit 1 floor evidence bundle`이라고 말할 때 실제로 어떤 파일이 있어야 하지?
- `Commit 2 closing green`을 주장하려면 어떤 smoke 출력과 어떤 화면 캡처가 최소선이지?
- `B-1~B-4` surface별 primer handoff는 어떤 묶음으로 갈라서 넘겨야 drift가 안 나지?

### 가장 짧은 답
- 모든 단계는 **manifest 1개 + 검증 출력 1묶음 + 화면/상태 evidence 1묶음 + 한 줄 handoff 문장 1개**로 닫는다.
- `Commit 1 floor`, `Commit 2 closing`, `B-1`, `B-2`, `B-3`, `B-4`는 각각 다른 artifact 묶음이다. 서로 섞지 않는다.
- 파일명은 `stage-owner-artifact`를 그대로 드러내야 한다. `final.png`, `new-log.txt` 같은 generic 이름은 금지한다.
- 제출물은 `무엇을 했는가`뿐 아니라 `이번 턴에 무엇을 열지 않았는가`까지 증명해야 한다.

---

## 1. 단계별 artifact manifest

## 1-1. Commit 1 floor manifest

### 3줄 요약
- Commit 1은 enter/snapshot/recovery 바닥만 닫는 턴이다.
- 따라서 핵심 제출물은 `reward apply를 안 열었다`는 non-change line과 `snapshot/recovery가 살아 있다`는 상태 증거다.
- UI/아트는 이 단계에서 마감 캡처가 아니라 payload sanity 보조 증거만 남긴다.

### 한 줄 목표
`phase='overrunEvent'` 진입 + canonical snapshot field set + recovery + non-change line을 artifact 수준으로 고정한다.

### 필수 manifest
| artifact key | 누가 제출 | 최소 내용 | 금지 |
| --- | --- | --- | --- |
| `commit1-smoke-log` | 프로그래머/QA | `S1/S2/S3/S7/S8/S9/S10/S11` 결과 또는 동등 출력 | `전체 smoke pass` 같은 큰 말만 남기기 |
| `commit1-state-diff` | 프로그래머 | `phase`, canonical field set, reward 미적용, `currentPage`/`pagePlan`/`segmentTrophyTemplateIds` 미변경 메모 | 코드 설명만 있고 상태 diff 없음 |
| `commit1-nonchange-note` | 프로그래머/PM | 이번 턴에 열지 않은 범위 3~4개 명시 | `다음에 할 예정`만 쓰고 현재 비변경선 미기록 |
| `commit1-payload-sanity` | UI/아트 보조 | `contentKey`/`badgeLabel`/`accentKey`/`artTokenSet` sanity 메모 또는 캡처 | shell polish, layout 확정 캡처 |
| `commit1-handoff-line` | PM/QA | `이번 턴은 Commit 1 floor만 닫았다.` 문장 | `오버런 완료` 같은 과장 문장 |

### 제출 묶음 예시
1. `commit1-smoke-log.txt`
2. `commit1-state-diff.md`
3. `commit1-nonchange-line.md`
4. `commit1-payload-sanity.md` 또는 `commit1-payload-sanity.png`
5. tracker/run log에 들어갈 `Commit 1 floor` handoff 문장

---

## 1-2. Commit 2 closing manifest

### 3줄 요약
- Commit 2는 장면 카드, confirm resolve, old baseline 제거를 닫는 턴이다.
- 따라서 핵심 제출물은 `보인다`보다 `한 번만 적용된다`와 `ending과 다른 장면 위계가 읽힌다`를 증명하는 묶음이다.
- 여기서부터 UI/아트가 실제 shell/tone sanity를 artifact로 남긴다.

### 한 줄 목표
`renderOverrunEvent(summary)` + `confirm-overrun-event` + `resolved` guard + old baseline 제거를 runtime-ready 증거로 묶는다.

### 필수 manifest
| artifact key | 누가 제출 | 최소 내용 | 금지 |
| --- | --- | --- | --- |
| `commit2-smoke-log` | 프로그래머/QA | `S4/S5/S6/S10/S12` 결과 또는 동등 출력 | acceptance 번호 없는 pass 메모 |
| `commit2-resolve-diff` | 프로그래머 | confirm 1회 적용, `resolved` no-op, old immediate baseline 제거 메모 | 클릭 전후 차이 없는 단순 캡처 |
| `commit2-scene-captures` | UI/아트 | `themeTrophy` / `enemyTrophy` / `none` 3축 중 최소 2축 + `none` 포함 권장 | ending 화면과 구분 안 되는 generic 캡처 |
| `commit2-boundary-note` | UI/PM | ending-vs-overrun scene boundary 메모 | flavor와 결과 정산을 같은 캡처에 섞기 |
| `commit2-handoff-line` | PM/QA | `이번 턴은 overrunEvent runtime-ready closing green이다.` | `오버런/온보딩 green` 같이 다음 단계까지 합치기 |

### 제출 묶음 예시
1. `commit2-smoke-log.txt`
2. `commit2-resolve-diff.md`
3. `commit2-theme-scene.png`
4. `commit2-enemy-scene.png`
5. `commit2-none-scene.png`
6. `commit2-boundary-note.md`
7. tracker/run log `Commit 2 closing` 문장

---

## 1-3. B-track manifest

### 공통 원칙
- B-track은 `surface 1개 = manifest 1개`다.
- `library`, `battle`, `reward/shop`, `ending` evidence는 서로 다른 단계이며 같은 폴더나 같은 문장으로 합치지 않는다.
- 모든 B-step은 **primer 노출 evidence + recovery/재노출 evidence + 금지 범위 메모 + handoff 문장**을 같이 제출한다.

| 단계 | 필수 artifact key | 최소선 |
| --- | --- | --- |
| B-1 library | `b1-surface-capture`, `b1-recovery-note`, `b1-handoff-line` | hero-primer 위치와 재노출/미재노출 제어가 같이 보여야 함 |
| B-2 battle | `b2-surface-capture`, `b2-first-action-note`, `b2-handoff-line` | 첫 주문/자동 패스/힌트 노출 트리거 중 해당 항목 evidence 포함 |
| B-3 reward-shop | `b3-reward-capture`, `b3-shop-capture`, `b3-boundary-note`, `b3-handoff-line` | reward와 shop이 같은 strip이 아니라 분리된 primer로 보임 |
| B-4 ending | `b4-ending-capture`, `b4-boundary-note`, `b4-handoff-line` | ending helper는 남고 `themeTrophy/enemyTrophy/none` flavor는 ending이 먹지 않음 |

---

## 2. 파일명 규칙

## 기본 형식
`YYYYMMDD-stage-owner-artifact.ext`

예시
- `20260318-commit1-programmer-smoke-log.txt`
- `20260318-commit1-qa-nonchange-note.md`
- `20260318-commit2-ui-theme-scene.png`
- `20260318-b1-ui-library-primer.png`
- `20260318-b4-pm-boundary-note.md`

## 파일명 필수 요소
| 요소 | 이유 |
| --- | --- |
| 날짜 | run log / tracker / git과 교차 추적하기 위해 |
| 단계명 | Commit 1 / Commit 2 / B-1~B-4를 섞지 않기 위해 |
| 오너 | 누가 제출한 evidence인지 즉시 드러내기 위해 |
| artifact 타입 | smoke, capture, state-diff, boundary-note 등을 reviewer가 바로 분류하기 위해 |

## 금지 파일명
- `final.png`
- `latest.txt`
- `new-capture.png`
- `proof.md`
- `done.txt`

이름만 보고 단계/오너/artifact 타입을 모르면 제출 실패로 본다.

---

## 3. 역할별 제출 책임

| 역할 | 반드시 남겨야 하는 것 | 있으면 좋은 것 | 남기면 안 되는 것 |
| --- | --- | --- | --- |
| 프로그래머 | smoke log, state diff, resolve diff, non-change line | grep token/semantic anchor 메모 | 코드 설명만 있고 실행 증거 없음 |
| UI | surface capture, hierarchy/boundary note | hover/focus/spacing 보조 캡처 | 미완성 shell을 마감안처럼 제출 |
| 아트 | token/accent/artToken sanity note, visual drift 캡처 | reuse/not-reuse 구분 메모 | 새 자산 제안과 이번 턴 evidence를 섞기 |
| QA | acceptance 번호 포함 pass/fail log, recovery note | retry/reload 재현 메모 | `정상 같음`처럼 느슨한 표현 |
| PM/기획 | 단계 handoff 문장, 다음 허용 단계 1개, blocker 문장 | 포털 노출 이유 1문장 | 여러 단계를 한 번에 green 처리 |
| 자동화 writer | append-only run log entry, tracker/index 반영 결과 | 배포/커밋 결과 메모 | 제출물 없는 green 요약 |

---

## 4. surface 캡처 규칙

## Commit 2 scene capture
- 최소 2장, 권장 3장: `themeTrophy`, `enemyTrophy`, `none`
- 동일 viewport / 동일 density / 동일 CTA 위치 기준으로 남긴다.
- 캡처만 보고 아래 질문에 답할 수 있어야 한다.
  1. ending과 다른 장면인가?
  2. branch마다 배지/헤더/flavor 차이가 읽히는가?
  3. `none`도 placeholder가 아니라 의도된 neutral branch로 읽히는가?

## B-track capture
- B-1: 서고 hero-primer가 책 선택 위계를 먹지 않고 붙어 있는지
- B-2: battle primer 또는 inline hint가 첫 행동을 막지 않는지
- B-3: reward와 shop guidance가 같은 설명 블록으로 뭉치지 않는지
- B-4: ending helper는 남고 overrun flavor는 넘기지 않는지

## 금지 패턴
- 한 캡처에 여러 단계 상태를 콜라주처럼 묶기
- crop이 과해서 surface 맥락을 잃는 캡처
- 캡처 파일과 handoff 문장 단계명이 어긋나는 제출

---

## 5. 검수 체크리스트

### QA/PM이 evidence 묶음을 볼 때 바로 확인할 것
1. 파일명에 날짜/단계/오너/artifact 타입이 모두 들어가 있는가
2. run log / tracker 단계명과 artifact 단계명이 같은가
3. smoke/acceptance 번호가 해당 단계 packet과 같은가
4. `무엇을 안 열었는가` 메모가 있는가
5. 다음 허용 단계가 하나만 적혀 있는가
6. Commit 2와 B-track이 같은 제출 묶음으로 섞이지 않았는가

### handoff-ready 판정
아래를 모두 만족해야만 `handoff-ready`로 본다.
- manifest 필수 artifact가 전부 있음
- 단계별 green 문장이 있음
- non-change line 또는 boundary note가 있음
- review 질문 없이 다음 오너가 바로 착수 가능함

---

## 6. handoff-ready 판정 문장

### Commit 1
> `Commit 1 floor` handoff-ready: enter/snapshot/recovery artifact manifest가 꽉 찼고, reward apply / page advance / onboarding은 아직 열지 않았다는 non-change line이 같이 제출됐다.

### Commit 2
> `Commit 2 closing` handoff-ready: scene/resolve artifact manifest가 꽉 찼고, confirm 1회 적용 / resolved guard / ending boundary가 같은 단계명으로 검수됐다.

### B-1~B-4
> `B-n` handoff-ready: 해당 surface primer artifact manifest가 꽉 찼고, 다음 surface 1개만 허용된다는 handoff 문장이 같이 제출됐다.

---

## 7. 리스크 & 후속과제
- 가장 큰 리스크는 `evidence bundle`이라는 말을 계속 쓰면서도 실제 제출물이 generic 파일명과 generic 캡처로 흘러 다시 reviewer 질문을 늘리는 것이다.
- 두 번째 리스크는 Commit 2 scene capture와 B-4 ending helper capture가 같은 시각 언어로 섞여 boundary 문서가 무력화되는 것이다.
- 세 번째 리스크는 문서화가 늘어날수록 증거 제출이 번거롭게 느껴져 `일단 tracker에 green만 쓰자`로 후퇴하는 것이다.
- 따라서 다음 writer 구현 슬롯은 새 설계를 더하기보다, 실제 A-track 코드 컷오버 또는 이후 B-track surface 구현 시 이 manifest 구조를 따라 **artifact 이름과 단계 문장을 먼저 고정**하고 제출물을 모아야 한다.
- 후속으로 필요하다면, 다음 문서화 슬롯은 `artifact 보관 위치/압축 규칙` 또는 `PR/리뷰 본문 템플릿` 수준으로만 더 좁혀야 한다. 범위를 다시 `증거 관리 시스템 전체`로 키우면 안 된다.

---

## 8. immediate next actions
1. A-track 코드 컷오버를 열 때 `Commit 1 floor` artifact 이름부터 먼저 정한다.
2. Commit 2에서는 scene capture와 resolve diff를 같은 단계명으로 묶는다.
3. B-track을 열게 되면 `surface 1개 = manifest 1개` 원칙을 그대로 재사용한다.
4. tracker/run log에는 단계명 없는 green 문장을 더 이상 남기지 않는다.
