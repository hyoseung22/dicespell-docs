# DiceSpell `Run Table` Oath Banner Filled Archive Examples Summary

## 3줄 요약
- 이 문서는 `oath-banner` 마지막 live archive가 실제로 어느 문장 밀도까지 채워져야 honest candidate인지 빠르게 읽기 위한 readable companion이다.
- 핵심은 새 ending 화면을 더 설계하는 것이 아니라, artifact 8종이 모두 같은 `banner-first stage language`를 쓰게 만드는 것이다.
- 첫 화면만 읽어도 프로그래머/UI/아트/QA/PM이 `무엇을 채우고`, `무엇을 아직 쓰지 말아야 하는지`, `candidate와 green을 어떤 문장으로 분리할지`를 바로 잡아야 한다.

## 한 줄 목표
`oath-banner` exemplar bundle을 **artifact 8종 + 금지 문장 + 복붙 문장 2개**로 짧게 읽히게 만든다.

## 왜 중요한가
- file map과 required artifacts만으로는 `어느 정도로 써야 honest archive인지`가 아직 모호하다.
- 마지막 stage는 특히 `ending 거의 완료`, `queue closing도 곧` 같은 큰 문장으로 부풀기 쉽다.
- 그래서 실제 exemplar bundle이 필요하다.

## 어떤 파일이 exemplar인가
- `manifest.md`
- `notes/hook-manifest.md`
- `checks/dom-sanity.md`
- `captures/oath-1440-overview.caption.md`
- `captures/oath-1440-banner-focus.caption.md`
- `notes/density-review.md`
- `assets/starter-asset-list.md`
- `notes/handoff-line.md`

## 누가 무엇을 읽어야 하나
| 역할 | 먼저 볼 것 | 바로 가져갈 결론 |
| --- | --- | --- |
| 프로그래머 | manifest / hook / check | oath는 `banner/result/carry`까지만 닫고 ending helper/queue closing은 아직 쓰지 않는다 |
| UI | overview / focus / density | 예쁜 ending shot이 아니라 banner-first pass/fail을 남긴다 |
| 아트 | starter asset list | 큰 리뉴얼 backlog가 아니라 Oath Banner Starter 충분성만 말한다 |
| QA | handoff line + density + manifest | wording alignment가 맞을 때만 candidate를 쓴다 |
| PM | handoff line | green은 마지막 한 줄만 쓴다 |

## 꼭 남겨야 하는 핵심 문장
- `이번 stage는 oath-banner banner / result / carry 읽힘까지만 닫는다.`
- `ending helper, reward/shop side family, queue closing은 아직 열지 않는다.`
- `oath-banner archive-ready candidate ...`
- `oath-banner green ... Run Table token queue closing review-only unlock.`

## 금지 문장
- `Overrun Oath 거의 완료`
- `ending은 사실상 끝`
- `queue closing도 같이 가능`
- `Run Table complete 느낌`

## 바로 다음 액션
1. `examples/oath-banner/` exemplar 8종을 reference layer로만 읽는다.
2. 실제 live archive는 `docs/artifacts/run-table-token-delivery/oath-banner/` 하나만 쓴다.
3. `manifest -> hook/check -> overview·banner-focus + density -> starter sanity -> candidate -> green` 순서를 지킨다.

## 한 장 결론
oath 마지막 stage는 `자료가 모였다`가 아니라 **`artifact 8종이 같은 banner-first stage boundary를 말한다`**일 때만 candidate다.
