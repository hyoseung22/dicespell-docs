# DiceSpell `Run Table` Oath Banner live kickoff packet

## 3줄 요약
- 이 문서는 `oath stage packet`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도, **battle green 뒤 실제 oath 첫 10분을 어떤 순서로 열어야 하는지** 남아 있던 마지막 실행 공백을 닫는 source-of-truth다.
- 핵심은 새 ending 화면을 더 설계하는 것이 아니라, `좋아, 이제 oath가 unlock됐다면 첫 10분 안에 어떤 archive를 만들고 어떤 banner / result / carry hook / hierarchy freeze / evidence 예약을 먼저 잠가야 하지?`를 **한 장의 kickoff 순서**로 압축하는 것이다.
- 목표는 다음 implementer / UI / 아트 / QA / PM이 oath 턴을 다시 `ending 전체 polish`로 부풀리지 않고, **oath kickoff -> banner-only edit -> required artifacts evidence drop -> candidate review-ready**까지 같은 stage 언어로 시작하게 만드는 것이다.

**한 줄 목표:** `oath-banner` 첫 live turn을 `stage archive 생성 -> manifest/stub 초안 -> oath semantic locator 재확인 -> oath-only hook edit -> required artifacts evidence drop` 5단계 kickoff로 고정해, battle 다음 refinement가 다시 `ending helper도 같이`, `reward/shop side family도 겸사`, `queue closing도 미리` 같은 확장으로 번지지 않게 만든다.

---

## 왜 이 문서가 필요한가
지금 oath 마지막 stage 문서 세트는 거의 충분하다.

이미 있는 것:
- 실행 큐 / unlock 순서: `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- stage readiness / unlock discipline: `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- oath 범위 계약: `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- required artifacts 8종: `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- candidate -> green review 절차: `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- queue closing 경계: `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`
- archive starter rule: `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- 문서 위계: `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- 현재 미완료 gap: `docs/dicespell_game_flow_token_gap_ledger_kr.md`

그런데 실제 oath live turn 직전에는 아직 아래 공백이 남아 있었다.

1. `문서는 충분한데, 그래서 battle green 뒤 oath 첫 10분에 무엇부터 해야 하지?`가 여러 packet에 흩어져 있다.
2. `oath-banner` stage boundary, `required artifacts`, `candidate review`는 각각 닫혀 있어도, **실전 kickoff 순서**로는 아직 압축돼 있지 않다.
3. 프로그래머는 `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta`를 어디까지 잠가야 하는지, UI/아트/QA/PM은 `언제부터 actual oath closing이고 어디까지가 아직 kickoff 메모인지`를 같은 문장으로 공유해야 한다.
4. 이 순서가 없으면 oath 첫 턴에서도 다시 `ending helper도 같이`, `reward/shop side family도 같이`, `queue closing wording도 미리` 같은 drift가 생긴다.

즉 이 문서는 새 oath stage packet이 아니라, **oath-banner 첫 live execution을 여는 정확한 시작 순서 packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. 10분 kickoff`, `3. semantic locator`, `4. edit 경계` | `oath stage packet`, `oath required artifacts`, `oath candidate review` | 이번 턴은 oath-only edit이며 ending helper 전체 재구성, reward/shop family 정리, queue closing 선기록은 같이 열면 안 된다 |
| UI | `1. 누가 지금 움직이는가`, `5-2. UI`, `6. 금지 패턴` | `oath stage packet`, `token closing packet`, `oath candidate review` | oath는 `banner > result > carry` first-glance를 닫는 턴이지 ending 전체 polish 턴이 아니다 |
| 아트 | `1. 누가 지금 움직이는가`, `5-3. 아트`, `6. 금지 패턴` | `token anchor packet`, `oath required artifacts` | 이번 턴 자산 일은 `Oath Banner Starter` sanity 확인이지 ending 배경/공용 shell 전체 리뉴얼이 아니다 |
| QA | `2. 10분 kickoff`, `5-4. QA`, `7. evidence drop`, `8. stop/go` | `oath required artifacts`, `oath candidate review`, `queue readiness board` | oath evidence는 oath stage archive에만 기록하고 queue closing은 아직 선기록하지 않는다 |
| PM/기획 | `0. first screen 결론`, `5-5. PM`, `8. stop/go`, `9. 기록 문장` | `queue readiness board`, `refinement queue`, `oath candidate review` | kickoff 메모와 `oath green` 문장은 다르며, queue closing review-only unlock은 oath candidate/green 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `oath-banner`를 실제로 열 때 첫 10분 안에 무엇부터 해야 하지?
- oath stage packet / required artifacts / candidate review / queue closing packet은 어떤 순서로 겹쳐 봐야 하지?
- 누가 지금 edit를 하고, 누가 아직 대기 또는 경량 검수여야 하지?
- 언제까지가 kickoff이고, 어디서부터가 진짜 banner-only edit인가?

