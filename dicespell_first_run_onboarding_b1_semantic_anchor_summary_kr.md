# DiceSpell 첫 런 온보딩 `B-1 library` semantic anchor summary

## 3줄 요약
- 이 문서는 `B-1 library` 첫 live edit 직전 마지막 작은 공백이던 `정확히 어느 함수/DOM/캡처를 같은 책임으로 다시 찾아야 하지?`를 3~5분 안에 읽게 만드는 readable companion이다.
- 핵심은 새 primer를 더 설계하는 것이 아니라, `readMetaProgress` / `persistMetaProgress` / `renderGrimoireShelf` / `openingPlan` / `caution` / `choose-grimoire` / first-open·return-state evidence를 **B-1 library semantic zone**으로 다시 묶는 것이다.
- 목표는 implementer/UI/QA/PM이 line drift가 있어도 `state family -> render family -> highlight family -> CTA path -> capture question` 순서로 같은 stage 언어를 재사용하게 만드는 것이다.

**한 줄 목표:** B-1 첫 live turn에서 semantic anchor 재확인을 감각적인 grep 대신 `library-only locator checklist`로 고정한다.

---

## first screen

### 이 문서가 답하는 질문
- B-1 첫 edit 전에 무엇을 semantic anchor라고 부르지?
- line number가 조금 밀려도 무엇까지만 찾고 멈춰야 하지?
- 왜 archive/stub가 있어도 semantic anchor packet이 하나 더 필요하지?

### 가장 짧은 답
- B-1 semantic anchor는 `meta state`, `library shell`, `highlight hook`, `choose CTA path`, `first-open/return-state evidence` 다섯 묶음이다.
- 프로그래머는 `readMetaProgress` / `persistMetaProgress` / `renderGrimoireShelf` / `data-action="choose-grimoire"`까지만 다시 찾는다.
- UI/QA는 `section-head -> hero-primer -> body-copy -> grid` 위계와 `openingPlan/caution` highlight만 본다.
- battle/reward/shop/ending render path나 다른 seen flag는 여전히 B-1 바깥이다.

---

## 왜 이 문서가 필요한가
- `B-1 library packet`은 범위를 알려 주지만, 첫 10분 안에 implementer/UI/QA가 같은 이름으로 다시 찾을 locator checklist는 따로 없었다.
- `live kickoff`는 semantic anchor 재확인을 요구하지만, 무엇이 semantic anchor인지 B-1 stage 언어로 다시 한 장에 고정하진 않았다.
- 그래서 실제 live edit 직전에는 line drift를 핑계로 stage drift가 생길 위험이 남아 있었다.

즉 이 summary는 **locator를 찾는 기준을 다시 좁혀 B-1이 library-only로 남게 만드는 요약 보드**다.

---

## core checklist

| anchor family | 다시 찾을 locator | 이번 stage 질문 | 이번 stage에서 열면 안 되는 것 |
| --- | --- | --- | --- |
| Meta state | `readMetaProgress`, `persistMetaProgress`, `uiGuidance.seenBookSelectPrimer` | seen flag를 어디서 읽고 저장하지? | 다른 primer seen flag 선추가 |
| Library shell | `renderGrimoireShelf`, `.section-head`, `.body-copy`, `renderLegacyLoadoutPanel`, `.grimoire-grid` | hero-primer가 어디서 library surface 안에 들어가지? | shelf 전체 재배치, modal primer |
| Highlight hook | `openingPlan`, `caution` | 어떤 두 줄만 guide처럼 올라오지? | 카드 전체 강조, 추천 ribbon |
| CTA path | `data-action="choose-grimoire"` | primer가 있어도 시작 버튼이 바로 눌리나? | primer dismiss를 선택보다 우선시키는 구조 |
| Evidence | `...first-open.png`, `...return-state.png` | 첫 진입 / 재진입 질문을 어떻게 증명하지? | battle/reward/shop/ending 캡처 혼입 |

---

## role-specific handoff points

### 프로그래머
- note는 `state family / render family / highlight family / CTA path / non-change line` 다섯 줄이면 충분하다.
- line drift가 있어도 위 다섯 책임만 찾고 멈춘다.
- `seenBattlePrimer` 등 다른 flag 예열은 금지다.

### UI
- `section-head -> hero-primer -> body-copy -> grid` 위계를 먼저 적는다.
- 판단 기준은 예쁨이 아니라 읽힘과 CTA 비차단이다.
- shelf refresh 제안은 이번 stage 바깥이다.

### 아트
- primer-library / library-gold / openingPlan-caution highlight까지만 본다.
- token sanity는 `충분 / 약함 / 과함` verdict로 끝내는 편이 맞다.
- 다른 surface 톤 정렬 논의는 다음 단계다.

### QA
- first-open / return-state 캡처는 둘 다 필요하다.
- acceptance log에는 `library-only boundary 유지` 문장이 빠지면 안 된다.
- generic glamour shot은 review-ready 근거가 아니다.

### PM
- semantic anchor 재확인은 kickoff layer다.
- green/unlock은 여전히 evidence bundle 뒤에만 쓴다.
- `온보딩 진행`, `library/battle 초안` 같은 큰 문장은 계속 금지다.

---

## stop / go

### GO
- programmer note가 `seenBookSelectPrimer`와 library render hook만 다룬다.
- UI/QA가 first-open / return-state 두 질문을 library-only로 적는다.
- PM boundary note가 `B-2 unlock 대기`를 유지한다.

### STOP
- battle/reward/shop/ending locator까지 같이 열기 시작한다.
- 다른 seen flag가 state diff note에 들어간다.
- capture가 다른 surface와 섞인다.
- hierarchy note가 shelf 전체 리디자인 제안으로 커진다.

---

## immediate next actions
1. `readiness board -> B-1 live kickoff -> B-1 artifact stub -> B-1 semantic anchor packet` 순서로 읽는다.
2. programmer state diff note에 다섯 줄 anchor를 먼저 적는다.
3. UI hierarchy note에 first-open / return-state 질문을 먼저 적는다.
4. 그 뒤에만 library-only edit를 시작한다.
5. review-ready / QA pass / PM green / B-2 unlock은 여전히 evidence bundle로만 닫는다.
