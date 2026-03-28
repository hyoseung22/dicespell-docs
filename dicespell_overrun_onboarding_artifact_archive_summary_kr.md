# DiceSpell `overrunEvent` / 첫 런 온보딩 아카이브 패킷 요약

## 3줄 요약
- 이 문서는 `증거 매니페스트`가 정한 제출물을 실제로 어디에 어떻게 모아 둘지 빠르게 읽게 만드는 companion이다.
- 핵심은 `무엇을 제출할까` 다음 단계인 `좋아, 그럼 그걸 어디에 두면 다음 사람이 바로 찾지?`를 stage별 archive 구조로 고정하는 것이다.
- 프로그래머, UI, 아트, QA, PM은 이제 같은 stage명 아래 같은 폴더 구조를 보고 handoff-ready를 판단할 수 있다.

**한 줄 목표:** `Commit 1 floor`, `Commit 2 closing`, `B-1~B-4`를 `stage 폴더 1개 + manifest 1개 + logs/captures/notes` 구조로 고정한다.

---

## 왜 중요한가
- evidence 파일명이 맞아도 저장 위치가 제각각이면 reviewer가 다시 찾게 된다.
- Commit 2 scene capture와 B-4 ending helper capture가 섞이면 boundary 문서가 약해진다.
- tracker/run log green 문장은 archive 경로까지 있어야 실제 handoff-ready로 읽힌다.

---

## 가장 짧은 규칙
- archive root는 `docs/artifacts/`
- 날짜별 루트는 `docs/artifacts/YYYYMMDD/`
- stage별 루트는 `docs/artifacts/YYYYMMDD/<stage>/`
- 각 stage는 아래 4개를 기본으로 가진다.
  - `manifest.md`
  - `logs/`
  - `captures/`
  - `notes/`

---

## stage 폴더 이름
- `commit1-floor`
- `commit2-closing`
- `b1-library`
- `b2-battle`
- `b3-reward-shop`
- `b4-ending`

stage를 한 폴더에 합치지 않는다.

---

## 파일명 규칙
`YYYYMMDD-stage-owner-artifact.ext`

예시
- `20260318-commit1-programmer-state-diff.md`
- `20260318-commit2-ui-theme-scene.png`
- `20260318-b4-pm-boundary-note.md`

금지
- `final.png`
- `latest.txt`
- `proof.md`

---

## owner별 drop 위치
- **프로그래머:** `logs/`, `notes/`
- **UI:** `captures/`, `notes/`
- **아트:** `captures/`, `notes/`
- **QA:** `logs/`, `notes/`
- **PM/기획:** `manifest.md`, `notes/`

---

## green 직전 체크
1. stage archive 폴더가 있다
2. `manifest.md`가 있다
3. 필수 artifact가 logs/captures/notes에 분리돼 있다
4. boundary 또는 non-change note가 있다
5. 다음 허용 단계가 tracker/run log와 같은 문장이다

---

## immediate next actions
1. 다음 A-track 구현 턴을 열면 먼저 `docs/artifacts/YYYYMMDD/commit1-floor/`를 만든다.
2. Commit 2에서는 scene capture와 boundary note를 `commit2-closing`에만 둔다.
3. B-track을 열면 `surface 1개 = stage 폴더 1개` 원칙을 그대로 쓴다.
4. tracker/run log green 문장에는 archive 경로를 함께 남긴다.
