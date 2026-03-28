# DiceSpell 첫 런 온보딩 `B-1 library` live kickoff packet

## 3줄 요약
- 이 문서는 `B-1 library` packet / runtime stack / owner workboard / evidence archive 문서가 이미 충분한 상태에서도, **실제 첫 library primer 커밋을 열기 직전 마지막 실행 공백**을 닫기 위한 source-of-truth다.
- 핵심은 새 primer 설계를 더하는 것이 아니라, `좋아, Commit 2 green 뒤 이제 B-1을 실제로 열자. 그러면 첫 10분 안에 어떤 archive를 만들고, 어떤 seen flag / render slot / evidence를 먼저 잠가야 하지?`를 **한 장의 kickoff 순서**로 압축하는 것이다.
- 목표는 다음 implementer가 온보딩 문서를 다시 큐레이션하지 않고도, **B-1 library kickoff -> 첫 edit -> 첫 acceptance evidence -> B-2 unlock 기록**까지 같은 stage 언어로 시작하게 만드는 것이다.

**한 줄 목표:** `B-1 library` 첫 live turn을 `stage archive 생성 -> manifest/stub 초안 -> semantic anchor 재확인 -> library-only edit -> acceptance evidence drop`의 5단계 kickoff로 고정해, 온보딩이 다시 `튜토리얼 전체 붙이기`로 부풀지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 DiceSpell의 첫 런 온보딩 축은 문서상으로는 충분하다.

이미 있는 것:
- 순서와 착수선: `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- cross-track 실행 큐: `docs/dicespell_overrun_onboarding_cutover_master_plan_kr.md`
- B-track commit 스택: `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- B-1 surface contract: `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- primer content source: `docs/dicespell_first_run_onboarding_content_deck_kr.md`
- UI slot source: `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- owner handoff: `docs/dicespell_first_run_onboarding_owner_workboard_kr.md`
- evidence/archive 규칙: `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`, `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`

그런데 실제 `B-1 library` 첫 live turn 직전에는 아직 아래가 남는다.

1. `문서는 충분한데, 그래서 첫 10분에 무엇부터 해야 하지?`가 여러 문서에 흩어져 있다.
2. `seenBookSelectPrimer`, `renderGrimoireShelf()`, `openingPlan/caution highlight`, `library acceptance evidence`가 각각은 닫혀 있어도, **한 번의 실전 kickoff 순서**로는 아직 압축돼 있지 않다.
3. 프로그래머는 첫 diff를 열기 전 `meta.uiGuidance`와 `library.book-select` payload를 어디까지 잠가야 하는지, UI/아트/QA/PM은 `언제부터 actual closing이고 어디까지가 아직 대기/검수 턴인지`를 같은 문장으로 공유해야 한다.
4. 이 순서가 없으면 첫 온보딩 turn에서도 다시 `battle도 같이`, `reward/shop 문구도 같이`, `ending helper도 미리` 같은 drift가 생긴다.

