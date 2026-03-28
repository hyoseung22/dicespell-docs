# DiceSpell `Run Table` Battle Stakes Plaque Handoff Scorecard - Summary

## 3줄 요약
- 이 문서는 `battle-stakes-plaque` 첫 실전 턴에서 **누가 먼저 움직이고 누가 마지막 green을 기록하는지**를 빠르게 읽기 위한 readable companion이다.
- 핵심은 새 stage를 더 설계하는 것이 아니라, `artifact는 모였는데 기록 문장과 owner 순서가 섞이는 문제`를 **제출 순서 + owner별 체크 + 복붙 문장**으로 잠그는 것이다.
- battle stage는 `프로그래머 바닥 제출 -> UI/아트 evidence -> QA candidate -> PM green` 순서로만 닫는다.

## 한 줄 목표
battle first stage를 `좋아 보이는 archive`가 아니라 **같은 handoff ladder로 닫히는 archive**로 만든다.

## 왜 중요하나
`live kickoff`, `semantic anchor`, `archive file map`, `filled archive examples`, `required artifacts`, `candidate review`까지 이미 있어도 실제 첫 live turn에서는 여전히 아래가 흔들리기 쉽다.

- capture가 manifest보다 먼저 올라옴
- QA candidate와 PM green이 같은 초록불처럼 기록됨
- tracker/run log/git에 `Battle Table 진행`, `전투는 거의 완료` 같은 큰 문장이 먼저 들어감
- battle green 전인데 oath/queue를 같이 열어 버림

이 scorecard는 그 마지막 drift를 막기 위한 운영 문서다.

---

## 첫 화면 결론

### 지금 기억할 것 4개
1. battle stage는 **프로그래머가 먼저 바닥을 잠근다.**
2. UI/아트 evidence는 그 다음이다.
3. QA는 `archive-ready candidate`까지만 말한다.
4. PM만 `battle-stakes-plaque green, oath-banner unlock`을 기록한다.

### 이 문서가 막는 대표 실수
- `manifest가 있으니 거의 green`이라고 쓰는 것
- `overview/stakes-focus capture가 나왔으니 candidate겠지`라고 넘기는 것
- `battle 됐고 oath도 곧` 같은 문장을 먼저 남기는 것

---

## 단계별 core flow

| 단계 | 누가 주도 | 무엇이 잠겨야 하나 | 아직 금지 |
| --- | --- | --- | --- |
| `archive-in-progress` 바닥 | 프로그래머 | `manifest.md`, `hook-manifest`, `dom-sanity`, battle-only boundary | `candidate`, `green`, `oath unlock` |
| `archive-in-progress` evidence | UI / 아트 / 프로그래머 | overview/focus capture, density verdict, starter sanity | `archive-ready`, `green` |
| `archive-ready candidate` | QA | artifact 8종 정렬, wording drift 없음, oath 미개방 확인 | `green` |
| `battle-stakes-plaque green` | PM | 최종 green 기록 + oath-banner unlock | `Battle Table 진행`, `oath/queue도 가능` |

---

## 역할별 빠른 handoff 포인트

### 프로그래머
- 먼저 잠글 것: `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md`
- 핵심 문장: `battle-stakes-plaque stage는 manifest + hook/check 바닥을 먼저 잠갔고, action panel/log/oath는 아직 열지 않았다.`
- 금지: capture 먼저 만들기, action panel/log/oath hook까지 같이 적기

### UI
- 제출물: overview capture, stakes-focus capture, density note
- 핵심 문장: `current / next / grimoire stakes 읽힘까지만 증명한다.`
- 금지: action panel/log, dice tray, oath 강조 장면

### 아트
- 제출물: `assets/starter-asset-list.md`
- 핵심 문장: `Stakes Plaque Starter 범위만 sanity 판단한다.`
- 금지: battle stage에서 combat shell 전체나 queue/oath 공용 프레임 리뉴얼 요구

### QA
- 제출물: candidate verdict
- 핵심 문장: `battle-stakes-plaque archive-ready candidate`
- 금지: `green`, `unlock 완료`

### PM
- 제출물: 최종 green 기록
- 핵심 문장: `battle-stakes-plaque green. 다음 허용 단계는 oath-banner archive 생성뿐이다.`
- 금지: `Battle Table 진행`, `대체로 완료`, `oath/queue도 같이`

---

## tracker / run log 복붙 문장

| 상황 | 복붙 문장 |
| --- | --- |
| kickoff 직후 | `battle-stakes-plaque archive를 열고 manifest + hook/check 바닥을 먼저 잠갔다.` |
| evidence 수집 중 | `battle-stakes-plaque archive-in-progress: current / next / grimoire stakes evidence를 battle-only 범위로 모으는 중이다.` |
| QA candidate | `battle-stakes-plaque archive-ready candidate: artifact 8종과 battle-only wording 정렬이 확인됐다.` |
| PM green | `battle-stakes-plaque green. oath-banner archive 생성만 다음 허용 단계다.` |

---

## 가장 큰 리스크

1. manifest보다 capture가 먼저 올라오는 것
2. QA candidate와 PM green이 같은 초록불로 기록되는 것
3. battle green 전인데 oath/queue를 암묵적으로 여는 것
4. summary만 읽고 source packet의 금지 범위를 생략하는 것

---

## immediate next actions

1. `battle filled archive examples` 다음에는 이 scorecard를 읽고 owner 순서를 고정한다.
2. battle archive 하나만 연다.
3. `프로그래머 바닥 -> UI/아트 evidence -> QA candidate -> PM green` 사다리를 재사용한다.
4. action panel, dice tray, log, oath-banner, queue closing은 battle green 전까지 계속 잠근다.

## 한 장 결론
Run Table battle 축의 남은 마지막 공백은 `무슨 artifact가 필요한가`만이 아니라 **`그 artifact를 누가 어떤 순서로 제출하고 어떤 문장으로 닫아야 honest archive가 되는가`**다. 이 요약 문서는 그 답을 빠르게 보여 주는 readable companion이다.
