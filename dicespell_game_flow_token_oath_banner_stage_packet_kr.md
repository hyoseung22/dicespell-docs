# DiceSpell `Run Table` Oath Banner 세 번째 스테이지 패킷

## 3줄 요약
- 이 문서는 `atlas-route-node green`, `battle-stakes-plaque green`까지 정리된 상태에서도 마지막 실제 refinement 턴 직전 끝까지 남아 있던 **`좋아, 이제 oath-banner는 정확히 어디까지를 한 stage로 닫지?`**를 고정하는 source-of-truth다.
- 핵심은 새 ending/overrun 화면을 더 설계하는 것이 아니라, `Overrun Oath` 마지막 stage를 **`banner / result / carry role hook 잠금 + banner-first hierarchy 검수 + Oath Banner Starter sanity + oath archive-ready candidate`**까지만 좁혀 reward/shop/ending family drift와 boundary 재확장을 막는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `battle green 뒤 어떤 조건으로 oath를 열 수 있는지`, `이번 턴에 oath에서 무엇을 해야 하는지`, `아직 무엇을 하면 안 되는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`oath-banner`를 **stage 1개 = archive 1개 = green 문장 1개** 원칙으로 닫아, Run Table token refinement의 마지막 턴을 다시 큰 ending polish가 아니라 review 가능한 closing stage로 고정한다.

## 왜 이 문서가 필요한가
지금 Run Table 축은 아래 문서까지 이미 닫혀 있다.

- `docs/dicespell_game_flow_surface_handoff_packet_kr.md`
- `docs/dicespell_game_flow_token_anchor_packet_kr.md`
- `docs/dicespell_game_flow_token_delivery_workboard_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_route_node_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_candidate_review_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- 왜 atlas가 첫 stage였는가
- 왜 battle이 둘째 stage였고, battle green 뒤에만 oath가 열려야 하는가
- `bootstrap-ready -> archive-in-progress -> archive-ready candidate -> stage green` ownership은 무엇인가
- oath도 `banner / result / carry` language로 닫아야 한다는 큰 방향은 무엇인가

하지만 실제 `oath-banner` 착수 직전에는 여전히 아래 공백이 남아 있다.

1. queue는 oath가 마지막이라고 말하지만, **oath stage 안에서 정확히 어느 role hook / capture / sanity / handoff line까지만 닫고 멈출지**는 여전히 사람이 다시 조합해야 한다.
2. battle green까지 왔기 때문에 implementer가 `그럼 ending helper도 같이`, `reward/shop side family도 같이`, `overrunEvent boundary도 같이`처럼 범위를 다시 키우기 쉽다.
3. UI/아트는 `banner / result / carry` hierarchy와 `Oath Banner Starter` sanity만 보면 되는데, 이 둘이 `ending 전체 polish`나 `오버런 경계 재설계`와 다르다는 점을 마지막 stage 언어로 다시 잠글 필요가 있다.
4. QA/PM은 battle green 뒤 oath를 열 수는 있지만, **어떤 evidence가 있어야 `oath archive-ready candidate`와 `oath-banner green, Run Table token queue closing`을 쓸 수 있는지**를 한 장으로 다시 보고 싶어진다.

