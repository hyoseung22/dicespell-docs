# DiceSpell `Run Table` Battle Stakes Plaque Archive File Map Packet

## 3줄 요약
- 이 문서는 `battle live kickoff`, `semantic anchor`, `required artifacts`, `candidate review`까지 이미 충분한 상태에서도 마지막으로 남아 있던 실행 공백인 **`좋아, 그러면 battle live archive를 실제로 열면 template / stub / manifest example / source packet을 각각 어떤 실제 파일 경로로 옮기고, 누가 언제 어떤 파일을 채워야 하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 전투 HUD를 더 설계하는 것이 아니라, battle archive를 **source docs → starter files → live archive destinations → owner fill order** 4층으로 다시 정렬해 `문서는 충분한데 실제 stage 폴더에서 어떤 파일을 먼저 만들고 어떤 파일은 끝까지 reference layer로 남겨야 하는지`가 흐려지는 마지막 운영 드리프트를 막는 것이다.
- 첫 화면만 읽어도 `어떤 문서가 어떤 실제 파일로 이어지는지`, `template/stub/example은 어디에 쓰는지`, `프로그래머/UI/아트/QA/PM이 어떤 파일을 언제 채워야 하는지`가 바로 보여야 한다.

## 한 줄 목표
`battle-stakes-plaque` archive를 **문서 읽기**에서 끝내지 않고 **실제 파일 라우팅과 owner별 작성 순서**까지 handoff-ready하게 만든다.

---

## 왜 이 문서가 필요한가
지금 battle second-stage 문서 스택은 이미 거의 다 닫혀 있다.

- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`
- `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_live_kickoff_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_semantic_anchor_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md`

또 실제 starter asset도 이미 있다.

- template: `docs/artifacts/run-table-token-delivery/templates/stage_manifest_battle_stakes_plaque.md`
- stubs: `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_*`
- manifest example: `docs/artifacts/run-table-token-delivery/examples/manifest_example_battle_stakes_plaque.md`

하지만 실제 battle first live archive 직전에는 여전히 작은 실무 공백이 남는다.

1. `문서를 읽는 순서`와 `실제 파일을 만드는 순서`는 다르다. 읽기 위계를 알아도 implementer는 여전히 `어떤 파일을 어느 폴더에 먼저 놓지?`에서 멈출 수 있다.
2. template / stub / manifest example이 모두 있으면 오히려 어떤 것은 live 출발점이고 어떤 것은 wording reference인지가 흐려진다.
3. `manifest 1 + note 3 + check 1 + capture 2 + asset note 1` 구조는 알아도, 각 실제 파일이 어떤 source doc와 연결되는지 한눈에 안 보이면 owner가 battle 문서를 다시 여러 장 열어야 한다.
4. 이 공백이 남아 있으면 battle archive는 다시 `파일은 있는데 아직 누구 책임인지 모르는 폴더`로 남아 candidate review 직전 stage discipline이 약해진다.

