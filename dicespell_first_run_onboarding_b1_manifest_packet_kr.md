# DiceSpell 첫 런 온보딩 `B-1 library` starter manifest packet

## 3줄 요약
- 이 문서는 `B-1 library packet`, `live kickoff`, `artifact stub`, `semantic anchor`, `required artifacts`, `review packet`까지로도 남아 있던 마지막 starter-manifest 공백인 **`좋아, archive를 열자마자 manifest 첫 줄은 정확히 어떻게 채워야 하지?`**를 닫는 source-of-truth다.
- 핵심은 새 primer를 더 설계하는 것이 아니라, `status / owner summary / boundary / next allowed step / handoff line` 다섯 칸을 **B-1 library stage 문장**으로 먼저 고정해, 첫 live turn의 archive가 generic 진행 메모로 흐르지 않게 만드는 것이다.
- 목표는 implementer/UI/아트/QA/PM이 B-1 archive를 열자마자 같은 starter manifest 문장으로 시작하고, review-ready/QA pass/PM green/B-2 unlock이 다시 섞이지 않게 만드는 것이다.

**한 줄 목표:** `B-1 library` 첫 archive의 starter manifest를 `field 5개 + 금지 문장 + review-ready 판정선`으로 고정해, library-only stage가 첫 줄부터 handoff-ready 언어를 유지하게 만든다.

---

## 왜 이 문서가 필요한가
지금 B-1 library 축은 거의 충분하다.

이미 있는 것:
- stage 범위: `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- 첫 10분 순서: `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- starter note 첫 줄: `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- semantic locator: `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- exact 제출물: `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- green/signoff: `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- 공통 manifest 규칙: `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
- filled example: `docs/artifacts/examples/stage_manifest_b1_library_example.md`

그런데 실제 B-1 첫 live turn 직전에는 여전히 아래 공백이 남아 있었다.

1. 공통 manifest fill packet은 강하지만, **B-1 library first turn에서 무엇을 먼저 적고 무엇을 일부러 작게 적어야 하는지**는 B-1 surface 언어로 다시 압축돼 있지 않았다.
2. example 파일은 완성본에 가깝기 때문에, 실제 첫 3분 안에 `archive-in-progress` 상태로 무엇부터 쓰고 멈춰야 하는지는 implementer가 다시 추론해야 했다.
3. 이 공백이 남아 있으면 starter manifest가 `온보딩 진행`, `library/battle 같이 준비`, `거의 green` 같은 큰 문장으로 흐르며 B-1의 library-only boundary가 첫 줄부터 흔들릴 수 있다.
4. starter stub와 semantic anchor가 stage-specific여도 manifest 첫 줄이 generic하면, archive 전체가 다시 progress memo처럼 읽혀 review-ready와 PM green이 섞이게 된다.

즉 이 문서는 새 방향 문서가 아니라, **B-1 archive를 여는 첫 줄과 다섯 field를 같은 stage 언어로 잠그는 마지막 starter packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. field contract`, `3. starter wording rule` | `B-1 live kickoff`, `artifact stub packet`, `semantic anchor packet` | starter manifest는 `seenBookSelectPrimer + hero-primer + highlight 2곳`까지만 말하고 다른 surface helper나 flag를 미리 열지 않는다 |
| UI | `2. field contract`, `4. owner summary line`, `5. handoff line rule` | `screen packet`, `B-1 review packet` | manifest의 UI 문장은 hierarchy/CTA 비차단만 말하고 shelf 전체 리디자인 의도를 섞지 않는다 |
| 아트 | `2. field contract`, `4. owner summary line` | `content deck`, `visual asset packet` | 아트 summary는 `primer-library` token sanity만 말하고 신규 대형 자산 필요를 green 조건처럼 쓰지 않는다 |
| QA | `2. field contract`, `6. review-ready manifest verdict` | `acceptance matrix`, `required artifacts packet` | manifest는 acceptance log를 대체하지 않지만, `library-only` boundary와 recovery 범위를 먼저 잠가 review-ready 문장을 작게 유지한다 |
| PM/기획 | `0. first screen 결론`, `5. handoff line rule`, `7. stop/go` | `readiness board`, `execution reporting packet` | B-1 manifest의 `next allowed step`은 끝까지 `review-ready 후 QA pass 대기` 또는 동등 문장으로 남고, B-2 unlock은 green 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- B-1 archive를 열자마자 manifest 다섯 칸은 정확히 어떻게 채워야 하지?
- 어느 문장까지가 honest stage contract이고, 어느 문장부터가 과장된 green 신호지?
- 왜 example이 있어도 starter manifest packet이 하나 더 필요하지?

