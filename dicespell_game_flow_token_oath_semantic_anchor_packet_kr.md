# DiceSpell `Run Table` Oath Banner semantic anchor packet

## 3줄 요약
- 이 문서는 `oath live kickoff`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도, **`좋아, 그럼 실제 oath live edit 직전에 정확히 어느 함수 / DOM / capture 질문을 같은 책임으로 다시 찾아야 하지?`**를 닫는 source-of-truth다.
- 핵심은 새 ending 화면을 더 설계하는 것이 아니라, `renderEnding(summary)` / `run-ending-stage` / `ending-banner` / `ending-stage-grid` / `ending-main-column` / `ending-side-column` / `continue-overrun` CTA를 **oath semantic zone 기준 계약**으로 다시 묶어 line drift가 있어도 stage drift가 생기지 않게 만드는 것이다.
- 목표는 다음 implementer / UI / 아트 / QA / PM이 oath 첫 live edit 전에 `semantic anchor 재확인`을 더 이상 감으로 하지 않고, **같은 locator 이름 · 같은 non-change line · 같은 evidence 질문**으로 시작하게 만드는 것이다.

**한 줄 목표:** `oath-banner` 첫 live turn의 semantic locator를 `surface root / banner-result-carry role / CTA context / ending boundary / banner-first proof` 5축으로 고정해, archive/stub가 준비된 뒤 실제 edit도 끝까지 `oath-only` stage language로 좁게 유지되게 만든다.

---

## 왜 이 문서가 필요한가
지금 oath final-stage 축은 거의 충분하다.

이미 있는 것:
- stage 범위: `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- 첫 10분 순서: `docs/dicespell_game_flow_token_oath_live_kickoff_packet_kr.md`
- exact 제출물: `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- starter sentence: `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`
- field contract: `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- green/signoff: `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- queue closing 경계: `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`
- 문서 위계: `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- 현재 미완료 gap: `docs/dicespell_game_flow_token_gap_ledger_kr.md`

그런데 실제 oath 첫 live turn 직전에는 아직 아래가 남아 있었다.

1. live kickoff packet은 `banner semantic locator 재확인`을 요구하지만, **무엇을 oath semantic anchor로 볼지**는 oath stage 언어로 한 장에 다시 압축돼 있지 않았다.
2. implementer / UI / QA는 모두 `banner / result / carry`를 말하지만, 실제 코드에서는 `renderEnding(summary)`, `.ending-banner`, `.ending-stage-grid`, `.ending-main-column`, `.ending-side-column`, `continue-overrun` CTA, ending summary list가 가까이 붙어 있어 locator 이름이 약하면 다시 `이 김에 ending helper도`, `side family도 같이`, `queue closing wording도 미리` 같은 drift가 생기기 쉽다.
3. oath stage는 battle보다 화면 의미 경계가 더 쉽게 흐려진다. 왜냐하면 ending 결과, carry-forward, overrun CTA, scene flavor, queue closing wording이 모두 인접해 있기 때문이다. 그래서 line number보다 **semantic family**를 계약으로 다시 잠글 필요가 있다.
4. 이 공백이 남아 있으면 archive / stub / required artifacts는 stage-specific인데 실제 code review와 UI sanity만 다시 generic ending memo로 돌아가, `oath-banner`라는 stage boundary가 가장 먼저 약해진다.

즉 이 문서는 새 방향 문서가 아니라, **oath kickoff 뒤 첫 live edit 전 semantic locator를 같은 문장으로 재확인하게 만드는 마지막 좁은 handoff packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. semantic anchor map`, `3. code locator rule` | `oath banner stage packet`, `oath live kickoff`, `oath required artifacts` | `renderEnding(summary)` / `.run-ending-stage` / `.ending-banner` / `.ending-stage-grid` / `.ending-main-column` / `.ending-side-column` / `continue-overrun` 주변만 다시 찾고 queue closing hook, ending helper 전체 재설계, reward/shop side family는 이번 stage에서 열지 않는다 |
| UI | `2. semantic anchor map`, `4. DOM/hierarchy anchor`, `5. capture anchor` | `oath required artifacts`, `oath candidate review` | banner-first hierarchy와 banner/result/carry role 관계만 판정하며 generic ending density나 reward/shop 유사도는 이번 stage 바깥이다 |
| 아트 | `4. DOM/hierarchy anchor`, `6. token sanity anchor` | `token anchor packet`, `oath required artifacts` | `Oath Banner Starter` sanity는 oath banner zone 안에서만 확인하며 ending shell 전체나 overrunEvent scene tone을 같이 묶지 않는다 |
| QA | `2. semantic anchor map`, `5. capture anchor`, `7. review-ready evidence rule` | `oath candidate review`, `queue readiness board` | candidate-ready는 first-glance / role hierarchy / CTA boundary 질문이 같은 oath-only 문장으로 묶였을 때만 성립한다 |
| PM/기획 | `0. first screen 결론`, `7. review-ready evidence rule`, `8. stop/go` | `queue readiness board`, `oath candidate review` | semantic anchor 재확인은 kickoff 층위이고, green / queue closing 문장은 여전히 evidence 뒤에만 쓴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- oath 첫 live edit 직전에 정확히 어떤 함수 / DOM / 캡처를 다시 찾아야 하지?
- line drift가 생겨도 `oath-only` stage를 어떻게 유지하지?
- 무엇을 semantic anchor라고 부르고, 무엇은 아직 금지 zone이지?

