# DiceSpell `Run Table` Token 증거 스텁 킷 패킷

## 3줄 요약
- 이 문서는 `token archive bootstrap`, `atlas live kickoff`, `required artifacts`, `candidate review`까지 이미 닫힌 상태에서도 마지막으로 남아 있던 작은 실전 공백, 즉 **`notes/`·`checks/`·`assets/` starter file 첫 줄을 정확히 어떻게 시작해야 하는가**를 고정하는 source-of-truth다.
- 지금 Run Table 문서는 stage archive 구조, required artifacts, candidate/green wording까지 충분하지만, 실제 첫 live atlas turn 직전에는 여전히 `좋아, manifest는 만들었어. 그런데 hook-manifest / dom-sanity / density-review / starter-asset-list / handoff-line 첫 문장은 무엇이어야 하지?`가 흔들릴 수 있다.
- 목표는 새 토큰 설계를 더하는 것이 아니라, 이미 닫힌 `atlas-route-node -> battle-stakes-plaque -> oath-banner` stage contract를 **복붙 가능한 text stub + stage별 첫 문장 + 금지 placeholder** 수준까지 내려 implementer/reviewer가 첫 3분 안에 같은 evidence 바닥을 깔게 만드는 것이다.

**한 줄 목표:** `atlas-route-node -> battle-stakes-plaque -> oath-banner`의 `notes/`·`checks/`·`assets/` starter file을 **빈 파일이 아니라 honest stub**로 맞춰, 이후 evidence가 채워져도 stage wording과 boundary가 다시 흔들리지 않게 만든다.

---

## 왜 이 문서가 필요한가
- 지금까지는 아래가 이미 충분히 닫혔다.
  - `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
  - `docs/dicespell_game_flow_token_atlas_live_kickoff_packet_kr.md`
  - `docs/dicespell_game_flow_token_atlas_required_artifacts_packet_kr.md`
  - `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
  - `docs/dicespell_game_flow_token_oath_required_artifacts_packet_kr.md`
  - `docs/artifacts/run-table-token-delivery/templates/`
- 그런데 실제 첫 live refinement 직전에는 여전히 이런 공백이 남는다.
  1. `manifest.md` 템플릿은 있지만 `hook-manifest.md`, `dom-sanity.md`, `density-review.md`, `starter-asset-list.md`, `handoff-line.md`는 빈 파일로 시작하기 쉽다.
  2. 빈 파일에서 시작하면 `TODO`, `later`, `Run Table 전체`, `거의 완료` 같은 generic placeholder가 stage contract보다 먼저 자리 잡는다.
  3. atlas/battle/oath는 각기 다른 surface인데도, stub가 없으면 첫 evidence가 다시 비슷한 감상형 note로 뭉개진다.
  4. live kickoff packet은 `manifest/stub 초안`을 요구하지만, 실제로 복사해 바로 쓸 수 있는 stage-specific stub kit는 아직 없다.
- 즉 이번 문서는 새 stage를 추가하는 문서가 아니라, **이미 닫힌 stage archive를 실제 text 바닥까지 handoff-ready하게 내리는 마지막 운영 문서**다.

---

