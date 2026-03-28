# DiceSpell `Run Table` Token Queue Signoff 요약

## 3줄 요약
- 이 문서는 `queue readiness board`, `queue closing packet`, `atlas/battle/oath handoff scorecard`까지 충분한 상태에서도 끝까지 남아 있던 **`좋아, 그러면 마지막으로 누가 review-ready를 올리고 누가 closing을 쓰지?`**를 짧게 읽게 만드는 readable companion이다.
- 핵심은 새 stage를 더 설계하는 것이 아니라, `stage green`, `queue-closing review-ready`, `Run Table token queue closing`, `follow-up reopen`의 **권한 주체**를 분리해 마지막 운영선에서도 같은 초록불이 반복되지 않게 만드는 것이다.
- PM / QA / 프로그래머 / UI / 아트가 3~5분 안에 `누가 어디까지 말할 수 있는가`를 바로 이해해야 한다.

## 한 줄 목표
Run Table token 마지막 운영선을 **제출자 / QA 검토자 / PM 기록자 / reopen 소유자**로 나눠, `oath-banner green`과 `Run Table token queue closing`이 다시 같은 뜻으로 소비되지 않게 만든다.

## 왜 이 문서를 먼저 보나
문서가 충분할수록 마지막엔 오히려 이런 오해가 생기기 쉽다.

- `oath까지 끝났으니 PM이 closing도 같이 쓰면 되지 않을까?`
- `QA가 review-ready라고 했으면 사실상 green 아닌가?`
- `follow-up이 조금 남았어도 기존 green은 지우고 다시 적으면 되지 않을까?`

이 요약 문서는 그 drift를 막기 위해, 마지막 운영선을 **무엇을 더 만들까보다 누가 어디까지 말할 수 있나** 기준으로 먼저 읽히게 만든다.

---

## 가장 짧은 정의

### 제출자
- 프로그래머 / UI / 아트
- stage archive, hook/check/capture, hierarchy/starter sanity evidence를 낸다
- `green`이나 `queue closing`은 쓰지 않는다

### 동반 검토자
- QA
- `archive-ready candidate`, `queue-closing review-ready`, unlock 가능 여부를 판정한다
- PM green을 대신 쓰지 않는다

### 마지막 기록자
- PM/기획
- `<stage> green`, `Run Table token queue closing`, `follow-up reopen`을 기록한다
- 단, QA review-ready 없이 다음 문장을 먼저 쓰지 않는다

---

## 마지막 signoff 순서

| 단계 | 누가 적나 | 의미 |
| --- | --- | --- |
| stage evidence drop | 프로그래머 / UI / 아트 | stage를 닫기 위한 입력이 모였다 |
| `archive-ready candidate` | QA | candidate 검토선이 열렸다 |
| `<stage> green` | PM/기획 | 해당 stage가 stage명 기준으로 닫혔다 |
| `queue-closing review-ready` | QA | atlas/battle/oath green, manifest, 링크가 정렬됐다 |
| `Run Table token queue closing` | PM/기획 | 마지막 운영 closing을 기록한다 |
| `follow-up reopen` | PM/기획 + 해당 stage owner | 새 bounded packet으로만 다시 연다 |

---

## 복붙용 허용 문장
- `atlas-route-node green, battle-stakes-plaque unlock`
- `battle-stakes-plaque green, oath-banner unlock`
- `oath-banner green, queue-closing review-only`
- `Run Table token queue closing: atlas-route-node / battle-stakes-plaque / oath-banner stage green과 handoff line, archive path, portal source link alignment 확인.`
- `Run Table token follow-up reopen: <bounded-packet-name> only, previous atlas/battle/oath green 유지.`

## 금지 문장
- `Run Table complete`
- `ending green`
- `전 UI 완료`
- `토큰 작업 끝`
- `사실상 최종본`

---

## 리스크 & 후속과제
- QA `review-ready`와 PM `queue closing`이 다시 같은 초록불로 기록되면 마지막 운영선이 무너진다.
- follow-up reopen을 기존 green 취소처럼 적으면 audit trail이 흐려진다.
- queue closing 시점에 새 edit를 끼워 넣으면 closing review가 다시 stage 확장으로 바뀐다.
- summary / exemplar / packet을 ownership 없이 섞어 읽으면 누가 stop line을 가진 문서인지 다시 흐려진다.

---

## immediate next actions
1. `queue readiness board -> queue closing packet -> queue signoff ladder` 순서로 읽는다.
2. `oath-banner green` 뒤에는 QA가 먼저 `queue-closing review-ready`를 올린다.
3. PM은 그 뒤에만 `Run Table token queue closing`을 기록한다.
4. follow-up이 필요하면 기존 green을 지우지 않고 새 bounded packet으로만 reopen을 연다.
5. tracker / run log / portal / manifest / git에는 허용 문장만 재사용한다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **마지막 운영선에서 누가 어디까지 말할 수 있는지**를 다시 잠가, `oath-banner green`, `queue-closing review-ready`, `Run Table token queue closing`, `follow-up reopen`이 서로 다른 ownership 문장으로 끝까지 유지되게 만드는 것이다.
