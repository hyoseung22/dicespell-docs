# DiceSpell `overrunEvent` / 첫 런 온보딩 증거 매니페스트 요약

## 3줄 요약
- 이 문서는 `Commit 1 floor`, `Commit 2 closing`, `B-1~B-4`를 실제 handoff-ready 제출물 단위로 다시 읽게 만드는 빠른 보기다.
- 핵심은 `evidence bundle`을 추상어로 두지 않고, 단계별 artifact 이름/최소 수량/오너 책임을 고정하는 것이다.
- 프로그래머, UI, 아트, QA, PM은 이제 `무엇을 만들지`뿐 아니라 `무엇을 제출해야 다음 사람이 바로 받는지`를 같은 언어로 볼 수 있다.

**한 줄 목표:** 단계마다 `manifest 1개 + 검증 출력 1묶음 + 화면/상태 evidence 1묶음 + handoff 문장 1개`를 남기게 만든다.

---

## 왜 중요한가
- 문서는 충분한데 제출물이 generic하면 reviewer가 다시 물어본다.
- tracker/run log 문장만 있고 artifact 이름이 흐리면 half-cutover가 다시 생긴다.
- 특히 `Commit 2 scene capture`와 `B-4 ending helper capture`가 섞이면 boundary 문서가 바로 약해진다.

---

## 누구를 위한 문서인가
| 역할 | 먼저 볼 것 | 바로 가져갈 결론 |
| --- | --- | --- |
| 프로그래머 | 단계별 manifest | smoke/state diff/resolve diff가 없으면 제출 미완료다 |
| UI | Commit 2 / B-track capture 규칙 | 단계별 shell evidence를 따로 남겨야 한다 |
| 아트 | visual sanity 규칙 | 자산 제작 여부와 제출 evidence는 다른 층위다 |
| QA | 검수 체크리스트 | 단계명/acceptance 번호/artifact 이름이 같아야 green이다 |
| PM/기획 | handoff-ready 문장 | `증거가 있다`가 아니라 `manifest가 꽉 찼다`가 완료선이다 |

---

## 단계별 최소 제출물

### Commit 1 floor
- `commit1-smoke-log`
- `commit1-state-diff`
- `commit1-nonchange-note`
- `commit1-payload-sanity`
- `commit1-handoff-line`

**의미:** enter/snapshot/recovery는 닫았지만 reward apply / page advance / onboarding은 아직 안 열었다는 것을 증명해야 한다.

### Commit 2 closing
- `commit2-smoke-log`
- `commit2-resolve-diff`
- `commit2-scene-captures`
- `commit2-boundary-note`
- `commit2-handoff-line`

**의미:** 장면 카드가 보이는 것만으로는 부족하고, confirm 1회 적용 / resolved guard / ending 경계가 함께 증명돼야 한다.

### B-1 ~ B-4
- `surface capture`
- `recovery or boundary note`
- `handoff line`

**의미:** primer surface 1개가 닫힐 때마다 manifest도 1개씩 따로 닫는다.

---

## 파일명 규칙
`YYYYMMDD-stage-owner-artifact.ext`

예시
- `20260318-commit1-programmer-smoke-log.txt`
- `20260318-commit2-ui-none-scene.png`
- `20260318-b3-qa-boundary-note.md`

금지
- `final.png`
- `latest.txt`
- `proof.md`

이름만 보고 단계/오너/artifact 타입이 안 보이면 실패로 본다.

---

## 역할별 제출 책임
- **프로그래머:** smoke log, state diff, resolve diff
- **UI:** surface capture, hierarchy/boundary note
- **아트:** token/accent/artToken sanity note
- **QA:** acceptance 번호가 붙은 pass/fail log
- **PM/기획:** 단계 handoff 문장 + 다음 허용 단계 1개

---

## handoff-ready 판정
아래를 모두 만족해야 한다.
1. manifest 필수 artifact가 전부 있다
2. 단계명이 tracker/run log와 같다
3. non-change line 또는 boundary note가 있다
4. 다음 오너가 질문 없이 바로 착수할 수 있다

---

## 리스크 & 후속과제
- generic 파일명과 generic 캡처로 다시 흘러갈 위험이 가장 크다.
- Commit 2 scene과 B-4 ending capture가 섞이면 boundary가 무너진다.
- 다음 구현 슬롯은 새 설계보다 먼저 artifact 이름과 단계 문장을 잠그는 쪽이 맞다.
