# DiceSpell `Run Table` Token Queue Closing 요약

## 3줄 요약
- 이 문서는 `atlas-route-node`, `battle-stakes-plaque`, `oath-banner` stage 문서가 모두 생긴 뒤 마지막으로 남아 있던 **`그러면 세 stage를 어떤 운영 문장으로 닫아야 half-green이 전체 완료처럼 안 보이지?`**를 짧게 읽게 만드는 readable companion이다.
- 핵심은 새 UI를 더 설계하는 것이 아니라, `oath-banner green`과 `Run Table token queue closing`을 다른 문장으로 분리해 tracker / run log / portal / manifest가 같은 뜻을 말하게 만드는 것이다.
- PM / 기획 / QA / 프로그래머 / UI / 아트가 3~5분 안에 `무엇이 stage green인지`, `무엇이 queue closing인지`, `언제 새 reopen packet이 필요한지`를 바로 이해해야 한다.

## 한 줄 목표
`atlas -> battle -> oath` 3-stage 큐를 **stage green 3개 + queue closing 1개** 언어로 닫아, 마지막 기록이 `Run Table complete` 같은 큰 초록불로 부풀지 않게 만든다.

## 왜 이 문서를 먼저 보나
마지막 stage 문서까지 생기면 보통 이렇게 흐르기 쉽다.

- `oath까지 끝났으니 그냥 Run Table 끝이라고 쓰면 되지 않을까?`
- `queue closing도 결국 마지막 green이니까 같은 뜻 아닌가?`
- `다음에 조금 더 다듬을 수도 있으니 일단 complete처럼 적고 나중에 다시 보자`

이 요약 문서는 그 drift를 막기 위해, 마지막 운영 문장을 **무엇을 더 닫는가보다 무엇과 무엇을 절대 같은 뜻으로 쓰면 안 되는가** 기준으로 먼저 읽히게 만든다.

---

## 가장 짧은 정의

### stage green
- `atlas-route-node green`
- `battle-stakes-plaque green`
- `oath-banner green`

이 셋은 각각 **stage 종료 문장**이다.

### queue closing
- `Run Table token queue closing`

이 문장은 **세 stage green과 handoff line, archive path, portal link가 같은 뜻을 말하는지 확인한 뒤**에만 쓰는 운영 종료 문장이다.

### 아직 같은 뜻이 아닌 것
- `Run Table complete`
- `ending green`
- `전 UI 완료`
- `사실상 최종본`

이런 문장은 계속 금지다.

---

## 누가 무엇을 보면 되나

| 역할 | 이번 문서에서 바로 가져갈 결론 |
| --- | --- |
| 프로그래머 | 내 closing 문장은 stage green 전까지다. queue closing을 위해 새 code/CSS edit를 끼워 넣지 않는다 |
| UI | overview/focus/density note는 stage evidence다. queue closing은 visual total-green 선언이 아니다 |
| 아트 | starter sanity는 stage green 근거이고, queue closing은 새 art request를 뜻하지 않는다 |
| QA | `oath-banner green` 뒤에도 한 번 더 `queue-closing review-ready`를 따로 올려야 한다 |
| PM/기획 | 마지막 허용 문장은 `Run Table token queue closing`뿐이다. 필요하면 그 다음에만 새 reopen packet을 연다 |

---

## 마지막 signoff 순서

| 단계 | 누가 적나 | 의미 |
| --- | --- | --- |
| `oath-banner green` | PM/기획 | 마지막 stage가 stage명 기준으로 닫혔다 |
| `queue-closing review-ready` | QA | atlas/battle/oath green 문장, manifest, 링크가 정렬됐다 |
| `Run Table token queue closing` | PM/기획 | 마지막 운영 closing을 같은 문장으로 기록한다 |
| `follow-up reopen` | PM/기획 + stage owner | 새 bounded packet이 필요할 때만 다시 연다 |

---

## 복붙용 허용 문장
- `oath-banner green, queue-closing review-only`
- `Run Table token queue closing: atlas-route-node / battle-stakes-plaque / oath-banner stage green과 handoff line, archive path, portal source link alignment 확인.`
- `Run Table token queue closing: follow-up reopen은 새 bounded packet이 있을 때만 연다.`

## 금지 문장
- `Run Table complete`
- `ending green`
- `전 UI 완료`
- `토큰 작업 끝`
- `사실상 최종본`

---

## 리스크 & 후속과제
- `oath-banner green`을 곧바로 전체 완료로 적으면 마지막 stage 경계가 무너진다.
- tracker / run log / portal / manifest가 다른 closing 문장을 쓰면 다음 자동화가 상태를 다시 추측하게 된다.
- queue closing 시점에 새 capture나 CSS edit를 끼워 넣으면 closing review가 다시 stage 확장으로 바뀐다.
- 후속 refinement가 필요할 때 기존 green을 덮어쓰지 말고, 새 bounded packet 이름으로만 reopen을 기록해야 한다.

---

## immediate next actions
1. `token refinement queue -> oath stage packet -> queue closing packet` 순서로 읽는다.
2. atlas / battle / oath green 문장이 모두 stage명 기준으로 있는지 확인한다.
3. QA가 `queue-closing review-ready`를 남긴다.
4. PM이 그 뒤에만 `Run Table token queue closing`을 같은 wording으로 tracker / run log / portal에 남긴다.
5. 후속 refine가 필요하면 `follow-up reopen`을 새 packet 이름으로만 기록한다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **`마지막 stage green`과 `마지막 운영 closing`을 같은 뜻으로 쓰지 못하게 막는 것**이다. Run Table token 축은 이제 `atlas`, `battle`, `oath`를 닫는 문서에서 한 단계 더 내려가, 그 세 stage를 어떤 문장으로 정리하고 어떤 경우에만 다시 bounded하게 열지까지 같은 언어로 기록할 수 있게 됐다.
