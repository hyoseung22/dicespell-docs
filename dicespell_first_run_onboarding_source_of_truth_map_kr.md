# DiceSpell 첫 런 온보딩 source-of-truth 정렬표

## 빠른 안내

### 3줄 요약
- 이 문서는 첫 런 온보딩 관련 문서를 `현재 상태 설명`과 `다음 구현 목표`로 나눠 읽게 만드는 정렬표다.
- 프로그래머는 current vs target 구분을 먼저, PM과 QA는 질문별 source-of-truth 열을 먼저 보면 된다.
- 읽을 때 핵심은 문서를 다 보는 것이 아니라, **지금 답을 어디서 가져와야 하는지 바로 찾는 것**이다.

**한 줄 목표:** 온보딩 문서 묶음에서 질문별 기준 문서를 빠르게 찾게 만든다.

### 왜 이 문서가 필요한가
- 문서가 많아질수록 readability의 핵심은 문장 단순화보다 `찾기 쉬움`에 있다.
- 이 문서는 Notion/Confluence에서 링크 허브처럼 동작하면서 source-of-truth drift를 줄인다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | current vs target 정렬표 | `integration workorder` |
| QA | 질문별 source-of-truth 표 | `acceptance matrix` |
| PM | owner별 읽기 순서 | `summary` |
| UI/아트 | target 문서 열 | `screen packet`, `content deck` |

---

## 문서 목적

이 문서는 현재 DiceSpell 첫 런 온보딩 축에서 이미 작성된 아래 문서들을 **`현재 코드 설명 문서`와 `다음 구현 목표 문서`로 명확히 분리해 읽게 만드는 정렬표**다.

- `docs/dicespell_first_run_onboarding_handoff_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
- `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- `docs/dicespell_current_build_reverse_spec_kr.md`
- `docs/dicespell_current_build_code_map_kr.md`

지금 온보딩 축은 이미 방향, 화면, 컷오버 순서, acceptance까지 상당히 많이 닫혀 있다. 그런데 실제 구현 직전 기준으로는 마지막으로 아래 혼선이 남아 있다.

> `현재 코드가 무엇을 하고 있는가`를 설명하는 문서와, `다음 온보딩 UX pass가 무엇을 바꿔야 하는가`를 설명하는 문서를 같은 층위로 읽으면, 구현자/UI/아트/QA가 서로 다른 baseline을 붙잡게 된다.

이 문서는 그 문제만 해결한다. 즉 새 기획을 추가하는 문서가 아니라, **질문별 source-of-truth, current-vs-target 정렬, owner별 읽기 순서, gap analysis, handoff 우선순위**를 한 장으로 고정하는 문서다.

---

## 1. 왜 이 문서가 지금 필요한가

현재 DiceSpell 문서 구조를 실제 구현자 관점에서 다시 보면, 오버런 축은 이미 같은 병목을 한 번 통과했다.

- `docs/dicespell_current_build_reverse_spec_kr.md`
- `docs/dicespell_current_build_code_map_kr.md`
- `docs/dicespell_overrun_event_*`
- `docs/dicespell_overrun_event_source_of_truth_map_kr.md`

즉 오버런 축은 이미 `현재 즉시 적용 런타임`과 `다음 컷오버 목표`를 서로 다른 문서 역할로 분리해 읽게 만들었다.

반면 첫 런 온보딩 축은 아래 문서가 이미 충분히 많음에도, 아직 같은 종류의 정렬표가 없다.

- handoff: 왜 필요한가 / 무엇을 하지 않을 것인가
- screen packet: 어느 render surface에 붙는가
- integration workorder: 어떤 순서로 꽂는가
- acceptance matrix: 언제 완료라고 말할 수 있는가

여기까지만 보면 충분해 보이지만, 실제 구현자는 아직도 아래 질문에서 잠깐 멈출 수 있다.

1. `docs/dicespell_current_build_reverse_spec_kr.md`에 적힌 책 선택/전투/보상/엔딩 설명은 **현재 그대로인 baseline**인가, 아니면 이제 온보딩 primer가 이미 붙어 있다고 가정한 target 설명인가?
2. `docs/dicespell_current_build_code_map_kr.md`가 말하는 `renderGrimoireShelf()` / `renderBattle()` / `renderReward()` / `renderShop()` / `renderEnding()`는 **그냥 현재 실행 경로 설명**인가, 아니면 primer slot까지 포함한 target 구조 설명인가?
3. 프로그래머는 어느 문서에서 `현재 render slot`을 읽고, 어느 문서에서 `다음 helper/payload/event hook`을 읽어야 하는가?
4. UI/아트 오너는 `현재 body-copy 구조`와 `다음 primer 배치 구조`를 어느 기준으로 분리해 봐야 하는가?
5. QA는 `현재 화면을 사실대로 읽는 문서`와 `붙여야 할 새 acceptance 기준 문서`를 어떤 순서로 읽어야 중복 검수나 오검수를 피할 수 있는가?

