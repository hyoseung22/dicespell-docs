# DiceSpell `overrunEvent` Commit 2 live kickoff packet

## 3줄 요약
- 이 문서는 `readiness board`, `commit2 surgery`, `visual asset packet`, `A-3 UI packet`, `ending boundary`, `content deck`, `evidence/archive/manifest` 문서가 이미 충분한 상태에서도 **실제 Commit 2 closing 첫 live edit 직전에 남아 있던 마지막 실행 공백**을 닫기 위한 source-of-truth다.
- 핵심은 새 설계를 더하는 것이 아니라, `좋아, 이제 Commit 1 green은 확인했어. 그러면 Commit 2 closing을 실제로 열기 전에 첫 10분 안에 정확히 무엇을 다시 잠그고, 어떤 archive를 만들고, 어떤 semantic zone만 열며, 무엇은 끝까지 건드리지 말아야 하지?`를 **한 장의 kickoff 순서**로 압축하는 것이다.
- 목표는 다음 구현자가 문서 묶음을 다시 큐레이션하지 않고도, **Commit 2 closing 착수 직전 preflight -> visual/boundary freeze -> closing-only edit -> evidence drop -> stop/go 기록**까지 같은 stage 언어로 시작하게 만드는 것이다.

**한 줄 목표:** Commit 2 첫 live turn을 `Commit 1 green 확인 -> closing archive 생성 -> manifest/stub 초안 -> boundary/visual/semantic freeze -> closing-only edit -> evidence drop`의 6단계 kickoff로 고정해, 마지막 커밋이 다시 `UI 조금 + resolve 조금 + 온보딩 조금` 식으로 부풀지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 DiceSpell의 `overrunEvent` 축은 Commit 2까지 문서상으로는 거의 닫혀 있다.

이미 있는 것:
- 착수/대기선: `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- Commit 2 절개선: `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- visual contract: `docs/dicespell_overrun_onboarding_visual_asset_packet_kr.md`
- UI layer contract: `docs/dicespell_overrun_a3_ui_packet_kr.md`
- ending ↔ overrun 경계: `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- exact branch copy/token: `docs/dicespell_overrun_event_content_deck_kr.md`
- semantic locator: `docs/dicespell_overrun_runtime_semantic_anchor_packet_kr.md`
- rehearsal / evidence / archive / manifest 규칙: `runtime rehearsal`, `evidence manifest`, `artifact archive`, `archive bootstrap`, `manifest fill`, `filled manifest examples`, `artifact stub`
- Commit 1 first-turn kickoff: `docs/dicespell_overrun_commit1_live_kickoff_packet_kr.md`

그런데 실제 Commit 2 첫 live turn 직전에는 여전히 아래가 남는다.

1. `Commit 1은 green이 났는데, 그래서 Commit 2 첫 10분에는 무엇을 먼저 다시 확인해야 하지?`가 여러 문서에 흩어져 있다.
2. Commit 2는 Commit 1보다 더 위험하다. visual closing, confirm resolve, old baseline 제거가 한 자리에 붙기 때문에 `이 김에 onboarding도`, `ending 카피도`, `새 polish도`가 가장 쉽게 섞인다.
3. 프로그래머/UI/아트/QA/PM이 모두 실제 참여자가 되는 첫 턴이라, **누가 지금 closing owner이고 누가 아직 금지 범위를 지키는 reviewer인지**를 같은 문장으로 공유해야 한다.
4. 이 순서가 없으면 Commit 2도 다시 `수술 시트는 읽었으니 감으로 closing하자`, `scene card만 예쁘면 되겠지`, `resolve는 smoke가 알아서 잡겠지` 같은 drift가 생긴다.

즉 이 문서는 새 설계 패킷이 아니라, **Commit 2 첫 live execution을 여는 정확한 closing kickoff 순서 패킷**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 10분 kickoff`, `3. semantic anchor`, `4. closing 경계` | `commit2 surgery sheet`, `A-3 UI packet`, `A-4 resolve packet`, `runtime semantic anchor` | Commit 2는 scene/resolve closing-only edit이며, Commit 1 green과 boundary freeze를 먼저 잠근 뒤에만 코드를 연다 |
| UI | `1. 누가 지금 움직이는가`, `2. 10분 kickoff`, `5-2. UI`, `6. 금지 패턴` | `visual asset packet`, `A-3 UI packet`, `ending boundary`, `content deck` | Commit 2는 첫 실제 closing 턴이지만 ending을 다시 꾸미는 턴이 아니라 `scene card`와 `result boundary`를 닫는 턴임을 확인한다 |
| 아트 | `1. 누가 지금 움직이는가`, `5-3. 아트`, `6. 금지 패턴` | `visual asset packet`, `content deck`, `screen packet` | Commit 2는 대형 신규 자산 턴이 아니라 token/accent/none variant sanity와 boundary-safe 톤을 닫는 턴임을 확인한다 |
| QA | `2. 10분 kickoff`, `5-4. QA`, `7. evidence drop`, `8. stop/go` | `fixture ledger`, `second runtime review`, `evidence manifest`, `filled manifest examples` | Commit 2 smoke는 `S4/S5/S6/S10/S12`만 기록하고, DOM/scene/resolve/no-op evidence가 다 모이기 전에는 green을 말하지 않는다 |
| PM/기획 | `0. first screen 결론`, `5-5. PM`, `8. stop/go`, `9. 기록 문장` | `execution reporting packet`, `manifest fill packet`, `stage_manifest_commit2_closing_example.md` | Commit 2는 `closing kickoff`와 `runtime-ready closing green`이 다른 문장임을 유지하고, B-track은 여전히 대기라는 사실을 기록 언어로 고정한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- Commit 1 green 뒤 Commit 2 closing을 실제로 열 때 첫 10분 안에 무엇부터 해야 하지?
- surgery / visual / boundary / content / evidence 문서는 어떤 순서로 겹쳐 봐야 하지?
- 누가 지금 closing에 참여하고, 누가 아직 금지 범위를 지켜야 하지?
- 어디까지가 kickoff이고, 어디서부터가 진짜 closing edit인가?