### 가장 짧은 답
- **프로그래머 / UI / QA / PM이 oath kickoff의 즉시 행동 오너**다.
- 아트는 oath에서 **starter asset sanity 확인자**로 먼저 관여한다.
- 첫 편집 전에는 반드시 `stage archive + manifest 초안 + oath stub 첫 줄 + banner semantic locator + 금지 범위 선언`이 있어야 한다.
- 이 다섯 개가 없으면 아직 kickoff도 끝나지 않은 상태다.

### oath에서 절대 하지 않는 것
- `battle-stakes-plaque` 재개방
- `queue closing` stage 착수
- `Run Table token complete` 문장 선기록
- ending helper / reward-shop side family 대확장
- `Overrun Oath` 전체 리디자인
- oath required artifacts가 없는 상태에서 `queue closing review-only unlock` 기록

---

## 1. 지금 누가 움직이고 누가 기다리는가

| 역할 | 지금 상태 | oath에서 하는 일 | oath에서 하지 않는 일 |
| --- | --- | --- | --- |
| 프로그래머 | 시작 가능 | archive/stub 생성, banner semantic locator 재확인, oath hook/role floor edit, hook manifest 정리 | battle hook 재수정, ending helper 구조 재설계, queue closing hook 선추가 |
| UI | 시작 가능 | overview/focus capture 예약, banner/result/carry first-glance와 oath hierarchy 밀도 검수 | ending 전체 캡처 패스, reward/shop side family 비교 패스 |
| 아트 | 경량 검수 가능 | `Oath Banner Starter` sanity note, banner accent drift 확인 | 새 ending 배경 패키지, 공용 side shell 제작 |
| QA | 시작 가능 | oath required artifacts 존재/정합성 점검, candidate-ready 조건 잠금 | queue closing evidence 선기록, battle 재검수 확대 |
| PM/기획 | 시작 가능 | stop/go 확인, manifest/handoff line 가드, `oath green` 문장 관리 | `Run Table 진행 중`, `ending 거의 완료` 같은 큰 문장 선기록 |

### 한 줄 판단 규칙
- oath kickoff는 `프로그래머 + UI + QA + PM`의 oath-only turn이다.
- 아트는 `지금 당장 많이 만드는 사람`이 아니라, **starter sanity와 drift를 먼저 막는 확인자**로 참여하는 것이 맞다.

---

## 2. oath 첫 10분 kickoff 순서

## 3줄 요약
- 첫 10분은 code edit보다 먼저 **archive / 문장 / locator / freeze**를 잠그는 시간이다.
- 이 순서를 생략하면 oath 첫 커밋이 다시 `banner도 하고 ending helper도`, `queue closing 문구도 미리` 같은 감각 기반 커밋이 된다.
- 각 단계는 끝날 때 실제 산출물이 있어야 하며, 말로만 `확인함`은 완료로 보지 않는다.

### T-10 ~ T-8: stage archive 생성
1. `docs/artifacts/run-table-token-delivery/oath-banner/` 생성 또는 starter bundle 복사
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
- 아직 `oath green` 문장은 쓰지 않는다.

### T-8 ~ T-6: manifest + stub 첫 줄 채우기
1. `manifest.md`에 아래 4가지를 먼저 적는다.
   - `status: bootstrap-ready`
   - `owner summary: oath banner만 연다`
   - `boundary / non-change: battle 재개방 / queue closing / ending helper 대확장 / reward-shop side family 재정리 미오픈`
   - `next allowed step: banner semantic locator 확인 후 oath-only edit`
