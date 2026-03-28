# DiceSpell 첫 런 온보딩 `B-1 library` archive bootstrap packet

## 3줄 요약
- 이 문서는 `B-1 library packet`, `live kickoff`, `artifact stub`, `manifest`, `semantic anchor`, `required artifacts`, `review packet`까지로도 남아 있던 마지막 archive-start 공백인 **`좋아, B-1 archive를 실제로 열자마자 어떤 폴더와 어떤 starter bundle을 먼저 만들어야 하지?`**를 닫는 source-of-truth다.
- 핵심은 새 primer를 더 설계하는 것이 아니라, `docs/artifacts/YYYYMMDD/b1-library/` 아래에 **무엇을 먼저 만들고**, **무엇은 placeholder여도 되고**, **무엇이 비어 있으면 아직 review-ready를 말하면 안 되는지**를 B-1 stage 언어로 고정하는 것이다.
- 목표는 implementer/UI/아트/QA/PM이 B-1 첫 live turn에서 archive를 결과 정리 장소가 아니라 **library-only evidence를 시작하는 작업 바닥**으로 쓰게 만드는 것이다.

**한 줄 목표:** `B-1 library` 첫 archive를 `폴더 구조 + starter file set + bootstrap verdict`까지 고정해, evidence가 임시 위치로 새거나 manifest만 있고 실제 stage bundle이 비는 위험을 줄인다.

---

## 왜 이 문서가 필요한가
지금 B-1 library 축은 거의 충분하다.

이미 있는 것:
- stage 범위: `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- 첫 10분 순서: `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- starter note 첫 줄: `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- starter manifest 규칙: `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- semantic locator: `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- exact 제출물: `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- green/signoff: `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- 공통 archive 규칙: `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
- 공통 bootstrap 규칙: `docs/dicespell_overrun_onboarding_archive_bootstrap_packet_kr.md`

그런데 실제 B-1 첫 live turn 직전에는 여전히 아래 공백이 남아 있었다.

1. 공통 archive/bootstrap packet은 강하지만, **B-1 library stage에서 어떤 starter bundle을 먼저 만들고 무엇을 아직 비워 둬도 되는지**는 B-1 언어로 다시 압축돼 있지 않았다.
2. `required artifacts 8종`은 review-ready 기준이지, 실제 첫 3분 안에 **무슨 파일명으로 무엇을 먼저 예약할지**까지는 설명하지 않는다.
3. 이 공백이 남아 있으면 archive를 만들었다고 해도 `manifest만 있고 captures/logs/notes는 아직 없음` 같은 반쪽 상태가 길어지고, implementer와 reviewer가 다시 `이제 edit 들어가도 되나?`, `이건 bootstrap-ready인가 archive-in-progress인가?`를 수동으로 합의해야 한다.
4. starter stub와 semantic anchor가 stage-specific여도 archive root가 느슨하면 첫 capture나 note가 다시 임시 이름(`final.png`, `latest-note`)이나 임시 위치로 새기 쉽다.