### 가장 짧은 답
- **프로그래머 / UI / 아트 / QA / PM이 Commit 2 kickoff의 즉시 행동 오너**다.
- 단, 모두가 edit owner인 것은 아니다. 프로그래머는 state/action split, UI는 shell/hierarchy, 아트는 token/accent sanity, QA는 closing evidence, PM은 stage language guard를 맡는다.
- 첫 편집 전에는 반드시 `Commit 1 green evidence 확인 + commit2-closing stage archive + manifest/stub 초안 + boundary/visual freeze + semantic anchor + 금지 범위 선언`이 있어야 한다.
- 이 여섯 개가 없으면 아직 kickoff도 끝나지 않은 상태다.

### Commit 2에서 절대 하지 않는 것
- onboarding B-track helper/primer 착수
- ending 본문에 overrun flavor 재주입
- 새 메카닉/새 reward branch 설계
- 대형 신규 자산 요청
- balance 가설 확장
- Commit 3처럼 보이는 polish backlog 흡수

---

## 1. 지금 누가 움직이고 누가 기다리는가

| 역할 | 지금 상태 | Commit 2에서 하는 일 | Commit 2에서 하지 않는 일 |
| --- | --- | --- | --- |
| 프로그래머 | 시작 가능 | scene/resolve branch 분리, `confirm-overrun-event` wiring, old baseline 제거, boundary-safe render split | onboarding helper, 새 기획 branch 추가 |
| UI | 시작 가능 | `scene-card` 위계, `none` neutral density, ending과 다른 shell 확인 | ending 전체 재디자인, reward/result 카드 재평준화 |
| 아트 | 시작 가능 | token/accent/none variant sanity, 장면 톤 비혼입 확인 | 새 key art, 대형 보상/ending 일러스트 마감 |
| QA | 시작 가능 | `S4/S5/S6/S10/S12` 범위 잠금, resolve/no-op/DOM evidence 수집 | B-track acceptance 수행 |
| PM/기획 | 시작 가능 | Commit 1 green 확인, stop/go 확인, closing 문장 가드 | `온보딩도 같이 진행` 같은 큰 문장 기록 |

