# DiceSpell `Run Table` Battle Stakes Plaque live kickoff packet

## 3줄 요약
- 이 문서는 `battle stage packet`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도, **atlas green 뒤 실제 battle 첫 10분을 어떤 순서로 열어야 하는지** 남아 있던 마지막 실행 공백을 닫는 source-of-truth다.
- 핵심은 새 전투 화면을 더 설계하는 것이 아니라, `좋아, 이제 battle이 unlock됐다면 첫 10분 안에 어떤 archive를 만들고 어떤 stakes hook / hierarchy freeze / evidence 예약을 먼저 잠가야 하지?`를 **한 장의 kickoff 순서**로 압축하는 것이다.
- 목표는 다음 implementer / UI / 아트 / QA / PM이 battle 턴을 다시 `전투 전체 polish`로 부풀리지 않고, **battle kickoff -> stakes-only edit -> required artifacts evidence drop -> candidate review-ready**까지 같은 stage 언어로 시작하게 만드는 것이다.

**한 줄 목표:** `battle-stakes-plaque` 첫 live turn을 `stage archive 생성 -> manifest/stub 초안 -> stakes semantic locator 재확인 -> battle-only hook edit -> required artifacts evidence drop` 5단계 kickoff로 고정해, atlas 다음 refinement가 다시 `action panel도 조금`, `oath도 미리`, `reward/shop도 겸사` 같은 확장으로 번지지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 battle second-stage 문서 세트는 거의 충분하다.

이미 있는 것:
- 실행 큐 / unlock 순서: `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- stage readiness / unlock discipline: `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- battle 범위 계약: `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- required artifacts 8종: `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- candidate -> green review 절차: `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
- archive starter rule: `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- 문서 위계: `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- 현재 미완료 gap: `docs/dicespell_game_flow_token_gap_ledger_kr.md`

그런데 실제 battle live turn 직전에는 아직 아래 공백이 남아 있었다.

1. `문서는 충분한데, 그래서 atlas green 뒤 battle 첫 10분에 무엇부터 해야 하지?`가 여러 packet에 흩어져 있다.
2. `battle-stakes-plaque` stage boundary, `required artifacts`, `candidate review`는 각각 닫혀 있어도, **실전 kickoff 순서**로는 아직 압축돼 있지 않다.
3. 프로그래머는 `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"`를 어디까지 잠가야 하는지, UI/아트/QA/PM은 `언제부터 actual battle closing이고 어디까지가 아직 kickoff 메모인지`를 같은 문장으로 공유해야 한다.
4. 이 순서가 없으면 battle 첫 턴에서도 다시 `dice tray도 같이`, `action panel도 같이`, `oath 쪽 문장도 미리` 같은 drift가 생긴다.

