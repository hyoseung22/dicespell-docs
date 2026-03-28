# DiceSpell `overrunEvent` Commit 1 live kickoff packet

## 3줄 요약
- 이 문서는 `readiness board`, `document stack`, `archive bootstrap`, `manifest fill`, `filled manifest examples`, `artifact stub`, `semantic anchor`, `commit1 surgery`, `runtime rehearsal`, `evidence manifest`, `artifact archive`까지 이미 충분한 상태에서도 **실제 첫 live edit 직전에 아직 남아 있던 마지막 실행 공백**을 닫기 위한 source-of-truth다.
- 핵심은 새 설계를 더하는 것이 아니라, `좋아, 이제 Commit 1 floor를 실제로 열자. 그러면 첫 10분 안에 정확히 어떤 폴더를 만들고, 어떤 문장을 쓰고, 어느 semantic anchor를 찾고, 누구는 아직 기다려야 하지?`를 **한 장의 킥오프 순서**로 압축하는 것이다.
- 목표는 다음 구현자가 문서 꾸러미를 다시 큐레이션하지 않고도, **Commit 1 floor 착수 직전 preflight -> 첫 편집 -> 첫 evidence drop -> stop/go 기록**까지 같은 stage 언어로 시작하게 만드는 것이다.

**한 줄 목표:** Commit 1 첫 live turn을 `archive 바닥 생성 -> manifest/stub 초안 -> semantic anchor 재확인 -> floor-only edit -> evidence drop`의 5단계 kickoff로 고정해, 첫 커밋부터 다시 범위가 부풀지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 DiceSpell의 `overrunEvent` 축은 문서상으로는 거의 닫혀 있다.

이미 있는 것:
- 착수/대기선: `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- 문서 위계: `docs/dicespell_overrun_onboarding_document_stack_packet_kr.md`
- archive 구조: `docs/dicespell_overrun_onboarding_archive_bootstrap_packet_kr.md`
- manifest 문장 규칙: `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
- exemplar: `docs/dicespell_overrun_onboarding_filled_manifest_examples_kr.md`
- stub floor: `docs/dicespell_overrun_onboarding_artifact_stub_packet_kr.md`
- locator: `docs/dicespell_overrun_runtime_semantic_anchor_packet_kr.md`
- line surgery: `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- 착수 전 freeze 언어: `docs/dicespell_overrun_runtime_rehearsal_packet_kr.md`
- 증거 규칙: `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`, `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`

그런데 실제 첫 live turn 직전에는 여전히 아래가 남는다.

1. `문서는 충분한데, 그래서 첫 10분에 무엇부터 해야 하지?`가 여러 문서에 흩어져 있다.
2. archive/stub/semantic anchor/surgery/rehearsal이 각각은 닫혀 있어도, **한 번의 실전 킥오프 순서**로는 아직 압축돼 있지 않다.
3. 프로그래머는 첫 diff를 열기 전 `어느 anchor까지 찾았는지`, QA/PM은 `언제까지가 kickoff이고 언제부터가 실제 edit인지`를 같은 문장으로 공유해야 한다.
4. 이 순서가 없으면 첫 실전 턴에서도 다시 `문서 몇 장 더 보고 판단하자`, `이 김에 Commit 2도 같이`, `archive는 나중에` 같은 drift가 생긴다.

즉 이 문서는 새 설계 패킷이 아니라, **Commit 1 첫 live execution을 여는 정확한 시작 순서 패킷**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 10분 kickoff`, `3. semantic anchor`, `4. edit 경계` | `commit1 surgery sheet`, `runtime semantic anchor`, `state matrix` | Commit 1은 floor-only edit이며, anchor/manifest/stub를 먼저 잠근 뒤에만 코드를 연다 |
| UI | `1. 누가 지금 움직이는가`, `5. owner별 handoff`, `6. 금지 패턴` | `visual asset packet`, `a3_ui_packet` | Commit 1은 UI closing 턴이 아니다. payload sanity 확인까지만 관여한다 |
| 아트 | `1. 누가 지금 움직이는가`, `5-3. 아트`, `6. 금지 패턴` | `visual asset packet`, `content deck` | 새 자산 마감은 아직 금지이며, scene 감도 판단도 Commit 2 이후다 |
| QA | `2. 10분 kickoff`, `5-4. QA`, `7. evidence drop`, `8. stop/go` | `fixture ledger`, `evidence manifest`, `artifact stub packet` | Commit 1 smoke는 S1/S2/S3/S7/S8/S9/S10/S11만 기록한다 |
| PM/기획 | `0. first screen 결론`, `5-5. PM`, `8. stop/go`, `9. 기록 문장` | `execution reporting packet`, `manifest fill packet`, `filled manifest examples` | kickoff 메모와 green 문장은 다르며, Commit 1 floor 문장은 evidence 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `Commit 1 floor`를 실제로 열 때 첫 10분 안에 무엇부터 해야 하지?
- manifest/example/stub/anchor/surgery sheet는 어떤 순서로 겹쳐 봐야 하지?
- 누가 지금 edit를 하고, 누가 아직 기다려야 하지?
- 언제까지가 kickoff이고, 어디서부터가 진짜 code edit인가?

