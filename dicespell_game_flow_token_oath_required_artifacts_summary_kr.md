# DiceSpell `Run Table` Oath Required Artifacts Summary

## 3줄 요약
- 이 문서는 `oath-banner` 마지막 stage가 `archive-ready candidate`라고 말하려면 정확히 어떤 artifact 8종이 어떤 역할로 들어가야 하는지 빠르게 읽게 만드는 companion이다.
- 핵심은 새 ending 미감을 더 설계하는 것이 아니라, `manifest / hook-manifest / dom-sanity / overview-focus capture / density review / starter asset list / handoff line`이 같은 oath stage 문장을 쓰게 만드는 것이다.
- 한마디로 `마지막 stage evidence가 모였는가`가 아니라 **`마지막 stage evidence가 같은 뜻을 말하는가`**를 보는 문서다.

## 한 줄 목표
`oath-banner` 마지막 stage를 **artifact bundle 8종 + candidate 최소선 1개**로 다시 잠가, queue closing 직전 마지막 handoff를 과장 없는 제출물 언어로 유지한다.

## 왜 이 문서가 필요한가
- oath stage packet은 `무엇을 닫을지`를 정했다.
- 하지만 실제 제출 직전에는 `어떤 파일이 무엇을 증명해야 하는지`를 다시 짧게 보고 싶어진다.
- 마지막 stage인 만큼 generic ending 총평이나 큰 green 문장이 섞이기 가장 쉽다.

## artifact 8종 핵심표

| 파일 | 무엇을 증명하나 | 핵심 질문 |
| --- | --- | --- |
| `manifest.md` | stage 상태/경계/다음 허용 단계 | `queue closing은 oath green 뒤에만 열린다는 문장이 있나?` |
| `notes/hook-manifest.md` | `overrun-oath`, `banner|result|carry`, `overrun-cta` hook | `role vocabulary가 실제 markup에 잠겼나?` |
| `checks/dom-sanity.md` | hook 누락 여부와 oath-only 범위 | `generic ending 메모가 아니라 role anchor 확인인가?` |
| `captures/oath-1440-overview.png` | banner-first hierarchy | `main banner가 첫 시선인가?` |
| `captures/oath-1440-banner-focus.png` | banner/result/carry 집중 읽힘 | `세 role과 CTA 맥락이 한 장에 붙어 보이나?` |
| `notes/density-review.md` | overview/focus 근거 verdict | `유지/약함/과함이 두 캡처를 직접 참조하나?` |
| `assets/starter-asset-list.md` | Oath Banner Starter 충분성/비범위 | `ending 전체 리뉴얼이 아니라 starter sanity인가?` |
| `notes/handoff-line.md` | candidate/green 복붙 문장 초안 | `Run Table complete` 같은 큰 문장이 빠졌나?` |

## archive-ready candidate 최소선
1. artifact 8종이 모두 oath archive 안에 있다.
2. `manifest`, `hook-manifest`, `dom-sanity`, `handoff-line`이 모두 `oath-banner` stage 언어를 쓴다.
3. overview/focus 두 캡처와 density-review가 같은 질문에 답한다.
4. starter note에 `이번 stage에서 아직 안 하는 것`이 있다.
5. `next allowed step`이 `Run Table token queue closing only after oath green`과 같은 뜻이다.

## 금지 문장
- `ending 거의 완료`
- `Run Table green`
- `queue closing도 같이 가능`
- `사실상 마지막 polish`

## role-specific handoff points
- **프로그래머:** hook/diff note를 generic ending 메모로 쓰지 않는다.
- **UI:** overview/focus 두 장 모두 필요하고, 둘 다 banner-first를 증명해야 한다.
- **아트:** `Oath Banner Starter` 충분성과 비범위만 적는다.
- **QA:** file existence가 아니라 wording alignment를 본다.
- **PM:** oath green은 queue closing 직전 마지막 stage green일 뿐 전체 종료가 아니다.

## immediate next actions
1. `token refinement queue -> oath banner stage packet -> oath required artifacts packet` 순서로 읽는다.
2. artifact 8종이 같은 stage 문장을 쓰는지 먼저 맞춘다.
3. QA는 alignment가 맞을 때만 `oath-banner archive-ready candidate`를 남긴다.
4. PM은 그 뒤에만 `oath-banner green`을 기록하고, queue closing은 다음 층위로만 연다.

## 한 장 결론
이 문서의 핵심은 단순하다. **oath 마지막 stage는 파일이 많다고 닫히는 게 아니라, 그 파일들이 같은 `oath-banner` 문장을 말할 때만 닫힌다.** 그래서 queue closing 직전 마지막 턴도 여전히 작은 stage 언어로만 남겨야 한다.
