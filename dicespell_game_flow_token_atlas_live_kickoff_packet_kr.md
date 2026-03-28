# DiceSpell `Run Table` Atlas Route Node live kickoff packet

## 3줄 요약
- 이 문서는 `queue readiness board`, `atlas stage packet`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도, **실제 첫 atlas refinement turn을 열기 직전 마지막 실행 공백**을 닫기 위한 source-of-truth다.
- 핵심은 새 route node 설계를 더하는 것이 아니라, `좋아, atlas만 지금 live-ready라면 첫 10분 안에 어떤 archive를 만들고 어떤 hook/threshold/freeze를 먼저 잠가야 하지?`를 **한 장의 kickoff 순서**로 압축하는 것이다.
- 목표는 다음 implementer가 Run Table 문서를 다시 큐레이션하지 않고도, **atlas kickoff -> first atlas-only edit -> required artifacts evidence drop -> candidate review-ready**까지 같은 stage 언어로 시작하게 만드는 것이다.

**한 줄 목표:** `atlas-route-node` 첫 live turn을 `stage archive 생성 -> manifest/stub 초안 -> semantic anchor 재확인 -> atlas-only hook edit -> required artifacts evidence drop`의 5단계 kickoff로 고정해, 첫 Run Table refinement가 다시 `battle도 조금`, `oath도 조금` 같은 확장으로 부풀지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 Run Table token 축은 문서상으로는 충분하다.

이미 있는 것:
- 착수/대기 판단: `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- 실행 큐: `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- 첫 stage contract: `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- required artifacts: `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- candidate/green review: `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- 문서 위계: `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- 실제 archive 바닥: `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- 현재 미완료 gap: `docs/dicespell_game_flow_token_gap_ledger_kr.md`

그런데 실제 첫 atlas live turn 직전에는 아직 아래가 남는다.

1. `문서는 충분한데, 그래서 첫 10분에 무엇부터 해야 하지?`가 여러 packet에 흩어져 있다.
2. `atlas-route-node` stage boundary, `required artifacts`, `candidate-ready` 규칙이 각각은 닫혀 있어도, **한 번의 실전 kickoff 순서**로는 아직 압축돼 있지 않다.
3. 프로그래머는 첫 diff를 열기 전 `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold`를 어디까지 잠가야 하는지, UI/아트/QA/PM은 `언제부터 actual atlas closing이고 어디까지가 아직 대기/검수 턴인지`를 같은 문장으로 공유해야 한다.
4. 이 순서가 없으면 첫 Run Table turn에서도 다시 `battle stakes도 같이`, `oath banner도 미리`, `preview/log도 이참에` 같은 drift가 생긴다.

즉 이 문서는 새 atlas stage packet이 아니라, **atlas-route-node 첫 live execution을 여는 정확한 시작 순서 packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 10분 kickoff`, `3. semantic anchor`, `4. edit 경계` | `atlas stage packet`, `required artifacts`, `refinement queue` | 이번 턴은 atlas-only edit이며 battle/oath/queue closing을 같이 열면 안 된다 |
| UI | `1. 누가 지금 움직이는가`, `5-2. UI`, `6. 금지 패턴` | `atlas stage packet`, `closing packet`, `candidate review` | atlas는 board first-glance와 threshold 읽힘만 닫는 턴이지 전체 Run Table polish 턴이 아니다 |
| 아트 | `1. 누가 지금 움직이는가`, `5-3. 아트`, `6. 금지 패턴` | `token anchor packet`, `required artifacts`, `evidence manifest` | 이번 턴 자산 일은 `Route Node Starter` sanity 확인이지 새 공통 frame 제작이 아니다 |
| QA | `2. 10분 kickoff`, `5-4. QA`, `7. evidence drop`, `8. stop/go` | `required artifacts`, `candidate review`, `queue readiness board` | atlas evidence는 atlas stage archive에만 기록하고 battle/oath는 아직 선기록하지 않는다 |
| PM/기획 | `0. first screen 결론`, `5-5. PM`, `8. stop/go`, `9. 기록 문장` | `queue readiness board`, `refinement queue`, `candidate review` | kickoff 메모와 `atlas green` 문장은 다르며, battle unlock은 atlas evidence 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `atlas-route-node`를 실제로 열 때 첫 10분 안에 무엇부터 해야 하지?
- atlas stage packet / required artifacts / candidate review / queue readiness board는 어떤 순서로 겹쳐 봐야 하지?
- 누가 지금 edit를 하고, 누가 아직 대기 또는 경량 검수여야 하지?
- 언제까지가 kickoff이고, 어디서부터가 진짜 atlas route node edit인가?