### 가장 짧은 답
- **프로그래머/QA/PM만 Commit 1 kickoff의 즉시 행동 오너**다.
- UI/아트는 Commit 1에서 **payload sanity / boundary 대기**까지만 본다.
- 첫 편집 전에는 반드시 `stage archive + manifest 초안 + stub 첫 줄 + semantic anchor + 금지 범위 선언`이 있어야 한다.
- 이 다섯 개가 없으면 아직 kickoff도 끝나지 않은 상태다.

### Commit 1에서 절대 하지 않는 것
- reward apply / page advance / resolve wiring
- scene card shell 마감
- overrun flavor polish
- onboarding B-track helper/primer 착수
- Commit 2 evidence 파일 선작성

---

## 1. 지금 누가 움직이고 누가 기다리는가

| 역할 | 지금 상태 | Commit 1에서 하는 일 | Commit 1에서 하지 않는 일 |
| --- | --- | --- | --- |
| 프로그래머 | 시작 가능 | archive/stub 생성, semantic anchor 재확인, floor-only edit, state diff 정리 | resolve/UI/closing 혼입 |
| QA | 시작 가능 | Commit 1 smoke 범위 잠금, smoke log stub append, non-change 검수 | Commit 2 fixture 수행 |
| PM/기획 | 시작 가능 | stop/go 확인, manifest/handoff line 점검, run log 문장 guard | green 선기록 |
| UI | 대기 | payload sanity note 확인, scene shell 미오픈 확인 | scene-card layout closing |
| 아트 | 대기 | token/accent drift 경계만 확인 | 새 장면 자산/톤 마감 |

### 한 줄 판단 규칙
- Commit 1 kickoff는 `프로그래머 + QA + PM`의 floor turn이다.
- UI/아트는 Commit 2 closing 전까지 **기다리는 것이 올바른 참여**다.

---

## 2. Commit 1 첫 10분 kickoff 순서

## 3줄 요약
- 첫 10분은 code edit보다 먼저 **archive / 문장 / anchor / freeze**를 잠그는 시간이다.
- 이 순서를 생략하면 첫 커밋이 다시 `감으로 자르는 커밋`이 된다.
- 각 단계는 끝날 때 산출물이 있어야 하며, 말로만 `확인함`은 완료로 보지 않는다.

### T-10 ~ T-8: stage archive 생성
1. `docs/artifacts/YYYYMMDD/commit1-floor/` 생성
2. 하위 폴더 `logs/`, `captures/`, `notes/` 생성
3. 아래 starter file 복사 또는 생성
   - `manifest.md`
   - `logs/YYYYMMDD-commit1-qa-smoke-log.md`
   - `notes/YYYYMMDD-commit1-programmer-state-diff.md`
   - `notes/YYYYMMDD-commit1-pm-nonchange-note.md`
   - `notes/YYYYMMDD-commit1-programmer-payload-sanity.md`

**완료 기준**
- 폴더와 파일이 실제로 생성돼 있다.
- 아직 green 문장은 쓰지 않는다.

