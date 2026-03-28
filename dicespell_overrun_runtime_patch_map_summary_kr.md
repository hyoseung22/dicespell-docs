# DiceSpell `overrunEvent` 런타임 패치 맵 요약

## 3줄 요약
- 이 문서는 `docs/dicespell_overrun_runtime_patch_map_kr.md`의 읽기용 companion이다.
- 핵심은 새 기획이 아니라, **현재 실제 코드가 어디서 A-1~A-4 target과 갈라져 있는지**를 3~5분 안에 파악하게 만드는 것이다.
- 프로그래머는 `continueOverrun()` 현재 책임 과밀을, UI/아트는 ending이 아직 flavor를 너무 많이 먹고 있다는 점을, QA는 smoke가 아직 immediate-apply baseline이라는 점을 먼저 보면 된다.

**한 줄 목표:** `overrunEvent` 실구현 전에 현재 live runtime을 다시 grep하지 않고 바로 컷오버 범위를 선언하게 만든다.

---

## 이 문서는 무엇인가
이미 DiceSpell에는 handoff/spec/screen/content/acceptance/A-1~A-4/runway 문서가 충분하다. 그런데 실제 구현 직전에는 결국 아래 질문이 다시 살아난다.

- 지금 live code에서 `continueOverrun()`가 정확히 뭘 한 번에 하고 있지?
- `renderEnding()`와 `renderCenter()`는 어느 정도까지 아직 old baseline이지?
- `smoke-test.mjs`는 뭘 아직 옛 기준으로 기대하고 있지?

이 문서는 그 질문에 답하는 **현재 runtime anchor 요약본**이다.

---

## 지금 실제 코드에서 벌어지는 일
### app.js
- `continue-overrun` 액션은 지금도 바로 `continueOverrun(appState.session)`를 호출한다.
- `renderEnding()`은 ending 안에 `오버런 전리품 보관함`과 `오버런 계속` CTA를 함께 렌더한다.
- `renderCenter()`는 아직 `overrunEvent` phase를 모른다.

### game-engine.js
- `chooseEndingOverrunReward()`는 `selectedRewardId`만 저장한다.
- `continueOverrun()`는 지금 한 함수 안에서 아래를 다 한다.
  - 선택 reward 적용
  - `overrunLevel += 1`
  - `segmentTrophyTemplateIds = []`
  - `pagePlan` 확장
  - `currentPage += 1`
  - `phase='library'`
  - `maybeEnterNextPage()`

### smoke-test.mjs
- 현재 smoke는 `continueOverrun()` 직후 이미 reward 적용과 다음 세그먼트 진입이 끝났다고 본다.

즉 현재 live runtime은 아직 `overrunEvent` cutover 이전 baseline이다.

---

## 왜 이게 중요하나
문서는 충분해도, 구현자는 결국 현재 코드와 부딪힌다. 여기서 가장 흔한 실수는 두 가지다.

1. `continueOverrun()`를 한 번에 다 뜯어내려다 enter/snapshot/UI/resolve가 다시 섞이는 것
2. old smoke baseline을 머릿속에서 놓치고 새 구조를 붙여 half-cutover 상태를 만드는 것

그래서 다음 구현은 문서 해석보다도 **현재 함수 책임을 step별로 재배치하는 일**로 읽어야 한다.

---

## A-step별로 지금 어디를 건드리나
### A-1
- `continueOverrun()`에서 immediate apply와 page progression을 떼고
- `phase='overrunEvent'` enter만 남긴다.

### A-2
- 엔진이 `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet`를 직접 들고 저장하게 만든다.
- render는 더 이상 branch를 추론하지 않는다.

### A-3
- `renderCenter()`가 `renderOverrunEvent(summary)`를 독립 장면 카드로 붙인다.
- ending과 다른 shell/tone을 만든다.

### A-4
- `confirm-overrun-event`에서만 reward 1회 적용 + next segment progression이 일어나게 만든다.
- old immediate baseline smoke를 제거한다.

---

## 역할별로 기억할 한 문장
### 프로그래머
지금 가장 중요한 건 새 함수명을 придумելու 게 아니라, `continueOverrun()`에 몰린 책임을 A-1~A-4로 잘라내는 것이다.

### UI
지금 ending이 너무 많은 말을 하고 있다. `overrunEvent`가 생기면 ending은 결과/버튼 의미까지만 남아야 한다.

### 아트
핵심은 대형 삽화보다 tone 경계다. 특히 `none`이 빈 상태처럼 보이면 실패다.

### QA
현재 smoke는 old baseline이다. 새 구조가 들어오면 `phase 분리`, `reward 1회 적용`, `reload guard`를 먼저 봐야 한다.

---

## 한 줄 결론
`overrunEvent` 실구현 직전의 진짜 빈칸은 문서 부족이 아니라, **현재 live runtime을 어디서 잘라 새 cutover 문서에 꽂을지에 대한 마지막 코드 앵커 정렬**이다.

---

## 원본 문서
- 원본 source handoff: `./dicespell_overrun_runtime_patch_map_kr.md`
- 실행 런웨이: `./dicespell_overrun_cutover_runway_packet_kr.md`
- A-1: `./dicespell_overrun_a1_engine_cutover_packet_kr.md`
- A-2: `./dicespell_overrun_a2_snapshot_packet_kr.md`
- A-3: `./dicespell_overrun_a3_ui_packet_kr.md`
- A-4: `./dicespell_overrun_a4_resolve_packet_kr.md`