### 가장 짧은 답
- **프로그래머 / UI / QA / PM이 atlas kickoff의 즉시 행동 오너**다.
- 아트는 atlas에서 **starter asset sanity 확인자**로 먼저 관여한다.
- 첫 편집 전에는 반드시 `stage archive + manifest 초안 + atlas stub 첫 줄 + semantic anchor + 금지 범위 선언`이 있어야 한다.
- 이 다섯 개가 없으면 아직 kickoff도 끝나지 않은 상태다.

### atlas에서 절대 하지 않는 것
- `battle-stakes-plaque` stage 착수
- `oath-banner` stage 착수
- queue closing 문장 선기록
- preview/log panel 대확장
- Route Atlas 전체 리디자인
- atlas required artifacts가 없는 상태에서 `battle unlock` 기록

---

## 1. 지금 누가 움직이고 누가 기다리는가

| 역할 | 지금 상태 | atlas에서 하는 일 | atlas에서 하지 않는 일 |
| --- | --- | --- | --- |
| 프로그래머 | 시작 가능 | archive/stub 생성, semantic anchor 재확인, atlas hook/threshold floor edit, hook manifest 정리 | battle/oath hook 선추가 |
| UI | 시작 가능 | overview/focus capture 예약, route node first-glance와 threshold 밀도 검수 | battle/oath visual pass, preview/log 전체 재배치 |
| 아트 | 경량 검수 가능 | `Route Node Starter` sanity note, token/accent drift 확인 | 새 공통 frame, 새 대형 배경 패키지 제작 |
| QA | 시작 가능 | atlas required artifacts 존재/정합성 점검, candidate-ready 조건 잠금 | battle/oath/queue closing evidence 선기록 |
| PM/기획 | 시작 가능 | stop/go 확인, manifest/handoff line 가드, `atlas green` 문장 관리 | `Run Table 진행 중` 같은 큰 문장 선기록 |

### 한 줄 판단 규칙
- atlas kickoff는 `프로그래머 + UI + QA + PM`의 atlas-only turn이다.
- 아트는 `지금 당장 많이 만드는 사람`이 아니라, **starter sanity와 drift를 먼저 막는 확인자**로 참여하는 것이 맞다.

---

## 2. atlas 첫 10분 kickoff 순서

## 3줄 요약
- 첫 10분은 code edit보다 먼저 **archive / 문장 / anchor / freeze**를 잠그는 시간이다.
- 이 순서를 생략하면 첫 Run Table 커밋이 다시 `atlas도 하고 battle도 살짝` 같은 감각 기반 커밋이 된다.
- 각 단계는 끝날 때 산출물이 있어야 하며, 말로만 `확인함`은 완료로 보지 않는다.

### T-10 ~ T-8: stage archive 생성
1. `docs/artifacts/run-table-token-delivery/atlas-route-node/` 생성 또는 starter bundle 복사
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
- 아직 `atlas green` 문장은 쓰지 않는다.

### T-8 ~ T-6: manifest + stub 첫 줄 채우기
1. `manifest.md`에 아래 4가지를 먼저 적는다.
   - `status: bootstrap-ready`
   - `owner summary: atlas route node만 연다`
   - `boundary / non-change: battle/oath/queue closing 미오픈`
   - `next allowed step: semantic anchor 확인 후 atlas-only edit`