### 가장 짧은 답
- oath semantic anchor는 **surface root / banner-result-carry role / CTA context / ending boundary / first-glance·banner-focus evidence** 다섯 묶음이다.
- 프로그래머는 `renderEnding(summary)`, `.run-ending-stage`, `.ending-banner`, `.ending-stage-grid`, `.ending-main-column`, `.ending-side-column`, `button[data-action="continue-overrun"]` 주변만 다시 찾는다.
- UI / QA는 `Overrun Oath banner -> result cluster -> carry column -> continue-overrun CTA` 위계와 `banner`의 즉시 읽힘, `result/carry`의 역할 분리를 확인한다.
- `renderRouteBoard()`, `renderBattle(summary)`, reward/shop side family, `Run Table token queue closing` wording, overrunEvent scene shell은 oath semantic zone 바깥이다.

### 한 줄 판정 규칙
oath semantic anchor 재확인은 **locator를 다시 찾는 일**이지 **ending 전체 범위를 다시 설계하거나 queue closing까지 미리 여는 일**이 아니다.

---

## 1. core structure

## 3줄 요약
- oath semantic anchor는 `렌더 루트`, `role hook`, `CTA`, `경계`, `증거 질문` 5축으로 나뉜다.
- 각 축마다 `무엇을 찾는가`, `왜 찾는가`, `같이 열면 안 되는 주변 책임`, `어느 live note에 기록하는가`가 따로 있다.
- 이 문서의 역할은 implementer / UI / QA가 같은 locator 이름을 쓰게 만드는 것이지, 새로운 ending feature spec을 만드는 것이 아니다.

### 한 줄 목표
semantic anchor를 `grep 결과`가 아니라 `oath stage 경계를 다시 잠그는 짧은 계약`으로 만든다.

---

## 2. semantic anchor map

