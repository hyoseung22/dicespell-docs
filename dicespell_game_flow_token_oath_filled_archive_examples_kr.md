# DiceSpell `Run Table` Oath Banner Filled Archive Examples

## 3줄 요약
- 이 문서는 `oath live kickoff`, `semantic anchor`, `archive file map`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도 끝까지 남아 있던 마지막 wording 공백인 **`좋아, oath first live archive를 열 수 있다는 건 알겠어. 그런데 banner/result/carry artifact 8종은 실제로 어느 문장 밀도까지 채워야 archive-ready candidate라고 말할 수 있지?`**를 고정하는 source-of-truth다.
- 핵심은 새 ending 화면을 더 설계하는 것이 아니라, `manifest / hook-manifest / dom-sanity / overview capture / banner-focus capture / density review / starter asset list / handoff line`을 **실전 archive에 그대로 복사해도 되는 filled exemplar**로 보여 줘 프로그래머 / UI / 아트 / QA / PM이 마지막 stage를 다시 `ending 전반 progress memo`로 부풀리지 않게 만드는 것이다.
- 첫 화면만 읽어도 `어디까지 채우면 honest oath archive인지`, `candidate와 green은 어느 문장에서 갈리는지`, `누가 어떤 문장을 tracker/run log/git에 그대로 재사용하면 되는지`가 바로 보여야 한다.

## 한 줄 목표
`oath-banner` 마지막 live archive를 **filled exemplar 8종 + banner-first wording rule + candidate/green 재사용 문장**으로 잠가, queue closing 직전 마지막 stage가 generic ending 메모가 아니라 handoff-ready bundle로 읽히게 만든다.

## 왜 이 문서가 필요한가
지금 Run Table oath 축은 아래 문서까지 이미 충분하다.

- `docs/dicespell_game_flow_token_oath_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_semantic_anchor_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_archive_file_map_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_oath_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- oath stage archive를 어떻게 시작하는가
- banner / result / carry semantic zone을 어디서 다시 찾는가
- template / stub / example / live archive path를 어떻게 분리하는가
- artifact 8종은 무엇이고 candidate와 green은 어떤 순서로 분리하는가

하지만 실제 첫 live turn 직전에는 여전히 작은 실무 공백이 남아 있다.

1. `무엇을 제출해야 하는가`와 `실제로 어느 문장 밀도까지 채워야 honest oath archive인가`는 다르다. packet만으로는 충분해도 implementer는 여전히 note가 너무 짧은지, 너무 큰 ending 총괄 문장인지, candidate/green을 미리 써 버린 것인지 망설일 수 있다.
2. `manifest_example_oath_banner.md`는 field 예시를 주지만, 실제 archive 안의 `hook-manifest`, `dom-sanity`, `density-review`, `starter-asset-list`, `handoff-line`, capture caption까지 같은 oath stage 언어로 어떻게 맞춰야 하는지는 한 폴더로 보지 못한다.
3. UI/아트/QA/PM은 제출 순서를 알아도, 프로그래머 바닥 note보다 먼저 ending helper 감상이나 queue closing 예고 문장이 올라가는 순간 archive 전체가 다시 `Overrun Oath 거의 완료` 같은 generic progress memo처럼 보일 위험이 있다.
4. 결국 scorecard와 file map이 있어도 actual bundle exemplar가 없으면 첫 oath 턴은 다시 `ending은 거의 닫힘`, `queue closing도 곧`, `Run Table finish 느낌` 같은 큰 문장을 손으로 정리하게 된다.

즉 지금 필요한 것은 새 stage packet이 아니라, **oath final stage의 채워진 archive가 실제로 어떤 문장으로 보여야 honest한지 보여 주는 filled archive examples**다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. artifact exemplar map`, `3. note wording rule` | `oath semantic anchor`, `oath archive file map`, `manifest fill packet` | manifest와 hook/check note는 `banner / result / carry 읽힘`까지 닫는 짧은 stage contract여야 하고 ending helper/reward-shop/queue closing 확장을 섞으면 안 된다 |
| UI | `2. artifact exemplar map`, `4. capture exemplar rule`, `5. role-specific handoff` | `oath required artifacts`, `oath candidate review` | overview/focus capture도 oath-only 질문과 caption sentence가 같이 있어야 하고 glamour shot은 비범위다 |
| 아트 | `2. artifact exemplar map`, `5. starter asset exemplar rule`, `6. 리스크 & 후속과제` | `token anchor packet`, `oath required artifacts` | starter asset note는 ending 리디자인 backlog가 아니라 Oath Banner Starter 충분성/비범위를 말하는 작은 sanity note여야 한다 |
| QA | `2. artifact exemplar map`, `6. candidate/green reuse rule`, `7. immediate next actions` | `oath candidate review`, `queue closing packet` | exemplar 수준의 wording alignment가 맞았을 때만 `archive-ready candidate`를 쓸 수 있고, green은 여전히 PM 마지막 기록이다 |
| PM/기획 | `0. first screen 결론`, `6. candidate/green reuse rule`, `7. immediate next actions` | `oath candidate review`, `queue closing packet` | oath green은 filled bundle이 scorecard/candidate review 문장을 그대로 재사용할 때만 기록한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `oath-banner archive-ready candidate`라고 말하려면 artifact 8종은 실제로 어디까지 채워져 있어야 하지?
- `starter bundle을 만들었다`와 `filled archive가 honest하다`는 어디서 갈리지?
- tracker / run log / git에는 어떤 문장을 그대로 복붙해도 과장이 아니지?