### 한 줄 판단 규칙
- Commit 2 kickoff는 **전원 참여 턴**이지만, 각자 맡는 책임은 closing 범위 안에서만 유효하다.
- Commit 2에서 `이왕이면`이 붙는 순간 거의 항상 stage drift다.

---

## 2. Commit 2 첫 10분 kickoff 순서

## 3줄 요약
- 첫 10분은 closing edit보다 먼저 **Commit 1 green 확인 / closing archive / visual-boundary freeze / semantic zone**를 잠그는 시간이다.
- 이 순서를 생략하면 Commit 2가 다시 `scene polish 위에 resolve를 나중에 덧붙이는 커밋`이 된다.
- 각 단계는 끝날 때 산출물이 있어야 하며, 구두로만 `확인함`은 완료로 보지 않는다.

### T-10 ~ T-8: Commit 1 green + stage archive 확인
1. Commit 1 evidence가 실제로 존재하는지 확인한다.
   - `manifest.md`
   - floor smoke log
   - state diff
   - non-change note
2. `docs/artifacts/YYYYMMDD/commit2-closing/` 생성
3. 하위 폴더 `logs/`, `captures/`, `notes/` 생성
4. starter file 복사 또는 생성
   - `manifest.md`
   - `logs/YYYYMMDD-commit2-qa-smoke-log.md`
   - `captures/YYYYMMDD-commit2-ui-scene-capture.md`
   - `notes/YYYYMMDD-commit2-programmer-resolve-diff.md`
   - `notes/YYYYMMDD-commit2-boundary-note.md`
   - 필요 시 `notes/YYYYMMDD-commit2-art-sanity.md`

**완료 기준**
- Commit 1 green evidence가 실제 파일 기준으로 확인된다.
- commit2-closing stage archive가 실제로 생성돼 있다.
- 아직 closing green 문장은 쓰지 않는다.

### T-8 ~ T-6: manifest + stub 첫 줄 채우기
1. `manifest.md`에 아래 5가지를 먼저 적는다.
   - `status: bootstrap-ready`
   - `owner summary: Commit 2 closing은 scene/resolve/runtime-ready 경계만 연다`
   - `boundary / non-change: onboarding 미오픈, ending flavor 비혼입, 새 branch 미추가`
   - `next allowed step: visual/boundary/semantic freeze 확인 후 closing edit`
   - `handoff line candidate: Commit 2 closing을 닫고 scene/resolve/runtime-ready 경계를 고정했다.`
2. stub 파일 첫 줄을 stage-specific 문장으로 채운다.
   - smoke log: `이 로그는 Commit 2 closing smoke 범위만 기록한다.`
   - scene capture: `이 capture는 scene-card 위계와 ending 경계만 기록한다.`
   - resolve diff: `이번 diff는 confirm resolve 1회 적용과 old baseline 제거만 기록한다.`
   - boundary note: `이번 note는 ending 결과/CTA 의미와 overrunEvent 장면 경계만 기록한다.`
   - art sanity: `이번 note는 token/accent/none variant sanity만 기록한다.`

**완료 기준**
- 빈 파일이 없다.
- `나중에 다듬기`, `일단 scene부터`, `거의 끝` 같은 generic placeholder가 없다.

### T-6 ~ T-4: visual / boundary / semantic freeze
1. 아래 source-of-truth를 같은 순서로 다시 확인한다.
   - `commit2 closing surgery sheet`
   - `visual asset packet`
   - `A-3 UI packet`
   - `ending_overrun_boundary_packet`
   - `event content deck`