2. stub 파일 첫 줄을 stage-specific 문장으로 채운다.
   - hook manifest: `이 메모는 oath root/banner/result/carry/CTA hook만 기록하고 queue closing hook은 아직 열지 않는다.`
   - dom sanity: `이 체크는 oath banner stage만 검수한다.`
   - density review: `이번 review는 Overrun Oath banner-first 읽힘과 banner/result/carry hierarchy만 본다.`
   - starter asset list: `이번 sanity는 Oath Banner Starter만 다루고 ending shell 전체 리뉴얼은 포함하지 않는다.`
   - handoff line: `이 파일은 oath candidate/green 문장 초안만 기록한다.`

**완료 기준**
- 빈 파일이 없다.
- `TODO`, `나중에 queue closing도`, `ending 전체`, `거의 다 됨` 같은 generic placeholder가 없다.

### T-6 ~ T-4: banner semantic locator 재확인
1. 아래 semantic zone을 실제 코드/마크업에서 다시 찾는다.
   - oath root surface
   - banner / result / carry role 노출 지점
   - CTA context / carry action 지점
2. `notes/hook-manifest.md`에 찾은 locator를 적는다.
   - 함수명 또는 render zone
   - 현재 oath baseline
   - 아직 건드리면 안 되는 주변 책임
3. line drift가 있더라도 stage는 넓히지 않는다.

**완료 기준**
- `line이 조금 밀려도 같은 책임을 찾았다`는 메모가 남아 있다.
- `ending helper도 같이 보자`, `queue closing 문장도 미리 보자` 같은 확장 메모가 없다.

### T-4 ~ T-2: packet + boundary freeze
1. `oath banner stage packet` 기준으로 허용 범위를 다시 선언한다.
   - oath root
   - banner / result / carry role hook
   - banner-first hierarchy
   - overview/focus capture
2. `required artifacts` / `candidate review` / `queue closing packet` 기준으로 금지 범위를 적는다.
   - battle 재개방 금지
   - queue closing 문장 금지
   - ending helper / reward-shop side family 대확장 금지
3. PM은 manifest의 `next allowed step`이 여전히 `oath-banner`인지 확인한다.

**완료 기준**
- manifest와 handoff note 둘 다 같은 금지 범위를 말한다.
- kickoff 메모와 `oath green` handoff 문장이 섞이지 않는다.

### T-2 ~ T+10: oath-only edit + 첫 evidence drop
1. oath live markup 또는 동등 zone에 `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta` floor를 추가/정렬한다.
2. edit 후 즉시 아래 evidence를 채운다.
   - hook manifest
   - dom sanity
   - overview/focus capture
   - density review
   - starter asset list
   - handoff line 초안
3. evidence가 없으면 `oath archive-ready candidate` 금지

**완료 기준**
- oath evidence bundle 초안이 archive 안에 존재한다.
- 아직 queue closing 문장은 없다.

---

## 3. oath semantic locator 재확인 규칙

| semantic zone | oath에서 찾는 이유 | 찾은 뒤 기록해야 하는 것 | 찾았다고 해서 열면 안 되는 것 |
| --- | --- | --- | --- |
| oath root surface | stage scope를 `Overrun Oath` 안에서도 banner family에만 고정하기 위해 | root locator와 oath-only 메모 | queue closing root 선추가 |
| role zone | banner/result/carry 읽힘을 review 가능한 hook 언어로 잠그기 위해 | role hook 이름과 검수 포인트 | reward/shop side vocabulary 혼입 |
| CTA context zone | carry action이 oath 장면 안에서 읽히는지 고정하기 위해 | CTA hook와 carry context note | ending helper 대수술 |
| oath evidence path | required artifacts를 oath-only로 잠그기 위해 | archive 경로와 파일명 메모 | queue closing evidence 선생성 |