| anchor family | canonical locator | 왜 다시 찾는가 | 반드시 기록할 live file | 같이 열면 안 되는 것 |
| --- | --- | --- | --- | --- |
| Surface root anchor | `renderEnding(summary)`, `.run-ending-stage`, `Overrun Oath`, 목표 hook `data-run-surface="overrun-oath"` | oath stage를 결과 화면 전체가 아니라 oath banner family 기준으로 다시 잠그기 위해 | `notes/hook-manifest.md` | `renderRouteBoard()`, `renderBattle(summary)`, overrunEvent scene shell, queue closing hook 선추가 |
| Role anchor | `.ending-banner`, `.ending-main-column`, `.ending-side-column`, 목표 hook `data-oath-role="banner|result|carry"` | banner / result / carry 읽힘을 oath-only vocabulary로 고정하기 위해 | `notes/hook-manifest.md`, `checks/dom-sanity.md` | reward/shop side family, ending summary 전체, generic stat card를 oath 증거처럼 쓰는 것 |
| CTA context anchor | `button[data-action="continue-overrun"]`, CTA cluster, 목표 hook `data-overrun-cta` | carry와 CTA가 같은 family 안에서 읽히되 banner를 덮지 않게 만들기 위해 | `notes/hook-manifest.md`, `checks/dom-sanity.md` | queue closing wording, ending total recap, follow-up reopen note를 CTA 근처에 미리 섞는 것 |
| Ending boundary anchor | ending summary list, trophy/result rows, carry-forward side column, overrun/onboarding/queue 관련 reference 문장 | result/carry는 이번 stage 핵심이지만 ending helper 전체와 queue closing은 비범위라는 경계를 메모에 먼저 박기 위해 | `notes/hook-manifest.md`, `manifest.md` | ending helper 구조 재설계, reward/shop side family 재정리, overrun boundary 재해석 |
| Evidence anchor | `captures/oath-1440-overview.png`, `captures/oath-1440-banner-focus.png`, `notes/density-review.md` | candidate-ready가 그냥 예쁜 ending 캡처가 아니라 같은 oath semantic 질문 두 개를 답하게 만들기 위해 | `captures/*`, `notes/density-review.md`, `notes/handoff-line.md` | generic `ending-latest.png`, queue closing preview, reward/shop close-up |

### 한 줄 결론
oath anchor는 `함수 이름 몇 개`만이 아니라 **code + DOM + evidence question**이 함께 묶인 final-stage locator다.

---

## 3. code locator rule

## 3줄 요약
- 프로그래머는 line number가 아니라 `oath root`, `role`, `CTA`, `boundary` 네 책임을 먼저 다시 찾는다.
- 이 단계의 진짜 목적은 `무엇을 바꾸나`보다 `무엇을 아직 안 바꾸나`를 live note에 먼저 적는 것이다.
- line drift가 생겨도 semantic family가 같으면 stage는 그대로고, queue closing / overrunEvent / reward-shop family까지 넓어지면 stage drift다.

### one-line goal
`renderEnding(summary)` / `.run-ending-stage` / `.ending-banner` / `.ending-main-column` / `.ending-side-column` / `button[data-action="continue-overrun"]`까지를 oath semantic zone으로 다시 찾고, 그 밖의 battle/atlas surface, queue closing, reward-shop family, ending helper 대확장은 이번 turn에 닫아 둔다.

### 찾은 뒤 note에 남길 최소 5줄
1. `root family:` oath surface가 실제로 시작하는 render zone과 root hook
2. `role family:` banner / result / carry를 읽게 하는 class / target data attribute / title zone
3. `cta family:` continue-overrun CTA가 carry family 안에서 어디에 붙고 어디서 boundary를 넘지 말아야 하는지
4. `boundary family:` ending helper 전체, reward/shop side family, queue closing wording은 이번 stage 비범위라는 선언
5. `non-change line:` battle/atlas hook, overrunEvent scene shell, queue closing packet wording, ending helper 구조 리라이트는 untouched

### 허용되는 기록 예시
> `renderEnding(summary)` semantic zone에서는 ".ending-banner"와 `.ending-main-column` / `.ending-side-column`의 banner / result / carry 읽힘만 다루고, queue closing wording·ending helper 재설계·reward/shop side family 비교는 이번 stage에서 열지 않는다.

> `oath root`와 `role` note에서는 `banner > result > carry` 위계를 review 가능한 DOM vocabulary로만 고정하고, overrunEvent scene tone이나 queue closing follow-up 문장은 비범위로 남긴다.

