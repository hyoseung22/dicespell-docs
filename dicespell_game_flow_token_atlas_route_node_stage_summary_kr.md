# DiceSpell `Run Table` Atlas Route Node 첫 스테이지 요약

## 3줄 요약
- 이 문서는 `atlas-route-node` 첫 실제 refinement 턴을 **`route node hook 잠금 + threshold 읽힘 검수 + starter asset sanity + atlas archive-ready candidate`**까지만 좁히는 readable companion이다.
- 핵심은 새 토큰을 더 설계하는 것이 아니라, `battle-stakes-plaque`와 `oath-banner`를 아직 열지 않은 채 atlas stage 하나만 한 archive와 한 green 문장으로 닫게 만드는 것이다.
- PM / 기획 / UI / 아트 / QA가 3~5분 안에 `왜 atlas가 첫 stage인지`, `무엇이 이번 턴 완료선인지`, `어떤 문장 전에는 battle을 열면 안 되는지`를 바로 이해해야 한다.

## 한 줄 목표
첫 Run Table refinement 턴을 `atlas only`로 고정해, 큐와 ownership 문서가 실제 stage 실행으로 이어지게 만든다.

## 왜 이 문서를 먼저 보나
queue packet과 archive bootstrap packet이 있어도 실제 첫 atlas 턴 직전엔 보통 이렇게 흐르기 쉽다.

- `atlas도 하고 battle도 조금 보면 되지 않을까?`
- `overview 캡처만 있으면 거의 된 거 아닌가?`
- `starter asset sanity가 부족하니 이번에 큰 리디자인 얘기도 같이 열까?`

이 요약 문서는 그 drift를 막기 위해, atlas stage를 **무엇을 하는 턴인지보다 무엇을 아직 하지 않는 턴인지** 기준으로 먼저 읽히게 만든다.

---

## 이번 stage의 가장 짧은 정의

### 이번에 닫는 것
- `Run Atlas` root / route node state / threshold hook
- `atlas-1440-overview.png`
- `atlas-1440-threshold-focus.png`
- `Route Node Starter` sanity note
- atlas stage archive와 handoff line 초안

### 이번에 아직 안 닫는 것
- battle stakes slot polish
- oath banner / result / carry hierarchy
- preview/log panel 대개편
- 지도 배경 전체 리뉴얼
- Run Table 전체 green 문장

### 한 줄 판정
atlas stage는 `Run Atlas가 핀 보드처럼 먼저 읽히는가`를 닫는 턴이지, Run Table 전체를 다듬는 턴이 아니다.

---

## 누가 무엇을 보면 되나

| 역할 | 이번 문서에서 바로 가져갈 결론 |
| --- | --- |
| 프로그래머 | atlas live edit은 `data-run-surface="atlas"`, `data-route-node-state`, `data-route-threshold`까지만 잠근다 |
| UI | `overview`와 `threshold-focus` 두 캡처만으로 first-glance verdict를 남긴다 |
| 아트 | `Route Node Starter` sanity만 본다. 큰 배경/공통 frame 재설계는 아직 금지다 |
| QA | required artifacts가 stage archive 안에 다 모였을 때만 `archive-ready candidate`를 쓴다 |
| PM/기획 | `atlas-route-node green` 전에는 battle unlock을 쓰지 않는다 |

---

## 이번 stage evidence 묶음
- `docs/artifacts/run-table-token-delivery/atlas-route-node/manifest.md`
- `captures/atlas-1440-overview.png`
- `captures/atlas-1440-threshold-focus.png`
- `notes/hook-manifest.md`
- `notes/density-review.md`
- `notes/handoff-line.md`
- `checks/dom-sanity.md`
- `assets/starter-asset-list.md`

이 묶음이 stage archive 안에 같은 이름으로 있어야 QA가 후보 green을 말할 수 있다.

---

## stage signoff 한눈표

| 단계 | 누가 적나 | 의미 |
| --- | --- | --- |
| `bootstrap-ready` | 프로그래머 | atlas 폴더 + manifest + starter file set이 생겼다 |
| `archive-in-progress` | 프로그래머 / UI | 첫 hook note 또는 첫 capture가 들어왔다 |
| `archive-ready candidate` | QA | atlas required artifacts가 다 채워졌다 |
| `atlas-route-node green` | PM/기획 | QA 확인까지 끝나 battle을 열 수 있다 |

---

## 리스크 & 후속과제
- atlas stage가 다시 `preview/log도 같이`, `battle도 같이`로 커지면 첫 큐부터 무너진다.
- overview/focus 캡처가 atlas stage archive 밖 임시 경로에 있으면 green 후보로 보지 않는다.
- starter asset sanity를 큰 리디자인 제안으로 확장하면 atlas green이 불필요하게 늦어진다.
- `atlas green`을 `Run Table green`처럼 기록하면 battle/oath unlock 규칙이 무너진다.

---

## immediate next actions
1. `token source-of-truth map -> token gap ledger -> token archive bootstrap -> token refinement queue -> atlas stage packet` 순서로 읽는다.
2. atlas stage archive를 먼저 만든다.
3. hook note / DOM sanity / overview/focus capture / starter asset sanity를 채운다.
4. QA가 `archive-ready candidate`를 적기 전에는 battle/oath를 열지 않는다.
5. PM은 `atlas-route-node green` 문장만 남기고 그 다음에만 battle unlock을 연다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **`atlas가 첫 stage라는 사실`을 `atlas만 닫는 실제 실행 규칙`으로 바꾸는 것**이다. 다음 Run Table refinement 턴은 이 stage packet 기준으로 route node와 threshold 읽힘만 닫고, battle/oath는 아직 대기 상태로 남겨야 한다.