## 이 문서를 먼저 봐야 하는 사람
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. stub layer`, `2. atlas/battle/oath stub 세트` | `atlas/battle/oath required artifacts`, `semantic anchor packet` | hook/state/diff 메모는 빈 파일이 아니라 stage-specific 범위 선언으로 시작한다 |
| UI | `3. density/capture reservation rule`, `4. stage별 금지 문장` | `surface handoff`, `closing packet`, `atlas live kickoff` | first-glance/density note는 감상문이 아니라 hierarchy verdict로 시작한다 |
| 아트 | `2-3. starter asset stub`, `4. 금지 문장` | `token anchor packet`, `visual asset 관련 summary` | starter sanity는 새 공통 frame 요구가 아니라 token drift/no-drift 문장으로 시작한다 |
| QA | `1. stub layer`, `5. QA check stub`, `6. handoff line guard` | `candidate review`, `queue readiness board` | candidate-ready는 manifest와 review packet에서 말하고, stub는 근거와 범위만 말한다 |
| PM/기획 | `0. first screen 결론`, `6. handoff line guard`, `7. append-only 규칙` | `source-of-truth map`, `execution reporting 성격의 문서들` | green 문장은 manifest/run log 전용이고 stub는 bootstrap-ready 바닥만 잠근다 |
| 자동화 writer | `7. append-only 규칙`, `8. 실제 starter kit 파일` | `automation run log`, `atlas live kickoff summary` | atlas/battle/oath archive는 기존 note를 덮어쓰기보다 새 stub/append로만 확장한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `notes/hook-manifest.md` 첫 줄에는 무엇부터 적어야 하지?
- `checks/dom-sanity.md`가 아직 pass/fail 전이어도 어떤 문장으로 시작해야 stage가 커지지 않지?
- `notes/handoff-line.md`는 언제까지 초안이고, 어디서부터 candidate/green wording이 되지?
- atlas/battle/oath가 서로 다른 stage라는 점을 stub 수준에서 어떻게 먼저 고정하지?

### 가장 짧은 답
- `manifest.md`가 stage contract라면, `notes/`·`checks/`·`assets/` stub는 **그 계약을 깨지 않는 첫 문장**이 있어야 한다.
- stub는 `완료 문장`이 아니라 `이번 파일이 무엇을 증명할 예정인지`, `이번 단계에서 무엇을 아직 열지 않았는지`, `다음 evidence가 어떤 형식으로 들어올지`만 말한다.
- fake capture나 dummy PNG는 만들지 않는다. 대신 stub 안의 `capture reservation` 문장으로 예약한다.
- `final`, `거의 끝`, `Run Table 진행`, `battle도 같이` 같은 큰 placeholder는 금지한다.

---

## 1. stub layer: 세 가지 상태만 허용한다

| 상태 | 의미 | stub에 적어도 되는 것 | stub에 적으면 안 되는 것 |
| --- | --- | --- | --- |
| `bootstrap-ready` | 파일을 막 만들었고 아직 실제 evidence는 안 들어옴 | stage명, owner, 예정 evidence 종류, 금지 범위, 다음 업데이트 위치 | candidate, green, unlock 선언 |
| `archive-in-progress` | 첫 evidence 일부가 들어왔지만 stage가 아직 안 닫힘 | 현재 채워진 증거, 아직 비어 있는 항목, 다음 확인 포인트 | `archive-ready`, `green`, 다음 stage 오픈 선언 |
| `archive-ready` | manifest 기준 필수 evidence가 모두 찼음 | 이 상태는 manifest와 candidate review packet이 말함 | 개별 stub 파일에서 단독 green 선언 |

### 운영 원칙
- stub 파일은 `빈칸`을 줄이기 위한 것이지 `별도 보고서`가 아니다.
- green/candidate 문장은 여전히 `manifest.md`, review packet, tracker/run log가 말한다.
- stub는 `이번 파일이 무엇을 담아야 하는지`와 `무엇은 아직 담지 말아야 하는지`를 먼저 잠그는 역할만 한다.

---

## 2. stage별 source stub

## 2-1. atlas-route-node

### 어떤 stub가 필요한가
| 파일 | 왜 필요한가 | 첫 문장 최소선 | 금지 문장 |
| --- | --- | --- | --- |
| `atlas hook manifest` | `atlas root / route-node-state / route-threshold` semantic locator를 먼저 잠그기 위해 | `이 메모는 atlas root/state/threshold hook만 기록하고 battle/oath hook은 아직 열지 않는다.` | `battle도 같이`, `Run Table 전체` |
| `atlas dom sanity` | root/state/threshold DOM evidence 범위를 먼저 고정하기 위해 | `이 체크는 atlas route node stage만 검수한다.` | `전체 surface 점검`, `battle까지 포함` |
| `atlas density review` | first-glance와 threshold 읽힘만 검수하게 만들기 위해 | `이번 review는 Run Atlas board first-glance와 threshold 읽힘만 본다.` | `전반 polish`, `preview/log 전체` |
| `atlas starter asset list` | Route Node Starter sanity를 새 key art 요구와 분리하기 위해 | `이번 sanity는 Route Node Starter만 다루고 공통 frame 신규 제작은 포함하지 않는다.` | `새 atlas frame 필요`, `battle과 공용` |
| `atlas handoff line` | candidate/green wording이 너무 일찍 커지지 않게 하기 위해 | `이 파일은 atlas candidate/green 문장 초안만 기록하고 battle unlock은 아직 확정하지 않는다.` | `atlas green`, `battle unlock 완료` |

### atlas handoff 포인트
- 프로그래머는 locator와 non-change line부터 적고 diff를 시작한다.
- UI는 overview/focus 캡처 전에도 density verdict 문장을 먼저 깐다.
- QA/PM은 handoff line stub를 `manifest handoff candidate` 수준으로만 유지한다.

## 2-2. battle-stakes-plaque

### 어떤 stub가 필요한가
| 파일 | 왜 필요한가 | 첫 문장 최소선 | 금지 문장 |
| --- | --- | --- | --- |
| `battle hook manifest` | `current / next / grimoire slot` locator를 먼저 잠그기 위해 | `이 메모는 battle current/next/grimoire stakes hook만 기록하고 oath/result hook은 아직 열지 않는다.` | `oath도 같이`, `전투 HUD 전체` |
| `battle dom sanity` | stakes 3슬롯 위계를 battle-only로 검수하기 위해 | `이 체크는 battle-stakes-plaque stage만 검수한다.` | `전투 전반 UI`, `action panel 전체` |
| `battle density review` | stakes first-glance만 보게 만들기 위해 | `이번 review는 Battle Table stakes first-glance와 3슬롯 읽힘만 본다.` | `전투 전체 밀도`, `HUD 정리` |
| `battle starter asset list` | Stakes Plaque Starter sanity를 공용 shell 제작과 분리하기 위해 | `이번 sanity는 Stakes Plaque Starter만 다루고 공통 frame 확장은 포함하지 않는다.` | `새 battle shell`, `atlas/oath 공통 자산` |
| `battle handoff line` | oath unlock 문장이 너무 일찍 나오지 않게 하기 위해 | `이 파일은 battle candidate/green 문장 초안만 기록하고 oath unlock은 아직 확정하지 않는다.` | `battle green`, `oath 바로 시작` |

### battle handoff 포인트
- 프로그래머는 action panel/HUD 대개편을 금지 범위로 먼저 적는다.
- UI는 stakes plaque hierarchy만 판단하고 전투 전체 polish를 분리한다.
- PM/QA는 atlas green 뒤에만 battle stub를 열었다는 점을 첫 줄에서 유지한다.

## 2-3. oath-banner

### 어떤 stub가 필요한가
| 파일 | 왜 필요한가 | 첫 문장 최소선 | 금지 문장 |
| --- | --- | --- | --- |
| `oath hook manifest` | `banner / result / carry` locator를 먼저 잠그기 위해 | `이 메모는 oath banner/result/carry hook만 기록하고 queue-closing wording은 아직 열지 않는다.` | `Run Table 전체`, `queue closing 포함` |
| `oath dom sanity` | banner-first hierarchy와 carry boundary를 oath-only로 검수하기 위해 | `이 체크는 oath-banner stage만 검수한다.` | `ending 전체`, `결과 화면 전반` |
| `oath density review` | banner-first 읽힘만 보게 만들기 위해 | `이번 review는 Overrun Oath banner-first hierarchy와 carry boundary만 본다.` | `ending polish`, `reward/shop family` |
| `oath starter asset list` | Oath Banner Starter sanity를 대형 ending art 요구와 분리하기 위해 | `이번 sanity는 Oath Banner Starter만 다루고 ending 전면 교체는 포함하지 않는다.` | `새 ending pack`, `Run Table 마감 art` |
| `oath handoff line` | queue-closing 문장이 너무 일찍 나오지 않게 하기 위해 | `이 파일은 oath candidate/green 문장 초안만 기록하고 queue-closing wording은 아직 확정하지 않는다.` | `Run Table token queue closing`, `전체 완료` |

### oath handoff 포인트
- 프로그래머는 `queue-closing` wording과 역할 hook을 같은 파일에 섞지 않는다.
- UI는 carry boundary를 first-glance 판단과 분리 기록한다.
- 아트는 `ending 전체 분위기`가 아니라 `Oath Banner Starter sanity`만 먼저 닫는다.

---

## 3. density / capture reservation rule

- fake screenshot, 빈 PNG, `placeholder.png`는 만들지 않는다.
- 대신 아래 둘 중 하나로 예약한다.
  1. `manifest.md`의 required artifacts 체크박스에 파일명을 적는다.
  2. 대응 density/handoff stub에 `capture reservation` 줄을 만든다.

예시
```md
## capture reservation
- expected file: captures/atlas-1440-overview.png
- expected file: captures/atlas-1440-threshold-focus.png
- note: battle/oath capture는 이 stage에 포함하지 않는다.
```

### 왜 fake capture를 금지하나
- 텍스트 stub는 honest placeholder가 되지만, 빈 PNG는 evidence처럼 보이기 쉽다.
- stage가 아직 안 닫혔는데 캡처 폴더가 채워진 것처럼 보이면 reviewer가 `bootstrap-ready`와 `archive-in-progress`를 혼동할 수 있다.

---

## 4. stage별 금지 문장

| stage | 금지 문장 | 왜 금지하나 |
| --- | --- | --- |
| atlas | `Run Table 진행`, `battle도 곧 가능`, `전체 atlas polish` | stage 1개가 전체 큐 진행처럼 보이기 때문 |
| battle | `Battle Table 거의 완료`, `oath도 같이`, `전투 UI 전반` | stakes stage가 전체 battle polish처럼 오독되기 때문 |
| oath | `ending 거의 완료`, `Run Table complete`, `queue closing도 같이` | oath stage와 운영 closing layer가 섞이기 때문 |

### 허용되는 문장 패턴
- `이 메모는 atlas root/state/threshold hook만 기록한다.`
- `이번 review는 battle stakes first-glance만 본다.`
- `이 파일은 oath candidate/green 문장 초안만 기록하고 queue-closing wording은 아직 확정하지 않는다.`
- `현재 미오픈 범위: battle/oath/queue closing.`

---

## 5. QA check stub 규칙

### QA가 첫 줄에 반드시 적을 것
1. stage명
2. 범위 artifact 또는 verdict 묶음
3. 이번 check가 다루지 않는 범위
4. 다음 append 위치

예시
```md
# atlas-route-node DOM sanity