즉 지금 남은 문서 병목은 방향이나 검수표 부족이 아니라, **문서 역할 분리 부족**이다.

---

## 2. 이 문서가 닫으려는 핵심 혼선

### 혼선 A. 현재 설명과 목표 계약이 같은 목소리로 보인다

현재 `docs/dicespell_current_build_reverse_spec_kr.md`와 `docs/dicespell_current_build_code_map_kr.md`는 아주 유용하다. 하지만 이 둘은 원래 역할상 **현재 실행 중인 build baseline**을 설명하는 문서다.

반면 온보딩 문서 4종은 아래를 설명한다.

- 무엇을 왜 붙여야 하는가
- 어디에 붙여야 하는가
- 어떤 순서로 컷오버해야 하는가
- 언제 완료라고 말할 수 있는가

즉 둘은 같은 층위 문서가 아니다.

### 혼선 B. owner별로 먼저 읽어야 할 문서가 다르다

- 프로그래머는 `현재 render hook / 상태 구조`를 먼저 읽고 target contract를 읽어야 한다.
- UI는 `현재 화면 위계`를 먼저 읽고 screen packet/acceptance를 읽어야 한다.
- 아트는 current build 전체보다 `screen packet + acceptance + 범위 제한`이 먼저다.
- QA는 현재 baseline과 acceptance를 둘 다 보되, one source로 섞지 않아야 한다.

### 혼선 C. 문서가 충분한데도 구현 직전 망설임이 남는다

지금 온보딩 문서 묶음은 이미 굉장히 생산적이다. 그런데도 구현 직전 망설임이 남는 이유는 문서 수가 적어서가 아니라, **각 문서가 “무엇의 source-of-truth인가”가 질문별로 정리돼 있지 않아서**다.

---

## 3. 문서 역할 분리 원칙

온보딩 축의 문서는 아래 3층으로 읽어야 한다.

| 층위 | 문서 | 역할 | 이 문서에서 하면 안 되는 것 |
| --- | --- | --- | --- |
| Current baseline | `docs/dicespell_current_build_reverse_spec_kr.md`, `docs/dicespell_current_build_code_map_kr.md` | 지금 코드가 실제로 무엇을 하고 어떤 파일/slot을 쓰는지 설명 | 아직 안 붙은 primer target 구조를 현재형으로 서술 |
| Target contract | `docs/dicespell_first_run_onboarding_handoff_kr.md`, `docs/dicespell_first_run_onboarding_screen_packet_kr.md`, `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`, `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` | 다음 온보딩 UX pass에서 무엇을 어떤 순서와 완료선으로 붙일지 계약 | 현재 코드가 이미 그렇게 동작한다고 오해하게 쓰기 |
| Tracker / operating dashboard | `docs/dicespell_development_tracker_kr.html`, `docs/dicespell_standalone_blueprint_kr.md`, `docs/dicespell_automation_run_log_kr.md` | 왜 지금 이 작업이 우선인지, blocker가 문서인지 구현인지, 리스크가 무엇인지 운영 판단 제공 | 세부 render slot이나 세세한 acceptance contract를 문서 여러 장 대신 혼자 떠맡기 |

핵심은 아래 두 줄이다.

- `current_build_*` 문서는 **현재 실행 상태 설명서**다.
- `first_run_onboarding_*` 문서는 **다음 구현 목표 계약서**다.

둘을 섞어 읽으면 온보딩 구현은 다시 감으로 흘러간다.

---

## 4. 질문별 source-of-truth 맵

## Q1. 지금 첫 런 온보딩은 실제 코드에 이미 붙어 있는가?

- **정답 source-of-truth:** `docs/dicespell_current_build_reverse_spec_kr.md`, `docs/dicespell_current_build_code_map_kr.md`
- **왜:** 이 질문은 `현재 build baseline` 질문이기 때문이다.
- **현재 답:** 아직 본격 primer layer는 붙어 있지 않다. 현재 문서는 책 선택/전투/reward/shop/ending이 어떻게 동작하는지를 baseline으로 설명한다.
- **보조 문서:** `docs/dicespell_development_tracker_kr.html`

## Q2. 첫 런 온보딩이 무엇을 바꿔야 하는가?

