# DiceSpell `Run Table` Token Manifest Fill 요약

## 3줄 요약
- 새 문서 `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`는 `archive bootstrap`, `artifact stub`, `atlas live kickoff`, `required artifacts`, `candidate review`까지 충분한 상태에서도 마지막으로 남아 있던 작은 실전 공백, 즉 `manifest.md`의 각 칸을 정확히 어떤 문장으로 채워야 하는가를 field-level로 고정한다.
- 핵심은 새 토큰이나 새 stage를 더 설계하는 것이 아니라, `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line`이 서로 다른 질문에 답하게 만들어 generic progress 문장과 큰 총괄 초록불을 막는 것이다.
- 이제 다음 Run Table refinement는 `source-of-truth map -> gap ledger -> archive bootstrap -> artifact stub -> atlas live kickoff -> manifest fill packet` 순서로 읽고, atlas archive의 manifest부터 honest stage contract로 채운 뒤 required artifacts를 모으면 된다.

**한 줄 목표:** Run Table stage archive의 `manifest.md`를 체크리스트가 아니라 `현재 stage / 아직 안 여는 범위 / 다음 unlock / 공식 handoff 문장`을 잠그는 실행 계약서로 만든다.

---

## 왜 이 문서가 필요한가
지금까지 문서는 `어떤 archive를 만들지`, `starter file 첫 줄을 무엇으로 시작할지`, `required artifacts 8종이 무엇인지`, `candidate/green을 어떤 review 순서로 올릴지`까지는 충분히 닫혀 있었다. 하지만 실제 실전 archive 직전에는 여전히 `좋아, manifest 각 칸에는 정확히 어떤 문장을 써야 하지?`가 여러 packet에 흩어져 있었다.

이 공백이 남아 있으면 아래 문제가 다시 생긴다.
- `status`에 `archive-ready candidate`와 `green`을 섞는다.
- `owner summary`는 atlas-only인데 `handoff line`은 `Run Table 진행`처럼 커진다.
- `boundary / non-change`가 비어 battle/oath가 암묵적으로 같이 열린 것처럼 보인다.
- `next allowed step`에 다음 unlock이 아니라 vague future plan이 들어간다.

즉 지금 필요한 것은 새 토큰 설계가 아니라, manifest의 다섯 칸을 honest stage vocabulary로 채우는 방법 자체를 잠그는 것이다.

---

## 가장 중요한 결론

### 1. manifest는 다섯 칸만 정확하면 된다
- `status`
- `owner summary`
- `boundary / non-change`
- `next allowed step`
- `handoff line`

### 2. 각 칸은 서로 다른 질문에 답해야 한다
- `status`: 지금 단계가 무엇인가
- `owner summary`: 이번 stage에서 무엇만 닫는가
- `boundary / non-change`: 무엇은 아직 열지 않는가
- `next allowed step`: green 뒤에 무엇이 열리는가
- `handoff line`: QA/PM이 복붙할 공식 문장은 무엇인가

### 3. 좋은 manifest는 `무엇을 했다`보다 `무엇만 했고 무엇은 아직 안 했다`를 먼저 말한다
- `atlas-route-node route state와 threshold 읽힘까지만 닫는다`
- `battle/oath/queue closing은 아직 열지 않는다`
- `battle-stakes-plaque unlock only after atlas-route-node green`
- `atlas-route-node archive-ready candidate ...`

### 4. generic progress 문장은 계속 금지다
아래 표현은 여전히 금지다.
- `Run Table 진행`
- `거의 완료`
- `battle도 가능할 듯`
- `전체 polish 예정`
- `사실상 green`

---

## 역할별로 바로 가져갈 포인트

| 역할 | 이번 문서에서 바로 가져갈 포인트 |
| --- | --- |
| 프로그래머 | manifest는 코드 수정 후 요약이 아니라 수정 전 stage boundary를 잠그는 파일이다 |
| UI | `owner summary`와 `boundary`는 visual 회고가 아니라 이번 surface 범위를 줄이는 칸이다 |
| 아트 | manifest에서도 starter sanity만 말하고 공통 리뉴얼은 적지 않는다 |
| QA | candidate/green은 `status`와 `handoff line`이 honest하게 분리돼 있을 때만 올린다 |
| PM/기획 | `next allowed step`은 unlock chain만 말해야 하고, vague future plan을 적으면 안 된다 |

---

## 즉시 다음 액션
1. atlas stage archive를 열 때 starter manifest를 복사한다.
2. `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line`부터 먼저 채운다.
3. `status`는 현재 단계만 적고, candidate/green은 signoff 뒤에만 올린다.
4. `next allowed step`에는 현재 stage 내부 TODO가 아니라 다음 unlock chain만 적는다.
5. 그 뒤에만 required artifacts note와 capture를 stage archive 안에 drop한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_manifest_fill_packet_kr.md`
- `docs/dicespell_game_flow_token_archive_bootstrap_packet_kr.md`
- `docs/dicespell_game_flow_token_artifact_stub_packet_kr.md`
- `docs/dicespell_game_flow_token_atlas_live_kickoff_packet_kr.md`

---

## 한 장 결론
Run Table token 축의 마지막 작은 실전 공백은 `archive와 stub는 만들었는데 manifest의 다섯 칸을 어떤 문장으로 채워야 stage 경계와 unlock discipline이 같이 살아남지?`였다. 이번 문서는 그 공백을 field-level contract로 잠가, 다음 atlas/battle/oath stage archive가 generic progress 메모가 아니라 실제 handoff-ready execution contract로 시작하게 만든다.