2. stub 파일 첫 줄을 stage-specific 문장으로 채운다.
   - hook manifest: `이 메모는 atlas root/state/threshold hook만 기록하고 battle/oath hook은 아직 열지 않는다.`
   - dom sanity: `이 체크는 atlas route node stage만 검수한다.`
   - density review: `이번 review는 Run Atlas board first-glance와 threshold 읽힘만 본다.`
   - starter asset list: `이번 sanity는 Route Node Starter만 다루고 공통 frame 신규 제작은 포함하지 않는다.`
   - handoff line: `이 파일은 atlas candidate/green 문장 초안만 기록한다.`

**완료 기준**
- 빈 파일이 없다.
- `TODO`, `나중에 battle도`, `Run Table 전체`, `거의 다 됨` 같은 generic placeholder가 없다.

### T-6 ~ T-4: semantic anchor 재확인
1. 아래 semantic zone을 실제 코드/마크업에서 다시 찾는다.
   - atlas root surface
   - route node state 노출 지점
   - threshold 텍스트/표식 지점
2. `notes/hook-manifest.md`에 찾은 locator를 적는다.
   - 함수명 또는 render zone
   - 현재 atlas baseline
   - 아직 건드리면 안 되는 주변 책임
3. line drift가 있더라도 stage는 넓히지 않는다.

**완료 기준**
- `line이 조금 밀려도 같은 책임을 찾았다`는 메모가 남아 있다.
- `battle stakes slot도 같이 보자` 같은 stage 확장 메모가 없다.

### T-4 ~ T-2: packet + boundary freeze
1. `atlas route node stage packet` 기준으로 허용 범위를 다시 선언한다.
   - atlas root
   - route node state hook
   - threshold 읽힘
   - overview/focus capture
2. `required artifacts` / `candidate review` 기준으로 금지 범위를 적는다.
   - battle/oath 미오픈
   - queue closing 문장 금지
   - preview/log 대확장 금지
3. PM은 manifest의 `next allowed step`이 여전히 `atlas-route-node`인지 확인한다.

**완료 기준**
- manifest와 handoff note 둘 다 같은 금지 범위를 말한다.
- kickoff 메모와 `atlas green` handoff 문장이 섞이지 않는다.

### T-2 ~ T+10: atlas-only edit + 첫 evidence drop
1. atlas live markup 또는 동등 zone에 `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold` floor를 추가/정렬한다.
2. edit 후 즉시 아래 evidence를 채운다.
   - hook manifest
   - dom sanity
   - overview/focus capture
   - density review
   - starter asset list
   - handoff line 초안
3. evidence가 없으면 `atlas archive-ready candidate` 금지

**완료 기준**
- atlas evidence bundle 초안이 archive 안에 존재한다.
- 아직 battle/oath/queue closing 문장은 없다.

---

## 3. semantic anchor 재확인 규칙

| semantic zone | atlas에서 찾는 이유 | 찾은 뒤 기록해야 하는 것 | 찾았다고 해서 열면 안 되는 것 |
| --- | --- | --- | --- |
| atlas root surface | stage scope를 `Run Atlas`에만 고정하기 위해 | root locator와 atlas-only 메모 | battle/oath root 선추가 |
| route node state zone | current/past/shop/boss 읽힘을 review 가능한 hook 언어로 잠그기 위해 | state hook 이름과 검수 포인트 | stakes/oath state vocabulary 혼입 |
| threshold zone | next threshold가 board first-glance에서 읽히는지 고정하기 위해 | threshold hook 이름과 캡처 포인트 | preview/log 대확장 |
| atlas evidence path | required artifacts를 atlas-only로 잠그기 위해 | archive 경로와 파일명 메모 | battle/oath evidence 선생성 |

### 프로그래머 handoff 포인트
- `hook-manifest.md`에 semantic locator를 먼저 적고 diff를 시작한다.
- line number는 참고값이고, **surface 책임 경계가 계약**이다.

### UI/QA/PM handoff 포인트
- review 코멘트도 line보다 `atlas root`, `route node state`, `threshold zone` 이름으로 남긴다.
- `battle stakes도 미리 맞췄다`가 보이면 atlas fail에 가깝다.

---

## 4. atlas edit 경계

## 한 줄 목표
atlas는 `route node hook + threshold 읽힘 + required artifacts evidence`까지만 닫는다.