- **정답 source-of-truth:** `docs/dicespell_first_run_onboarding_handoff_kr.md`
- **왜:** 범위, 목적, 포함/제외, 4개 surface 정의가 여기서 결정된다.
- **현재 답:** 대형 튜토리얼 모드가 아니라, `도서관 / 첫 전투 / 첫 reward·shop / 첫 ending·overrun` 4개 surface primer layer를 붙인다.

## Q3. primer를 현재 화면 어디에 얹어야 하는가?

- **정답 source-of-truth:** `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- **왜:** 실제 `renderGrimoireShelf()` / `renderBattle()` / `renderReward()` / `renderShop()` / `renderEnding()` 기준 주입 위치와 밀도를 가장 잘게 고정한 문서이기 때문이다.
- **보조 문서:** `docs/dicespell_current_build_code_map_kr.md`
- **읽는 순서:** code map으로 현재 slot 확인 → screen packet으로 target slot 계약 확인

## Q4. primer 상태는 어디에 저장하고 어떤 순서로 구현해야 하는가?

- **정답 source-of-truth:** `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
- **왜:** `meta.uiGuidance`, `getPrimerState()`, event hook, Step 0~4 컷오버 순서가 여기서 고정된다.
- **보조 문서:** `docs/dicespell_current_build_reverse_spec_kr.md`, `docs/dicespell_current_build_code_map_kr.md`

## Q5. 언제 구현 완료라고 말할 수 있는가?

- **정답 source-of-truth:** `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- **왜:** acceptance 8종, recovery 6종, owner별 납품 증거, `overrunEvent` 전후 ending 문맥 복구선이 여기서 닫힌다.

## Q6. 현재 tracker가 말하는 다음 우선순위는 문서 추가인가 구현 전환인가?

- **정답 source-of-truth:** `docs/dicespell_development_tracker_kr.html`, `docs/dicespell_standalone_blueprint_kr.md`
- **왜:** 이 질문은 source-of-truth가 코드나 화면이 아니라 운영 판단이기 때문이다.
- **현재 답:** 온보딩 축의 방향/화면/작업지시서/acceptance는 꽤 많이 닫혔고, 남은 최우선은 문서 공백보다 실제 구현 전환이다. 다만 현재 문서 역할 분리까지 닫아 두면 다음 UX pass에서 오독 비용이 더 줄어든다.

## Q7. UI 오너는 무엇을 넘기면 끝인가?

- **정답 source-of-truth:** `docs/dicespell_first_run_onboarding_screen_packet_kr.md` + `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- **왜:** 전자는 배치/밀도/카피 계약, 후자는 검수 종료선을 담당한다.

## Q8. 아트 오너는 current build 전체를 읽어야 하는가?

- **정답 source-of-truth:** `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- **보조 문서:** `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- **왜:** 아트 오너에게는 current build 전체보다 backplate/badge/accent 범위와 완료선이 더 중요하다.

## Q9. QA는 무엇을 baseline으로 보고 무엇을 target으로 봐야 하는가?

- **정답 source-of-truth:**
  - baseline: `docs/dicespell_current_build_reverse_spec_kr.md`, `docs/dicespell_current_build_code_map_kr.md`
  - target / 완료선: `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- **왜:** 현재 화면이 실제로 어떻게 보이는지와, 붙여야 할 새 guidance layer가 어떤 pass/fail을 가져야 하는지는 다른 질문이기 때문이다.

---

## 5. current vs target 정렬표

| 주제 | current baseline 문서가 말하는 것 | target contract 문서가 말하는 것 | 구현자가 이렇게 읽어야 함 |
| --- | --- | --- | --- |
| 도서관 책 선택 | 책 카드에 `playstyle/openingPlan/caution/signatureRule/completionBonus`가 이미 노출돼 있음 | 첫 런에서는 `초반 운영 -> 주의점` 읽기 순서를 hero-primer로 강하게 만든다 | 현재 field는 이미 있음, 다음 pass는 읽기 순서 레이어를 얹는 작업 |
| 첫 전투 | `battle-subcopy`, hand panel, log, auto-pass 흐름이 존재 | hero-primer + 첫 주문 inline hint + auto-pass hint를 추가 | 전투 규칙을 바꾸는 것이 아니라 읽는 우선순위를 보조하는 작업 |
| reward / shop | 각각 별도 phase로 동작하지만 첫 런용 목적 문구 강조는 아직 약함 | reward는 `즉시`, shop은 `장기 투자`로 primer 강화 | phase 구조는 유지, purpose line만 first-run layer로 분리 |
| ending | 패배/승리/오버런 정보를 한 화면에서 처리 | `무엇이 남는가`와 `버튼 의미`만 primer로 얇게 분리 | ending은 행동 의미까지만, 장면 감각은 `overrunEvent`에 남김 |
| 저장 구조 | `meta`와 session 구조는 있으나 `uiGuidance`는 아직 없음 | `meta.uiGuidance`가 장기 seen source-of-truth | 새로운 진행 시스템이 아니라 lightweight UX state 추가 |
| 코드 구조 | render 함수는 현재 화면만 설명 | helper/payload/event hook로 target slot을 계산 | code map은 baseline, workorder는 컷오버 계약 |

---

## 6. owner별 읽기 우선순위

## 6.1 프로그래머

### 먼저 읽을 것

1. `docs/dicespell_current_build_code_map_kr.md`
2. `docs/dicespell_current_build_reverse_spec_kr.md`

### 그 다음 읽을 것

3. `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
4. `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
5. `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`

### 마지막으로 참고할 것

6. `docs/dicespell_first_run_onboarding_handoff_kr.md`
7. `docs/dicespell_development_tracker_kr.html`

### 이유

프로그래머는 먼저 `현재 render slot / 상태 구조 / 실행 경로`를 정확히 알아야 한다. 그 다음에야 `어디에 어떤 helper/payload/hook를 얹을지`가 안전하게 읽힌다.

## 6.2 UI 오너

### 먼저 읽을 것

1. `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
2. `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`

