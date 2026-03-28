# DiceSpell `Run Table` Atlas Route Node live kickoff 요약

## 3줄 요약
- 이 문서는 `atlas route node stage`, `required artifacts`, `candidate review`, `queue readiness board`가 이미 충분한 상태에서도 남아 있던 마지막 실행 공백, 즉 **`그래서 첫 10분에 정확히 무엇부터 해야 하지?`**를 읽기 좋게 압축한 companion이다.
- 핵심은 새 atlas 설계를 더하는 것이 아니라, 이미 있는 atlas 문서들을 **한 번의 실전 kickoff 순서**로 묶는 것이다.
- 첫 화면만 읽어도 `누가 지금 움직이고`, `무엇을 아직 열지 않으며`, `어떤 evidence가 생기기 전에는 atlas candidate/green을 말하면 안 되는지`가 바로 보이게 만드는 데 목적이 있다.

**한 줄 목표:** `atlas-route-node` 첫 live turn을 `archive 생성 -> manifest/stub 초안 -> semantic anchor -> atlas-only edit -> required artifacts evidence drop` 순서로만 열게 만든다.

---

## 왜 필요한가
문서는 이미 충분하다.

문제는 이제 `무슨 문서를 더 써야 하지?`가 아니라:
- `첫 10분에 어떤 문서를 어떤 순서로 겹쳐 보지?`
- `누가 지금 edit를 하고 누가 기다리지?`
- `kickoff 메모와 atlas green 문장을 어디서 갈라 쓰지?`

이 세 가지다.

즉 이 문서는 **문서 부족 보완**이 아니라 **atlas 첫 실전 착수 순서 정렬**을 위한 요약이다.

---

## 첫 화면 결론

### 지금 바로 움직이는 사람
- 프로그래머
- UI
- QA
- PM/기획

### 지금은 경량 검수 위주인 사람
- 아트

### 이유
atlas는 Run Table 전체 착수 턴이 아니라 **atlas-only first stage turn**이기 때문이다.

- 해야 하는 것: atlas archive, root/state/threshold hook, overview/focus capture, starter sanity, atlas-only boundary note
- 하면 안 되는 것: battle/oath 착수, queue closing 문장, preview/log 전체 개편, Run Table 전체 리디자인

---

## 10분 kickoff 순서

| 시간대 | 해야 하는 일 | 끝났다고 말할 기준 |
| --- | --- | --- |
| T-10 ~ T-8 | `docs/artifacts/run-table-token-delivery/atlas-route-node/` 생성 + starter file 확인 | stage archive가 실제로 존재한다 |
| T-8 ~ T-6 | `manifest.md` + hook/dom/density/asset/handoff stub 첫 줄 채우기 | 빈 파일과 generic placeholder가 없다 |
| T-6 ~ T-4 | atlas root / route node state / threshold semantic anchor 재확인 | semantic locator 메모가 남아 있다 |
| T-4 ~ T-2 | atlas 허용 범위와 금지 범위 선언 | battle/oath/queue closing 미오픈이 같은 문장으로 적혀 있다 |
| T-2 ~ T+10 | atlas-only edit + required artifacts evidence 수집 | evidence bundle이 archive에 들어간다 |

---

## 역할별 handoff 포인트

### 프로그래머
- 먼저 할 일: manifest/stub 생성, semantic anchor 기록, atlas-only hook edit
- 금지: battle/oath hook 혼입

### UI
- 먼저 할 일: overview 1장 + threshold focus 1장 + density verdict
- 금지: preview/log 전체 재설계

### 아트
- 먼저 할 일: `Route Node Starter` sanity 확인
- 금지: 신규 공통 frame, 대형 배경 제작

### QA
- 먼저 할 일: atlas required artifacts 존재/정합성 확인
- 금지: battle/oath/queue closing evidence 선기록

### PM/기획
- 먼저 할 일: `next allowed step`, boundary, handoff line 가드 확인
- 금지: `Run Table 진행 중`, `battle도 곧 가능` 같은 큰 문장 선기록

---

## atlas에서 절대 하지 않는 것
- `data-stakes-slot`, `data-oath-role` 선추가
- battle/oath 캡처와 sanity note 동시 생성
- preview/log panel 전체 개편
- `Route Node Starter` sanity를 새 공통 frame 제작 턴으로 키우는 것
- atlas proof 없는 `battle unlock` 기록
- summary만 읽고 source packet 확인 없이 구현/리뷰 시작

이 중 하나라도 들어오면 atlas kickoff가 아니라 **범위 drift**다.

---

## 최소 evidence
1. `manifest.md`
2. `notes/hook-manifest.md`
3. `checks/dom-sanity.md`
4. `captures/atlas-1440-overview.png`
5. `captures/atlas-1440-threshold-focus.png`
6. `notes/density-review.md`
7. `assets/starter-asset-list.md`
8. `notes/handoff-line.md`

### 중요한 점
- generic `final.png`, `latest.txt`는 쓰지 않는다.
- candidate/green 문장은 evidence가 생긴 뒤 manifest / tracker / run log에서만 확정한다.

---

## 기록 가드

### 허용되는 kickoff 문장
- `atlas-route-node kickoff: archive/stub/semantic anchor/preflight 완료 후 atlas-only route node edit 착수`
- `atlas evidence는 atlas-route-node stage archive에 수집 중`

### 허용되는 candidate/green 문장
- `이번 턴은 atlas-route-node stage archive에 root/state/threshold hook, overview/focus capture, starter sanity, atlas-only boundary note를 모아 archive-ready candidate까지 준비했고 battle/oath/queue closing은 아직 열지 않았다.`
- `atlas-route-node green: atlas required artifacts와 first-glance verdict이 정렬돼 battle-stakes-plaque unlock만 연다.`

### 아직 금지되는 문장
- `Run Table 진행 중`
- `battle도 곧 가능`
- `token refinement 착수`
- `Run Table 거의 완료`

---

## 리스크 & 후속과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| atlas가 다시 Run Table 전체 착수처럼 부풀어짐 | stage별 archive discipline 붕괴 | atlas-only kickoff 순서를 packet으로 고정 |
| preview/log 개편이 route node보다 먼저 열림 | 첫 stage가 큰 UI patch로 변질 | route node/threshold first-glance만 closing 범위로 제한 |
| PM이 candidate 전에 battle unlock을 씀 | half-cutover가 완료처럼 보임 | kickoff/candidate/green 문장을 분리 |
| QA가 문서 충분함과 candidate-ready를 같은 뜻으로 씀 | archive evidence discipline 약화 | required artifacts 존재를 candidate 조건으로 고정 |

---

## 바로 할 일
1. `source-of-truth map -> gap ledger -> archive bootstrap -> refinement queue -> atlas stage packet -> 이 kickoff summary/source` 순서로 연다.
2. `atlas-route-node` stage archive부터 만든다.
3. manifest와 stub 첫 줄을 채운다.
4. semantic anchor를 다시 찾는다.
5. 그 뒤에만 atlas-only edit와 evidence 수집을 시작한다.