### 프로그래머 handoff 포인트
- `hook-manifest.md`에 semantic locator를 먼저 적고 diff를 시작한다.
- line number는 참고값이고, **surface 책임 경계가 계약**이다.

### UI/QA/PM handoff 포인트
- review 코멘트도 line보다 `oath root`, `banner role`, `carry CTA context` 이름으로 남긴다.
- `ending helper도 미리 맞췄다`가 보이면 oath fail에 가깝다.

---

## 4. oath edit 경계

## 한 줄 목표
oath는 `banner / result / carry role hook + banner-first hierarchy + required artifacts evidence`까지만 닫는다.

| 구분 | oath에서 해야 하는 것 | oath에서 하지 않는 것 |
| --- | --- | --- |
| markup | `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta` review-friendly 노출 | queue closing hook, ending helper 재설계, reward/shop side family 정리 |
| capture | `oath-1440-overview`, `oath-1440-banner-focus` 2장 | reward/shop strip, helper copy, 전체 ending glamour shot |
| note | oath-only wording, next allowed step, non-change line | `Overrun Oath 거의 완료`, `Run Table 완료`, 큰 총괄 진행 메모 |
| art sanity | `Oath Banner Starter` 충분/약함/과함 verdict | ending shell 전체 리뉴얼 backlog |
| review prep | required artifacts 8종 채우기, candidate line 초안 | queue closing 선기록 |

---

## 5. role-specific handoff points

### 5-1. 프로그래머
- `renderEnding(summary)`와 연결된 oath zone만 다시 찾고 닫는다.
- helper 대수술보다 `banner / result / carry` role vocabulary를 review 가능한 DOM/메모로 남기는 쪽이 우선이다.
- 첫 diff 전에 archive와 locator note를 먼저 만든다.

### 5-2. UI
- 이번 턴 캡처는 oath 두 장이면 충분하다.
- overview는 `main banner가 먼저 읽히는가`, focus는 `banner / result / carry 관계가 한 장에서 읽히는가`만 증명하면 된다.
- side card나 CTA가 더 먼저 눈에 띄면 fail note를 남기고 green 문장을 미룬다.

### 5-3. 아트
- `Oath Banner Starter` sanity만 닫는다.
- ending을 위해 새 배경, 공용 side shell, reward/shop 공용 frame을 열지 않는다.
- starter note에는 `이번 stage에 아직 안 하는 것`도 같이 적는다.

### 5-4. QA
- `oath archive-ready candidate`는 required artifacts가 채워졌을 때만 쓴다.
- `bootstrap-ready`와 `archive-ready candidate`를 같은 review 문장으로 합치지 않는다.
- battle 회귀나 queue closing 증거가 섞이면 oath green이 아니라 oath fail note를 남긴다.

### 5-5. PM/기획
- oath green은 `Run Table green`이 아니다.
- oath green 뒤에만 queue closing review-only unlock을 쓴다.
- 기록 문장은 반드시 stage명 기준으로 남긴다.

---

## 6. 금지 패턴

## 3줄 요약
- oath kickoff의 실패는 느린 진행이 아니라 범위가 넓어지는 것이다.
- 특히 oath는 ending 경계와 맞닿아 있어서 `이 김에 같이`가 가장 쉽게 붙는다.
- 아래 문장이 나오면 이미 stage drift 신호로 본다.

### 금지 문장 / 행동
- `이 김에 ending helper도 같이 정리하자`
- `reward/shop side family도 이번 pass에`
- `queue closing 문구도 미리 맞춰 두자`
- `Overrun Oath 전반 polish`
- `Run Table 거의 완료`
- oath archive를 만들지 않고 임시 캡처 / 임시 메모만 남기는 것

### drift가 생겼을 때 되돌리는 질문
1. 이 수정이 `banner / result / carry first-glance`를 직접 닫는가?
2. 이 note가 oath stage archive 안에 남아도 `queue closing only after oath green`을 약하게 만들지 않는가?
3. 이 캡처 / 메모 / hook 이름이 oath-only vocabulary를 유지하는가?

---

## 7. evidence drop rule