### 가장 짧은 답
- B-1 starter manifest는 `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line` 다섯 칸만 먼저 채우면 된다.
- 다섯 칸 모두 `B-1 library`, `seenBookSelectPrimer`, `library.book-select`, `openingPlan/caution`, `battle/reward/shop/ending 미오픈` 언어를 유지해야 한다.
- first turn의 status는 보통 `archive-in-progress` 또는 동등 문장이지 `green`이나 `ready for battle`이 아니다.
- `next allowed step`은 끝까지 `review-ready`나 `QA pass 대기`까지로만 쓰고, `B-2 unlock`은 PM green 뒤에만 쓴다.

### 한 줄 판정 규칙
starter manifest는 **완성 선언문**이 아니라 **이번 stage가 아직 무엇만 닫았는지 적는 좁은 계약서**다.

---

## 1. core structure

## 3줄 요약
- B-1 starter manifest는 `지금 stage가 무엇인지`, `누가 무엇을 제출 중인지`, `무엇을 아직 안 열었는지`, `다음에 무엇이 허용되는지`, `handoff를 어떻게 한 줄로 남길지`만 적는다.
- 다섯 칸 중 하나라도 다른 surface를 미리 열거나 generic progress memo로 흐르면 archive는 아직 bootstrap/in-progress다.
- 이 문서의 역할은 manifest fill packet의 공통 규칙을 B-1 library stage 언어로 다시 압축하는 것이다.

### one-line goal
starter manifest를 `빈 템플릿`이 아니라 `B-1 live turn을 좁게 유지하는 첫 boundary note`로 만든다.

---

## 2. field contract

| field | B-1에서 반드시 답해야 하는 질문 | starter 권장 문장 성격 | 아직 쓰면 안 되는 것 |
| --- | --- | --- | --- |
| `status` | 지금 archive가 어떤 단계에 있지? | `archive-in-progress`, `review-ready 대기`, `QA pass 대기`처럼 아직 닫히지 않은 현재 단계 | `green`, `battle ready`, `온보딩 진행 완료` |
| `owner summary` | 프로그래머/UI/아트/QA/PM이 이번 stage에서 정확히 무엇만 다루고 있지? | `library-only` 범위와 제출물 1개씩 | battle/reward/shop/ending 선행 착수, 큰 roadmap 메모 |
| `boundary / non-change` | 무엇을 일부러 안 열었지? | `seenBookSelectPrimer 외 다른 flag 미추가`, `battle/reward/shop/ending 미오픈`, `CTA 비차단 유지` | `다음에 공용 helper 정리 예정`, `같이 손보면 좋은 후보` |
| `next allowed step` | 지금 바로 다음에 허용되는 가장 작은 단계는 무엇이지? | `review-ready evidence 정렬`, `QA pass 대기` 같은 작은 단계 | `B-2 battle unlock`, `온보딩 다음 phase`를 조기 선언 |
| `handoff line` | 이 stage를 지금 어떤 한 줄로 넘기지? | `B-1 library archive-in-progress` 또는 `review-ready` 수준의 honest line | `B-1 green`, `온보딩 green`, `battle 오픈`을 미리 쓰는 line |

### 한 줄 결론
B-1 starter manifest는 매 field가 **현재 stage를 좁히는 문장**이어야 하고, **다음 stage를 여는 문장**이면 아직 과하다.

---

## 3. starter wording rule

## 3줄 요약
- B-1 manifest의 첫 버전은 `작게`, `현재형으로`, `library-only`로 적는 편이 맞다.
- 완성도 높은 문장이 아니라 honest한 현재 단계 문장이 중요하다.
- generic progress memo보다 작은 stage sentence가 낫다.

### one-line goal
starter manifest를 `지금 archive가 어디까지 찼는지`만 말하는 현재형 문장으로 시작한다.

### 권장 패턴
1. `status`는 항상 현재 시점 현재형으로 쓴다.
   - 예: `archive-in-progress`, `review-ready evidence 정렬 중`
2. `owner summary`는 owner별 1줄만 쓴다.
   - 예: `ui: hero-primer 1면과 highlight 2곳 hierarchy만 점검 중`
3. `boundary / non-change`는 최소 2줄 이상 쓴다.
   - 예: `battle/reward/shop/ending primer는 아직 미오픈`