즉 이 문서는 새 battle stage packet이 아니라, **battle-stakes-plaque 첫 live execution을 여는 정확한 시작 순서 packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 10분 kickoff`, `3. semantic locator`, `4. edit 경계` | `battle stage packet`, `battle required artifacts`, `battle candidate review` | 이번 턴은 battle-only edit이며 atlas/oath/action panel 전체 재구성은 같이 열면 안 된다 |
| UI | `1. 누가 지금 움직이는가`, `5-2. UI`, `6. 금지 패턴` | `battle stage packet`, `token closing packet`, `battle candidate review` | battle은 `current > next > grimoire` stakes first-glance를 닫는 턴이지 전투 화면 전체 polish 턴이 아니다 |
| 아트 | `1. 누가 지금 움직이는가`, `5-3. 아트`, `6. 금지 패턴` | `token anchor packet`, `battle required artifacts` | 이번 턴 자산 일은 `Stakes Plaque Starter` sanity 확인이지 battle HUD 전체 아트 리뉴얼이 아니다 |
| QA | `2. 10분 kickoff`, `5-4. QA`, `7. evidence drop`, `8. stop/go` | `battle required artifacts`, `battle candidate review`, `queue readiness board` | battle evidence는 battle stage archive에만 기록하고 oath/queue closing은 아직 선기록하지 않는다 |
| PM/기획 | `0. first screen 결론`, `5-5. PM`, `8. stop/go`, `9. 기록 문장` | `queue readiness board`, `refinement queue`, `battle candidate review` | kickoff 메모와 `battle green` 문장은 다르며, oath unlock은 battle candidate/green 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `battle-stakes-plaque`를 실제로 열 때 첫 10분 안에 무엇부터 해야 하지?
- battle stage packet / required artifacts / candidate review / queue readiness board는 어떤 순서로 겹쳐 봐야 하지?
- 누가 지금 edit를 하고, 누가 아직 대기 또는 경량 검수여야 하지?
- 언제까지가 kickoff이고, 어디서부터가 진짜 stakes-only edit인가?

### 가장 짧은 답
- **프로그래머 / UI / QA / PM이 battle kickoff의 즉시 행동 오너**다.
- 아트는 battle에서 **starter asset sanity 확인자**로 먼저 관여한다.
- 첫 편집 전에는 반드시 `stage archive + manifest 초안 + battle stub 첫 줄 + stakes semantic locator + 금지 범위 선언`이 있어야 한다.
- 이 다섯 개가 없으면 아직 kickoff도 끝나지 않은 상태다.

### battle에서 절대 하지 않는 것
- `atlas-route-node` 재개방
- `oath-banner` stage 착수
- queue closing 문장 선기록
- action panel / dice tray / log / controls 대확장
- `Battle Table` 전체 리디자인
- battle required artifacts가 없는 상태에서 `oath unlock` 기록

---

## 1. 지금 누가 움직이고 누가 기다리는가

| 역할 | 지금 상태 | battle에서 하는 일 | battle에서 하지 않는 일 |
| --- | --- | --- | --- |
| 프로그래머 | 시작 가능 | archive/stub 생성, stakes semantic locator 재확인, battle hook/slot floor edit, hook manifest 정리 | atlas/oath hook 선추가, action panel 구조 재설계 |
| UI | 시작 가능 | overview/focus capture 예약, current/next/grimoire first-glance와 stakes hierarchy 밀도 검수 | action panel/HUD 전체 캡처 패스, oath banner visual pass |
| 아트 | 경량 검수 가능 | `Stakes Plaque Starter` sanity note, token/accent drift 확인 | 새 전투 공용 frame, oath/ending 공용 패키지 제작 |
| QA | 시작 가능 | battle required artifacts 존재/정합성 점검, candidate-ready 조건 잠금 | oath/queue closing evidence 선기록 |
| PM/기획 | 시작 가능 | stop/go 확인, manifest/handoff line 가드, `battle green` 문장 관리 | `Run Table 진행 중` 같은 큰 문장 선기록 |

### 한 줄 판단 규칙
- battle kickoff는 `프로그래머 + UI + QA + PM`의 battle-only turn이다.
- 아트는 `지금 당장 많이 만드는 사람`이 아니라, **starter sanity와 drift를 먼저 막는 확인자**로 참여하는 것이 맞다.

---

## 2. battle 첫 10분 kickoff 순서

## 3줄 요약
- 첫 10분은 code edit보다 먼저 **archive / 문장 / locator / freeze**를 잠그는 시간이다.
- 이 순서를 생략하면 battle 첫 커밋이 다시 `stakes도 하고 action panel도`, `oath tone도 미리` 같은 감각 기반 커밋이 된다.
- 각 단계는 끝날 때 실제 산출물이 있어야 하며, 말로만 `확인함`은 완료로 보지 않는다.

### T-10 ~ T-8: stage archive 생성
1. `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/` 생성 또는 starter bundle 복사
2. 하위 폴더 `captures/`, `notes/`, `checks/`, `assets/` 존재 확인
3. 아래 starter file 복사 또는 생성
   - `manifest.md`
   - `notes/hook-manifest.md`
   - `checks/dom-sanity.md`
   - `notes/density-review.md`
   - `assets/starter-asset-list.md`
   - `notes/handoff-line.md`

**완료 기준**
- 폴더와 파일이 실제로 생성돼 있다.
- 아직 `battle green` 문장은 쓰지 않는다.

### T-8 ~ T-6: manifest + stub 첫 줄 채우기
1. `manifest.md`에 아래 4가지를 먼저 적는다.
   - `status: bootstrap-ready`
   - `owner summary: battle stakes plaque만 연다`
   - `boundary / non-change: atlas/oath/queue closing/action panel 대확장 미오픈`
   - `next allowed step: stakes semantic locator 확인 후 battle-only edit`
2. stub 파일 첫 줄을 stage-specific 문장으로 채운다.
   - hook manifest: `이 메모는 battle root/current-next-grimoire stakes hook만 기록하고 atlas/oath hook은 아직 열지 않는다.`
   - dom sanity: `이 체크는 battle stakes stage만 검수한다.`
   - density review: `이번 review는 Battle Table stakes first-glance와 current/next/grimoire 읽힘만 본다.`
   - starter asset list: `이번 sanity는 Stakes Plaque Starter만 다루고 battle HUD 전체 리뉴얼은 포함하지 않는다.`
   - handoff line: `이 파일은 battle candidate/green 문장 초안만 기록한다.`

**완료 기준**
- 빈 파일이 없다.
- `TODO`, `나중에 oath도`, `전투 전체`, `거의 다 됨` 같은 generic placeholder가 없다.

### T-6 ~ T-4: stakes semantic locator 재확인
1. 아래 semantic zone을 실제 코드/마크업에서 다시 찾는다.
   - battle root surface
   - current / next / grimoire stakes slot 노출 지점
   - stakes hierarchy / plaque focus 지점
2. `notes/hook-manifest.md`에 찾은 locator를 적는다.
   - 함수명 또는 render zone
   - 현재 battle baseline
   - 아직 건드리면 안 되는 주변 책임
3. line drift가 있더라도 stage는 넓히지 않는다.

**완료 기준**
- `line이 조금 밀려도 같은 책임을 찾았다`는 메모가 남아 있다.
- `action panel도 같이 보자`, `oath tone도 미리 보자` 같은 확장 메모가 없다.

### T-4 ~ T-2: packet + boundary freeze
1. `battle stakes stage packet` 기준으로 허용 범위를 다시 선언한다.
   - battle root
   - current/next/grimoire slot hook
   - stakes first-glance
   - overview/focus capture
2. `required artifacts` / `candidate review` 기준으로 금지 범위를 적는다.
   - atlas/oath 미오픈
   - queue closing 문장 금지
   - action panel / dice tray / log / controls 대확장 금지
3. PM은 manifest의 `next allowed step`이 여전히 `battle-stakes-plaque`인지 확인한다.

**완료 기준**
- manifest와 handoff note 둘 다 같은 금지 범위를 말한다.
- kickoff 메모와 `battle green` handoff 문장이 섞이지 않는다.

### T-2 ~ T+10: battle-only edit + 첫 evidence drop
1. battle live markup 또는 동등 zone에 `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"` floor를 추가/정렬한다.
2. edit 후 즉시 아래 evidence를 채운다.
   - hook manifest
   - dom sanity
   - overview/focus capture
   - density review
   - starter asset list
   - handoff line 초안