즉 지금 필요한 것은 새 visual concept가 아니라, **battle green 다음 실제 마지막 oath 턴을 실행 단위로 다시 압축한 oath stage packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `1. stage boundary`, `2. 구현 범위`, `4. evidence bundle` | `token source-of-truth map`, `token refinement queue`, `battle stage packet` | 이번 턴은 oath role hook과 banner-first hierarchy만 닫고 ending 재설계나 overrun boundary 확장은 일부러 건드리지 않는다 |
| UI | `1. stage boundary`, `3. UI/아트 검수선`, `4. evidence bundle` | `token closing packet`, `surface handoff summary`, `ending overrun boundary packet` | oath는 `banner > result > carry` first-glance를 닫는 stage이지 ending 전체 density pass가 아니다 |
| 아트 | `3. UI/아트 검수선`, `5. role-specific handoff` | `token anchor packet`, `token evidence manifest` | `Oath Banner Starter` sanity만 보면 되고 ending 배경/공용 shell 전체 리뉴얼은 아직 금지다 |
| QA | `4. evidence bundle`, `6. signoff rule`, `7. 리스크 & 후속과제` | `token evidence manifest`, `token refinement queue`, `ending overrun boundary packet` | `oath archive-ready candidate`는 required bundle이 채워졌을 때만 쓰고 `Run Table token complete` 같은 큰 문장은 아직 금지다 |
| PM/기획 | `0. first screen 결론`, `6. signoff rule`, `8. immediate next actions` | `token gap ledger`, `token refinement queue summary` | oath green은 queue 마지막 stage 기록일 뿐 ending/overrun 전체 green이 아니며, 기록도 stage명 기준으로만 남긴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `battle-stakes-plaque green` 뒤 다음 실제 refinement 턴에서 `oath-banner`는 정확히 어디까지를 한 stage로 닫지?
- 무엇이 들어와야 `oath archive-ready candidate`라고 말할 수 있지?
- 왜 ending helper 확장, reward/shop side family 재정리, overrun/ending boundary 재설계는 이번 턴에 아직 열면 안 되지?

### 가장 짧은 답
- 이번 stage는 `Overrun Oath`의 oath banner family만 닫는다.
- 범위는 `surface root + banner/result/carry role hook + 1440 overview/focus + starter asset sanity + oath handoff line 초안`까지다.
- ending helper 전체 개편, reward/shop side family 재정리, overrun/ending boundary 재설계, onboarding/overrun 문서 축 재개방은 아직 금지다.
- `oath archive-ready candidate`는 QA가 oath required bundle을 모두 본 뒤에만 쓴다.

### 한 줄 판정 규칙
이번 oath 턴은 **`banner > result > carry hierarchy가 첫 시선에 결과 화면이 아니라 oath 장면으로 읽히는지`**를 닫는 stage이지, ending 전체를 다시 디자인하는 턴이 아니다.

---

## 1. stage boundary

## 3줄 요약
- oath 마지막 stage는 `Overrun Oath` 전체가 아니라 `oath banner`의 role hierarchy를 닫는 bounded stage다.
- 이 stage의 성공은 새 ending shell 발명이 아니라, `banner / result / carry`가 같은 문장으로 markup / evidence / review에 남는지로 판단한다.
- battle을 다시 열지 않고 overrun/onboarding 경계 논의를 아직 열지 않는 것이 oath green의 일부다.

### 한 줄 목표
oath stage를 `banner-first closing`까지만 좁혀 queue 마지막 stage가 큰 경계 재설계로 커지지 않게 만든다.

| 구분 | 이번 stage에 포함 | 이번 stage에서 금지 |
| --- | --- | --- |
| surface | `Overrun Oath`의 banner / result / carry 읽힘 | reward/shop side family 재정렬, ending helper 전체 재작성, onboarding/overrun boundary 재설계 |
| markup 계약 | `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta` | `data-stakes-slot`, `data-route-node-state`, primer/helper 신규 family 추가 |
| UI 범위 | banner-first hierarchy, result/carry 층위, 1440 overview/focus 검수 | ending 전체 density pass, 결과표/보관함/CTA 전체 카피 재정리 |
| 아트 범위 | `Oath Banner Starter` sanity note | ending 배경 전체 리뉴얼, reward/shop 공용 frame 확장 |
| 기록 문장 | `oath-banner bootstrap-ready`, `oath-banner archive-ready candidate`, `oath-banner green` | `Overrun Oath 거의 완료`, `ending 전반 green`, `Run Table 전체 종료` |

### 이번 stage를 oath-only로 고정하는 이유
1. queue packet이 battle 다음 stage를 oath로 고정했으므로, 실제 마지막 live turn도 oath만 닫혀야 queue가 의미를 가진다.
2. `oath-banner`는 ending/reward/carry 경계가 가장 민감해 마지막 stage로 좁혀야 drift를 줄일 수 있다.
3. oath green에서 ending/overrun 경계나 onboarding helper까지 같이 만지면 `stage 1개 = archive 1개 = green 문장 1개` 원칙이 마지막 턴에서 무너진다.

---

## 2. 구현 범위

