# DiceSpell `overrunEvent` 런타임 시맨틱 앵커 패킷 요약

## 3줄 요약
- 이 문서는 `Commit 1 floor` / `Commit 2 closing` handoff가 이미 충분한 상태에서, **line number가 조금 밀려도 같은 절개 지점을 다시 찾게 만드는 readable companion**이다.
- 새 설계를 더하지 않고 `continueOverrun`, `renderCenter`, `renderEnding`, overrun smoke block을 함수 책임 / grep token / surface semantic 기준으로 다시 압축한다.
- 첫 화면만 읽어도 프로그래머/UI/아트/QA/PM이 `어디를 다시 찾을지`, `무엇은 여전히 같은 단계인지`, `무슨 문장을 그대로 써야 하는지`를 판단할 수 있어야 한다.

## 한 줄 목표
line drift가 생겨도 stage boundary는 그대로 유지하게 만든다.

## 왜 이 문서가 필요한가
지금 문제는 설계 부족이 아니라, line-anchored surgery sheet가 조금만 밀려도 implementer와 reviewer가 다시 넓게 grep하며 범위를 키울 수 있다는 점이다.

- Commit 1은 여전히 `continueOverrun` immediate chain 제거 + `renderCenter` route floor다.
- Commit 2는 여전히 resolve zone + scene/result split + closing smoke다.
- line이 밀려도 `Commit 1 floor`, `Commit 2 closing` 단계명은 바뀌지 않는다.

---

## 무엇을 다시 찾으면 되나

| 단계 | 다시 찾아야 하는 semantic zone | 핵심 토큰 |
| --- | --- | --- |
| Commit 1 | `continueOverrun()` immediate progression | `continueOverrun`, `pagePlan.push`, `maybeEnterNextPage`, `segmentTrophyTemplateIds` |
| Commit 1 | `renderCenter()` route floor | `renderCenter`, `continue-overrun`, `renderEnding(` |
| Commit 1 | overrun smoke floor | `overrunResult`, `overrunDraftResult` |
| Commit 2 | resolve zone | `resolved`, `confirm-overrun-event`, `applyEffectBundle` |
| Commit 2 | scene/result split | `renderCenter`, `renderEnding`, `reward-card` |
| Commit 2 | closing smoke | `S4/S5/S6/S10/S12`에 해당하는 overrun block |

---

## 역할별 한 줄 핸드오프
- **프로그래머**: line drift는 탐색 문제일 뿐, 범위 재설계 이유가 아니다.
- **UI**: line이 아니라 `scene-card` vs `ending helper` surface 경계를 지킨다.
- **아트**: `artTokenSet` / accent / neutral `none` branch를 semantic anchor로 본다.
- **QA**: locator가 바뀌어도 S번호와 non-change line은 그대로 유지한다.
- **PM**: `semantic anchor 재확인` 메모는 가능하지만 새 단계명은 만들지 않는다.

---

## 기록 규칙
- 허용: `Commit 1 floor semantic anchor를 live code 기준으로 재확인했다.`
- 허용: `Commit 1 floor를 닫고 overrunEvent 진입/snapshot 바닥만 고정했다.`
- 금지: `범위를 다시 설계했다`, `오버런 전환 거의 완료` 같은 큰 문장

---

## 리스크 & 후속과제
- line anchor drift가 곧 범위 drift로 번질 수 있다.
- UI/아트가 line anchor를 못 읽고 visual layer를 감각적으로 합치면 scene/result/primer 경계가 다시 섞인다.
- QA/PM이 locator drift와 stage drift를 같은 코멘트로 남기면 기록 신뢰도가 떨어진다.

---

## 즉시 다음 액션
1. `readiness board -> commit1/commit2 surgery sheet -> semantic anchor packet` 순서로 읽는다.
2. Commit 1 전에는 `continueOverrun`, `renderCenter`, overrun smoke block을 semantic token으로 먼저 다시 찾는다.
3. Commit 2 전에는 `renderEnding`, `reward-card`, `resolved` semantic zone을 먼저 다시 찾는다.
4. green 문장은 계속 `Commit 1 floor`, `Commit 2 closing`만 쓴다.

---

## 바로 가기
- `docs/dicespell_overrun_runtime_semantic_anchor_packet_kr.md`
- `docs/dicespell_overrun_commit1_floor_surgery_sheet_kr.md`
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