2. `notes/...boundary-note.md`에 아래를 적는다.
   - ending이 맡는 것: 결과 / 정산 / CTA 의미
   - overrunEvent가 맡는 것: 장면 / flavor / confirm
   - 이번 턴 금지: onboarding primer, overrun flavor를 ending helper에 주입, reward-card처럼 재사용해 scene를 죽이는 것
3. UI/아트는 `none` branch를 placeholder가 아니라 정상 장면으로 읽어야 한다는 한 줄 sanity를 남긴다.

**완료 기준**
- manifest와 boundary note가 같은 금지 범위를 말한다.
- visual packet과 boundary packet의 책임 경계가 다른 문장으로 재해석되지 않는다.

### T-4 ~ T-2: semantic anchor 재확인
1. 아래 semantic zone을 실제 코드에서 다시 찾는다.
   - `renderCenter`
   - `renderEnding`
   - `continue-overrun` action
   - `confirm-overrun-event` 예정 zone 또는 동등 dispatcher zone
   - `resolved` / resolve guard zone
   - overrun smoke closing block
2. `notes/...resolve-diff.md`에 찾은 locator를 적는다.
   - 함수명
   - grep token
   - 이번 턴에 열 수 있는 책임
   - 아직 건드리면 안 되는 주변 책임
3. line drift가 있어도 stage는 넓히지 않는다.

**완료 기준**
- semantic locator 메모가 note에 남아 있다.
- `이 김에 onboarding hook도 보자` 같은 stage 확장 메모가 없다.

### T-2 ~ T+10: closing-only edit + 첫 evidence drop
1. `renderCenter()`에 explicit `overrunEvent` branch / 동등 closing line을 붙인다.
2. `scene-card` hierarchy와 `none` density를 Commit 2 범위까지만 반영한다.
3. `confirm-overrun-event`와 `resolved` guard 또는 동등 resolve 경계를 Commit 2 범위까지만 닫는다.
4. old immediate baseline을 제거한다.
5. edit 후 즉시 아래 evidence를 채운다.
   - smoke log: `S4/S5/S6/S10/S12`
   - scene capture: branch별 shell / CTA / footnote / none density
   - resolve diff: 바뀐 값 / 1회 적용 / no-op 재실행 가드
   - boundary note: ending vs overrunEvent 비혼입 유지
   - 필요 시 art sanity: token/accent/none variant 판정
6. evidence가 없으면 green 금지

**완료 기준**
- Commit 2 evidence bundle 초안이 archive 안에 존재한다.
- 아직 B-track 문장은 없다.

---

## 3. semantic anchor 재확인 규칙

| semantic zone | Commit 2에서 찾는 이유 | 찾은 뒤 기록해야 하는 것 | 찾았다고 해서 열면 안 되는 것 |
| --- | --- | --- | --- |
| `renderCenter` | explicit `overrunEvent` branch closing을 넣기 위해 | branch entry / fallback / shell 책임 | onboarding primer branch |
| `renderEnding` | 결과/CTA 의미까지만 남기는 boundary를 확인하기 위해 | 남겨야 할 ending 책임 / 줄여야 할 flavor | ending flavor 확장 |
| action dispatcher | `continue-overrun`와 `confirm-overrun-event` 의미를 분리하기 위해 | CTA 단계별 action 의미 | extra CTA / 임시 debug action |
| `resolved` zone | reward 1회 적용과 reload-safe no-op를 닫기 위해 | guard 위치 / 중복 적용 금지 | 복수 resolve 경로 |
| overrun smoke block | Commit 2 fixture 범위를 분리하기 위해 | `S4/S5/S6/S10/S12`만 Commit 2임을 메모 | B-track acceptance 수행 |

### 프로그래머 handoff 포인트
- `resolve-diff.md`에 semantic locator를 먼저 적고 diff를 시작한다.
- line number는 참고값이고, **closing 책임 경계가 계약**이다.

### UI/아트/QA handoff 포인트
- review 코멘트도 line보다 `semantic zone` 이름으로 남긴다.
- `ending도 같이 손보자`, `reward 카드 느낌으로도 되겠다`, `온보딩 힌트도 여기서 달자`가 보이면 Commit 2 fail에 가깝다.

