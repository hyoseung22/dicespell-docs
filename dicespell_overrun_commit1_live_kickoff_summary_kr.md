# DiceSpell `overrunEvent` Commit 1 live kickoff 요약

## 3줄 요약
- 이 문서는 Commit 1 첫 live turn 직전 남아 있던 마지막 실행 공백, 즉 **`그래서 첫 10분에 정확히 무엇부터 해야 하지?`**를 읽기 좋게 압축한 companion이다.
- 핵심은 새 설계를 더하는 것이 아니라, 이미 있는 archive/manifest/stub/semantic anchor/surgery/rehearsal 문서를 **한 번의 실전 kickoff 순서**로 묶는 것이다.
- 첫 화면만 읽어도 `누가 지금 움직이고`, `무엇을 아직 열지 않으며`, `어떤 evidence가 생기기 전에는 green을 말하면 안 되는지`가 바로 보이게 만드는 데 목적이 있다.

**한 줄 목표:** Commit 1 첫 live turn을 `archive 생성 -> manifest/stub 초안 -> semantic anchor -> floor-only edit -> evidence drop` 순서로만 열게 만든다.

---

## 왜 필요한가
문서는 이미 충분하다.

문제는 이제 `무슨 문서를 더 써야 하지?`가 아니라:
- `첫 10분에 어떤 문서를 어떤 순서로 겹쳐 보지?`
- `누가 지금 edit를 하고 누가 기다리지?`
- `kickoff 메모와 green 문장을 어디서 갈라 쓰지?`

이 세 가지다.

즉 이 문서는 **문서 부족 보완**이 아니라 **첫 실전 착수 순서 정렬**을 위한 요약이다.

---

## 첫 화면 결론

### 지금 바로 움직이는 사람
- 프로그래머
- QA
- PM/기획

### 지금은 기다리는 사람
- UI
- 아트

### 이유
Commit 1은 scene closing 턴이 아니라 **floor-only turn**이기 때문이다.

- 해야 하는 것: `enter/snapshot floor`, recovery, non-change evidence
- 하면 안 되는 것: resolve, scene shell, onboarding B-track, 새 자산 마감

---

## 10분 kickoff 순서

| 시간대 | 해야 하는 일 | 끝났다고 말할 기준 |
| --- | --- | --- |
| T-10 ~ T-8 | `docs/artifacts/YYYYMMDD/commit1-floor/` 생성 + `logs/`, `captures/`, `notes/` 생성 | stage archive가 실제로 존재한다 |
| T-8 ~ T-6 | `manifest.md` + smoke/state/non-change/payload stub 첫 줄 채우기 | 빈 파일과 generic placeholder가 없다 |
| T-6 ~ T-4 | `continueOverrun`, `renderCenter`, overrun smoke block semantic anchor 재확인 | semantic locator 메모가 남아 있다 |
| T-4 ~ T-2 | surgery + rehearsal 기준 금지 범위 선언 | reward/page/scene/onboarding 미오픈이 같은 문장으로 적혀 있다 |
| T-2 ~ T+10 | floor-only edit + Commit 1 evidence 수집 | smoke/state/non-change evidence가 archive에 들어간다 |

---

## 역할별 handoff 포인트

### 프로그래머
- 먼저 할 일: manifest/stub 생성, semantic anchor 기록, floor-only edit
- 금지: resolve/scene/onboarding 혼입

### QA
- 먼저 할 일: `S1/S2/S3/S7/S8/S9/S10/S11` 범위 잠금
- 금지: fixture 번호 없는 `ok` 기록

### PM/기획
- 먼저 할 일: `next allowed step`, non-change, handoff candidate guard 확인
- 금지: green 선기록

### UI
- 지금 할 일: payload sanity 확인까지만
- 금지: scene shell closing

### 아트
- 지금 할 일: drift 금지 확인까지만
- 금지: 톤/자산 마감

---

## Commit 1에서 절대 하지 않는 것
- reward apply / page advance
- confirm resolve
- scene-card layout closing
- ending helper 수정
- onboarding B-track helper 주입
- Commit 2 evidence 생성

이 중 하나라도 들어오면 Commit 1 kickoff가 아니라 **범위 drift**다.

---

## 최소 evidence
1. `manifest.md`
2. `logs/...commit1...smoke-log.md`
3. `notes/...state-diff.md`
4. `notes/...nonchange-note.md`
5. 필요 시 `notes/...payload-sanity.md`

### 중요한 점
- fake capture는 만들지 않는다.
- green 문장은 stub가 아니라 manifest / tracker / run log에서만 확정한다.

---

## 기록 가드

### 허용되는 kickoff 문장
- `Commit 1 floor kickoff: archive/stub/semantic anchor/preflight 완료 후 floor-only edit 착수`
- `Commit 1 floor evidence는 commit1-floor stage archive에 수집 중`

### 아직 금지되는 문장
- `runtime-ready 전환`
- `scene/resolve 닫힘`
- `오버런 구현 진행`
- `온보딩 다음 단계 준비 완료`

---

## 리스크 & 후속과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| 첫 live turn에서 archive/stub/anchor 순서가 다시 owner별로 갈라짐 | Commit 1이 다시 감각 기반 커밋이 됨 | kickoff 순서를 source packet으로 고정 |
| kickoff 메모와 green 문장이 섞임 | preflight가 완료처럼 기록됨 | kickoff 문장과 green 문장을 분리 |
| UI/아트가 Commit 1에서 너무 빨리 움직임 | payload floor보다 시각 closing이 앞섬 | Commit 1은 floor turn, Commit 2는 closing turn으로 재고정 |

---

## 바로 할 일
1. `commit1-floor` stage archive부터 만든다.
2. manifest와 Commit 1 stub 첫 줄을 채운다.
3. semantic anchor를 다시 찾는다.
4. 금지 범위를 선언한다.
5. 그 뒤에만 Commit 1 floor edit와 evidence 수집을 시작한다.
