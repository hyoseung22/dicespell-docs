# DiceSpell Run Table 토큰 스텁 킷 요약

## 3줄 요약
- 새 문서 `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`는 `archive bootstrap`, `atlas live kickoff`, `required artifacts`, `candidate review` 다음에 남아 있던 마지막 작은 실전 공백, 즉 `notes/`·`checks/`·`assets/` starter file 첫 문장을 무엇으로 시작할지`를 고정한다.
- 핵심은 빈 파일이나 generic placeholder 대신, stage 책임과 금지 범위를 먼저 적는 **honest stub**로 atlas/battle/oath archive 바닥을 까는 것이다.
- 이번에는 source packet뿐 아니라 `docs/artifacts/run-table-token-delivery/stubs/` 아래에 atlas/battle/oath용 텍스트 stub starter 파일도 같이 추가해 바로 복사·수정 가능한 형태로 맞췄다.

## 한 줄 목표
Run Table stage archive를 `폴더는 있는데 note/check는 빈 상태`로 두지 않고, **manifest template + stage-specific stub 5종** 조합으로 첫 3분 안에 같은 기록 언어를 깔게 만든다.

## 왜 중요한가
지금까지는 아래가 이미 있었다.
- stage archive 구조
- starter manifest 템플릿
- required artifacts
- candidate/green wording
- atlas live kickoff 순서

하지만 실제 첫 live turn 직전에는 여전히 이런 질문이 남아 있었다.
- hook manifest 첫 줄에 무엇을 적어야 하지?
- dom sanity는 언제부터 범위를 써 두어야 하지?
- density review는 감상문 대신 어떤 verdict로 시작하지?
- handoff line stub는 어디까지 초안이고 어디서부터 candidate wording이 되지?

이번 문서는 그걸 **text stub 규칙 + 복사 가능한 starter file 15종**으로 닫는다.

## 누가 어떤 부분을 보면 되나
| 역할 | 먼저 볼 부분 | 핵심 결론 |
| --- | --- | --- |
| 프로그래머 | atlas/battle/oath hook manifest stub | locator/nondrift 메모는 빈 파일이 아니라 stage-specific 범위 선언으로 시작한다 |
| UI | density / capture reservation rule | fake PNG를 만들지 않고 text stub와 manifest에 예약한다 |
| 아트 | starter asset stub | starter sanity는 새 공통 frame 요구가 아니라 token drift/no-drift 문장으로 시작한다 |
| QA | QA check stub | stage명, scope, not-in-scope를 첫 줄에 적는다 |
| PM/기획 | handoff line guard | candidate/green 문장은 manifest와 review packet 전용이고 stub는 근거만 말한다 |

## 이번에 같이 생긴 실제 파일
- `docs/artifacts/run-table-token-delivery/stubs/README.md`
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

## 리스크 & 후속과제
- stub가 없으면 atlas live kickoff가 요구한 `manifest/stub 초안`이 다시 빈 파일과 generic wording으로 시작할 수 있다.
- atlas/battle/oath가 같은 wording의 generic note를 쓰면 stage boundary가 파일 첫 줄부터 흐려진다.
- handoff line을 stub까지 확정문으로 쓰기 시작하면 candidate/green wording이 여러 버전으로 갈라진다.

## immediate next actions
1. 다음 atlas turn 직전 `stage_manifest_atlas_route_node.md`와 atlas stub 5종을 함께 연다.
2. 빈 `notes/`·`checks/`·`assets/` 파일을 만들지 말고 새 stub를 복사해 첫 문장부터 atlas-only 책임을 적는다.
3. battle/oath도 같은 규칙을 재사용하되, 각 stage green 전에는 다음 stage stub를 실제 archive에 drop하지 않는다.
4. candidate/green 문장은 계속 manifest와 run log에서만 확정한다.

## 원본 문서
- source handoff: `./dicespell_game_flow_token_artifact_stub_packet_kr.md`
- archive bootstrap: `./dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- atlas live kickoff: `./dicespell_game_flow_token_atlas_live_kickoff_packet_kr.md`