---

## 4. Commit 2 closing 경계

**한 줄 목표:** Commit 2는 `scene shell + confirm resolve + old baseline 제거 + boundary evidence`까지만 닫는다.

| 구분 | Commit 2에서 해야 하는 것 | Commit 2에서 하지 않는 것 |
| --- | --- | --- |
| 엔진 | confirm 시 reward 1회 적용, `resolved` guard, old immediate baseline 제거 | onboarding state/helper 추가, 새 reward branch 설계 |
| 렌더 | `overrunEvent` 중앙 카드 위계, `none` neutral density, ending과 다른 shell | ending 본문 재확장, primer layer 주입 |
| QA | `S4/S5/S6/S10/S12` closing smoke + DOM/scene evidence | B-track acceptance/recovery |
| 기록 | closing evidence, boundary note, Commit 2 문장 후보 | `온보딩 시작`, `오버런 전체 green` 같은 큰 문장 |

### 이 경계가 중요한 이유
- Commit 2는 **runtime-ready closing을 만드는 커밋**이다.
- B-track은 **그 위에 primer를 surface별로 심는 커밋**이다.
- 둘을 합치면 다시 half-cutover가 생긴다.

---

## 5. owner별 handoff 포인트

### 5-1. 프로그래머
- 먼저 넘길 것
  - `manifest.md` 초안
  - `resolve-diff.md` semantic locator 메모
  - `boundary-note.md` 금지 범위
- 나중에 넘길 것
  - smoke 결과 반영 후 updated resolve diff
  - `confirm-overrun-event` / `resolved` guard evidence
- 넘기지 말 것
  - B-track primer 범위
  - 새 content branch 제안

### 5-2. UI
- 지금 받을 것
  - `scene-card` hierarchy 메모
  - `none` branch density 메모
  - ending vs overrunEvent boundary note
- 지금 받지 않을 것
  - ending 대수선 요청
  - reward/shop/primer 확장 요청
- Commit 2에서 해야 할 한 줄
  - `Commit 2는 scene-card closing 턴이지, ending을 다시 설명하는 턴이 아니다.`

### 5-3. 아트
- 지금 받을 것
  - token/accent/none variant sanity 메모
  - 필요 시 minimal shell 예외 요청 1줄
- 지금 받지 않을 것
  - 새 key art
  - ending flavor art
  - B-track 자산 패키지 마감
- Commit 2에서 해야 할 한 줄
  - `아트는 Commit 2에서 대형 신규 자산이 아니라 boundary-safe visual sanity를 닫는다.`

### 5-4. QA
- 먼저 받을 것
  - Commit 2 smoke log stub
  - fixture 범위: `S4/S5/S6/S10/S12`
- 반드시 적을 것
  - scene shell evidence
  - resolve 1회 적용 / no-op guard evidence
  - 이번 로그가 다루지 않는 범위(B-track)
- 금지
  - `전체 smoke ok`
  - fixture 번호 없는 pass 문장
  - DOM/scene evidence 없는 closing green

### 5-5. PM/기획
- 먼저 확인할 것
  - Commit 1 green evidence 존재 여부
  - manifest의 `status`, `next allowed step`, `handoff line candidate`
  - boundary note의 금지 범위
- 지금 하지 않을 것
  - `온보딩도 곧 시작` 선기록
  - `오버런 거의 완료` 같은 큰 문장 기록
- Commit 2에서 해야 할 한 줄
  - `이번 턴은 Commit 2 closing kickoff와 runtime-ready evidence 생성까지만 연다.`

---

## 6. 금지 패턴

1. Commit 2에서 onboarding B-track helper/primer를 여는 것
2. ending 카피나 helper에 overrun flavor를 다시 섞는 것
3. `scene-card`를 reward/result 카드 재활용 수준으로 대충 닫아 branch identity를 죽이는 것
4. `none` branch를 빈 화면 / 오류 상태처럼 처리하는 것
5. UI shell은 닫았는데 resolve/no-op/old baseline 제거를 미루는 것
6. resolve는 닫았는데 scene capture / boundary note / art sanity 없이 green을 말하는 것
7. `이왕이면`이라는 이유로 polish backlog를 Commit 2에 같이 태우는 것