### 보조로 읽을 것

3. `docs/dicespell_current_build_reverse_spec_kr.md`
4. `docs/dicespell_current_build_code_map_kr.md`

### 이유

UI 오너에게 중요한 것은 `현재 화면 위계`와 `target primer 위계`의 차이다. 전체 역기획보다 screen packet과 acceptance가 먼저다.

## 6.3 아트 오너

### 먼저 읽을 것

1. `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
2. `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`

### 필요 시만 읽을 것

3. `docs/dicespell_first_run_onboarding_handoff_kr.md`

### 이유

아트 범위는 backplate/badge/accent 중심이다. current code 전체를 먼저 읽게 하면 범위가 불필요하게 커진다.

## 6.4 QA

### 먼저 읽을 것

1. `docs/dicespell_current_build_reverse_spec_kr.md`
2. `docs/dicespell_current_build_code_map_kr.md`

### 그 다음 읽을 것

3. `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
4. `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`

### 이유

QA는 먼저 현재 baseline이 무엇인지 알아야 한다. 그 위에 target acceptance를 얹어야 `현재 이미 있는 기능`과 `이번에 붙일 기능`을 분리해 검수할 수 있다.

---

## 7. gap analysis

## Gap 1. 온보딩 축에는 아직 별도의 source-of-truth 정렬표가 없었다

- 영향: current build 설명서와 target handoff 문서를 같은 층위로 읽을 위험이 남아 있었다.
- 이번 대응: 이 문서로 질문별 source-of-truth와 owner별 읽기 순서를 고정한다.

## Gap 2. `current_build_*` 문서가 온보딩 target을 이미 포함한다고 오해될 수 있었다

- 영향: 구현자가 `현재 body-copy가 이미 primer 역할을 하고 있지 않나?` 같은 애매한 판단을 할 수 있다.
- 이번 대응: current는 baseline, onboarding 문서 4종은 target contract라는 역할 분리를 명시한다.

## Gap 3. UI/아트/QA가 어떤 current 문서를 꼭 읽어야 하는지 우선순위가 없었다

- 영향: 어떤 owner는 문서를 과하게 읽고, 어떤 owner는 baseline 없이 target만 읽어 맥락을 놓칠 수 있다.
- 이번 대응: owner별 읽기 우선순위를 고정한다.

## Gap 4. tracker 관점에서는 `다음 구현`이 맞지만, 문서 관점에서는 마지막 정렬표가 빠져 있었다

- 영향: 온보딩 축이 사실상 닫혀 있어도 `문서가 충분히 production-ready한가`라는 질문에는 마지막 한 칸이 비어 있었다.
- 이번 대응: 이제 온보딩 축도 handoff + screen packet + integration workorder + acceptance matrix + source-of-truth map 5단으로 닫힌다.

---

## 8. 역할별 실제 handoff 메모

## 프로그래머에게 넘길 것

- `current_build_code_map`은 어디를 고쳐야 하는지 확인하는 baseline이다.
- `screen_packet`은 어디에 무엇을 꽂아야 하는지 말한다.
- `integration_workorder`는 어떤 순서로 꽂을지 말한다.
- `acceptance_matrix`는 언제 끝났다고 말할지 말한다.
- 이 문서는 위 문서들을 어떤 순서와 역할로 읽어야 하는지 고정한다.