## 3줄 요약
- 프로그래머 범위는 ending live markup을 `banner / result / carry` role language로 잠그는 일이다.
- 이 stage에서는 oath banner vocabulary drift를 줄이고 evidence-friendly hook 이름을 남기는 것이 우선이다.
- reward/shop helper나 overrun/onboarding 경계까지 같은 턴에 크게 재설계하면 범위를 넘는다.

### 한 줄 목표
`renderEnding(summary)`와 연결된 live oath markup을 review 가능한 role vocabulary로 잠근다.

| 항목 | 현재 baseline | 이번 stage target | 이번 stage에서 아직 안 함 |
| --- | --- | --- | --- |
| surface root | ending surface는 있으나 oath family 식별이 generic ending shell에 기대고 있음 | `data-run-surface="overrun-oath"` | reward/shop/ending 전체 root 재정렬 |
| role identity | banner / side / CTA가 시각적으로만 암시될 수 있음 | `data-oath-role="banner|result|carry"`로 검수 가능한 role 노출 | ending helper / primer family 추가 |
| CTA context | carry CTA가 장면 맥락과 느슨하게 결합될 수 있음 | `data-overrun-cta`로 CTA context 고정 | overrun boundary 재설계 |
| helper vocabulary | result/carry 문장이 helper/side copy에서 drift할 여지 | role language와 동일 어휘 | ending copy 대량 재작성 |

### 프로그래머 handoff line
- 이번 oath stage는 `role hook 잠금 + banner-first hierarchy 고정`까지만 닫는다.
- onboarding primer나 `overrunEvent` 경계 helper는 같은 턴에 넣지 않는다.
- helper를 새 tree로 갈아엎기보다, 지금 있는 ending/oath family가 review 가능한 `banner / result / carry` 단어를 쓰게 만드는 쪽이 우선이다.

---

## 3. UI / 아트 검수선

## 3줄 요약
- UI와 아트의 목표는 `Overrun Oath`가 banner 중심 장면으로 먼저 읽히는가를 닫는 것이지, 전체 ending 미감 리뉴얼이 아니다.
- overview/focus 두 캡처와 starter sanity note만으로도 이번 stage는 충분히 검수 가능해야 한다.
- oath는 side card보다 `banner > result > carry`가 먼저 보여야 green 후보가 된다.

### 한 줄 목표
oath를 `banner-first closing` 기준으로 닫고, 나머지 ending polish는 일부러 뒤로 미룬다.

| 항목 | 이번 stage 완료선 | 아직 금지 |
| --- | --- | --- |
| overview capture | `captures/oath-1440-overview.png`에서 main banner가 side card/CTA보다 먼저 읽힌다 | ending helper / result summary / side family 전체를 한 화면에서 같이 닫기 |
| focus capture | `captures/oath-1440-banner-focus.png`에서 banner/result/carry 층위와 CTA 맥락이 한 장에서 읽힌다 | reward/shop side strip, primer/helper family 재설계 |
| density note | `notes/density-review.md`에 `유지 / 약함 / 과함` 수준의 짧은 oath verdict가 있다 | 감상형 장문 회고, ending 전체 기획 토론 |
| starter asset sanity | `assets/starter-asset-list.md`에 `Oath Banner Starter` sanity와 아직 금지 자산이 함께 적혀 있다 | ending 배경 전체 리뉴얼, 공용 side shell 확장 |

### UI / 아트 owner에게 넘길 것
- oath stage는 `banner > result > carry > 나머지 보조 정보` 순서를 닫는 turn이다.
- 캡처 이름은 반드시 oath stage 이름을 말해야 한다.
- starter asset sanity는 `충분 / 약함 / 과함`을 판단하는 note면 충분하다. 큰 ending shell 리뉴얼 제안은 이번 stage 범위 밖이다.

---

## 4. evidence bundle

## 3줄 요약
- oath green은 느낌이 아니라 bundle로 판단한다.
- required artifacts가 stage archive 안에 같은 이름으로 모여야 QA가 `archive-ready candidate`를 쓸 수 있다.
- evidence가 stage 밖 임시 폴더에 흩어지면 이번 stage는 닫히지 않은 것으로 본다.

### 한 줄 목표
oath를 `말`이 아니라 `같은 경로의 evidence bundle`로 닫는다.