4. `next allowed step`은 가장 작은 다음 행동만 쓴다.
   - 예: `required artifacts 8종 wording alignment 확인 뒤 review-ready 선언 검토`
5. `handoff line`은 stage명과 현재 단계명 둘 다 넣는다.
   - 예: `B-1 library archive-in-progress: manifest/stub/semantic anchor 정렬 중이며 battle surface는 아직 열지 않았다.`

### 금지 패턴
- `이제 battle도 준비된 듯`
- `온보딩 첫 단계 완료`
- `library/battle 초안 정리`
- `다른 helper도 같이 열면 좋음`
- `거의 green`

---

## 4. owner summary line contract

## 3줄 요약
- owner summary는 다섯 역할이 지금 무엇을 하고 있는지보다, 각 역할이 이번 stage에서 무엇을 **안 하고 있는지**까지 함께 보여 줘야 한다.
- summary가 커질수록 stage는 흐려진다.
- owner summary는 evidence의 목차이지 retrospective가 아니다.

### one-line goal
owner summary를 `이번 stage 증거가 어디서 나올지`만 보여 주는 짧은 목차로 고정한다.

| owner | starter line에 꼭 들어갈 것 | 쓰면 좋은 예 | 쓰면 안 되는 예 |
| --- | --- | --- | --- |
| programmer | `seenBookSelectPrimer`, `renderGrimoireShelf`, 다른 flag 미추가 | `programmer: seenBookSelectPrimer와 library render hook만 정렬 중이며 다른 primer flag는 아직 건드리지 않음.` | `공용 primer helper도 검토 중` |
| ui | hero-primer, highlight 2곳, CTA 비차단 | `ui: hero-primer 1면과 openingPlan/caution highlight 2곳 hierarchy, CTA 비차단만 점검 중.` | `shelf refresh 방향도 같이 검토 중` |
| art | primer-library token sanity | `art: primer-library badge/accent/highlight가 안내처럼 읽히는지만 확인 중.` | `도서관 전체 key art 필요` |
| qa | B1-A1~A5, recovery 3종, library-only | `qa: B1-A1~A5와 recovery 3종을 library-only 범위로 수집 중.` | `battle helper도 같이 볼 예정` |
| pm | 경계 유지, unlock 대기 | `pm: B-1 review-ready 전까지 battle/reward/shop/ending 미오픈과 next-step 대기 문장만 유지 중.` | `B-2 곧 오픈 예정` |

---

## 5. handoff line rule

## 3줄 요약
- starter manifest의 handoff line은 마지막 green 문장이 아니다.
- 첫 handoff line은 현재 단계와 아직 닫히지 않은 경계를 함께 말해야 한다.
- 이 줄이 과장되면 tracker/run log도 같이 과장된다.

### one-line goal
handoff line을 `현재 stage 상태 + 아직 닫지 않은 경계`의 두 문장으로만 만든다.

### starter handoff line 권장 구조
> `B-1 library archive-in-progress:` `seenBookSelectPrimer`, hero-primer, highlight 2곳 관련 manifest/stub/semantic anchor 정렬 중이며 battle/reward/shop/ending primer는 아직 열지 않았다.

### review-ready 직전 line 예시
> `B-1 library review-ready candidate:` library-only artifact 8종 wording alignment 확인 중이며 QA pass와 B-2 unlock은 아직 기록하지 않는다.

### 왜 중요한가
- 이 줄은 나중에 tracker/run log의 씨앗이 된다.
- 처음부터 `green`, `unlock`, `next phase`를 넣으면 stage archive 전체가 조기 완료처럼 읽힌다.
- honest line이 있어야 review-ready와 PM green을 분리 기록할 수 있다.

---

## 6. review-ready manifest verdict

## 3줄 요약
- manifest가 잘 썼다고 review-ready는 아니다.
- 하지만 manifest가 generic하면 artifact bundle이 좋아도 review-ready를 미뤄야 한다.
- 즉 manifest는 필요조건이지만 충분조건은 아니다.

### review-ready로 갈 수 있는 starter manifest
- `status`가 아직 작은 현재 단계다.
- `owner summary`가 모두 B-1 library 범위만 말한다.
- `boundary / non-change`가 다른 surface 미오픈과 다른 flag 미추가를 적는다.
- `next allowed step`이 `review-ready`, `QA pass 대기` 등 작은 단계로 적혀 있다.
- `handoff line`이 honest하고 과장되지 않다.