즉 이 문서는 새 방향 문서가 아니라, **B-1 live kickoff 직후 archive를 실제로 어떻게 열고 stage evidence 바닥을 어떻게 먼저 고정할지**를 잠그는 마지막 bootstrap packet이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. starter bundle`, `3. bootstrap 순서` | `B-1 live kickoff`, `manifest packet`, `semantic anchor packet` | 코드 edit 전에 `manifest + logs + captures + notes`와 B-1 starter 파일명을 먼저 고정한다 |
| UI | `2. starter bundle`, `4. capture bootstrap` | `screen packet`, `B-1 review packet` | first-open / return-state capture는 B-1 archive 안에 분리 저장하고 generic screenshot 이름을 쓰지 않는다 |
| 아트 | `2. starter bundle`, `5. note bootstrap` | `artifact stub packet`, `visual asset packet` | `art token sanity` note를 capture와 분리해 같은 archive 안에서 시작한다 |
| QA | `3. bootstrap 순서`, `6. bootstrap verdict` | `required artifacts packet`, `review packet` | starter bundle이 없으면 acceptance 수집을 시작하지 않고, review-ready도 쓰지 않는다 |
| PM/기획 | `0. first screen 결론`, `6. bootstrap verdict`, `7. stop/go` | `readiness board`, `execution reporting packet` | B-1 archive는 `bootstrap-ready -> archive-in-progress -> review-ready` 순서로만 올라가고, B-2 unlock은 아직 금지다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- B-1 library 첫 live turn에서 archive를 실제로 어떤 경로와 어떤 starter file set으로 열지?
- manifest는 있는데 capture/log/note가 아직 없는 상태를 어떻게 판정하지?
- 언제까지가 bootstrap-ready고, 어디서부터 archive-in-progress나 review-ready라고 말할 수 있지?

### 가장 짧은 답
- B-1 archive는 `docs/artifacts/YYYYMMDD/b1-library/`를 기준으로 연다.
- 첫 3분 안에 최소 `manifest.md`, `logs/`, `captures/`, `notes/`를 만들고, B-1 starter file 5종의 파일명 또는 초안을 같이 고정한다.
- `first-open`, `return-state`, `acceptance log`, `guidance state diff`, `ui hierarchy`, `art token sanity`, `boundary note`가 아직 하나도 없으면 archive는 `bootstrap-ready` 또는 `archive-in-progress`일 뿐 `review-ready`가 아니다.
- B-1 archive는 끝까지 `library-only`를 유지해야 하며 battle/reward/shop/ending evidence를 섞지 않는다.

### 한 줄 판정 규칙
B-1 archive는 **생겼다**와 **실제로 시작됐다**가 다르다. starter bundle이 있어야 live turn이 열린다.

---

## 1. core structure

## 3줄 요약
- B-1 bootstrap은 `경로 고정 -> starter file set 생성 -> 첫 placeholder/초안 입력 -> semantic anchor/evidence drop 준비` 순서로 진행한다.
- 이 문서의 역할은 `required artifacts 8종`을 다시 정의하는 것이 아니라, 그 8종이 archive 안에서 **어떤 첫 파일/첫 줄/첫 캡처 예약**으로 시작해야 하는지 고정하는 것이다.
- 핵심은 많은 파일이 아니라 **올바른 이름과 올바른 stage boundary를 가진 첫 파일**이다.

### one-line goal
B-1 archive를 `review-ready 전의 진짜 작업 바닥`으로 만든다.

---

## 2. B-1 starter bundle

| 구분 | canonical path | starter 상태에서 반드시 있어야 하는 것 | 아직 비어 있어도 되는 것 | 비어 있으면 안 되는 이유 |
| --- | --- | --- | --- | --- |
| root | `docs/artifacts/YYYYMMDD/b1-library/` | stage 폴더 자체 | 없음 | 경로가 없으면 다른 evidence도 stage 언어를 잃는다 |
| manifest | `manifest.md` | `status`, `owner summary`, `boundary`, `next allowed step`, `handoff line` 초안 | 세부 verdict 문장 | manifest가 없으면 bootstrap 상태조차 판정 불가 |
| logs | `logs/YYYYMMDD-b1-qa-acceptance-log.md` | 파일 존재 + B1-A1~A5 / recovery 3종 예약 문장 | 최종 pass/fail verdict | QA가 무엇을 수집할지 경로를 잃는다 |
| captures | `captures/YYYYMMDD-b1-library-first-open.png`, `captures/YYYYMMDD-b1-library-return-state.png` | 파일명 예약 또는 실제 캡처 1장 이상 | 두 번째 캡처의 최종 버전 | first-open/return-state를 다른 surface와 섞기 쉽다 |
| notes | `notes/YYYYMMDD-b1-programmer-guidance-state.md`, `notes/YYYYMMDD-b1-ui-hierarchy-note.md`, `notes/YYYYMMDD-b1-art-token-sanity.md`, `notes/YYYYMMDD-b1-pm-boundary-note.md` | 파일 존재 + starter sentence 1~3줄 | 최종 review wording | surface boundary와 locator contract가 캡처만 남고 사라진다 |

### 한 줄 결론
B-1 archive starter bundle은 **manifest 1 + log 1 + capture 2 + note 4**의 경로와 첫 문장만 먼저 살아 있으면 된다.

---

## 3. bootstrap 순서

## 3줄 요약
- B-1 live turn은 `archive 생성 -> starter file set -> manifest 초안 -> note/log starter sentence -> capture 예약 -> 그 뒤 edit` 순서가 맞다.
- starter bundle을 만든 뒤에만 semantic anchor 재확인과 live edit에 들어간다.
- 코드를 먼저 열고 archive를 나중에 정리하는 흐름은 이 stage에서 금지다.

### one-line goal
B-1 edit보다 archive starter bundle을 먼저 고정한다.

| 순서 | 해야 할 일 | 완료 기준 | 금지 |
| --- | --- | --- | --- |
| 1 | 날짜 루트 선택 | `docs/artifacts/YYYYMMDD/` 결정 | Desktop/Downloads 임시 폴더 사용 |
| 2 | stage 폴더 생성 | `b1-library/` 생성 | `library`, `onboarding1`, `tmp` 같은 임의 이름 |
| 3 | 하위 폴더 생성 | `logs/`, `captures/`, `notes/` 생성 | `misc/`, `images/` 등 임의 폴더 |
| 4 | manifest 초안 생성 | B-1 manifest 5필드가 starter sentence로 채워짐 | evidence 다 모인 뒤에 manifest 작성 |
| 5 | log/note starter file 생성 | acceptance log 1개 + note 4개 존재 | note 없이 캡처만 먼저 쌓기 |
| 6 | capture 파일명 예약 또는 첫 캡처 저장 | `first-open`, `return-state` 이름이 archive 안에 고정됨 | `final.png`, `latest.png` 등 generic naming |
| 7 | semantic anchor 재확인 뒤 live edit 시작 | archive가 최소 `bootstrap-ready` 상태 | 코드 수정 후 archive 정리 |

### bootstrap 완료 최소선
아래 6개가 모두 있으면 `bootstrap-ready`로 본다.
1. `b1-library/` 폴더
2. `manifest.md`
3. `logs/acceptance log`
4. `notes/` starter note 4종
5. `captures/` 안의 file name reservation 또는 캡처 1장 이상
6. manifest의 `next allowed step`이 아직 `review-ready 대기` 수준으로 작은 문장

---

## 4. capture bootstrap rule

## 3줄 요약
- B-1 capture는 `예쁜 화면 수집`이 아니라 `first-open`과 `return-state`라는 두 질문을 archive 안에 미리 예약하는 작업이다.
- capture 파일명은 stage boundary를 말해야 한다.
- `library-first-open`과 `library-return-state`를 starter 단계부터 분리해 두면 B-2 이후 surface와 혼입될 가능성이 크게 줄어든다.

### one-line goal
capture 이름부터 B-1 semantic question을 말하게 만든다.

| capture | starter 상태에서 꼭 있어야 하는 것 | 나중에 채워질 것 | 금지 |
| --- | --- | --- | --- |
| `YYYYMMDD-b1-library-first-open.png` | 파일명 또는 실제 첫 캡처 | hero-primer + highlight + CTA 읽힘 verdict | `primer.png`, `library.png`, `final.png` |
| `YYYYMMDD-b1-library-return-state.png` | 파일명 또는 실제 두 번째 캡처 | 재노출 제어 + boundary 유지 verdict | `return.png`, `state2.png` |

### capture note에 같이 적을 문장
- `first-open:` hero-primer, body-copy, CTA가 한 화면 안에 읽히는지 확인 예정
- `return-state:` primer 재노출 제어와 highlight 유지가 library-only로 보이는지 확인 예정

---

## 5. note bootstrap rule

## 3줄 요약
- B-1 note는 최종 회고문이 아니라 stage-specific starter sentence로 시작해야 한다.
- 각 note는 다른 역할의 시선이 아니라 다른 evidence family를 대표한다.
- note 이름과 첫 줄이 맞지 않으면 capture/log가 있어도 archive 의미가 흐려진다.

### one-line goal
note 4종을 `guidance / hierarchy / token sanity / boundary` 네 역할로 분리해 시작한다.

| note | starter 첫 줄에 꼭 들어갈 것 | 아직 쓰면 안 되는 것 |
| --- | --- | --- |
| programmer guidance state | `seenBookSelectPrimer`, `library.book-select`, 다른 seen flag 미오픈 | battle/reward/shop/ending helper 예열 |
| UI hierarchy note | `section-head -> hero-primer -> body-copy -> grid`, CTA 비차단 | shelf 전체 redesign 감상 |
| art token sanity | `primer-library`, `openingPlan/caution` highlight, 안내성 verdict | battle/reward/ending family 통합 논의 |
| PM boundary note | `library-only`, `battle/reward/shop/ending 미오픈`, `B-2 unlock 금지` | `온보딩 진행`, `battle 곧 열림` 같은 큰 문장 |

---

## 6. bootstrap verdict ladder

## 3줄 요약
- B-1 archive는 `없음`, `bootstrap-ready`, `archive-in-progress`, `review-ready` 네 상태로만 본다.
- manifest만 있으면 `bootstrap-ready`도 아닐 수 있다.
- 이 사다리를 쓰면 `evidence는 좀 있는데 아직 review-ready는 아님`을 과장 없이 말할 수 있다.

### one-line goal
B-1 archive 상태를 green 대신 작은 단계 문장으로 관리한다.

| 상태 | 뜻 | 허용 문장 | 아직 금지 |
| --- | --- | --- | --- |
| 없음 | stage archive 자체가 없음 | `B-1 미착수` | kickoff/green 문장 |
| bootstrap-ready | 경로 + starter bundle이 생성됨 | `B-1 archive-in-progress 시작 가능` | review-ready, QA pass, B-2 unlock |
| archive-in-progress | starter file에 실제 초안/캡처/로그가 들어오기 시작함 | `artifact wording alignment 진행 중` | green, battle unlock |
| review-ready | required artifacts와 wording alignment가 거의 닫힘 | `review-ready candidate` | PM green, B-2 unlock |

### 핵심 규칙
- `manifest만 있음` = 아직 부족하다.
- `captures만 있음` = 아직 부족하다.
- `manifest + log + note 4 + capture 예약`이 있어야 비로소 `bootstrap-ready`다.

---

## 7. stop / go

### GO 조건
- `b1-library/manifest.md`가 starter sentence로 채워져 있다.
- `logs/acceptance log`, `notes` 4종, `captures` 2종의 경로가 모두 고정됐다.
- `next allowed step`이 아직 `review-ready 대기` 또는 동등 문장이다.
- archive 안 어떤 파일도 battle/reward/shop/ending wording을 먼저 열지 않는다.

### STOP 신호
- `manifest.md`만 있고 log/note/capture 경로가 없다.
- 파일명에 `final`, `latest`, `misc` 같은 generic naming이 들어간다.
- PM boundary note 없이 다른 note가 먼저 커진다.
- capture가 B-1 archive 밖에 저장된다.
- `next allowed step`이 너무 빨리 `B-2 unlock`을 쓴다.

### STOP 이후 행동
1. archive root와 starter file set부터 다시 만든다.
2. manifest를 `archive-in-progress` 수준으로 낮춘다.
3. boundary note에 `library-only / other surfaces locked`를 먼저 적는다.
4. 그 뒤에만 semantic anchor 재확인과 live edit를 다시 연다.

---

## 8. 역할별 handoff 포인트

### 프로그래머
- starter bundle이 없으면 code diff를 시작하지 않는다.
- `guidance-state` note와 acceptance log 경로를 먼저 만든 뒤 edit에 들어간다.

### UI
- capture는 `first-open`, `return-state` 두 장으로 시작한다.
- 한 장짜리 generic library capture는 B-1 archive starter로 부족하다.

### 아트
- `art-token-sanity` note는 capture와 따로 시작한다.
- 큰 자산 요구는 bootstrap note가 아니라 후속 backlog로 뺀다.

### QA
- `acceptance log` starter file이 없는 상태에서 pass/fail 메모를 다른 곳에 남기지 않는다.
- B1-A1~A5 / recovery 3종 예약 문장부터 같은 파일에 적는다.

### PM/기획
- archive 상태를 `bootstrap-ready / archive-in-progress / review-ready` 중 하나로만 기록한다.
- PM green과 B-2 unlock은 review packet 전에는 쓰지 않는다.

---

## 9. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| manifest만 있고 실제 starter file set이 비는 위험 | stage archive가 이름뿐인 구조로 남는다 | B-1 starter bundle을 file path 기준으로 고정 | 첫 archive 생성 직후 |
| capture가 generic 이름이나 archive 밖 경로로 저장되는 위험 | B-1 evidence가 다른 surface와 섞인다 | first-open / return-state canonical file name 고정 | 첫 UI capture 저장 시 |
| note 4종이 만들어지지 않아 semantic/boundary 판단이 캡처만 남는 위험 | review-ready 판단이 다시 감각에 의존한다 | guidance/hierarchy/token/boundary note를 bootstrap 필수로 지정 | 첫 review-ready 검토 시 |
| bootstrap-ready와 review-ready가 같은 뜻처럼 기록되는 위험 | B-2 unlock이 조기 개방된다 | verdict ladder를 별도 표로 고정 | tracker/run log 기록 시 |
| starter bundle이 너무 무거워 첫 3분 안에 못 만들어지는 위험 | archive 규칙이 실제로는 다시 생략된다 | 최소 starter 상태를 `manifest + log + note4 + capture 예약`으로 제한 | 다음 B-1 live kickoff 직전 |

---

## 10. 즉시 다음 액션
1. `readiness board -> B-1 live kickoff packet -> 이 archive bootstrap packet -> B-1 manifest packet -> B-1 semantic anchor packet` 순서로 읽는다.
2. `docs/artifacts/YYYYMMDD/b1-library/`를 먼저 만들고 `manifest.md`, `logs/`, `captures/`, `notes/`를 바로 생성한다.
3. acceptance log 1개, note 4개, capture 2종의 파일명/초안을 먼저 고정한다.
4. 그 뒤에만 starter sentence와 semantic locator 메모를 채운다.
5. review-ready / QA pass / PM green / B-2 unlock은 여전히 `required artifacts`와 `review packet` 단계에서만 올린다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
- `docs/dicespell_overrun_onboarding_archive_bootstrap_packet_kr.md`
- `docs/artifacts/templates/stage_manifest_b1_library.md`