### 금지되는 기록 예시
- `이 김에 ending helper도 같이 다듬자`
- `reward/shop side family도 이번 pass에 맞추자`
- `queue closing wording도 여기서 미리 넣자`
- `Overrun Oath 전반 shell을 공용 helper로 정리 예정`

---

## 4. DOM / hierarchy anchor

## 3줄 요약
- UI / 아트는 oath에서 더 화려한 ending shell을 만드는 것이 아니라, oath surface가 여전히 banner 중심 문턱으로 읽히는지 본다.
- semantic anchor는 결국 DOM hierarchy와 density에서 확인돼야 한다.
- 이 단계에서 필요한 건 새 shell이 아니라 `어디까지가 oath banner first-glance이고 어디부터가 result/carry/CTA인가`의 경계 확인이다.

### one-line goal
`Overrun Oath banner -> result cluster -> carry column -> continue-overrun CTA` 위계를 유지한 채 banner/result/carry만 oath stage 핵심 읽힘으로 잠근다.

### UI / 아트가 다시 확인할 DOM zone
| DOM zone | oath에서 묻는 질문 | pass 기준 | fail 예시 |
| --- | --- | --- | --- |
| `.run-ending-stage` / headline zone | surface가 바로 `Overrun Oath`와 문턱 문맥으로 읽히는가 | surface 진입 직후 banner 의미가 result list/CTA보다 먼저 읽힌다 | 결과표/버튼 인상이 더 먼저 튄다 |
| `.ending-banner` | main banner가 oath 장면의 주어로 읽히는가 | title / tone / accent가 result/carry보다 먼저 잡힌다 | banner가 그냥 상단 라벨처럼 사라진다 |
| `.ending-main-column` | result cluster가 banner의 보조 결과로 읽히는가 | 결과는 banner 아래에서 설명 역할을 한다 | result가 별도 dashboard처럼 banner보다 크게 읽힌다 |
| `.ending-side-column` | carry cluster와 CTA가 banner를 덮지 않고 이어지는가 | carry/CTA는 오른쪽 보조 column으로 남는다 | carry/CTA가 banner와 같은 1순위로 튄다 |
| `button[data-action="continue-overrun"]` | CTA가 carry 맥락 안에서 읽히는가 | CTA는 다음 단계 약속으로 읽히고 queue closing 문장을 선점하지 않는다 | CTA가 `Run Table complete` 같은 종결감으로 읽힌다 |

### role-specific handoff points
- UI는 hierarchy note를 `무엇이 먼저 읽히는가` 중심으로 남긴다.
- 아트는 `Oath Banner Starter` token이 banner-first 읽힘을 방해하지 않는지만 확인한다.
- 둘 다 ending helper 전체, overrunEvent scene shell, reward/shop side family 재설계 제안은 이번 stage에서 보류한다.

---

## 5. capture anchor

## 3줄 요약
- oath 캡처는 시안 모음이 아니라 semantic anchor의 결과를 증명하는 두 장 세트다.
- overview는 `main banner가 먼저 읽히는가`, banner-focus는 `banner / result / carry 관계가 같은 문장으로 붙는가`를 답해야 한다.
- 이 두 질문이 빠지면 review-ready가 아니라 그냥 예쁜 ending 장면 모음이다.

### one-line goal
overview / banner-focus를 `oath-only semantic question` 두 개로 고정한다.

| capture | 답해야 하는 질문 | 반드시 같이 적을 note | 금지 혼입 |
| --- | --- | --- | --- |
| `captures/oath-1440-overview.png` | 첫 스크린에서 main banner가 result/carry/CTA보다 먼저 읽히는가 | `notes/density-review.md` + `checks/dom-sanity.md` | reward/shop overlay, generic `ending.png`, queue closing preview |
| `captures/oath-1440-banner-focus.png` | banner / result / carry role 관계와 CTA 맥락이 한 장에서 same-family로 읽히는가 | `notes/density-review.md` + `notes/handoff-line.md` | side-only crop, result table glamour shot, overrunEvent scene comparison |