- stage: atlas-route-node
- scope: atlas root / route-node-state / route-threshold
- not in scope: battle-stakes-plaque, oath-banner, queue closing
- next append: overview/focus capture drop 후 locator alignment verdict 추가
```

### 금지 패턴
- `대체로 괜찮음`
- `거의 됨`
- stage명이 없는 `sanity ok`
- candidate/green 문장을 check file 안에서 먼저 쓰는 것

---

## 6. handoff line guard

- handoff line은 manifest와 candidate review packet 전용이다.
- stub 파일에서는 아래처럼 `manifest handoff candidate`까지만 적는다.

예시
```md
## manifest handoff candidate
- atlas-route-node candidate/green wording은 manifest.md와 atlas candidate review packet에서만 확정한다.
- 이 note는 atlas-only boundary와 wording 초안만 유지한다.
```

### 왜 manifest 전용인가
- handoff line이 note/check 곳곳에 흩어지면 stage 문장이 다시 여러 버전으로 갈라진다.
- live kickoff와 candidate review가 이미 completion wording을 닫아 뒀으므로, stub는 evidence 바닥만 말하는 편이 안전하다.

---

## 7. append-only 규칙

- stub 파일은 나중에 `덮어쓰기 보고서`로 바꾸지 않는다.
- evidence가 들어오면 아래 둘 중 하나만 허용한다.
  1. 같은 파일 안에 `## update YYYY-MM-DD HH:MM` 섹션을 append
  2. `-v2`, `-1430` suffix의 새 파일을 만들고 manifest에 superseded note 추가
