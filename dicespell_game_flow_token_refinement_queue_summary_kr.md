# DiceSpell `Run Table` Token 리파인먼트 큐 요약

## 3줄 요약
- 이 문서는 `Run Table` token refinement를 `atlas-route-node -> battle-stakes-plaque -> oath-banner` 순서로만 여는 queue 요약이다.
- 핵심은 새 토큰을 더 설계하는 것이 아니라 **stage 1개 = archive 1개 = green 문장 1개** 원칙을 고정하는 것이다.
- `bootstrap-ready`, `archive-ready`, `stage green`은 같은 사람이 같은 문장으로 쓰면 안 된다.

## 한 줄 목표
Run Table token refinement가 다시 큰 UI polish나 큰 초록불로 뭉개지지 않게, **어느 stage를 먼저 열고 누가 다음 stage를 열어 주는지**를 빠르게 판정한다.

## 왜 이 문서가 필요한가
지금은 `anchor`, `closing`, `evidence manifest`, `archive bootstrap`까지 이미 충분하다.

남아 있던 마지막 공백은 이것이었다.
- 그래서 실제 첫 stage는 무엇이지?
- bootstrap-ready는 누가 쓰지?
- archive-ready는 누가 확인하지?
- 다음 stage는 언제 열 수 있지?

이 요약의 결론은 단순하다.
**atlas를 먼저 닫고, battle을 그다음에 열고, oath는 마지막에 연다.**

---

## first screen 결론
- 첫 stage는 `atlas-route-node`다.
- 둘째 stage는 `battle-stakes-plaque`다.
- 마지막 stage는 `oath-banner`다.
- `bootstrap-ready`는 stage driver가 starter bundle을 만든 뒤에만 쓴다.
- `archive-ready`는 QA 확인 전에는 candidate일 뿐이다.
- `stage green`은 PM이 QA unlock 뒤에만 기록한다.

---

## canonical queue

| 순서 | stage | 지금 이 순서가 맞는 이유 |
| --- | --- | --- |
| 1 | atlas-route-node | route state / threshold / board first-glance를 가장 작게 닫아 archive discipline을 먼저 굳히기 좋다 |
| 2 | battle-stakes-plaque | atlas green 뒤 같은 capture naming과 signoff 언어로 slot hierarchy를 닫기 좋다 |
| 3 | oath-banner | ending/reward/carry 경계가 가장 민감하므로 가장 마지막에 열어 drift를 줄인다 |

### 한 줄 규칙
한 stage가 `green`이 되기 전에는 다음 stage를 열지 않는다.

---

## 상태 ownership

| 상태 | 누가 적나 | 뜻 |
| --- | --- | --- |
| bootstrap-ready | stage driver(기본은 프로그래머) | 폴더 + manifest + starter file set 준비 완료 |
| archive-in-progress | stage driver + UI/아트/QA note | evidence가 stage archive에 채워지는 중 |
| archive-ready candidate | QA | required artifacts가 closing 기준으로 충족됨 |
| stage green | PM/기획 | QA unlock 뒤 다음 stage를 열 수 있음 |

### 금지
- `Run Table 거의 완료`
- `토큰 정리 완료`
- `atlas/battle/oath 진행중`

---

## stage별 handoff 포인트

### atlas-route-node
- 지금 바로 열 수 있는 첫 stage다.
- route hook, threshold focus capture, starter asset sanity만 닫는다.
- battle/oath는 아직 열지 않는다.

### battle-stakes-plaque
- atlas green 뒤에만 연다.
- `current / next / grimoire` slot hierarchy만 닫는다.
- battle action panel 대개편은 금지다.

### oath-banner
- battle green 뒤 마지막에만 연다.
- `banner / result / carry` role과 CTA boundary만 닫는다.
- overrun/ending boundary 재설계는 금지다.

---

## 리스크 & 후속과제
- 가장 큰 리스크는 atlas/battle/oath를 동시에 조금씩 열어 stage archive와 green 문장이 다시 섞이는 것이다.
- 두 번째 리스크는 `bootstrap-ready`와 `archive-ready`를 같은 사람이 같은 초록불처럼 기록하는 것이다.
- 세 번째 리스크는 oath를 너무 일찍 열어 Run Table refinement가 다시 경계 재설계로 커지는 것이다.

---

## immediate next actions
1. 다음 착수 전 `token source-of-truth map -> token gap ledger -> token archive bootstrap -> token refinement queue` 순서로 읽는다.
2. 첫 stage는 반드시 `atlas-route-node`로만 연다.
3. atlas green 뒤에만 battle을 연다.
4. battle green 뒤에만 oath를 연다.
5. tracker/run log/public portal에는 stage 이름이 들어간 green 문장만 남긴다.

---

## 한 장 결론
Run Table token 축의 마지막 작은 공백은 **`무엇을 먼저 열고 누가 다음 stage를 열어 주는가`**였다. 이 요약은 그 공백을 `atlas -> battle -> oath` queue와 `bootstrap-ready -> archive-ready -> stage green` ownership으로 짧게 고정해, 다음 refinement가 다시 큰 덩어리 작업으로 흐르지 않게 만든다.
