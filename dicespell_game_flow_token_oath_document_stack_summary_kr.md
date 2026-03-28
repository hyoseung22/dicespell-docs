# DiceSpell `Run Table` Oath Banner Document Stack Summary

## 3줄 요약
- Oath 문서는 이제 충분하다. 남은 공백은 새 설계가 아니라 **`어떤 문서가 실제 계약이고, 어떤 문서는 빠른 읽기나 예시인지`**를 한눈에 구분하는 일이다.
- 이 문서는 Oath 문서 묶음을 `baseline / source packet / readable companion / archive evidence / signoff` 5층으로 다시 묶어, 프로그래머 / UI / 아트 / QA / PM이 같은 stage를 다른 문서 층위로 오해하지 않게 만든 readable companion이다.
- 핵심은 간단하다. **packet이 먼저, example은 나중, green 문장은 마지막, queue closing은 oath green 뒤**다.

## 한 줄 목표
Oath 첫 실전 turn에서 누구도 summary나 exemplar만 보고 착수/green 판단을 내리지 않게 만든다.

---

## 왜 필요한가
Oath는 이미 아래 문서까지 갖췄다.

- 범위: `oath banner stage packet`
- 착수: `oath live kickoff`
- locator: `oath semantic anchor`
- archive 경로: `oath archive file map`
- artifact 정의: `oath required artifacts`
- filled wording 예시: `oath filled archive examples`
- owner 제출 순서: `oath handoff scorecard`
- signoff: `oath candidate review`
- 다음 단계 guard: `queue closing packet`

문제는 이제 `문서 부족`이 아니라 `문서 역할 혼선`이다.

---

## 문서 층위 한 장 정리

| 층위 | 의미 | 대표 문서 | 기억할 점 |
| --- | --- | --- | --- |
| baseline | 지금 어떤 stage가 열려 있는지 | `source-of-truth map`, `gap ledger`, `queue readiness board` | Oath가 지금 live-ready인지 먼저 확인한다 |
| source packet | 실제 계약 문서 | `oath banner stage`, `live kickoff`, `semantic anchor`, `archive file map`, `required artifacts` | 착수와 범위는 여기서 잠근다 |
| readable companion | 빠른 읽기용 | 각 `*_summary_kr.md` | 이해를 돕지만 계약 문서를 대체하지 않는다 |
| archive evidence | 실제 제출물 예시 | `filled archive examples`, `examples/oath-banner/` | wording density reference이지 현재 status 승격 문서가 아니다 |
| signoff | 제출 순서/최종 기록 | `oath handoff scorecard`, `oath candidate review`, `queue closing packet` | candidate / green / queue closing unlock은 여기서 분리한다 |

---

## 역할별 가장 짧은 읽기 순서

### 프로그래머
`source-of-truth map -> gap ledger -> queue readiness board -> oath banner stage -> live kickoff -> semantic anchor -> archive file map -> required artifacts -> filled examples`

- source packet을 먼저 읽는다.
- example archive는 wording sanity 확인용이다.
- green/queue closing 문장은 review 전에는 쓰지 않는다.

### UI
`oath required artifacts -> oath filled archive examples -> oath handoff scorecard -> oath candidate review`

- capture는 질문/판정이 함께 있어야 한다.
- 예쁜 ending 샷이 아니라 overview / banner-focus 증거가 목적이다.

### 아트
`oath required artifacts -> oath filled archive examples -> oath handoff scorecard`

- starter sanity는 작은 충분성 note다.
- ending shell, reward/shop side family, queue closing 공용 자산은 아직 비범위다.

### QA
`oath required artifacts -> oath filled archive examples -> oath handoff scorecard -> oath candidate review -> queue closing packet`

- exemplar는 candidate 어조 참고용이다.
- `candidate`, `green`, `queue closing review-only unlock`은 서로 다른 단계다.

### PM/기획
`oath summary -> oath banner stage packet -> oath handoff scorecard -> oath candidate review -> queue closing packet`

- summary는 빠른 이해용이다.
- 최종 green / queue closing unlock은 source packet + signoff 문장으로만 남긴다.

---

## 이 문서가 잠그는 핵심 규칙

1. **packet이 계약 문서다.** summary는 빠른 보기다.
2. **example archive는 reference다.** 현재 honest status보다 큰 문장을 먼저 복사하면 안 된다.
3. **scorecard와 review packet이 signoff 문서다.** candidate / green / queue closing review-only unlock은 여기서만 분리한다.
4. **queue closing은 여전히 Oath green 뒤 review-only로만 열린다.**

---

## 리스크 & 후속과제

- summary가 source packet보다 먼저 읽히는 순간 Oath boundary가 약해질 수 있다.
- filled archive examples가 있어도 implementer가 example을 현재 status처럼 쓰면 `candidate`, `green`, `queue closing unlock`이 조기 사용될 수 있다.
- 포털/노션 붙여넣기에서는 문서 역할이 더 쉽게 섞이므로, owner별 읽기 순서를 같이 넘겨야 한다.

---

## immediate next actions

1. 다음 Oath live turn은 `oath document stack packet`을 먼저 열어 문서 층위를 잠근 뒤 시작한다.
2. 프로그래머는 source packet을 먼저, UI/아트는 required artifacts와 exemplar를 먼저, QA/PM은 scorecard/review/queue closing을 마지막에 본다.
3. final green 문장은 여전히 `oath candidate review packet`과 `queue closing packet`에서만 가져온다.