### 아직 review-ready로 갈 수 없는 starter manifest
- `status`가 이미 green 같다.
- owner summary가 battle/reward/shop/ending을 미리 언급한다.
- boundary가 비어 있거나 generic하다.
- `next allowed step`이 B-2 unlock을 조기 선언한다.
- handoff line이 `온보딩 진행`, `첫 런 가이드 완료` 같은 큰 문장이다.

### 한 줄 판정
starter manifest는 **작게 적을수록** review-ready로 가기 쉽고, **크게 적을수록** 다시 수정해야 한다.

---

## 7. stop / go

### GO 조건
- starter manifest 5개 field가 모두 채워져 있다.
- 각 field가 `B-1 library`와 `library-only` boundary를 유지한다.
- artifact stub/semantic anchor와 wording이 충돌하지 않는다.
- `next allowed step`이 아직 review-ready/QA pass 대기 수준이다.

### STOP 신호
- `status`나 `handoff line`에 green/unlock이 먼저 들어간다.
- owner summary가 다른 surface를 예열한다.
- boundary / non-change가 비어 있다.
- `next allowed step`이 B-2 unlock으로 바로 점프한다.

### STOP 이후 행동
1. manifest를 다시 `archive-in-progress` 수준으로 낮춘다.
2. owner summary를 surface별 1줄로 줄인다.
3. boundary / non-change에 `battle/reward/shop/ending 미오픈`을 먼저 넣는다.
4. 그 뒤 artifact stub와 semantic anchor wording alignment를 다시 본다.

---

## 8. 역할별 handoff 포인트

### 프로그래머
- manifest를 코드 변경 요약으로 쓰지 말고 stage boundary note로 쓴다.
- `다음에 같이 건드릴 것`은 manifest 대신 별도 backlog에 남긴다.

### UI
- owner summary는 hierarchy/CTA 비차단만 쓴다.
- `보여 주고 싶은 개선점`이 아니라 `이번 stage에서 증명할 것`만 적는다.

### 아트
- primer-library token sanity만 적고, 대형 자산 필요는 manifest에서 빼 둔다.
- B-1 manifest는 art request ticket가 아니다.

### QA
- acceptance log 전에 manifest wording alignment를 먼저 본다.
- manifest가 generic하면 artifact bundle completeness도 다시 의심하는 편이 맞다.

### PM/기획
- `next allowed step`과 `handoff line`을 가장 늦게 확정한다.
- green 문장과 unlock 문장은 review packet 단계까지 미룬다.

---

## 9. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| starter manifest가 example를 흉내 내며 너무 빨리 완성 문장으로 커지는 위험 | B-1과 B-2 경계가 흐려진다 | field 5개를 `현재형 + 작은 다음 단계`로 제한 | B-1 archive 생성 직후 |
| owner summary가 각자 다른 vocabulary를 쓰는 위험 | review-ready alignment가 다시 회의 기반이 된다 | owner별 starter line contract를 표로 고정 | 첫 review-ready 검토 시 |
| handoff line이 green 문장을 선점하는 위험 | tracker/run log가 조기 완료처럼 읽힌다 | handoff line을 `archive-in-progress`/`review-ready candidate` 수준으로 제한 | PM 기록 직전 |
| boundary가 빠져 다른 surface 미오픈이 안 보이는 위험 | B-1 library-only discipline이 약해진다 | boundary/non-change에 미오픈 surface를 필수로 적게 함 | manifest 초안 작성 시 |
| manifest가 너무 일반적이라 stub/semantic anchor와 연결되지 않는 위험 | archive가 again generic progress memo가 된다 | artifact stub packet, semantic anchor packet과 바로 이어 읽도록 순서 고정 | B-1 live kickoff 직후 |

---

## 10. 즉시 다음 액션
1. `readiness board -> B-1 live kickoff packet -> B-1 artifact stub packet -> 이 starter manifest packet -> B-1 semantic anchor packet` 순서로 읽는다.
2. archive 생성 직후 `status / owner summary / boundary / next allowed step / handoff line` 다섯 칸부터 먼저 채운다.
3. handoff line에는 green이나 unlock을 쓰지 않는다.
4. 그 뒤에만 note stub와 semantic anchor 메모를 채운다.
5. review-ready / QA pass / PM green / B-2 unlock은 여전히 `required artifacts`와 `review packet` 단계에서만 올린다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_artifact_stub_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
- `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
- `docs/artifacts/templates/stage_manifest_b1_library.md`
- `docs/artifacts/examples/stage_manifest_b1_library_example.md`
