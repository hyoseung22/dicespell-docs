# DiceSpell `Run Table` Battle Stakes Plaque Archive File Map 요약

## 3줄 요약
- 새 문서 `docs/dicespell_game_flow_token_battle_archive_file_map_packet_kr.md`는 battle live archive를 열 때 **template / stub / manifest example / source packet이 각각 어떤 실제 파일 경로로 이어지고 누가 언제 채워야 하는지**를 한 장으로 고정한다.
- 목적은 새 battle 화면 설계를 더하는 것이 아니라, 이미 충분한 battle 문서 세트를 **실제 stage 폴더 기준 file routing + owner fill order**로 다시 압축하는 것이다.
- 이제 battle은 `문서를 읽었는데 실제로 어떤 파일을 먼저 만들어야 하지?`를 감으로 처리하지 않고, `live archive root 하나 + template/stub/example 역할 분리 + programmer → UI/아트 → QA → PM` 순서로만 닫는다.

## 한 줄 목표
battle 첫 실전 archive를 `문서 묶음`에서 끝내지 않고, **실제 파일 경로와 작성 순서가 정리된 handoff-ready archive workflow**로 만든다.

## 왜 중요한가
지금까지도 아래는 이미 있었다.
- source-of-truth map / gap ledger
- archive bootstrap packet
- artifact stub packet
- manifest fill packet
- battle stage packet / live kickoff / semantic anchor
- required artifacts / candidate review
- template / stub / manifest example 파일들

하지만 실제 first live battle archive 직전에는 여전히 이런 질문이 남아 있었다.
- template는 어디에 복사하고 manifest example은 어디까지 참고만 해야 하나?
- live archive destination은 정확히 어디인가?
- `manifest 1 + note 3 + check 1 + capture 2 + asset note 1`를 누가 어떤 순서로 채우나?
- QA candidate와 PM green은 어느 파일에서 마무리되나?

이번 문서는 그 마지막 실무 공백을 **source doc → live file route map + owner fill order**로 닫는다.

## 가장 중요한 결론
### 1) 실제 제출 경로는 하나다
모든 live 작성은 아래 경로에서만 이뤄진다.

```text
docs/artifacts/run-table-token-delivery/battle-stakes-plaque/
```

즉 아래는 전부 reference layer다.
- `docs/artifacts/run-table-token-delivery/templates/`
- `docs/artifacts/run-table-token-delivery/stubs/`
- `docs/artifacts/run-table-token-delivery/examples/manifest_example_battle_stakes_plaque.md`

### 2) template / stub / example 역할이 다르다
- `template` = live archive의 **빈 뼈대**
- `stub` = live file의 **첫 문장 출발점**
- `manifest example` = live file의 **상태 어조 참고용**

그래서 example 파일을 그대로 제출 artifact처럼 링크하면 안 된다.

### 3) owner 순서는 고정이다
1. 프로그래머가 `manifest.md`, `notes/hook-manifest.md`, `checks/dom-sanity.md` 바닥을 만든다.
2. UI가 `captures/battle-1440-overview.png`, `captures/battle-1440-stakes-focus.png`, `notes/density-review.md`를 채운다.
3. 아트가 `assets/starter-asset-list.md`를 채운다.
4. QA가 `notes/handoff-line.md`에서 `archive-ready candidate`를 기록한다.
5. PM이 archive path와 함께 `battle-stakes-plaque green, oath-banner unlock`을 남긴다.

## owner별 한 줄 결론
| 역할 | 이 문서에서 가져갈 핵심 |
| --- | --- |
| 프로그래머 | template/stub을 live archive file로 먼저 옮겨 path를 잠그기 전엔 어떤 evidence도 candidate가 아니다 |
| UI | example은 참고용이고, 실제 제출은 `captures/`와 `notes/density-review.md` live file에만 남긴다 |
| 아트 | starter sanity는 `assets/starter-asset-list.md` live file 하나를 채우는 stage evidence다 |
| QA | `notes/handoff-line.md`는 existing live files 위에서 candidate를 분리 기록하는 review file이다 |
| PM | final green은 archive path와 함께 남겨야 하고 example 문장을 그대로 green line으로 쓰면 안 된다 |

## live archive 핵심 파일
| live file | 역할 |
| --- | --- |
| `manifest.md` | stage 상태 / owner summary / boundary / next step / handoff line |
| `notes/hook-manifest.md` | battle root / stakes slot / hierarchy locator와 non-change line |
| `checks/dom-sanity.md` | hook 존재/정합성 점검 |
| `captures/battle-1440-overview.png` | current stakes + battle table first-glance 증거 |
| `captures/battle-1440-stakes-focus.png` | current / next / grimoire slot 관계 집중 증거 |
| `notes/density-review.md` | 유지·약함·과함 verdict |
| `assets/starter-asset-list.md` | Stakes Plaque Starter 충분성 verdict |
| `notes/handoff-line.md` | candidate / green 복붙 문장 |

## 리스크 & 후속과제
- template/stub/example 역할이 다시 섞이면 honest status보다 앞선 wording이 archive에 들어올 수 있다.
- owner가 서로 다른 경로를 제출 경로로 착각하면 reviewer가 같은 archive를 보지 못한다.
- PM이 archive path 없이 green만 남기면 stage archive discipline이 약해진다.

## 즉시 다음 액션
1. 실제 battle turn에서는 source-of-truth map / gap ledger 뒤에 이 file map packet으로 live destination 경로를 먼저 고정한다.
2. 프로그래머는 template/stub을 live archive로 옮겨 바닥 파일 3종을 먼저 만든다.
3. UI/아트는 reference layer를 참고하되 live destination file만 채운다.
4. QA는 candidate를 분리 기록하고, PM은 archive path와 final green을 같이 남긴다.

## 원본 문서
- source handoff: `./dicespell_game_flow_token_battle_archive_file_map_packet_kr.md`
- battle live kickoff: `./dicespell_game_flow_token_battle_live_kickoff_packet_kr.md`
- battle semantic anchor: `./dicespell_game_flow_token_battle_semantic_anchor_packet_kr.md`
- battle required artifacts: `./dicespell_game_flow_token_battle_required_artifacts_packet_kr.md`
- battle candidate review: `./dicespell_game_flow_token_battle_candidate_review_packet_kr.md`
