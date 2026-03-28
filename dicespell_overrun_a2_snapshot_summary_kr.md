# DiceSpell `overrunEvent` A-2 snapshot/normalize 요약

## 이번 문서가 닫는 공백

A-1 문서로 `continueOverrun()`를 `overrunEvent` 진입 wrapper로 줄이는 첫 커밋 범위는 이미 닫혔다. 그런데 그 다음 구현 직전에는 더 작은 공백이 남는다.

> `좋아, phase는 분리됐어. 그런데 save/load를 다시 열었을 때도 slime/goblin/lich/golem/collapse/none branch가 같은 표정으로 살아 있으려면 snapshot에 정확히 무엇이 들어 있어야 하지?`

이번 문서 `docs/dicespell_overrun_a2_snapshot_packet_kr.md`는 바로 이 질문에 답한다.

---

## 핵심 결론

A-2부터 `overrunEvent` snapshot은 단순히 `sourceType`만 저장하는 얇은 state가 아니다. 아래 display contract를 **엔진이 먼저 canonical field set으로 저장**한다.

- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `flavorText`
- `confirmLabel`
- `rewardId` / `rewardTitle` / `rewardDescription` / `rewardTag`
- `resolved`

즉 UI는 render 시점에 title 문자열이나 `sourceType`로 branch를 다시 추론하지 않는다. snapshot이 이미 exact branch identity를 들고 있어야 한다.

---

## 왜 이게 중요한가

이 단계가 없으면 다음 문제가 바로 생긴다.

1. save/load 후 branch 톤이 drift한다.
2. `themeTrophy` 안에서 슬라임/고블린/리치를 다시 title 문자열로 판정하게 된다.
3. invalid reward나 legacy save를 열었을 때 `none fallback`이 문서마다, 코드마다 다르게 생긴다.
4. A-3 중앙 카드 UI가 화면 레이아웃 작업이 아니라 branch 추론기까지 떠안게 된다.

A-2는 이 drift를 미리 끊는 단계다.

---

## owner별 handoff 포인트

### 프로그래머
- `buildCanonicalOverrunEventSnapshot()`와 `normalizeOverrunEventState()`를 만든다.
- `contentKey`는 엔진에서 확정한다.
- legacy / invalid / missing payload는 `none` 또는 canonical branch로 안정 수렴시킨다.

### UI
- `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`은 snapshot을 그대로 읽는다.
- render 단계에서 branch를 재판정하지 않는다.

### 아트
- 최소 6종 토큰(`theme-slime`, `theme-goblin`, `theme-lich`, `enemy-golem`, `enemy-collapse`, `none-neutral`)을 같은 identity 축으로 읽는다.
- `none`도 오류 상태가 아니라 정상 neutral branch로 취급한다.

### QA
- 생성 3종(slime / golem / none)
- recovery 3종(legacy / invalid / reload)
- 총 A2-S1~S6 smoke로 drift를 잡는다.

---

## A-2 완료선

A-2는 아래 세 가지가 동시에 성립해야 닫힌다.

1. `overrunEvent` snapshot이 canonical field set을 저장한다.
2. reload/legacy/invalid/missing 상태가 같은 규칙으로 normalize된다.
3. A-3 UI는 더 이상 branch 추론 없이 snapshot display-only 소비만 하면 된다.

---

## 다음 작업

A-2가 닫히면 다음 A-3는 훨씬 단순해진다. 그때부터는 중앙 카드 1장 UI를 `badge → header → flavor → reward summary → CTA` 위계로 배치하는 화면 handoff에 집중하면 된다.

즉 이번 문서는 새 화면을 그리는 문서가 아니라,
**다음 화면 작업이 branch 추론 없이 안전하게 시작되도록 payload를 고정하는 문서**다.
