# DiceSpell `overrunEvent` 첫 런타임 전달 패킷 요약

## 3줄 요약
- 이 문서는 `docs/dicespell_overrun_first_runtime_delivery_packet_kr.md`의 읽기용 companion이다.
- 핵심은 새 설계가 아니라, **첫 실제 코드 턴을 어떤 전달 묶음으로 닫을지**를 3~5분 안에 파악하게 만드는 것이다.
- 프로그래머는 `한 커밋에 무엇을 같이 묶는가`를, QA는 `무엇이 아직 안 바뀌어야 하는가`를, PM은 `이번 턴이 어디까지 닫혔는가`를 먼저 보면 된다.

**한 줄 목표:** `overrunEvent` first runtime slice를 **실행 가능한 첫 커밋 + 리뷰 가능한 첫 증거 묶음**으로 다시 압축한다.

---

## 이 문서가 왜 있나
지금 DiceSpell에는 이미 `runway`, `runtime patch map`, `first runtime slice`, `state matrix`, `review packet`, `A-1`, `A-2` 문서가 있다. 부족한 건 설계가 아니라, 이 문서들을 실제 첫 턴 handoff로 **어떻게 묶어 넘길지**였다.

이 요약은 그 공백만 짧게 메운다.

---

## 이번 턴에 실제로 닫는 것
### 포함
- `continueOverrun()` enter wrapper화
- canonical snapshot helper / normalize helper
- `phase='overrunEvent'` 최소 explicit branch
- enter / canonical / legacy / missing / invalid / none smoke
- 리뷰용 evidence bundle

### 제외
- reward 적용
- `currentPage` 증가
- `pagePlan` 확장
- `segmentTrophyTemplateIds` 초기화
- 중앙 카드 UI polish
- confirm resolve
- styles 작업

즉 이번 턴은 **장면을 저장 가능한 상태로 만드는 턴**이지, **장면을 예쁘게 보여 주거나 실제로 넘기는 턴**이 아니다.

---

## 파일별로 보면 더 단순하다
- `src/game-engine.js`: enter + canonical snapshot + normalize
- `src/app.js`: 최소 branch만
- `scripts/smoke-test.mjs`: enter/recovery smoke
- `src/styles.css`: 건드리지 않음

커밋도 이 셋만 묶는 게 맞다.

---

## 리뷰에서 꼭 같이 말해야 하는 문장
아래 문장을 tracker / run log / 리뷰 코멘트에서 그대로 써도 된다.

> 이번 턴은 `ending -> overrunEvent` 진입과 canonical snapshot 저장/복구까지만 닫았고, reward 적용·page progression·중앙 카드 UI polish·confirm resolve는 아직 열지 않았다.

이 문장이 중요한 이유는, `무엇이 됐는가`와 `무엇이 아직 안 됐는가`를 같이 말하기 때문이다.

---

## 역할별로 보면
### 프로그래머
여러 문서를 다시 섞지 말고, 한 커밋에 `game-engine.js + app.js 최소 branch + smoke-test.mjs`만 묶는다.

### QA
smoke green만 보면 안 된다. reward 미적용, `currentPage/pagePlan/segmentTrophyTemplateIds` 미변경까지 같이 확인해야 pass다.

### UI
이번 턴은 구현 대기다. `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`이 freeze됐는지만 보면 된다.

### 아트
이번 턴은 asset 제작이 아니라 `artTokenSet` freeze 확인 턴이다. `none`도 정상 branch인지 확인하면 충분하다.

### PM/기획
이번 턴은 `첫 장면의 존재를 안전하게 저장하는 턴`이다. `장면을 완성하는 턴`도, `다음 세그먼트로 넘기는 턴`도 아니다.

---

## 바로 다음 턴은 무엇인가
이 전달 묶음이 pass면 다음 선택지는 둘 중 하나뿐이다.

1. `A-3 UI`: 중앙 카드 1장 shell
2. `A-4 resolve`: confirm CTA와 1회 적용 규칙

fail이면 새 문서를 더 쓰지 말고, first runtime slice 범위로 되돌린다.

---

## 한 줄 결론
이 문서는 `overrunEvent` first runtime slice를 **문서 세트가 아니라 실제 첫 handoff packet**으로 다시 묶어, 다음 구현과 리뷰가 같은 커밋 경계에서 시작되게 만든다.