이 중 하나라도 들어오면 Commit 2 kickoff가 아니라 **closing drift**다.

---

## 7. 최소 evidence drop

1. `manifest.md`
2. `logs/...commit2...smoke-log.md`
3. `captures/...commit2...scene-capture.md`
4. `notes/...resolve-diff.md`
5. `notes/...boundary-note.md`
6. 필요 시 `notes/...art-sanity.md`

### 중요한 점
- fake capture는 만들지 않는다.
- scene capture가 없으면 UI closing evidence가 아니다.
- resolve diff가 없으면 runtime-ready evidence가 아니다.
- green 문장은 stub가 아니라 manifest / tracker / run log에서만 확정한다.

---

## 8. stop / go 판정

### stop
- Commit 1 green evidence가 실제로 없다.
- ending과 overrunEvent의 책임 문장이 서로 다르다.
- `confirm-overrun-event` / `resolved` guard / old baseline 제거 중 하나라도 이번 턴 범위에서 빠져 있다.
- B-track이나 ending 확장 요청이 같이 끼어든다.

### go
- Commit 1 green evidence가 확인됐다.
- commit2-closing archive와 manifest/stub가 stage 언어로 채워졌다.
- visual/boundary/semantic freeze가 같은 금지 범위를 말한다.
- `S4/S5/S6/S10/S12` + scene capture + resolve diff + boundary note가 같은 stage evidence로 모였다.

---

## 9. 기록 문장 가드

### 허용되는 kickoff 문장
- `Commit 2 closing kickoff: Commit 1 green 확인 후 closing archive/visual boundary preflight 완료, closing-only edit 착수`
- `Commit 2 closing evidence는 commit2-closing stage archive에 수집 중`

### 허용되는 green 문장
- `Commit 2 closing을 닫고 scene/resolve/runtime-ready 경계를 고정했다.`

### 아직 금지되는 문장
- `온보딩도 이어서 구현`
- `오버런 전체 완료`
- `ending 리디자인 완료`
- `다음 phase 대부분 준비됨`

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| Commit 2가 `scene polish 턴`으로 오해돼 resolve/no-op/baseline 제거가 느슨해지는 위험 | runtime-ready처럼 보이지만 실제로는 old baseline이 남는다 | kickoff 순서에 resolve diff + guard evidence + old baseline 제거를 함께 고정 |
| ending과 overrunEvent 경계가 closing 턴에 다시 흐려지는 위험 | B-4 ending helper와 scene flavor가 섞여 재작업이 생긴다 | boundary note를 manifest와 같은 단계의 필수 evidence로 고정 |
| UI/아트가 신규 자산/과한 polish를 같이 밀어 넣는 위험 | Commit 2 범위가 visual backlog 처리 턴으로 변질된다 | Commit 2는 token/accent/none sanity만 허용하고 대형 자산은 금지 |
| QA/PM이 DOM evidence 없이 closing green을 기록하는 위험 | half-cutover가 runtime-ready처럼 남는다 | `S4/S5/S6/S10/S12` + scene capture + resolve diff를 필수 묶음으로 못 박음 |
| Commit 2에서 B-track을 조기 착수하는 위험 | 잘못된 closing baseline 위에 primer가 올라간다 | Commit 2 green 전 B-track 금지를 다시 명시 |

---

## 11. 바로 할 일
1. Commit 1 green evidence부터 확인한다.
2. `commit2-closing` stage archive를 만든다.
3. manifest와 Commit 2 stub 첫 줄을 채운다.
4. visual/boundary/content/semantic freeze를 같은 문장으로 잠근다.
5. 그 뒤에만 Commit 2 closing edit와 evidence 수집을 시작한다.
6. Commit 2 green 뒤에만 B-1 library를 연다.