- 자동화 run log와 tracker는 stage 완료를 append한다. stub 내용 정리를 위해 과거 green entry를 대규모 수정하지 않는다.

---

## 8. 실제 starter kit 파일

이번 슬롯에서 함께 추가하는 Run Table 전용 text stub starter kit:
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_atlas_hook_manifest.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_atlas_dom_sanity.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_atlas_density_review.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_atlas_starter_asset_list.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_atlas_handoff_line.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_hook_manifest.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_dom_sanity.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_density_review.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_starter_asset_list.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_handoff_line.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_oath_hook_manifest.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_oath_dom_sanity.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_oath_density_review.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_oath_starter_asset_list.md`
- `docs/artifacts/run-table-token-delivery/stubs/stage_stub_oath_handoff_line.md`
- `docs/artifacts/run-table-token-delivery/stubs/README.md`

### 사용 순서
1. 대응 `manifest.md` 템플릿을 먼저 연다.
2. stage에 맞는 stub 파일 5종을 복사한다.
3. 날짜, owner, expected capture/file name, stage boundary를 실제 턴에 맞게 바꾼다.
4. 실제 evidence가 들어오면 append하거나 `v2`를 만든다.
5. candidate/green 문장은 stub가 아니라 manifest와 tracker/run log에서만 확정한다.

---

## 리스크 & 후속과제
- 가장 큰 리스크는 atlas live kickoff가 `manifest/stub 초안`을 요구하는 상태에서도 실제로는 빈 파일에서 시작해 `Run Table 전체`, `battle도 같이` 같은 wording drift가 다시 생기는 것이다.
- 두 번째 리스크는 battle/oath stage가 atlas와 같은 톤의 generic note를 재사용해 stage별 역할 구분이 파일 첫 줄부터 흐려지는 것이다.
- 세 번째 리스크는 handoff line이 stub까지 중복 기록돼 candidate/green wording이 여러 버전으로 갈라지는 것이다.
- 따라서 다음 실제 atlas turn 착수 전에는 `archive bootstrap packet -> atlas live kickoff packet -> 이 stub packet -> stubs/README.md` 순서로 먼저 읽고, 빈 note/check/asset file를 generic placeholder로 시작하지 말아야 한다.

## immediate next actions
1. 다음 atlas turn 직전 `stage_manifest_atlas_route_node.md`와 atlas stub 5종을 같이 연다.
2. 빈 `notes/`·`checks/`·`assets/` 파일을 만들지 말고 `docs/artifacts/run-table-token-delivery/stubs/` 파일을 복사해 첫 문장부터 atlas-only 책임을 적는다.
3. battle/oath도 같은 방식으로 stage-specific stub를 복사하되, atlas green 전에는 battle stub를 실제 archive에 drop하지 않는다.
4. candidate/green 문장은 계속 manifest와 run log에서만 확정한다.
