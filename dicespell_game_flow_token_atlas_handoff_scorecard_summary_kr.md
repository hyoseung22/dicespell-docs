# DiceSpell `Run Table` Atlas Route Node Handoff Scorecard - Summary

## 3줄 요약
- 이 문서는 `atlas-route-node` 첫 실전 턴에서 **누가 먼저 움직이고 누가 마지막 green을 기록하는지**를 빠르게 읽기 위한 readable companion이다.
- 핵심은 새 stage를 더 설계하는 것이 아니라, `manifest는 채웠는데 기록 문장이 섞이는 문제`를 **제출 순서 + owner별 체크 + 복붙 문장**으로 잠그는 것이다.
- atlas stage는 `프로그래머 바닥 제출 -> UI/아트 evidence -> QA candidate -> PM green` 순서로만 닫는다.

## 한 줄 목표
atlas first stage를 `좋아 보이는 archive`가 아니라 **같은 handoff ladder로 닫히는 archive**로 만든다.

## 왜 중요하나
`live kickoff`, `manifest fill`, `required artifacts`, `candidate review`까지 이미 있어도 실제 첫 live turn에서는 여전히 아래가 흔들리기 쉽다.

- capture가 manifest보다 먼저 올라옴
- QA candidate와 PM green이 같은 초록불처럼 기록됨
- tracker/run log/git에 `Run Table 진행`, `거의 완료` 같은 큰 문장이 먼저 들어감
- atlas green 전인데 battle/oath를 같이 열어 버림

이 scorecard는 그 마지막 drift를 막기 위한 운영 문서다.

---

## 첫 화면 결론

### 지금 기억할 것 4개
1. atlas stage는 **프로그래머가 먼저 바닥을 잠근다.**
2. UI/아트 evidence는 그 다음이다.
3. QA는 `archive-ready candidate`까지만 말한다.
4. PM만 `atlas-route-node green, battle-stakes-plaque unlock`을 기록한다.

### 이 문서가 막는 대표 실수
- `manifest가 있으니 거의 green`이라고 쓰는 것
- `overview/focus capture가 나왔으니 candidate겠지`라고 넘기는 것
- `atlas 됐고 battle도 곧` 같은 문장을 먼저 남기는 것

---

## 단계별 core flow

| 단계 | 누가 주도 | 무엇이 잠겨야 하나 | 아직 금지 |
| --- | --- | --- | --- |
| `bootstrap-ready` | 프로그래머 | `manifest.md`, `hook-manifest`, `dom-sanity`, atlas-only boundary | `candidate`, `green`, `battle unlock` |
| `archive-in-progress` | UI / 아트 / 프로그래머 | overview/focus capture, density verdict, starter sanity | `archive-ready`, `green` |
| `archive-ready candidate` | QA | artifact 8종 정렬, wording drift 없음, battle/oath 미개방 확인 | `green` |
| `atlas-route-node green` | PM | 최종 green 기록 + battle-stakes-plaque unlock | `Run Table 진행`, `battle/oath도 가능` |

---

## 역할별 빠른 handoff 포인트

### 프로그래머
- 먼저 잠글 것: `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md`
- 핵심 문장: `atlas-route-node stage는 manifest + hook/check 바닥을 먼저 잠갔고, 아직 battle/oath는 열지 않았다.`
- 금지: capture 먼저 만들기, battle/oath hook까지 같이 적기

### UI
- 제출물: overview capture, threshold focus capture, density note
- 핵심 문장: `route node와 threshold 읽힘까지만 증명한다.`
- 금지: preview/log, battle stakes 강조 장면

### 아트
- 제출물: `assets/starter-asset-list.md`
- 핵심 문장: `Route Node Starter 범위만 sanity 판단한다.`
- 금지: atlas stage에서 전체 지도/공통 프레임 리뉴얼 요구

### QA
- 제출물: candidate verdict
- 핵심 문장: `atlas-route-node archive-ready candidate`
- 금지: `green`, `unlock 완료`

### PM
- 제출물: 최종 green 기록
- 핵심 문장: `atlas-route-node green. 다음 허용 단계는 battle-stakes-plaque archive 생성뿐이다.`
- 금지: `Run Table 진행`, `대체로 완료`, `battle/oath도 같이`

---

## tracker / run log 복붙 문장

| 상황 | 복붙 문장 |
| --- | --- |
| kickoff 직후 | `atlas-route-node archive를 열고 manifest + hook/check 바닥을 먼저 잠갔다.` |
| evidence 수집 중 | `atlas-route-node archive-in-progress: route node/threshold evidence를 atlas-only 범위로 모으는 중이다.` |
| QA candidate | `atlas-route-node archive-ready candidate: artifact 8종과 atlas-only wording 정렬이 확인됐다.` |
| PM green | `atlas-route-node green. battle-stakes-plaque archive 생성만 다음 허용 단계다.` |

---

## 가장 큰 리스크

1. manifest보다 capture가 먼저 올라오는 것
2. QA candidate와 PM green이 같은 초록불로 기록되는 것
3. atlas green 전인데 battle/oath를 암묵적으로 여는 것
4. summary만 읽고 source packet의 금지 범위를 생략하는 것

---

## immediate next actions

1. `source-of-truth map -> gap ledger -> archive bootstrap -> artifact stub -> atlas live kickoff -> manifest fill -> atlas handoff scorecard -> atlas required artifacts -> atlas candidate review` 순서로 읽는다.
2. atlas archive 하나만 연다.
3. 이 문서의 순서대로 `프로그래머 바닥 -> UI/아트 evidence -> QA candidate -> PM green`을 재사용한다.
4. battle/oath는 atlas green 전까지 계속 잠근다.
