# DiceSpell `Run Table` Token 아카이브 부트스트랩 요약

## 3줄 요약
- 이 문서는 `token evidence manifest packet`의 실제 첫 동작 버전이다.
- 핵심은 `나중에 evidence를 정리한다`가 아니라 **refinement를 시작하기 전에 stage 폴더와 manifest 초안을 먼저 만든다**는 점이다.
- atlas-route-node / battle-stakes-plaque / oath-banner는 모두 `bootstrap-ready -> archive-in-progress -> archive-ready` 3상태로만 본다.

## 한 줄 목표
archive 규칙을 읽는 데서 끝내지 말고, **착수 3분 안에 starter bundle을 만드는 습관**으로 바꾼다.

## 왜 이 문서가 필요한가
지금까지는 `무엇을 제출해야 하는가`와 `어디에 보관해야 하는가`까지는 충분히 닫혀 있었다.

하지만 실제 refinement 턴 직전 마지막 병목은 여전히 남아 있었다.
- 그래서 첫 폴더는 정확히 어떻게 열지?
- manifest 초안은 무엇부터 적지?
- 아직 비어 있어도 되는 것과 비어 있으면 안 되는 것은 무엇이지?

이 요약의 결론은 단순하다.
**Run Table token archive는 결과 정리 장소가 아니라 착수 직전 작업 바닥이다.**

---

## first screen 결론
- 먼저 `docs/artifacts/run-table-token-delivery/<stage>/`를 만든다.
- 그 안에 `captures/`, `notes/`, `checks/`, `assets/`, `manifest.md`를 만든다.
- starter 파일명 또는 첫 artifact를 적고 나서 live edit에 들어간다.
- closing 문장은 `archive-ready`가 된 뒤에만 쓴다.

---

## stage별 최소 starter bundle

| stage | 먼저 만들 것 | 이 단계의 핵심 |
| --- | --- | --- |
| atlas-route-node | `manifest.md`, `atlas-1440-overview`, `hook-manifest`, `density-review`, `dom-sanity`, `starter-asset-list` | route state/threshold와 board first-glance를 먼저 잠근다 |
| battle-stakes-plaque | `manifest.md`, `battle-1440-overview`, `hook-manifest`, `density-review`, `dom-sanity`, `starter-asset-list` | current/next/grimoire slot과 plaque 위계를 먼저 잠근다 |
| oath-banner | `manifest.md`, `oath-1440-overview`, `hook-manifest`, `density-review`, `dom-sanity`, `starter-asset-list` | banner/result/carry role과 CTA boundary를 먼저 잠근다 |

### 권장 추가 예약 파일
- atlas: `atlas-1440-threshold-focus.png`, `notes/handoff-line.md`
- battle: `battle-1440-stakes-focus.png`, `notes/handoff-line.md`
- oath: `oath-1440-banner-focus.png`, `notes/handoff-line.md`

---

## 역할별 handoff 포인트
- **프로그래머에게 넘길 것:** `hook-manifest.md`, `dom-sanity.md`를 먼저 만든다.
- **UI에게 넘길 것:** `overview/focus` 캡처 이름부터 stage 책임을 말하게 만든다.
- **아트에게 넘길 것:** `starter-asset-list.md`에서 이번 턴 제출/금지 범위를 같이 적는다.
- **QA에게 넘길 것:** `handoff-line.md`는 archive-ready 전까지 초안으로 둔다.
- **PM에게 넘길 것:** manifest의 `status`와 `next allowed step`이 없으면 큰 green 문장을 쓰지 않는다.

---

## green 전 체크
1. `manifest.md`가 있다.
2. `captures/`, `notes/`, `checks/`, `assets/`가 있다.
3. starter 파일명 또는 첫 artifact가 있다.
4. `hook-manifest.md`, `dom-sanity.md`, `starter-asset-list.md`가 있다.
5. generic 이름(`final`, `latest`, `misc`)이 없다.

### 상태 해석
- `bootstrap-ready`: 폴더/manifest/starter file 준비 완료, refinement 시작 가능
- `archive-in-progress`: evidence가 채워지는 중, closing 문장 금지
- `archive-ready`: required artifacts가 manifest 기준으로 충족, closing 문장 가능

---

## 리스크 & 후속과제
- 가장 큰 리스크는 evidence manifest는 알지만 starter bundle을 만들지 않아 캡처와 메모가 다시 임시 위치로 새는 것이다.
- 두 번째 리스크는 atlas/battle/oath stage boundary가 파일명부터 흐려져 surface별 closing review가 다시 뭉개지는 것이다.
- 그래서 이번에는 packet뿐 아니라 `docs/artifacts/run-table-token-delivery/templates/`에 stage별 starter manifest도 함께 둔다.

---

## immediate next actions
1. 다음 refinement 착수 전 `templates/`의 해당 stage starter manifest를 복사해 stage 폴더에 놓는다.
2. atlas는 `threshold-focus`, battle은 `stakes-focus`, oath는 `banner-focus` 예약명부터 만든다.
3. 프로그래머는 hook/check note를 먼저 채우고 나서 markup/CSS edit에 들어간다.
4. QA/PM은 `bootstrap-ready`와 `archive-ready`를 구분해 기록한다.