즉 지금 필요한 것은 새 battle 방향 문서가 아니라, **기존 battle 문서를 실제 archive file routing 언어로 다시 압축한 packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. live archive tree`, `3. source doc → file route map` | `battle live kickoff`, `battle semantic anchor`, `battle required artifacts` | template/stub은 live archive 출발점이고 manifest example은 복사본이 아니라 wording reference다 |
| UI | `2. live archive tree`, `4. owner fill order`, `5-2. UI handoff` | `battle required artifacts`, `battle candidate review` | overview/focus capture와 density note는 실제 destination path가 먼저 잠긴 뒤에만 채운다 |
| 아트 | `3. source doc → file route map`, `5-3. art handoff` | `artifact stub`, `battle required artifacts` | starter sanity는 `assets/` live file을 채우는 stage이며 manifest example은 status 어조 참고용이지 art note 대체물이 아니다 |
| QA | `4. owner fill order`, `5-4. QA handoff`, `6. review-ready route` | `battle required artifacts`, `battle candidate review` | candidate line은 programmer/UI/art 파일이 먼저 잠긴 뒤 pass/fail로 적는 review stage 문장이다 |
| PM/기획 | `0. first screen 결론`, `4. owner fill order`, `5-5. PM handoff` | `battle candidate review`, `queue readiness board` | PM은 stage archive 경로와 final green/unlock line을 같이 기록하며 manifest example 문장을 그대로 final line으로 쓰지 않는다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- battle archive를 실제로 열면 어떤 source doc가 어떤 실제 파일 경로로 이어지지?
- template / stub / manifest example은 각각 언제 쓰지?
- owner별로 어떤 파일을 언제 채우고 누가 마지막 handoff line을 적지?

### 가장 짧은 답
- `template`는 live archive의 **빈 뼈대**다.
- `stub`는 live archive의 **첫 문장 출발점**이다.
- `manifest example`은 live archive의 **상태 어조 reference**다.
- 실제 작성은 항상 `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/` 아래 live files에만 한다.
- owner별 순서는 `프로그래머 바닥 파일 → UI/아트 evidence → QA candidate → PM green`이다.

### 한 줄 판정 규칙
battle archive file routing이 잘 됐다는 말은 `파일이 많다`가 아니라 **`모든 owner가 같은 destination path를 source-of-truth로 쓰고, template/stub/example의 역할을 섞지 않았다`**는 뜻이다.

---

## 1. core structure

## 3줄 요약
- battle archive는 `live archive destination` 하나를 중심으로, template/stub/example/source packet이 그 주변으로 연결되는 구조다.
- 실제 작성 파일과 참고 파일을 분리하지 않으면 manifest example wording이 현재 honest status보다 앞서서 candidate/green language를 오염시키기 쉽다.
- 따라서 이 문서는 파일 트리를 설명하는 동시에, 각 파일에 붙는 owner와 작성 타이밍을 함께 잠근다.

## 한 줄 목표
`무슨 파일이 필요한가`에서 끝내지 않고 `어느 경로에 누가 언제 채우는가`까지 묶는다.

---

## 2. live archive tree

## 3줄 요약
- 아래 트리는 battle 첫 live turn에서 실제로 채워지는 유일한 archive root다.
- template/stub/example은 이 트리 밖에 남고, live 작성은 이 트리 안에서만 이뤄진다.
- 즉 `docs/artifacts/run-table-token-delivery/examples/manifest_example_battle_stakes_plaque.md`는 reference 경로지 제출 경로가 아니다.

## 한 줄 목표
실제 제출/검수/기록은 항상 동일한 archive root 하나를 바라보게 만든다.

```text
docs/artifacts/run-table-token-delivery/battle-stakes-plaque/
├── manifest.md
├── notes/
│   ├── hook-manifest.md
│   ├── density-review.md
│   └── handoff-line.md
├── checks/
│   └── dom-sanity.md
├── captures/
│   ├── battle-1440-overview.png
│   └── battle-1440-stakes-focus.png
└── assets/
    └── starter-asset-list.md