### T-8 ~ T-6: manifest + stub 첫 줄 채우기
1. `manifest.md`에 아래 4가지를 먼저 적는다.
   - `status: bootstrap-ready`
   - `owner summary: Commit 1 floor enter/snapshot floor만 연다`
   - `boundary / non-change: reward apply / page advance / onboarding 미오픈`
   - `next allowed step: semantic anchor 확인 후 floor edit`
2. stub 파일 첫 줄을 stage-specific 문장으로 채운다.
   - smoke log: `이 로그는 Commit 1 floor smoke 범위만 기록한다.`
   - state diff: `이번 diff는 enter/snapshot floor만 기록하고 reward apply/page advance는 아직 열지 않는다.`
   - non-change note: `이번 note는 Commit 1 비변경선만 기록한다.`
   - payload sanity: `contentKey/badgeLabel/accentKey/artTokenSet payload shape만 확인한다.`

**완료 기준**
- 빈 파일이 없다.
- `TODO`, `later`, `final`, `거의 끝` 같은 generic placeholder가 없다.

### T-6 ~ T-4: semantic anchor 재확인
1. 아래 semantic zone을 실제 코드에서 다시 찾는다.
   - `continueOverrun`
   - `renderCenter`
   - overrun smoke block
2. `notes/...state-diff.md`에 찾은 locator를 적는다.
   - 함수명
   - grep token
   - 아직 건드리면 안 되는 주변 책임
3. line drift가 있더라도 stage는 넓히지 않는다.

**완료 기준**
- `line이 조금 밀려도 같은 책임을 찾았다`는 메모가 note에 남아 있다.
- `이 김에 renderEnding도 같이 보자` 같은 stage 확장 메모가 없다.

### T-4 ~ T-2: surgery + rehearsal freeze
1. `commit1 surgery sheet` 기준으로 제거할 즉시 적용 줄과 남길 floor를 다시 선언한다.
2. `runtime rehearsal packet` 기준으로 금지 범위를 적는다.
   - reward apply 금지
   - page advance 금지
   - scene polish 금지
   - onboarding helper 금지
3. PM은 manifest의 `next allowed step`이 여전히 Commit 1 floor인지 확인한다.

**완료 기준**
- manifest와 non-change note 둘 다 같은 금지 범위를 말한다.
- kickoff 메모와 handoff green 문장이 섞이지 않는다.

### T-2 ~ T+10: floor-only edit + 첫 evidence drop
1. `continueOverrun()`를 enter/snapshot floor 의미로 좁힌다.
2. canonical snapshot floor와 normalize/recovery를 Commit 1 범위까지만 반영한다.
3. edit 후 즉시 아래 evidence를 채운다.
   - smoke log: S1/S2/S3/S7/S8/S9/S10/S11 범위
   - state diff: 바뀐 값 / 안 바뀐 값
   - non-change note: reward/page/onboarding 미오픈 유지
4. evidence가 없으면 green 금지

**완료 기준**
- Commit 1 evidence bundle 초안이 archive 안에 존재한다.
- 아직 Commit 2나 B-track 문장은 없다.

---

## 3. semantic anchor 재확인 규칙

| semantic zone | Commit 1에서 찾는 이유 | 찾은 뒤 기록해야 하는 것 | 찾았다고 해서 열면 안 되는 것 |
| --- | --- | --- | --- |
| `continueOverrun` | 즉시 적용 monolith를 floor-only entry로 줄이기 위해 | 현재 책임 / 제거 대상 / 유지 대상 | resolve wiring |
| `renderCenter` | explicit `overrunEvent` branch 최소선 확인 | branch 미오픈 또는 최소선 상태 | scene-card UI 마감 |
| overrun smoke block | Commit 1 fixture 범위 분리 | S1/S2/S3/S7/S8/S9/S10/S11만 Commit 1임을 메모 | S4/S5/S6/S12 수행 |
| `renderEnding` | Commit 1에선 직접 수정 금지 zone임을 확인 | non-change note에 `ending direct closing 미오픈` 기입 | ending helper 변경 |

