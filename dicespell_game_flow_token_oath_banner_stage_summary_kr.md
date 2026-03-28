# DiceSpell `Run Table` Oath Banner 세 번째 스테이지 요약

## 3줄 요약
- 이 문서는 `battle-stakes-plaque green` 뒤 마지막 실제 refinement 턴을 **`banner / result / carry role hook 잠금 + banner-first hierarchy 검수 + Oath Banner Starter sanity + oath archive-ready candidate`**까지만 좁히는 readable companion이다.
- 핵심은 새 ending/overrun 화면을 더 설계하는 것이 아니라, `oath-banner` stage 하나만 한 archive와 한 green 문장으로 닫게 만드는 것이다.
- PM / 기획 / UI / 아트 / QA가 3~5분 안에 `왜 oath가 마지막 stage인지`, `무엇이 이번 턴 완료선인지`, `어떤 문장 전에는 ending/overrun 전체 green을 쓰면 안 되는지`를 바로 이해해야 한다.

## 한 줄 목표
battle 다음 refinement 턴을 `oath banner only`로 고정해, queue와 ownership 문서가 실제 마지막 stage 실행으로 이어지게 만든다.

## 왜 이 문서를 먼저 보나
battle green 뒤 실제 oath 턴 직전엔 보통 이렇게 흐르기 쉽다.

- `마지막 stage니까 ending helper도 같이 보면 되지 않을까?`
- `overview 캡처만 있으면 거의 된 거 아닌가?`
- `starter asset sanity가 부족하니 이번에 ending shell 전체도 같이 열까?`

이 요약 문서는 그 drift를 막기 위해, oath stage를 **무엇을 하는 턴인지보다 무엇을 아직 하지 않는 턴인지** 기준으로 먼저 읽히게 만든다.

---

## 이번 stage의 가장 짧은 정의

### 이번에 닫는 것
- `Overrun Oath` root / `banner | result | carry` role hook
- `oath-1440-overview.png`
- `oath-1440-banner-focus.png`
- `Oath Banner Starter` sanity note
- oath stage archive와 handoff line 초안

### 이번에 아직 안 닫는 것
- ending helper / reward/shop side family 전체 개편
- battle stakes 재보정
- overrun/ending boundary 재설계
- onboarding / overrun 문서 축 재개방
- `Run Table 전체 완료` 같은 큰 문장

### 한 줄 판정
oath stage는 `banner > result > carry hierarchy가 먼저 읽히는가`를 닫는 턴이지, ending 전체를 다듬는 턴이 아니다.

---

## 누가 무엇을 보면 되나

| 역할 | 이번 문서에서 바로 가져갈 결론 |
| --- | --- |
| 프로그래머 | oath live edit은 `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta`까지만 잠근다 |
| UI | `overview`와 `banner-focus` 두 캡처만으로 banner-first verdict를 남긴다 |
| 아트 | `Oath Banner Starter` sanity만 본다. ending 배경/공용 shell 전체 리뉴얼은 아직 금지다 |
| QA | required artifacts가 stage archive 안에 다 모였을 때만 `oath archive-ready candidate`를 쓴다 |
| PM/기획 | `oath-banner green` 전에는 `Run Table complete`나 ending/overrun 전체 green을 쓰지 않는다 |

---

## 이번 stage evidence 묶음
- `docs/artifacts/run-table-token-delivery/oath-banner/manifest.md`
- `captures/oath-1440-overview.png`
- `captures/oath-1440-banner-focus.png`
- `notes/hook-manifest.md`
- `notes/density-review.md`
- `notes/handoff-line.md`
- `checks/dom-sanity.md`
- `assets/starter-asset-list.md`

이 묶음이 stage archive 안에 같은 이름으로 있어야 QA가 후보 green을 말할 수 있다.

---

## stage signoff 한눈표

| 단계 | 누가 적나 | 의미 |
| --- | --- | --- |
| `bootstrap-ready` | 프로그래머 | oath 폴더 + manifest + starter file set이 생겼다 |
| `archive-in-progress` | 프로그래머 / UI | 첫 hook note 또는 첫 capture가 들어왔다 |
| `archive-ready candidate` | QA | oath required artifacts가 다 채워졌다 |
| `oath-banner green` | PM/기획 | QA 확인까지 끝나 마지막 stage를 stage명 기준으로 닫을 수 있다 |

---

## 리스크 & 후속과제
- oath stage가 다시 `banner + ending helper + reward/shop side family + overrun boundary`로 커지면 마지막 큐가 무너진다.
- overview/focus 캡처가 oath stage archive 밖 임시 경로에 있으면 green 후보로 보지 않는다.
- starter asset sanity를 ending 전체 리디자인 제안으로 확장하면 oath green이 불필요하게 늦어진다.
- `oath green`을 `ending green`이나 `Run Table complete`처럼 기록하면 마지막 stage 경계가 무너진다.

---

## immediate next actions
1. `token source-of-truth map -> token refinement queue -> battle stage packet -> oath stage packet` 순서로 읽는다.
2. battle green evidence를 먼저 확인한다.
3. oath stage archive를 먼저 만든다.
4. hook note / DOM sanity / overview/focus capture / starter asset sanity를 채운다.
5. QA가 `oath archive-ready candidate`를 적기 전에는 큰 queue closing 문장을 쓰지 않는다.
6. PM은 `oath-banner green` 문장만 남기고 필요하면 그 다음에만 follow-up packet을 연다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **`oath가 마지막 stage라는 사실`을 `oath banner만 닫는 실제 실행 규칙`으로 바꾸는 것**이다. 다음 Run Table refinement 턴은 이 stage packet 기준으로 `banner / result / carry` 읽힘만 닫고, ending/overrun 전체 재설계는 여전히 stage 밖으로 남겨야 한다.
