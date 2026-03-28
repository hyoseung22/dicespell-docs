# DiceSpell 첫 런 온보딩 `B-1 library` live kickoff 요약

## 3줄 요약
- 이 문서는 `B-1 library` 첫 live turn 직전 남아 있던 마지막 실행 공백, 즉 **`그래서 첫 10분에 정확히 무엇부터 해야 하지?`**를 읽기 좋게 압축한 companion이다.
- 핵심은 새 primer 설계를 더하는 것이 아니라, 이미 있는 `B-1 packet`, `runtime stack`, `screen packet`, `content deck`, `evidence/archive` 문서를 **한 번의 실전 kickoff 순서**로 묶는 것이다.
- 첫 화면만 읽어도 `누가 지금 움직이고`, `무엇을 아직 열지 않으며`, `어떤 evidence가 생기기 전에는 B-1 green을 말하면 안 되는지`가 바로 보이게 만드는 데 목적이 있다.

**한 줄 목표:** `B-1 library` 첫 live turn을 `archive 생성 -> manifest/stub 초안 -> semantic anchor -> library-only edit -> acceptance evidence drop` 순서로만 열게 만든다.

---

## 왜 필요한가
문서는 이미 충분하다.

문제는 이제 `무슨 문서를 더 써야 하지?`가 아니라:
- `첫 10분에 어떤 문서를 어떤 순서로 겹쳐 보지?`
- `누가 지금 edit를 하고 누가 기다리지?`
- `kickoff 메모와 B-1 green 문장을 어디서 갈라 쓰지?`

이 세 가지다.

즉 이 문서는 **문서 부족 보완**이 아니라 **B-1 첫 실전 착수 순서 정렬**을 위한 요약이다.

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
B-1은 온보딩 전체 착수 턴이 아니라 **library-only turn**이기 때문이다.

- 해야 하는 것: `seenBookSelectPrimer`, `library.book-select` hero-primer, `openingPlan/caution` highlight, library acceptance evidence
- 하면 안 되는 것: battle/reward/shop/ending primer, shelf 전체 리디자인, B-2 unlock 선기록

---

## 10분 kickoff 순서

| 시간대 | 해야 하는 일 | 끝났다고 말할 기준 |
| --- | --- | --- |
| T-10 ~ T-8 | `docs/artifacts/YYYYMMDD/b1-library/` 생성 + `logs/`, `captures/`, `notes/` 생성 | stage archive가 실제로 존재한다 |
| T-8 ~ T-6 | `manifest.md` + acceptance/state/UI/PM stub 첫 줄 채우기 | 빈 파일과 generic placeholder가 없다 |
| T-6 ~ T-4 | `readMetaProgress`, `persistMetaProgress`, `renderGrimoireShelf`, `openingPlan/caution` semantic anchor 재확인 | semantic locator 메모가 남아 있다 |
| T-4 ~ T-2 | B-1 packet 기준 허용 범위와 금지 범위 선언 | battle/reward/shop/ending 미오픈이 같은 문장으로 적혀 있다 |
| T-2 ~ T+10 | library-only edit + B1-A1~A5 acceptance evidence 수집 | evidence bundle이 archive에 들어간다 |

---

## 역할별 handoff 포인트

### 프로그래머
- 먼저 할 일: manifest/stub 생성, semantic anchor 기록, library-only edit
- 금지: battle/reward/shop/ending primer 혼입

### UI
- 먼저 할 일: hero-primer 1장 + highlight 2곳 + CTA 비차단 검수
- 금지: shelf 전체 재설계

### 아트
- 먼저 할 일: badge/accent/highlight token sanity 확인
- 금지: 신규 대형 자산 마감

### QA
- 먼저 할 일: B1-A1~A5 + recovery 3종 범위 잠금
- 금지: B-2/B-3/B-4 evidence 선기록

### PM/기획
- 먼저 할 일: `next allowed step`, boundary, handoff line 가드 확인
- 금지: `온보딩 진행 중`, `B-2도 곧 가능` 같은 큰 문장 선기록

---

## B-1에서 절대 하지 않는 것
- `seenBattlePrimer`, `seenRewardPrimer`, `seenVictoryPrimer` 선추가
- `renderBattle()` / `renderReward()` / `renderEnding()` primer 착수
- shelf 전체 레이아웃 개편
- library primer proof 없는 `B-2 unlock` 기록
- summary만 읽고 source packet 확인 없이 구현/리뷰 시작

이 중 하나라도 들어오면 B-1 kickoff가 아니라 **범위 drift**다.

---

## 최소 evidence
1. `manifest.md`
2. `logs/...b1-library...acceptance-log.md`
3. `notes/...state-diff.md`
4. `notes/...ui-hierarchy-note.md`
5. `notes/...pm-boundary-note.md`
6. 필요 시 library surface 캡처 1~2장

### 중요한 점
- generic `final.png`, `latest.txt`는 쓰지 않는다.
- green 문장은 evidence가 생긴 뒤 manifest / tracker / run log에서만 확정한다.

---

## 기록 가드

### 허용되는 kickoff 문장
- `B-1 library kickoff: archive/stub/semantic anchor/preflight 완료 후 library-only primer edit 착수`
- `B-1 library evidence는 b1-library stage archive에 수집 중`

### 허용되는 green 문장
- `이번 커밋은 B-1 library primer만 닫아 seenBookSelectPrimer, hero-primer, openingPlan/caution highlight, library acceptance evidence를 고정했고 다른 surface primer는 아직 열지 않았다.`

### 아직 금지되는 문장
- `온보딩 진행 중`
- `battle primer도 준비 완료`
- `첫 런 가이드 착수`
- `library/battle 초안 완료`

---

## 리스크 & 후속과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| B-1이 다시 온보딩 전체 착수처럼 부풀어짐 | surface별 commit discipline 붕괴 | library-only kickoff 순서를 packet으로 고정 |
| seen flag가 미리 늘어남 | recovery/debug 범위 불필요 확장 | non-change line을 먼저 기록 |
| UI가 shelf 전체 재배치로 범위를 키움 | primer 커밋이 UI refactor 커밋으로 변질 | hero-primer + highlight + 비차단성만 closing 범위로 제한 |
| PM이 green 전에 B-2 unlock을 씀 | half-cutover가 완료처럼 보임 | kickoff/green/unlock 문장을 분리 |

---

## 바로 할 일
1. `Commit 2 green` 확인 후 `B-1 packet -> 이 kickoff summary/source` 순서로 연다.
2. `b1-library` stage archive부터 만든다.
3. manifest와 stub 첫 줄을 채운다.
4. semantic anchor를 다시 찾는다.
5. 그 뒤에만 library-only edit와 evidence 수집을 시작한다.