3. evidence가 없으면 `battle archive-ready candidate` 금지

**완료 기준**
- battle evidence bundle 초안이 archive 안에 존재한다.
- 아직 oath/queue closing 문장은 없다.

---

## 3. stakes semantic locator 재확인 규칙

| semantic zone | battle에서 찾는 이유 | 찾은 뒤 기록해야 하는 것 | 찾았다고 해서 열면 안 되는 것 |
| --- | --- | --- | --- |
| battle root surface | stage scope를 `Battle Table` 안에서도 stakes plaque에만 고정하기 위해 | root locator와 battle-only 메모 | atlas/oath root 선추가 |
| stakes slot zone | current/past-next/grimoire 읽힘을 review 가능한 hook 언어로 잠그기 위해 | slot hook 이름과 검수 포인트 | action panel state vocabulary 혼입 |
| stakes hierarchy zone | current > next > grimoire 위계를 first-glance 기준으로 고정하기 위해 | hierarchy note와 캡처 포인트 | dice tray / log / controls 대확장 |
| battle evidence path | required artifacts를 battle-only로 잠그기 위해 | archive 경로와 파일명 메모 | oath/queue closing evidence 선생성 |

### 프로그래머 handoff 포인트
- `hook-manifest.md`에 semantic locator를 먼저 적고 diff를 시작한다.
- line number는 참고값이고, **surface 책임 경계가 계약**이다.