| 구분 | atlas에서 해야 하는 것 | atlas에서 하지 않는 것 |
| --- | --- | --- |
| 상태/마크업 | atlas root, route node state, threshold hook 정렬 | battle/oath hook 선추가 |
| 캡처 | overview 1장, threshold focus 1장 | battle/oath 캡처 선생성 |
| UX | route node와 next threshold가 먼저 읽히는지 확인 | preview/log, panel family 전체 개편 |
| 기록 | atlas candidate/green 후보 문장, atlas-only boundary note | `Run Table 진행`, `battle unlock 완료` 선기록 |

### 이 경계가 중요한 이유
- atlas는 첫 Run Table surface라서 가장 쉽게 `이왕이면 battle도`처럼 범위가 커진다.
- atlas에서 battle/oath까지 같이 열면 `stage 1개 = archive 1개` 규칙이 무너진다.
- 이번 packet의 목적은 더 많은 토큰을 넣는 것이 아니라, **첫 surface를 production handoff 단위로 닫는 것**이다.

---

## 5. 역할별 handoff

### 5-1. 프로그래머
- atlas의 핵심은 `route node state`와 `threshold` vocabulary를 review 가능한 hook으로 만드는 일이다.
- `queue readiness board`는 착수선을 말해 주지만, 실제 수정 계약은 `atlas route node stage packet`과 이 kickoff packet이 맡는다.
- `battle-stakes-plaque`, `oath-banner`, queue closing wording을 같이 만지기 시작하면 atlas 범위를 벗어난다.

### 5-2. UI
- 이번 턴의 UI 마감 범위는 `overview 1장 + threshold focus 1장 + density verdict 1개`다.
- atlas는 `route node > threshold > 보조 설명` 순서를 먼저 읽히게 해야 한다.
- Run Table 전체 비주얼 refresh는 이 턴의 성공 조건이 아니다.

### 5-3. 아트
- 이번 턴 산출물은 새 key art 패키지가 아니라, `Route Node Starter` sanity note다.
- atlas stage는 low-cost token sanity로 닫혀야 하며, `battle`이나 `oath`용 asset 요구가 같이 들어오면 범위를 넘는다.
- 신규 자산이 정말 필요하더라도 이 턴엔 `예외 필요 판정`까지만 남긴다.

### 5-4. QA
- 이번 턴의 pass/fail은 `required artifacts 8종 중 atlas bundle`, atlas-only boundary, candidate-ready 조건으로 본다.
- `battle stage도 곧 붙일 예정` 같은 미래 계획은 QA pass 근거가 아니다.
- evidence는 `atlas-only`라는 문장이 빠지면 안 된다.

### 5-5. PM/기획
- 지금 남길 수 있는 green 문장은 `atlas-route-node`뿐이다.
- `Run Table 진행`, `token refinement 착수`, `battle도 곧 가능` 같은 큰 문장은 금지한다.
- battle unlock은 atlas evidence bundle이 tracker/run log/portal에 반영된 뒤에만 쓴다.

---

## 6. 금지 패턴

1. `data-stakes-slot`, `data-oath-role`까지 한 번에 추가하는 것
2. battle/oath 캡처와 sanity note를 같이 생성하는 것
3. atlas stage를 빌미로 preview/log panel 전체를 재설계하는 것
4. `Route Node Starter` sanity를 새 공통 frame 제작 턴으로 키우는 것
5. atlas proof가 없는 상태에서 `battle unlock`을 기록하는 것
6. summary만 읽고 source packet 확인 없이 구현/리뷰를 시작하는 것

---

## 7. evidence drop 규칙

## 한 줄 목표
atlas 증거는 `atlas-route-node stage archive`로만 남긴다.

| 증거 | 최소 내용 | owner |
| --- | --- | --- |
| `manifest.md` | `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line` | PM/프로그래머 |
| `notes/hook-manifest.md` | atlas root/state/threshold locator와 non-change line | 프로그래머 |
| `checks/dom-sanity.md` | hook 존재/정합성 점검 | 프로그래머 + QA |
| `captures/atlas-1440-overview.png` | route node first-glance | UI |
| `captures/atlas-1440-threshold-focus.png` | threshold focus | UI |
| `notes/density-review.md` | `유지 / 약함 / 과함` verdict | UI + QA |
| `assets/starter-asset-list.md` | `Route Node Starter` sanity와 금지 자산 | 아트 |
| `notes/handoff-line.md` | atlas candidate/green 문장 초안 | QA + PM |

