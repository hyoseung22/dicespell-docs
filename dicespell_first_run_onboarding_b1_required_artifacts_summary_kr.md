# DiceSpell 첫 런 온보딩 `B-1 library` Required Artifacts 요약

## 3줄 요약
- 이 문서는 `B-1 library` 첫 stage가 review-ready가 되기 전 **정확히 어떤 artifact가 어떤 역할을 해야 하는지**를 빠르게 읽기 위한 readable companion이다.
- 핵심은 문서를 더 늘리는 것이 아니라, 첫 library turn의 제출물을 `manifest 1개 + log 1개 + note 4개 + capture 2장`의 **같은 library-only 문장**으로 정렬하는 것이다.
- PM / 기획 / 프로그래머 / UI / 아트 / QA가 3~5분 안에 `무엇이 모여야 review-ready인지`, `무엇이 아직 generic이면 보류인지`, `왜 B-2 unlock 전에 exact B-1 artifact bundle이 먼저 필요하지`를 이해해야 한다.

## 한 줄 목표
B-1 첫 stage를 `파일 있음`이 아니라 **artifact alignment 있음**으로 판정하게 만든다.

## 왜 이 문서를 먼저 보나
kickoff packet과 review packet이 있어도 실제 첫 B-1 turn 직전엔 보통 이런 drift가 생긴다.

- `acceptance log만 있으면 거의 됐겠지`
- `캡처 한 장만 있어도 설명은 되지 않을까`
- `state diff note는 나중에 길게 쓰면 되겠지`
- `art sanity는 다음 턴에 정리해도 되겠지`
- `PM 메모는 그냥 온보딩 진행이라고 써도 되겠지`

이 요약 문서는 그 drift를 막기 위해, B-1 stage를 **무엇을 만들까**보다 **무엇이 정렬돼야 QA pass와 PM green이 가능하나** 기준으로 먼저 읽히게 만든다.

---

## B-1 required artifacts 한눈표

| 파일 | 무엇을 증명하나 | owner 핵심 역할 |
| --- | --- | --- |
| `manifest.md` | 지금 stage가 `B-1 library`이고 B-2 unlock 전 단계라는 사실 | stage boundary / next allowed step 고정 |
| `logs/...qa-acceptance-log.md` | B1-A1~A5 / recovery 3종 / CTA 비차단 기록 | QA가 library-only pass 근거 고정 |
| `notes/...programmer-state-diff.md` | `seenBookSelectPrimer`와 render hook 경계 | 프로그래머가 library-only diff 언어 잠금 |
| `notes/...ui-hierarchy-note.md` | hero-primer 위치와 CTA 비차단 | UI가 first-open / return-state 읽힘 판정 |
| `notes/...art-token-sanity.md` | badge/accent/highlight drift 없음 | 아트가 `안내` 톤과 비범위 고정 |
| `notes/...pm-boundary-note.md` | battle/reward/shop/ending 미오픈 유지 | PM이 B-1과 B-2 기록 분리 |
| `captures/...first-open.png` | 첫 진입 primer on 상태 증명 | UI + QA가 첫 스크린 읽힘 증명 |
| `captures/...return-state.png` | 재진입/재노출 정책 증명 | UI + QA가 recovery 언어 증명 |

---

## 가장 중요한 판정 기준

### review-ready로 볼 수 있는 경우
- artifact 8종이 모두 B-1 archive 안에 있다.
- `manifest`, `state diff`, `boundary note`, `acceptance log`가 모두 `B-1 library`와 `library-only` 문장을 쓴다.
- `first-open / return-state` 캡처 두 장이 모두 있고 hierarchy note가 두 장을 근거로 판정한다.
- art sanity note가 `충분 / 약함 / 과함` 중 하나로 끝나고 대형 자산 논의로 흐르지 않는다.

### 아직 review-ready가 아닌 경우
- 파일은 다 있는데 wording이 generic하다.
- acceptance log는 있는데 state diff가 다른 surface 확장을 암시한다.
- 캡처가 1장뿐이라 재노출 정책이 안 보인다.
- PM boundary note가 `온보딩 진행` 같은 큰 문장을 쓴다.

### 한 줄 판정
B-1 첫 stage는 **artifact completeness + artifact alignment**가 둘 다 있을 때만 review-ready다.

---

## 역할별 결론

| 역할 | 이번 문서에서 바로 가져갈 결론 |
| --- | --- |
| 프로그래머 | state diff는 `seenBookSelectPrimer + hero-primer + highlight 2곳`까지만 적고 다른 surface flag는 섞지 않는다 |
| UI | first-open / return-state는 예쁜 샷이 아니라 다른 질문을 답하는 두 장 세트다 |
| 아트 | art sanity note는 신규 asset backlog가 아니라 `안내` 톤 충분성 메모다 |
| QA | 파일 존재만으로는 부족하고 wording alignment까지 봐야 pass 후보다 |
| PM/기획 | B-1 green은 B-2 unlock 직전 문장일 뿐 온보딩 전체 green이 아니다 |

---

## immediate next actions
1. `B-1 live kickoff packet` 다음에 이 요약을 읽고 artifact 8종부터 먼저 확인한다.
2. `manifest`, `acceptance log`, `state diff`, `boundary note`가 같은 `B-1 library` 문장을 쓰는지 먼저 본다.
3. first-open / return-state 캡처와 hierarchy note를 같이 채운다.
4. art sanity note에는 충분성과 비범위를 함께 적는다.
5. QA는 wording alignment까지 확인한 뒤에만 `B-1 QA pass`를 적고, PM은 그 뒤에만 `B-1 library green`과 `다음 단계는 B-2 battle만 연다`를 기록한다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **`B-1 evidence가 모였다`를 `B-1 artifact 8종이 같은 library-only 문장으로 정렬됐다`로 바꾸는 것**이다. 다음 온보딩 turn은 이 기준으로 artifact 8종을 채우고, 그 뒤에만 QA pass와 B-2 unlock을 말해야 한다.
