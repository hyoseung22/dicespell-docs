# DiceSpell `overrunEvent` / 첫 런 온보딩 아카이브 부트스트랩 요약

## 3줄 요약
- 이 문서는 `artifact archive packet`의 실제 첫 동작 버전이다.
- 핵심은 `나중에 evidence를 정리한다`가 아니라 **코드를 열기 전에 stage 폴더와 manifest 초안을 먼저 만든다**는 점이다.
- Commit 1/Commit 2/B-1~B-4는 모두 `bootstrap-ready -> archive-in-progress -> archive-ready`의 3상태로만 본다.

## 한 줄 목표
archive 규칙을 읽는 것에서 끝내지 말고, **착수 3분 안에 starter bundle을 만드는 습관**으로 바꾼다.

## 왜 이 문서가 필요한가
지금까지는 `무엇을 제출해야 하는가`와 `어디에 보관해야 하는가`까지는 충분히 닫혀 있었다.

하지만 실제 구현 턴 직전 마지막 병목은 여전히 남았다.
- 그래서 첫 폴더는 정확히 어떻게 열지?
- manifest 초안은 무엇부터 적지?
- 아직 비어 있어도 되는 것과 비어 있으면 안 되는 것은 무엇이지?

이 요약의 결론은 단순하다.
**stage archive는 결과 정리 장소가 아니라 착수 직전 작업 바닥이다.**

---

## first screen 결론
- 먼저 `docs/artifacts/YYYYMMDD/<stage>/`를 만든다.
- 그 안에 `logs/`, `captures/`, `notes/`, `manifest.md`를 만든다.
- starter 파일명 또는 첫 artifact를 적고 나서 코드를 연다.
- green 문장은 `archive-ready`가 된 뒤에만 쓴다.

---

## stage별 최소 starter bundle

| stage | 먼저 만들 것 | 이 단계의 핵심 |
| --- | --- | --- |
| Commit 1 floor | `manifest.md`, `smoke-log`, `state-diff`, `nonchange-note` | scene보다 floor/state/non-change 증거가 먼저다 |
| Commit 2 closing | `manifest.md`, `smoke-log`, `theme/enemy/none scene`, `boundary-note` | scene/resolve/boundary를 같은 stage로 묶는다 |
| B-1 library | `manifest.md`, `library-primer capture`, `guidance-state note` | seen flag와 비차단이 핵심이다 |
| B-2 battle | `manifest.md`, `battle-entry capture`, `trigger note` | primer보다 trigger 타이밍 기록이 먼저 필요하다 |
| B-3 reward/shop | `manifest.md`, `reward capture`, `shop capture`, `purpose boundary note` | reward와 shop은 starter 단계부터 분리한다 |
| B-4 ending | `manifest.md`, `ending capture`, `boundary note`, `tone note` | ending helper와 overrun flavor 경계를 먼저 잠근다 |

---

## 역할별 handoff 포인트
- **프로그래머에게 넘길 것:** diff note 파일을 먼저 만들고 들어간다.
- **UI에게 넘길 것:** 첫 capture 파일명부터 stage 책임을 말하게 만든다.
- **아트에게 넘길 것:** tone/token note는 capture와 분리한다.
- **QA에게 넘길 것:** starter bundle이 없으면 리뷰를 시작하지 않는다.
- **PM에게 넘길 것:** `next allowed step`과 handoff line 초안이 manifest에 있어야 착수 선언이 가능하다.

---

## green 전 체크
1. `manifest.md`가 있다.
2. `logs/`, `captures/`, `notes/`가 있다.
3. starter 파일명 또는 첫 artifact가 있다.
4. generic 이름(`final`, `latest`, `misc`)이 없다.
5. stage 문장과 archive 경로가 같다.

### 상태 해석
- `bootstrap-ready`: 폴더/manifest/starter file 준비 완료, 구현 시작 가능
- `archive-in-progress`: evidence가 채워지는 중, green 금지
- `archive-ready`: required artifacts가 manifest 기준으로 충족, green 가능

---

## 리스크 & 후속과제
- 가장 큰 리스크는 archive 규칙은 알지만 starter bundle을 만들지 않아 evidence가 다시 임시 폴더로 새는 것이다.
- 두 번째 리스크는 stage별 manifest 초안이 없어서 첫 턴마다 체크박스와 green 문장이 흔들리는 것이다.
- 그래서 이번에는 packet뿐 아니라 `docs/artifacts/templates/`에 stage별 starter manifest도 함께 둔다.

---

## immediate next actions
1. 다음 Commit 1 착수 전 `docs/artifacts/templates/stage_manifest_commit1_floor.md`를 복사해 날짜 루트에 놓는다.
2. Commit 2는 scene capture 예약명부터 만든다.
3. B-track은 surface 1개당 stage bundle 1개 원칙을 bootstrap 때부터 지킨다.
4. tracker/run log에는 `bootstrap-ready`와 `archive-ready`를 구분해 적는다.