```

### 파일별 한 줄 역할
| live file | 역할 | 최초 작성자 | 최종 확인자 |
| --- | --- | --- | --- |
| `manifest.md` | stage 상태 / owner summary / boundary / next step / handoff line | 프로그래머 | PM |
| `notes/hook-manifest.md` | battle root / stakes slot / hierarchy locator와 non-change line | 프로그래머 | QA |
| `checks/dom-sanity.md` | hook 존재/정합성 점검 | 프로그래머 초안 또는 QA 병행 | QA |
| `captures/battle-1440-overview.png` | current stakes + battle table first-glance 증거 | UI | QA |
| `captures/battle-1440-stakes-focus.png` | current / next / grimoire slot 관계 집중 증거 | UI | QA |
| `notes/density-review.md` | overview/focus 기준 유지·약함·과함 verdict | UI | QA |
| `assets/starter-asset-list.md` | Stakes Plaque Starter 충분성 / 비범위 | 아트 | PM |
| `notes/handoff-line.md` | candidate / green 복붙 문장 초안 | QA 초안 또는 PM 확정 | PM |

---

## 3. source doc → file route map

## 3줄 요약
- 이 표는 `어떤 문서를 읽어 어떤 live file을 채우는가`를 묶는다.
- source packet은 규칙을 주고, template/stub은 빈 틀을 주고, example은 상태 어조를 맞춘다.
- live archive는 항상 destination에만 작성한다.

## 한 줄 목표
문서와 실제 파일을 1:1로 연결해 owner가 다시 battle 문서를 뒤적이지 않게 만든다.

| source asset | 분류 | live destination | 언제 쓰나 | 사용 방식 | 절대 하면 안 되는 것 |
| --- | --- | --- | --- | --- | --- |
| `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md` | source packet | archive root 전체 | stage 폴더 만들 때 | 폴더 구조와 starter bundle 기준 확인 | 이 문서 자체를 제출 artifact처럼 취급 |
| `docs/artifacts/run-table-token-delivery/templates/stage_manifest_battle_stakes_plaque.md` | template | `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/manifest.md` | archive 생성 직후 | 파일 뼈대를 복사해 live manifest 생성 | template 파일 자체를 수정해서 제출 |
| `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md` | source packet | `manifest.md` | manifest 첫 작성 때 | 필드 의미/금지 문장 확인 | example status를 먼저 복붙 |
| `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_hook_manifest.md` | stub | `notes/hook-manifest.md` | hook note 생성 직후 | battle root/current-next-grimoire/hierarchy 첫 줄 초안 | atlas/oath hook 메모 선기록 |
| `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_dom_sanity.md` | stub | `checks/dom-sanity.md` | check 생성 직후 | hook 존재/누락 질문 구조 복사 | 전체 Battle Table 품평 메모로 확장 |
| `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_density_review.md` | stub | `notes/density-review.md` | UI note 생성 직후 | first-glance / slot hierarchy verdict 초안 | atlas/oath density 회고 혼입 |
| `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_starter_asset_list.md` | stub | `assets/starter-asset-list.md` | art sanity 시작 시 | starter 충분성 / 비범위 초안 | combat shell 전체 리뉴얼 발주 문서화 |
| `docs/artifacts/run-table-token-delivery/stubs/stage_stub_battle_handoff_line.md` | stub | `notes/handoff-line.md` | candidate/green note 생성 직후 | candidate/green 분리 문장 초안 | `Battle Table 진행` 같은 큰 총괄 문장 |
| `docs/dicespell_game_flow_token_battle_stakes_stage_packet_kr.md` | source packet | live archive 전체 | archive 경계 잠글 때 | stage boundary와 금지 범위 확인 | stakes plaque stage를 battle 전체 polish로 확대 |
| `docs/dicespell_game_flow_token_battle_live_kickoff_packet_kr.md` | source packet | live archive 전체 | archive 생성 직후 | kickoff 순서와 금지 범위 확인 | kickoff 메모를 green 문장으로 승격 |
| `docs/dicespell_game_flow_token_battle_semantic_anchor_packet_kr.md` | source packet | `notes/hook-manifest.md`, `checks/dom-sanity.md` | live edit 직전 | `renderBattle(summary)` / `.battle-stakes-grid` / `[data-stakes-slot]` / boundary locator 재확인 | action panel·dice tray·log·atlas/oath를 같은 locator층으로 섞기 |
| `docs/dicespell_game_flow_token_battle_required_artifacts_packet_kr.md` | source packet | live archive 전체 | artifact 8종 채울 때 | 각 file이 답해야 하는 질문 확인 | file 존재만으로 candidate 선언 |
| `docs/artifacts/run-table-token-delivery/examples/manifest_example_battle_stakes_plaque.md` | example file | `manifest.md` | manifest 초안이 생긴 뒤 | status/handoff line 어조 참고 | example 문장을 현재 status와 무관하게 선복사 |
| `docs/dicespell_game_flow_token_battle_candidate_review_packet_kr.md` | signoff packet | `notes/handoff-line.md`, `manifest.md` | candidate review 이후 | QA candidate / PM green line 분리 | candidate packet 문장을 live artifact 생성 전에 사용 |

---

## 4. owner fill order

## 3줄 요약
- 아래 순서는 file creation과 signoff를 섞지 않기 위한 실제 운영 순서다.
- 파일은 많지만 핵심은 `누가 먼저 live destination을 만든 뒤 다음 owner를 부르는가`다.
- 이 순서를 어기면 example이 현재 상태보다 앞서거나 QA/PM 문장이 조기 사용된다.

## 한 줄 목표
battle archive를 `파일 생성 -> 질문 채움 -> verdict 기록` 사다리로 잠근다.

| 순서 | owner | 먼저 여는 source | 실제 채우는 live file | 이 단계 종료 조건 |
| --- | --- | --- | --- | --- |
| 1 | 프로그래머 | archive bootstrap + manifest fill + battle stage packet | `manifest.md` | stage root와 manifest가 live archive에 생성됨 |
| 2 | 프로그래머 | artifact stub + live kickoff + semantic anchor | `notes/hook-manifest.md`, `checks/dom-sanity.md` | root/slot/hierarchy/non-change 4축이 적힘 |
| 3 | UI | required artifacts + candidate review | `captures/battle-1440-overview.png`, `captures/battle-1440-stakes-focus.png`, `notes/density-review.md` | overview/focus evidence와 density verdict가 연결됨 |
| 4 | 아트 | artifact stub + required artifacts | `assets/starter-asset-list.md` | Stakes Plaque Starter 충분성 verdict와 비범위가 적힘 |
| 5 | QA | candidate review + required artifacts | `notes/handoff-line.md`, 필요 시 `checks/dom-sanity.md` | `archive-ready candidate` line과 artifact alignment verdict가 기록됨 |
| 6 | PM | candidate review + queue readiness board | `manifest.md`, `notes/handoff-line.md` | final green / oath unlock line이 같은 archive 경로와 함께 남음 |

### 순서상 꼭 기억할 것
1. `examples/manifest_example_battle_stakes_plaque.md`는 누구도 직접 수정하지 않는다.
2. QA는 `notes/handoff-line.md`에 candidate line을 적지만 archive root 구조를 새로 만들지 않는다.
3. PM green은 `manifest.md` 또는 `notes/handoff-line.md`에 archive path와 함께 남는다.
4. `oath-banner unlock`은 PM final line 전에는 어떤 live file에도 쓰지 않는다.

---

## 5. role-specific handoff points

## 5-1. programmer handoff

### 3줄 요약
- 프로그래머는 battle archive에서 가장 먼저 실제 destination 파일을 만든다.
- template/stub을 live file로 변환하는 사람이므로 `참고 경로`와 `제출 경로`를 가장 엄격하게 분리해야 한다.
- 프로그래머가 경로를 흐리면 이후 owner 모두가 다른 파일을 보게 된다.

### 한 줄 목표
live archive의 뼈대와 hook/check 바닥을 먼저 잠근다.

- 먼저 읽을 것: `archive bootstrap`, `manifest fill`, `battle stage packet`, `battle live kickoff`, `battle semantic anchor`
- 먼저 만들 것: `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md`
- 하지 말 것:
  - template/stub 파일 자체 수정
  - manifest example wording을 상태 판단 전에 복붙
  - `atlas`, `oath`, `queue closing`, `Battle Table 전체 polish` 범위 메모 혼입

## 5-2. UI handoff

### 3줄 요약
- UI는 capture와 density note를 live destination 경로에만 남긴다.
- manifest example은 상태 어조 참고용이지 제출물 대체물이 아니다.
- UI의 핵심은 예쁜 샷이 아니라 first-glance / stakes-focus 질문과 density verdict를 연결하는 것이다.

### 한 줄 목표
capture 2장과 `density-review.md`를 같은 live archive 안에서 닫는다.

- 먼저 읽을 것: `battle required artifacts`, `battle candidate review`
- 실제 채울 것: `captures/*`, `notes/density-review.md`
- 하지 말 것:
  - example manifest를 제출 artifact처럼 링크
  - candidate / green 문장 선기록
  - action panel glamour shot, atlas/oath 캡처 혼입

## 5-3. art handoff

### 3줄 요약
- 아트는 이번 stage에서 가장 작은 live asset note 하나를 채운다.
- 작다고 해서 자유 형식이 아니라, stub과 source packet의 역할을 가장 엄격하게 분리해야 한다.
- starter sanity note는 battle stage evidence고 backlog 문서가 아니다.

### 한 줄 목표
`assets/starter-asset-list.md`에 Stakes Plaque Starter 충분성 verdict만 남긴다.

- 먼저 읽을 것: `artifact stub`, `battle required artifacts`
- 실제 채울 것: `assets/starter-asset-list.md`
- 하지 말 것:
  - 신규 combat shell 요청서로 확대
  - manifest example 어조를 art note처럼 오용
  - oath/ending 공용 자산 확장을 battle 필수 조건으로 승격

## 5-4. QA handoff

### 3줄 요약
- QA는 live archive 안에서 마지막으로 `candidate` line과 alignment verdict를 정리한다.
- QA는 새 파일을 만드는 역할이 아니라 기존 live files가 같은 battle 질문을 쓰는지 검증하는 역할이다.
- 그래서 required artifacts/candidate review packet을 wording reference보다 먼저 봐야 한다.

### 한 줄 목표
existing live files 위에서 `archive-ready candidate`와 `green`을 분리 기록한다.

- 먼저 읽을 것: `battle required artifacts`, `battle candidate review`
- 실제 업데이트할 것: `notes/handoff-line.md`, 필요 시 `checks/dom-sanity.md`
- 하지 말 것:
  - example wording을 현재 상태 확인 전에 복붙
  - green / oath unlock 기록
  - 누락 파일이 있는데도 generic `대체로 완료` 기록

## 5-5. PM handoff

### 3줄 요약
- PM은 stage archive 전체 경로와 final line을 함께 닫는 사람이다.
- PM이 path를 남기지 않고 green만 쓰면 archive discipline이 무너진다.
- battle의 마지막 안정장치는 PM이 `어느 경로를 닫았는지`를 같이 기록하는 것이다.

### 한 줄 목표
archive path와 `battle-stakes-plaque green`을 같은 stage 언어로 함께 남긴다.

- 먼저 읽을 것: `battle candidate review`, `queue readiness board`
- 실제 업데이트할 것: `manifest.md` 또는 `notes/handoff-line.md`
- 하지 말 것:
  - example status 문장을 final green으로 직접 승격
  - archive 경로 없는 green 기록
  - `Battle Table 진행`, `Run Table 거의 완료` 같은 큰 총괄 문장

---

## 6. review-ready route

## 3줄 요약
- candidate는 단순히 파일이 다 있다는 뜻이 아니라, 모든 live destination이 올바른 source와 연결돼 있다는 뜻이다.
- template/stub/example 역할이 섞이면 파일 수는 맞아도 candidate가 아니다.
- 아래 표는 candidate 직전에 무엇을 다시 확인해야 하는지 정리한다.

## 한 줄 목표
battle candidate를 `파일 존재`가 아니라 `file routing integrity`로 판단한다.

| 확인 항목 | 맞는 상태 | 틀린 상태 |
| --- | --- | --- |
| destination path | 모든 evidence가 `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/` 아래 있음 | `templates/`, `stubs/`, `examples/`에 직접 기록이 남아 있음 |
| manifest route | `stage_manifest_battle_stakes_plaque.md`를 기반으로 live `manifest.md` 생성 | template 파일 자체를 현재 상태로 수정 |
| stub route | stub 문장은 live note/check에 옮겨져 있음 | stub 파일이 그대로 제출물처럼 링크됨 |
| example route | manifest example은 status/handoff 어조 참고용 | example 문장을 실제 review artifact처럼 사용 |
| owner route | programmer → UI/아트 → QA → PM 순서가 archive에 반영됨 | capture가 먼저 오고 hook/check 바닥이 없음 |

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| template/stub/example의 역할이 다시 섞이는 위험 | honest status보다 앞선 wording과 green language가 조기 유입될 수 있다 | source docs → live destination → owner fill order를 한 표로 재고정했다 | 다음 실제 battle archive 생성 직전 |
| owner가 서로 다른 경로를 제출 경로로 착각하는 위험 | reviewer가 같은 archive를 못 보고 handoff가 다시 채팅/구두 설명으로 흩어진다 | live archive root를 `docs/artifacts/run-table-token-delivery/battle-stakes-plaque/` 하나로 고정했다 | 다음 overview/stakes-focus capture drop 직전 |
| manifest example이 실제 artifact처럼 복붙되는 위험 | candidate / PM green이 현재 status보다 앞서 과장될 수 있다 | example은 status wording reference, 제출물은 live destination이라는 규칙을 명시했다 | 다음 candidate 기록 직전 |
| PM이 archive path 없이 final green만 남기는 위험 | stage archive 1개 = green 문장 1개 원칙이 약해진다 | PM handoff에 path + green + oath unlock 동시 기록 원칙을 추가했다 | 다음 PM green 기록 직전 |

---

## 8. immediate next actions
1. 다음 실제 battle turn은 `source-of-truth map`과 `gap ledger`를 읽은 뒤 바로 이 file map packet으로 live destination 경로를 먼저 잠근다.
2. 프로그래머는 template/stub을 live archive로 옮겨 `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md`를 먼저 만든다.
3. UI/아트는 battle packet을 참고하되 실제 작성은 `captures/`, `notes/`, `assets/` live files에만 남긴다.
4. QA는 `notes/handoff-line.md`에서 candidate를 기록하고, PM은 archive path와 함께 final green / oath unlock을 기록한다.
5. `templates/`, `stubs/`, `examples/manifest_example_battle_stakes_plaque.md`는 끝까지 reference layer로만 남기고, 제출 경로로 사용하지 않는다.

---

## 원본 문서 연결
- source-of-truth map: `./dicespell_game_flow_token_source_of_truth_map_kr.md`
- gap ledger: `./dicespell_game_flow_token_gap_ledger_kr.md`
- archive bootstrap: `./dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- artifact stub: `./dicespell_game_flow_token_artifact_stub_packet_kr.md`
- manifest fill: `./dicespell_game_flow_token_manifest_fill_packet_kr.md`
- battle stage packet: `./dicespell_game_flow_token_battle_stakes_stage_packet_kr.md`
- battle live kickoff: `./dicespell_game_flow_token_battle_live_kickoff_packet_kr.md`
- battle semantic anchor: `./dicespell_game_flow_token_battle_semantic_anchor_packet_kr.md`
- battle required artifacts: `./dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- battle candidate review: `./dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
- readable companion: `./dicespell_game_flow_token_battle_archive_file_map_summary_kr.md`
