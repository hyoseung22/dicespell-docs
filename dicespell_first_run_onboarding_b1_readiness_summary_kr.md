# DiceSpell 첫 런 온보딩 `B-1 library` Readiness Summary

## 3줄 요약
- B-1 문서는 이미 충분하다. 남은 공백은 새 primer 설계가 아니라 **`그래서 지금 실제로 누가 B-1을 시작해도 되고, 누가 아직 기다려야 하지?`**를 한 장으로 보는 일이다.
- 이 문서는 `bootstrap-ready -> archive-in-progress -> review-ready -> QA pass -> PM green -> B-2 unlock`을 읽기 좋게 압축한 companion이다.
- 핵심은 간단하다. **지금 live-ready는 B-1 하나뿐이고, B-2/B-3/B-4는 아직 locked**다.

## 한 줄 목표
B-1 첫 실전 turn을 `문서 세트`가 아니라 **시작 가능 / 대기 / 제출 / 기록 보드**로 읽게 만든다.

---

## 왜 지금 필요한가
B-1에는 이미 아래 문서가 있다.

- B-1 library packet
- live kickoff
- archive bootstrap / artifact stub / manifest / semantic anchor
- required artifacts / filled archive examples / handoff scorecard / review packet
- archive file map / document stack

문제는 문서가 부족한 게 아니다.
실전 직전엔 오히려 이런 질문이 남는다.

1. 지금 정말 B-1만 열 수 있나?
2. `archive를 만들었다`와 `review-ready`와 `green`은 정확히 어디서 갈라지나?
3. QA/PM은 언제부터 어떤 문장을 써도 되나?
4. B-2 battle은 왜 아직 열면 안 되나?

이번 문서는 그 공백을 **B-1 only live-ready 보드**로 닫는다.

---

## first screen 결론

### 가장 짧은 답
- **지금 실제로 열 수 있는 stage**: `B-1 library`
- **지금 금지**: `B-2 battle`, `B-3 reward/shop`, `B-4 ending` 선착수
- **지금 해야 하는 일**: `docs/artifacts/YYYYMMDD/b1-library/` archive를 먼저 만들고 `library-only` evidence를 채운다
- **B-2 unlock 시점**: `B-1 QA pass` 뒤 `PM green`이 기록된 다음

### 한 줄 판정 규칙
첫 런 온보딩은 지금 **`B-1 only live-ready, B-2/B-3/B-4 locked`** 상태다.

---

## B-1 readiness 사다리

| 단계 | 의미 | 지금 할 일 | 아직 쓰면 안 되는 문장 |
| --- | --- | --- | --- |
| `bootstrap-ready` | archive 바닥을 여는 단계 | root / manifest / stub 생성 | `green`, `B-2 unlock` |
| `archive-in-progress` | live evidence를 채우는 단계 | state diff, hierarchy, art sanity, boundary, capture 2장 작성 | `QA pass`, `green` |
| `review-ready` | artifact alignment를 선언하는 단계 | library-only wording 정렬 확인 | `B-2 unlock` |
| `QA pass` | acceptance/recovery를 검수하는 단계 | B1-A1~A5 + recovery 3종 + CTA 비차단 확인 | `온보딩 진행`, `green` |
| `PM green` | B-1 하나만 닫았다고 기록하는 단계 | `B-1 library green` 문장 기록 | `B-2도 ready`, `온보딩 전체 진행` |
| `B-2 unlock` | 다음 stage를 하나만 여는 단계 | `다음 단계는 B-2 battle만 연다` 기록 | B-3/B-4 동시 unlock |

---

## 역할별로 보면

### 프로그래머
- 지금 여는 범위는 `seenBookSelectPrimer`, `renderGrimoireShelf()`, `openingPlan/caution`, library archive evidence뿐이다.
- B-2/B-3/B-4는 참고 문서이지 실행 범위가 아니다.

### UI
- 지금 필요한 것은 `first-open / return-state` 두 장과 hierarchy note다.
- battle hero-primer나 reward/shop strip은 아직 비범위다.

### 아트
- 이번 턴 역할은 `primer-library` badge/accent/highlight sanity다.
- 신규 대형 자산이나 다음 surface token 제안은 아직 열지 않는다.

### QA
- `review-ready`, `QA pass`, `PM green`을 같은 의미로 쓰지 않는다.
- artifact 8종이 모두 있어도 wording drift가 있으면 pass를 미룬다.

### PM/기획
- 지금 남길 수 있는 green 문장은 B-1 하나뿐이다.
- `온보딩 진행`, `library/battle 동시 준비`, `거의 완료` 같은 큰 문장은 금지다.

---

## 기록 문장 기준

| 단계 | 권장 문장 |
| --- | --- |
| archive-in-progress | `B-1 archive-in-progress: library-only starter bundle과 first-open/return-state evidence를 채우는 중이며 battle/reward/shop/ending primer는 아직 열지 않았다.` |
| review-ready | `B-1 review-ready: seenBookSelectPrimer, hero-primer, openingPlan/caution highlight, library-only archive/evidence 초안을 묶었고 다른 surface primer는 아직 열지 않았다.` |
| QA pass | `B-1 QA pass: B1-A1~A5와 recovery 3종, CTA 비차단, library-only boundary 유지가 확인됐다.` |
| PM green | `B-1 library green: seenBookSelectPrimer, library.book-select hero-primer, openingPlan/caution highlight, library acceptance/recovery evidence를 고정했고 battle/reward/shop/ending primer는 아직 열지 않았다.` |
| B-2 unlock | `다음 단계는 B-2 battle만 연다.` |

---

## 리스크 & 후속과제

| 리스크 | 이번 문서의 대응 |
| --- | --- |
| `archive를 만들었다 = 거의 green`처럼 읽히는 위험 | `archive-in-progress / review-ready / QA pass / PM green / unlock`을 분리했다 |
| UI/아트가 B-2 visual 메모를 너무 일찍 여는 위험 | B-2/B-3/B-4를 locked로 못 박았다 |
| PM이 큰 진행 문장을 다시 쓰는 위험 | 복붙용 단계 문장을 readiness board 안에 고정했다 |
| B-2 unlock 문장이 빠지는 위험 | unlock을 별도 단계로 분리했다 |

---

## 즉시 다음 액션
1. 다음 B-1 turn은 `readiness board -> live kickoff -> required artifacts -> review packet` 순서로 연다.
2. `docs/artifacts/YYYYMMDD/b1-library/` root를 먼저 만들고 `archive-in-progress` 문장부터 적는다.
3. `PM green` 전에는 B-2를 열지 않는다.