### 가장 짧은 답
- oath final stage는 **artifact 8종이 모두 존재**해야 할 뿐 아니라, 각 artifact의 첫 화면이 `oath-banner`, `banner / result / carry`, `banner-first hierarchy`, `queue closing은 아직 아님`, `next allowed step`을 같은 stage 언어로 말해야 한다.
- capture도 파일명만 맞으면 충분하지 않다. overview/focus의 질문과 짧은 caption sentence가 density review와 같은 뜻을 말해야 한다.
- QA는 exemplar 수준으로 wording이 정렬됐을 때만 `archive-ready candidate`를 쓴다.
- PM은 그 다음에만 `oath-banner green: oath final-stage evidence bundle과 handoff line 정렬 확인. Run Table token queue closing review-only unlock.`을 기록한다.

### 한 줄 판정 규칙
좋은 oath filled archive는 `자료가 모였다`가 아니라 **`각 artifact가 같은 banner-first stage boundary와 같은 queue discipline을 말한다`**로 판단한다.

---

## 1. core structure

## 3줄 요약
- 이 문서는 `필수 artifact 목록`을 다시 반복하는 문서가 아니라, 그 artifact들이 실제로 채워졌을 때의 **honest wording density**를 고정하는 문서다.
- 핵심은 길게 설명하는 것이 아니라, `이번 stage가 무엇만 닫았는지`, `무엇을 아직 열지 않았는지`, `candidate와 green을 누가 쓰는지`가 file-level로 바로 보이는 상태다.
- 아래 exemplar bundle은 실제 oath archive를 시작할 때 거의 그대로 복사해도 되지만, 현재 상태에 맞게 `status`와 `handoff line`만 honest하게 낮춰 써야 한다.

### 한 줄 목표
oath archive를 `template`이 아니라 **실제로 채워진 stage contract example**로 보여 준다.

### exemplar bundle 위치
- source handoff 문서: `docs/dicespell_game_flow_token_oath_filled_archive_examples_kr.md`
- readable companion: `docs/dicespell_game_flow_token_oath_filled_archive_examples_summary_kr.md`
- filled exemplar bundle: `docs/artifacts/run-table-token-delivery/examples/oath-banner/`

---

## 2. artifact exemplar map

## 3줄 요약
- 아래 표는 oath stage의 8종 artifact가 exemplar bundle에서 각각 어떤 파일로 존재하는지, 무엇을 증명하는지, 어떤 문장을 재사용해야 하는지를 한 번에 보여 준다.
- 목적은 implementer가 `파일명은 맞는데 문장이 너무 크다`거나 `capture는 있는데 verdict line이 없다` 같은 반쪽 archive를 만들지 않게 하는 것이다.
- 각 artifact는 혼자서는 완결이 아니고, 같은 `oath-banner` ladder의 다른 면이다.

### 한 줄 목표
artifact 8종을 `같은 oath final stage를 말하는 파일 묶음`으로 읽게 만든다.

| exemplar 파일 | 실제 archive 대응 위치 | 무엇을 증명하는가 | 꼭 남겨야 하는 핵심 문장 |
| --- | --- | --- | --- |
| `manifest.md` | `manifest.md` | stage status / boundary / next step | `이번 stage는 oath-banner banner / result / carry 읽힘까지만 닫는다.` |
| `notes/hook-manifest.md` | `notes/hook-manifest.md` | oath root/role/CTA hook 책임 | `ending helper 확장, reward/shop side family, queue closing은 아직 열지 않는다.` |
| `checks/dom-sanity.md` | `checks/dom-sanity.md` | canonical hook 존재/누락 여부 | `banner / result / carry / CTA hook은 확인했고 queue closing hook은 이번 stage 검수 밖이다.` |
| `captures/oath-1440-overview.caption.md` | `captures/oath-1440-overview.png`와 짝 | banner-first first-glance 질문 | `main banner가 first-glance로 읽히고 result/carry 및 CTA는 그다음 계층으로 머문다.` |
| `captures/oath-1440-banner-focus.caption.md` | `captures/oath-1440-banner-focus.png`와 짝 | role hierarchy 집중 질문 | `banner / result / carry 관계가 같은 oath sentence로 이어진다.` |
| `notes/density-review.md` | `notes/density-review.md` | overview/focus에 대한 유지/약함/과함 verdict | `ending helper, reward/shop side family, queue closing 평가는 아직 근거에 포함하지 않는다.` |
| `assets/starter-asset-list.md` | `assets/starter-asset-list.md` | Oath Banner Starter 충분성/금지 범위 | `ending shell 전체 리뉴얼과 queue closing 공용 shell 확장은 이번 stage 비범위다.` |
| `notes/handoff-line.md` | `notes/handoff-line.md` | candidate/green 복붙 문장 | `archive-ready candidate`와 `green`을 다른 줄로 분리한다. |