### QA note에 꼭 들어갈 문장
> overview / banner-focus 캡처는 모두 oath banner final-stage 안에서만 기록하며, queue closing preview나 generic ending polish evidence로 재사용하지 않는다.

---

## 6. token sanity anchor

## 3줄 요약
- oath 아트 sanity의 semantic anchor는 `Oath Banner Starter`, `seal/accent`, `banner emphasis`, `carry support tone`뿐이다.
- token sanity는 새 자산 발주 여부가 아니라 현재 stage를 닫아도 되는 최소 충분성 verdict여야 한다.
- oath는 의미 경계가 쉽게 흐려지기 때문에 큰 ending shell 리뉴얼 논의로 커지는 순간 stage drift다.

### one-line goal
oath token sanity를 `banner-first 문턱 유지`라는 단일 질문으로 제한한다.

### 아트 note에 적을 최소 구조
1. `이번 sanity는 Oath Banner Starter / banner emphasis / carry support tone만 확인한다.`
2. `verdict: 충분 / 약함 / 과함`
3. `이번 stage 비범위: ending helper shell, reward/shop side frame, overrunEvent scene art, queue closing badge`

### 금지되는 확장
- `ending side shell도 같은 frame family로 맞추자`
- `reward/shop card도 같이 정리하자`
- `queue closing badge까지 이번에 같이 만들자`
- `Overrun Oath 전체 background package를 새로 열자`

---

## 7. review-ready evidence rule

## 3줄 요약
- semantic anchor 재확인은 review-ready의 선행 조건이지 green 자체가 아니다.
- review-ready는 `locator 메모 + oath-only diff + overview / banner-focus 질문 + boundary note`가 같은 문장을 쓸 때 성립한다.
- locator가 있어도 wording이 generic하거나 queue closing / reward-shop / overrunEvent가 섞이면 아직 bootstrap / in-progress다.

### review-ready로 볼 수 있는 경우
- `notes/hook-manifest.md`가 `surface root / role / CTA / boundary` 4축을 oath-only로 기록한다.
- `checks/dom-sanity.md`가 `.ending-banner`, `.ending-main-column`, `.ending-side-column`, `button[data-action="continue-overrun"]` 존재와 queue closing 비범위를 함께 판정한다.
- `notes/density-review.md`가 overview / banner-focus 두 질문을 `oath-only` 범위로 기록한다.
- `manifest.md`와 `notes/handoff-line.md`가 `queue closing only after oath green`을 유지한다.

### 아직 review-ready가 아닌 경우
- function grep 결과만 있고 semantic family 설명이 없다.
- hierarchy note가 ending helper / reward-shop side family / queue closing wording까지 미리 끌고 온다.
- overview 캡처가 있어도 어떤 semantic question을 답하는지 note에 연결돼 있지 않다.
- battle/atlas helper 메모나 overrunEvent scene tone이 oath note 안에 섞여 있다.

### 한 줄 판정
oath semantic anchor packet의 목적은 **무엇을 찾았는지**보다 **찾고도 범위를 넓히지 않았는지**를 증명하는 것이다.

---

## 8. stop / go 판정

### GO 조건
- archive / stub가 이미 생성돼 있다.
- programmer note가 `renderEnding(summary)`와 role/CTA zone만 다룬다고 적는다.
- UI / QA가 overview / banner-focus 두 질문을 oath-only로 기록한다.
- PM boundary note가 `queue closing review-only unlock 대기`를 유지한다.

### STOP 신호
- ending helper / reward-shop side family 재배치가 banner edit보다 먼저 열린다.
- `Run Table token queue closing` wording이나 follow-up reopen 문장이 note에 들어간다.
- overview 캡처가 banner보다 result table이나 CTA 회고로 변한다.
- starter sanity가 ending shell 리디자인 제안으로 커진다.