### UI/QA/PM handoff 포인트
- review 코멘트도 line보다 `battle root`, `stakes slot`, `stakes hierarchy` 이름으로 남긴다.
- `action panel도 미리 맞췄다`가 보이면 battle fail에 가깝다.

---

## 4. battle edit 경계

## 한 줄 목표
battle은 `stakes slot hook + stakes first-glance + required artifacts evidence`까지만 닫는다.

| 구분 | battle에서 해야 하는 것 | battle에서 하지 않는 것 |
| --- | --- | --- |
| markup | `data-run-surface="battle-table"`, `data-stakes-slot="current|next|grimoire"` review-friendly 노출 | atlas/oath data hook, action panel helper 재설계 |
| capture | `battle-1440-overview`, `battle-1440-stakes-focus` 2장 | dice tray / controls / log 중심 glamour shot |
| note | battle-only wording, next allowed step, non-change line | `Battle Table 거의 완료`, `oath도 가능`, 큰 총괄 진행 메모 |
| art sanity | `Stakes Plaque Starter` 충분/약함/과함 verdict | battle HUD 전체 리뉴얼 backlog |
| review prep | required artifacts 8종 채우기, candidate line 초안 | queue closing 선기록 |

---

## 5. role-specific handoff points

### 5-1. 프로그래머
- `renderBattle(summary)`와 연결된 stakes zone만 다시 찾고 닫는다.
- helper 대수술보다 `current / next / grimoire` slot vocabulary를 review 가능한 DOM/메모로 남기는 쪽이 우선이다.
- 첫 diff 전에 archive와 locator note를 먼저 만든다.

### 5-2. UI
- 이번 턴 캡처는 battle 두 장이면 충분하다.
- overview는 `current stakes가 먼저 읽히는가`, focus는 `current / next / grimoire 관계가 한 장에서 읽히는가`만 증명하면 된다.
- action panel, dice tray, log가 더 먼저 눈에 띄면 fail note를 남기고 green 문장을 미룬다.

### 5-3. 아트
- `Stakes Plaque Starter` sanity만 닫는다.
- battle을 위해 새 combat shell이나 oath/ending 공용 frame을 열지 않는다.
- starter note에는 `이번 stage에 아직 안 하는 것`도 같이 적는다.

### 5-4. QA
- `battle archive-ready candidate`는 required artifacts가 채워졌을 때만 쓴다.
- `bootstrap-ready`와 `archive-ready candidate`를 같은 review 문장으로 합치지 않는다.
- atlas hook 회귀나 oath 증거가 섞이면 battle green이 아니라 battle fail note를 남긴다.

### 5-5. PM/기획
- battle green은 `Run Table green`이 아니다.
- battle green 뒤에만 oath unlock을 쓴다.
- 기록 문장은 반드시 stage명 기준으로 남긴다.

---

## 6. 금지 패턴

## 3줄 요약
- battle kickoff의 실패는 느린 진행이 아니라 범위가 넓어지는 것이다.
- 특히 battle은 화면 정보량이 많아서 `이 김에 같이`가 가장 쉽게 붙는다.
- 아래 문장이 나오면 이미 stage drift 신호로 본다.

### 금지 문장 / 행동
- `이 김에 action panel도 같이 정리하자`
- `dice tray / log 밀도도 이번 battle pass에 포함`
- `oath 쪽 wording도 미리 맞춰 두자`
- `Battle Table 전반 polish`
- `Run Table 거의 완료`
- battle archive를 만들지 않고 임시 캡처 / 임시 메모만 남기는 것

### drift가 생겼을 때 되돌리는 질문
1. 이 수정이 `current / next / grimoire stakes first-glance`를 직접 닫는가?
2. 이 note가 battle stage archive 안에 남아도 `oath unlock only after battle green`을 약하게 만들지 않는가?
3. 이 캡처 / 메모 / hook 이름이 battle-only vocabulary를 유지하는가?

---

## 7. evidence drop rule

