# DiceSpell `Run Table` Battle Stakes Plaque semantic anchor summary

## 3줄 요약
- 이 문서는 `battle live kickoff` 뒤 실제 live edit 직전에 남아 있던 마지막 locator 공백, 즉 **`좋아, battle에서 정확히 어느 함수 / DOM / 캡처 질문을 같은 책임으로 다시 찾아야 하지?`**를 빠르게 읽게 만드는 readable companion이다.
- 결론은 간단하다. battle semantic anchor는 `surface root / stakes slot state / hierarchy pressure / battle boundary / capture proof` 5축이고, `renderBattle(summary)` / `.battle-stakes-grid` / `[data-stakes-slot="current|next|grimoire"]`까지만 다시 찾는다.
- action panel / dice tray / log / atlas / oath를 같이 여는 순간 stage drift다. semantic anchor 재확인은 green이 아니라 **kickoff layer**다.

**한 줄 목표:** battle 첫 live edit 전에 `무엇을 다시 찾고 무엇을 아직 안 여는지`를 한 화면으로 잠가, `battle-stakes-plaque`가 다시 `Battle Table 전체 polish`로 부풀지 않게 만든다.

---

## 누구를 위한 문서인가
- **프로그래머:** `renderBattle(summary)`와 stakes zone만 다시 찾고 action panel/HUD 전체 재설계는 열지 않기 위해
- **UI:** `current > next > grimoire` first-glance와 stakes focus만 검수하고 battle 전체 미감 회고로 번지지 않기 위해
- **아트:** `Stakes Plaque Starter` sanity만 확인하고 battle HUD 전체 리뉴얼 논의로 커지지 않기 위해
- **QA/PM:** semantic anchor 메모와 green 문장을 다른 층위로 기록하기 위해

---

## 왜 지금 이 문서가 필요한가
이미 battle 축에는 아래 문서가 있다.
- `battle stakes stage packet`
- `battle live kickoff packet`
- `battle required artifacts packet`
- `battle candidate review packet`
- `manifest fill packet`
- `source-of-truth map`
- `gap ledger`

그런데도 실제 live edit 직전에는 마지막 작은 공백이 남는다.

> `좋아, kickoff 순서와 required artifacts는 알겠어. 그런데 실제 코드를 열기 직전에 정확히 어느 locator를 같은 책임으로 다시 찾아야 하지?`

이 공백이 남아 있으면 battle은 가장 쉽게 `이 김에 action panel도`, `dice tray도`, `log도`로 커진다. 이 summary는 그 drift를 막기 위한 battle-only locator 가이드다.

---

## first screen 결론

### battle semantic anchor 5축
1. **surface root**
   - `renderBattle(summary)`
   - `.battle-table`
   - `data-run-surface="battle-table"`
2. **stakes slot state**
   - `.battle-stakes-grid`
   - `.stakes-card`
   - `[data-stakes-slot="current|next|grimoire"]`
3. **hierarchy pressure**
   - current 강조
   - next / grimoire 보조 관계
   - stakes title / value / emphasis zone
4. **battle boundary**
   - `.battle-actions`
   - `.dice-tray`
   - `.log-panel`
   - 이들은 이번 stage 비범위
5. **capture proof**
   - `captures/battle-1440-overview.png`
   - `captures/battle-1440-stakes-focus.png`
   - `notes/density-review.md`

### 가장 짧은 답
- battle에서 다시 찾는 것은 `stakes plaque`다.
- 같이 열면 안 되는 것은 `action panel / dice tray / log / atlas / oath`다.
- semantic anchor 재확인은 green이 아니라 **kickoff 메모**다.

---

## battle에서 실제로 무엇을 다시 찾는가

| anchor family | 지금 다시 찾는 이유 | 같이 열면 안 되는 것 |
| --- | --- | --- |
| surface root | battle stage scope를 stakes plaque까지 좁히기 위해 | atlas/oath/ending root helper |
| stakes slot | current / next / grimoire를 같은 vocabulary로 고정하기 위해 | generic stat card, action panel slot |
| hierarchy | `current > next > grimoire` first-glance를 잠그기 위해 | action panel / dice tray / log density 재설계 |
| boundary | 비범위를 note에 먼저 박기 위해 | action panel helper 재정리, dice tray spacing pass, log tone 정리 |
| capture proof | overview / stakes-focus 질문을 증거로 남기기 위해 | generic combat glamour shot |

---

## 역할별 handoff 핵심

### 프로그래머
- `notes/hook-manifest.md`에 먼저 `root / stakes slot / hierarchy / boundary / non-change` 5줄을 적는다.
- line number보다 semantic family가 계약이다.
- action panel / dice tray / log 구조는 찾더라도 이번 turn엔 닫아 둔다.

### UI
- `notes/density-review.md`에 overview / stakes-focus 질문부터 적는다.
- overview는 `current stakes가 먼저 읽히는가`, focus는 `current / next / grimoire가 같은 family로 읽히는가`만 답한다.

### 아트
- `Stakes Plaque Starter` 충분성만 본다.
- battle HUD 전체 리뉴얼, oath/ending 공용 frame 제안은 이번 stage 바깥이다.

### QA / PM
- semantic anchor 메모는 kickoff 층위다.
- `archive-ready candidate`, `battle-stakes-plaque green`, `oath-banner unlock`은 evidence 뒤에만 쓴다.
- `Battle Table 진행`, `전투 polish 계속`, `Run Table 거의 완료` 같은 큰 문장은 금지다.

---

## 캡처 질문 2개만 기억하면 된다

| capture | 질문 | fail 신호 |
| --- | --- | --- |
| `battle-1440-overview.png` | current stakes가 action panel / dice tray / log보다 먼저 읽히는가? | action panel이나 주사위가 먼저 눈에 들어옴 |
| `battle-1440-stakes-focus.png` | current / next / grimoire 관계가 한 장에서 같은 언어로 읽히는가? | current만 튀고 next/grimoire가 다른 카드처럼 흩어짐 |

---

## 지금 바로 써먹는 stop / go

### GO
- archive / stub가 이미 있다.
- hook note가 battle root와 stakes slot만 다룬다.
- density note가 overview / stakes-focus 두 질문만 적는다.
- PM note가 `oath unlock only after battle green`을 유지한다.

### STOP
- action panel / dice tray / log를 이번 stage 안으로 끌어오려는 메모가 나온다.
- atlas/oath helper가 note에 섞인다.
- 캡처가 generic combat shot이 된다.
- starter sanity가 battle shell 리뉴얼 논의로 커진다.

---

## immediate next actions
1. 다음 battle turn은 `source-of-truth map -> gap ledger -> battle live kickoff -> battle semantic anchor packet` 순서로 읽는다.
2. `hook-manifest.md`에 `root / stakes slot / hierarchy / boundary / non-change`를 먼저 적는다.
3. `density-review.md`에 overview / stakes-focus 질문을 먼저 적는다.
4. 그 다음에만 live edit와 evidence drop으로 들어간다.
5. semantic anchor 메모가 있어도 green은 아니다. candidate/green은 여전히 evidence bundle 뒤에서만 쓴다.

---

## 한 장 결론
지금 battle 축의 남은 공백은 `문서가 부족하다`가 아니라 **`좋아, 실전 edit 직전에 정확히 어디까지를 battle stakes plaque라고 다시 찾아야 하지?`**였다. 이 summary는 그 답을 `surface root / stakes slot / hierarchy / boundary / capture proof` 5축으로 묶어, 다음 live turn이 다시 `Battle Table 전체 polish`로 부풀지 않게 만드는 빠른 읽기용 battle semantic anchor 가이드다.