## 3줄 요약
- live kickoff의 목적은 끝내는 것이 아니라 **candidate review가 가능한 bundle로 진입하는 것**이다.
- 그러므로 첫 evidence drop도 `대충 캡처 한 장`이 아니라 required artifacts와 같은 이름/같은 경로를 먼저 맞춰야 한다.
- evidence가 stage archive 밖에 흩어지면 아직 kickoff-only 상태다.

### 한 줄 목표
oath 첫 evidence drop부터 required artifacts 경로와 이름을 맞춘다.

### 첫 evidence drop 체크리스트
- `manifest.md`에 `bootstrap-ready` 또는 `archive-in-progress` 상태가 honest하게 적혀 있다.
- `notes/hook-manifest.md`와 `checks/dom-sanity.md`가 같은 hook vocabulary를 쓴다.
- `captures/oath-1440-overview.png`, `captures/oath-1440-banner-focus.png` 이름이 canonical naming을 따른다.
- `notes/density-review.md`가 overview/focus를 직접 참조한다.
- `assets/starter-asset-list.md`가 `Oath Banner Starter` verdict와 비범위를 함께 적는다.
- `notes/handoff-line.md`에는 candidate/green 중 아직 해당되는 문장만 초안으로 남긴다.

### 아직 쓰면 안 되는 것
- `oath-banner green`
- `Run Table token queue closing review-only unlock`
- `ending 전반적으로 거의 완료`

---

## 8. stop / go rule

## 3줄 요약
- kickoff가 끝났다고 바로 candidate가 되는 것은 아니다.
- stop/go 기준은 `artifact가 모였는가`가 아니라 `문장 정렬 + 범위 준수 + stage archive 존재`다.
- QA/PM은 이 시점부터만 다음 packet으로 넘어갈 수 있다.

### go 조건
- archive와 starter files가 실제로 존재한다.
- manifest와 stub 첫 줄이 oath-only language를 쓴다.
- semantic locator note가 있다.
- non-change / 금지 범위 선언이 있다.
- 첫 evidence drop이 canonical path에 들어갔다.

### stop 조건
- queue closing이나 ending helper 확장 문장이 하나라도 먼저 들어갔다.
- overview/focus 캡처가 oath stage naming을 쓰지 않는다.
- starter asset note가 ending 전체 리뉴얼 backlog처럼 읽힌다.
- battle hook 회귀 메모가 섞였다.

### 한 줄 판단 규칙
`kickoff-ready`는 `go-to-edit`일 뿐이고, `candidate-ready`는 required artifacts와 review packet까지 다시 통과해야만 열린다.

---

## 9. 기록 문장

### kickoff 완료 직후 허용 문장
- `oath-banner kickoff: stage archive, manifest/stub, semantic locator, oath-only boundary를 먼저 잠갔다.`
- `oath-banner live turn은 banner / result / carry first-glance evidence drop까지만 열었고 queue closing은 아직 잠겨 있다.`

### 아직 금지 문장
- `Overrun Oath 거의 완료`
- `ending 사실상 green`
- `queue closing도 같이 진행 가능`
- `Run Table token 완료`

### candidate / green은 어디서 쓰나
- candidate 문장은 `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md` 기준 QA 단계에서만 쓴다.
- green 문장은 PM이 마지막에 `oath-banner green`을 기록할 때만 쓴다.

---

## immediate next actions
1. oath를 열 때는 이 문서로 먼저 archive/stub/locator/freeze를 잠근다.
2. 그다음 `oath stage packet -> required artifacts -> candidate review` 순서로 넘어간다.
3. 실제 evidence가 생기기 전에는 `queue closing review-only unlock`이나 총괄 progress 문장을 쓰지 않는다.

---

## 리스크 & 후속과제
- 가장 큰 리스크는 oath 문서가 충분해졌다는 이유로 banner stage를 다시 `ending 전체 polish`로 읽는 것이다.
- 다음 oath 실제 turn은 `archive 먼저`, `locator note 먼저`, `overview/focus 질문 먼저`를 지켜야 한다.
- 후속 문서는 oath도 battle처럼 semantic anchor / archive file map / filled archive examples / handoff scorecard ladder를 끝까지 갖추는 쪽이 우선이다.