---

## 3. note wording rule

## 3줄 요약
- exemplar bundle의 note는 모두 짧다. 하지만 짧은 이유는 정보가 부족해서가 아니라, stage boundary를 흐리지 않기 위해서다.
- 좋은 oath note는 항상 `이번 stage 범위`, `비범위`, `다음 unlock 또는 review gate`를 포함한다.
- 나쁜 oath note는 언제나 감상형 문장이나 큰 총괄 progress 문장으로 커진다.

### 한 줄 목표
oath note를 `길게 친절한 설명`이 아니라 **짧고 재사용 가능한 stage contract sentence**로 맞춘다.

### exemplar 공통 규칙
1. 첫 줄에는 `oath-banner` stage명을 넣는다.
2. 둘째 줄에는 `banner / result / carry 읽힘` 범위를 적는다.
3. 셋째 줄에는 `ending helper`, `reward/shop side family`, `queue closing` 비범위를 적는다.
4. 마지막 줄에는 `candidate 전 단계` 또는 `queue closing은 oath green 뒤에만` 같은 gate를 적는다.

### 금지 문장
- `Overrun Oath 거의 완료`
- `ending은 사실상 끝`
- `queue closing도 같이 가능`
- `Run Table complete 느낌`
- `ending 전반 안정적`

### 좋은 문장 패턴
- `이번 stage는 oath-banner banner / result / carry 읽힘까지만 닫는다.`
- `ending helper, reward/shop side family, queue closing은 아직 열지 않는다.`
- `현재 bundle은 oath candidate 검토용이며 queue closing unlock 근거는 PM green 뒤에만 사용한다.`

---

## 4. capture exemplar rule

## 3줄 요약
- source-of-truth는 여전히 텍스트이므로, exemplar bundle의 capture는 실제 `.png` 대신 **같은 이름을 가리키는 caption 파일**로 보관한다.
- 중요한 것은 예쁜 ending 장면이 아니라 각 장면이 무엇을 증명하는지, 어떤 fail을 막는지까지 같은 oath language로 적는 것이다.
- 이 caption 파일은 실제 archive에서 `png + 짧은 caption note`를 함께 만들 때의 기준 문장으로 재사용한다.

### 한 줄 목표
capture exemplar도 oath stage 질문에 답하는 text-first evidence로 고정한다.

| caption exemplar | 실제 runtime evidence에서 같이 남길 것 | pass 문장 | fail 문장 |
| --- | --- | --- | --- |
| `captures/oath-1440-overview.caption.md` | `captures/oath-1440-overview.png` | `main banner가 먼저 읽히고 result/carry와 CTA는 그 뒤에 따른다.` | `CTA나 side card가 main banner보다 먼저 튄다.` |
| `captures/oath-1440-banner-focus.caption.md` | `captures/oath-1440-banner-focus.png` | `banner / result / carry role 관계가 같은 oath sentence로 이어진다.` | `banner만 과도하게 커지고 result/carry는 별도 카드 묶음처럼 분리된다.` |

### capture 사용 규칙
- 실제 archive에서는 `.png`를 만든 뒤 이 exemplar caption sentence를 짧게 맞춰 붙인다.
- density review는 두 caption의 pass/fail을 직접 요약해야 한다.
- glamour shot이나 전체 ending HUD 감상은 exemplar 바깥이다.

---

## 5. starter asset exemplar rule

## 3줄 요약
- `starter-asset-list.md` exemplar는 `무엇을 더 만들까`보다 `지금 어디까지면 oath stage를 닫을 수 있나`를 먼저 말한다.
- 좋은 starter note는 충분성 verdict와 함께 **이번 stage에서 일부러 안 하는 것**을 반드시 적는다.
- 큰 key art, ending shell 통합, queue closing 공용 frame 확장은 oath exemplar에 들어오지 않는다.

### 한 줄 목표
아트 note도 `작은 충분성` 언어로 잠근다.