즉 프로그래머는 이 문서 기준으로 더 이상 `현재 코드 설명`과 `다음 목표 계약`을 같은 문맥으로 읽지 않는다.

## UI 오너에게 넘길 것

- current build는 기존 위계와 정보 밀도를 보는 참고 baseline이다.
- target 위계, badge/accent/primer 밀도, CTA 비경쟁 규칙은 `screen_packet`과 `acceptance_matrix`가 source-of-truth다.
- 이 문서는 그 둘이 current build를 어떤 방식으로 덮어써야 하는지 정렬해 준다.

## 아트 오너에게 넘길 것

- 온보딩은 새 scene art 작업이 아니다.
- backplate/badge/accent 중심의 lightweight asset pass다.
- current build 전체를 재설계할 필요가 없고, target 범위는 `screen_packet`에 한정된다.

## QA에게 넘길 것

- baseline은 `current_build_*`
- pass/fail은 `acceptance_matrix`
- hook/순서는 `integration_workorder`
- 이 문서는 이 셋을 혼동 없이 읽는 매뉴얼이다.

---

## 9. 다음 구현 슬롯을 위한 읽기 패킷

다음 UX 구현 슬롯에서 첫 런 온보딩을 실제로 붙일 때는 아래 패킷만 열면 된다.

### 패킷 A. baseline 확인

1. `docs/dicespell_current_build_code_map_kr.md`
2. `docs/dicespell_current_build_reverse_spec_kr.md`

### 패킷 B. target 계약 확인

3. `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
4. `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
5. `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`

### 패킷 C. 운영 판단 확인

6. `docs/dicespell_development_tracker_kr.html`
7. `docs/dicespell_standalone_blueprint_kr.md`

핵심은 이것이다.

> 다음 구현 슬롯은 이제 `무엇을 만들지`를 다시 토론하는 자리가 아니라, current baseline 위에 target primer layer를 Step 0~4 순서로 꽂는 bounded 작업이다.

---

## 10. 리스크 & 후속 과제

### 리스크 1. current와 target을 다시 같은 층위로 읽을 위험

- 영향: `body-copy 정도면 primer 아닌가?`, `current build 설명을 target contract처럼 읽자` 같은 미세한 해석 차이가 다시 생긴다.
- 대응: 온보딩 구현 전에는 이 문서의 질문별 source-of-truth 표를 먼저 훑고 시작한다.

### 리스크 2. owner별 문서 읽기 범위가 다시 과하거나 부족해질 위험

- 영향: 프로그래머는 과도한 문서를 읽고, 아트/QA는 baseline 없이 target만 읽을 수 있다.
- 대응: owner별 읽기 우선순위를 작업 kickoff checklist에 포함한다.

### 리스크 3. tracker가 `이제 구현으로 가자`고 말하는 시점에 문서 정렬표 없이 전환할 위험

- 영향: 문서 수는 많은데도 실제 착수 속도가 기대만큼 안 나올 수 있다.
- 대응: 이번 source-of-truth map까지 닫은 뒤에는, 추가 온보딩 문서보다 실제 구현 전환을 우선한다.

### 다음 후속 과제

1. 다음 UX pass가 열리면 `도서관 + 첫 전투 primer`부터 실제 코드에 붙인다.
2. 구현 전 review에서는 이 문서의 `패킷 A/B/C` 순서 그대로 문서를 확인한다.
3. 필요하다면 후속으로 `production cutsheet`를 별도로 만들 수 있지만, 현재 우선순위는 문서 추가보다 코드 컷오버다.

---

## 11. 최종 결론

첫 런 온보딩 축은 이제 `무슨 말을 할까`, `어디에 붙일까`, `어떤 순서로 꽂을까`, `언제 끝났다고 할까`까지는 이미 거의 닫혀 있었다.

남아 있던 마지막 문서 공백은 그 문서들을 **어떤 질문에 어떤 source-of-truth로 읽어야 하는가**였다.

이 정렬표가 닫아 주는 것은 바로 그 마지막 한 칸이다.

이제 온보딩 축도 다음처럼 읽으면 된다.

- `current_build_*` = 지금 코드 설명
- `first_run_onboarding_*` = 다음 구현 목표 계약
- `tracker / blueprint / run log` = 왜 지금 이 작업을 하는지와 운영 판단

즉 다음 UX pass는 더 이상 문서 역할을 재해석하는 시간이 아니라, 실제 primer layer를 bounded하게 붙이는 시간이어야 한다.