### 필수 archive 경로
- `docs/artifacts/run-table-token-delivery/oath-banner/manifest.md`
- `docs/artifacts/run-table-token-delivery/oath-banner/captures/oath-1440-overview.png`
- `docs/artifacts/run-table-token-delivery/oath-banner/captures/oath-1440-banner-focus.png`
- `docs/artifacts/run-table-token-delivery/oath-banner/notes/hook-manifest.md`
- `docs/artifacts/run-table-token-delivery/oath-banner/notes/density-review.md`
- `docs/artifacts/run-table-token-delivery/oath-banner/notes/handoff-line.md`
- `docs/artifacts/run-table-token-delivery/oath-banner/checks/dom-sanity.md`
- `docs/artifacts/run-table-token-delivery/oath-banner/assets/starter-asset-list.md`

### oath stage required artifacts

| 파일 | 무엇을 증명하는가 | 누가 주로 채우는가 |
| --- | --- | --- |
| `manifest.md` | stage 상태, required artifacts, next allowed step, handoff line 초안 | 프로그래머 + PM |
| `notes/hook-manifest.md` | oath root/banner/result/carry/CTA hook이 실제 markup에서 무엇인지 | 프로그래머 |
| `checks/dom-sanity.md` | root/banner/result/carry/CTA hook이 누락 없이 박혔는지 | 프로그래머 + QA |
| `captures/oath-1440-overview.png` | banner-first hierarchy와 side card 억제 | UI |
| `captures/oath-1440-banner-focus.png` | banner / result / carry 집중 읽힘 | UI |
| `notes/density-review.md` | `유지 / 약함 / 과함` verdict | UI + QA |
| `assets/starter-asset-list.md` | `Oath Banner Starter` sanity와 아직 금지 범위 | 아트 |
| `notes/handoff-line.md` | oath stage 복붙용 green/candidate 문장 초안 | QA + PM |

### archive-ready candidate 최소선
1. oath stage archive가 생성돼 있다.
2. 위 required artifacts가 모두 존재한다.
3. `hook-manifest.md`와 `dom-sanity.md`가 같은 role vocabulary를 쓴다.
4. overview/focus 캡처 파일명이 oath stage 목적을 그대로 말한다.
5. `manifest.md`의 `next allowed step`이 `Run Table token queue closing only after oath green`과 일치한다.

---

## 5. role-specific handoff points

### 프로그래머에게 넘길 것
- `Overrun Oath`에서 닫아야 하는 것은 `banner / result / carry` role language다.
- battle hook 재수정이나 onboarding/overrun helper를 같은 턴에 넣지 않는다.
- oath stage의 성공은 helper 대수술이 아니라 review 가능한 role anchor를 남기는 데 있다.

### UI에게 넘길 것
- 이번 턴 캡처는 oath 두 장이면 충분하다.
- overview는 `banner > result > carry`, focus는 `banner hierarchy와 role 이름`만 증명하면 된다.
- side card나 CTA가 메인 banner보다 먼저 보이면 fail note를 남기고 green 문장은 미룬다.

### 아트에게 넘길 것
- `Oath Banner Starter` sanity만 닫는다.
- ending을 위해 새 ending 배경이나 공용 shell 전체를 열지 않는다.
- starter note에는 `이번 stage에 아직 안 하는 것`도 같이 적는다.

### QA에게 넘길 것
- `oath archive-ready candidate`는 required artifacts가 채워졌을 때만 쓴다.
- `bootstrap-ready`와 `archive-ready candidate`를 같은 review 문장으로 합치지 않는다.
- battle hook 회귀나 onboarding/overrun evidence가 섞이면 oath green이 아니라 oath fail note를 남긴다.

### PM/기획에게 넘길 것
- oath green은 `Run Table queue closing`이지 ending/overrun 전체 green이 아니다.
- 기록 문장은 반드시 stage명 기준으로 남긴다.
- 필요하면 그 다음에만 후속 queue/follow-up packet을 연다.

---

## 6. signoff rule

## 3줄 요약
- oath stage도 `준비`, `진행`, `후보`, `green`이 다른 층위다.
- 같은 사람이 시작과 최종 green을 모두 쓰면 마지막 stage 경계가 다시 흐려진다.
- queue closing 문장은 oath green 뒤에만 열린다.