### exemplar가 보여 주는 것
- Oath Banner Starter 구성 3~4개
- `충분` 또는 `약함` verdict 1개
- 이번 stage 비범위 2~3개
- oath와 다른 surface가 섞이지 않게 만드는 포인트 1줄

### 아트 오너에게 넘길 것
- 이 exemplar를 쓰면 starter note가 backlog 문서로 커지는 것을 막을 수 있다.
- `ending 전체 shell도 같이` 같은 제안은 oath green 뒤 별도 reopen 문장으로만 남긴다.

---

## 6. candidate / green reuse rule

## 3줄 요약
- filled archive exemplar의 진짜 목적은 note를 예쁘게 써 주는 것이 아니라, candidate와 green을 언제 어떻게 재사용할지까지 고정하는 것이다.
- QA는 exemplar 수준의 wording alignment가 맞을 때만 `archive-ready candidate`를 쓴다.
- PM은 같은 bundle을 보고도 `green`과 `queue closing review-only unlock`을 마지막 줄에서만 쓴다.

### 한 줄 목표
oath filled bundle이 required artifacts와 candidate review packet의 공식 문장을 그대로 재사용하게 만든다.

### QA candidate 복붙 문장
- `oath-banner archive-ready candidate: banner / result / carry role hook, banner-first overview/focus, Oath Banner Starter sanity bundle 정렬 확인. Run Table token queue closing은 oath green 뒤에만 연다.`

### PM green 복붙 문장
- `oath-banner green: oath final-stage evidence bundle과 handoff line 정렬 확인. Run Table token queue closing review-only unlock.`

### 재사용 규칙
1. candidate는 QA line이다. PM green 전에는 queue closing을 확정하지 않는다.
2. green은 PM 마지막 line이다. `Overrun Oath 거의 완료` 같은 큰 문장으로 바꾸지 않는다.
3. manifest의 `status`와 `handoff line`은 현재 honest 단계만 적는다. 예시가 있다 해도 아직 bootstrap-ready면 그대로 낮춰 쓴다.

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| manifest/file map/required artifacts는 있어도 실제 note가 exemplar보다 더 큰 ending 총괄 문장으로 쓰여 oath stage가 generic progress memo처럼 보이는 위험 | candidate/green/queue closing 경계가 다시 owner 감각에 의존한다 | filled archive examples로 8종 artifact의 honest wording density를 file-level exemplar로 고정 | 첫 oath archive 생성 직전 |
| overview/banner-focus capture는 있는데 질문/판정 문장이 비어 있어 1440p evidence가 감상형 스크린샷으로 흐르는 위험 | QA가 banner-first pass/fail을 문서 대신 해석으로 판단하게 된다 | text-first caption exemplar 2종으로 capture 질문과 pass/fail language를 같이 고정 | 첫 overview/banner-focus capture 수집 직전 |
| starter asset note가 oath stage 충분성 대신 ending shell 전체 리뉴얼 제안으로 커지는 위험 | oath green이 늦어지고 stage boundary가 약해진다 | exemplar에 비범위 문장과 금지 예시를 함께 넣어 small-sanity language를 재사용하게 함 | 첫 starter sanity note 작성 직전 |
| candidate와 green 복붙 문장이 예시에서 분리돼 있어도 실제 run log에서 다시 같은 초록불로 기록되는 위험 | queue closing unlock이 조기 개방되거나 stage discipline이 무너진다 | candidate / green reuse rule을 packet 안에 같이 고정하고 QA/PM line을 따로 제시 | 첫 oath candidate review / PM green 기록 직전 |

---

## 8. immediate next actions

1. 다음 oath 실제 refinement는 `source-of-truth map -> gap ledger -> queue readiness board -> oath live kickoff -> oath semantic anchor -> oath archive file map -> oath filled archive examples -> oath handoff scorecard -> oath required artifacts -> oath candidate review -> queue closing` 순서로 읽는다.
2. 실제 live archive는 반드시 `docs/artifacts/run-table-token-delivery/oath-banner/` 하나만 제출 경로로 사용하고, `examples/oath-banner/`는 reference layer로만 둔다.
3. 첫 live turn에서도 `manifest / hook-manifest / dom-sanity`를 먼저 채운 뒤에만 `overview/banner-focus capture + density`, `starter sanity`, `candidate`, `green`으로 넘어간다.
4. ending helper, reward/shop side family, queue closing은 oath green 전까지 계속 잠근다.

## 한 장 결론
Run Table oath 축의 남은 마지막 공백은 `무슨 artifact가 필요한가`가 아니라 **`그 artifact를 실제로 어떤 문장으로 채워야 honest archive-ready candidate가 되는가`**다. 이 문서는 그 답을 source-of-truth와 exemplar bundle로 같이 보여 주는 oath-only handoff packet이다.
