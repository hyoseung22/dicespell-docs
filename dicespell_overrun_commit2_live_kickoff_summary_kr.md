# DiceSpell `overrunEvent` Commit 2 live kickoff 요약

## 3줄 요약
- 이 문서는 Commit 2 첫 live closing turn 직전 남아 있던 마지막 실행 공백, 즉 **`그래서 Commit 1 green 뒤 첫 10분에 정확히 무엇부터 다시 잠가야 하지?`**를 읽기 좋게 압축한 companion이다.
- 핵심은 새 설계를 더하는 것이 아니라, 이미 있는 surgery / visual / boundary / content / evidence 문서를 **한 번의 실전 closing kickoff 순서**로 묶는 것이다.
- 첫 화면만 읽어도 `누가 지금 실제 closing에 참여하고`, `무엇을 아직 열지 않으며`, `어떤 evidence가 생기기 전에는 runtime-ready green을 말하면 안 되는지`가 바로 보이게 만드는 데 목적이 있다.

**한 줄 목표:** Commit 2 첫 live turn을 `Commit 1 green 확인 -> closing archive 생성 -> manifest/stub 초안 -> visual/boundary/semantic freeze -> closing-only edit -> evidence drop` 순서로만 열게 만든다.

---

## 왜 필요한가
문서는 이미 충분하다.

문제는 이제 `무슨 문서를 더 써야 하지?`가 아니라:
- `Commit 1 green 뒤 Commit 2 첫 10분에는 무엇을 어떤 순서로 겹쳐 보지?`
- `누가 지금 실제 closing owner이고 누가 금지 범위를 지키는 reviewer지?`
- `kickoff 문장과 runtime-ready green 문장을 어디서 갈라 쓰지?`

이 세 가지다.

즉 이 문서는 **문서 부족 보완**이 아니라 **마지막 closing 착수 순서 정렬**을 위한 요약이다.

---

## 첫 화면 결론

### 지금 바로 움직이는 사람
- 프로그래머
- UI
- 아트
- QA
- PM/기획

### 하지만 아직 기다리는 것
- onboarding B-track 구현
- ending flavor 확장
- 새 메카닉/새 자산 추가

### 이유
Commit 2는 `모두가 참여하는 턴`이지만, 동시에 **가장 쉽게 범위가 부풀어지는 closing-only turn**이기 때문이다.

- 해야 하는 것: `scene-card`, `confirm resolve`, `resolved guard`, `old baseline 제거`, boundary evidence
- 하면 안 되는 것: onboarding, ending 재확장, polish backlog 흡수

---

## 10분 kickoff 순서

| 시간대 | 해야 하는 일 | 끝났다고 말할 기준 |
| --- | --- | --- |
| T-10 ~ T-8 | Commit 1 green evidence 확인 + `docs/artifacts/YYYYMMDD/commit2-closing/` 생성 | Commit 1 evidence가 실제 파일로 있고 stage archive가 존재한다 |
| T-8 ~ T-6 | `manifest.md` + smoke/scene/resolve/boundary stub 첫 줄 채우기 | 빈 파일과 generic placeholder가 없다 |
| T-6 ~ T-4 | `commit2 surgery` + `visual asset` + `boundary` + `content deck` freeze | ending/result와 overrun scene 책임이 같은 문장으로 적혀 있다 |
| T-4 ~ T-2 | `renderCenter`, `renderEnding`, dispatcher, `resolved`, smoke block semantic anchor 재확인 | semantic locator 메모가 남아 있다 |
| T-2 ~ T+10 | closing-only edit + Commit 2 evidence 수집 | `S4/S5/S6/S10/S12` + scene capture + resolve diff + boundary note가 archive에 들어간다 |

---

## 역할별 handoff 포인트

### 프로그래머
- 먼저 할 일: Commit 1 green 확인, semantic anchor 기록, closing-only edit
- 금지: onboarding/helper/새 branch 혼입

### UI
- 먼저 할 일: `scene-card` hierarchy, `none` density, ending 경계 확인
- 금지: ending 전체 재디자인

### 아트
- 먼저 할 일: token/accent/none sanity 확인
- 금지: 새 key art, 대형 신규 자산 마감

### QA
- 먼저 할 일: `S4/S5/S6/S10/S12` 범위 잠금 + scene/resolve evidence 수집
- 금지: fixture 번호 없는 `ok` 기록

### PM/기획
- 먼저 할 일: stop/go, stage language, green 문장 가드 확인
- 금지: `온보딩도 같이` 선기록

---

## Commit 2에서 절대 하지 않는 것
- onboarding B-track helper/primer 착수
- ending helper에 overrun flavor 재주입
- 새 reward/content branch 설계
- 대형 신규 자산 요청
- balance 가설 확장
- polish backlog 흡수

이 중 하나라도 들어오면 Commit 2 kickoff가 아니라 **closing drift**다.

---

## 최소 evidence
1. `manifest.md`
2. `logs/...commit2...smoke-log.md`
3. `captures/...scene-capture.md`
4. `notes/...resolve-diff.md`
5. `notes/...boundary-note.md`
6. 필요 시 `notes/...art-sanity.md`

### 중요한 점
- scene capture가 없으면 UI closing evidence가 아니다.
- resolve diff가 없으면 runtime-ready evidence가 아니다.
- green 문장은 stub가 아니라 manifest / tracker / run log에서만 확정한다.

---

## 기록 가드

### 허용되는 kickoff 문장
- `Commit 2 closing kickoff: Commit 1 green 확인 후 closing archive/visual boundary preflight 완료, closing-only edit 착수`
- `Commit 2 closing evidence는 commit2-closing stage archive에 수집 중`

### 허용되는 green 문장
- `Commit 2 closing을 닫고 scene/resolve/runtime-ready 경계를 고정했다.`

### 아직 금지되는 문장
- `온보딩도 이어서 구현`
- `오버런 전체 완료`
- `ending 리디자인 완료`
- `다음 phase 대부분 준비됨`

---

## 리스크 & 후속과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| Commit 2가 단순 scene polish 턴으로 오해됨 | old baseline과 resolve 결함이 남는다 | resolve diff + guard evidence를 필수화 |
| ending ↔ overrunEvent 경계가 closing 턴에 다시 흐려짐 | B-4 이전에 재작업이 생긴다 | boundary note를 필수 evidence로 고정 |
| UI/아트가 신규 자산/과한 polish를 같이 밀어 넣음 | Commit 2 범위가 backlog 처리 턴이 된다 | token/accent/none sanity까지만 허용 |
| QA/PM이 scene capture 없이 green을 기록함 | half-cutover가 runtime-ready처럼 남는다 | smoke + capture + resolve diff 묶음을 강제 |

---

## 바로 할 일
1. Commit 1 green evidence부터 확인한다.
2. `commit2-closing` stage archive를 만든다.
3. manifest와 Commit 2 stub 첫 줄을 채운다.
4. visual/boundary/semantic freeze를 잠근다.
5. 그 뒤에만 Commit 2 closing edit와 evidence 수집을 시작한다.
6. Commit 2 green 뒤에만 B-1 library를 연다.
