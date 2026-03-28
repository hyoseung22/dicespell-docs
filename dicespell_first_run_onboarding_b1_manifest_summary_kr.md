# DiceSpell 첫 런 온보딩 `B-1 library` starter manifest summary

## 3줄 요약
- 이 문서는 `B-1 library` 첫 archive를 열자마자 `manifest 첫 줄을 정확히 어떻게 적어야 하지?`라는 마지막 starter-manifest 공백을 3~5분 안에 읽게 만드는 readable companion이다.
- 핵심은 새 primer를 더 설계하는 것이 아니라, `status / owner summary / boundary / next allowed step / handoff line` 다섯 칸을 **B-1 library stage 문장**으로 먼저 잠가 archive가 generic progress memo로 흐르지 않게 만드는 것이다.
- 목표는 implementer/UI/아트/QA/PM이 B-1 archive를 열자마자 같은 starter manifest 문장으로 시작하고, review-ready/QA pass/PM green/B-2 unlock이 다시 같은 층위로 섞이지 않게 만드는 것이다.

**한 줄 목표:** B-1 첫 archive의 starter manifest를 `field 5개 + 금지 문장 + 작은 다음 단계` 체크리스트로 고정한다.

---

## first screen

### 이 문서가 답하는 질문
- B-1 archive를 열자마자 manifest 다섯 칸은 무엇으로 채워야 하지?
- example이 있어도 왜 B-1 starter manifest packet이 하나 더 필요하지?
- 어떤 문장부터가 review-ready/green을 너무 빨리 말하는 과장이지?

### 가장 짧은 답
- starter manifest는 `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line` 다섯 칸만 먼저 채우면 된다.
- 다섯 칸 모두 `B-1 library`, `seenBookSelectPrimer`, `hero-primer`, `openingPlan/caution`, `battle/reward/shop/ending 미오픈` 언어를 유지해야 한다.
- `status`와 `handoff line`은 처음부터 green이면 안 되고, 보통 `archive-in-progress`나 `review-ready candidate` 수준이 맞다.
- `next allowed step`은 끝까지 `review-ready evidence 정렬`이나 `QA pass 대기`처럼 작은 단계로 남기고, B-2 unlock은 PM green 뒤에만 쓴다.

---

## 왜 이 문서가 필요한가
- 공통 `manifest fill packet`은 강하지만, 실제 첫 B-1 live turn에서 무엇부터 적고 어디서 멈출지는 B-1 surface 언어로 다시 잠겨 있지 않았다.
- filled example은 완성본에 가깝기 때문에, 막 archive를 만든 직후의 honest한 starter wording은 여전히 implementer가 추론해야 했다.
- 이 공백이 남아 있으면 manifest 첫 줄이 `온보딩 진행`, `library/battle 같이 준비`, `거의 green` 같은 큰 문장으로 커져 B-1의 library-only boundary가 흔들릴 수 있다.

즉 이 summary는 **starter manifest를 B-1 stage contract로 다시 좁히는 빠른 체크리스트**다.

---

## starter manifest checklist

| field | B-1에서 꼭 답할 질문 | 지금 쓰면 좋은 수준 | 지금 쓰면 안 되는 수준 |
| --- | --- | --- | --- |
| `status` | 지금 archive가 정확히 어디까지 왔지? | `archive-in-progress`, `review-ready evidence 정렬 중` | `green`, `battle ready`, `온보딩 완료` |
| `owner summary` | 각 owner는 이번 stage에서 무엇만 다루지? | `hero-primer`, `highlight 2곳`, `library-only acceptance`, `token sanity` | battle/reward/shop/ending, shelf refresh, 대형 자산 |
| `boundary / non-change` | 무엇을 아직 안 열었지? | 다른 primer flag 미추가, 다른 surface 미오픈, CTA 비차단 유지 | 다음에 같이 건드릴 후보, helper 공용화 계획 |
| `next allowed step` | 가장 작은 다음 단계는 무엇이지? | `required artifacts wording alignment`, `QA pass 대기` | `B-2 unlock`, `온보딩 다음 phase` |
| `handoff line` | 지금 이 stage를 어떤 한 줄로 넘기지? | `B-1 library archive-in-progress ... battle/reward/shop/ending은 아직 열지 않았다.` | `B-1 green`, `battle 오픈`, `온보딩 진행 완료` |

---

## owner summary quick guide

### 프로그래머
- `seenBookSelectPrimer`와 `renderGrimoireShelf`만 적는다.
- 다른 primer flag 예열은 금지다.

### UI
- `hero-primer 1면 + highlight 2곳 + CTA 비차단`만 적는다.
- shelf 전체 리디자인은 이번 stage 바깥이다.

### 아트
- `primer-library` token sanity만 적는다.
- 신규 대형 자산 필요를 green 조건처럼 쓰지 않는다.

### QA
- `B1-A1~A5 + recovery 3종 + library-only`만 적는다.
- battle helper 검수 예고를 넣지 않는다.

### PM
- `다른 surface 미오픈`, `B-2 unlock 대기`만 적는다.
- green/unlock을 starter manifest에서 먼저 쓰지 않는다.

---

## handoff line quick rule

### 좋은 starter line
> `B-1 library archive-in-progress:` `seenBookSelectPrimer`, hero-primer, highlight 2곳 관련 manifest/stub/semantic anchor 정렬 중이며 battle/reward/shop/ending primer는 아직 열지 않았다.

### 과한 line
> `B-1 green, 다음은 battle 진입 가능`

핵심은 간단하다.
**starter handoff line은 현재 stage를 좁히는 문장이지, 다음 stage를 여는 문장이 아니다.**

---

## stop / go

### GO
- field 5개가 모두 채워져 있다.
- `library-only` boundary가 명시돼 있다.
- `next allowed step`이 아직 작은 단계다.
- handoff line이 honest하다.

### STOP
- status/handoff line에 green이 먼저 들어간다.
- owner summary가 다른 surface를 예열한다.
- boundary가 비어 있다.
- `next allowed step`이 바로 B-2 unlock이다.

---

## immediate next actions
1. `readiness board -> B-1 live kickoff -> B-1 artifact stub -> B-1 starter manifest packet -> B-1 semantic anchor` 순서로 읽는다.
2. archive 생성 직후 manifest 5개 field부터 먼저 채운다.
3. handoff line에는 green이나 unlock을 쓰지 않는다.
4. 그 뒤에만 stub/semantic anchor 메모를 채운다.
5. review-ready / QA pass / PM green / B-2 unlock은 여전히 `required artifacts`와 `review packet` 단계에서만 올린다.