### 프로그래머 handoff 포인트
- `state-diff.md`에 semantic locator를 먼저 적고 diff를 시작한다.
- line number는 참고값이고, **책임 경계가 계약**이다.

### QA/PM handoff 포인트
- review 코멘트도 line보다 `semantic zone` 이름으로 남긴다.
- `renderEnding도 슬쩍 손봤다`가 보이면 Commit 1 fail에 가깝다.

---

## 4. Commit 1 edit 경계

## 한 줄 목표
Commit 1은 `enter/snapshot floor + recovery + non-change evidence`까지만 닫는다.

| 구분 | Commit 1에서 해야 하는 것 | Commit 1에서 하지 않는 것 |
| --- | --- | --- |
| 엔진 | `phase='overrunEvent'` 진입, canonical snapshot floor, normalize/recovery | reward apply, overrunLevel/page/currentPage 진행 |
| 렌더 | floor를 읽을 최소 branch 확인 | scene-card shell/tone/hierarchy closing |
| QA | Commit 1 fixture smoke | Commit 2 closing fixture |
| 기록 | floor evidence, non-change line, Commit 1 문장 후보 | runtime-ready closing 문장 |

### 이 경계가 중요한 이유
- Commit 1은 **중간 상태를 안전하게 만드는 커밋**이다.
- Commit 2는 **보이는 장면과 resolve를 닫는 커밋**이다.
- 둘을 합치면 다시 half-cutover가 생긴다.

---

## 5. owner별 handoff 포인트

### 5-1. 프로그래머
- 먼저 넘길 것
  - `manifest.md` 초안
  - `state-diff.md` semantic locator 메모
  - `nonchange-note.md` 금지 범위
- 나중에 넘길 것
  - smoke 결과 반영 후 updated state diff
- 넘기지 말 것
  - Commit 2 scene/resolve 계획
  - B-track primer 범위

### 5-2. UI
- 지금 받을 것
  - `payload-sanity.md`의 canonical field set 확인 메모
- 지금 받지 않을 것
  - layout closing 요청
  - scene-card polish 요청
- Commit 1에서 해야 할 한 줄
  - `payload floor는 보이지만 scene shell closing은 아직 아니다.`

### 5-3. 아트
- 지금 받을 것
  - Commit 1에서는 별도 자산 요청 없음
  - 필요 시 `none`/`theme`/`enemy` token drift 금지 메모만 확인
- 지금 받지 않을 것
  - tone board, 새 key art, 카드 외형 마감
- Commit 1에서 해야 할 한 줄
  - `아트는 Commit 2 closing 전까지 대기 상태다.`

### 5-4. QA
- 먼저 받을 것
  - Commit 1 smoke log stub
  - fixture 범위: `S1/S2/S3/S7/S8/S9/S10/S11`
- 반드시 적을 것
  - 이번 로그가 다루지 않는 범위
  - 다음 append 위치
- 금지
  - `전체 smoke ok`
  - fixture 번호 없는 pass 문장

### 5-5. PM/기획
- 먼저 확인할 것
  - manifest의 `status`, `next allowed step`, `handoff line candidate`
  - non-change note의 금지 범위
- 지금 하지 않을 것
  - Commit 1 green 선기록
  - `overrunEvent 진행` 같은 큰 문장 기록
- Commit 1에서 해야 할 한 줄
  - `이번 턴은 Commit 1 floor kickoff와 evidence 생성까지만 연다.`

---

## 6. Commit 1 금지 패턴

1. `archive는 나중에 정리하고 먼저 코드부터` 시작하기
2. manifest는 채웠는데 stub 파일을 비워 두기
3. semantic anchor를 찾는 김에 Commit 2 zone까지 편집하기
4. UI/아트에게 scene polish를 먼저 요청하기
5. QA log에 fixture 번호 없이 `ok`만 쓰기
6. run log에 `오버런 구현 진행` 같은 큰 문장 남기기
7. Commit 1 evidence 없이 Commit 2를 열어 보기

### 금지 이유
이 패턴들은 전부 `kickoff는 끝나지 않았는데 edit만 먼저 진행된 상태`를 만든다. 그 상태가 가장 위험하다.

---

## 7. evidence drop 규칙