### 한 줄 목표
oath stage의 signoff를 `제출`, `확인`, `기록`으로 분리한다.

| 단계 | 누가 먼저 적는가 | 무엇이 있어야 하는가 | 다음에 열리는 것 |
| --- | --- | --- | --- |
| `bootstrap-ready` | 프로그래머 | stage 폴더 + manifest + starter file set | oath live edit 시작 |
| `archive-in-progress` | 프로그래머 / UI | 첫 hook note 또는 첫 capture 존재 | 중간 sanity review |
| `archive-ready candidate` | QA | oath required artifacts 충족 | PM green 검토 |
| `oath-banner green` | PM/기획 | QA candidate + handoff line 합의 | `Run Table token queue closing` 기록 |

### 금지 문장
- `Overrun Oath 거의 완료`
- `ending도 사실상 닫힘`
- `Run Table 전체 완료`
- `oath 사실상 green`

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| oath stage가 다시 `banner + ending helper + reward/shop side family + overrun boundary` 묶음으로 커지는 위험 | 마지막 stage에서 boundary가 흐려져 queue와 stage archive 언어가 무력화된다 | 이번 packet에서 oath 포함/금지 범위를 다시 표로 고정 | 다음 oath first edit 직전 |
| required artifacts가 oath stage archive 밖 임시 경로에 흩어지는 위험 | QA candidate가 감각 의존형 판단으로 돌아간다 | oath archive 경로와 required artifacts를 file-level로 못 박음 | 첫 oath capture drop 직전 |
| UI/아트가 starter sanity를 ending 전체 리디자인 제안으로 확장하는 위험 | oath green이 불필요하게 늦어지고 stage queue closing이 흐려진다 | 이번 stage는 `Oath Banner Starter` sanity만 본다고 고정 | oath density review 직전 |
| PM/QA가 oath green을 `ending green`이나 `Run Table complete`처럼 크게 기록하는 위험 | stage 경계가 사라지고 후속 packet의 unlock discipline이 무너진다 | signoff rule과 금지 문장을 stage명 기준으로 고정 | oath green 기록 직전 |
| battle green 문장을 oath에도 그대로 복붙해 `candidate = review pass`, `green = queue closing`이 다시 흐려지는 위험 | battle과 oath stage 문장이 섞여 마지막 queue closing 의미가 약해진다 | oath 전용 candidate/green stage명을 따로 고정 | oath handoff-line 초안 작성 직전 |

---

## 8. immediate next actions

1. 실제 다음 refinement 턴에서는 `token source-of-truth map -> token gap ledger -> token refinement queue -> battle stage packet -> 이 oath stage packet` 순서로 읽는다.
2. `battle-stakes-plaque green` evidence가 먼저 있는지 확인한다.
3. `docs/artifacts/run-table-token-delivery/oath-banner/` starter bundle부터 만든다.
4. 프로그래머는 `notes/hook-manifest.md`, `checks/dom-sanity.md`를 먼저 채운 뒤 live oath edit에 들어간다.
5. UI는 `oath-1440-overview.png`, `oath-1440-banner-focus.png` 두 장만 우선 예약/생성한다.
6. 아트는 `assets/starter-asset-list.md`에 `Oath Banner Starter` sanity와 아직 금지 자산을 함께 적는다.
7. QA가 `oath-banner archive-ready candidate`를 쓰기 전에는 `Run Table token queue closing` 문장을 열지 않는다.
8. PM은 `oath-banner green` 문장만 남기고, 필요하면 그 다음에만 follow-up queue나 next packet을 연다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_refinement_queue_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 다음 가장 가치 큰 공백은 **`battle 다음 마지막 stage가 oath라는 건 알겠는데, 그 oath stage를 정확히 어디까지를 한 archive와 한 green 문장으로 닫을 것인가`**였다. 이 문서는 그 공백을 `stage boundary`, `banner/result/carry 구현 범위`, `overview/focus/starter sanity evidence bundle`, `signoff rule`, `queue closing 조건`까지 한 장으로 다시 압축해, 다음 refinement가 다시 큰 ending polish나 reward/overrun boundary 동시 수정으로 흐르지 않게 만드는 oath-final stage packet이다.
