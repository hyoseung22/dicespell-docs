# DiceSpell `Run Table` Token 구현 갭 레저 요약

## 3줄 요약
- 이 문서는 `Run Atlas / Battle Table / Overrun Oath` 토큰 문서 세트가 충분한 지금, **실제로 아직 안 닫힌 구현 갭이 무엇인지**를 빠르게 읽게 하는 companion이다.
- 새 토큰을 더 설계하는 문서가 아니라, `route node / stakes plaque / oath banner` 중 어디가 아직 hook, density, starter sanity, archive evidence에서 비어 있는지 보여 주는 문서다.
- source-of-truth는 `docs/dicespell_game_flow_token_gap_ledger_kr.md`이고, 이 요약은 PM/UI/아트/QA가 3~5분 안에 같은 결론을 읽도록 돕는다.

## 한 줄 목표
Run Table 토큰 축을 `문서가 많으니 거의 끝난 상태`가 아니라 **surface별 남은 gap을 닫아야 하는 상태**로 다시 읽히게 만든다.

## 왜 이 문서를 먼저 보나
지금 병목은 `무슨 문서를 읽지?`보다 `그래서 실제로 무엇이 아직 안 끝났지?`다.

이미 있는 것:
- surface handoff
- token anchor
- delivery workboard
- closing packet
- evidence manifest
- source-of-truth map

아직 남은 것:
- atlas/battle/oath 각 surface의 live hook lock
- 1440p first-glance closing evidence
- starter asset sanity note
- stage archive evidence

즉 다음 refinement는 새 packet 추가보다 **남은 gap closure**가 우선이다.

---

## first screen 결론
- **문서 blocker는 거의 없다.**
- **실구현 blocker는 남아 있다.**
- 남은 blocker는 surface별로 다르다.
  - Atlas: route state / threshold hook + atlas capture + starter sanity
  - Battle: stakes slot hook + battle capture + plaque sanity
  - Oath: banner/result/carry hook + oath capture + banner/seal sanity

### 한 줄 판정 규칙
`문서가 있다 = 끝`이 아니다. `markup/CSS/review/archive evidence가 있다 = 닫힘`이다.

---

## 구현 갭 한눈표

| surface | 아직 남은 실제 갭 | 닫힘 증거 |
| --- | --- | --- |
| Atlas / route node | `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold`, atlas 1440p hierarchy, starter sanity, atlas archive | DOM hook 목록 + atlas capture + starter note + `atlas-route-node` archive |
| Battle / stakes plaque | `data-run-surface="battle-table"`, `data-stakes-slot`, stakes density, starter sanity, battle archive | DOM hook 목록 + battle capture + starter note + `battle-stakes-plaque` archive |
| Oath / banner | `data-run-surface="overrun-oath"`, `data-oath-role`, `data-overrun-cta`, banner-first hierarchy, starter sanity, oath archive | DOM hook 목록 + oath capture + starter note + `oath-banner` archive |

### 한 줄 해석
세 surface는 같은 `토큰 문서 축` 안에 있지만, **닫아야 하는 gap은 서로 다르다.**

---

## 역할별 빠른 handoff

### 프로그래머
- 새 render family를 만드는 턴이 아니다.
- 지금 있는 live render를 `surface root / state / slot / role hook`으로 잠그는 턴이다.
- helper vocabulary가 drift하지 않게 같은 단어를 유지한다.

### UI
- 새 레이아웃 발명보다 `무엇이 먼저 보여야 하는가`를 닫는 턴이다.
- atlas는 current node와 next threshold, battle은 current stakes, oath는 main banner가 첫 시선이어야 한다.
- prototype board는 reference고, closing 증거는 capture + note다.

### 아트
- 큰 배경이나 전체 shell보다 starter token sanity가 우선이다.
- atlas는 node difference, battle은 plaque difference, oath는 banner/seal difference만 닫아도 충분하다.
- archive에는 key art보다 starter note가 먼저 남아야 한다.

### QA / PM
- `Run Table token 진행` 같은 큰 문장은 금지다.
- 어느 surface gap이 닫혔는지를 evidence 묶음과 함께 기록해야 한다.
- 문서 handoff-ready와 runtime/evidence-ready를 같은 초록불로 쓰지 않는다.

---

## 리스크 & 후속과제

| 리스크 | 이번 대응 |
| --- | --- |
| 문서가 충분해 `거의 끝났다`는 착시가 생기는 위험 | gap ledger로 문서 완료와 실구현 완료를 다시 분리 |
| atlas/battle/oath가 다시 큰 UI pass로 합쳐지는 위험 | surface별 gap과 증거를 따로 고정 |
| tracker/run log에서 큰 green 문장이 다시 생기는 위험 | `어느 surface gap이 닫혔는가` 기준으로 기록 언어를 재정리 |
| starter asset이 큰 자산 논의로 팽창하는 위험 | starter sanity note 중심으로 범위를 다시 좁힘 |

---

## 즉시 다음 액션
1. `token source-of-truth map -> token gap ledger -> token delivery workboard` 순서로 읽는다.
2. atlas/battle/oath 중 **한 surface씩** gap을 선언한다.
3. 같은 surface에 대해 DOM hook, 1440p capture, starter sanity, archive evidence를 함께 닫는다.
4. 기록은 `토큰 작업 진행`이 아니라 `어느 surface gap을 닫았는가`로 남긴다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_delivery_workboard_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