즉 이 문서는 새 primer packet이 아니라, **B-1 library 첫 live execution을 여는 정확한 시작 순서 packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 10분 kickoff`, `3. semantic anchor`, `4. edit 경계` | `B-1 library packet`, `screen packet`, `runtime stack` | B-1은 `library-only edit`이며 `battle/reward/shop/ending`을 같이 열면 안 된다 |
| UI | `1. 누가 지금 움직이는가`, `5-2. UI`, `6. 금지 패턴` | `screen packet`, `content deck`, `owner workboard` | B-1은 shelf 전체 리디자인 턴이 아니라 hero-primer + highlight density만 닫는 턴이다 |
| 아트 | `1. 누가 지금 움직이는가`, `5-3. 아트`, `6. 금지 패턴` | `content deck`, `screen packet` | 새 key art보다 primer badge/highlight token sanity가 우선이다 |
| QA | `2. 10분 kickoff`, `5-4. QA`, `7. evidence drop`, `8. stop/go` | `B-1 library packet`, `acceptance matrix`, `evidence manifest` | B-1 evidence는 library acceptance/recovery만 기록하고 battle 이후 surface는 아직 기록하지 않는다 |
| PM/기획 | `0. first screen 결론`, `5-5. PM`, `8. stop/go`, `9. 기록 문장` | `readiness board`, `runtime stack`, `execution reporting packet` | kickoff 메모와 `B-1 green` 문장은 다르며, B-2 unlock은 evidence 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `B-1 library`를 실제로 열 때 첫 10분 안에 무엇부터 해야 하지?
- B-1 packet / screen packet / content deck / owner workboard는 어떤 순서로 겹쳐 봐야 하지?
- 누가 지금 edit를 하고, 누가 아직 대기 또는 경량 검수여야 하지?
- 언제까지가 kickoff이고, 어디서부터가 진짜 library primer edit인가?

### 가장 짧은 답
- **프로그래머 / UI / QA / PM이 B-1 kickoff의 즉시 행동 오너**다.
- 아트는 B-1에서 **badge / accent / highlight sanity 확인**까지만 관여한다.
- 첫 편집 전에는 반드시 `stage archive + manifest 초안 + B-1 stub 첫 줄 + semantic anchor + 금지 범위 선언`이 있어야 한다.
- 이 다섯 개가 없으면 아직 kickoff도 끝나지 않은 상태다.

### B-1에서 절대 하지 않는 것
- `battle.entry` / `battle.first-spell` / `battle.auto-pass` primer 착수
- reward/shop primer 문구 선반영
- ending helper / overrun CTA helper 선반영
- shelf 전체 레이아웃 리디자인
- library primer evidence가 없는 상태에서 `B-2 unlock` 기록

---

## 1. 지금 누가 움직이고 누가 기다리는가

| 역할 | 지금 상태 | B-1에서 하는 일 | B-1에서 하지 않는 일 |
| --- | --- | --- | --- |
| 프로그래머 | 시작 가능 | archive/stub 생성, semantic anchor 재확인, `seenBookSelectPrimer` / hero-primer / highlight floor edit, state diff 정리 | battle/reward/shop/ending helper 추가 |
| UI | 시작 가능 | hero-primer 위치, `openingPlan` / `caution` highlight 밀도, shelf 비차단성 검수 | shelf 전체 재배치, primer family 확장 |
| 아트 | 경량 검수 가능 | badge/highlight/accent token sanity 메모 | 신규 대형 아트 패키지 제작 |
| QA | 시작 가능 | B1-A1~A5 acceptance/recovery 증거 잠금, library-only smoke/캡처 기록 | B-2/B-3/B-4 evidence 선기록 |
| PM/기획 | 시작 가능 | stop/go 확인, manifest/handoff line 가드, `B-1 green` 문장 관리 | `온보딩 진행 중` 같은 큰 문장 선기록 |

### 한 줄 판단 규칙
- B-1 kickoff는 `프로그래머 + UI + QA + PM`의 library-only turn이다.
- 아트는 `지금 당장 많이 만드는 사람`이 아니라, **token drift를 먼저 막는 확인자**로 참여하는 것이 맞다.

---

## 2. B-1 첫 10분 kickoff 순서

## 3줄 요약
- 첫 10분은 code edit보다 먼저 **archive / 문장 / anchor / freeze**를 잠그는 시간이다.
- 이 순서를 생략하면 첫 온보딩 커밋이 다시 `library도 하고 battle도 살짝` 같은 감각 기반 커밋이 된다.
- 각 단계는 끝날 때 산출물이 있어야 하며, 말로만 `확인함`은 완료로 보지 않는다.

### T-10 ~ T-8: stage archive 생성
1. `docs/artifacts/YYYYMMDD/b1-library/` 생성
2. 하위 폴더 `logs/`, `captures/`, `notes/` 생성
3. 아래 starter file 복사 또는 생성
   - `manifest.md`
   - `logs/YYYYMMDD-b1-library-qa-acceptance-log.md`
   - `notes/YYYYMMDD-b1-library-programmer-state-diff.md`
   - `notes/YYYYMMDD-b1-library-ui-hierarchy-note.md`
   - `notes/YYYYMMDD-b1-library-pm-boundary-note.md`

**완료 기준**
- 폴더와 파일이 실제로 생성돼 있다.
- 아직 `B-1 green` 문장은 쓰지 않는다.

### T-8 ~ T-6: manifest + stub 첫 줄 채우기
1. `manifest.md`에 아래 4가지를 먼저 적는다.
   - `status: bootstrap-ready`
   - `owner summary: B-1 library primer만 연다`
   - `boundary / non-change: battle/reward/shop/ending primer 미오픈`
   - `next allowed step: semantic anchor 확인 후 library-only edit`
2. stub 파일 첫 줄을 stage-specific 문장으로 채운다.
   - acceptance log: `이 로그는 B-1 library acceptance/recovery만 기록한다.`
   - state diff: `이번 diff는 seenBookSelectPrimer와 library hero-primer만 기록하고 다른 surface primer는 아직 열지 않는다.`
   - hierarchy note: `library primer는 shelf 선택을 막지 않는 hero-primer 1장과 highlight 2곳만 확인한다.`
   - boundary note: `이번 note는 B-1 비확장선만 기록한다.`

**완료 기준**
- 빈 파일이 없다.
- `TODO`, `나중에`, `이따 battle도`, `온보딩 전체` 같은 generic placeholder가 없다.

### T-6 ~ T-4: semantic anchor 재확인
1. 아래 semantic zone을 실제 코드에서 다시 찾는다.
   - `readMetaProgress` / `persistMetaProgress`
   - `renderGrimoireShelf`
   - `openingPlan` / `caution` render slot
2. `notes/...state-diff.md`에 찾은 locator를 적는다.
   - 함수명
   - 현재 primer 미삽입 baseline
   - 아직 건드리면 안 되는 주변 책임
3. line drift가 있더라도 stage는 넓히지 않는다.

**완료 기준**
- `line이 조금 밀려도 같은 책임을 찾았다`는 메모가 note에 남아 있다.
- `이 김에 battle primer slot도 같이 보자` 같은 stage 확장 메모가 없다.

### T-4 ~ T-2: packet + boundary freeze
1. `B-1 library packet` 기준으로 허용 범위를 다시 선언한다.
   - `seenBookSelectPrimer` 1필드
   - `library.book-select` hero-primer 1장
   - `openingPlan` / `caution` highlight 2곳
2. `screen packet` / `content deck` 기준으로 금지 범위를 적는다.
   - battle/reward/shop/ending primer 금지
   - shelf 전체 레이아웃 재설계 금지
   - primer가 책 선택 CTA를 가리는 밀도 금지
3. PM은 manifest의 `next allowed step`이 여전히 `B-1 library`인지 확인한다.

**완료 기준**
- manifest와 boundary note 둘 다 같은 금지 범위를 말한다.
- kickoff 메모와 `B-1 green` handoff 문장이 섞이지 않는다.

### T-2 ~ T+10: library-only edit + 첫 evidence drop
1. `meta.uiGuidance` 또는 동등 위치에 `seenBookSelectPrimer` floor를 추가한다.
2. `renderGrimoireShelf()`에 `library.book-select` hero-primer와 `openingPlan` / `caution` highlight를 B-1 범위까지만 반영한다.
3. edit 후 즉시 아래 evidence를 채운다.
   - acceptance log: B1-A1~A5 / recovery 3종 범위
   - state diff: 바뀐 값 / 안 바뀐 값
   - hierarchy note: hero-primer 밀도 / CTA 비차단 / highlight 가독성
   - boundary note: battle/reward/shop/ending 미오픈 유지
4. evidence가 없으면 `B-1 green` 금지

**완료 기준**
- B-1 evidence bundle 초안이 archive 안에 존재한다.
- 아직 B-2 / B-3 / B-4 문장은 없다.

---

## 3. semantic anchor 재확인 규칙

| semantic zone | B-1에서 찾는 이유 | 찾은 뒤 기록해야 하는 것 | 찾았다고 해서 열면 안 되는 것 |
| --- | --- | --- | --- |
| `readMetaProgress` / `persistMetaProgress` | primer 노출/재노출 상태를 library-only 범위로 저장하기 위해 | `seenBookSelectPrimer` 위치와 recovery 메모 | battle/reward/shop/ending seen flag 선추가 |
| `renderGrimoireShelf` | hero-primer / highlight 주입 slot을 library surface 안에서만 닫기 위해 | primer 삽입 위치, CTA 비차단 메모 | shelf 구조 전면 개편 |
| `openingPlan` / `caution` block | library primer가 정확히 어떤 기존 정보 slot을 보강하는지 확인하기 위해 | highlight 대상과 카피 밀도 메모 | 새로운 info family 추가 |
| library acceptance path | B1-A1~A5 증거를 library-only로 잠그기 위해 | acceptance/recovery 범위 메모 | B-2 battle acceptance 선기록 |

### 프로그래머 handoff 포인트
- `state-diff.md`에 semantic locator를 먼저 적고 diff를 시작한다.
- line number는 참고값이고, **surface 책임 경계가 계약**이다.

### UI/QA/PM handoff 포인트
- review 코멘트도 line보다 `semantic zone` 이름으로 남긴다.
- `battle primer slot도 미리 손봤다`가 보이면 B-1 fail에 가깝다.

---

## 4. B-1 edit 경계

## 한 줄 목표
B-1은 `library hero-primer + seen flag + highlight + acceptance evidence`까지만 닫는다.

| 구분 | B-1에서 해야 하는 것 | B-1에서 하지 않는 것 |
| --- | --- | --- |
| 상태 | `seenBookSelectPrimer` 또는 동등 library-only flag 추가 | battle/reward/shop/ending용 seen flag 선추가 |
| 렌더 | `library.book-select` hero-primer 1장, `openingPlan` / `caution` highlight 보강 | primer family 확장, extra panel 추가 |
| UX | primer가 책 선택을 막지 않는지 확인 | shelf 흐름을 tutorial modal처럼 바꾸는 것 |
| 기록 | B1-A1~A5 acceptance/recovery evidence, `B-1 green` 후보 문장 | B-2 unlock 선기록 |

### 이 경계가 중요한 이유
- B-1은 첫 온보딩 surface라서 가장 쉽게 `온보딩 전체 착수`처럼 범위가 커진다.
- library에서 battle/reward/shop/ending까지 같이 열면 `surface 1개 = commit 1개` 규칙이 무너진다.
- 이번 packet의 목적은 primer를 많이 넣는 것이 아니라, **첫 surface를 production handoff 단위로 닫는 것**이다.

---

## 5. 역할별 handoff

### 5-1. 프로그래머
- B-1의 핵심은 `seenBookSelectPrimer`와 `renderGrimoireShelf()` library-only 주입이다.
- `runtime stack`은 순서를 말해 주지만, 실제 수정 계약은 `B-1 library packet`과 이 kickoff packet이 맡는다.
- `battle.entry`, `reward.first`, `ending.overrun-cta` payload를 같이 만지기 시작하면 B-1 범위를 벗어난다.

### 5-2. UI
- 이번 턴의 UI 마감 범위는 `hero-primer 1장 + highlight 2곳 + CTA 비차단`이다.
- primer는 `책을 고르기 전에 읽히되, 책을 고르는 행동을 막지 않아야` 한다.
- shelf 전체 비주얼 refresh는 이 턴의 성공 조건이 아니다.

### 5-3. 아트
- 이번 턴 산출물은 새 일러스트 패키지가 아니라, `badge/accent/highlight drift 없음` 확인이다.
- `library.book-select`는 library surface의 저밀도 primer여야 하며, `battle`이나 `reward`처럼 다른 표정을 가져오면 안 된다.
- 신규 자산이 정말 필요하더라도 이 턴엔 `예외 필요 판정`까지만 남긴다.

### 5-4. QA
- 이번 턴의 pass/fail은 `B1-A1~A5 acceptance + recovery 3종 + library 비차단성`으로 본다.
- `battle primer도 곧 붙일 예정` 같은 미래 계획은 QA pass 근거가 아니다.
- evidence는 `library-only`라는 문장이 빠지면 안 된다.

### 5-5. PM/기획
- 지금 남길 수 있는 green 문장은 `B-1 library primer`뿐이다.
- `온보딩 진행`, `첫 런 가이드 착수`, `library/battle 초안 완료` 같은 큰 문장은 금지한다.
- B-2 unlock은 B-1 evidence bundle이 tracker/run log/portal에 반영된 뒤에만 쓴다.

---

## 6. 금지 패턴

1. `seenBattlePrimer`, `seenRewardPrimer`, `seenVictoryPrimer`까지 한 번에 추가하는 것
2. `renderBattle()` / `renderReward()` / `renderEnding()` primer slot을 같이 열기 시작하는 것
3. `library.book-select` primer를 modal처럼 키워 shelf 선택을 가리는 것
4. `openingPlan` / `caution`을 새 정보 패널 family로 분리해 B-1 scope를 키우는 것
5. primer proof가 없는 상태에서 `B-2 battle ready`를 기록하는 것
6. `summary`만 읽고 source packet 확인 없이 구현/리뷰를 시작하는 것

---

## 7. evidence drop 규칙

## 한 줄 목표
B-1 증거는 `library-only acceptance bundle`로만 남긴다.

| 증거 | 최소 내용 | owner |
| --- | --- | --- |
| `manifest.md` | `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line` | PM/프로그래머 |
| acceptance log | B1-A1~A5, recovery 3종, 비차단성 판정 | QA |
| state diff note | `seenBookSelectPrimer` 추가 여부, 다른 primer flag 미추가, 관련 render hook | 프로그래머 |
| hierarchy note | hero-primer 위치, highlight 가독성, CTA 비가림 여부 | UI |
| boundary note | `battle/reward/shop/ending 미오픈`, `B-2 unlock은 green 뒤` | PM |
| capture | library surface 1~2장, primer on/off 또는 first-open evidence | UI/QA |

### 파일명 규칙
- generic `final.png`, `latest-note.md`, `done.txt` 금지
- stage/owner가 보이도록 아래 형식을 권장한다.
  - `captures/YYYYMMDD-b1-library-open-state.png`
  - `captures/YYYYMMDD-b1-library-primer-density.png`
  - `notes/YYYYMMDD-b1-library-programmer-state-diff.md`
  - `logs/YYYYMMDD-b1-library-qa-acceptance-log.md`

---

## 8. stop / go 판정

### GO 조건
- `manifest.md`와 stub가 stage-specific 문장으로 채워져 있다.
- semantic anchor note가 있다.
- B-1 edit가 `library-only` 범위를 벗어나지 않았다.
- acceptance / state diff / hierarchy / boundary evidence가 archive 안에 있다.

### STOP 신호
- battle/reward/shop/ending primer 코드가 같이 열렸다.
- `seenBookSelectPrimer` 외 다른 primer flag가 생겼다.
- library primer가 CTA를 가려 `책 선택 비차단`이 무너졌다.
- `B-1 green` 기록이 evidence보다 먼저 나왔다.

### STOP 이후 다음 행동
1. stage를 넓히지 말고 B-1 archive 안에서 boundary note를 먼저 갱신한다.
2. `무엇을 덜어내야 B-1로 돌아오는가`를 기록한다.
3. 필요하면 이번 턴은 `B-1 skipped(partial)`로 남기고, B-2는 열지 않는다.

---

## 9. tracker / run log 기록 문장

### kickoff 문장
> B-1 library kickoff: archive/stub/semantic anchor/preflight를 먼저 잠근 뒤 library-only primer edit에 착수한다.

### green 문장
> 이번 커밋은 `B-1 library` primer만 닫아 `seenBookSelectPrimer`, `library.book-select` hero-primer, `openingPlan/caution` highlight, library acceptance/recovery evidence를 고정했고, battle/reward/shop/ending primer는 아직 열지 않았다.

### 다음 단계 unlock 문장
> B-1 evidence bundle이 tracker/run log/portal에 반영됐으므로 다음 단계는 `B-2 battle`만 연다.

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| B-1이 다시 `온보딩 전체 착수`처럼 부풀어 battle/reward/shop/ending까지 섞이는 위험 | surface 1개 = commit 1개 discipline 붕괴 | library-only kickoff 순서와 금지 범위를 packet으로 고정 | B-1 첫 live edit 직전 |
| `seenBookSelectPrimer` 외 다른 flag를 선추가하는 위험 | recovery/debug 범위가 불필요하게 커짐 | state diff note에 non-change line을 먼저 쓰게 규정 | B-1 리뷰 시 |
| UI가 primer 가독성을 이유로 shelf 전체 재배치를 시작하는 위험 | primer commit이 UI refactor commit으로 변질 | hero-primer 1장 + highlight 2곳 + CTA 비차단만 closing 범위로 고정 | B-1 UI review 시 |
| PM이 B-1 green 전에 B-2 unlock을 기록하는 위험 | half-cutover가 완료처럼 보임 | kickoff/green/unlock 문장을 분리 | tracker/run log 작성 시 |
| 아트가 신규 자산 필요성을 과대평가하는 위험 | 작은 primer turn이 asset blocker처럼 보임 | B-1 아트 기본 역할을 token sanity로 한정 | B-1 아트 sanity review 시 |

---

## 11. 즉시 다음 액션
1. `Commit 2 green`이 확인되면 implementer는 `readiness board -> onboarding runtime stack -> B-1 library packet -> 이 live kickoff packet` 순서로 읽고 시작한다.
2. `docs/artifacts/YYYYMMDD/b1-library/` archive부터 만든다.
3. manifest와 B-1 stub 첫 줄을 채운다.
4. semantic anchor를 다시 찾는다.
5. 그 뒤에만 B-1 library-only edit와 acceptance evidence 수집을 시작한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_summary_kr.md`
- `docs/dicespell_first_run_onboarding_runtime_stack_packet_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- `docs/dicespell_first_run_onboarding_content_deck_kr.md`
- `docs/dicespell_first_run_onboarding_owner_workboard_kr.md`
- `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