### STOP 이후 다음 행동
1. semantic anchor note부터 `oath-only`로 되돌린다.
2. 이번 turn에서 어떤 locator가 과하게 열렸는지 boundary note에 적는다.
3. 필요하면 이번 stage는 `archive-in-progress`로 남기고 queue closing은 열지 않는다.

---

## 9. 역할별 handoff 포인트

### 프로그래머
- semantic anchor note는 code diff보다 먼저 남긴다.
- `root / role / cta / boundary / non-change` 다섯 줄이면 충분하다.
- queue closing / ending helper / reward-shop family / overrunEvent scene까지 넓히기 시작하면 oath 범위를 벗어난다.

### UI
- hierarchy note는 `banner first-glance`와 `result/carry support`만 본다.
- generic ending polish는 다음 stage 또는 별도 backlog다.
- overview / banner-focus 질문을 note에 먼저 써 두고 캡처를 채운다.

### 아트
- token sanity는 Oath Banner Starter 세트에 한정한다.
- 신규 대형 자산은 이번 stage pass 조건이 아니다.
- `banner가 먼저 읽힌다`, `carry가 보조 family로 남는다` 같은 문장을 남기는 쪽이 더 중요하다.

### QA
- overview / banner-focus 질문을 같은 로그 안에 묶는다.
- `oath-only boundary 유지`가 빠지면 pass를 미룬다.
- generic ending shot이나 queue closing preview는 review-ready 근거가 아니다.

### PM/기획
- semantic anchor 재확인은 kickoff 메모 층위다.
- green / unlock 문장은 evidence 정렬 뒤에만 쓴다.
- `Overrun Oath 진행`, `ending 거의 완료`, `Run Table 완료` 같은 큰 문장은 계속 금지한다.

---

## 10. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| line drift 때문에 implementer가 oath semantic zone을 넘어 ending helper / reward-shop side family / queue closing wording까지 같이 grep하는 위험 | oath final-stage가 다시 큰 ending polish 묶음으로 부푼다 | semantic family를 code / DOM / evidence 질문 단위로 고정 | oath 첫 live edit 직전 |
| hierarchy note가 result/carry를 넘어 generic ending density 회고로 커지는 위험 | `banner > result > carry` stage 핵심이 약해진다 | hierarchy anchor를 `.ending-banner`, `.ending-main-column`, `.ending-side-column`, CTA context로 한정 | programmer review-ready 제출 시 |
| overview / banner-focus 캡처가 generic ending shot으로 흐르는 위험 | QA candidate가 visual 참고 수준으로 약해진다 | capture anchor를 질문 기반 2장 세트로 고정 | QA candidate 직전 |
| token sanity가 ending shell 전체 리뉴얼 논의로 커지는 위험 | oath green이 늦어지고 queue closing 경계가 흐려진다 | `Oath Banner Starter` 세트만 oath semantic zone으로 한정 | UI / 아트 sanity review 시 |
| PM이 semantic anchor 재확인을 green이나 queue closing unlock처럼 기록하는 위험 | half-cutover가 완료처럼 보인다 | semantic anchor는 kickoff layer, green은 evidence layer, queue closing은 다음 운영 layer로 분리 | tracker / run log 작성 시 |

---

## 11. 즉시 다음 액션
1. 다음 oath 실제 refinement는 `source-of-truth map -> gap ledger -> queue readiness board -> oath live kickoff -> 이 semantic anchor packet` 순서로 읽는다.
2. programmer는 `notes/hook-manifest.md`에 `root / role / cta / boundary / non-change` 다섯 줄을 먼저 적는다.
3. UI는 `notes/density-review.md`에 overview / banner-focus 질문을 먼저 적는다.
4. 그 뒤에만 oath-only edit와 evidence drop을 시작한다.
5. `archive-ready candidate / green / queue closing`은 여전히 이 semantic anchor 메모 뒤의 evidence bundle로만 닫는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_oath_banner_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
