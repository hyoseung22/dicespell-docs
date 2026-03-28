# DiceSpell `Run Table` Oath Banner semantic anchor 요약

## 3줄 요약
- 이 문서는 battle green 뒤 oath 첫 실전 턴에서 **정확히 어떤 locator를 다시 찾아야 하는지** 빠르게 읽는 companion이다.
- 핵심은 oath를 `ending 전체 polish`로 읽지 않고, `oath-banner` 한 stage의 semantic zone으로만 다시 고정하는 것이다.
- 첫 live edit 전에는 반드시 `root / role / CTA / boundary / evidence question`이 같은 oath-only 문장으로 잠겨 있어야 한다.

**한 줄 목표:** oath 첫 live turn의 semantic locator를 `surface root -> banner/result/carry role -> continue-overrun CTA -> boundary -> overview/banner-focus proof` 5축으로 고정한다.

---

## 이 문서는 누구를 위한가
- **프로그래머:** `renderEnding(summary)`에서 지금 다시 찾아야 할 zone만 빠르게 확인할 사람
- **UI:** `banner > result > carry` hierarchy가 first-glance로 읽히는지만 보고 싶은 사람
- **아트:** `Oath Banner Starter` sanity만 확인하고 ending 전체 리뉴얼은 미루려는 사람
- **QA / PM:** semantic anchor 메모를 candidate/green/queue closing과 섞지 않으려는 사람

---

## 왜 지금 필요한가
이미 oath에는 stage packet, live kickoff, required artifacts, candidate review 문서가 있다.

그래도 실제 turn 직전에는 이 질문이 남았다.

> `좋아, 이제 oath archive를 실제로 열 수 있어. 그런데 편집 직전에 정확히 어떤 함수 / DOM / 캡처 질문을 다시 찾아야 하지?`

이 요약은 그 공백만 닫는다.

---

## first screen 결론
- 지금 다시 찾아야 하는 것은 **oath semantic anchor**다.
- 프로그래머는 `renderEnding(summary)`, `.run-ending-stage`, `.ending-banner`, `.ending-main-column`, `.ending-side-column`, `button[data-action="continue-overrun"]` 주변만 본다.
- UI / QA는 `main banner가 먼저 읽히는가`, `banner / result / carry와 CTA 맥락이 한 장에서 같은 family로 읽히는가` 두 질문만 증명하면 된다.
- `ending helper`, `reward/shop side family`, `queue closing wording`, `Run Table complete` 문장은 이번 stage 범위 밖이다.

---

## oath semantic anchor 5축

| 축 | 지금 다시 찾을 것 | 왜 중요한가 |
| --- | --- | --- |
| Surface root | `renderEnding(summary)`, `.run-ending-stage`, 목표 hook `data-run-surface="overrun-oath"` | oath stage를 generic ending이 아니라 oath surface로 다시 잠그기 위해 |
| Role anchor | `.ending-banner`, `.ending-main-column`, `.ending-side-column`, 목표 hook `data-oath-role="banner|result|carry"` | banner/result/carry 읽힘을 같은 role vocabulary로 고정하기 위해 |
| CTA context | `button[data-action="continue-overrun"]`, 목표 hook `data-overrun-cta` | carry와 CTA가 같은 family로 읽히되 banner를 덮지 않게 하기 위해 |
| Boundary | ending helper 전체, reward/shop side family, queue closing wording은 비범위 | locator를 찾는 순간 범위도 같이 잠가야 drift가 안 생긴다 |
| Evidence | `oath-1440-overview.png`, `oath-1440-banner-focus.png` | semantic anchor가 실제로 first-glance 증거까지 이어지는지 확인하기 위해 |

---

## 프로그래머가 먼저 가져갈 결론
- oath 첫 live edit은 `renderEnding(summary)` 전체가 아니라 **oath banner family**만 다시 찾는 턴이다.
- 지금 필요한 건 helper 대수술보다 `root / role / CTA / boundary / non-change` 다섯 줄을 `notes/hook-manifest.md`에 먼저 적는 일이다.
- queue closing hook, ending helper 구조 재설계, reward/shop side family 비교는 이번 stage에서 금지다.

---

## UI가 먼저 가져갈 결론
- 캡처는 두 장이면 충분하다.
  - `oath-1440-overview.png`
  - `oath-1440-banner-focus.png`
- overview는 **main banner가 먼저 읽히는가**, focus는 **banner / result / carry와 CTA 맥락이 한 장에서 읽히는가**만 증명하면 된다.
- result table이나 CTA가 banner보다 더 먼저 튀면 이번 stage는 fail note가 맞다.

---

## 아트가 먼저 가져갈 결론
- 이번 턴 아트 일은 `Oath Banner Starter` sanity note 하나면 충분하다.
- ending shell 전체 리뉴얼, reward/shop 공용 side frame, queue closing badge는 지금 열 일이 아니다.
- starter note에는 `이번 stage에서 아직 안 하는 것`을 같이 적어야 한다.

---

## QA / PM이 먼저 가져갈 결론
- semantic anchor 메모는 candidate도 green도 아니다.
- `oath archive-ready candidate`는 required artifacts 8종이 같은 oath stage 언어를 쓸 때만 가능하다.
- `oath-banner green`과 `Run Table token queue closing review-only unlock`은 semantic anchor 메모 뒤에만 쓴다.
- `Overrun Oath 거의 완료`, `ending 사실상 green`, `Run Table 완료` 같은 큰 문장은 전부 금지다.

---

## 금지 패턴
- `이 김에 ending helper도 같이`
- `reward/shop side family도 이번 pass에`
- `queue closing wording도 미리`
- `Overrun Oath 전반 shell polish`
- semantic locator 없이 generic ending 메모만 남기기

---

## 즉시 다음 액션
1. `source-of-truth map -> gap ledger -> queue readiness board -> oath live kickoff -> oath semantic anchor` 순서로 다시 읽는다.
2. `notes/hook-manifest.md`에 `root / role / cta / boundary / non-change` 5줄을 먼저 적는다.
3. `notes/density-review.md`에 overview / banner-focus 질문을 먼저 적는다.
4. 그 뒤에만 oath-only edit와 evidence drop을 시작한다.

---

## 이어서 볼 문서
- 원본 source handoff: `./dicespell_game_flow_token_oath_semantic_anchor_packet_kr.md`
- stage packet: `./dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- live kickoff: `./dicespell_game_flow_token_oath_live_kickoff_packet_kr.md`
- required artifacts: `./dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- candidate review: `./dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- readiness board: `./dicespell_game_flow_token_queue_readiness_board_kr.md`
