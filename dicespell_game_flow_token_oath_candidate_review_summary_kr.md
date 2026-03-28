# DiceSpell `Run Table` Oath Candidate Review Summary

## 3줄 요약
- 이 문서는 `oath-banner` 마지막 stage에서 QA와 PM이 무엇을 어떤 순서로 보고 `candidate -> green -> queue closing unlock`을 구분해야 하는지 빠르게 읽게 만드는 companion이다.
- 핵심은 `artifact 존재`가 아니라 **`artifact alignment review -> banner-first verdict -> starter sanity verdict -> candidate -> green`** 순서를 같은 문장으로 유지하는 것이다.
- 한마디로 `마지막 stage evidence가 있나`가 아니라 **`마지막 stage evidence를 같은 질문으로 읽었나`**를 보는 문서다.

## 한 줄 목표
`oath-banner` 마지막 stage review를 **candidate와 green, queue closing unlock이 서로 다른 층위라는 점**까지 포함해 짧게 고정한다.

## 왜 이 문서가 필요한가
- oath stage packet은 `무엇을 닫을지`를 정했다.
- oath required artifacts packet은 `무엇을 제출할지`를 정했다.
- 하지만 실제 리뷰 직전에는 `누가 어떤 순서로 읽고 어떤 문장을 남길지`를 다시 짧게 확인하고 싶어진다.

## review 순서 핵심표

| 순서 | 무엇을 보나 | 핵심 질문 |
| --- | --- | --- |
| 1 | `manifest / hook-manifest / dom-sanity / handoff-line` | `모두 oath-banner와 queue closing 전 문장을 같은 뜻으로 말하나?` |
| 2 | `overview / banner-focus / density-review` | `banner-first hierarchy가 실제로 읽히나?` |
| 3 | `starter-asset-list` | `Oath Banner Starter 충분성과 비범위가 같이 적혀 있나?` |
| 4 | QA candidate | `위 1~3이 모두 pass인가?` |
| 5 | PM green | `candidate가 있고 handoff-line과 green 문장이 같은 뜻인가?` |

## fail 대표 예시
- `handoff-line`이 `ending 거의 완료`처럼 커진다.
- density-review가 overview/focus 두 캡처를 직접 참조하지 않는다.
- starter note가 ending shell 리뉴얼 backlog처럼 읽힌다.
- QA candidate 없이 PM이 green이나 queue closing을 먼저 적는다.

## 허용 문장
- `oath-banner archive-ready candidate ...`
- `oath-banner green ...`
- `Run Table token queue closing review-only unlock`

## 금지 문장
- `Overrun Oath 거의 완료`
- `ending은 사실상 끝`
- `Run Table token 완료`
- `queue closing도 같이 시작`

## immediate next actions
1. `token refinement queue -> oath banner stage packet -> oath required artifacts packet -> oath candidate review packet` 순서로 읽는다.
2. QA는 wording alignment와 banner-first evidence를 먼저 확인한다.
3. PM은 QA candidate 뒤에만 `oath-banner green`을 기록한다.
4. queue closing은 그다음 운영 층위로만 연다.

## 한 장 결론
이 문서의 핵심은 단순하다. **oath 마지막 stage는 artifact가 모였다고 green이 되는 게 아니라, QA가 candidate를 확인하고 PM이 마지막 stage green을 기록한 뒤에야 queue closing을 열 수 있다.** 그래서 마지막 턴도 여전히 작은 stage 문장으로만 닫아야 한다.