### 파일명 규칙
- generic `final.png`, `latest-note.md`, `done.txt` 금지
- stage/owner가 보이도록 아래 형식을 권장한다.
  - `captures/atlas-1440-overview.png`
  - `captures/atlas-1440-threshold-focus.png`
  - `notes/hook-manifest.md`
  - `checks/dom-sanity.md`
  - `notes/density-review.md`
  - `assets/starter-asset-list.md`

---

## 8. stop / go 판정

### GO 조건
- `manifest.md`와 stub가 stage-specific 문장으로 채워져 있다.
- semantic anchor note가 있다.
- atlas edit가 `atlas-route-node` 범위를 벗어나지 않았다.
- required artifacts evidence가 archive 안에 있다.

### STOP 신호
- battle/oath stage 파일이 같이 열렸다.
- `Run Table 전체` 같은 wording이 manifest/handoff note에 들어갔다.
- overview에서 route node보다 preview/log가 먼저 읽힌다.
- `atlas green` 기록이 evidence보다 먼저 나왔다.

### STOP 이후 다음 행동
1. stage를 넓히지 말고 atlas archive 안에서 boundary note를 먼저 갱신한다.
2. `무엇을 덜어내야 atlas로 돌아오는가`를 기록한다.
3. 필요하면 이번 턴은 `atlas-route-node skipped(partial)`로 남기고, battle은 열지 않는다.

---

## 9. tracker / run log 기록 문장

### kickoff 문장
> atlas-route-node kickoff: archive/stub/semantic anchor/preflight를 먼저 잠근 뒤 atlas-only route node edit에 착수한다.

### candidate 문장
> 이번 턴은 `atlas-route-node` stage archive에 root/state/threshold hook, overview/focus capture, starter sanity, atlas-only boundary note를 모아 `archive-ready candidate`까지 준비했고, battle/oath/queue closing은 아직 열지 않았다.

### green 문장
> atlas-route-node green: atlas required artifacts와 first-glance verdict이 정렬돼 battle-stakes-plaque unlock만 연다.

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| atlas가 다시 `Run Table 전체 착수`처럼 부풀어 battle/oath까지 섞이는 위험 | stage별 refinement discipline 붕괴 | atlas-only kickoff 순서와 금지 범위를 packet으로 고정 | atlas 첫 live edit 직전 |
| atlas hook보다 preview/log 개편이 먼저 열리는 위험 | route node stage가 evidence-friendly stage가 아니라 큰 UI patch가 됨 | route node/threshold first-glance를 closing 범위로 고정 | atlas UI review 시 |
| PM이 candidate 전에 battle unlock을 기록하는 위험 | half-cutover가 완료처럼 보임 | kickoff/candidate/green 문장을 분리 | tracker/run log 작성 시 |
| 아트가 starter sanity를 대형 자산 요구로 키우는 위험 | 작은 stage가 asset blocker처럼 보임 | 아트 기본 역할을 starter sanity로 한정 | atlas art sanity review 시 |
| QA가 `문서가 충분함`과 `candidate-ready`를 같은 뜻으로 쓰는 위험 | archive evidence discipline이 약해짐 | required artifacts evidence 존재를 candidate 조건으로 못 박음 | atlas candidate review 시 |

---

## 11. 즉시 다음 액션
1. 다음 Run Table 작업자는 `source-of-truth map -> gap ledger -> archive bootstrap -> refinement queue -> atlas route node stage packet -> 이 live kickoff packet` 순서로 읽고 시작한다.
2. `docs/artifacts/run-table-token-delivery/atlas-route-node/` archive부터 만든다.
3. manifest와 atlas stub 첫 줄을 채운다.
4. semantic anchor를 다시 찾는다.
5. 그 뒤에만 atlas-only edit와 required artifacts evidence 수집을 시작한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