## 3줄 요약
- kickoff가 끝났다는 뜻은 code edit이 아니라 evidence bundle이 같은 archive에 모였다는 뜻이다.
- evidence는 candidate의 전제조건이지, 감상형 첨부물이 아니다.
- battle은 특히 capture와 note가 쉽게 generic해지므로 파일명과 문장 둘 다 stage명을 지켜야 한다.

### 반드시 같은 archive에 있어야 하는 것
- `manifest.md`
- `notes/hook-manifest.md`
- `checks/dom-sanity.md`
- `captures/battle-1440-overview.png`
- `captures/battle-1440-stakes-focus.png`
- `notes/density-review.md`
- `assets/starter-asset-list.md`
- `notes/handoff-line.md`

### 한 줄 기준
battle kickoff 완료는 **archive가 열리고, note 첫 줄이 battle-only로 채워지고, 첫 evidence 경로가 canonical naming으로 생긴 상태**를 말한다.

---

## 8. stop / go 판정

### GO 조건
- archive / stub / file route가 이미 생성돼 있다.
- programmer note가 `battle root / current-next-grimoire stakes hook / hierarchy`만 다룬다고 적는다.
- UI note가 `overview / stakes-focus` 두 질문만 답한다고 적는다.
- PM note가 `oath unlock only after battle green`을 그대로 유지한다.

### STOP 조건
- action panel / dice tray / controls / log를 이번 stage 안으로 가져오려는 메모가 나온다.
- `Battle Table` 전체 progress 문장이 먼저 적힌다.
- oath 관련 hook / capture / wording이 archive 안에 같이 생긴다.
- required artifacts 이름과 실제 파일명이 벌써 어긋난다.

### 한 줄 판정
**battle kickoff는 빠른 diff보다 빠른 boundary 잠금이 더 중요하다.**

---

## 9. 기록 문장

### kickoff 직후 남길 수 있는 문장
- `battle-stakes-plaque bootstrap-ready: archive/stub/locator freeze 완료, 아직 stakes-only edit 전.`
- `battle-stakes-plaque archive-in-progress: current/next/grimoire stakes hook과 overview/focus evidence 수집 중, oath는 아직 미오픈.`

### 아직 남기면 안 되는 문장
- `battle-stakes-plaque green`
- `oath-banner unlock`
- `Battle Table 거의 완료`
- `Run Table 진행 중`

### tracker / run log용 짧은 복붙 문장
- `battle-stakes-plaque kickoff는 stage archive -> manifest/stub -> stakes locator -> battle-only edit -> evidence drop 순서가 잠긴 뒤에만 시작한다.`
- `battle first live turn에서는 current/next/grimoire stakes bundle만 닫고 action panel/oath/queue closing은 열지 않는다.`

---

## immediate next actions
1. atlas green 뒤 battle turn을 열 때 먼저 이 kickoff packet으로 archive/stub/locator/freeze를 잠근다.
2. 그 다음 `battle stage packet -> battle required artifacts packet -> battle candidate review packet` 순서로 넘어간다.
3. 실전 evidence가 쌓이기 전에는 `oath unlock`이나 `Battle Table` 총괄 progress 문장을 쓰지 않는다.
4. 다음 battle 문서 확장은 새 방향서보다 `semantic anchor / file map / exemplar / scorecard`처럼 실제 live turn drift를 줄이는 쪽이 우선이다.

---

## 리스크 & 후속과제
- 가장 큰 리스크는 battle 문서가 충분한 상태에서 오히려 implementer와 reviewer가 `stakes plaque`를 `전투 전체 polish`로 다시 읽는 것이다.
- 특히 action panel / dice tray / log / controls는 시각적 존재감이 커서, battle evidence가 생기기 전에 scope를 같이 끌어들이기 쉽다.
- 따라서 다음 battle 실제 turn은 `archive 먼저`, `locator note 먼저`, `overview/focus 질문 먼저` 원칙을 반드시 지켜야 한다.
- 후속 문서 우선순위는 battle 첫 live turn이 실제로 열리기 전에 남아 있는 locator/file-routing/filled wording/owner-order 공백을 atlas와 같은 stage ladder로 메우는 쪽이 맞다.