## Commit 1 최소 evidence bundle
1. `manifest.md`
2. `logs/...commit1...smoke-log.md`
3. `notes/...state-diff.md`
4. `notes/...nonchange-note.md`
5. 필요 시 `notes/...payload-sanity.md`

### evidence drop 순서
1. smoke 범위 기입
2. state diff 기입
3. non-change line 기입
4. manifest 상태를 `archive-in-progress` 또는 근거 있는 최신 상태로 업데이트
5. handoff candidate는 manifest에만 갱신

### capture에 대한 규칙
- Commit 1은 fake capture 금지
- 필요 시 payload note에 reservation만 적는다
- 실제 시각 evidence는 Commit 2 이후로 넘긴다

---

## 8. stop / go 규칙

| 신호 | 의미 | 즉시 행동 |
| --- | --- | --- |
| `renderEnding`까지 같이 건드리고 싶다 | Commit 2 혼입 | 즉시 stop, non-change note 재확인 |
| smoke 범위를 `전부 한번에` 돌리고 싶다 | fixture drift | Commit 1 scope로 복귀 |
| manifest/stub가 비어 있다 | kickoff 미완료 | edit stop 후 archive 바닥부터 채움 |
| UI/아트가 shell closing을 시작했다 | 역할 drift | Commit 2 전 대기선 재공지 |
| B-track helper를 같이 넣고 싶다 | track drift | B-track 금지 문장 재확인 |

### go 판단
아래가 모두 있으면 Commit 1 floor edit를 계속해도 된다.
- archive root 존재
- manifest 초안 존재
- stub 첫 줄 존재
- semantic anchor 메모 존재
- 금지 범위 선언 존재

---

## 9. tracker / run log 기록 문장 가드

### kickoff 메모로 허용되는 문장
- `Commit 1 floor kickoff: archive/stub/semantic anchor/preflight 완료 후 floor-only edit 착수`
- `Commit 1 floor evidence는 commit1-floor stage archive에 수집 중`

### 아직 금지되는 문장
- `overrunEvent 구현 진행`
- `runtime-ready 전환`
- `scene/resolve 닫힘`
- `온보딩 다음 단계 준비 완료`

### green 직전 문장 후보
- `Commit 1 floor는 enter/snapshot floor와 non-change evidence까지 archive-in-progress 상태로 수집했다.`

> 실제 green 문장은 `execution reporting packet`과 `filled manifest examples` 기준으로만 확정한다.

---

## 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| archive/stub/anchor/surgery 문서가 충분해도 첫 live turn에서 순서가 다시 owner별 감각으로 갈라질 위험 | 첫 커밋이 다시 `감으로 자르는 커밋`이 되고 Commit 1/2 경계가 흐려진다 | 이 kickoff packet으로 첫 10분 순서와 역할별 대기선을 한 장으로 다시 고정 | 다음 Commit 1 착수 직전 |
| kickoff 메모와 green 문장이 다시 섞일 위험 | preflight만 끝났는데 완료처럼 기록될 수 있다 | `kickoff 메모`와 `green 문장`을 분리해 명시하고 manifest 전용 문장 가드를 다시 고정 | 다음 run log/tracker 작성 직전 |
| UI/아트가 Commit 1에서 너무 빨리 scene closing 감각으로 들어갈 위험 | payload floor보다 시각 마감이 앞서 drift가 생긴다 | Commit 1을 프로그래머/QA/PM floor turn으로 재고정하고 UI/아트는 대기로 명시 | Commit 1 edit 시작 직전 |

---

## 즉시 다음 액션
1. `docs/artifacts/YYYYMMDD/commit1-floor/` stage archive를 먼저 만든다.
2. starter manifest와 Commit 1 stub 4종 첫 줄을 채운다.
3. `continueOverrun` / `renderCenter` / overrun smoke block semantic anchor를 다시 찾는다.
4. `commit1 surgery sheet`와 `runtime rehearsal packet` 기준 금지 범위를 선언한다.
5. 그 뒤에만 Commit 1 floor edit와 S1/S2/S3/S7/S8/S9/S10/S11 evidence 수집을 시작한다.
